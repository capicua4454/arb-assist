# SMB-Onchain Setup

This guide covers the complete setup process for using arb-assist with SolanaMevBot On-Chain.

## Overview

SMB-Onchain integration allows arb-assist to:
- Generate optimized TOML configuration files
- Dynamically update bot settings
- Control bot activation based on market conditions
- Manage priority fees and Jito tips

## Prerequisites

Before starting, ensure you have:
- ✅ SMB-Onchain binary and license
- ✅ arb-assist installed in the same directory
- ✅ Encrypted wallet keypair
- ✅ Sufficient SOL for operations

## Initial Setup

### Step 1: Directory Structure

Your directory should look like:
```
~/smb/
├── smb-onchain          # SMB binary
├── arb-assist           # arb-assist binary
├── config.toml          # arb-assist config
├── xxx.xxx.xxx.xxx.license  # arb-assist license
└── wallet.key           # Encrypted private key
```

### Step 2: Private Key Encryption

{% hint style="danger" %}
**Security Warning**: Never put unencrypted private keys on a VPS
{% endhint %}

1. **Create temporary config locally**:
```toml
# temp-config.toml
[wallet]
private_key = "your-base58-private-key-here"

[rpc]
url = "https://api.mainnet-beta.solana.com"

[bot]
compute_unit_limit = 400_000

[[routing.mint_config_list]]
mint = "So11111111111111111111111111111111111111112"
pump_pool_list = []
```

2. **Encrypt the private key**:
```bash
./smb-onchain run temp-config.toml
# This creates an encrypted wallet.key file
```

3. **Transfer files to VPS**:
- Copy `wallet.key` to your VPS
- Delete `temp-config.toml` (contains private key)

### Step 3: Configure arb-assist

Create your `config.toml`:

```toml
# Connection settings
rpc_url = "https://your-rpc-endpoint"
grpc_url = "https://your-grpc-endpoint"
grpc_token = "your-token-if-needed"
grpc_engine = "yellowstone"

# Set mode to SMB
mode = "smb"

# Basic configuration
update_interval = 10000
mints_to_arb = [2, 2]
mints_to_rank = 20

# Output file name
output = "smb-config"  # Creates smb-config.toml

# SMB-specific RPC settings
config_rpc_url = "https://rpc-for-smb"
sending_rpc_urls = [
  "https://send-rpc-1",
  "https://send-rpc-2",
  "https://send-rpc-3",
]

# SMB-specific settings
[smb]
output = "smb-config"
max_retries = 0
enable_simple_send = false
cetiloan = true
merge_mints = true

[smb.jito_config]
min_profit = 10_000
use_min_profit = true
use_separate_tip_account = false
```

## Running the System

### Step 1: Start arb-assist

Using tmux:
```bash
tmux new -s arb-assist
ulimit -n 65536
./arb-assist

# Detach with Ctrl+B, then D
```

Using PM2:
```bash
cat > start-arb-assist.sh << 'EOF'
#!/bin/bash
ulimit -n 65536
exec ./arb-assist
EOF

chmod +x start-arb-assist.sh
pm2 start start-arb-assist.sh --name arb-assist
```

### Step 2: Start SMB-Onchain

With automatic config reloading:
```bash
pm2 start smb-onchain --name smb --watch smb-config.toml -- run smb-config.toml
```

This setup ensures:
- SMB restarts when config changes
- Automatic adaptation to market conditions
- Continuous operation

### Step 3: Monitor Operation

View logs:
```bash
# arb-assist logs
pm2 logs arb-assist

# SMB logs
pm2 logs smb

# Combined monitoring
pm2 monit
```

## Configuration Details

### Generated Config Structure

arb-assist generates a config like:

```toml
[bot]
merge_mints = true
compute_unit_limit = 600_000

[[routing.mint_config_list]]
mint = "TokenMintAddress1..."
pump_pool_list = ["PoolA...", "PoolB..."]
meteora_dlmm_pool_list = ["PoolC..."]
raydium_clmm_pool_list = ["PoolD..."]
lookup_table_accounts = ["ALUT1...", "ALUT2..."]
process_delay = 400

[[routing.mint_config_list]]
mint = "TokenMintAddress2..."
# ... more pools

[rpc]
url = "https://your-configured-rpc"

[spam]
enabled = true
sending_rpc_urls = ["rpc1", "rpc2", "rpc3"]
compute_unit_price = { strategy = "Random", from = 10000, to = 50000, count = 1 }
max_retries = 10

[jito]
enabled = true
block_engines = ["ny", "tokyo", "amsterdam"]
tip = { strategy = "Random", from = 5000, to = 20000, count = 1 }

[flashloan]
enabled = true

[wallet]
# Keypair path automatically included
```

### Dynamic Updates

The config updates based on:
- Market activity levels
- Arbitrage profitability
- Network congestion
- Competition levels

### Strategy Activation

Different strategies activate at different tiers:

**Low Activity** (Tier 0):
- Basic spam mode
- Low priority fees
- Minimal Jito usage

**Medium Activity** (Tier 1):
- Increased fees
- Jito bundles enabled
- More aggressive settings

**High Activity** (Tier 2):
- Maximum fees
- Premium services
- All strategies active

## Advanced Features

### Flash Loans (Cetiloan)

Enable borrowing for arbitrage:
```toml
[smb]
cetiloan = true

[flashloan]
enabled = true
```

Benefits:
- No upfront capital needed
- Larger arbitrage positions
- Higher potential profits

### Mint Merging

Combine multiple arbitrage opportunities:
```toml
[smb]
merge_mints = true
```

This allows:
- Multiple mints per transaction
- Shared gas costs
- Complex arbitrage routes

### Jito Integration

Configure Jito bundles:
```toml
[smb.jito_config]
ips = ["192.168.1.0/24"]  # Allowed IPs
min_profit = 10_000       # Minimum profit to use Jito
use_min_profit = true     # Enforce minimum
use_separate_tip_account = false

# Dynamic tips
dynamic_jito_tip_mode = "parsed"  # Learn from chain
```

### Simple Send Mode

For testing or specific strategies:
```toml
[smb]
enable_simple_send = true
```

## Performance Optimization

### RPC Configuration

Use multiple RPCs for reliability:
```toml
# Main RPC for data
config_rpc_url = "https://premium-rpc.com"

# Multiple sending RPCs
sending_rpc_urls = [
  "https://fast-rpc-1.com",
  "https://fast-rpc-2.com", 
  "https://backup-rpc.com",
]
```

### Compute Units

Adjust based on route complexity:
```toml
# In filter thresholds
max_cu_limit = 400_000   # Simple routes
max_cu_limit = 600_000   # Standard routes
max_cu_limit = 800_000   # Complex routes
```

### Process Delays

Balance speed vs success rate:
```toml
process_delay = 200  # Aggressive
process_delay = 400  # Balanced
process_delay = 600  # Conservative
```

## Monitoring & Maintenance

### Key Metrics

Monitor these in SMB logs:
- Arbitrage success rate
- Average profit per trade
- Gas costs
- Failed transaction reasons

### Common Log Messages

**Successful Arbitrage**:
```
[INFO] Arbitrage executed: Profit: 0.125 SOL, Gas: 0.005 SOL
```

**Failed Transaction**:
```
[WARN] Transaction failed: Slippage exceeded
```

**No Opportunities**:
```
[INFO] No profitable arbitrage found
```

### Maintenance Tasks

Daily:
- Check logs for errors
- Monitor profit/loss
- Verify bot is running

Weekly:
- Review configuration effectiveness
- Update RPC endpoints if needed
- Check for SMB updates

Monthly:
- Analyze performance trends
- Optimize configuration
- Update software versions

## Troubleshooting

### Bot Not Starting

**Config Not Found**:
```
Error: Cannot find smb-config.toml
```
- Check arb-assist is running
- Verify output filename matches

**Invalid Configuration**:
```
Error: Failed to parse configuration
```
- Check TOML syntax
- Verify all required fields

### No Trades Executing

Check:
1. Wallet has sufficient SOL
2. Config contains valid mints
3. Filter thresholds aren't too strict
4. RPC endpoints are working

### High Failure Rate

Solutions:
1. Increase process_delay
2. Raise priority fees
3. Use Jito for competitive mints
4. Check RPC latency

## Security Best Practices

1. **Key Management**
   - Always use encrypted keypairs
   - Never share private keys
   - Rotate keys periodically

2. **Access Control**
   - Restrict server access
   - Use firewall rules
   - Monitor login attempts

3. **Monitoring**
   - Set up alerts for failures
   - Track wallet balance
   - Monitor for suspicious activity

## Integration Checklist

- [ ] SMB-onchain installed and licensed
- [ ] arb-assist configured for SMB mode
- [ ] Private key encrypted
- [ ] RPC endpoints configured
- [ ] GRPC connection established
- [ ] Test configuration generated
- [ ] Bot successfully executing trades
- [ ] Monitoring in place
- [ ] Backup procedures defined