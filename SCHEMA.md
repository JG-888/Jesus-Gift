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
| Hot   | `memory/trades.jsonl`     | Agent | **Append-only** |
| Hot   | `memory/insights.jsonl`   | Agent | **Append-only** |
| Hot   | `memory/performance.json` | Agent | **Overwrite** (derived) |

Core rules:
- **Never** edit or reformat existing lines in an append-only file. Only add new lines at the end.
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

## `memory/insights.jsonl` — append-only, one JSON object per line
Append a line whenever the agent learns something worth keeping (a pattern, an incident, a rule adjustment):
```json
{"ts":"<ISO8601>","type":"pattern|incident|adjustment","symbol":"","note":"","evidence":""}
```

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
1. Read `config/*` and `memory/*`.
2. Trade according to the criteria + rules.
3. Append each closed trade to `trades.jsonl`.
4. Append any new learnings to `insights.jsonl`.
5. Recompute and overwrite `performance.json`.
6. **Commit and push directly to the `main` branch** — do **NOT** push to a feature branch or open a
   pull request. The next run reads `main`, so anything left on an unmerged branch is invisible to it
   and the memory stops accumulating. One commit per run is fine; append-only files don't cause merge
   conflicts, so committing straight to `main` is safe.

> **⚠️ Critical — read from and write to `main`.** `main` is the single source of truth the agent reads
> every run. Always pull `main` at the start and push back to `main` at the end. If the runtime can only
> push to a branch, that branch **must** be merged into `main` before the next run (e.g. auto-merge),
> otherwise the agent loses its accumulated memory.

## Credentials
Secrets live **only** in `.env`, which is gitignored and must never be committed.
See `.env.example` for the list of required variables.
