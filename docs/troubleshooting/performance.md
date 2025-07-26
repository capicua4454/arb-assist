# Performance Issues

This guide helps diagnose and resolve performance-related problems with arb-assist.

## CPU Performance

### High CPU Usage

**Symptoms**:
- Consistent 100% CPU usage
- Slow response times
- Delayed config updates
- System becomes unresponsive

**Diagnosis**:
```bash
# Check CPU usage
htop
# or
top -p $(pgrep arb-assist)

# Check process details
ps aux | grep arb-assist
```

### CPU Optimization Strategies

#### 1. Reduce Processing Frequency

Increase update intervals:
```toml
update_interval = 30000  # 30 seconds (from 10)
run_interval = 120000    # 2 minutes (from 1)
```

#### 2. Limit Data Scope

Reduce the amount of data processed:
```toml
# Fewer mints to track
mints_to_rank = 10  # From 50

# Fewer pools per mint
filter_thresholds = [{
  top_pool_num = 2,  # From 5
}]

# Filter programs
filter_programs = true
arb_programs = ["MEVi...", "NA24..."]  # Only proven programs
```

#### 3. Simplify Computations

Use simpler sorting strategies:
```toml
# Simple metric
intermint_sort_strategy = { metric = "profit", direction = "descending" }

# Avoid complex metrics like volatility or turnover
```

#### 4. Enable Caching

Reduce redundant calculations:
```toml
# Longer halflife = less frequent recalculation
halflife = 300000  # 5 minutes
```

### CPU Profiling

Monitor which operations consume most CPU:

1. **Enable logging**:
```toml
log_output = true
```

2. **Check operation timings** in logs:
```
[DEBUG] GRPC processing: 234ms
[DEBUG] Mint ranking: 89ms
[DEBUG] Config generation: 45ms
```

3. **Identify bottlenecks** and optimize accordingly

## Memory Performance

### Memory Leaks

**Symptoms**:
- Gradually increasing memory usage
- Eventually crashes with OOM
- System swap usage increases

**Diagnosis**:
```bash
# Monitor memory usage over time
watch -n 5 'ps aux | grep arb-assist | grep -v grep'

# Check system memory
free -h

# Monitor swap usage
vmstat 1
```

### Memory Optimization

#### 1. Reduce Data Retention

Faster decay of old data:
```toml
halflife = 60000  # 1 minute (aggressive decay)
```

#### 2. Limit Tracked Entities

Reduce memory footprint:
```toml
mints_to_rank = 15      # Lower limit
aluts_per_pool = 5      # Fewer ALUTs
dexes = [               # Only essential DEXes
  "675k...",  # Raydium
  "whir...",  # Orca
]
```

#### 3. Configure Garbage Collection

For systems with limited memory:
```toml
# Restart periodically to clear memory
# Add to crontab:
0 */4 * * * systemctl restart arb-assist
```

### Memory Usage Estimation

Calculate approximate memory usage:
```
Base overhead: ~50 MB
Per mint: ~100 KB
Per pool: ~10 KB
Per ALUT: ~1 KB

Total = 50 MB + (mints * 100 KB) + (mints * pools * 10 KB) + (mints * aluts * 1 KB)
```

Example with 50 mints, 5 pools each, 20 ALUTs:
```
50 MB + (50 * 100 KB) + (50 * 5 * 10 KB) + (50 * 20 * 1 KB)
= 50 MB + 5 MB + 2.5 MB + 1 MB
= 58.5 MB
```

## Network Performance

### Bandwidth Issues

**Symptoms**:
- GRPC disconnections
- Incomplete data
- Slow updates
- RPC timeouts

**Diagnosis**:
```bash
# Monitor network usage
iftop
# or
nethogs

# Check bandwidth
speedtest-cli

# Monitor specific connection
tcpdump -i any host grpc.example.com
```

### Network Optimization

#### 1. GRPC Stream Filtering

Reduce data volume by filtering at source:
```toml
filter_programs = true  # Only specific programs
exclude_mints = [       # Skip unnecessary tokens
  "So11111111111111111111111111111111111112",
  "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v",
]
```

#### 2. RPC Optimization

Distribute load across multiple endpoints:
```toml
# Different RPCs for different purposes
rpc_url = "https://data-rpc.com"      # For reads
config_rpc_url = "https://fast-rpc.com" # For bot
sending_rpc_urls = [                    # For sends
  "https://send1.com",
  "https://send2.com",
]
```

#### 3. Connection Pooling

For NotArb, use dedicated connections:
```toml
[notarb]
token_accounts_checker = { rpc_url = "https://slow-rpc.com", delay_seconds = 5 }
blockhash_updater = { rpc_url = "https://fast-rpc.com", delay_ms = 300 }
```

### Latency Optimization

Reduce network latency:

1. **Geographic Proximity**:
   - Use RPC/GRPC endpoints near your server
   - Consider multi-region deployment

2. **Connection Reuse**:
   - Maintain persistent connections
   - Avoid connection churn

3. **Batch Operations**:
   - Group related requests
   - Reduce round trips

## Disk I/O Performance

### Slow Config Updates

**Symptoms**:
- Delays in writing config files
- File system errors
- Disk space issues

**Diagnosis**:
```bash
# Check disk usage
df -h

# Monitor disk I/O
iostat -x 1

# Check write speed
dd if=/dev/zero of=test.file bs=1M count=1000
```

### Disk Optimization

#### 1. Use Fast Storage

- Prefer NVMe SSD over HDD
- Use local storage over network storage
- Ensure adequate free space (>20%)

#### 2. Reduce Write Frequency

For less critical updates:
```toml
update_interval = 60000  # Update every minute
```

#### 3. Optimize File Operations

Use efficient file writing:
```toml
# Disable unnecessary outputs
log_output = false

# Use single output mode
mode = "smb"  # Not "both"
```

## System Resource Monitoring

### Comprehensive Monitoring Setup

1. **Install monitoring tools**:
```bash
sudo apt install htop iotop iftop sysstat
```

2. **Create monitoring script**:
```bash
#!/bin/bash
# monitor-arb-assist.sh
while true; do
  echo "=== $(date) ==="
  ps aux | grep arb-assist | grep -v grep
  free -h | grep -E "Mem|Swap"
  iostat -x 1 1 | grep -E "avg-cpu|sda"
  echo ""
  sleep 60
done
```

3. **Log resource usage**:
```bash
./monitor-arb-assist.sh > resource-usage.log 2>&1 &
```

### Performance Baselines

Typical resource usage for reference:

| Metric | Light Load | Medium Load | Heavy Load |
|--------|-----------|-------------|------------|
| CPU | 10-20% | 30-50% | 70-90% |
| Memory | 100-200 MB | 300-500 MB | 1-2 GB |
| Network | 10-50 Mbps | 50-100 Mbps | 100-200 Mbps |
| Disk I/O | <1 MB/s | 1-5 MB/s | 5-10 MB/s |

## Performance Tuning Workflow

### Step 1: Identify Bottleneck

1. Check CPU usage
2. Monitor memory consumption
3. Analyze network traffic
4. Review disk I/O

### Step 2: Apply Optimization

Based on bottleneck:
- **CPU**: Reduce processing frequency/scope
- **Memory**: Limit data retention/tracking
- **Network**: Filter streams, use better endpoints
- **Disk**: Use faster storage, reduce writes

### Step 3: Measure Impact

1. Record baseline metrics
2. Apply single change
3. Monitor for 1-2 hours
4. Compare metrics
5. Keep or revert change

### Step 4: Iterate

Continue optimizing until:
- Performance is acceptable
- Resource usage is sustainable
- Arbitrage effectiveness maintained

## Advanced Performance Tips

### 1. Process Priority

Increase arb-assist priority:
```bash
# Nice value (-20 highest, 19 lowest)
sudo renice -n -10 -p $(pgrep arb-assist)
```

### 2. CPU Affinity

Dedicate CPU cores:
```bash
# Assign to cores 2 and 3
taskset -cp 2,3 $(pgrep arb-assist)
```

### 3. Kernel Tuning

Optimize network stack:
```bash
# /etc/sysctl.conf
net.core.rmem_max = 134217728
net.core.wmem_max = 134217728
net.ipv4.tcp_rmem = 4096 87380 134217728
net.ipv4.tcp_wmem = 4096 65536 134217728
net.core.netdev_max_backlog = 5000
```

### 4. File System Optimization

Use appropriate mount options:
```bash
# For SSD in /etc/fstab
/dev/sda1 / ext4 defaults,noatime,nodiratime 0 1
```

## When to Scale

Consider scaling when:
- Single instance can't handle load
- Need geographic distribution
- Require redundancy
- Want to specialize strategies

Scaling options:
1. **Vertical**: Upgrade server specs
2. **Horizontal**: Multiple instances
3. **Distributed**: Geographic spread
4. **Specialized**: Different configs for different strategies