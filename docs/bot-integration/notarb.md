# NotArb Setup

This guide covers the complete setup process for using arb-assist with NotArb onchain-bot.

## Overview

NotArb integration allows arb-assist to:
- Generate JSON configuration files
- Create markets.json with trading pairs
- Provide lookup-tables.json for ALUTs
- Generate notarb-attributes.json for dynamic settings
- Control bot parameters based on market conditions

## Prerequisites

Before starting, ensure you have:
- ✅ NotArb Release cloned from GitHub
- ✅ Java 11+ installed
- ✅ arb-assist installed in onchain-bot directory
- ✅ Protected keypair file
- ✅ Sufficient SOL for operations

## Initial Setup

### Step 1: Directory Structure

Your directory should look like:
```
~/notarb/Release/
├── notarb-global.toml    # Global NotArb config
├── notarb.sh             # NotArb launcher
└── onchain-bot/
    ├── notarb.jar        # NotArb binary
    ├── arb-assist        # arb-assist binary
    ├── config.toml       # arb-assist config
    ├── xxx.xxx.xxx.xxx.license  # arb-assist license
    └── protected-keypair.json   # Protected wallet
```

### Step 2: Keypair Protection

{% hint style="info" %}
NotArb uses a protected keypair format for enhanced security
{% endhint %}

1. **Configure keypair path** in `notarb-global.toml`:
```toml
keypair_path = "/path/to/your/keypair.json"
```

2. **Protect the keypair**:
```bash
cd ~/notarb/Release
bash notarb.sh protect-keypair
```

3. **Update path** to protected keypair:
```toml
keypair_path = "/path/to/protected-keypair.json"
```

### Step 3: Configure arb-assist

Create your `config.toml` in the onchain-bot directory:

```toml
# Connection settings
rpc_url = "https://your-rpc-endpoint"
grpc_url = "https://your-grpc-endpoint"
grpc_token = "your-token-if-needed"
grpc_engine = "yellowstone"

# Set mode to NotArb
mode = "na"

# Basic configuration
update_interval = 10000
mints_to_arb = [2, 2, 3]
mints_to_rank = 30

# Output file name
output = "notarb-config"  # Creates notarb-config.json

# NotArb-specific settings
[notarb]
output = "notarb-config"
jvm_args = ["-server", "-Xmx8192m"]
keypair_path = "${DEFAULT_KEYPAIR_PATH}"
protect_keypair = true
threads = 0  # Dynamic thread pool
flash_loan = true
max_lookup_tables = 10
meteora_bin_limit = 20

# IPs for proxy (optional)
ips_file_path = ""
prefunded_keypairs_path = ""

# Jito endpoints
jito_targets = [
  "https://ny.mainnet.block-engine.jito.wtf",
  "https://tokyo.mainnet.block-engine.jito.wtf",
  "https://slc.mainnet.block-engine.jito.wtf",
  "https://amsterdam.mainnet.block-engine.jito.wtf",
  "https://frankfurt.mainnet.block-engine.jito.wtf",
]

max_bundle_transactions = 1

# Connection pools
token_accounts_checker = { rpc_url = "", delay_seconds = 3 }
blockhash_updater = { rpc_url = "", delay_ms = 400 }
account_size_loader = { rpc_url = "", invalid_account_size = 200, buffer_size = 1500 }

# WSOL unwrapper
wsol_unwrapper = { 
  enabled = true, 
  check_minutes = 1, 
  trigger_sol = 0.5, 
  target_sol = 5, 
  priority_fee_lamports = 190,
  reader_rpc_url = ""
}
```

## Running the System

### Step 1: Start arb-assist

Navigate to onchain-bot directory:
```bash
cd ~/notarb/Release/onchain-bot
```

Using tmux:
```bash
tmux new -s arb-assist
ulimit -n 65536
./arb-assist

# Detach with Ctrl+B, then D
```

### Step 2: Start NotArb

From the main NotArb directory:
```bash
cd ~/notarb/Release

# Start with PM2 watching config changes
pm2 start ./notarb.sh \
  --interpreter bash \
  --name notarb-onchain \
  --watch onchain-bot/notarb-config.json \
  -- onchain-bot/notarb-config.json
```

### Step 3: Monitor Operation

View logs:
```bash
# arb-assist logs
tmux attach -t arb-assist

# NotArb logs
pm2 logs notarb-onchain

# Combined monitoring
pm2 monit
```

## Generated Files

### notarb-config.json

Main configuration file:
```json
{
  "jvm_args": ["-server", "-Xmx8192m"],
  "keypair_path": "${DEFAULT_KEYPAIR_PATH}",
  "protect_keypair": true,
  "threads": 0,
  "flash_loan": true,
  "max_lookup_tables": 10,
  "meteora_bin_limit": 20,
  "jito_targets": [
    "https://ny.mainnet.block-engine.jito.wtf",
    "https://tokyo.mainnet.block-engine.jito.wtf"
  ],
  "max_bundle_transactions": 1
}
```

### markets.json

Trading pairs organized by groups:
```json
{
  "groups": [
    [
      "MarketAddress1...",
      "MarketAddress2..."
    ],
    [
      "MarketAddress3...",
      "MarketAddress4...",
      "MarketAddress5..."
    ]
  ],
  "update_timestamp": 1753221082643
}
```

Structure:
- 2D array matching `mints_to_arb` configuration
- Each sub-array is a transaction group
- Timestamp for version tracking

### lookup-tables.json

Address lookup tables:
```json
[
  "ALUTAddress1...",
  "ALUTAddress2...",
  "ALUTAddress3...",
  "ALUTAddress4..."
]
```

Purpose:
- Reduce transaction size
- Include commonly used addresses
- Updated dynamically

### notarb-attributes.json

Dynamic trading parameters:
```json
{
  "cu_limit": 400000,
  "jito_cooldown": 350,
  "jito_enabled": true,
  "jito_min_tip": 5000,
  "jito_max_tip": 25000,
  "spam_cooldown": 400,
  "spam_enabled": true,
  "spam_min_fee": 10000,
  "spam_max_fee": 50000
}
```

Updates based on:
- Market conditions
- Network congestion
- Competition levels

## Advanced Configuration

### JVM Optimization

<!-- ⚠️ REVIEW: JVM optimization settings below are suggestions and should be tested based on your specific hardware and workload -->
Tune Java performance:
```toml
jvm_args = [
  "-server",                    # Server JVM
  "-Xmx8192m",                 # 8GB heap
  "-Xms4096m",                 # 4GB initial heap
  "-XX:+UseG1GC",              # G1 garbage collector
  "-XX:MaxGCPauseMillis=50",   # 50ms max pause
  "-XX:+UseStringDeduplication" # String optimization
]
```

Memory recommendations:
- **8GB RAM**: `-Xmx4096m`
- **16GB RAM**: `-Xmx8192m`
- **32GB RAM**: `-Xmx16384m`

### Thread Management

Configure thread pools:
```toml
threads = 0    # Dynamic (recommended)
threads = -1   # Unlimited threads
threads = 16   # Fixed 16 threads
```

Considerations:
- **0**: Adapts to workload
- **-1**: Maximum parallelism
- **Fixed**: Predictable resource usage

### Meteora DLMM Settings

For Meteora dynamic pools:
```toml
meteora_bin_limit = 20  # Max bins to consider
```

Higher values:
- More price points
- Better pricing
- Higher computation

### WSOL Management

Automatic unwrapping:
```toml
wsol_unwrapper = {
  enabled = true,           # Auto-unwrap WSOL
  check_minutes = 1,        # Check every minute
  trigger_sol = 0.5,        # Unwrap if SOL < 0.5
  target_sol = 5,           # Unwrap to 5 SOL
  priority_fee_lamports = 190,
  reader_rpc_url = ""       # Optional dedicated RPC
}
```

## Multi-Region Setup

### Jito Regions

Configure multiple regions:
```toml
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

Benefits:
- Geographic distribution
- Redundancy
- Lower latency

### Proxy Configuration

For distributed execution:
```toml
ips_file_path = "/path/to/ips.txt"
prefunded_keypairs_path = "/path/to/keypairs.txt"
```

IP file format:
```
192.168.1.100
192.168.1.101
192.168.1.102
```

## Performance Optimization

### Connection Pools

Optimize RPC usage:
```toml
# Fast blockhash updates
blockhash_updater = { 
  rpc_url = "https://fastest-rpc.com", 
  delay_ms = 300 
}

# Slower account checks
token_accounts_checker = { 
  rpc_url = "https://standard-rpc.com", 
  delay_seconds = 5 
}

# Bulk data loading
account_size_loader = { 
  rpc_url = "https://bulk-rpc.com", 
  buffer_size = 2000 
}
```

### Flash Loan Configuration

Enable capital efficiency:
```toml
flash_loan = true
borrow_amount = 500_000_000_000  # 500 SOL
```

Benefits:
- No upfront capital
- Larger positions
- Higher profits

## Monitoring & Troubleshooting

### Key Log Messages

**Successful Arbitrage**:
```
[INFO] Arbitrage completed: Mint: PUMP...xyz, Profit: 0.234 SOL
```

**Market Updates**:
```
[INFO] Markets updated: 45 pairs across 3 groups
```

**Configuration Reload**:
```
[INFO] Configuration reloaded from notarb-config.json
```

### Common Issues

**Java Heap Error**:
```
java.lang.OutOfMemoryError: Java heap space
```
Solution: Increase `-Xmx` value in `jvm_args`

**ALUTs Error**:
```
Error: Too many lookup tables
```
Solution: Reduce `max_lookup_tables`

**No Markets Found**:
Check:
1. GRPC connection active
2. Filter thresholds not too strict
3. DEXes configured correctly

### Performance Metrics

Monitor in NotArb logs:
- Transaction success rate
- Average execution time
- Profit per transaction
- Gas efficiency

## Best Practices

### Configuration Management

1. **Version Control**
   ```bash
   git init
   git add config.toml
   git commit -m "Initial NotArb config"
   ```

2. **Backup Configurations**
   ```bash
   cp notarb-config.json notarb-config.json.backup
   cp markets.json markets.json.backup
   ```

3. **Test Changes**
   - Make small incremental changes
   - Monitor for 1-2 hours
   - Roll back if issues arise

### Security

1. **Keypair Protection**
   - Always use protected keypairs
   - Store backups securely
   - Never expose unprotected keys

2. **Access Control**
   - Restrict onchain-bot directory
   - Use proper file permissions
   - Monitor access logs

### Maintenance

Daily:
- Check bot is running
- Review profit/loss
- Monitor error logs

Weekly:
- Analyze performance trends
- Update configurations
- Check for NotArb updates

Monthly:
- Full system review
- Optimize JVM settings
- Update dependencies

## Integration Checklist

- [ ] NotArb Release cloned
- [ ] Java 11+ installed
- [ ] arb-assist in onchain-bot directory
- [ ] Keypair protected
- [ ] config.toml configured
- [ ] Test run successful
- [ ] PM2 automation setup
- [ ] Monitoring configured
- [ ] Backup procedures in place