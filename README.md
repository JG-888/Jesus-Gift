# Jesus-Gift Trading Agent — Long-Term Memory

This repository is the long-term memory for the Jesus-Gift trading agent. The
agent accesses it via the GitHub API on every routine run: it reads its strategy
and past memory before trading, and writes results back afterward.

**Strategy:** low-float small-cap **parabolic short** — short stocks that spiked
100%+ on huge volume relative to float and are rolling over on their first red
day, covering at the morning "impatience" flush (the "JesseG" strategy). Traded
short-only on TradeZero, 09:30–11:30 ET. Full details in
[`config/rules.md`](./config/rules.md) and [`config/strategy.yml`](./config/strategy.yml).

The read/write contract that keeps this memory from degrading is in
[`SCHEMA.md`](./SCHEMA.md) — read that first.

## Structure

```
config/          <- COLD memory (you own; agent only reads)
  strategy.yml     numeric parameters & criteria - single source of truth
  rules.md         the playbook: how to apply the strategy
memory/          <- HOT memory (agent owns; written every run)
  trades.jsonl     append-only ledger, one closed trade per line
  insights.jsonl   append-only learnings, one per line
  performance.json derived stats snapshot, overwritten each run
SCHEMA.md        <- the read/write contract the agent follows
.env.example     <- template for required secrets (.env itself is gitignored)
```

## How the agent uses it

1. Fetch latest `config/*` and `memory/*` from GitHub.
2. Run the pre-market scan and trade according to the criteria + rules.
3. Append closed trades to `trades.jsonl` and learnings to `insights.jsonl`.
4. Recompute and overwrite `performance.json`.
5. Commit back to the repository.

## Security

- `.env` holds API keys and is **gitignored** — never commit it.
- Keep secrets out of every committed file; use `.env.example` to document
  which variables are needed.
