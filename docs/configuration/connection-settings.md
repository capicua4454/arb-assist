# Connection Settings

This section covers all network and connection-related configuration options for arb-assist.

## RPC Configuration

### rpc_url

**Type**: `string`  
**Required**: Yes  
**Description**: Solana RPC endpoint for fetching blockchain data

```toml
rpc_url = "https://api.mainnet-beta.solana.com"
```

#### Choosing an RPC Provider

**Public RPCs** (Not recommended for production):
```toml
rpc_url = "https://api.mainnet-beta.solana.com"
rpc_url = "https://solana-api.projectserum.com"
```

**Recommended RPCs**:
```toml
# Helius (cheaper option, fine for starting out)
rpc_url = "https://mainnet.helius-rpc.com/?api-key=YOUR_KEY"

# NFT-gated shared nodes (better performance)
rpc_url = "http://deez.top:80"
rpc_url = "http://shark.top:80"
rpc_url = "http://platinum.top:80"
rpc_url = "http://corvus.top:80"
rpc_url = "http://thor.top:80"
rpc_url = "http://vision.top:80"
```

### config_rpc_url

**Type**: `string`  
**Required**: No  
**Default**: Same as `rpc_url`  
**Description**: RPC endpoint for the generated bot configuration

```toml
config_rpc_url = "http://utah.deez.top:80"
```

Use this when you want arb-assist and your bot to use different RPCs.

### sending_rpc_urls

**Type**: `array`  
**Required**: Yes (for SMB mode)  
**Description**: RPC endpoints for sending transactions

```toml
sending_rpc_urls = [
  "https://mainnet.rpc1.com",
  "https://mainnet.rpc2.com",
  "https://mainnet.rpc3.com",
]
```

Best practices:
- Use 3-5 different RPC providers
- Mix geographic regions
- Include both premium and free endpoints
- Test latency to each endpoint

## GRPC Configuration

### grpc_url

**Type**: `string`  
**Required**: Yes  
**Description**: GRPC endpoint for streaming transaction data

```toml
grpc_url = "http://grpc.provider.com:10001"
```

### grpc_token

**Type**: `string`  
**Required**: Depends on provider  
**Description**: Authentication token for GRPC endpoint

```toml
grpc_token = "your-authentication-token"
```

For Yellowstone:
```toml
grpc_token = ""  # Often not required
```

For ThorStreamer:
```toml
grpc_token = "your-x-token"  # Usually required
```

### grpc_engine

**Type**: `string`  
**Required**: Yes  
**Values**: `"yellowstone"` | `"thor"`  
**Description**: Type of GRPC engine to use

```toml
grpc_engine = "yellowstone"  # For Yellowstone GRPC
# OR
grpc_engine = "thor"         # For ThorStreamer
```

#### Yellowstone GRPC

Characteristics:
- Native gRPC protocol
- Lower latency
- Better for high-frequency trading
- More stable connection

Configuration example:
```toml
grpc_url = "http://nl.grpc.deez.top:10001"
grpc_token = ""
grpc_engine = "yellowstone"
```

#### ThorStreamer

Characteristics:
- HTTP/2 based
- Easier firewall traversal
- Good for VPS environments
- Token authentication

Configuration example:
```toml
grpc_url = "https://thor.provider.com/stream"
grpc_token = "eyJ0eXAiOiJKV1QiLCJhbGc..."
grpc_engine = "thor"
```

## API Keys

### helius_key

**Type**: `string`  
**Required**: No  
**Description**: Helius API key for priority fee estimation

```toml
helius_key = "your-helius-api-key"
```

Benefits of using Helius:
- Real-time priority fee data
- Network congestion metrics
- More accurate fee estimation
- Reduced failed transactions

Without Helius key:
- Falls back to parsed transaction data
- Less accurate fee estimates
- May overpay or underpay fees

### jito_uuid

**Type**: `string`  
**Required**: No  
**Description**: UUID for Jito bundle identification

```toml
jito_uuid = "abcd-efgh-ijklm-opqrs-11223344556600"
```

Used for:
- Tracking bundle performance
- Jito analytics
- Debugging bundle issues

## Network Settings

### port

**Type**: `integer`  
**Required**: No  
**Default**: `0` (disabled)  
**Description**: Port for serving configuration files over HTTP

```toml
port = 8080  # Enable file server on port 8080
# OR
port = 0     # Disable file server
```

File server endpoints:
- `http://server:8080/smb-config.toml`
- `http://server:8080/notarb-config.toml`
- `http://server:8080/markets.json`
- `http://server:8080/lookup-tables.json`

Security considerations:
- Use firewall to restrict access
- Consider reverse proxy with authentication
- Monitor access logs

## Connection Pools

### For NotArb

Additional connection settings in the `[notarb]` section:

```toml
[notarb]
# Token account checking
token_accounts_checker = { 
  rpc_url = "https://separate-rpc.com", 
  delay_seconds = 3 
}

# Blockhash updates
blockhash_updater = { 
  rpc_url = "https://fast-rpc.com", 
  delay_ms = 400 
}

# Account data loading
account_size_loader = { 
  rpc_url = "https://data-rpc.com", 
  invalid_account_size = 200, 
  buffer_size = 1500 
}
```

These allow using different RPCs for different purposes:
- **token_accounts_checker**: Slower, less critical checks
- **blockhash_updater**: Fast, low-latency requirement
- **account_size_loader**: Bulk data operations

## Connection Troubleshooting

### RPC Issues

**Connection Refused**
```
Error: Connection refused to RPC endpoint
```
Solutions:
- Verify URL format (https:// vs http://)
- Check firewall rules
- Confirm API key in URL
- Test with curl: `curl YOUR_RPC_URL`

**Rate Limiting**
```
Error: 429 Too Many Requests
```
Solutions:
- Use premium RPC with higher limits
- Add multiple RPC URLs
- Reduce update_interval
- Implement request batching

### GRPC Issues

**Authentication Failed**
```
Error: GRPC authentication failed
```
Solutions:
- Verify grpc_token is correct
- Check token expiration
- Confirm grpc_engine matches provider
- Contact provider support

**Stream Interrupted**
```
Error: GRPC stream disconnected
```
Solutions:
- Check network stability
- Increase timeout values
- Use closer geographic endpoint
- Monitor bandwidth usage

## Performance Optimization

### Latency Reduction

1. **Geographic Proximity**
   - Use endpoints close to your VPS
   - Test ping times to each endpoint
   - Consider multi-region setup

2. **Connection Pooling**
   - Reuse HTTP connections
   - Maintain persistent GRPC streams
   - Implement reconnection logic

3. **Load Balancing**
   ```toml
   sending_rpc_urls = [
     "https://us-east.rpc.com",
     "https://us-west.rpc.com", 
     "https://eu.rpc.com",
   ]
   ```

### Bandwidth Management

Monitor bandwidth usage:
- GRPC streams: 50-200 Mbps typical
- RPC requests: 10-50 Mbps typical
- Total recommended: 1 Gbps connection

Reduce bandwidth:
- Filter unnecessary GRPC data
- Batch RPC requests
- Implement local caching
- Use compression where supported

## Best Practices

1. **Redundancy**
   - Multiple RPC providers
   - Fallback GRPC endpoints
   - Automatic failover logic

2. **Security**
   - Rotate API keys regularly
   - Use HTTPS where possible
   - Implement IP whitelisting
   - Monitor for suspicious activity

3. **Monitoring**
   - Track connection uptime
   - Monitor response times
   - Alert on failures
   - Log connection events

4. **Testing**
   ```bash
   # Test RPC
   curl -X POST -H "Content-Type: application/json" \
     -d '{"jsonrpc":"2.0","id":1,"method":"getBlockHeight"}' \
     YOUR_RPC_URL

   # Test GRPC (example)
   grpcurl -H "x-token: YOUR_TOKEN" \
     YOUR_GRPC_URL:PORT \
     list
   ```