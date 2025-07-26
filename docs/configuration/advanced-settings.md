# Advanced Settings

This section covers advanced configuration options for fine-tuning arb-assist performance and behavior.

## Core Advanced Settings

### update_interval

**Type**: `integer` (milliseconds)  
**Default**: `10000` (10 seconds)  
**Description**: How often to regenerate configuration files

```toml
update_interval = 5000   # Fast updates (5 seconds)
update_interval = 10000  # Standard (10 seconds)
update_interval = 30000  # Conservative (30 seconds)
```

Trade-offs:
- **Lower**: Fresher data, higher CPU usage
- **Higher**: Less resource usage, delayed reactions

### run_interval

**Type**: `integer` (milliseconds)  
**Default**: `60000` (1 minute)  
**Description**: Minimum time between trades on the same mint

```toml
run_interval = 30000   # Aggressive (30 seconds)
run_interval = 60000   # Standard (1 minute)
run_interval = 300000  # Conservative (5 minutes)
```

Purpose:
- Prevents over-trading
- Allows market to settle
- Reduces failed transactions

### halflife

**Type**: `integer` (milliseconds)  
**Default**: `120000` (2 minutes)  
**Description**: Time for statistics to decay by 50%

```toml
halflife = 60000   # Fast decay (1 minute)
halflife = 120000  # Standard (2 minutes)
halflife = 300000  # Slow decay (5 minutes)
```

Effects on different metrics:
- **Profit**: Recent profits weighted more
- **Volume**: Old volume fades out
- **Success rate**: Adapts to current performance

### Visual Example of Half-life Decay

```
Time     | Weight
---------|--------
Now      | 100%
1 min    | 50%
2 min    | 25%
3 min    | 12.5%
4 min    | 6.25%
```

## Advanced Filtering

### ignore_filters

**Type**: `boolean`  
**Default**: `false`  
**Description**: Bypass all filter thresholds

```toml
ignore_filters = false  # Apply filters normally
ignore_filters = true   # Use any mint with 2+ pools
```

Use cases:
- Testing configurations
- Maximum opportunity capture
- With Jito no_failure_mode

### Complex Filter Expressions

Combine multiple criteria:

```toml
filter_thresholds = [
  {
    # Basic requirements
    min_profit = 1_000_000,
    min_roi = 1.5,
    min_txns = 5,
    
    # Volume requirements
    min_net_volume = 100_000_000,
    min_total_volume = 1_000_000_000,
    
    # Balance requirements
    min_imbalance_ratio = 0.1,  # At least 10% directional
    max_imbalance_ratio = 0.9,  # Not more than 90% one-sided
    
    # Market depth
    min_liquidity = 10_000_000_000,
    min_turnover = 0.5,         # Volume/Liquidity ratio
    
    # Volatility
    min_volatility = 0.001,     # Minimum price movement
    
    # Profit consistency
    min_profit_per_arb = 100_000,  # Average profit per trade
  }
]
```

## ALUTs (Address Lookup Tables)

### aluts_per_pool

**Type**: `integer`  
**Default**: `20`  
**Description**: Number of ALUTs to collect per pool

```toml
aluts_per_pool = 10   # Minimal
aluts_per_pool = 20   # Standard
aluts_per_pool = 50   # Comprehensive
```

### aluts

**Type**: `array`  
**Default**: `[]`  
**Description**: Custom ALUTs to always include

```toml
aluts = [
  "4sKLJ1Qoudh8PJyqBeuKocYdsZvxTcRShUt9aKqwhgvC",  # Custom ALUT 1
  "7nYmUgQPvvKN9vnt3xHukiGcgaqvBPfmFfkJcJQHHbsD",  # Custom ALUT 2
]
```

### ALUT Strategy

For NotArb:
```toml
[notarb]
max_lookup_tables = 10  # Maximum ALUTs per transaction
```

Optimization tips:
- More ALUTs = larger transactions
- Fewer ALUTs = may miss addresses
- Monitor which ALUTs are actually used

## Logging and Monitoring

### log_output

**Type**: `boolean`  
**Default**: `false`  
**Description**: Enable detailed logging

```toml
log_output = true  # Enable verbose logging
```

Logs include:
- Mint rankings
- Filter decisions
- Configuration updates
- Performance metrics

### Custom Memos

Add identifying information to transactions:

```toml
filter_thresholds = [
  { memo = "conservative-v1", ... },
  { memo = "moderate-v2", ... },
  { memo = "aggressive-v3", ... },
]
```

## Mode-Specific Advanced Settings

### SMB Advanced Settings

```toml
[smb]
output = "smb-config"
max_retries = 0              # Retry failed transactions
enable_simple_send = false   # Use simple send mode
cetiloan = true             # Enable flash loans
merge_mints = true          # Allow mint merging
```

**cetiloan**: Flash loan integration
- Borrow funds for arbitrage
- No upfront capital needed
- Small fee for borrowing

**merge_mints**: Combine multiple mints
- More complex transactions
- Higher potential profit
- Increased gas usage

### NotArb Advanced Settings

```toml
[notarb]
output = "notarb-config"
jvm_args = ["-server", "-Xmx8192m", "-XX:+UseG1GC"]
threads = 0                  # 0 = dynamic, -1 = unlimited
flash_loan = true
meteora_bin_limit = 20       # Max Meteora DLMM bins
```

**JVM Optimization**:
```toml
jvm_args = [
  "-server",                 # Server JVM
  "-Xmx8192m",              # 8GB heap
  "-XX:+UseG1GC",           # G1 garbage collector
  "-XX:MaxGCPauseMillis=50" # 50ms max GC pause
]
```

**Thread Management**:
```toml
threads = 0    # Dynamic thread pool (recommended)
threads = -1   # Thread per task (maximum performance)
threads = 16   # Fixed thread pool (predictable)
```

## Network Optimization

### Connection Pooling

For NotArb:
```toml
[notarb]
# Separate RPC endpoints for different tasks
token_accounts_checker = { 
  rpc_url = "https://dedicated-checker.rpc.com", 
  delay_seconds = 3 
}

blockhash_updater = { 
  rpc_url = "https://fast-blockhash.rpc.com", 
  delay_ms = 400 
}

account_size_loader = { 
  rpc_url = "https://bulk-data.rpc.com", 
  invalid_account_size = 200, 
  buffer_size = 1500 
}
```

Benefits:
- Distribute load across endpoints
- Optimize for specific tasks
- Prevent rate limiting

### File Server Configuration

```toml
port = 8080  # Enable file server
port = 0     # Disable file server
```

Security for file server:
```bash
# UFW firewall rules
sudo ufw allow from 192.168.1.0/24 to any port 8080
sudo ufw deny 8080
```

## Performance Tuning

### Memory Management

Control memory usage:
1. Reduce `mints_to_rank`
2. Increase `halflife` (faster decay)
3. Limit `aluts_per_pool`
4. Increase `update_interval`

### CPU Optimization

Reduce CPU load:
1. Simplify sort strategies
2. Use fewer filter thresholds
3. Enable `filter_programs`
4. Increase delays

### Network Optimization

Reduce bandwidth:
1. Filter GRPC stream
2. Batch RPC requests
3. Use compression
4. Cache static data

## Special Configurations

### Market Maker Mode

Focus on providing liquidity:
```toml
# High turnover, balanced flow
intermint_sort_strategy = { metric = "turnover", direction = "descending" }

filter_thresholds = [{
  min_turnover = 2.0,
  min_imbalance_ratio = 0.4,
  max_imbalance_ratio = 0.6,
  min_liquidity = 100_000_000_000,
}]
```

### Sniper Mode

Target new opportunities:
```toml
# Sort by newest pools
intermint_sort_strategy = { metric = "pool_age", direction = "ascending" }

# Fast updates
update_interval = 5000
halflife = 30000  # 30 second decay

# Aggressive execution
spam_levels = [{
  process_delay = 200,
  tx_count = 5,
}]
```

### Research Mode

Collect maximum data:
```toml
# Track everything
mints_to_rank = 100
filter_programs = false
ignore_filters = true
log_output = true

# Slow updates to reduce load
update_interval = 60000
```

## Experimental Features

### Custom Market Loaders

For NotArb:
```toml
[notarb]
market_loader = "custom-loader-endpoint"
lookup_table_loader = "custom-alut-endpoint"
```

### Advanced Metrics

Custom sorting metrics (future):
```toml
# Potential future metrics
metric = "sharpe_ratio"      # Risk-adjusted returns
metric = "kelly_criterion"   # Optimal position sizing
metric = "entropy"          # Market efficiency
```

## Best Practices

### Configuration Management

1. **Version Control**
   - Track all config changes
   - Use meaningful commit messages
   - Tag stable configurations

2. **Testing Protocol**
   - Test new settings with small amounts
   - Monitor for 24 hours minimum
   - Compare performance metrics

3. **Documentation**
   - Comment complex configurations
   - Document parameter choices
   - Keep change log

### Performance Monitoring

Key metrics to track:
- CPU usage percentage
- Memory consumption
- Network bandwidth
- Success rate
- Average ROI
- Configuration generation time

### Gradual Optimization

1. Start with defaults
2. Change one parameter at a time
3. Measure impact
4. Document results
5. Iterate based on data

## Troubleshooting Advanced Issues

### Memory Leaks

Symptoms:
- Increasing memory usage over time
- System slowdowns
- OOM errors

Solutions:
- Reduce `mints_to_rank`
- Decrease `aluts_per_pool`
- Increase `halflife`
- Restart periodically

### CPU Spikes

Symptoms:
- 100% CPU usage
- Delayed config updates
- System unresponsive

Solutions:
- Increase `update_interval`
- Simplify sort strategies
- Enable `filter_programs`
- Upgrade hardware

### Network Saturation

Symptoms:
- Dropped GRPC connections
- RPC timeouts
- Incomplete data

Solutions:
- Upgrade bandwidth
- Filter GRPC stream
- Use multiple endpoints
- Implement caching