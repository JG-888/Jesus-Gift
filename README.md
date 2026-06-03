# Jesus-Gift Trading Agent — Long-Term Memory

This repository is the long-term memory for the Jesus-Gift scalping agent. The
agent accesses it via the GitHub API on every routine run: it reads its config
and past memory before trading, and writes results back afterward.

The read/write contract that keeps this memory from degrading is in
[`SCHEMA.md`](./SCHEMA.md) — read that first.

## Structure

```
config/          <- COLD memory (you own; agent only reads)
  strategy.yml     numeric parameters - single source of truth
  rules.md         protocol & philosophy (no raw numbers)
memory/          <- HOT memory (agent owns; written every run)
  trades.jsonl     append-only ledger, one closed trade per line
  insights.jsonl   append-only learnings, one per line
  performance.json derived stats snapshot, overwritten each run
SCHEMA.md        <- the read/write contract the agent follows
.env.example     <- template for required secrets (.env itself is gitignored)
```

## How the agent uses it

1. Fetch latest `config/*` and `memory/*` from GitHub.
2. Trade according to config + rules.
3. Append closed trades to `trades.jsonl` and learnings to `insights.jsonl`.
4. Recompute and overwrite `performance.json`.
5. Commit back to the repository.

## Security

- `.env` holds API keys and is **gitignored** — never commit it.
- Keep secrets out of every committed file; use `.env.example` to document
  which variables are needed.
