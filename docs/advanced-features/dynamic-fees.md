# Dynamic Fee Adjustment

arb-assist implements sophisticated dynamic fee adjustment mechanisms to optimize transaction success rates while minimizing costs.

## Overview

Dynamic fees automatically adjust based on:
- Network congestion levels
- Recent successful transaction fees
- Competition for block space
- Market volatility
- Specific token competition

## Priority Fee Sources

### Helius API Integration

The most accurate fee estimation comes from Helius:

```toml
helius_key = "your-helius-api-key"
```

Benefits:
- Real-time network statistics
- Percentile-based recommendations
- Historical success rates
- Accurate congestion metrics

### Transaction Parsing

Without Helius, arb-assist learns from the blockchain:

```toml
helius_key = ""  # Empty = use transaction parsing
```

How it works:
1. Monitors successful arbitrage transactions
2. Extracts priority fees used
3. Builds statistical model
4. Applies to your transactions

### Percentile-Based Fees

Configure fee percentiles for different strategies:

```toml
spam_levels = [
  {
    min_cu_percentile = 25,   # 25th percentile (low fees)
    max_cu_percentile = 50,   # 50th percentile (medium fees)
  }
]
```

Common percentile strategies:
- **0-25**: Ultra low cost, may fail often
- **25-50**: Budget conscious, moderate success
- **50-75**: Balanced approach
- **75-95**: High success rate
- **95-99**: Maximum priority

## Fee Strategies

### Random Strategy

```toml
fee_strategy = "Random"
min_cu_price = 10_000
max_cu_price = 50_000
```

Characteristics:
- Unpredictable to competitors
- Good for avoiding detection
- Natural variance in costs

Use when:
- Testing fee ranges
- Avoiding patterns
- General arbitrage

### Linear Strategy

```toml
fee_strategy = "Linear"
min_cu_price = 10_000
max_cu_price = 100_000
tx_count = 5
```

Characteristics:
- Gradual fee increase
- Predictable escalation
- Even cost distribution

Example progression (5 transactions):
```
TX 1: 10,000
TX 2: 32,500
TX 3: 55,000
TX 4: 77,500
TX 5: 100,000
```

Use when:
- Systematic testing
- Gradual competition
- Budget constraints

### Exponential Strategy

```toml
fee_strategy = "Exponential"
min_cu_price = 10_000
max_cu_price = 1_000_000
```

Characteristics:
- Rapid fee escalation
- Aggressive competition
- High final fees

Example progression:
```
TX 1: 10,000
TX 2: 31,623
TX 3: 100,000
TX 4: 316,227
TX 5: 1,000,000
```

Use when:
- High-value opportunities
- Extreme competition
- Must-win scenarios

## Dynamic Jito Tips

### Tip Modes

```toml
dynamic_jito_tip_mode = "parsed"     # Learn from chain
dynamic_jito_tip_mode = "tipstream"  # Use API
dynamic_jito_tip_mode = "none"       # Fixed tips
```

### Parsed Mode

Learns optimal tips from successful bundles:

```toml
dynamic_jito_tip_mode = "parsed"
```

Process:
1. Monitors Jito bundle landings
2. Extracts tip amounts
3. Builds tip distribution
4. Applies percentiles

Benefits:
- Self-adjusting
- No external dependencies
- Follows market dynamics

### Tipstream Mode

Uses real-time tip floor data:

```toml
dynamic_jito_tip_mode = "tipstream"
jito_uuid = "your-uuid"  # Optional tracking
```

Benefits:
- Most accurate pricing
- Instant updates
- Official Jito data

### Tip Percentiles

Configure tip ranges:

```toml
jito_levels = [
  {
    min_tip_percentile = 50,  # 50th percentile floor
    max_tip_percentile = 75,  # 75th percentile ceiling
  }
]
```

## Multi-Tier Fee Configuration

### Cascading Fee Levels

Different fees for different market conditions:

```toml
# Tier 0: Low competition
spam_levels = [
  {
    filter_level = 0,
    min_cu_percentile = 10,
    max_cu_percentile = 25,
    min_cu_price = 1_000,
    max_cu_price = 10_000,
  }
]

# Tier 1: Medium competition
jito_levels = [
  {
    filter_level = 1,
    min_tip_percentile = 50,
    max_tip_percentile = 75,
    min_tip = 5_000,
    max_tip = 50_000,
  }
]

# Tier 2: High competition
fast_levels = [
  {
    filter_level = 2,
    min_tip = 100_000,
    max_tip = 1_000_000,
  }
]
```

### Market-Based Activation

Fees adjust based on profitability:

```toml
filter_thresholds = [
  { min_profit = 1_000_000 },   # Low fees OK
  { min_profit = 10_000_000 },  # Medium fees needed
  { min_profit = 100_000_000 }, # High fees justified
]
```

## Fee Optimization Strategies

### Conservative Approach

Minimize costs, accept some failures:

```toml
# Low percentiles
min_cu_percentile = 10
max_cu_percentile = 30

# Tight ranges
min_cu_price = 5_000
max_cu_price = 20_000

# Random distribution
fee_strategy = "Random"
```

### Balanced Approach

Good success rate, reasonable costs:

```toml
# Middle percentiles
min_cu_percentile = 40
max_cu_percentile = 60

# Moderate ranges
min_cu_price = 20_000
max_cu_price = 100_000

# Linear progression
fee_strategy = "Linear"
```

### Aggressive Approach

Maximum success, cost is secondary:

```toml
# High percentiles
min_cu_percentile = 75
max_cu_percentile = 95

# Wide ranges
min_cu_price = 50_000
max_cu_price = 1_000_000

# Exponential growth
fee_strategy = "Exponential"
```

## Real-World Examples

### Token Launch Scenario

High competition, time-sensitive:

```toml
# Use Jito with high tips
dynamic_jito_tip_mode = "tipstream"
jito_levels = [{
  min_tip_percentile = 90,
  max_tip_percentile = 99,
  no_failure_mode = true,
}]
```

### Stable Market Arbitrage

Low competition, volume-based:

```toml
# Minimal fees
spam_levels = [{
  min_cu_price = 1_000,
  max_cu_price = 5_000,
  fee_strategy = "Random",
}]
```

### Volatile Market Swings

Rapid changes, mixed competition:

```toml
# Multiple strategies
spam_levels = [{
  filter_level = 0,
  bundle_groups = [1, 2],
  min_cu_percentile = 25,
  max_cu_percentile = 50,
}]

jito_levels = [{
  filter_level = 1,
  bundle_groups = [3, 4],
  min_tip_percentile = 50,
  max_tip_percentile = 75,
}]
```

## Monitoring Fee Performance

### Key Metrics

Track these metrics:
1. **Success Rate by Fee Level**
2. **Average Cost per Success**
3. **ROI Impact of Fees**
4. **Competition Analysis**

### Log Analysis

Enable logging to track fees:

```toml
log_output = true
```

Analyze patterns:
```bash
# Extract fee data
grep "priority_fee" arb-assist.log | awk '{print $NF}' | sort -n | uniq -c

# Success rate by fee
grep -B1 "success\|failed" arb-assist.log | grep "priority_fee"
```

### Fee Efficiency Calculation

Calculate optimal fee ranges:

```
Efficiency = (Successful Trades * Avg Profit) / Total Fees Paid

ROI = (Total Profit - Total Fees) / Total Fees
```

## Advanced Fee Techniques

### Time-Based Adjustments

Adjust fees by time of day:

```python
# Conceptual - not directly configurable
Peak hours (9am-5pm EST): Higher fees
Off-peak (night/weekend): Lower fees
Epoch boundaries: Maximum fees
```

### Token-Specific Fees

Different fees for different tokens:

```toml
# Use different bundle groups
mints_to_arb = [2, 2, 3]  # 3 groups

# Group 1-2: Low-value tokens, low fees
spam_levels = [{
  bundle_groups = [1, 2],
  max_cu_price = 50_000,
}]

# Group 3: High-value tokens, high fees
jito_levels = [{
  bundle_groups = [3],
  min_tip = 100_000,
}]
```

### Adaptive Learning

System continuously improves:

1. **Initial**: Use conservative estimates
2. **Learning**: Gather success/failure data
3. **Optimization**: Adjust ranges based on results
4. **Maturity**: Minimal fees for maximum success

## Troubleshooting Fee Issues

### Consistent Failures

If transactions consistently fail:
1. Increase minimum percentiles
2. Widen fee ranges
3. Switch to Jito bundles
4. Check RPC latency

### Excessive Costs

If fees are too high:
1. Lower maximum percentiles
2. Tighten fee ranges
3. Use parsed mode instead of API
4. Filter for higher profit opportunities

### Erratic Success Rates

If success is unpredictable:
1. Switch from Random to Linear strategy
2. Use longer time windows for percentiles
3. Check for network issues
4. Verify competition levels

## Best Practices

1. **Start Conservative**: Begin with low fees and increase gradually
2. **Monitor Constantly**: Track success rates and adjust
3. **Use Multiple Strategies**: Different approaches for different scenarios
4. **Set Limits**: Always define maximum acceptable fees
5. **Learn from Data**: Let the system adapt to market conditions
6. **Regular Reviews**: Analyze fee performance weekly
7. **Emergency Overrides**: Have manual override capabilities

## Integration with Strategies

Fees should align with overall strategy:

- **High-Frequency**: Lower fees, accept some failures
- **High-Value**: Higher fees, ensure success
- **Market Making**: Consistent medium fees
- **Sniping**: Maximum fees for critical moments

Remember: The goal is not the lowest fees, but the best ROI after fees.