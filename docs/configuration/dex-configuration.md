# DEX & Program Configuration

This section covers how to configure which decentralized exchanges (DEXes) and arbitrage programs arb-assist monitors.

## DEX Configuration

### dexes

**Type**: `array`  
**Required**: Yes  
**Description**: List of DEX program IDs to include in arbitrage analysis

```toml
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
```

### Supported DEXes

#### Raydium
```toml
# AMM V4 - Classic pools
"675kPX9MHTjS2zt1qfr1NYHuzeLXfQM9H24wFSUt1Mp8"

# CPMM - Concentrated liquidity
"CPMMoo8L3F4NbTegBCKVNunggL7H1ZpdTHKxQB5qKP1C"

# CLMM - Concentrated liquidity market maker
"CAMMCzo5YL8w4VFF8KVHrK22GGUsp5VTaW7grrKgrWqK"
```

#### Meteora
```toml
# DLMM - Dynamic liquidity market maker
"LBUZKhRxPF3XUpBCjp4YzTKgLccjZhTSDM9YuVaPwxo"

# Dynamic AMM
"Eo7WjKq67rjJQSZxS6z3YkapzY3eMj6Xy8X5EQVn5UaB"

# DAMM V2
"cpamdpZCGKUy5JxQXB4dcpGPiikHawvSWAd6mEn1sGG"
```

#### Orca
```toml
# Whirlpools - Concentrated liquidity
"whirLbMiicVdio4qvUfM5KAg6Ct8VwpYzGff3uctyCc"
```

#### Other DEXes
```toml
# PumpFun - Bonding curve AMM
"pAMMBay6oceH9fJKBRHGP5D4bD4sWpmSwMn52FMfXEA"

# SolFi
"SoLFiHG9TfgtdUXUjWAxi3LtvYuFyDLVhBWxdMZxyCe"

# Vertigo
"vrTGoBuy5rYSxAfV3jaRJWHH6nN9WK4NRExGxsk1bCJ"
```

### DEX Selection Strategy

Choose DEXes based on:

1. **Liquidity**: Include high-liquidity DEXes
2. **Volume**: Focus on active trading venues
3. **Arbitrage Potential**: Some DEX pairs create more opportunities
4. **Gas Efficiency**: Some DEXes have lower transaction costs

Recommended minimum set:
```toml
dexes = [
  "675kPX9MHTjS2zt1qfr1NYHuzeLXfQM9H24wFSUt1Mp8",   # Raydium V4
  "whirLbMiicVdio4qvUfM5KAg6Ct8VwpYzGff3uctyCc",    # Orca
  "LBUZKhRxPF3XUpBCjp4YzTKgLccjZhTSDM9YuVaPwxo",    # Meteora DLMM
]
```

## Arbitrage Program Configuration

### filter_programs

**Type**: `boolean`  
**Required**: No  
**Default**: `false`  
**Description**: Whether to only include transactions from specified arbitrage programs

```toml
filter_programs = true  # Only analyze transactions from arb_programs
# OR
filter_programs = false # Analyze all transactions (slower)
```

### arb_programs

**Type**: `array`  
**Required**: Yes if `filter_programs = true`  
**Description**: List of arbitrage program addresses to monitor

```toml
arb_programs = [
  "MEViEnscUm6tsQRoGd9h6nLQaQspKj7DB2M5FwM3Xvz",    # Solana MEV Bot (on-chain)
  "NA247a7YE9S3p9CdKmMyETx8TTwbSdVbVYHHxpnHTUV",    # NotArb
  "MEVbwoHUDJNcxZHFFg4XsyuQmBUeXEHFLNqXH635Sgi",    # Solana MEV Bot (Jupiter)
  "FAMBuBHJ1U8DUeY3HNGuW9XsCM4bPZKYk9NXSyR2W2zd",   # NotArb (Alternative)
]
```

### Copy Trading Configuration

You can monitor successful traders or bots:

```toml
# Monitor specific wallets instead of programs
arb_programs = [
  "3HXfeBuc1aPwAvBsdReNXKa7j1jSP3khbHbWCVkU8hUu",   # Successful trader 1
  "King7ki4SKMBPb3iupnQwTyjsq294jaXsgLmJo8cb7T",    # Successful trader 2
  "A6PDFLzh3VbkFn6aJmHe2LSd5kaYe8bmsQc6yPjinpC2",   # Bot wallet
]
```

This allows you to:
- Copy strategies from profitable traders
- Learn from successful bot configurations
- Identify new arbitrage opportunities

### Common Arbitrage Programs

#### Production Bots
```toml
# Solana MEV Bot variants
"MEViEnscUm6tsQRoGd9h6nLQaQspKj7DB2M5FwM3Xvz"    # On-chain version
"MEVbwoHUDJNcxZHFFg4XsyuQmBUeXEHFLNqXH635Sgi"    # Jupiter version

# NotArb variants  
"NA247a7YE9S3p9CdKmMyETx8TTwbSdVbVYHHxpnHTUV"    # Main
"FAMBuBHJ1U8DUeY3HNGuW9XsCM4bPZKYk9NXSyR2W2zd"   # Alternative
"NA365bsPdvZ8sP58qJ5QFg7eXygCe8aPRRxR9oeMbR5"    # Legacy
```

#### Example Traders (for learning)
```toml
"3HXfeBuc1aPwAvBsdReNXKa7j1jSP3khbHbWCVkU8hUu"   # RM4
"King7ki4SKMBPb3iupnQwTyjsq294jaXsgLmJo8cb7T"    # 7dGr
"E6kDzoUtNHuk7kyNzK9XUzqi5H3NfvhcXhKv4ToDDW1L"    # CG8n
"6MWVTis8rmmk6Vt9zmAJJbmb3VuLpzoQ1aHH4N6wQEGh"    # CzYQ
"MEDALksSEYQQUBsyjSjmhE1ib6Cn7bRV48MYGQ5oQGY"    # E8bT
```

## Token Configuration

### exclude_mints

**Type**: `array`  
**Required**: No  
**Default**: `[]`  
**Description**: Token mints to exclude from arbitrage

```toml
exclude_mints = [
  "So11111111111111111111111111111111111111112",     # WSOL
  "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v",    # USDC
  "Es9vMFrzaCERmJfrF4H2FYD4KCoNkY11McCe8BenwNYB",    # USDT
]
```

Common exclusions:
- **Base tokens**: Often used as intermediaries
- **Stablecoins**: Low volatility, minimal arbitrage
- **Problematic tokens**: Known issues or restrictions

### base_mints

**Type**: `array`  
**Required**: Yes  
**Description**: Base tokens for arbitrage paths

```toml
# Single base token
base_mints = [
  "So11111111111111111111111111111111111111112",     # WSOL only
]

# Multiple base tokens (SMB only)
base_mints = [
  "So11111111111111111111111111111111111111112",     # WSOL
  "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v",    # USDC
]
```

Strategies:
- **WSOL only**: Most liquid, standard approach
- **USDC only**: Stablecoin arbitrage
- **Both**: 3-hop and 4-hop opportunities

### include_token2022

**Type**: `boolean`  
**Required**: No  
**Default**: `false`  
**Description**: Whether to include Token-2022 (Token Extensions) mints

```toml
include_token2022 = false  # Exclude Token-2022 mints
# OR
include_token2022 = true   # Include Token-2022 mints
```

## Market Filtering

### Pool Requirements

Configure minimum pool requirements:

```toml
filter_thresholds = [
  {
    min_liquidity = 1_000_000_000,      # Minimum $1k liquidity
    min_total_volume = 10_000_000_000,  # Minimum $10k volume
    top_pool_num = 3,                   # Use top 3 pools per mint
  }
]
```

### Market Age Filtering

Filter by pool age (since first detection):

```toml
# In sort strategies
intermint_sort_strategy = { 
  metric = "pool_age", 
  direction = "ascending"  # Prefer newer pools
}
```

## Performance Considerations

### DEX Count Impact

More DEXes = More opportunities but:
- Higher CPU usage
- More memory consumption  
- Increased network traffic
- Slower processing

Recommended:
- **Production**: 5-10 major DEXes
- **Testing**: 3-5 DEXes
- **Research**: All available

### Program Filtering Impact

With `filter_programs = true`:
- ✅ Faster processing
- ✅ Less noise
- ✅ Focus on proven strategies
- ❌ May miss new opportunities

With `filter_programs = false`:
- ✅ Discover new strategies
- ✅ Complete market view
- ❌ Much slower
- ❌ More false positives

## Best Practices

1. **Start Focused**
   ```toml
   # Begin with major DEXes and known programs
   dexes = ["675k...", "whir...", "LBUZ..."]
   filter_programs = true
   arb_programs = ["MEVi...", "NA24..."]
   ```

2. **Expand Gradually**
   - Add DEXes one at a time
   - Monitor performance impact
   - Track new opportunities

3. **Regular Updates**
   - Check for new DEX deployments
   - Update program addresses
   - Remove deprecated DEXes

4. **Monitor Effectiveness**
   - Track arbitrage count by DEX
   - Measure profit by program
   - Identify optimal combinations

## Troubleshooting

### No Arbitrage Found

Check:
1. DEX addresses are correct
2. Programs are actively trading
3. Not filtering too aggressively
4. Base mints are appropriate

### High CPU Usage

Try:
1. Reduce number of DEXes
2. Enable program filtering
3. Increase update_interval
4. Exclude low-volume DEXes

### Missing Opportunities

Consider:
1. Adding more DEXes
2. Including new programs
3. Reducing exclusions
4. Monitoring competitors