# Version Compatibility

This guide details compatibility between arb-assist versions and supported bot versions, helping you maintain a stable arbitrage system.

## Compatibility Matrix

### arb-assist Versions

| arb-assist Version | SMB-Onchain | NotArb | Solana | Notes |
|-------------------|-------------|---------|---------|--------|
| Latest | 0.9.8+ | Latest GitHub | 1.17+ | Current recommended |
| 1.2.x | 0.9.5-0.9.8 | v2.0+ | 1.17+ | Stable |
| 1.1.x | 0.9.0-0.9.5 | v1.5+ | 1.16+ | Legacy support |
| 1.0.x | 0.8.x | v1.0+ | 1.14+ | Deprecated |

### Feature Compatibility

| Feature | Introduced | SMB Required | NotArb Required |
|---------|------------|--------------|-----------------|
| Dynamic Jito Tips | v1.1.0 | 0.9.5+ | v1.5+ |
| File Server Mode | v1.0.5 | Any | Any |
| Flash Loans | v1.0.0 | 0.9.0+ | v1.0+ |
| Token2022 Support | v1.2.0 | 0.9.8+ | v2.0+ |
| Multi-strategy Levels | v1.1.5 | 0.9.7+ | v1.8+ |
| Helius Integration | v1.0.8 | Any | Any |

## Bot Version Requirements

### SMB-Onchain Compatibility

#### Version 0.9.8+ (Current)
- Full feature support
- All strategy levels
- Token2022 compatible
- Optimized performance

Required config fields:
```toml
[bot]
compute_unit_limit = 600000

[spam]
compute_unit_price = { strategy = "Random", from = 10000, to = 50000 }

[jito]
tip = { strategy = "Random", from = 5000, to = 20000 }
```

#### Version 0.9.5-0.9.7
- Most features supported
- No Token2022
- Limited strategy options

Differences:
```toml
# Older format
[spam]
compute_unit_price = 10000  # Fixed value only

[jito]
tip_lamports = 5000  # Fixed value only
```

#### Version 0.9.0-0.9.4
- Basic support only
- Limited Jito integration
- No dynamic fees

### NotArb Compatibility

#### Latest GitHub Release
- Full feature support
- All output files
- Dynamic attributes

Required files:
- `notarb-config.json`
- `markets.json`
- `lookup-tables.json`
- `notarb-attributes.json`

#### Version 2.0+
- Multi-region Jito
- Flash loan support
- Thread optimization

New config fields:
```json
{
  "jito_targets": [...],
  "max_bundle_transactions": 5,
  "threads": 0
}
```

#### Version 1.5-1.9
- Basic JSON support
- Limited Jito regions
- Fixed thread pools

Missing features:
- No `notarb-attributes.json`
- No dynamic thread management
- Limited to 3 Jito regions

## Solana Version Compatibility

### Mainnet Requirements

| Solana Version | arb-assist Support | Notes |
|----------------|-------------------|--------|
| 1.18.x | Full | Current mainnet |
| 1.17.x | Full | Recommended minimum |
| 1.16.x | Partial | No compute budget v2 |
| 1.14.x | Limited | Missing features |

### Breaking Changes

#### Solana 1.17
- New compute budget instructions
- Address lookup tables v2
- Priority fee changes

Update config for 1.17+:
```toml
# New compute budget format
max_cu_limit = 1_400_000  # Increased limit

# ALUTs v2
aluts_per_mint = 30  # More ALUTs supported
```

## Configuration Migration

### Migrating from v1.0 to v1.1

Key changes:
```toml
# Old (v1.0)
[jito]
enabled = true
tip_lamports = 10000

# New (v1.1)
[jito]
enabled = true
tip = { strategy = "Random", from = 5000, to = 20000 }
```

Migration script:
```bash
#!/bin/bash
# Backup old config
cp config.toml config.toml.v1.0

# Update format
sed -i 's/tip_lamports = \([0-9]*\)/tip = { strategy = "Random", from = \1, to = \1 }/g' config.toml
```

### Migrating from v1.1 to v1.2

New fields added:
```toml
# Add Token2022 support
include_token2022 = false  # Set true if needed

# Add profit per arb metric
min_profit_per_arb = 0  # In filter thresholds
```

## API Changes

### Output File Format Changes

#### v1.0 Output
```toml
# SMB format
[[mints]]
address = "..."
pools = [...]
```

#### v1.1+ Output
```toml
# SMB format
[[routing.mint_config_list]]
mint = "..."
pump_pool_list = [...]
raydium_clmm_pool_list = [...]
```

### Metric Name Changes

| Old Name (v1.0) | New Name (v1.1+) | Description |
|-----------------|------------------|-------------|
| `volume` | `total_volume` | Combined volume |
| `arbs` | `successful_arbs` | Success count |
| `roi_ratio` | `roi` | Simplified name |

## Deprecation Notices

### Deprecated in v1.2
- `simple_mode` configuration
- Fixed fee values
- Single RPC endpoint

Replace with:
```toml
# Multiple RPCs
sending_rpc_urls = ["rpc1", "rpc2", "rpc3"]

# Dynamic fees
fee_strategy = "Random"
```

### Removed in v1.1
- `legacy_output` option
- `v1_compatibility` flag
- Old GRPC format

## Upgrade Paths

### From v1.0.x to Latest

1. **Backup Everything**
```bash
cp -r ~/arb-assist ~/arb-assist-backup
```

2. **Update Config**
```bash
# Get new example
wget https://github.com/capicua4454/arb-assist/raw/refs/heads/main/config.toml.example

# Compare differences
diff config.toml config.toml.example
```

3. **Gradual Migration**
```toml
# Start with compatibility mode
legacy_support = true  # If available

# Test thoroughly
# Then remove legacy support
```

### From v1.1.x to Latest

Simpler upgrade:
```bash
# Usually just binary replacement
wget -O arb-assist https://github.com/capicua4454/arb-assist/raw/refs/heads/main/arb-assist

# Minor config additions
echo "include_token2022 = false" >> config.toml
```

## Compatibility Testing

### Test Suite

Create test configuration:
```toml
# test-config.toml
mode = "both"
log_output = true
update_interval = 60000  # Slower for testing

# Minimal filters
filter_thresholds = [{
  min_profit = 1,
  max_cu_limit = 400000,
  top_pool_num = 2
}]
```

Run compatibility test:
```bash
# Test arb-assist
./arb-assist -c test-config.toml

# Check outputs
ls -la *-config.*

# Validate formats
python3 -c "import toml; toml.load('smb-config.toml')"
python3 -c "import json; json.load(open('notarb-config.json'))"
```

### Version Detection

Check versions programmatically:
```bash
#!/bin/bash
# check-versions.sh

# Check arb-assist
echo "arb-assist MD5: $(md5sum arb-assist | cut -d' ' -f1)"

# Check SMB version
if [ -f smb-onchain ]; then
    ./smb-onchain --version 2>/dev/null || echo "Version flag not supported"
fi

# Check NotArb version
if [ -f notarb.jar ]; then
    java -jar notarb.jar --version 2>/dev/null || echo "Version info not available"
fi

# Check Solana version
solana --version
```

## Troubleshooting Compatibility

### Common Issues

#### "Unknown configuration field"
- Using new config with old binary
- Solution: Update binary or remove new fields

#### "Invalid output format"
- Bot expecting different format
- Solution: Check bot version requirements

#### "Missing required field"
- Old config with new binary
- Solution: Add missing fields from example

### Compatibility Mode

Some versions support compatibility mode:
```toml
# Force v1.0 output format
compatibility_mode = "v1.0"

# Use legacy field names
use_legacy_names = true
```

## Best Practices

1. **Test Before Production**
   - Run parallel test instance
   - Verify output formats
   - Check bot acceptance

2. **Incremental Updates**
   - Update arb-assist first
   - Test thoroughly
   - Then update bots

3. **Keep Backups**
   - Previous binary versions
   - Working configurations
   - Known-good combinations

4. **Monitor Changes**
   - Read release notes
   - Check Discord announcements
   - Test new features

5. **Document Your Stack**
```bash
# Create version file
cat > versions.txt << EOF
Date: $(date)
arb-assist: v1.2.0
SMB-Onchain: v0.9.8
NotArb: v2.0
Solana: v1.18.1
Config Version: 2024-01-26
EOF
```

## Support Matrix

### Long-term Support

- Latest version: Full support
- Previous minor: 6 months
- Previous major: 3 months
- Older versions: Community only

### Getting Help

For compatibility issues:
1. Check this guide first
2. Search Discord history
3. Post detailed version info
4. Include error messages
5. Share config (remove secrets)

Remember: When in doubt, matching versions that others report working together is the safest approach.