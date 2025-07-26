# Trading Strategies

This section covers the various execution strategies available in arb-assist, including spam mode, Jito bundles, and other advanced trading configurations.

## Strategy Levels Overview

arb-assist supports multiple trading strategies that activate based on market conditions:

```toml
# Each strategy maps to filter threshold levels
spam_levels = [...]      # Standard fast execution
jito_levels = [...]      # MEV-protected bundles
fast_levels = [...]      # Premium fast lanes
nextblock_levels = [...]  # Next block inclusion
```

Each level corresponds to a filter threshold tier, allowing different strategies for different market conditions.

## Spam Strategy

### Configuration

```toml
spam_levels = [
  {
    filter_level = 0,                    # Maps to filter_thresholds[0]
    bundle_groups = [1],                 # Which mint groups to use
    process_delay = 400,                 # Delay between attempts (ms)
    min_cu_percentile = 25,              # Minimum fee percentile
    max_cu_percentile = 50,              # Maximum fee percentile
    min_cu_price = 1_000,               # Minimum compute unit price
    max_cu_price = 10_000,              # Maximum compute unit price
    tx_count = 1,                       # Transactions per burst
    fee_strategy = "Random"             # Fee selection strategy
  },
  {
    filter_level = 1,                    # Higher tier
    bundle_groups = [1, 2],             # More groups
    process_delay = 300,                # Faster retry
    min_cu_percentile = 50,
    max_cu_percentile = 75,
    min_cu_price = 10_000,
    max_cu_price = 50_000,
    tx_count = 3,                       # More aggressive
    fee_strategy = "Linear"
  }
]
```

### Fee Strategies

**Random**
```toml
fee_strategy = "Random"
```
- Randomly selects fee within range
- Good for avoiding detection
- Unpredictable cost

**Linear**
```toml
fee_strategy = "Linear"
```
- Gradually increases fees
- Predictable escalation
- Good for testing limits

**Exponential**
```toml
fee_strategy = "Exponential"
```
- Rapidly increasing fees
- For highly competitive situations
- Can get expensive quickly

### Priority Fee Sources

When `helius_key` is configured:
```toml
helius_key = "your-api-key"
```
- Uses real-time network fee data
- Percentiles based on recent blocks
- More accurate pricing

Without Helius:
- Learns from parsed transactions
- Less accurate but functional
- May overpay or underpay

## Jito Bundle Strategy

### Basic Configuration

```toml
jito_levels = [
  {
    filter_level = 0,
    bundle_groups = [1, 2],
    process_delay = 400,
    min_tip = 1_000,                    # Minimum Jito tip
    max_tip = 10_000,                   # Maximum Jito tip
    min_tip_percentile = 25,            # Dynamic tip percentile
    max_tip_percentile = 50,
    tx_count = 1,
    tip_strategy = "Random",
    sending_strategy = "AllAtOnce",     # Bundle sending method
    no_failure_mode = true              # Ensure bundle success
  }
]
```

### Jito-Specific Settings

**no_failure_mode**
```toml
no_failure_mode = true   # All transactions must succeed
no_failure_mode = false  # Allow partial failures
```
- `true`: Safer but consumes tip always
- `false`: Risk of wasted tips

**sending_strategy**
```toml
sending_strategy = "AllAtOnce"  # Send complete bundle
sending_strategy = "OneByOne"   # Send individually
```

### Dynamic Jito Tips

```toml
dynamic_jito_tip_mode = "parsed"     # Learn from blockchain
dynamic_jito_tip_mode = "tipstream"  # Use tip stream API
dynamic_jito_tip_mode = "none"       # Fixed tips only
```

**Parsed Mode**
- Analyzes successful bundles
- Learns optimal tip amounts
- Self-adjusting

**Tipstream Mode**
- Real-time tip floor data
- Most accurate pricing
- Requires API access

### Jito Configuration (SMB)

```toml
[smb.jito_config]
ips = ["192.168.1.0/24"]              # Allowed IP ranges
min_profit = 5_000                    # Minimum profit to use Jito
use_min_profit = true                 # Enforce minimum
use_separate_tip_account = false      # Tip account management
```

### Jito Configuration (NotArb)

```toml
[notarb]
jito_targets = [
  "https://ny.mainnet.block-engine.jito.wtf",
  "https://tokyo.mainnet.block-engine.jito.wtf",
  "https://amsterdam.mainnet.block-engine.jito.wtf",
]
max_bundle_transactions = 5           # Bundle size limit
```

## Advanced Trading Strategies

### Fast Lane Services

```toml
fast_levels = [
  {
    filter_level = 2,                   # High-value opportunities
    bundle_groups = [1],
    process_delay = 200,
    min_tip = 1_000_000,               # Higher tips
    max_tip = 2_000_000,
    tip_strategy = "Linear",
    no_failure_mode = false,
    min_cu_price = 50_000,
    max_cu_price = 100_000
  }
]

[smb.fast_config]
min_profit = 50_000
use_min_profit = true
auth_value = "your-fast-lane-auth"
```

### NextBlock Strategy

```toml
nextblock_levels = [
  {
    filter_level = 2,
    bundle_groups = [1],
    min_tip = 2_000_000,
    max_tip = 5_000_000,
    tip_strategy = "Exponential"
  }
]

[smb.nextblock_config]
min_profit = 100_000
use_min_profit = true
auth_value = "your-nextblock-auth"
```

## Multi-Strategy Coordination

### Cascading Strategies

Configure multiple strategies for different market conditions:

```toml
# Tier 0: Normal market - Use spam
spam_levels = [
  { filter_level = 0, bundle_groups = [1, 2], ... }
]

# Tier 1: Competitive - Add Jito
jito_levels = [
  { filter_level = 1, bundle_groups = [3, 4], ... }
]

# Tier 2: High value - Use premium services
fast_levels = [
  { filter_level = 2, bundle_groups = [5], ... }
]
```

### Bundle Group Strategy

Assign different groups to different strategies:

```toml
mints_to_arb = [2, 2, 3, 3, 4]  # 5 groups

# Groups 1-2: Spam strategy (low value)
# Groups 3-4: Jito bundles (medium value)
# Group 5: Fast lane (high value)
```

## Timing Configuration

### Process Delays

```toml
process_delay = 400  # Standard delay (ms)
```

Recommendations:
- **200-400ms**: Aggressive, may fail more
- **400-600ms**: Balanced approach
- **600-1000ms**: Conservative, higher success

### Update Intervals

```toml
update_interval = 10000  # Config update frequency (ms)
run_interval = 60000     # Minimum time between trades
```

Balance between:
- Fresh data (lower interval)
- System resources (higher interval)
- Market volatility (adjust accordingly)

## Risk Management

### Minimum Profit Requirements

Always set minimum profit thresholds:

```toml
[smb.jito_config]
min_profit = 10_000      # Only use Jito if profitable
use_min_profit = true
```

### Transaction Limits

Control exposure with transaction counts:

```toml
tx_count = 1  # Conservative
tx_count = 3  # Moderate
tx_count = 5  # Aggressive
```

### Compute Unit Limits

Prevent excessive fees:

```toml
max_cu_limit = 400_000   # Standard
max_cu_limit = 600_000   # Complex routes
max_cu_limit = 800_000   # Maximum allowed
```

## Strategy Selection Guide

### For New Users

Start conservative:
```toml
# Single strategy, moderate fees
spam_levels = [{
  filter_level = 0,
  bundle_groups = [1],
  min_cu_price = 5_000,
  max_cu_price = 20_000,
  tx_count = 1,
  fee_strategy = "Random"
}]
```

### For Competitive Markets

Use Jito with dynamic tips:
```toml
dynamic_jito_tip_mode = "parsed"

jito_levels = [{
  filter_level = 0,
  bundle_groups = [1, 2],
  min_tip_percentile = 50,
  max_tip_percentile = 90,
  no_failure_mode = true
}]
```

### For High-Value Opportunities

Combine multiple strategies:
```toml
# Use everything available
spam_levels = [{ filter_level = 0, ... }]
jito_levels = [{ filter_level = 1, ... }]
fast_levels = [{ filter_level = 2, ... }]
```

## Performance Optimization

### Fee Optimization

Monitor and adjust:
1. Track success rates by fee level
2. Find optimal fee ranges
3. Adjust percentiles based on results
4. Use Helius for better estimates

### Bundle Optimization

For Jito bundles:
1. Group similar value mints
2. Keep bundles small (1-3 txs)
3. Monitor tip efficiency
4. Adjust based on competition

### Latency Optimization

Reduce delays:
1. Colocate with RPC nodes
2. Use multiple sending RPCs
3. Optimize process_delay
4. Monitor network conditions

## Common Issues

### High Failure Rate

Symptoms:
- Many failed transactions
- Wasted fees
- Low profitability

Solutions:
- Increase process_delay
- Raise priority fees
- Switch to Jito bundles
- Check RPC performance

### Overpaying Fees

Symptoms:
- High gas costs
- Low ROI
- Profitable trades become losses

Solutions:
- Lower fee percentiles
- Use dynamic fee estimation
- Set tighter fee ranges
- Monitor competitor fees

### Missing Opportunities

Symptoms:
- Seeing arbs on-chain you didn't catch
- Low transaction count
- Competitors winning

Solutions:
- Reduce process_delay
- Increase fee ranges
- Add more strategies
- Use faster RPCs