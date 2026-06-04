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

## `memory/trades.jsonl` — append-only, one JSON object per line
Append exactly one line per **closed** trade (all trades are shorts for now):
```json
{"id":"<uuid>","ts_open":"<ISO8601>","ts_close":"<ISO8601>","symbol":"EQ","side":"short","shares":0,"entry":0.0,"cover":0.0,"pnl":0.0,"pnl_pct":0.0,"stop_resistance":0.0,"prior_day_gain_pct":0.0,"volume_to_float":0.0,"float_shares":0,"impatience_time":"10:00","entry_note":"","exit_note":""}
```

## `memory/insights.jsonl` — append-only, one JSON object per line
Append a line whenever the agent learns something worth keeping (a pattern, an incident, a rule adjustment):
```json
{"ts":"<ISO8601>","type":"pattern|incident|adjustment","symbol":"","note":"","evidence":""}
```

## `memory/performance.json` — overwrite, derived snapshot
Recompute from `trades.jsonl` each run. Keys: `schema_version`, `updated_at`,
`sessions`, `trades`, `wins`, `losses`, `win_rate`, `profit_factor`,
`gross_profit`, `gross_loss`, `net_pnl`, `avg_win`, `avg_loss`,
`max_drawdown`, `current_drawdown`, `consecutive_wins`, `consecutive_losses`.

## Update order each run
1. Read `config/*` and `memory/*`.
2. Trade according to the criteria + rules.
3. Append each closed trade to `trades.jsonl`.
4. Append any new learnings to `insights.jsonl`.
5. Recompute and overwrite `performance.json`.
6. Commit. One commit per run is fine — append-only files don't cause merge conflicts.

## Credentials
Secrets live **only** in `.env`, which is gitignored and must never be committed.
See `.env.example` for the list of required variables.
