# Strategy Templates

Pre-built strategies for common arbitrage scenarios. Copy and modify these templates to match your needs.

## Token Launch Strategy

Capture opportunities during new token launches when volatility is highest.

### Configuration

```toml
# Fast infrastructure essential
update_interval = 3000   # 3-second updates
halflife = 30000        # 30-second decay for quick adaptation
run_interval = 10000    # Allow frequent trades

# Track many tokens to catch launches
mints_to_rank = 100
mints_to_arb = [1, 1, 2, 2, 3]  # Mix of single and multi-token arbs

# Sort by newest opportunities
intermint_sort_strategy = { metric = "pool_age", direction = "ascending" }
pool_sort_strategy = { metric = "volatility", direction = "descending" }

# Include PumpFun for new launches
dexes = [
  "pAMMBay6oceH9fJKBRHGP5D4bD4sWpmSwMn52FMfXEA",  # PumpFun
  "675kPX9MHTjS2zt1qfr1NYHuzeLXfQM9H24wFSUt1Mp8",  # Raydium
  "whirLbMiicVdio4qvUfM5KAg6Ct8VwpYzGff3uctyCc",  # Orca
]

# Low barriers for new tokens
filter_thresholds = [{
  min_volatility = 0.05,        # 5% volatility
  min_total_volume = 10_000_000, # Low volume OK
  min_txns = 1,                 # Brand new OK
  min_liquidity = 1_000_000_000, # $1k minimum
  max_cu_limit = 800_000,       # Higher for complex routes
  top_pool_num = 5
}]

# Aggressive execution
spam_levels = [{
  filter_level = 0,
  bundle_groups = [1, 2],
  process_delay = 100,
  min_cu_percentile = 80,
  max_cu_percentile = 95,
  tx_count = 5,
  fee_strategy = "Exponential"
}]

# Jito for guaranteed inclusion
jito_levels = [{
  filter_level = 0,
  bundle_groups = [3, 4, 5],
  min_tip_percentile = 90,
  max_tip_percentile = 99,
  no_failure_mode = true
}]
```

### Execution Tips

1. **Monitor Social Channels**: Watch for launch announcements
2. **Pre-position Capital**: Have SOL ready in wallet
3. **Use Flash Loans**: Enable `cetiloan` or `flash_loan`
4. **Watch for Rugs**: Set stop-loss mentally
5. **Take Profits Early**: New tokens can dump quickly

## Stable Arbitrage Strategy

Focus on established tokens with consistent opportunities.

### Configuration

```toml
# Moderate update frequency
update_interval = 15000  # 15 seconds
halflife = 300000       # 5 minutes
run_interval = 120000   # 2 minutes between trades

# Focus on quality
mints_to_rank = 20
mints_to_arb = [2, 2]

# Sort by efficiency
intermint_sort_strategy = { metric = "roi", direction = "descending" }
pool_sort_strategy = { metric = "liquidity", direction = "descending" }

# Major DEXes only
dexes = [
  "675kPX9MHTjS2zt1qfr1NYHuzeLXfQM9H24wFSUt1Mp8",
  "whirLbMiicVdio4qvUfM5KAg6Ct8VwpYzGff3uctyCc",
  "CAMMCzo5YL8w4VFF8KVHrK22GGUsp5VTaW7grrKgrWqK",
]

# Strict quality filters
filter_thresholds = [{
  min_profit = 5_000_000,
  min_roi = 2.5,
  min_txns = 50,               # Well-established
  min_fails = 10,              # Max 10 failures
  min_liquidity = 50_000_000_000, # $50k liquidity
  min_total_volume = 5_000_000_000,
  max_cu_limit = 400_000,
  top_pool_num = 3
}]

# Conservative fees
spam_levels = [{
  filter_level = 0,
  bundle_groups = [1, 2],
  min_cu_percentile = 40,
  max_cu_percentile = 60,
  fee_strategy = "Linear"
}]
```

### Execution Tips

1. **Long-term Focus**: Compound profits over time
2. **Risk Management**: Set daily loss limits
3. **Fee Optimization**: Track and minimize costs
4. **Consistent Execution**: Let the system run
5. **Regular Reviews**: Adjust filters monthly

## High-Frequency Strategy

Maximum transaction throughput for competitive markets.

### Configuration

```toml
# Fastest possible updates
update_interval = 2000   # 2 seconds
halflife = 60000        # 1 minute
run_interval = 5000     # 5 seconds minimum

# Track everything
mints_to_rank = 200
mints_to_arb = [1, 1, 1, 1, 1, 2, 2, 2, 2, 2]  # Many groups

# Pure profit focus
intermint_sort_strategy = { metric = "profit", direction = "descending" }

# Minimal filters
filter_thresholds = [{
  min_profit = 10_000,
  min_roi = 1.01,       # Any profit
  max_cu_limit = 1_000_000,
  top_pool_num = 8
}]

# Multi-tier spam
spam_levels = [
  {
    filter_level = 0,
    bundle_groups = [1, 2, 3, 4, 5],
    process_delay = 50,
    min_cu_price = 100,
    max_cu_price = 10_000,
    tx_count = 20,
    fee_strategy = "Random"
  },
  {
    filter_level = 0,
    bundle_groups = [6, 7, 8, 9, 10],
    process_delay = 100,
    min_cu_percentile = 50,
    max_cu_percentile = 90,
    tx_count = 10,
    fee_strategy = "Exponential"
  }
]
```

### Execution Tips

1. **Infrastructure**: Colocate with RPC nodes
2. **Monitoring**: Watch success rates closely
3. **Cost Control**: Track cumulative fees
4. **Failure Analysis**: Log and analyze failures
5. **Scaling**: Add servers for more throughput

## Cross-DEX Arbitrage Strategy

Focus on price discrepancies between different DEXes.

### Configuration

```toml
# All major DEXes required
dexes = [
  "675kPX9MHTjS2zt1qfr1NYHuzeLXfQM9H24wFSUt1Mp8",  # Raydium AMM
  "CPMMoo8L3F4NbTegBCKVNunggL7H1ZpdTHKxQB5qKP1C",  # Raydium CPMM
  "whirLbMiicVdio4qvUfM5KAg6Ct8VwpYzGff3uctyCc",  # Orca
  "LBUZKhRxPF3XUpBCjp4YzTKgLccjZhTSDM9YuVaPwxo",  # Meteora DLMM
  "CAMMCzo5YL8w4VFF8KVHrK22GGUsp5VTaW7grrKgrWqK",  # Raydium CLMM
  "Eo7WjKq67rjJQSZxS6z3YkapzY3eMj6Xy8X5EQVn5UaB",  # Meteora Dynamic
]

# Focus on tokens with multiple pools
mints_to_arb = [2, 2, 2]
filter_thresholds = [{
  min_liquidity = 10_000_000_000,
  top_pool_num = 6,  # Need multiple pools
  max_cu_limit = 600_000,
}]

# Sort by best arbitrage opportunities
pool_sort_strategy = { metric = "profit", direction = "descending" }
```

### Execution Tips

1. **Pool Analysis**: Identify consistent price gaps
2. **Route Optimization**: Find shortest paths
3. **Slippage Calculation**: Account for large trades
4. **MEV Protection**: Use Jito for valuable arbs
5. **Liquidity Monitoring**: Track pool depths

## Flash Loan Arbitrage Strategy

Maximize profits using borrowed capital.

### Configuration

```toml
# SMB with Cetiloan
[smb]
cetiloan = true
merge_mints = true  # Combine for efficiency

# NotArb with built-in flash loans
[notarb]
flash_loan = true
borrow_amount = 1_000_000_000_000  # 1000 SOL

# Focus on high-value opportunities
filter_thresholds = [{
  min_profit = 50_000_000,  # Higher profit needed
  min_roi = 1.5,           # After loan fees
  min_liquidity = 100_000_000_000,  # Deep liquidity required
  max_cu_limit = 800_000,
  top_pool_num = 4
}]

# Premium execution for large trades
jito_levels = [{
  filter_level = 0,
  bundle_groups = [1, 2, 3],
  min_tip = 50_000,
  max_tip = 500_000,
  no_failure_mode = true  # Must succeed
}]
```

### Execution Tips

1. **Capital Efficiency**: No upfront funds needed
2. **Risk Management**: Failed TX still costs fees
3. **Profit Calculation**: Include loan fees
4. **Size Limits**: Check max borrow amounts
5. **Success Priority**: Use `no_failure_mode`

## Market Making Strategy

Provide liquidity while capturing spreads.

### Configuration

```toml
# Focus on balanced markets
intermint_sort_strategy = { metric = "turnover", direction = "descending" }

# Balanced flow requirements
filter_thresholds = [{
  min_total_volume = 10_000_000_000,
  min_turnover = 1.5,
  min_imbalance_ratio = 0.2,
  max_imbalance_ratio = 0.8,  # Not too one-sided
  min_liquidity = 20_000_000_000,
  max_cu_limit = 400_000,
  top_pool_num = 4
}]

# Moderate execution
spam_levels = [{
  filter_level = 0,
  bundle_groups = [1, 2, 3, 4],
  min_cu_percentile = 30,
  max_cu_percentile = 50,
  fee_strategy = "Linear"
}]
```

### Execution Tips

1. **Two-sided Flow**: Capture both directions
2. **Inventory Management**: Monitor positions
3. **Spread Calculation**: Ensure profitability
4. **Risk Limits**: Set position limits
5. **Fee Minimization**: Optimize for volume

## Copy Trading Strategy

Follow successful arbitrageurs.

### Configuration

```toml
# Monitor specific successful traders
filter_programs = true
arb_programs = [
  "SuccessfulWallet1...",
  "ProfitableBot2...",
  "MarketMaker3...",
]

# Copy their token selections
intermint_sort_strategy = { metric = "successful_arbs", direction = "descending" }

# Slightly lower thresholds than leaders
filter_thresholds = [{
  min_profit = 1_000_000,
  min_txns = 3,  # They proved it works
  max_cu_limit = 600_000,
  top_pool_num = 4
}]

# Similar fee structure
spam_levels = [{
  filter_level = 0,
  bundle_groups = [1, 2],
  min_cu_percentile = 45,
  max_cu_percentile = 65,
  fee_strategy = "Linear"
}]
```

### Execution Tips

1. **Wallet Selection**: Choose consistent winners
2. **Lag Acceptance**: You'll be slightly behind
3. **Fee Adjustment**: May need higher fees
4. **Strategy Analysis**: Understand their approach
5. **Risk Scaling**: Start small, scale up

## Integration Tips

### Combining Strategies

Mix strategies for different market conditions:

```toml
# Tier 0: Stable arbitrage
filter_thresholds = [{
  min_profit = 10_000_000,
  min_roi = 3.0,
  min_txns = 50,
}]

# Tier 1: High-frequency
filter_thresholds = [{
  min_profit = 1_000_000,
  min_roi = 1.5,
  min_txns = 10,
}]

# Tier 2: Token launch
filter_thresholds = [{
  min_volatility = 0.05,
  min_txns = 1,
}]
```

### Risk Management

1. **Position Sizing**: Never risk more than you can afford
2. **Stop Losses**: Set mental or automated limits
3. **Diversification**: Run multiple strategies
4. **Monitoring**: Set up alerts for anomalies
5. **Regular Reviews**: Assess performance weekly

### Performance Optimization

1. **Start Simple**: Test one strategy thoroughly
2. **Measure Everything**: Track all metrics
3. **Incremental Changes**: Adjust gradually
4. **A/B Testing**: Compare strategies
5. **Continuous Learning**: Markets evolve

Remember: These templates are starting points. Successful arbitrage requires continuous adaptation to market conditions.