# Memory Schema & Read/Write Contract

This repository is the trading agent's long-term memory. The agent reads it at
the start of every routine run and writes back at the end. To stop memory from
degrading over hundreds of runs, every file has a fixed role and update rule.

Strategy: **low-float small-cap parabolic short ("first red day")** — see
`config/strategy.yml` (parameters) and `config/rules.md` (playbook).

## Two layers of memory

| Layer | File | Who writes | Update rule |
|-------|------|-----------|-------------|
| Cold  | `config/strategy.yml`     | Human | Overwrite (rare) |
| Cold  | `config/rules.md`         | Human | Overwrite (rare) |
| Ops   | `OPERATIONS.md`           | Human + Agent (proven fixes) | Overwrite (keep lean) |
| Hot   | `memory/trades.jsonl`     | Agent | **Append-only** |
| Hot   | `memory/insights.jsonl`   | Agent | **Append + consolidate** |
| Hot   | `memory/watchlist.jsonl`  | Agent | **Append + consolidate** |
| Hot   | `memory/performance.json` | Agent | **Overwrite** (derived) |

Core rules:
- **`trades.jsonl` is a strict append-only ledger** — an immutable factual record. Never edit, reorder,
  or delete a past trade line; only add new lines at the end.
- **`insights.jsonl` is append _and consolidate_.** Normally add new lines — but when a new insight
  **supersedes** an older one (corrects it, or is a strictly more complete version of the *same* lesson),
  **remove/replace the obsolete line** instead of keeping both, so hot memory doesn't clog with stale
  duplicates. Only remove when the new line fully preserves what the old one taught; keep genuinely
  distinct insights; **when in doubt, keep both.** Pull `main` first (consolidate against the current
  file), and note in the commit message what you removed and why (git history keeps the old line).
- `performance.json` is **derived** from `trades.jsonl` — recompute it, never hand-edit.
- `config/` is the single source of truth for numbers. Everything else references it; nothing duplicates values.
- **Cold memory is principle-only.** `config/rules.md` (plain English) and `config/strategy.yml` hold the
  strategy's *principles and parameters* — keep them lean. Per-trade examples, field notes, and session
  observations go in `memory/insights.jsonl` (hot), **not** in `rules.md`. Promote a hot-memory insight
  to a cold rule only once it has proven out over multiple trades.

## `memory/trades.jsonl` — append-only, one JSON object per line
Append exactly one line per **closed** trade (all trades are shorts for now):
```json
{"id":"<uuid>","ts_open":"<ISO8601>","ts_close":"<ISO8601>","symbol":"EQ","side":"short","shares":0,"entry":0.0,"cover":0.0,"pnl":0.0,"pnl_pct":0.0,"stop_resistance":0.0,"prior_day_gain_pct":0.0,"volume_to_float":0.0,"float_shares":0,"impatience_time":"10:00","setup_behaved":"as_expected","profit_window":true,"mfe_potential":0.0,"captured_pct":0.0,"loss_cause":"none","entry_note":"","exit_note":""}
```
**Scoring fields — score the _strategy_ separately from execution (these are the real success measures):**
- `setup_behaved` — did the criteria-selected name move down as predicted? `as_expected` (offered a real downward profit window) · `reversed` (dropped then squeezed green — e.g. fundamental/FDA-catalyst names) · `dead_tape` (thin tape, no flush, ground sideways/up) · `other`.
- `profit_window` — was there a moment a disciplined exit could have booked **> $0**? `true`/`false`.
- `mfe_potential` — max favorable excursion: the best profit that was actually available (≈ the by-principle low / flush low), in account $.
- `captured_pct` — `pnl / mfe_potential` when a window existed — the **efficiency** (e.g. 0.25, 0.50, 0.90). Booking *any* positive fraction is a successful application.
- `loss_cause` — if the trade lost or underperformed: `execution` (setup behaved; entry/exit/sizing mistake) · `selection` (shouldn't have been taken — dead_tape / sub-100% / catalyst) · `none`.

## `memory/insights.jsonl` — append + consolidate, one JSON object per line
Add a line whenever the agent learns something worth keeping (a pattern, an incident, a rule adjustment):
```json
{"ts":"<ISO8601>","type":"pattern|incident|adjustment","symbol":"","note":"","evidence":""}
```
**Consolidate as you go (avoid clogging).** If a new insight makes an earlier one **obsolete** — it
corrects it, or is a more complete version of the *same* lesson — **delete the superseded line(s)** and
keep only the current one, rather than accumulating both. Do this only when the new line fully preserves
what the old one taught; keep distinct lessons; when unsure, keep both. This is a deliberate exception to
strict append-only and applies to **insights only** — never to the `trades.jsonl` ledger. Record what you
removed (and why) in the commit message — git history retains the old line if it's ever needed.

## `memory/watchlist.jsonl` — append + consolidate, one JSON object per line
Multi-day tracking (Jesse's "folders of companies"): names that spiked 100%+ but **haven't clearly rolled
over yet.** Read it **first** each run; update it **last**. One line per tracked name:
```json
{"symbol":"XYZ","spike_date":"<YYYY-MM-DD>","added":"<YYYY-MM-DD>","prior_gain_pct":0.0,"volume_to_float":0.0,"float_shares":0,"pre_spike_base":0.0,"spike_high":0.0,"resistance":0.0,"status":"watching","waiting_for":"clear roll-over / first red day","days_tracked":1,"review_by":"<YYYY-MM-DD>","notes":""}
```
- `status` — `watching` (not yet rolled over) · `ready` (rolled over today → a live candidate) · `traded` · `expired`.
- **Prune once `days_tracked` reaches 3** — the drop tames each day; after ~3 days there's little correction left (judge the inverted-parabola arc on the **Yahoo 5-day view**). Also drop names that already played out.
- **Consolidate like insights:** one line per name — update it *in place* each day, never stack duplicates.

## `memory/performance.json` — overwrite, derived snapshot
Recompute from `trades.jsonl` each run. Keys: `schema_version`, `updated_at`,
`sessions`, `trades`, `wins`, `losses`, `win_rate`, `profit_factor`,
`gross_profit`, `gross_loss`, `net_pnl`, `avg_win`, `avg_loss`,
`max_drawdown`, `current_drawdown`, `consecutive_wins`, `consecutive_losses`,
plus the three **success measures** below.

### Track three success rates separately — do NOT collapse them into `win_rate`
1. `setup_behaved_as_expected_rate` — of qualifying setups, the fraction where the share behaved downward
   / offered a profit window (`setup_behaved == "as_expected"`). **This is the test of whether the
   strategy's ~80% is real** — it scores the *setup*, not the operator.
2. `application_success_rate` — of trades where a profit window existed, the fraction that booked **> $0**
   (captured *some* profit, any amount). **This is the real "did it make money" scorecard** — booking 25%
   or 50% of the max still counts as a success, not a failure.
3. `avg_capture_pct` — mean `captured_pct`: how much of the *available* profit execution captured
   (the efficiency / "perfection" metric — a quality target, NOT the pass/fail bar).

`win_rate` (P&L-positive trades) stays, but read it **alongside** these: a low `win_rate` with a high
`setup_behaved_as_expected_rate` means the strategy is sound and **execution** is the gap — score them apart.

## Update order each run
1. Read `config/*`, `OPERATIONS.md` (the runbook), and `memory/*` — **including `watchlist.jsonl`.**
2. **Build today's candidate pool:** check the watchlist for names that have **now rolled over** (mark them
   `ready`); scan finviz for fresh 100%+ gainers; add any that **haven't yet rolled over** to the watchlist.
   From **all** candidates (fresh + `ready`), **trade only the single best** (one trade/day).
3. Trade according to the criteria + rules (or stand down if nothing qualifies).
4. Append each closed trade to `trades.jsonl`.
5. Append any new learnings to `insights.jsonl`.
6. **Update `watchlist.jsonl`:** mark traded / played-out names, bump `days_tracked`, and **prune anything past 3 days.**
7. Recompute and overwrite `performance.json`.
8. **Commit and push directly to the `main` branch** — do **NOT** push to a feature branch or open a
   pull request. The next run reads `main`, so anything left on an unmerged branch is invisible to it
   and the memory stops accumulating. One commit per run is fine; append-only files don't cause merge
   conflicts, so committing straight to `main` is safe.
9. **Publish the daily summary to ClickUp with an IMMEDIATE email** — follow `OPERATIONS.md` §1
   exactly: summary task in *Agenda*, assigned to the owner, **due date WITH a time 5–10 minutes
   out (ET)** — never date-only, never omitted; verify the task was created; retry per §2. Send it
   on **no-trade days too** — silence is indistinguishable from failure.

> **⚠️ Critical — read from and write to `main`.** `main` is the single source of truth the agent reads
> every run. Always pull `main` at the start and push back to `main` at the end. If the runtime can only
> push to a branch, that branch **must** be merged into `main` before the next run (e.g. auto-merge),
> otherwise the agent loses its accumulated memory.

## Credentials
Secrets live **only** in `.env`, which is gitignored and must never be committed.
See `.env.example` for the list of required variables.
