# Operations Runbook — how to run the trading day (non-strategy)

Strategy lives in `config/` (what to trade and why). The memory contract lives in `SCHEMA.md`
(what to read/write). **This file is the third leg: operational procedure** — the mechanics of
running a session: reporting, notifications, API quirks, retries. The agent reads it every run
and follows it exactly. Keep it lean like the constitution: procedures, not diaries — per-day
events still go to `memory/insights.jsonl`, but once an operational method is *proven*, it gets
written (or corrected) **here**, so future runs don't have to re-derive it.

## 1. End-of-day report → ClickUp → owner's email (IMMEDIATE)

**Goal:** the owner receives an email within minutes of the session ending — never the
next-morning digest, never silence.

**Why this recipe:** the agent acts as the owner's own ClickUp account, and ClickUp never sends
instant notifications for your *own* actions — so creating the task alone emails nobody, no matter
what "Task activity" triggers are on. The one trigger that works is **time-based, not action-based**:
a task with a due date **+ time** fires a due-date notification at that time. A **date-only** due
date instead lands in the ~7 AM next-day digest (that was the "late" failure), and a missing due
date sends nothing (the "never" failure).

**⚠️ Precondition (owner-side, one-time — the agent CANNOT set this via API):** in ClickUp
**Settings → Notifications → Email (Custom)**, the **"Start & Due Dates"** trigger section must be
**enabled** (it ships disabled — that was the root cause of every silent failure). Deliverability
can be sanity-checked anytime with the **"Send test notification"** button on the Email row. If
summaries ever go silent again, check this setting **first**.

**Procedure — after the last position is closed and memory is pushed:**
1. Create the daily summary task in **Jesus' Gift → Agenda** (list id `901416729887`):
   - `name`: `Trading Day Summary — YYYY-MM-DD (Day): SYMBOL side, net ±$X (n trades, xW/yL)`
   - `description`: the full session writeup. Besides the basics (date, name traded or no-trade,
     profit/loss), it **must contain these three sections** — the owner reads this email as the
     record of the day:
     1. **Pre-market scan** — the candidates considered (fresh gainers + watchlist), each with its
        key numbers, and **why the traded one was chosen over the others** (or why all were
        rejected on a no-trade day).
     2. **The story of the trade** — a narrative of what happened while trading, in order, with
        the **size and time (ET) of every entry and every exit** (shorts, covers, spring-bounce
        legs), the stops/limits as they moved, and what the tape did in between (flushes, pops,
        halts).
     3. **Honest conclusion & insights** — what the result really was and why: setup quality vs
        execution quality, what was captured vs what was available, the cause of any loss, and
        what (if anything) was learned or should change. Honest means honest — no dressing up a
        mistake, no burying a lucky win.
   - `assignees`: `["210189122"]` (the owner)
   - **`due_date`: today's date WITH a time 5–10 minutes in the future, ET** (e.g. session ends
     10:47 → due `YYYY-MM-DD 10:55`). This is the line that makes the email immediate.
     **Never date-only. Never omit it.**
   - `priority`: `normal`
2. **Verify** the task exists (fetch it back by id). If creation failed, retry — see §2.
3. On a **no-trade day**, still send the summary (what was scanned, why stood down, watchlist
   changes) with the same due-time recipe. Silence is indistinguishable from failure.

**Validated end-to-end 2026-07-06:** with "Start & Due Dates" email triggers enabled, a task
created 6:58 PM ET with due time 7:02 PM **emailed the owner at 7:03 PM** (task `86bat8qfv`).
Earlier tests without that setting never emailed — the recipe only works with the precondition
above. Known-good summary format: task `86babygf6` (2026-06-08 SPHL).

## 2. ClickUp API reliability

- The connector/API **fails transiently** (DNS errors, "server isn't responding"). **Retry with
  backoff** — 3 attempts, a few seconds apart, before giving up (validated 2026-06-11: first
  create failed, immediate retry succeeded).
- If ClickUp is unreachable after retries, say so **in the session's git commit message** so the
  owner can see the notification step failed and why.

## 3. TradeZero API quirks (validated live)

**API contract (reverse-engineered & validated live 2026-07-07 — record so no future run re-derives it):**
- Base `https://webapi.tradezero.com` (paper + live share it; the key pair picks the env). Behind a
  **KrakenD** gateway. Auth = two headers on every request: `TZ-API-KEY-ID: $TRADING_API_KEY` and
  `TZ-API-SECRET-KEY: $TRADING_API_SECRET`. Paper account id = **`TZP5320F`**.
- **Responses are gzip** — use `curl --compressed` or you get binary. GET is clean JSON.
- **Place order:** `POST /v1/api/accounts/{acct}/order` (**singular `order`**; plural 404s). Body:
  `{"symbol","side","orderType","orderQuantity","limitPrice","securityType":"Stock","timeInForce":"Day","clientOrderId"}`.
  - `side`: only **`"Buy"` / `"Sell"`** (NOT "Short"/"Cover"). Selling with no long auto-becomes
    **`SellShort`** (opens a short); Buying against a short covers. `openClose` is auto-set.
  - `orderType`: `Limit` | `Market` | `Stop` | `StopLimit`. `Market` → omit `limitPrice`.
  - `securityType`: `Stock` | `Option` | `Mleg` (required). `timeInForce`: `Day` | `GoodTillCancel` |
    `AtTheOpening` | `ImmediateOrCancel` | `FillOrKill` | `GoodTillCrossing` | `Day_Plus` | `GTC_Plus`.
  - **KrakenD error decoding:** a wrong ENUM returns a clean JSON `- field: must be one of ...` (400).
    Valid enums but `invalid character '<' looking for beginning of value` (400, `x-krakend-completed: false`)
    = a WRONG FIELD NAME (e.g. `quantity` instead of `orderQuantity`) — the backend returned an HTML error
    KrakenD couldn't parse. Fix the field name, not the values.
- **Cancel:** `DELETE /v1/api/accounts/{acct}/orders/{clientOrderId}` (plural `orders`).
- **Reads:** `GET /v1/api/accounts` (cash/equity), `.../{acct}/positions`, `.../{acct}/orders`,
  `.../{acct}/order/{clientOrderId}`, `.../{acct}/locates/inventory`. A helper lives at
  `scratchpad/tz.sh` (short|cover|status|orders|order|cancel) — re-create it per run (scratchpad is ephemeral).
- **Pre-flight** every run with a non-fillable order (Buy Limit far BELOW market, qty 1) then cancel it —
  proves write+cancel before the open. Paper shorts need **no locate** (`locateInventory` is empty; a plain
  `Sell` still fills as `SellShort`).
- Order body **requires** `securityType: "Stock"` (else 400).
- Orders placed **during a LULD halt are REJECTED, not queued** — re-place on the live reopen;
  a marketable limit far through the market can be **price-band rejected**; a plain market order
  on a live reopen fills.
- **Cover-fill lag:** after a cover fills, the `/positions` endpoint lags — **verify by FILLS
  (order `priceAvg` × qty), not by positions**, before sending another order (prevents the
  double-fill bug of 2026-06-08/16).
- Paper-account P&L fields may not post intraday — authoritative session P&L = the fills.
- Short-sale-restricted (SSR) names: shorts must be entered on an uptick — static limits left
  below the market suffer adverse selection at the open.

## 4. Data-source fallbacks (pre-market scan)

- Gainer list: **finviz.com before 09:30** (it resets after the open). If unusable for the needed
  date, the **Nasdaq screener API** (`api.nasdaq.com/api/screener/stocks?...download=true`, UA +
  `Accept: application/json` headers) shows prior-day %change pre-market.
- Float: knowyourfloat.com + finviz + Yahoo (average, drop outliers — see `config/rules.md` §3).
- If the broker quote feed is frozen/stale (no live tape), **stand down** — no tape, no trade
  (2026-06-29).
- **Finnhub reality (validated 2026-07-07):** the key's plan has **NO candle access** — `/stock/candle`
  returns 403. So the "1-min chart" = **poll `/quote` every ~12-15s** (fields `c/o/h/l/pc/t`; `t` advances
  on each RTH trade). It gives current + running session high/low but **not per-minute OHLC or volume**,
  and it is **stale/frozen pre-market** (`t` stays at the prior close until 09:30 RTH prints). Detect the
  open by `t` advancing. `/stock/market-status?exchange=US` gives `isOpen`/`holiday`/`session` (used to
  confirm a non-holiday trading day). `/stock/profile2` gives `floatingShare` + `shareOutstanding` (float).
- **Prior-day gainers & OHLC without finviz:** `api.nasdaq.com/api/screener/stocks?...&download=true`
  (UA + `Accept: application/json`) ranks by `pctchange`; `api.nasdaq.com/api/quote/{sym}/historical`
  gives daily OHLCV (base, spike, resistance, roll-over). Right after the open, Finnhub `/quote` still
  reports the PRIOR session's o/h/l/c (pre-RTH), handy for confirming the spike-day shape.
- **Perplexity caveat (2026-07-07):** it is **unreliable for exact single-day gainer data** for a specific
  recent date (returned "no 100%+ gainers" for a day that clearly had them). Use it for **catalyst/context
  and API-doc lookups**, NOT as the gainer scanner — drive selection off the market-data APIs above.

---
*Update rule: agent reads every run; proven fixes get edited into this file (overwrite, keep it
lean) with the reason in the commit message. Strategy changes never go here — those are the
constitution's (`config/`).*
