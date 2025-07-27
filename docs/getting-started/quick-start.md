# Quick Start Guide

Get up and running with arb-assist in minutes! This guide assumes you've completed the [Installation](installation.md) steps.

## Step 1: Basic Configuration

### Minimal Configuration

Create a basic `config.toml` with essential settings:

```toml
# Connection settings
rpc_url = "https://api.mainnet-beta.solana.com"  # Replace with your RPC
grpc_url = "https://your-grpc-endpoint"          # Your GRPC endpoint
grpc_token = "your-token"                         # If required
grpc_engine = "yellowstone"                       # or "thor"

# Bot mode
mode = "smb"  # or "na" or "both"

# Basic settings
update_interval = 10000    # Update every 10 seconds
mints_to_rank = 10        # Track top 10 mints
mints_to_arb = [2, 2]     # 2 mints per transaction, 2 groups

# Simple filter
filter_thresholds = [
  {
    min_profit = 1_000_000,
    min_roi = 1.5,
    min_txns = 2,
    min_total_volume = 100_000_000,
    max_cu_limit = 400_000,
    top_pool_num = 3
  }
]

# Output file
output = "bot-config"  # Will create bot-config.toml or .json
```

## Step 2: Prepare Your Wallet

### For SMB-Onchain

1. Create a temporary config file locally:
```toml
[wallet]
private_key = "your-base58-private-key"

[rpc]
url = "https://api.mainnet-beta.solana.com"
```

2. Encrypt your private key:
```bash
./smb-onchain run temp-config.toml
# This creates an encrypted .key file
```

3. Transfer the `.key` file to your VPS
4. Delete the temporary config

### For NotArb

1. Follow NotArb's keypair protection:
```bash
bash notarb.sh protect-keypair
```

2. Update the config to point to protected keypair

## Step 3: Start arb-assist

### First Run (Testing)

```bash
# Increase file limits
ulimit -n 65536

# Run arb-assist
./arb-assist
```

### What You'll See

1. **License Validation**
```
[2024-01-26 10:00:00] License validated for IP: xxx.xxx.xxx.xxx
```

2. **GRPC Connection**
```
[2024-01-26 10:00:01] Connected to Yellowstone GRPC
[2024-01-26 10:00:01] Subscribing to transaction stream...
```

3. **Data Analysis**
```
[2024-01-26 10:00:10] Analyzing 1,234 transactions...
[2024-01-26 10:00:10] Found 56 arbitrage transactions
[2024-01-26 10:00:10] Tracking 23 unique mints
```

4. **Ranking Output**
```
Top Arbitrage Mints:
#1  PUMP...123 | Profit: 123.45 SOL | ROI: 245% | Txns: 89
#2  MEME...456 | Profit: 98.76 SOL  | ROI: 189% | Txns: 67
#3  COIN...789 | Profit: 76.54 SOL  | ROI: 156% | Txns: 45
```

5. **Config Generation**
```
[2024-01-26 10:00:20] Generated bot-config.toml with 3 mints
[2024-01-26 10:00:20] Next update in 10 seconds...
```

## Step 4: Start Your Bot

### For SMB-Onchain

In another terminal or tmux pane:

```bash
# Start SMB with PM2 watching config changes
pm2 start smb-onchain --watch bot-config.toml -- run bot-config.toml

# Monitor logs
pm2 logs smb-onchain
```

### For NotArb

```bash
# Start NotArb with PM2
pm2 start ./notarb.sh --interpreter bash --name notarb-onchain \
  --watch onchain-bot/notarb-config.toml -- onchain-bot/notarb-config.toml

# Monitor logs
pm2 logs notarb-onchain
```

## Step 5: Monitor Performance

### arb-assist Metrics

Watch for:
- Number of mints being tracked
- Profit and ROI trends
- Transaction success rates
- Network fee estimates

### Bot Performance

Monitor your bot's:
- Successful arbitrages
- Failed transactions
- Profit/loss
- Gas consumption

### Using PM2 Monitoring

```bash
# View all processes
pm2 status

# Monitor in real-time
pm2 monit

# View specific logs
pm2 logs arb-assist --lines 100
```

## Step 6: Basic Optimization

### Adjust Filters

If no mints are found, lower requirements:
```toml
filter_thresholds = [
  {
    min_profit = 100_000,      # Lower from 1M
    min_roi = 1.2,            # Lower from 1.5
    min_txns = 1,             # Lower from 2
    min_total_volume = 10_000_000,  # Lower from 100M
    max_cu_limit = 400_000,
    top_pool_num = 3
  }
]
```

### Add Fee Strategies

Enable dynamic fees:
```toml
# For Helius priority fees
helius_key = "your-helius-api-key"

# Spam configuration
spam_levels = [
  {
    filter_level = 0,
    bundle_groups = [1],
    min_cu_price = 1_000,
    max_cu_price = 10_000,
    tx_count = 1,
    fee_strategy = "Random"
  }
]
```

### Enable Jito

For competitive markets:
```toml
# Jito configuration
jito_levels = [
  {
    filter_level = 0,
    bundle_groups = [1, 2],
    min_tip = 1_000,
    max_tip = 10_000,
    tip_strategy = "Random",
    no_failure_mode = true
  }
]

dynamic_jito_tip_mode = "parsed"  # Learn from successful bundles
```

## Common Quick Start Issues

### No Mints Found

**Problem**: Config generates with dummy values
```toml
mint = "11111111111111111111111111111111"
```

**Solution**: 
- Lower filter thresholds
- Check GRPC connection
- Verify arbitrage programs in config
- Wait for market activity

### High Failure Rate

**Problem**: Bot transactions failing frequently

**Solution**:
- Increase `process_delay` (try 600-800ms)
- Reduce `mints_to_arb` numbers
- Enable Jito for competitive mints
- Check RPC performance

### Insufficient Funds

**Problem**: "Failed to create token account" errors

**Solution**:
- Add more SOL to wallet (0.1-0.5 SOL recommended)
- Enable flash loans
- Reduce concurrent transactions

## Production Checklist

Before going live:

- [ ] Test configuration for 30+ minutes
- [ ] Verify profitable mints are being found
- [ ] Confirm bot is executing trades
- [ ] Set up monitoring alerts
- [ ] Document your configuration
- [ ] Create config backups
- [ ] Set up log rotation
- [ ] Configure auto-restart on failure

## Next Steps

### Fine-tune Your Setup
- [Configuration Overview](../configuration/overview.md) - Understand all options
- [Trading Strategies](../configuration/trading-strategies.md) - Optimize for profit

### Advanced Features
- [File Server Mode](../advanced-features/file-server.md) - Manage multiple bots
- [Market Intelligence](../advanced-features/market-intelligence.md) - Advanced analysis

### Troubleshooting
- [Common Errors](../troubleshooting/common-errors.md) - Quick fixes
- [Performance Issues](../troubleshooting/performance.md) - Optimization tips

## Getting Help

Need assistance?
- Join our [Discord](https://discord.gg/ADtnjdy5m5)
- Check [FAQ](../troubleshooting/common-errors.md)
- Review example configs

Remember: Start conservative, monitor closely, and gradually increase aggressiveness as you understand the system better!