# Basic Configuration

This guide provides a basic configuration based on `config.toml.example` with explanations for getting started with arb-assist.

## Minimal Working Configuration

Here's a minimal configuration to get you started:

```toml
# Connection Settings
rpc_url = "http://nl.deez.top:80"                   
grpc_url = "http://nl.grpc.deez.top:10001"          
grpc_token = ""                                     
grpc_engine = "yellowstone"                         

# Mode Selection - Choose one:
mode = "both"    # Generates configs for both SMB and NotArb
# mode = "smb"   # Only for SolanaMevBot
# mode = "na"    # Only for NotArb

# Basic Settings
port = 8080                                         
helius_key = ""    # Optional - for better fee estimation                                  

# DEX Selection - All major DEXes included by default
dexes = [
  "675kPX9MHTjS2zt1qfr1NYHuzeLXfQM9H24wFSUt1Mp8",   # raydium amm v4
  "CPMMoo8L3F4NbTegBCKVNunggL7H1ZpdTHKxQB5qKP1C",   # raydium cpmm
  "pAMMBay6oceH9fJKBRHGP5D4bD4sWpmSwMn52FMfXEA",    # pumpfun amm
  "LBUZKhRxPF3XUpBCjp4YzTKgLccjZhTSDM9YuVaPwxo",    # meteora dlmm
  "whirLbMiicVdio4qvUfM5KAg6Ct8VwpYzGff3uctyCc",    # orca whirlpools
  "CAMMCzo5YL8w4VFF8KVHrK22GGUsp5VTaW7grrKgrWqK",   # raydium clmm
]

# Program Filtering
filter_programs = true                             
arb_programs = [
  "MEViEnscUm6tsQRoGd9h6nLQaQspKj7DB2M5FwM3Xvz",    # Solana MEV Bot (on-chain)
  "NA247a7YE9S3p9CdKmMyETx8TTwbSdVbVYHHxpnHTUV",    # NotArb
]

# Exclude base tokens from arbitrage
exclude_mints = [                                 
  "So11111111111111111111111111111111111111112",     # WSOL
  "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v",    # USDC
]

# Update Settings
update_interval = 10000    # Update every 10 seconds                          
run_interval = 60000       # Run bot every 60 seconds                             
halflife = 120000          # Decay stats every 2 minutes                            

# Mint Selection
mints_to_arb = [2, 2, 2, 2, 2]    # 5 groups of 2 mints each
mints_to_rank = 20                # Track top 20 mints

# Sorting Strategy
intermint_sort_strategy = { metric = "profit", direction = "descending" }
pool_sort_strategy = { metric = "profit", direction = "descending" }

# Basic Filter Threshold
filter_thresholds = [
  {
    max_cu_limit = 400_000, 
    top_pool_num = 3, 
    memo = "0", 
    min_profit = 1_000_000,      # Minimum 0.001 SOL profit
    min_roi = 1.5,               # 50% return on fees
    min_txns = 1,                # At least 1 successful arb
    min_total_volume = 1_000_000_000,  # 1 SOL volume
    min_liquidity = 1_000_000_000,     # 1 SOL liquidity
  },
]

# Basic Spam Settings
spam_levels = [
  {
    filter_level = 0, 
    bundle_groups = [1], 
    process_delay = 400, 
    min_cu_percentile = 25, 
    max_cu_percentile = 50, 
    tx_count = 1, 
    fee_strategy = "Random"
  },
]

# Basic Jito Settings
jito_levels = [
  {
    filter_level = 0, 
    bundle_groups = [1, 2, 3], 
    process_delay = 400, 
    min_tip = 1_000, 
    max_tip = 2_000, 
    tx_count = 1, 
    tip_strategy = "Random", 
    sending_strategy = "AllAtOnce", 
    no_failure_mode = true 
  },
]

# RPC for Bot
config_rpc_url = "http://utah.deez.top:80"        
sending_rpc_urls = [
  "http://main.deez.top:80",                      
]       

# Base Token (WSOL by default)
base_mints = [
  "So11111111111111111111111111111111111111112",
]

# ALUTs
aluts_per_pool = 20                                 

# Jito Tip Mode
dynamic_jito_tip_mode = "parsed"                  

# SMB Configuration
[smb]
output = "smb-config"
cetiloan = true
merge_mints = true

# NotArb Configuration
[notarb]
output = "notarb-config"
jvm_args = ["-server", "-Xmx8192m"]
keypair_path = "${DEFAULT_KEYPAIR_PATH}"
protect_keypair = true
flash_loan = true
max_lookup_tables = 10
```

## Key Configuration Sections Explained

### Connection Settings
- `rpc_url`: Your Solana RPC endpoint for data fetching
- `grpc_url`: GRPC endpoint for streaming transactions (Yellowstone or Thor)
- `mode`: Choose which bot configs to generate

### Program Filtering
- `filter_programs = true`: Only analyze transactions from specified programs
- `arb_programs`: List of arbitrage programs to monitor

### Filter Thresholds
Define minimum requirements for mints to be included:
- `min_profit`: Minimum profit in lamports (1 SOL = 1,000,000,000 lamports)
- `min_roi`: Minimum return on investment ratio
- `min_txns`: Minimum successful arbitrage transactions seen
- `min_liquidity`: Minimum liquidity in pools

### Execution Strategies
- **Spam**: Regular priority fee transactions
- **Jito**: Bundle transactions with tips
- Additional strategies available: fast, astralane, nextblock, node1, blockrazor

## Tips for New Users

1. **Start Conservative**: Begin with higher filter thresholds to focus on proven opportunities
2. **Monitor Performance**: Use `log_output = true` to see detailed analysis
3. **Adjust Gradually**: Lower thresholds as you understand market behavior
4. **Test First**: Run with small positions before scaling up

## Next Steps

- Review [SMB-Focused Config](smb-config.md) for SMB-specific optimizations
- Review [NotArb-Focused Config](notarb-config.md) for NotArb-specific settings
- Check the [Configuration Schema](../config-reference/configuration-schema.md) for all available options