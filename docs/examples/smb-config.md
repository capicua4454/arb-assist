# SMB-Focused Config

This configuration is optimized specifically for SolanaMevBot On-Chain, based on the production `config.toml (smb)` file.

## Full SMB Configuration

```toml
# Connection Settings
rpc_url = "http://nl.deez.top:80"                   
grpc_url = "http://nl.grpc.deez.top:10001"          
grpc_token = ""                                     
grpc_engine = "yellowstone"                         

# SMB-only mode
mode = "smb"

# Basic Settings
port = 8080 
helius_key = ""    # Optional but recommended for better fees                                  

# DEX Selection
dexes = [
  "675kPX9MHTjS2zt1qfr1NYHuzeLXfQM9H24wFSUt1Mp8",   # raydium amm v4
  "CPMMoo8L3F4NbTegBCKVNunggL7H1ZpdTHKxQB5qKP1C",   # raydium cpmm
  "pAMMBay6oceH9fJKBRHGP5D4bD4sWpmSwMn52FMfXEA",    # pumpfun amm
  "LBUZKhRxPF3XUpBCjp4YzTKgLccjZhTSDM9YuVaPwxo",    # meteora dlmm
  "whirLbMiicVdio4qvUfM5KAg6Ct8VwpYzGff3uctyCc",    # orca whirlpools
  "CAMMCzo5YL8w4VFF8KVHrK22GGUsp5VTaW7grrKgrWqK",   # raydium clmm
  "Eo7WjKq67rjJQSZxS6z3YkapzY3eMj6Xy8X5EQVn5UaB",   # meteora dynamic
  "SoLFiHG9TfgtdUXUjWAxi3LtvYuFyDLVhBWxdMZxyCe",    # solfi
  "cpamdpZCGKUy5JxQXB4dcpGPiikHawvSWAd6mEn1sGG",    # meteora damm v2
  "vrTGoBuy5rYSxAfV3jaRJWHH6nN9WK4NRExGxsk1bCJ",    # vertigo
]

# Program Filtering - Monitor both SMB and NotArb
filter_programs = true                             
arb_programs = [
  "MEViEnscUm6tsQRoGd9h6nLQaQspKj7DB2M5FwM3Xvz",    # Solana MEV Bot (on-chain)
  "NA247a7YE9S3p9CdKmMyETx8TTwbSdVbVYHHxpnHTUV",    # NotArb
]

# Base tokens excluded
exclude_mints = [                                 
  "So11111111111111111111111111111111111111112",     # WSOL
  "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v",    # USDC
]

# Update Settings
update_interval = 10000                           
run_interval = 60000                              
halflife = 120000                                 
ignore_filters = false
include_token2022 = false                           

# SMB-optimized: Only 1 group of 2 mints
mints_to_arb = [2, 0, 0, 0, 0]
mints_to_rank = 20

# Sorting
intermint_sort_strategy = { metric = "profit", direction = "descending" }
pool_sort_strategy = { metric = "profit", direction = "descending" }

# Single Filter Threshold
filter_thresholds = [
  {
    max_cu_limit = 400_000, 
    top_pool_num = 3, 
    memo = "0", 
    min_profit = 1_000_000,
    min_profit_per_arb = 0,
    min_roi = 1,
    min_txns = 1,
    min_fails = 0,
    min_net_volume = 0,
    min_total_volume = 1_000_000_000,
    min_imbalance_ratio = 0.0,
    max_imbalance_ratio = 1.0,
    min_liquidity = 1_000_000_000,
    min_turnover = 0.0,
    min_volatility = 0.0
  },
]

# Execution Strategies
spam_levels = [
  {
    filter_level = 0,
    bundle_groups = [1],
    process_delay = 400,
    min_cu_percentile = 0,
    max_cu_percentile = 99,
    min_cu_price = 0,
    max_cu_price = 5_000,
    tx_count = 1,
    fee_strategy = "Random"
  },
]

jito_levels = [
  {
    filter_level = 0,
    bundle_groups = [1],
    process_delay = 400,
    min_tip = 1_000,
    max_tip = 2000,
    min_tip_percentile = 0,
    max_tip_percentile = 99,
    tx_count = 1,
    tip_strategy = "Random",
    sending_strategy = "AllAtOnce",
    no_failure_mode = true
  },
]

fast_levels = [
  {
    filter_level = 0,
    bundle_groups = [1],
    process_delay = 400,
    min_tip = 15_000,
    max_tip = 20_000,
    min_tip_percentile = 0,
    max_tip_percentile = 99,
    tx_count = 1,
    tip_strategy = "Random",
    sending_strategy = "AllAtOnce",
    no_failure_mode = true,
    min_cu_percentile = 0,
    max_cu_percentile = 25,
    min_cu_price = 0,
    max_cu_price = 1_000,
    fee_strategy = "Random"
  },
]

# RPC Configuration
config_rpc_url = "http://utah.deez.top:80"        
sending_rpc_urls = [
  "http://main.deez.top:80",                      
]         

# SMB supports both WSOL and USDC for 3hop/4hop
base_mints = [
  "So11111111111111111111111111111111111111112",
  "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v",
]

# ALUTs
aluts_per_pool = 20                                 
aluts = []

# Jito Configuration
dynamic_jito_tip_mode = "parsed"                  
jito_uuid = ""                                    
log_output = false                                

# SMB-Specific Configuration
[smb]
output = "smb-config"
max_retries = 0
enable_simple_send = false
cetiloan = true        # Enable flash loans
merge_mints = true     # Merge multiple mints in one transaction
skip_ata_creation = false

[smb.jito_config]
ips = []
min_profit = 5_000
use_min_profit = true
use_separate_tip_account = false

[smb.fast_config]
min_profit = 5_000
use_min_profit = false
auth_value = ""
request_params = { frontRunningProtection = false }
urls = [
  "https://fast.circular.fi/transactions/no-failure/smb",
]

[smb.astralane_config]
min_profit = 5_000
use_min_profit = true
auth_value = ""
request_params = { mevProtect = false }

[smb.nextblock_config]
min_profit = 5_000
use_min_profit = true
auth_value = ""
request_params = { 
  skipPreFlight = true, 
  snipeTransaction = true, 
  frontRunningProtection = false, 
  disableRetries = true, 
  revertOnFail = false 
}

[smb.node1_config]
min_profit = 5_000
use_min_profit = true
auth_value = ""

[smb.blockrazor_config]
min_profit = 5_000
use_min_profit = true
auth_value = ""
```

## Key SMB Optimizations

### 1. Single Transaction Group
- `mints_to_arb = [2, 0, 0, 0, 0]` - Only one group with 2 mints
- Simplifies execution and reduces complexity

### 2. Multiple Base Tokens
- Both WSOL and USDC enabled for 3-hop and 4-hop arbitrage
- Provides more arbitrage opportunities

### 3. Flash Loan Support
- `cetiloan = true` - Enables capital-efficient trading
- No need for large wallet balances

### 4. Multiple Execution Strategies
- Spam: Standard priority fee transactions
- Jito: Bundled transactions with tips
- Fast: Premium transaction service with higher tips

### 5. Request Parameters
Each service can have custom parameters:
- `frontRunningProtection`: Protect against frontrunning
- `skipPreFlight`: Skip preflight checks for speed
- `snipeTransaction`: Optimize for sniping opportunities

## Recommended Settings for Different Market Conditions

### High Competition
- Increase `max_tip` in jito_levels
- Enable multiple execution strategies
- Use `no_failure_mode = true` for guaranteed execution

### Low Activity
- Lower filter thresholds to catch more opportunities
- Reduce tips and fees to maximize profit
- Consider disabling some execution strategies

### New Pools
- Lower `min_txns` requirement
- Increase `max_cu_limit` for complex routes
- Enable all DEXes for maximum coverage

## Tips for SMB Users

1. **Start with Conservative Settings**: Use the config as-is initially
2. **Monitor Bot Logs**: SMB provides detailed execution logs
3. **Adjust Based on Performance**: Tweak thresholds based on success rates
4. **Use Flash Loans**: Enable `cetiloan` to maximize capital efficiency
5. **Multiple Strategies**: Use different strategies for different filter levels