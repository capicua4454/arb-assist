# Prerequisites

Before installing arb-assist, ensure your system meets all the requirements below.

## System Requirements

### Hardware Requirements

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| **CPU** | 8-core dedicated Ryzen | 16-core dedicated Ryzen |
| **RAM** | 16 GB | 32 GB |
| **Storage** | 1 GB | 1 GB |
| **Network** | Default/Basic | Default/Basic |

{% hint style="warning" %}
**Important**: arb-assist uses heavy GRPC streams that require significant CPU resources. Many users report CPU usage warnings above 70% on 8-core systems. Running on weaker hardware may result in missed opportunities or data processing delays.
{% endhint %}


### Operating System

- **Linux** (Ubuntu 22.04+ recommended)
- Windows via WSL2 (with performance limitations)
- macOS (not officially supported but may work)

### Network Requirements

#### RPC Endpoint
You need a reliable Solana RPC endpoint with:
- GetTransaction support
- GetBlock support
- Reasonable rate limits (10+ requests/second)

**Recommended providers:**
- Helius (cheaper option, fine for starting out)
- NFT-gated shared nodes (better performance: Deez, Shark, Platinum, Corvus, Thor, Vision, etc.)

#### GRPC Endpoint
You must have access to one of the following GRPC services:

**Yellowstone GRPC**
- Protocol: gRPC
- Requires subscription
- Lower latency
- More reliable

**ThorStreamer**
- Protocol: HTTP/2
- Requires API token
- Good coverage
- Alternative option

{% hint style="info" %}
**Note**: GRPC access typically requires a paid subscription. Please check with providers for current pricing.
{% endhint %}

## Software Requirements

### Node.js v22+
arb-assist requires Node.js version 22 or higher for certain features.

Check your version:
```bash
node -v
```

### Git
Required for cloning repositories and version control:
```bash
git --version
```

### Build Tools
Some dependencies may require compilation:
```bash
# Ubuntu/Debian
sudo apt-get install build-essential

# CentOS/RHEL
sudo yum groupinstall "Development Tools"
```

## Bot Requirements

### For SMB-Onchain

1. **SMB-Onchain Binary**
   - Version 0.11.5 or higher
   - Valid SMB license

2. **Directory Structure**
   - arb-assist must be in the same directory as smb-onchain
   - License file must be in the same directory

### For NotArb

1. **NotArb Release**
   - Latest version from GitHub
   - Java 11+ installed

2. **Directory Structure**
   - arb-assist must be in the `onchain-bot` subdirectory
   - Proper NotArb global configuration

## License Requirements

### arb-assist License

{% hint style="danger" %}
**Critical**: arb-assist requires a valid license file that is:
- Locked to your server's IP address
- Named in format: `license_username_serverIP_expiration.json`
- Placed in the same directory as the arb-assist binary
- **MUST NOT be renamed or modified** - keep it exactly as provided by the arb-assist team
{% endhint %}

To obtain a license:
1. Contact the arb-assist team
2. Provide your server's IP address
3. Receive your personalized license file
4. Do not share the license file
5. **Do not rename or modify the license file** - use it exactly as provided

### Bot Licenses

Each bot requires its own license:
- **SMB-Onchain**: Separate license from SolanaMevBot team
- **NotArb**: May require license depending on version

## Account Requirements

### Solana Wallet
- Funded wallet with:
  - At least 1 SOL for gas fees
  - 0.01 WSOL wrapped for ATA (Associated Token Account) creation
  - Additional arbitrage capital (or flash loan setup)
- Private key in standard Solana format

### API Keys (Optional but Recommended)

#### Helius API Key
For enhanced priority fee estimation:
- Sign up at [helius.dev](https://helius.dev)
- Free tier available
- Improves fee accuracy

#### Jito Authentication
For Jito bundle submission:
- Jito tip account
- Bundle authentication (if required)

## Network Configuration

### Firewall Rules

Open the following ports:
- **Outbound**: All (for RPC/GRPC connections)
- **Inbound**: 8080 (if using file server mode)

### DNS Resolution
Ensure your server can resolve:
- RPC endpoint domains
- GRPC endpoint domains
- npm registry (for dependencies)

## Performance Optimization

### System Limits
Increase file descriptor limits:
```bash
# Check current limit
ulimit -n

# Increase limit (temporary)
ulimit -n 65536

# Increase limit (permanent)
echo "* soft nofile 65536" | sudo tee -a /etc/security/limits.conf
echo "* hard nofile 65536" | sudo tee -a /etc/security/limits.conf
```

<!-- ### Network Tuning
Optimize network settings for low-latency arbitrage:
```bash
# Add to /etc/sysctl.conf

# Buffer sizes optimized for small packets (8MB is plenty for blockchain transactions)
net.core.rmem_max = 8388608
net.core.wmem_max = 8388608
net.ipv4.tcp_rmem = 4096 87380 8388608
net.ipv4.tcp_wmem = 4096 65536 8388608

# Low latency optimizations
net.ipv4.tcp_low_latency = 1        # Prioritize latency over throughput
net.ipv4.tcp_adv_win_scale = 1      # Better buffer efficiency
net.core.netdev_max_backlog = 5000  # Handle burst packet rates
net.ipv4.tcp_congestion_control = bbr # Better for small packets
net.ipv4.tcp_fastopen = 3            # Reduce connection setup time

# Apply changes
sudo sysctl -p
```

Disable NIC interrupt coalescing for lowest latency:
```bash
# Check current settings
sudo ethtool -c eth0

# Disable interrupt coalescing (adjust interface name as needed)
sudo ethtool -C eth0 rx-usecs 0
``` -->

## Verification Checklist

Before proceeding to installation, verify:

- [ ] Server meets hardware requirements
- [ ] Linux OS installed and updated
- [ ] RPC endpoint URL and credentials ready
- [ ] GRPC endpoint URL and authentication ready
- [ ] Node.js v22+ installed
- [ ] Git installed
- [ ] arb-assist license file obtained
- [ ] Bot software and licenses ready
- [ ] Funded Solana wallet
- [ ] Network connectivity confirmed

## Next Steps

Once all prerequisites are met, proceed to [Installation](installation.md).