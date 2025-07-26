# Sample Configurations

This section provides complete, production-ready configuration examples for different trading strategies and scenarios.

## Basic Configuration

A minimal configuration to get started:

```toml
# Connection settings
rpc_url = "https://api.mainnet-beta.solana.com"
grpc_url = "http://grpc.provider.com:10001"
grpc_token = ""
grpc_engine = "yellowstone"

# Mode selection
mode = "smb"  # or "na" or "both"

# Basic settings
update_interval = 10000
halflife = 120000
mints_to_arb = [2, 2]
mints_to_rank = 20

# Output
output = "arb-config"

# DEXes to monitor
dexes = [
  "675kPX9MHTjS2zt1qfr1NYHuzeLXfQM9H24wFSUt1Mp8",  # Raydium V4
  "whirLbMiicVdio4qvUfM5KAg6Ct8VwpYzGff3uctyCc",  # Orca
  "LBUZKhRxPF3XUpBCjp4YzTKgLccjZhTSDM9YuVaPwxo",  # Meteora
]

# Arbitrage programs
filter_programs = true
arb_programs = [
  "MEViEnscUm6tsQRoGd9h6nLQaQspKj7DB2M5FwM3Xvz",  # SMB
  "NA247a7YE9S3p9CdKmMyETx8TTwbSdVbVYHHxpnHTUV",  # NotArb
]

# Base configuration
base_mints = ["So11111111111111111111111111111111111111112"]  # WSOL
exclude_mints = []

# Sorting
intermint_sort_strategy = { metric = "profit", direction = "descending" }
pool_sort_strategy = { metric = "liquidity", direction = "descending" }

# Basic filter
filter_thresholds = [{
  min_profit = 1_000_000,
  min_roi = 1.5,
  min_txns = 2,
  min_total_volume = 100_000_000,
  max_cu_limit = 400_000,
  top_pool_num = 3
}]

# RPC for sending
sending_rpc_urls = [
  "https://api.mainnet-beta.solana.com"
]

# Basic spam config
spam_levels = [{
  filter_level = 0,
  bundle_groups = [1, 2],
  min_cu_price = 10_000,
  max_cu_price = 50_000,
  fee_strategy = "Random"
}]

[smb]
output = "smb-config"
cetiloan = false
merge_mints = false
```

## Conservative Trading Configuration

For risk-averse strategies with high success rates:

```toml
# Premium RPC endpoints for reliability
rpc_url = "https://mainnet.helius-rpc.com/?api-key=YOUR_KEY"
grpc_url = "http://premium.grpc.provider:10001"
grpc_token = "your-token"
grpc_engine = "yellowstone"
helius_key = "your-helius-key"  # For accurate fee estimation

mode = "both"  # Generate configs for both bots

# Slower updates, longer memory
update_interval = 30000  # 30 seconds
halflife = 300000       # 5 minutes
run_interval = 120000   # 2 minutes between trades

# Conservative mint selection
mints_to_arb = [2, 2]   # Simple 2-mint arbitrage
mints_to_rank = 10      # Track only top 10

# Major DEXes only
dexes = [
  "675kPX9MHTjS2zt1qfr1NYHuzeLXfQM9H24wFSUt1Mp8",  # Raydium V4
  "whirLbMiicVdio4qvUfM5KAg6Ct8VwpYzGff3uctyCc",  # Orca
  "CAMMCzo5YL8w4VFF8KVHrK22GGUsp5VTaW7grrKgrWqK",  # Raydium CLMM
]

# Only proven arbitrageurs
filter_programs = true
arb_programs = [
  "MEViEnscUm6tsQRoGd9h6nLQaQspKj7DB2M5FwM3Xvz",
  "NA247a7YE9S3p9CdKmMyETx8TTwbSdVbVYHHxpnHTUV",
]

# Conservative sorting - efficiency over volume
intermint_sort_strategy = { metric = "roi", direction = "descending" }
pool_sort_strategy = { metric = "liquidity", direction = "descending" }

# Strict filters
filter_thresholds = [{
  min_profit = 10_000_000,      # 0.01 SOL minimum
  min_roi = 3.0,                # 300% return required
  min_txns = 20,                # Proven track record
  min_fails = 5,                # Max 5 failures
  min_total_volume = 1_000_000_000,
  min_liquidity = 10_000_000_000,
  max_cu_limit = 300_000,       # Lower CU for safety
  top_pool_num = 2,             # Only best 2 pools
  memo = "conservative-v1"
}]

# Multiple reliable RPCs
sending_rpc_urls = [
  "https://mainnet.helius-rpc.com/?api-key=KEY1",
  "https://api.mainnet-beta.solana.com",
  "https://solana-api.projectserum.com",
]

# Conservative fee strategy
spam_levels = [{
  filter_level = 0,
  bundle_groups = [1, 2],
  process_delay = 600,          # Slower retry
  min_cu_percentile = 40,       # Middle percentiles
  max_cu_percentile = 60,
  min_cu_price = 20_000,
  max_cu_price = 100_000,
  tx_count = 1,
  fee_strategy = "Linear"       # Predictable escalation
}]

# Use Jito for reliability
dynamic_jito_tip_mode = "parsed"
jito_levels = [{
  filter_level = 0,
  bundle_groups = [1, 2],
  min_tip_percentile = 50,
  max_tip_percentile = 70,
  min_tip = 5_000,
  max_tip = 50_000,
  tip_strategy = "Linear",
  sending_strategy = "AllAtOnce",
  no_failure_mode = true        # Ensure success
}]

[smb]
output = "smb-conservative"
max_retries = 2
enable_simple_send = false
cetiloan = false               # No flash loans
merge_mints = false            # Keep it simple

[smb.jito_config]
min_profit = 20_000            # Only use Jito if very profitable
use_min_profit = true

[notarb]
output = "notarb-conservative"
jvm_args = ["-server", "-Xmx4096m"]  # Conservative memory
threads = 4                           # Fixed thread pool
flash_loan = false
```

## Aggressive High-Frequency Configuration

For maximum opportunity capture with acceptable failure rates:

```toml
# Fast, reliable infrastructure
rpc_url = "https://dedicated.rpc.provider/YOUR_KEY"
grpc_url = "http://colocated.grpc:10001"
grpc_token = "premium-token"
grpc_engine = "yellowstone"
helius_key = "your-key"

mode = "smb"  # SMB for speed

# Aggressive timings
update_interval = 5000   # 5 second updates
halflife = 60000        # 1 minute decay
run_interval = 30000    # 30 seconds between trades

# Track many opportunities
mints_to_arb = [3, 3, 4, 4, 5]  # Multiple groups
mints_to_rank = 100             # Track top 100

# All major DEXes
dexes = [
  "675kPX9MHTjS2zt1qfr1NYHuzeLXfQM9H24wFSUt1Mp8",
  "CPMMoo8L3F4NbTegBCKVNunggL7H1ZpdTHKxQB5qKP1C",
  "pAMMBay6oceH9fJKBRHGP5D4bD4sWpmSwMn52FMfXEA",
  "LBUZKhRxPF3XUpBCjp4YzTKgLccjZhTSDM9YuVaPwxo",
  "whirLbMiicVdio4qvUfM5KAg6Ct8VwpYzGff3uctyCc",
  "CAMMCzo5YL8w4VFF8KVHrK22GGUsp5VTaW7grrKgrWqK",
  "Eo7WjKq67rjJQSZxS6z3YkapzY3eMj6Xy8X5EQVn5UaB",
]

# Don't filter programs - catch everything
filter_programs = false

# Sort by raw profit
intermint_sort_strategy = { metric = "profit", direction = "descending" }
pool_sort_strategy = { metric = "total_volume", direction = "descending" }

# Multiple filter tiers
filter_thresholds = [
  { # Tier 0: Catch everything profitable
    min_profit = 100_000,
    min_roi = 1.1,
    min_txns = 1,
    max_cu_limit = 600_000,
    top_pool_num = 5,
    memo = "aggressive-low"
  },
  { # Tier 1: Medium opportunities
    min_profit = 1_000_000,
    min_roi = 1.5,
    min_txns = 5,
    max_cu_limit = 800_000,
    top_pool_num = 6,
    memo = "aggressive-med"
  },
  { # Tier 2: High value
    min_profit = 10_000_000,
    min_roi = 2.0,
    min_txns = 10,
    max_cu_limit = 1_000_000,
    top_pool_num = 8,
    memo = "aggressive-high"
  }
]

# Many fast RPCs
sending_rpc_urls = [
  "https://fast1.rpc.com",
  "https://fast2.rpc.com",
  "https://fast3.rpc.com",
  "https://fast4.rpc.com",
  "https://fast5.rpc.com",
]

# Multi-tier execution
spam_levels = [
  { # Low value: cheap spam
    filter_level = 0,
    bundle_groups = [1, 2],
    process_delay = 200,
    min_cu_price = 1_000,
    max_cu_price = 10_000,
    tx_count = 5,
    fee_strategy = "Random"
  },
  { # Medium value: moderate fees
    filter_level = 1,
    bundle_groups = [3, 4],
    process_delay = 300,
    min_cu_percentile = 50,
    max_cu_percentile = 75,
    tx_count = 3,
    fee_strategy = "Linear"
  }
]

# Jito for high value
dynamic_jito_tip_mode = "tipstream"
jito_levels = [{
  filter_level = 2,
  bundle_groups = [5],
  min_tip_percentile = 75,
  max_tip_percentile = 95,
  tx_count = 1,
  tip_strategy = "Exponential",
  no_failure_mode = false       # Accept some failures
}]

# Premium services for top opportunities
fast_levels = [{
  filter_level = 2,
  bundle_groups = [5],
  min_tip = 1_000_000,
  max_tip = 5_000_000,
}]

[smb]
output = "smb-aggressive"
max_retries = 5              # Keep trying
enable_simple_send = false
cetiloan = true              # Use flash loans
merge_mints = true           # Combine opportunities

[smb.jito_config]
min_profit = 5_000
use_min_profit = true

[smb.fast_config]
min_profit = 50_000
use_min_profit = true
auth_value = "your-auth"
```

## Volume-Based Market Making Configuration

For strategies focused on high-volume opportunities:

```toml
rpc_url = "https://your.rpc.endpoint"
grpc_url = "http://your.grpc:10001"
grpc_engine = "yellowstone"

mode = "na"  # NotArb for market making

update_interval = 10000
halflife = 180000  # 3 minutes

mints_to_arb = [2, 2, 2, 2]  # Even distribution
mints_to_rank = 30

# Volume-focused sorting
intermint_sort_strategy = { metric = "total_volume", direction = "descending" }
pool_sort_strategy = { metric = "turnover", direction = "descending" }

# Volume-based filters
filter_thresholds = [
  {
    min_total_volume = 10_000_000_000,   # $10k volume
    min_net_volume = 1_000_000_000,      # $1k directional
    min_turnover = 2.0,                  # 2x liquidity turnover
    min_imbalance_ratio = 0.1,           # 10% minimum imbalance
    max_imbalance_ratio = 0.5,           # 50% maximum imbalance
    min_liquidity = 5_000_000_000,       # $5k liquidity
    max_cu_limit = 400_000,
    top_pool_num = 4
  }
]

# Balanced execution
spam_levels = [{
  filter_level = 0,
  bundle_groups = [1, 2, 3, 4],
  min_cu_percentile = 40,
  max_cu_percentile = 60,
  fee_strategy = "Linear"
}]

[notarb]
output = "notarb-volume"
jvm_args = ["-server", "-Xmx8192m", "-XX:+UseG1GC"]
threads = 0  # Dynamic threading
flash_loan = true
meteora_bin_limit = 30  # More bins for better pricing
```

## New Token Sniper Configuration

For catching new token launches and volatile opportunities:

```toml
rpc_url = "https://fastest.rpc.possible"
grpc_url = "http://premium.grpc:10001"
grpc_engine = "yellowstone"

mode = "both"

# Fast updates for new opportunities
update_interval = 3000   # 3 seconds
halflife = 30000        # 30 second decay
run_interval = 15000    # 15 seconds minimum

# Track many potential tokens
mints_to_arb = [1, 1, 1, 1, 1]  # Individual tokens
mints_to_rank = 50

# All DEXes including new ones
dexes = [
  "675kPX9MHTjS2zt1qfr1NYHuzeLXfQM9H24wFSUt1Mp8",
  "CPMMoo8L3F4NbTegBCKVNunggL7H1ZpdTHKxQB5qKP1C",
  "pAMMBay6oceH9fJKBRHGP5D4bD4sWpmSwMn52FMfXEA",
  "LBUZKhRxPF3XUpBCjp4YzTKgLccjZhTSDM9YuVaPwxo",
  "whirLbMiicVdio4qvUfM5KAg6Ct8VwpYzGff3uctyCc",
]

# Don't filter - catch new arbitrageurs
filter_programs = false

# Sort by newest pools
intermint_sort_strategy = { metric = "pool_age", direction = "ascending" }
pool_sort_strategy = { metric = "volatility", direction = "descending" }

# Filters for new volatile tokens
filter_thresholds = [{
  min_volatility = 0.02,          # 2% volatility minimum
  min_total_volume = 100_000_000, # Low volume OK
  min_txns = 1,                   # Brand new OK
  max_cu_limit = 600_000,
  top_pool_num = 3
}]

# Aggressive fee strategy for competition
spam_levels = [{
  filter_level = 0,
  bundle_groups = [1, 2, 3, 4, 5],
  process_delay = 100,           # Very fast
  min_cu_percentile = 75,        # High fees
  max_cu_percentile = 95,
  tx_count = 10,                 # Spam hard
  fee_strategy = "Exponential"
}]

# Always use Jito for new tokens
dynamic_jito_tip_mode = "tipstream"
jito_levels = [{
  filter_level = 0,
  bundle_groups = [1, 2, 3, 4, 5],
  min_tip_percentile = 90,       # Top tips
  max_tip_percentile = 99,
  tip_strategy = "Exponential",
  no_failure_mode = true
}]
```

## Multi-Region Distributed Configuration

For running across multiple geographic regions:

```toml
# Region-specific endpoints
rpc_url = "https://us-east.rpc.provider"
grpc_url = "http://us-east.grpc:10001"
grpc_engine = "yellowstone"

mode = "both"
port = 8080  # Enable file server

# Standard timings
update_interval = 10000
halflife = 120000

# Region-specific RPCs
sending_rpc_urls = [
  "https://us-east-1.rpc.com",
  "https://us-east-2.rpc.com",
  "https://us-west-1.rpc.com",
  "https://eu-west-1.rpc.com",
  "https://asia-1.rpc.com",
]

# Standard configuration
mints_to_arb = [2, 2, 3]
mints_to_rank = 25

# Balanced filters
filter_thresholds = [{
  min_profit = 5_000_000,
  min_roi = 2.0,
  min_txns = 10,
  min_total_volume = 500_000_000,
  max_cu_limit = 500_000,
  top_pool_num = 4
}]

[notarb]
# Multi-region Jito
jito_targets = [
  "https://ny.mainnet.block-engine.jito.wtf",
  "https://tokyo.mainnet.block-engine.jito.wtf",
  "https://slc.mainnet.block-engine.jito.wtf",
  "https://amsterdam.mainnet.block-engine.jito.wtf",
  "https://frankfurt.mainnet.block-engine.jito.wtf",
  "https://london.mainnet.block-engine.jito.wtf",
  "https://singapore.mainnet.block-engine.jito.wtf",
]
```

## Testing Configuration

For testing strategies without risking funds:

```toml
rpc_url = "https://api.devnet.solana.com"  # Or testnet
grpc_url = "http://test.grpc:10001"
grpc_engine = "yellowstone"

mode = "both"
log_output = true  # Enable detailed logging

# Slow updates for observation
update_interval = 60000  # 1 minute
halflife = 600000       # 10 minutes

# Minimal configuration
mints_to_arb = [1]
mints_to_rank = 5

# Very low thresholds for testing
filter_thresholds = [{
  min_profit = 1,
  min_roi = 0.1,
  min_txns = 0,
  max_cu_limit = 200_000,
  top_pool_num = 2
}]

# Minimal fees for testing
spam_levels = [{
  filter_level = 0,
  bundle_groups = [1],
  min_cu_price = 1,
  max_cu_price = 100,
  fee_strategy = "Random"
}]

[smb]
enable_simple_send = true  # Simplified sending
cetiloan = false          # No flash loans in test
```

## Usage Tips

1. **Start with Basic**: Test core functionality first
2. **Adjust Gradually**: Change one parameter at a time
3. **Monitor Results**: Use `log_output = true` initially
4. **Scale Appropriately**: Match configuration to your resources
5. **Regular Updates**: Review and adjust weekly
6. **Document Changes**: Keep notes on what works

Remember: These are starting points. Optimal configuration depends on your specific goals, capital, and risk tolerance.