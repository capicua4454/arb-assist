# Migration Guides

Step-by-step guides for migrating between major versions of arb-assist and handling breaking changes.

## Migration Overview

Before any migration:

1. **Backup Everything**
2. **Read Release Notes**
3. **Test in Development**
4. **Plan Downtime**
5. **Have Rollback Ready**

## v1.0 to v1.1 Migration

### Breaking Changes

1. **Dynamic Fee Structure**
   - Old: Fixed fee values
   - New: Strategy-based fees

2. **Jito Configuration**
   - Old: Simple tip amount
   - New: Complex tip strategies

3. **Output Format**
   - Old: Simple array format
   - New: Structured routing format

### Migration Steps

#### Step 1: Backup Current Setup

```bash
# Create migration directory
mkdir -p ~/arb-assist-migration/v1.0-backup
cd ~/arb-assist-migration/v1.0-backup

# Backup all files
cp ~/arb-assist/* .

# Create archive
tar -czf arb-assist-v1.0-backup-$(date +%Y%m%d).tar.gz *
```

#### Step 2: Update Configuration

```bash
# Download migration script
cat > migrate-v1.0-to-v1.1.sh << 'EOF'
#!/bin/bash

# Backup original
cp config.toml config.toml.v1.0

# Update spam configuration
if grep -q "compute_unit_price = [0-9]" config.toml; then
    # Extract old value
    OLD_PRICE=$(grep "compute_unit_price = " config.toml | grep -o "[0-9]*")
    
    # Replace with new format
    sed -i "s/compute_unit_price = ${OLD_PRICE}/compute_unit_price = { strategy = \"Random\", from = ${OLD_PRICE}, to = $((OLD_PRICE * 2)) }/g" config.toml
fi

# Update Jito configuration
if grep -q "tip_lamports = [0-9]" config.toml; then
    OLD_TIP=$(grep "tip_lamports = " config.toml | grep -o "[0-9]*")
    sed -i "s/tip_lamports = ${OLD_TIP}/tip = { strategy = \"Random\", from = ${OLD_TIP}, to = $((OLD_TIP * 2)) }/g" config.toml
fi

# Add new strategy levels if missing
if ! grep -q "spam_levels" config.toml; then
    cat >> config.toml << 'CONFIG'

# Strategy levels (new in v1.1)
spam_levels = [{
  filter_level = 0,
  bundle_groups = [1],
  min_cu_price = 10000,
  max_cu_price = 50000,
  fee_strategy = "Random"
}]

jito_levels = [{
  filter_level = 0,
  bundle_groups = [1],
  min_tip = 5000,
  max_tip = 20000,
  tip_strategy = "Random"
}]
CONFIG
fi

echo "Migration complete. Please review config.toml"
EOF

chmod +x migrate-v1.0-to-v1.1.sh
./migrate-v1.0-to-v1.1.sh
```

#### Step 3: Test New Configuration

```bash
# Download new binary
wget https://github.com/capicua4454/arb-assist/raw/refs/heads/main/arb-assist -O arb-assist.v1.1
chmod +x arb-assist.v1.1

# Test with migrated config
./arb-assist.v1.1 -c config.toml

# Let run for 5 minutes
# Check for errors
# Verify output format
```

#### Step 4: Deploy

```bash
# If test successful
pm2 stop arb-assist
mv arb-assist arb-assist.v1.0
mv arb-assist.v1.1 arb-assist
pm2 start arb-assist
```

## v1.1 to v1.2 Migration

### Breaking Changes

1. **Token2022 Support**
   - New field required
   - Pool detection changes

2. **Profit Metrics**
   - New profit_per_arb metric
   - Filter threshold additions

3. **Multi-Base Tokens**
   - base_mint → base_mints array

### Migration Steps

#### Step 1: Config Additions

```bash
# Add new fields
cat >> config.toml << 'EOF'

# Token2022 support (new in v1.2)
include_token2022 = false

# Update base mint format
# Old: base_mint = "So11..."
# New: base_mints = ["So11..."]
EOF

# Fix base_mint format
sed -i 's/base_mint = "\(.*\)"/base_mints = ["\1"]/g' config.toml
```

#### Step 2: Update Filter Thresholds

```bash
# Add profit_per_arb to existing thresholds
python3 << 'EOF'
import toml
import sys

# Load config
with open('config.toml', 'r') as f:
    config = toml.load(f)

# Update filter thresholds
if 'filter_thresholds' in config:
    for threshold in config['filter_thresholds']:
        if 'min_profit_per_arb' not in threshold:
            threshold['min_profit_per_arb'] = 0

# Save updated config
with open('config.toml', 'w') as f:
    toml.dump(config, f)

print("Filter thresholds updated")
EOF
```

## Major Version Migration (v1.x to v2.x)

### Complete Overhaul

When migrating major versions with significant changes:

#### Step 1: Parallel Installation

```bash
# Create new directory for v2
mkdir ~/arb-assist-v2
cd ~/arb-assist-v2

# Download v2
wget https://github.com/capicua4454/arb-assist/raw/refs/heads/v2/arb-assist
wget https://github.com/capicua4454/arb-assist/raw/refs/heads/v2/config.toml.example

# Copy license
cp ~/arb-assist/*.license .
```

#### Step 2: Manual Configuration

```bash
# Start fresh with new example
cp config.toml.example config.toml

# Manually transfer settings
# Use guide for each section:

# 1. Connection settings
# - Copy RPC URLs
# - Copy GRPC settings
# - Update new format

# 2. Trading parameters
# - Transfer thresholds
# - Adapt to new structure
# - Set new defaults

# 3. Bot-specific settings
# - Migrate output names
# - Update bot configs
```

#### Step 3: Gradual Transition

```bash
# Run both versions temporarily
# v1 on main port
# v2 on different port

# In v2 config.toml:
port = 8081  # Different from v1

# Start v2
pm2 start ./arb-assist --name arb-assist-v2

# Monitor both
pm2 logs

# Gradually move bots to v2
# Once stable, deprecate v1
```

## Configuration Format Migrations

### TOML to JSON Migration

If switching output formats:

```python
#!/usr/bin/env python3
# migrate-toml-to-json.py

import toml
import json
import sys

def convert_config(toml_file, json_file):
    """Convert TOML config to JSON format"""
    
    # Load TOML
    with open(toml_file, 'r') as f:
        config = toml.load(f)
    
    # Transform structure
    json_config = {
        "connection": {
            "rpc_url": config.get("rpc_url"),
            "grpc_url": config.get("grpc_url"),
            "grpc_engine": config.get("grpc_engine")
        },
        "trading": {
            "mints_to_arb": config.get("mints_to_arb"),
            "mints_to_rank": config.get("mints_to_rank"),
            "filter_thresholds": config.get("filter_thresholds")
        },
        "execution": {
            "spam_levels": config.get("spam_levels"),
            "jito_levels": config.get("jito_levels")
        }
    }
    
    # Save JSON
    with open(json_file, 'w') as f:
        json.dump(json_config, f, indent=2)
    
    print(f"Converted {toml_file} to {json_file}")

if __name__ == "__main__":
    convert_config("config.toml", "config.json")
```

### Strategy Migration

When strategy formats change:

```bash
# Old format: Single strategy
cat old-strategy.toml
[strategy]
type = "aggressive"
cu_price = 50000

# New format: Multi-level strategies
cat > new-strategy.toml << 'EOF'
spam_levels = [
  {
    filter_level = 0,
    bundle_groups = [1],
    min_cu_price = 40000,
    max_cu_price = 60000,
    fee_strategy = "Random"
  }
]
EOF
```

## Database Migration

If arb-assist adds persistent storage:

### Export Historical Data

```bash
# Export current in-memory data
./arb-assist --export-data data-export.json

# Convert to new format
python3 << 'EOF'
import json

# Load old data
with open('data-export.json', 'r') as f:
    old_data = json.load(f)

# Transform to new schema
new_data = {
    "version": "2.0",
    "mints": {},
    "pools": {},
    "metrics": {}
}

# Migrate each record
for mint in old_data.get("mints", []):
    new_data["mints"][mint["address"]] = {
        "profit": mint.get("total_profit", 0),
        "trades": mint.get("successful_arbs", 0),
        "last_seen": mint.get("timestamp")
    }

# Save new format
with open('data-import.json', 'w') as f:
    json.dump(new_data, f, indent=2)
EOF
```

## Bot Migration

### SMB Config Migration

```bash
# Old SMB format
cat old-smb-config.toml
[[mints]]
address = "..."
pools = []

# New SMB format
cat > new-smb-config.toml << 'EOF'
[[routing.mint_config_list]]
mint = "..."
pump_pool_list = []
raydium_clmm_pool_list = []
EOF

# Migration script
python3 migrate-smb-config.py old-smb-config.toml new-smb-config.toml
```

### NotArb Config Migration

```bash
# Migrate markets.json format
python3 << 'EOF'
import json

# Old format: flat array
old_markets = ["market1", "market2", "market3"]

# New format: grouped structure
new_markets = {
    "groups": [
        ["market1", "market2"],
        ["market3"]
    ],
    "update_timestamp": 1234567890
}

with open('markets.json', 'w') as f:
    json.dump(new_markets, f, indent=2)
EOF
```

## Rollback Procedures

### Quick Rollback

```bash
# Stop new version
pm2 stop arb-assist

# Restore backup
cd ~/arb-assist
rm -rf *
tar -xzf ~/arb-assist-migration/v1.0-backup/arb-assist-v1.0-backup-*.tar.gz

# Restart old version
pm2 start arb-assist
```

### Partial Rollback

Keep some new features while reverting others:

```bash
# Use new binary with compatibility mode
./arb-assist --compatibility v1.0

# Or use hybrid config
cat > hybrid-config.toml << 'EOF'
# New connection settings
rpc_url = "https://new-rpc.com"

# Old strategy format
[strategy]
type = "conservative"
legacy_mode = true
EOF
```

## Testing Migrations

### Migration Test Suite

```bash
#!/bin/bash
# test-migration.sh

# Test configuration parsing
echo "Testing config parsing..."
./arb-assist --validate-config config.toml || exit 1

# Test output generation
echo "Testing output generation..."
timeout 60 ./arb-assist -c config.toml
ls -la *-config.* || exit 1

# Test bot compatibility
echo "Testing bot compatibility..."
if [ -f smb-onchain ]; then
    ./smb-onchain --dry-run smb-config.toml || exit 1
fi

echo "All tests passed!"
```

### Staged Migration

1. **Development Environment**
```bash
# Test full migration process
# Document any issues
# Refine migration scripts
```

2. **Staging Environment**
```bash
# Run for 24 hours
# Monitor all metrics
# Compare with production
```

3. **Production Migration**
```bash
# Schedule during low activity
# Have support ready
# Monitor closely
```

## Best Practices

1. **Always Backup**
   - Configuration files
   - Binary versions
   - License files
   - Historical data

2. **Test Thoroughly**
   - Parse configurations
   - Generate outputs
   - Verify bot acceptance
   - Check performance

3. **Document Changes**
   - What changed
   - Why it changed
   - How to adapt
   - Rollback steps

4. **Communicate**
   - Announce migrations
   - Provide timeline
   - Share results
   - Gather feedback

5. **Monitor Post-Migration**
   - Performance metrics
   - Error rates
   - Success rates
   - Resource usage

Remember: Successful migrations require planning, testing, and patience. When in doubt, run old and new versions in parallel until confident in the migration.