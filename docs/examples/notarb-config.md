# NotArb-Focused Config

This configuration is optimized specifically for NotArb onchain-bot, based on the production `config.toml (notarb)` file.

## Full NotArb Configuration

```toml
# Connection Settings
rpc_url = "http://nl.deez.top:80"                   
grpc_url = "http://nl.grpc.deez.top:10001"          
grpc_token = ""                                     
grpc_engine = "yellowstone"                         

# NotArb-only mode
mode = "na"

# Basic Settings
port = 8080 
helius_key = ""                                     

# DEX Selection - Core DEXes for NotArb
dexes = [
  "675kPX9MHTjS2zt1qfr1NYHuzeLXfQM9H24wFSUt1Mp8",   # raydium amm v4
  "CPMMoo8L3F4NbTegBCKVNunggL7H1ZpdTHKxQB5qKP1C",   # raydium cpmm
  "pAMMBay6oceH9fJKBRHGP5D4bD4sWpmSwMn52FMfXEA",    # pumpfun amm
  "LBUZKhRxPF3XUpBCjp4YzTKgLccjZhTSDM9YuVaPwxo",    # meteora dlmm
  "whirLbMiicVdio4qvUfM5KAg6Ct8VwpYzGff3uctyCc",    # orca whirlpools
  "CAMMCzo5YL8w4VFF8KVHrK22GGUsp5VTaW7grrKgrWqK",   # raydium clmm
  "cpamdpZCGKUy5JxQXB4dcpGPiikHawvSWAd6mEn1sGG",    # meteora damm v2
]

# Program Filtering
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

# NotArb-optimized: 5 groups of 2 mints each
mints_to_arb = [2, 2, 2, 2, 2]
mints_to_rank = 20

# Sorting
intermint_sort_strategy = { metric = "profit", direction = "descending" }
pool_sort_strategy = { metric = "profit", direction = "descending" }

# Single Filter Threshold with new percentile settings
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
    min_volatility = 0.0,
    min_fee_percentile = {50=1000, 80=10000},
    max_fee_percentile = {50=10000, 80=1000000},
    min_tip_percentile = {50=1000, 80=10000},
    max_tip_percentile = {50=10000, 80=1000000}
  },
]

# Execution Strategies
spam_levels = [
  {
    filter_level = 0,
    bundle_groups = [1],
    process_delay = 400,
    min_cu_percentile = 0,
    max_cu_percentile = 0,   # NotArb handles its own CU pricing
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
    no_failure_mode = true   # prefer_success in NotArb
  },
]

# FAST levels for NotArb (requires min_tip > 15000)
fast_levels = [
  {
    filter_level = 0,
    bundle_groups = [1],
    process_delay = 400,
    min_tip = 15_000,    # Minimum 15000 lamports required
    max_tip = 20_000,
    min_tip_percentile = 0,
    max_tip_percentile = 99,
    tx_count = 1,
    tip_strategy = "Random",
    sending_strategy = "AllAtOnce",
    no_failure_mode = true,    # Required for FAST
    min_cu_percentile = 0,
    max_cu_percentile = 25,
    min_cu_price = 1_000,
    max_cu_price = 1_000,
    fee_strategy = "Random"
  },
]

# ASTRALANE levels for NotArb (requires min_tip > 10000)
astralane_levels = [
  {
    filter_level = 0,
    bundle_groups = [1],
    process_delay = 400,
    min_tip = 10_000,    # Minimum 10000 lamports required
    max_tip = 20_000,
    min_tip_percentile = 0,
    max_tip_percentile = 99,
    tx_count = 1,
    tip_strategy = "Random",
    sending_strategy = "AllAtOnce",
    no_failure_mode = true,    # Required for ASTRALANE
    min_cu_percentile = 0,
    max_cu_percentile = 25,
    min_cu_price = 1_000,
    max_cu_price = 1_000,
    fee_strategy = "Random"
  },
]

# RPC Configuration
config_rpc_url = "http://utah.deez.top:80"        
sending_rpc_urls = [
  "http://main.deez.top:80",                      
]        

# NotArb typically uses WSOL only
base_mints = [
  "So11111111111111111111111111111111111111112",
]

# ALUTs
aluts_per_pool = 20                                 
aluts = []

# Jito Configuration
dynamic_jito_tip_mode = "parsed"                  
jito_uuid = ""                                    
log_output = false                                

# NotArb-Specific Configuration
[notarb]
output = "notarb-config"                            
jvm_args = ["-server", "-Xmx8192m"]                # 8GB heap for JVM
keypair_path = "${DEFAULT_KEYPAIR_PATH}"            
protect_keypair = true                              
threads = 0    # Dynamic thread pool                                        
flash_loan = true                                   
max_lookup_tables = 10                              
meteora_bin_limit = 20                              
ips_file_path = ""         # For proxy senders                          
prefunded_keypairs_path = "" # For proxy senders                        

# Jito endpoints for NotArb
jito_targets = [
  "https://ny.mainnet.block-engine.jito.wtf",
  "https://tokyo.mainnet.block-engine.jito.wtf",
  "https://slc.mainnet.block-engine.jito.wtf",
  "https://amsterdam.mainnet.block-engine.jito.wtf",
  "https://frankfurt.mainnet.block-engine.jito.wtf",
  "https://london.mainnet.block-engine.jito.wtf",
  "https://singapore.mainnet.block-engine.jito.wtf",
]
max_bundle_transactions = 1                         

# Connection configurations - leave empty to use main RPC
token_accounts_checker = { rpc_url = "", delay_seconds = 3 }
blockhash_updater = { rpc_url = "", delay_ms = 400 }
account_size_loader = { 
  rpc_url = "", 
  invalid_account_size = 200, 
  buffer_size = 1500 
}
market_loader = ""
lookup_table_loader = ""

# WSOL unwrapper configuration
wsol_unwrapper = { 
  enabled = true, 
  check_minutes = 1, 
  trigger_sol = 0.5,      # Unwrap when WSOL > 0.5 SOL
  target_sol = 5,         # Keep 5 SOL after unwrapping
  priority_fee_lamports = 190, 
  reader_rpc_url = ""
}

# FAST configuration for NotArb
[notarb.fast_config]
auth_value = ""
urls = ["https://fast.circular.fi/transactions/no-failure/notarb"]

# ASTRALANE configuration for NotArb
[notarb.astralane_config]
auth_value = ""
```

## Key NotArb Optimizations

### 1. Multiple Transaction Groups
- `mints_to_arb = [2, 2, 2, 2, 2]` - 5 groups for parallel execution
- NotArb can handle multiple concurrent arbitrages efficiently

### 2. JVM Optimization
- `-Xmx8192m` - 8GB heap allocation for optimal performance
- `threads = 0` - Dynamic thread pool adapts to workload

### 3. Flash Loan Support
- `flash_loan = true` - Built-in flash loan support
- No need for large capital in wallet

### 4. Multiple Jito Endpoints
- 7 different regional endpoints for redundancy
- Automatic failover between endpoints

### 5. WSOL Unwrapper
- Automatically unwraps excess WSOL to SOL
- Maintains optimal balance for operations
- Prevents WSOL accumulation

## NotArb-Specific Features

### Proxy Support
```toml
ips_file_path = "/path/to/ips.txt"                    
prefunded_keypairs_path = "/path/to/keypairs.txt"     
```
- Support for distributed execution
- Use multiple IPs for better success rates

### Connection Management
- Separate RPC endpoints for different operations
- Optimized polling intervals
- Efficient blockhash management

### Market Data
- `markets.json` - Trading pairs organized by groups
- `lookup-tables.json` - ALUTs for transaction size optimization
- `notarb-attributes.json` - Dynamic trading parameters

## Output Files Generated

When running with `mode = "na"`, arb-assist generates:

1. **notarb-config.json** - Main configuration
2. **markets.json** - Trading pairs grouped for execution
3. **lookup-tables.json** - Address lookup tables
4. **notarb-attributes.json** - Dynamic parameters like fees and tips

## Recommended Settings

### For High Volume
- Increase `mints_to_arb` groups
- Enable proxy senders for distribution
- Use all available Jito endpoints

### For Stability
- Use conservative filter thresholds
- Enable `protect_keypair = true`
- Set reasonable WSOL unwrapper limits

### For New Users
- Start with default settings
- Monitor logs carefully
- Gradually increase complexity

## Tips for NotArb Users

1. **JVM Tuning**: Adjust `-Xmx` based on available RAM
2. **Thread Management**: Let NotArb handle threading with `threads = 0`
3. **WSOL Management**: Configure unwrapper to prevent accumulation
4. **Multiple RPCs**: Use different endpoints for redundancy
5. **Monitor Output Files**: Check generated JSON files regularly