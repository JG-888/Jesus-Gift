---
name: Trading Rules & Risk Management
description: Core trading rules and risk management protocols for the agent
type: project
---

# Trading Rules & Risk Management

## Golden Rules (Non-negotiable)
1. **Never exceed position size limits** - Max 0.1% per trade
2. **Always enforce stop losses** - No exceptions, execute immediately
3. **Daily loss cap** - Halt all trading after 5% daily loss
4. **Only trade during market hours** - 9:30am-4:00pm EST
5. **Verify spread before entry** - Min 0.01% spread required

## Position Management
- Max 3 concurrent short positions
- Exit strategy must be pre-defined before entry
- No averaging down on losing positions
- Trailing stop at 0.5% once profitable

## Risk Per Trade
- Individual trade risk: 2% stop loss
- Max account risk: 5% daily
- Profit target: 1.5% minimum
- Risk/Reward ratio minimum: 1:1

## Market Analysis Requirements
- Check volatility before entry (>2.5 ATR for shorts)
- Confirm with both 1m and 5m timeframes
- Verify volume is above 1.5x average
- RSI must be >70 for short signals

## Execution Quality
- Use limit orders only (no market orders for scalping)
- Max slippage tolerance: 0.2%
- Retry failed orders up to 3 times
- Log all executions with timestamp

## Recovery Protocols
- If daily loss hit: Wait for next trading day
- If 3 consecutive losses: Review strategy parameters
- If drawdown >10%: Reduce position size by 50%
- If system error: Pause and alert immediately

## Review Schedule
- Daily: Trade P&L and win rate
- Weekly: Strategy effectiveness and adjustments
- Monthly: Performance metrics and market analysis
- Quarterly: Full strategy reassessment
