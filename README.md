# Jesus-Gift Trading Agent - Long-Term Memory

This repository serves as the long-term memory system for the Jesus-Gift trading agent. The agent accesses this memory via the GitHub API during routine executions to:

- Retrieve historical trade performance data
- Access learned market patterns
- Apply configured trading rules
- Update performance logs after each trading session

## Structure

### `/memory`
Long-term memory files that persist across routine executions:
- `performance.md` - Historical trade performance analytics
- `market_patterns.md` - Learned market patterns and observations
- `trading_rules.md` - Agent-discovered trading rules and adjustments

### `/config`
Trading agent configuration:
- `strategy.yml` - Strategy parameters and entry/exit conditions

### `/logs`
Trading activity logs (not committed to git):
- Daily trade execution records
- Performance metrics

### `/.claude`
Claude agent settings and configuration:
- `settings.json` - Agent preferences and behavior configuration

## Agent Integration

The Claude trading agent routine retrieves files from this repository using the GitHub API:

```
https://api.github.com/repos/{owner}/Jesus-Gift/contents/memory/{file}
```

Update frequency: Agent reads before each trade execution and commits changes after significant events.

## Usage

1. Agent routine fetches latest memory from GitHub
2. Agent executes trades based on current configuration and learned patterns
3. Agent updates memory files with new performance data and learnings
4. Agent commits changes back to repository

## Notes

- `.env` is excluded from git (contains API credentials)
- Local memory files in `memory/*.local.md` are excluded
- `logs/` directory is excluded (local execution logs only)
