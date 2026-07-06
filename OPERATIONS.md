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

---
*Update rule: agent reads every run; proven fixes get edited into this file (overwrite, keep it
lean) with the reason in the commit message. Strategy changes never go here — those are the
constitution's (`config/`).*
