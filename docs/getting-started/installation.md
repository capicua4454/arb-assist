# Installation

This guide walks you through installing arb-assist and the supported arbitrage bots.

## Overview

The installation process involves:
1. Installing system dependencies
2. Setting up Node.js environment
3. Installing process managers
4. Downloading and configuring your chosen bot
5. Installing arb-assist
6. Verifying the installation

## Step 1: System Dependencies

### Update System Packages
```bash
sudo apt update && sudo apt upgrade -y
```

### Install Essential Tools
```bash
sudo apt install -y wget curl git unzip build-essential
```

## Step 2: Install Node.js

### Using NVM (Recommended)

Install Node Version Manager:
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
```

Load NVM:
```bash
source "$HOME/.nvm/nvm.sh"
```

Install Node.js v22:
```bash
nvm install 22
nvm use 22
nvm alias default 22
```

### Verify Installation
```bash
node -v     # Should print "v22.x.x"
npm -v      # Should print "10.x.x"
nvm current # Should print "v22.x.x"
```

## Step 3: Install Process Managers

### PM2 (Process Manager)
```bash
npm install -g pm2

# Set up PM2 to start on boot
pm2 startup
# Follow the command it outputs
```

### TMUX (Terminal Multiplexer)
```bash
sudo apt-get install -y tmux
```

## Step 4A: Install SolanaMevBot On-Chain

### Create Directory
```bash
mkdir ~/smb
cd ~/smb
```

### Download SMB-Onchain
```bash
# Download latest version
wget https://sourceforge.net/projects/solanamevbotonchain/files/smb-onchain-0.9.8.zip
unzip smb-onchain-0.9.8.zip

# Make executable
chmod +x smb-onchain upgrade.sh

# Run upgrade script
./upgrade.sh
```

### Verify Installation
```bash
./smb-onchain --version
```

## Step 4B: Install NotArb (Alternative)

### Create Directory
```bash
mkdir ~/notarb
cd ~/notarb
```

### Clone NotArb Repository
```bash
git clone https://github.com/NotArb/Release.git
cd Release/onchain-bot
```

### Install Java (if needed)
```bash
# Check if Java is installed
java -version

# If not installed:
sudo apt install -y openjdk-11-jre-headless
```

## Step 5: Install arb-assist

### Navigate to Bot Directory
```bash
# For SMB-Onchain
cd ~/smb

# OR for NotArb
cd ~/notarb/Release/onchain-bot
```

### Download arb-assist
```bash
# Download the binary
wget https://github.com/capicua4454/arb-assist/raw/refs/heads/main/arb-assist

# Download example configuration
wget https://github.com/capicua4454/arb-assist/raw/refs/heads/main/config.toml.example

# Make executable
chmod +x arb-assist
```

### Set Up License File
```bash
# Copy your license file to the directory
# The license file should be named something like: xxx.xxx.xxx.xxx.license
# where xxx.xxx.xxx.xxx is your server IP

# Example:
cp ~/downloads/192.168.1.100.license .
```

### Create Configuration
```bash
# Copy example config
cp config.toml.example config.toml

# Edit configuration
nano config.toml
```

## Step 6: Directory Structure Verification

### For SMB-Onchain
Your directory should look like:
```
~/smb/
├── smb-onchain
├── upgrade.sh
├── arb-assist
├── config.toml
├── config.toml.example
└── xxx.xxx.xxx.xxx.license
```

### For NotArb
Your directory should look like:
```
~/notarb/Release/onchain-bot/
├── notarb.jar
├── arb-assist
├── config.toml
├── config.toml.example
└── xxx.xxx.xxx.xxx.license
```

## Step 7: Initial Configuration

### Edit config.toml

Set your RPC and GRPC endpoints:
```toml
rpc_url = "https://your-rpc-endpoint"
grpc_url = "https://your-grpc-endpoint"
grpc_token = "your-grpc-token"  # If required
grpc_engine = "yellowstone"      # or "thor"
```

Choose your mode:
```toml
mode = "smb"   # For SMB-Onchain
# OR
mode = "na"    # For NotArb
# OR
mode = "both"  # For both bots
```

### Set Up Private Key

{% hint style="danger" %}
**Security Warning**: Never put your private key directly in configuration files on a VPS. Always encrypt it first.
{% endhint %}

#### For SMB-Onchain
1. Create a temporary config with your private key locally
2. Run SMB to encrypt it
3. Transfer only the encrypted key file to your VPS
4. Delete the temporary config

#### For NotArb
1. Use NotArb's keypair protection feature
2. Follow the NotArb documentation for secure key management

## Step 8: Test Installation

### Increase File Limits
```bash
ulimit -n 65536
```

### Test arb-assist
```bash
./arb-assist
```

You should see:
- License validation message
- GRPC connection confirmation
- Data streaming messages

### Expected Output
```
[INFO] License validated successfully
[INFO] Connected to Yellowstone GRPC
[INFO] Streaming transactions...
[INFO] Analyzing arbitrage opportunities...
```

Press `Ctrl+C` to stop the test.

## Step 9: Production Setup

### Using TMUX
```bash
# Create new session
tmux new -s arb-assist

# Inside tmux
ulimit -n 65536
./arb-assist

# Detach from session
# Press Ctrl+B, then D
```

### Using PM2
```bash
# Create startup script
cat > start-arb-assist.sh << 'EOF'
#!/bin/bash
ulimit -n 65536
exec ./arb-assist
EOF

chmod +x start-arb-assist.sh

# Start with PM2
pm2 start start-arb-assist.sh --name arb-assist
pm2 save
```

## Troubleshooting Installation

### Common Issues

**License Error**
```
Error: Invalid license file
```
- Ensure license file is in the correct directory
- Verify the IP address matches your server
- Check file permissions

**GRPC Connection Failed**
```
Error: Failed to connect to GRPC endpoint
```
- Verify GRPC URL is correct
- Check authentication token
- Ensure firewall allows outbound connections

**Too Many Open Files**
```
Error: Too many open files
```
- Run `ulimit -n 65536` before starting
- Add to `/etc/security/limits.conf` for permanent fix

### Getting Help

If you encounter issues:
1. Check the [Troubleshooting](../troubleshooting/common-errors.md) section
2. Join our [Discord](https://discord.gg/ADtnjdy5m5)
3. Review bot-specific documentation

## Next Steps

Installation complete! Proceed to:
- [Quick Start Guide](quick-start.md) for running your first arbitrage session
- [Configuration Overview](../configuration/overview.md) for detailed setup options