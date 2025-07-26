# Common Errors

This guide covers the most frequently encountered errors and their solutions.

## Configuration Errors

### Dummy Configuration Generated

**Symptom**: Your bot config contains dummy values
```toml
mint = "11111111111111111111111111111111"
```

**Causes**:
- No mints meet your filter criteria
- GRPC stream not receiving data
- Arbitrage programs not active

**Solutions**:
1. Lower filter thresholds:
```toml
filter_thresholds = [{
  min_profit = 100_000,      # Lower from 1M
  min_roi = 1.0,            # Lower from 2.0
  min_txns = 1,             # Lower from 5
}]
```

2. Check GRPC connection:
```bash
# Look for connection messages in logs
[INFO] Connected to Yellowstone GRPC
[INFO] Streaming transactions...
```

3. Verify arbitrage programs are active:
- Check programs on-chain
- Try adding more programs
- Disable `filter_programs` temporarily

### Invalid License Error

**Error**:
```
Error: Invalid license file
```

**Solutions**:
1. Verify license file is in correct directory
2. Check IP address matches your server:
```bash
# Check your IP
curl ifconfig.me
```
3. Ensure license filename format: `xxx.xxx.xxx.xxx.license`
4. Check file permissions:
```bash
chmod 644 *.license
```

### Configuration Parse Error

**Error**:
```
Error: Failed to parse config.toml
```

**Common Issues**:
1. **Invalid TOML syntax**:
```toml
# Wrong
array = [1, 2, 3,]  # Trailing comma

# Correct
array = [1, 2, 3]
```

2. **Missing quotes**:
```toml
# Wrong
rpc_url = https://example.com

# Correct
rpc_url = "https://example.com"
```

3. **Type mismatches**:
```toml
# Wrong
min_profit = "1000000"  # String instead of number

# Correct
min_profit = 1_000_000
```

## Runtime Errors

### Too Many Open Files

**Error**:
```
called `Result::unwrap()` on an `Err` value: Os { code: 24, kind: Uncategorized, message: "Too many open files" }
```

**Immediate Fix**:
```bash
ulimit -n 65536
```

**Permanent Fix**:
```bash
# Add to /etc/security/limits.conf
* soft nofile 65536
* hard nofile 65536

# Add to your startup script
ulimit -n 65536
```

### GRPC Connection Failed

**Error**:
```
Error: Failed to connect to GRPC endpoint
```

**Troubleshooting Steps**:
1. Verify URL format:
```toml
grpc_url = "http://grpc.example.com:10001"  # Note: http not https
```

2. Check authentication:
```toml
grpc_token = "your-token"  # If required
grpc_engine = "yellowstone"  # or "thor"
```

3. Test connectivity:
```bash
curl -v http://grpc.example.com:10001
```

4. Verify firewall allows outbound connections

### RPC Connection Issues

**Error**:
```
Error: RPC request failed: 429 Too Many Requests
```

**Solutions**:
1. Use premium RPC with higher limits
2. Add multiple RPC endpoints:
```toml
sending_rpc_urls = [
  "https://rpc1.com",
  "https://rpc2.com",
  "https://rpc3.com",
]
```
3. Increase `update_interval` to reduce requests

## Bot-Specific Errors

### SMB: Failed to Create Token Account

**Error**:
```
Failed to create token account: Error
```

**Cause**: Insufficient SOL for creating Associated Token Accounts (ATAs)

**Solution**:
1. Check wallet balance:
```bash
solana balance YOUR_WALLET_ADDRESS
```

2. Fund wallet with more SOL:
- Each ATA costs ~0.00203928 SOL
- Recommend keeping 0.5-1 SOL minimum

3. Enable WSOL unwrapping (NotArb):
```toml
wsol_unwrapper = {
  enabled = true,
  trigger_sol = 0.5,
  target_sol = 5
}
```

### SMB: Private Key Not Found

**Error**:
```
Error: Cannot find wallet key file
```

**Solutions**:
1. Ensure encrypted key file exists
2. Check file is in same directory as smb-onchain
3. Verify encryption was successful:
```bash
ls -la *.key
```

### NotArb: Java Heap Space

**Error**:
```
java.lang.OutOfMemoryError: Java heap space
```

**Solution**:
Increase heap size in config:
```toml
[notarb]
jvm_args = ["-server", "-Xmx16384m"]  # Increase from 8GB to 16GB
```

### NotArb: Too Many Lookup Tables

**Error**:
```
Error: Transaction too large - too many lookup tables
```

**Solution**:
Reduce lookup tables:
```toml
[notarb]
max_lookup_tables = 5  # Reduce from 10
```

## Download Errors

### HTML Content Instead of Binary

**Error**:
```
./arb-assist: line 1: syntax error near unexpected token `<'
./arb-assist: line 1: `<!DOCTYPE html>'
```

**Cause**: Downloaded GitHub page instead of binary

**Solution**:
Use direct download link:
```bash
# Correct
wget https://github.com/capicua4454/arb-assist/raw/refs/heads/main/arb-assist

# Wrong (downloads HTML page)
wget https://github.com/capicua4454/arb-assist/
```

## Performance Issues

### High CPU Usage

**Symptoms**:
- 100% CPU utilization
- Slow config updates
- System unresponsive

**Solutions**:
1. Increase update interval:
```toml
update_interval = 30000  # 30 seconds instead of 10
```

2. Reduce tracked mints:
```toml
mints_to_rank = 10  # Instead of 50
```

3. Enable program filtering:
```toml
filter_programs = true
```

### Memory Leaks

**Symptoms**:
- Gradually increasing memory usage
- Eventually crashes with OOM

**Solutions**:
1. Reduce data retention:
```toml
halflife = 60000  # Faster decay
```

2. Limit scope:
```toml
mints_to_rank = 20  # Lower number
aluts_per_pool = 10  # Fewer ALUTs
```

3. Restart periodically:
```bash
# Add to crontab
0 */6 * * * pm2 restart arb-assist
```

## Market Data Issues

### No Arbitrage Opportunities Found

**Symptoms**:
- Consistently empty mint lists
- No profitable trades identified

**Debugging Steps**:
1. Check market activity:
```bash
# Look for transaction counts in logs
[INFO] Analyzed 1,234 transactions
[INFO] Found 0 arbitrage transactions  # Problem here
```

2. Verify DEX configuration:
```toml
dexes = [
  "675kPX9MHTjS2zt1qfr1NYHuzeLXfQM9H24wFSUt1Mp8",  # Raydium
  "whirLbMiicVdio4qvUfM5KAg6Ct8VwpYzGff3uctyCc",  # Orca
]
```

3. Try broader filters:
```toml
ignore_filters = true  # Temporarily bypass all filters
```

### Stale Data

**Symptoms**:
- Outdated mint information
- Missed opportunities
- Wrong pool data

**Solutions**:
1. Check timestamps in generated files
2. Verify GRPC stream is active
3. Reduce halflife for faster updates:
```toml
halflife = 30000  # 30 seconds
```

## Network Issues

### Firewall Blocking Connections

**Symptoms**:
- Cannot connect to RPC/GRPC
- Timeouts on all requests

**Solutions**:
1. Check outbound rules:
```bash
sudo ufw status
```

2. Allow necessary connections:
```bash
sudo ufw allow out 443/tcp  # HTTPS
sudo ufw allow out 80/tcp   # HTTP
sudo ufw allow out 10000:10010/tcp  # Common GRPC ports
```

### DNS Resolution Failures

**Error**:
```
Error: Failed to resolve hostname
```

**Solutions**:
1. Check DNS settings:
```bash
cat /etc/resolv.conf
```

2. Add reliable DNS servers:
```bash
echo "nameserver 8.8.8.8" | sudo tee -a /etc/resolv.conf
echo "nameserver 1.1.1.1" | sudo tee -a /etc/resolv.conf
```

## Quick Diagnostics Checklist

When encountering issues, check:

1. **Logs**:
```bash
pm2 logs arb-assist --lines 100
```

2. **System Resources**:
```bash
htop  # CPU and memory
df -h  # Disk space
```

3. **Network Connectivity**:
```bash
ping google.com
curl -I https://api.mainnet-beta.solana.com
```

4. **File Permissions**:
```bash
ls -la arb-assist
ls -la *.license
ls -la config.toml
```

5. **Process Status**:
```bash
pm2 status
ps aux | grep arb-assist
```

## Getting Help

If you can't resolve an issue:

1. **Collect Information**:
   - Error messages
   - Config file (remove sensitive data)
   - System specifications
   - Recent changes made

2. **Join Discord**:
   - [Discord Server](https://discord.gg/ADtnjdy5m5)
   - Post in #support channel
   - Include collected information

3. **GitHub Issues**:
   - Search existing issues first
   - Create detailed bug report
   - Include reproduction steps