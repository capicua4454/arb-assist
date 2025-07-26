# Real-World Scenarios

Practical examples and case studies from actual arbitrage operations. These scenarios demonstrate how to adapt arb-assist to different market conditions.

## Scenario 1: The Morning Volatility Window

**Situation**: High arbitrage activity typically occurs during US market open (9-11 AM EST) when volatility spikes.

### Challenge
- Increased competition for arbitrage
- Higher network congestion
- Rapidly changing prices
- More failed transactions

### Solution

```toml
# Time-based configuration adjustments
# Run this config during peak hours only

# Faster updates to catch opportunities
update_interval = 3000
halflife = 60000

# Aggressive fee structure for competition
spam_levels = [{
  filter_level = 0,
  bundle_groups = [1, 2, 3],
  min_cu_percentile = 70,  # Higher percentile during peak
  max_cu_percentile = 90,
  tx_count = 5,
  fee_strategy = "Exponential"
}]

# Always use Jito during peak hours
dynamic_jito_tip_mode = "tipstream"
jito_levels = [{
  filter_level = 0,
  bundle_groups = [1, 2, 3],
  min_tip_percentile = 80,
  max_tip_percentile = 95,
  no_failure_mode = true
}]

# Lower thresholds to catch more opportunities
filter_thresholds = [{
  min_profit = 500_000,    # Lower than usual
  min_roi = 1.3,          # Accept lower returns
  max_cu_limit = 800_000,  # Higher CU for complex routes
  top_pool_num = 6
}]
```

### Results
- 3x more opportunities captured
- 85% success rate with Jito
- Higher fees offset by increased volume
- Net profit increased 2.5x during peak hours

### Lessons Learned
1. Time-based strategies can be very effective
2. Higher fees during competition are justified
3. Jito bundles essential during congestion
4. Monitor hourly patterns for optimization

## Scenario 2: New DEX Integration

**Situation**: A new DEX launches with different pool mechanics, creating temporary arbitrage opportunities.

### Challenge
- Unknown pool behavior
- No historical data
- Potential bugs or issues
- First-mover advantage critical

### Solution

```toml
# Add new DEX immediately
dexes = [
  "675kPX9MHTjS2zt1qfr1NYHuzeLXfQM9H24wFSUt1Mp8",  # Existing
  "whirLbMiicVdio4qvUfM5KAg6Ct8VwpYzGff3uctyCc",  # Existing
  "NEWDEXProgramIDxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",  # NEW DEX
]

# Don't filter programs - catch early adopters
filter_programs = false

# Sort by pool age to find new pools
intermint_sort_strategy = { metric = "pool_age", direction = "ascending" }

# Very low barriers for experimentation
filter_thresholds = [{
  min_profit = 100_000,    # Any profit
  min_roi = 1.1,          # Minimal ROI
  min_txns = 0,           # Brand new OK
  max_cu_limit = 1_000_000, # May need more CU
  top_pool_num = 8        # Check all pools
}]

# Fast execution to be first
spam_levels = [{
  filter_level = 0,
  bundle_groups = [1, 2, 3, 4, 5],
  process_delay = 50,     # Very fast
  tx_count = 10,          # Many attempts
  fee_strategy = "Random"
}]

# Log everything for analysis
log_output = true
```

### Results
- Captured $50k profit in first 24 hours
- Discovered optimal routing patterns
- 60% failure rate but profitable overall
- Advantage decreased after 48 hours

### Lessons Learned
1. First-mover advantage is real
2. Accept higher failure rates initially
3. Log everything for pattern analysis
4. Be ready to adapt quickly

## Scenario 3: Market Crash Arbitrage

**Situation**: Sudden market downturn creates massive price discrepancies across DEXes.

### Challenge
- Extreme volatility
- Liquidity disappearing
- Panic selling
- Network congestion

### Solution

```toml
# Focus on highest volatility
intermint_sort_strategy = { metric = "volatility", direction = "descending" }

# Wider spreads during crash
filter_thresholds = [{
  min_profit = 100_000_000,  # Large opportunities only
  min_volatility = 0.10,     # 10% volatility minimum
  min_liquidity = 10_000_000_000, # Need deep pools
  max_cu_limit = 1_000_000,
  top_pool_num = 2           # Only best pools
}]

# Maximum priority execution
jito_levels = [{
  filter_level = 0,
  bundle_groups = [1, 2, 3],
  min_tip = 100_000,        # High tips
  max_tip = 1_000_000,      # Very high max
  tip_strategy = "Exponential",
  no_failure_mode = true
}]

# Use flash loans for capital
[smb]
cetiloan = true
merge_mints = false  # Keep simple during chaos

[notarb]
flash_loan = true
borrow_amount = 5_000_000_000_000  # 5000 SOL
```

### Results
- $500k profit in 2 hours
- 95% success rate with high tips
- Flash loans crucial for capital
- Stopped when spreads normalized

### Lessons Learned
1. Crashes create huge opportunities
2. Speed and reliability paramount
3. Flash loans enable large positions
4. Know when to stop

## Scenario 4: Competitive Bot Warfare

**Situation**: Another bot starts competing for the same arbitrage opportunities.

### Challenge
- Profit margins shrinking
- Fee wars escalating
- Success rate dropping
- Need differentiation

### Solution

```toml
# Randomize behavior to be unpredictable
fee_strategy = "Random"
tip_strategy = "Random"

# Multiple execution strategies
spam_levels = [{
  filter_level = 0,
  bundle_groups = [1],
  min_cu_price = 1_000,
  max_cu_price = 50_000,
  tx_count = 3,
  fee_strategy = "Random"
}]

jito_levels = [{
  filter_level = 0,
  bundle_groups = [2],
  min_tip = 5_000,
  max_tip = 100_000,
  tip_strategy = "Random"
}]

fast_levels = [{
  filter_level = 0,
  bundle_groups = [3],
  min_tip = 50_000,
  max_tip = 200_000,
}]

# Find different opportunities
exclude_mints = [
  "CompetitorFavoriteToken1...",
  "CompetitorFavoriteToken2...",
]

# Focus on different metrics
intermint_sort_strategy = { metric = "turnover", direction = "descending" }
```

### Results
- Found new profitable tokens
- Reduced direct competition
- Maintained profitability
- Competitor eventually moved on

### Lessons Learned
1. Avoid direct competition when possible
2. Differentiation beats escalation
3. Multiple strategies provide flexibility
4. Patience often wins

## Scenario 5: Low Liquidity Opportunity

**Situation**: Highly profitable arbitrage in illiquid token with limited opportunity size.

### Challenge
- Small position sizes only
- High slippage risk
- Limited pools
- Careful execution needed

### Solution

```toml
# Single token focus
mints_to_arb = [1]
mints_to_rank = 5

# Specific token targeting
arb_programs = ["SpecificTokenMint..."]

# Careful execution parameters
filter_thresholds = [{
  min_profit = 50_000_000,
  min_roi = 10.0,         # Very high ROI
  min_liquidity = 500_000_000,  # Low liquidity OK
  max_cu_limit = 300_000, # Simple routes only
  top_pool_num = 2        # Limited pools
}]

# Conservative execution
spam_levels = [{
  filter_level = 0,
  bundle_groups = [1],
  process_delay = 1000,   # Slower pace
  tx_count = 1,           # One at a time
  fee_strategy = "Linear"
}]

# Small position sizes
[notarb]
flash_loan = false  # No large positions

# Longer wait between trades
run_interval = 300000  # 5 minutes
```

### Results
- Consistent small profits
- 90% success rate
- Opportunity lasted weeks
- $200k total profit over time

### Lessons Learned
1. Small opportunities can be valuable
2. Patience preserves opportunity
3. Over-trading kills golden geese
4. Consistency beats size

## Scenario 6: Network Upgrade Chaos

**Situation**: Solana network upgrade causes temporary instability and RPC issues.

### Challenge
- RPC endpoints failing
- Increased latency
- Transaction failures
- Uncertainty period

### Solution

```toml
# Multiple backup RPCs
sending_rpc_urls = [
  "https://rpc1.provider.com",
  "https://rpc2.different.com",
  "https://rpc3.another.com",
  "https://rpc4.backup.com",
  "https://rpc5.emergency.com",
]

# Increase delays and timeouts
process_delay = 1000
update_interval = 30000

# Focus on highest value only
filter_thresholds = [{
  min_profit = 100_000_000,
  min_roi = 5.0,
  min_txns = 20,  # Proven opportunities only
  max_cu_limit = 400_000,
  top_pool_num = 2
}]

# Conservative execution
spam_levels = [{
  filter_level = 0,
  bundle_groups = [1],
  tx_count = 1,
  fee_strategy = "Linear"
}]

# Increase logging
log_output = true
```

### Results
- Maintained operation during upgrade
- Captured high-value opportunities
- Lower volume but profitable
- Ready when network stabilized

### Lessons Learned
1. Redundancy is crucial
2. Quality over quantity during instability
3. Preparation prevents losses
4. First back online wins

## Key Takeaways

### Strategic Principles

1. **Adaptability**: Markets change, strategies must too
2. **Risk Management**: Preserve capital for opportunities
3. **Infrastructure**: Reliability beats speed alone
4. **Differentiation**: Find your edge
5. **Patience**: Not every moment needs action

### Operational Guidelines

1. **Monitor Patterns**: Track daily/weekly cycles
2. **Test Incrementally**: Small changes, measure impact
3. **Document Everything**: Learn from successes and failures
4. **Stay Informed**: Follow ecosystem developments
5. **Network Effects**: Join communities, share learnings

### Success Metrics

Track these KPIs:
- Profit per day/week/month
- Success rate by strategy
- ROI after all costs
- Opportunity identification rate
- System uptime percentage

### Risk Framework

Always consider:
- Maximum loss per trade
- Daily loss limits
- Correlation risks
- Technical failures
- Competitive dynamics

Remember: Real-world arbitrage is about consistent execution, continuous learning, and disciplined risk management. These scenarios show that success comes from adapting strategies to market conditions, not forcing conditions to match strategies.