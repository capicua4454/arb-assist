# Mint Selection & Ranking

This section explains how arb-assist selects and ranks tokens (mints) for arbitrage opportunities.

## Core Concepts

### Mint Groups

Mints are organized into transaction groups for arbitrage:

```toml
mints_to_arb = [2, 2, 3]  # 3 groups: 2 mints, 2 mints, 3 mints
```

This creates:
- **Group 1**: 2 mints per transaction
- **Group 2**: 2 mints per transaction  
- **Group 3**: 3 mints per transaction

### Ranking System

```toml
mints_to_rank = 20  # Keep top 20 mints in ranking
```

Requirements:
- `mints_to_rank` ≥ sum of `mints_to_arb`
- Higher values = more opportunities tracked
- Lower values = less memory usage

## Mint Configuration

### mints_to_arb

**Type**: `array`  
**Required**: Yes  
**Description**: Number of mints per transaction group

```toml
# Simple: 2 groups of 2 mints each
mints_to_arb = [2, 2]

# Complex: Multiple group sizes
mints_to_arb = [2, 2, 3, 3, 4]

# Single group
mints_to_arb = [5]
```

Strategies:
- **[2, 2]**: Standard, balanced approach
- **[1, 1, 1]**: Isolated trades, safer
- **[3, 4, 5]**: Complex routes, higher gas

### mints_to_rank

**Type**: `integer`  
**Required**: Yes  
**Description**: Total number of mints to track

```toml
mints_to_rank = 10   # Minimal tracking
mints_to_rank = 20   # Standard
mints_to_rank = 50   # Comprehensive
mints_to_rank = 100  # Maximum coverage
```

Trade-offs:
- **Higher**: More opportunities, more memory
- **Lower**: Focused approach, less overhead

## Sorting Strategies

### intermint_sort_strategy

**Type**: `object`  
**Required**: Yes  
**Description**: How to rank mints against each other

```toml
intermint_sort_strategy = { 
  metric = "profit", 
  direction = "descending" 
}
```

### Available Metrics

#### Financial Metrics

**profit**
```toml
metric = "profit"  # Total arbitrage profit
```
- Raw profit in lamports
- Best for maximizing returns
- May favor high-volume tokens

**roi** (Return on Investment)
```toml
metric = "roi"  # Profit / Gas fees ratio
```
- Efficiency metric
- Good for limited capital
- Favors gas-efficient trades

**profit_per_arb**
```toml
metric = "profit_per_arb"  # Average profit per trade
```
- Consistency metric
- Identifies reliable opportunities
- Smooths out outliers

#### Volume Metrics

**total_volume**
```toml
metric = "total_volume"  # Buy + Sell volume
```
- Market activity indicator
- High volume = more opportunities
- May include wash trading

**net_volume**
```toml
metric = "net_volume"  # |Buy - Sell| volume
```
- Directional pressure indicator
- High net = trending token
- Identifies momentum

**turnover**
```toml
metric = "turnover"  # Volume / Liquidity ratio
```
- Market efficiency metric
- High turnover = active trading
- Relative to pool size

#### Success Metrics

**successful_arbs**
```toml
metric = "successful_arbs"  # Count of profitable trades
```
- Reliability indicator
- High count = proven opportunity
- Ignores profit size

**fails**
```toml
metric = "fails"  # Failed transaction count
```
- Risk indicator
- Use with ascending sort
- Identifies problematic mints

#### Market Metrics

**liquidity**
```toml
metric = "liquidity"  # Total pool liquidity
```
- Depth indicator
- Higher = less slippage
- More stable prices

**volatility**
```toml
metric = "volatility"  # Price movement metric
```
- Opportunity indicator
- Higher = more arbitrage
- Also higher risk

**pool_age**
```toml
metric = "pool_age"  # Time since first seen
```
- Maturity indicator
- Newer = more volatile
- Older = more stable

**imbalance**
```toml
metric = "imbalance"  # Buy/sell pressure ratio
```
- Direction indicator
- High = one-sided flow
- Arbitrage opportunity

### pool_sort_strategy

**Type**: `object`  
**Required**: Yes  
**Description**: How to rank pools within each mint

```toml
pool_sort_strategy = { 
  metric = "liquidity", 
  direction = "descending" 
}
```

Common strategies:
- **liquidity**: Use deepest pools first
- **profit**: Most profitable pools first
- **volume**: Most active pools first

## Filter Thresholds

### Multi-tier Filtering

```toml
filter_thresholds = [
  { # Tier 0: Low activity
    min_profit = 1_000_000,
    min_roi = 1.5,
    min_txns = 2,
    min_total_volume = 100_000_000,
    min_liquidity = 10_000_000,
    top_pool_num = 3
  },
  { # Tier 1: Medium activity  
    min_profit = 10_000_000,
    min_roi = 3.0,
    min_txns = 10,
    min_total_volume = 1_000_000_000,
    min_liquidity = 100_000_000,
    top_pool_num = 4
  },
  { # Tier 2: High activity
    min_profit = 100_000_000,
    min_roi = 5.0,
    min_txns = 50,
    min_total_volume = 10_000_000_000,
    min_liquidity = 1_000_000_000,
    top_pool_num = 5
  }
]
```

### Filter Parameters

**min_profit**
- Type: `integer` (lamports)
- Minimum cumulative profit
- 1 SOL = 1_000_000_000 lamports

**min_roi**
- Type: `float`
- Minimum return on investment
- Example: 2.0 = 200% return

**min_txns**
- Type: `integer`
- Minimum successful arbitrages
- Higher = more proven

**min_fails**
- Type: `integer`  
- Maximum failed transactions
- Lower = more reliable

**min_net_volume**
- Type: `integer` (lamports)
- Minimum directional volume
- Indicates trending tokens

**min_total_volume**
- Type: `integer` (lamports)
- Minimum trading volume
- Ensures adequate activity

**min_imbalance_ratio**
- Type: `float` (0.0 - 1.0)
- Minimum buy/sell imbalance
- Higher = stronger trend

**max_imbalance_ratio**
- Type: `float` (0.0 - 1.0)
- Maximum buy/sell imbalance
- Prevents one-sided markets

**min_liquidity**
- Type: `integer` (lamports)
- Minimum pool liquidity
- Ensures adequate depth

**min_turnover**
- Type: `float`
- Minimum volume/liquidity ratio
- Indicates trading activity

**min_volatility**
- Type: `float`
- Minimum price volatility
- Higher = more opportunities

**max_cu_limit**
- Type: `integer`
- Maximum compute units
- Controls transaction complexity

**top_pool_num**
- Type: `integer` (minimum 2)
- Number of pools to use
- More pools = more routes

## Selection Examples

### Conservative Strategy
```toml
# Focus on proven, liquid opportunities
intermint_sort_strategy = { metric = "roi", direction = "descending" }
mints_to_arb = [2, 2]
mints_to_rank = 10

filter_thresholds = [{
  min_profit = 10_000_000,
  min_roi = 3.0,
  min_txns = 20,
  min_fails = 5,
  min_liquidity = 1_000_000_000,
  top_pool_num = 3
}]
```

### Aggressive Strategy
```toml
# Capture all opportunities
intermint_sort_strategy = { metric = "profit", direction = "descending" }
mints_to_arb = [3, 3, 4, 4]
mints_to_rank = 50

filter_thresholds = [{
  min_profit = 100_000,
  min_roi = 1.1,
  min_txns = 1,
  min_total_volume = 10_000_000,
  top_pool_num = 5
}]
```

### Volume-Based Strategy
```toml
# Follow the money
intermint_sort_strategy = { metric = "total_volume", direction = "descending" }
pool_sort_strategy = { metric = "turnover", direction = "descending" }

filter_thresholds = [{
  min_total_volume = 10_000_000_000,
  min_turnover = 2.0,
  min_txns = 5,
  top_pool_num = 4
}]
```

### New Token Strategy
```toml
# Target fresh opportunities
intermint_sort_strategy = { metric = "pool_age", direction = "ascending" }

filter_thresholds = [{
  min_volatility = 0.05,
  min_total_volume = 1_000_000_000,
  max_imbalance_ratio = 0.8,
  top_pool_num = 3
}]
```

## Advanced Configuration

### Tiered Mint Selection

Use different groups for different strategies:

```toml
mints_to_arb = [2, 2, 3, 3]  # 4 groups

# Group 1-2: Conservative (2 mints)
# Group 3-4: Aggressive (3 mints)

spam_levels = [
  { bundle_groups = [1, 2], ... },  # Conservative groups
]

jito_levels = [
  { bundle_groups = [3, 4], ... },  # Aggressive groups
]
```

### Dynamic Adjustment

Combine with half-life decay:

```toml
halflife = 60000  # 1 minute decay

# Fast decay for volatile markets
halflife = 30000  # 30 seconds

# Slow decay for stable markets  
halflife = 300000  # 5 minutes
```

## Performance Tuning

### Memory Usage

Approximate memory per mint:
- Basic data: ~1 KB
- Pool data: ~10 KB per pool
- History: ~100 KB per mint

Total estimate:
```
Memory = mints_to_rank * (1 + 10 * avg_pools + 100) KB
```

### CPU Usage

Processing time factors:
- Sorting: O(n log n) for n mints
- Filtering: O(n * m) for m thresholds
- Updates: Every `update_interval` ms

### Optimization Tips

1. **Reduce mints_to_rank** if memory constrained
2. **Increase update_interval** if CPU constrained
3. **Simplify sort metrics** for faster processing
4. **Use fewer filter tiers** for quicker decisions

## Troubleshooting

### No Mints Selected

```toml
# Dummy mint indicates no opportunities found
mint = "11111111111111111111111111111111"
```

Solutions:
- Lower filter thresholds
- Check GRPC data stream
- Verify arbitrage programs
- Wait for market activity

### Poor Quality Mints

If selected mints aren't profitable:
- Increase min_profit threshold
- Raise min_roi requirement
- Require more min_txns
- Check sort strategy

### Missed Opportunities

If missing good trades:
- Increase mints_to_rank
- Lower filter thresholds
- Add more mint groups
- Reduce update_interval