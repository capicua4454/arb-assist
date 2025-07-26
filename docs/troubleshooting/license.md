# License Issues

This guide covers common license-related problems and their solutions.

## License Validation Errors

### Invalid License File

**Error**:
```
Error: Invalid license file
```

**Common Causes**:
1. Wrong IP address in license
2. License file not found
3. Corrupted license file
4. Expired license

**Solutions**:

1. **Verify server IP**:
```bash
# Check your server's public IP
curl ifconfig.me
# or
curl ipinfo.io/ip
# or
wget -qO- https://api.ipify.org
```

2. **Check license filename**:
```bash
# License must be named: license.json
ls -la license.json
```

3. **Verify license location**:
```bash
# Must be in same directory as arb-assist
pwd
ls -la arb-assist license.json
```

### License File Not Found

**Error**:
```
Error: Cannot find license file
```

**Troubleshooting Steps**:

1. **Check file exists**:
```bash
# Check for license file
find . -name "license.json" -type f

# Check current directory
ls -la | grep license
```

2. **Verify permissions**:
```bash
# License needs to be readable
chmod 644 license.json

# Check ownership
ls -la license.json
```

3. **Correct placement**:
```bash
# Ensure in same directory
cd /path/to/arb-assist
ls -la
# Should see both arb-assist and license.json
```

### IP Address Mismatch

**Error**:
```
Error: License not valid for this IP address
```

**Common Scenarios**:

1. **Dynamic IP changed**:
   - VPS provider assigned new IP
   - Server migration
   - Network configuration change

2. **NAT/Proxy issues**:
   - Behind NAT
   - Using VPN
   - Cloud provider networking

**Solutions**:

1. **Verify current IP**:
```bash
# External IP (what license should match)
curl ifconfig.me

# Internal IPs (for reference)
ip addr show
ifconfig
```

2. **Check for IP changes**:
```bash
# Compare with license filename
ls *.license
# If 192.168.1.100.license exists but your IP is 192.168.1.101
# You need a new license
```

3. **Request new license**:
   - Contact support with new IP
   - Provide server details
   - Explain IP change reason

## License File Management

### Backup Procedures

Always backup your license:

1. **Local backup**:
```bash
# Create backup directory
mkdir -p ~/licenses/backup
cp *.license ~/licenses/backup/

# With timestamp
cp *.license ~/licenses/backup/license-$(date +%Y%m%d).bak
```

2. **Secure storage**:
```bash
# Encrypt before storing
tar -czf - *.license | gpg -c > license-backup.tar.gz.gpg

# Decrypt when needed
gpg -d license-backup.tar.gz.gpg | tar -xzf -
```

### Multiple Licenses

Managing licenses for multiple servers:

1. **Naming convention**:
```bash
licenses/
├── production/
│   └── 192.168.1.100.license
├── staging/
│   └── 192.168.1.101.license
└── development/
    └── 192.168.1.102.license
```

2. **Deployment script**:
```bash
#!/bin/bash
# deploy-license.sh
SERVER_IP=$(curl -s ifconfig.me)
LICENSE_FILE="licenses/production/${SERVER_IP}.license"

if [ -f "$LICENSE_FILE" ]; then
    cp "$LICENSE_FILE" .
    echo "License deployed for IP: $SERVER_IP"
else
    echo "Error: No license found for IP: $SERVER_IP"
    exit 1
fi
```

### License Security

Protect your license files:

1. **File permissions**:
```bash
# Readable by user only
chmod 600 *.license

# Or readable by user and group
chmod 640 *.license
```

2. **Access control**:
```bash
# Create dedicated user
sudo useradd -r -s /bin/false arb-assist

# Set ownership
sudo chown arb-assist:arb-assist *.license
```

3. **Audit access**:
```bash
# Monitor license file access
auditctl -w /path/to/license.file -p r -k license_access

# View audit logs
ausearch -k license_access
```

## License Validation Process

### How License Validation Works

1. **arb-assist startup**:
   - Looks for license.json file
   - Extracts IP from filename
   - Compares with server IP
   - Validates license content

2. **Validation checks**:
   - File integrity
   - IP address match
   - Expiration date (if applicable)
   - License signature

### Debugging License Issues

Enable verbose logging:

1. **Check startup logs**:
```bash
# Run manually to see all output
./arb-assist

# Look for license-related messages
[INFO] Looking for license file...
[INFO] Found license: license.json
[INFO] Validating license...
[INFO] License valid
```

2. **Common error patterns**:
```
[ERROR] No license file found in current directory
[ERROR] License does not match server IP
[ERROR] License validation failed: Invalid signature
[ERROR] License expired on 2024-01-01
```

## Server Migration

When moving to a new server:

### Pre-migration Checklist

1. **Note current IP**:
```bash
echo "Current IP: $(curl -s ifconfig.me)"
```

2. **Backup current setup**:
```bash
tar -czf arb-assist-backup.tar.gz arb-assist config.toml license.json
```

3. **Request new license**:
   - Get new server IP
   - Contact support
   - Provide migration timeline

### Migration Process

1. **Setup new server**:
```bash
# Install dependencies
# Copy arb-assist binary
# Copy configuration
```

2. **Test with new license**:
```bash
# Don't delete old server yet
# Run test on new server
./arb-assist

# Verify it starts correctly
```

3. **Cutover**:
```bash
# Stop old instance
pm2 stop arb-assist

# Start new instance
pm2 start arb-assist
```

### Post-migration

1. **Verify operation**:
   - Check logs
   - Monitor performance
   - Ensure bot connects

2. **Cleanup**:
   - Remove old license
   - Update documentation
   - Notify team

## Cloud Provider Specifics

### AWS EC2

Elastic IPs and licensing:
```bash
# Check if using Elastic IP
aws ec2 describe-addresses --filters "Name=instance-id,Values=$(ec2-metadata --instance-id)"

# Note: Elastic IPs persist across restarts
```

### Google Cloud

External IPs:
```bash
# Get external IP
gcloud compute instances describe INSTANCE_NAME --format='get(networkInterfaces[0].accessConfigs[0].natIP)'
```

### DigitalOcean

Floating IPs:
```bash
# Check droplet IP
doctl compute droplet get DROPLET_ID --format PublicIPv4
```

## License Troubleshooting Checklist

When experiencing license issues:

- [ ] Verify server's external IP
- [ ] Check license file exists
- [ ] Confirm file is named license.json
- [ ] Verify file permissions (readable)
- [ ] Ensure in correct directory
- [ ] Check for error messages in logs
- [ ] Test with absolute path
- [ ] Verify no VPN/proxy active
- [ ] Confirm server time is correct
- [ ] Contact support if unresolved

## Getting Support

When contacting support:

1. **Provide information**:
   - Current server IP
   - License file exists (license.json)
   - Error messages
   - Server provider
   - Migration details (if applicable)

2. **Include diagnostics**:
```bash
# Create diagnostic report
echo "=== Diagnostic Report ===" > diagnostic.txt
echo "Date: $(date)" >> diagnostic.txt
echo "IP: $(curl -s ifconfig.me)" >> diagnostic.txt
echo "License file:" >> diagnostic.txt
ls -la license.json >> diagnostic.txt
echo "Directory contents:" >> diagnostic.txt
ls -la >> diagnostic.txt
echo "Error output:" >> diagnostic.txt
./arb-assist 2>&1 | head -20 >> diagnostic.txt
```

3. **Support channels**:
   - Discord: [Join Server](https://discord.gg/ADtnjdy5m5)
   - Include diagnostic report
   - Be patient for response

## Best Practices

1. **Always backup licenses**: Before any changes
2. **Document IP addresses**: Keep record of licensed IPs
3. **Plan migrations**: Request new licenses in advance
4. **Monitor expiration**: If licenses have expiry dates
5. **Secure storage**: Protect license files
6. **Test thoroughly**: Before production deployment