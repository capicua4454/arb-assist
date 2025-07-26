# Connection Problems

This guide helps resolve network and connection-related issues with arb-assist.

## RPC Connection Issues

### Connection Refused

**Error**:
```
Error: Connection refused to RPC endpoint
```

**Common Causes**:
1. Incorrect URL format
2. Firewall blocking connection
3. RPC endpoint down
4. Invalid authentication

**Troubleshooting Steps**:

1. **Verify URL format**:
```toml
# Correct formats
rpc_url = "https://api.mainnet-beta.solana.com"
rpc_url = "http://localhost:8899"
rpc_url = "https://mainnet.helius-rpc.com/?api-key=YOUR_KEY"

# Common mistakes
rpc_url = "api.mainnet-beta.solana.com"  # Missing protocol
rpc_url = "https://api.mainnet-beta.solana.com/"  # Trailing slash
```

2. **Test connection**:
```bash
# Basic connectivity test
curl -I https://api.mainnet-beta.solana.com

# Test RPC method
curl -X POST -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"getHealth"}' \
  YOUR_RPC_URL
```

3. **Check firewall**:
```bash
# Check if port is open
nc -zv api.mainnet-beta.solana.com 443

# Check firewall rules
sudo ufw status
```

### Rate Limiting

**Error**:
```
Error: 429 Too Many Requests
```

**Solutions**:

1. **Use premium RPC**:
```toml
# Helius
rpc_url = "https://mainnet.helius-rpc.com/?api-key=YOUR_KEY"

# Triton
rpc_url = "https://YOUR_INSTANCE.triton.one/YOUR_TOKEN"

# QuickNode
rpc_url = "https://YOUR_ENDPOINT.quiknode.pro/YOUR_KEY/"
```

2. **Implement request distribution**:
```toml
# Multiple RPCs for different purposes
rpc_url = "https://read-rpc.com"  # For data fetching
config_rpc_url = "https://bot-rpc.com"  # For bot config

sending_rpc_urls = [  # For transaction sending
  "https://send1.com",
  "https://send2.com",
  "https://send3.com",
]
```

3. **Reduce request frequency**:
```toml
update_interval = 30000  # 30 seconds instead of 10
```

### SSL/TLS Errors

**Error**:
```
Error: SSL certificate problem
```

**Solutions**:

1. **Update certificates**:
```bash
sudo apt update
sudo apt install ca-certificates
sudo update-ca-certificates
```

2. **Check system time**:
```bash
# Incorrect time can cause SSL errors
timedatectl status
sudo timedatectl set-ntp true
```

3. **Use HTTP if appropriate** (local/private networks only):
```toml
rpc_url = "http://10.0.0.5:8899"  # Internal RPC
```

## GRPC Connection Issues

### Failed to Connect

**Error**:
```
Error: Failed to connect to GRPC endpoint
```

**Debugging Steps**:

1. **Verify GRPC configuration**:
```toml
# Yellowstone example
grpc_url = "http://grpc.example.com:10001"  # Note: often HTTP not HTTPS
grpc_token = ""  # May not need token
grpc_engine = "yellowstone"

# ThorStreamer example
grpc_url = "https://thor.example.com/stream"
grpc_token = "eyJ0eXAiOiJKV1QiLCJhbGc..."  # Required token
grpc_engine = "thor"
```

2. **Test GRPC connectivity**:
```bash
# For HTTP/2
curl -I http://grpc.example.com:10001

# Check if port is open
telnet grpc.example.com 10001
```

3. **Verify authentication**:
   - Check token hasn't expired
   - Ensure token is correctly formatted
   - Verify engine type matches provider

### Stream Interruptions

**Error**:
```
Error: GRPC stream disconnected
```

**Common Causes**:
1. Network instability
2. Provider-side timeout
3. Resource limits
4. Authentication expiry

**Solutions**:

1. **Implement reconnection logic** (arb-assist handles this automatically)

2. **Monitor connection stability**:
```bash
# Continuous ping test
ping -i 1 grpc.example.com | ts

# Monitor packet loss
mtr grpc.example.com
```

3. **Check provider status**:
   - Visit provider status page
   - Check Discord/support channels
   - Try alternative endpoints

### Authentication Failures

**Error**:
```
Error: GRPC authentication failed
```

**Solutions**:

1. **Verify token format**:
```toml
# JWT token (common)
grpc_token = "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9..."

# API key
grpc_token = "sk_live_abcd1234..."

# Empty (some providers)
grpc_token = ""
```

2. **Check token permissions**:
   - Ensure token has streaming permissions
   - Verify IP whitelist if applicable
   - Check rate limits on token

3. **Token rotation**:
   - Some providers rotate tokens
   - Update token in config
   - Restart arb-assist

## Network Configuration

### Firewall Issues

**Symptoms**:
- All external connections fail
- Local connections work
- Timeout errors

**Solutions**:

1. **Check outbound rules**:
```bash
# UFW (Ubuntu Firewall)
sudo ufw status verbose

# Allow outbound HTTPS
sudo ufw allow out 443/tcp

# Allow outbound HTTP
sudo ufw allow out 80/tcp

# Allow GRPC ports (common range)
sudo ufw allow out 10000:10010/tcp
```

2. **iptables rules**:
```bash
# List current rules
sudo iptables -L -n -v

# Allow outbound connections
sudo iptables -A OUTPUT -p tcp --dport 443 -j ACCEPT
sudo iptables -A OUTPUT -p tcp --dport 80 -j ACCEPT
```

3. **Cloud provider firewalls**:
   - AWS: Check Security Groups
   - GCP: Check VPC firewall rules
   - Azure: Check Network Security Groups

### DNS Resolution

**Error**:
```
Error: Failed to resolve hostname
```

**Solutions**:

1. **Check DNS configuration**:
```bash
# View current DNS servers
cat /etc/resolv.conf

# Test DNS resolution
nslookup api.mainnet-beta.solana.com
dig api.mainnet-beta.solana.com
```

2. **Add reliable DNS servers**:
```bash
# Edit resolv.conf
sudo nano /etc/resolv.conf

# Add these lines
nameserver 8.8.8.8      # Google DNS
nameserver 1.1.1.1      # Cloudflare DNS
nameserver 8.8.4.4      # Google DNS backup
```

3. **Fix systemd-resolved** (Ubuntu):
```bash
# Disable and reconfigure
sudo systemctl disable systemd-resolved
sudo systemctl stop systemd-resolved

# Create static resolv.conf
sudo rm /etc/resolv.conf
sudo bash -c 'echo "nameserver 8.8.8.8" > /etc/resolv.conf'
```

### Proxy Configuration

If behind a corporate proxy:

1. **System proxy settings**:
```bash
export HTTP_PROXY=http://proxy.company.com:8080
export HTTPS_PROXY=http://proxy.company.com:8080
export NO_PROXY=localhost,127.0.0.1
```

2. **Application-specific proxy**:
```toml
# Some configurations may support
proxy_url = "http://proxy.company.com:8080"
```

## Connection Pool Management

### Too Many Connections

**Symptoms**:
- Connection refused after running for a while
- "Too many connections" errors
- Resource exhaustion

**Solutions**:

1. **Reuse connections**:
```toml
# For NotArb - use same RPC for multiple purposes
[notarb]
token_accounts_checker = { rpc_url = "" }  # Empty = use main RPC
blockhash_updater = { rpc_url = "" }
```

2. **Connection limits**:
```bash
# Check current limits
ulimit -n

# Increase if needed
ulimit -n 65536
```

3. **Monitor connections**:
```bash
# Count connections to RPC
netstat -an | grep :443 | grep ESTABLISHED | wc -l

# Watch connections over time
watch 'netstat -an | grep :443 | grep ESTABLISHED | wc -l'
```

## Latency Optimization

### High Latency Issues

**Symptoms**:
- Slow responses
- Timeouts despite connectivity
- Missed opportunities

**Solutions**:

1. **Geographic optimization**:
```bash
# Test latency to endpoints
ping -c 10 api.mainnet-beta.solana.com
ping -c 10 api.mainnet-beta.solana.com | grep avg

# Traceroute to identify slow hops
traceroute api.mainnet-beta.solana.com
```

2. **Use regional endpoints**:
```toml
# Choose endpoints near your server
# US East
rpc_url = "https://us-east.rpc.com"

# Europe
rpc_url = "https://eu.rpc.com"

# Asia
rpc_url = "https://asia.rpc.com"
```

3. **Connection timing**:
```bash
# Measure connection time
time curl -I https://api.mainnet-beta.solana.com

# Detailed timing
curl -w "@curl-format.txt" -o /dev/null -s https://api.mainnet-beta.solana.com
```

Create `curl-format.txt`:
```
time_namelookup:  %{time_namelookup}s\n
time_connect:  %{time_connect}s\n
time_appconnect:  %{time_appconnect}s\n
time_pretransfer:  %{time_pretransfer}s\n
time_redirect:  %{time_redirect}s\n
time_starttransfer:  %{time_starttransfer}s\n
time_total:  %{time_total}s\n
```

## Connection Monitoring

### Setup Monitoring

1. **Create connection monitor script**:
```bash
#!/bin/bash
# monitor-connections.sh

while true; do
    echo "=== $(date) ==="
    
    # Test RPC
    curl -s -o /dev/null -w "RPC Response: %{http_code} Time: %{time_total}s\n" \
        -X POST -H "Content-Type: application/json" \
        -d '{"jsonrpc":"2.0","id":1,"method":"getHealth"}' \
        YOUR_RPC_URL
    
    # Test GRPC
    timeout 5 nc -zv YOUR_GRPC_HOST YOUR_GRPC_PORT &>/dev/null
    if [ $? -eq 0 ]; then
        echo "GRPC: Connected"
    else
        echo "GRPC: Failed"
    fi
    
    # Count connections
    echo "Active connections: $(netstat -an | grep ESTABLISHED | wc -l)"
    
    echo ""
    sleep 60
done
```

2. **Run monitor**:
```bash
chmod +x monitor-connections.sh
./monitor-connections.sh > connection-monitor.log 2>&1 &
```

### Alert on Failures

Setup alerts for connection issues:

```bash
#!/bin/bash
# alert-on-failure.sh

check_connection() {
    curl -s -f -X POST -H "Content-Type: application/json" \
        -d '{"jsonrpc":"2.0","id":1,"method":"getHealth"}' \
        YOUR_RPC_URL > /dev/null
    return $?
}

if ! check_connection; then
    echo "RPC connection failed at $(date)" | mail -s "arb-assist Alert" your@email.com
    # Or send to Discord webhook, Telegram, etc.
fi
```

## Best Practices

1. **Use multiple endpoints**: Always have fallbacks
2. **Monitor continuously**: Catch issues early
3. **Document endpoints**: Keep a list of working endpoints
4. **Test before deploying**: Verify all connections work
5. **Have emergency contacts**: Provider support, Discord channels
6. **Keep credentials secure**: Use environment variables or secure storage