# Updating arb-assist

This guide covers how to safely update arb-assist to the latest version while maintaining your configuration and minimizing downtime.

## Update Methods

### Method 1: Direct Download (Recommended)

The simplest way to update is using wget with the overwrite flag:

```bash
# Stop arb-assist first
pm2 stop arb-assist
# or
killall arb-assist

# Download new version (overwrites existing)
wget -O arb-assist https://github.com/capicua4454/arb-assist/raw/refs/heads/main/arb-assist

# Make executable
chmod +x arb-assist

# Start again
pm2 start arb-assist
```

### Method 2: Backup and Replace

For extra safety, backup the current version first:

```bash
# Create backup with timestamp
cp arb-assist arb-assist.backup.$(date +%Y%m%d-%H%M%S)

# Download new version
wget https://github.com/capicua4454/arb-assist/raw/refs/heads/main/arb-assist -O arb-assist.new

# Make executable
chmod +x arb-assist.new

# Stop current version
pm2 stop arb-assist

# Replace with new version
mv arb-assist.new arb-assist

# Start new version
pm2 start arb-assist
```

### Method 3: Blue-Green Deployment

For zero downtime updates:

```bash
# Download new version with different name
wget https://github.com/capicua4454/arb-assist/raw/refs/heads/main/arb-assist -O arb-assist-new
chmod +x arb-assist-new

# Start new version on different port
cat > config-new.toml << EOF
$(cat config.toml)
port = 8081  # Different port
EOF

# Run new version
pm2 start ./arb-assist-new --name arb-assist-new -- -c config-new.toml

# Test new version
curl http://localhost:8081/smb-config.toml

# If working, switch over
pm2 stop arb-assist
pm2 delete arb-assist
mv arb-assist arb-assist-old
mv arb-assist-new arb-assist
pm2 restart arb-assist-new --name arb-assist

# Cleanup
rm arb-assist-old config-new.toml
```

## Pre-Update Checklist

Before updating, always:

- [ ] Check current version is working properly
- [ ] Note current configuration settings
- [ ] Backup configuration files
- [ ] Backup license file
- [ ] Check disk space available
- [ ] Plan update window
- [ ] Notify team if applicable

### Backup Commands

```bash
# Create update directory
mkdir -p ~/arb-assist-backups/$(date +%Y%m%d)
cd ~/arb-assist-backups/$(date +%Y%m%d)

# Backup all important files
cp ~/arb-assist/arb-assist .
cp ~/arb-assist/config.toml .
cp ~/arb-assist/*.license .
cp ~/arb-assist/*-config.toml . 2>/dev/null || true
cp ~/arb-assist/*-config.json . 2>/dev/null || true

# Create archive
tar -czf arb-assist-backup-$(date +%Y%m%d-%H%M%S).tar.gz *
```

## Version Checking

### Current Version

Check your current version:

```bash
# Run with version flag (if supported)
./arb-assist --version

# Or check file info
ls -la arb-assist
md5sum arb-assist
```

### Latest Version

Check for updates:

1. Visit [GitHub Releases](https://github.com/capicua4454/arb-assist/releases)
2. Check Discord announcements
3. Compare file sizes/checksums

## Update Process

### Step 1: Prepare

```bash
# Check current status
pm2 status arb-assist

# Monitor current performance
pm2 logs arb-assist --lines 50

# Note any errors or issues
```

### Step 2: Download

```bash
# Download to temporary location first
wget https://github.com/capicua4454/arb-assist/raw/refs/heads/main/arb-assist -O arb-assist.tmp

# Verify download
ls -la arb-assist.tmp

# Make executable
chmod +x arb-assist.tmp
```

### Step 3: Test

```bash
# Test new version with your config
./arb-assist.tmp -c config.toml

# Watch for:
# - License validation
# - GRPC connection
# - Config generation
# - Any errors

# Let it run for 1-2 minutes
# Check generated configs are valid
```

### Step 4: Deploy

```bash
# If test successful, deploy
pm2 stop arb-assist
mv arb-assist arb-assist.old
mv arb-assist.tmp arb-assist
pm2 start arb-assist

# Monitor startup
pm2 logs arb-assist
```

### Step 5: Verify

```bash
# Check new version is running
pm2 status arb-assist

# Verify config generation
ls -la *-config.*

# Check bot is receiving updates
tail -f smb-config.toml  # Should update every interval
```

## Rollback Procedure

If issues occur after update:

### Immediate Rollback

```bash
# Stop problematic version
pm2 stop arb-assist

# Restore backup
mv arb-assist arb-assist.bad
mv arb-assist.old arb-assist

# Restart old version
pm2 start arb-assist

# Verify working
pm2 logs arb-assist
```

### From Backup Archive

```bash
# Find backup
ls ~/arb-assist-backups/

# Extract backup
cd ~/arb-assist
tar -xzf ~/arb-assist-backups/20240126/arb-assist-backup-*.tar.gz

# Restart
pm2 restart arb-assist
```

## Configuration Migration

### Breaking Changes

Sometimes updates require config changes:

```bash
# Check for config changes
diff config.toml.example config.toml

# Common migrations:
# - New required fields
# - Renamed parameters
# - Deprecated options
```

### Migration Script

Create a migration helper:

```bash
#!/bin/bash
# migrate-config.sh

# Backup current config
cp config.toml config.toml.pre-update

# Add new required fields
if ! grep -q "new_field" config.toml; then
    echo "new_field = \"default_value\"" >> config.toml
fi

# Update renamed fields
sed -i 's/old_field_name/new_field_name/g' config.toml

echo "Config migrated. Please review changes."
```

## Automated Updates

### Update Script

Create an automated update script:

```bash
#!/bin/bash
# update-arb-assist.sh

set -e  # Exit on error

# Configuration
BACKUP_DIR="$HOME/arb-assist-backups"
INSTALL_DIR="$HOME/arb-assist"
DOWNLOAD_URL="https://github.com/capicua4454/arb-assist/raw/refs/heads/main/arb-assist"

# Create backup
echo "Creating backup..."
mkdir -p "$BACKUP_DIR/$(date +%Y%m%d)"
cp "$INSTALL_DIR/arb-assist" "$BACKUP_DIR/$(date +%Y%m%d)/"
cp "$INSTALL_DIR/config.toml" "$BACKUP_DIR/$(date +%Y%m%d)/"

# Download new version
echo "Downloading update..."
wget -q "$DOWNLOAD_URL" -O "$INSTALL_DIR/arb-assist.new"
chmod +x "$INSTALL_DIR/arb-assist.new"

# Test new version
echo "Testing new version..."
cd "$INSTALL_DIR"
timeout 30 ./arb-assist.new -c config.toml || {
    echo "Test failed! Aborting update."
    rm arb-assist.new
    exit 1
}

# Deploy
echo "Deploying update..."
pm2 stop arb-assist
mv arb-assist arb-assist.old
mv arb-assist.new arb-assist
pm2 start arb-assist

# Verify
sleep 5
if pm2 status | grep -q "arb-assist.*online"; then
    echo "Update successful!"
    rm arb-assist.old
else
    echo "Update failed! Rolling back..."
    pm2 stop arb-assist
    mv arb-assist.old arb-assist
    pm2 start arb-assist
    exit 1
fi
```

### Scheduled Updates

Add to crontab for weekly updates:

```bash
# Every Sunday at 3 AM
0 3 * * 0 /home/user/update-arb-assist.sh >> /var/log/arb-assist-update.log 2>&1
```

## Update Notifications

### Discord Webhook

Get notified of updates:

```bash
#!/bin/bash
# check-updates.sh

CURRENT_MD5=$(md5sum arb-assist | cut -d' ' -f1)
LATEST_MD5=$(wget -qO- https://github.com/capicua4454/arb-assist/raw/refs/heads/main/arb-assist | md5sum | cut -d' ' -f1)

if [ "$CURRENT_MD5" != "$LATEST_MD5" ]; then
    curl -X POST "$DISCORD_WEBHOOK" \
        -H "Content-Type: application/json" \
        -d "{\"content\": \"New arb-assist update available!\"}"
fi
```

## Best Practices

### 1. Update Timing

- Update during low activity periods
- Avoid updating during high volatility
- Plan for 15-30 minute window
- Have rollback plan ready

### 2. Testing Protocol

- Test in development first
- Run parallel for 1 hour
- Compare performance metrics
- Verify all features work

### 3. Communication

- Notify team before updating
- Document changes made
- Share update results
- Report any issues

### 4. Monitoring

After update, monitor:
- CPU usage changes
- Memory consumption
- Success rates
- Error logs
- Performance metrics

## Troubleshooting Updates

### Update Fails to Start

```bash
# Check permissions
ls -la arb-assist
chmod +x arb-assist

# Check license is present
ls -la *.license

# Try manual start
./arb-assist -c config.toml
```

### Configuration Errors

```bash
# Validate TOML syntax
python3 -c "import toml; toml.load('config.toml')"

# Compare with example
diff config.toml config.toml.example
```

### Performance Degradation

If new version performs worse:
1. Check config compatibility
2. Review changelog for changes
3. Monitor resource usage
4. Consider rollback
5. Report to developers

## Update Log

Keep a log of updates:

```bash
# Create update log
cat >> ~/arb-assist-updates.log << EOF
Date: $(date)
Version: From $OLD_VERSION to $NEW_VERSION
Changes: [List any config changes]
Issues: [Any problems encountered]
Result: [Success/Rollback]
---
EOF
```

Remember: Always test updates thoroughly before deploying to production. When in doubt, wait for community feedback on new versions.