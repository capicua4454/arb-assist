# Version Compatibility

{% hint style="danger" %}
⚠️ **TEMPLATE ONLY**: This is a template file for documenting version compatibility. All version numbers and compatibility information shown are placeholders and must be replaced with actual data once available.
{% endhint %}

This guide will detail compatibility between arb-assist versions and supported bot versions, helping you maintain a stable arbitrage system.

## Compatibility Matrix

<!-- ⚠️ REVIEW: All version numbers and compatibility information below are FICTIONAL EXAMPLES -->

### arb-assist Versions

| arb-assist Version | SMB-Onchain | NotArb | Solana | Notes |
|-------------------|-------------|---------|---------|--------|
| Current | ?.?.?+ | Latest GitHub | 1.17+ | ⚠️ REVIEW: Add actual versions |
| Previous | ?.?.? | ?.? | 1.16+ | ⚠️ REVIEW: Add real compatibility |

### Feature Compatibility

<!-- ⚠️ REVIEW: Verify which features actually exist and their version requirements -->

| Feature | Introduced | SMB Required | NotArb Required |
|---------|------------|--------------|-----------------|
| Dynamic Jito Tips | ??? | ?.?.?+ | ???+ |
| File Server Mode | ??? | Any | Any |
| Flash Loans | ??? | ?.?.?+ | ???+ |
| Token2022 Support | ??? | ?.?.?+ | ???+ |
| Multi-strategy Levels | ??? | ?.?.?+ | ???+ |
| Helius Integration | ??? | Any | Any |

## Bot Version Requirements

### SMB-Onchain Compatibility

<!-- ⚠️ REVIEW: Replace with actual SMB version requirements and config formats -->

#### Current Version
- Full feature support
- All strategy levels
- Token2022 compatible (if applicable)
- Optimized performance

Required config fields:
```toml
# ⚠️ REVIEW: Verify these are actual required fields
[bot]
compute_unit_limit = 600000

[spam]
compute_unit_price = { strategy = "Random", from = 10000, to = 50000 }

[jito]
tip = { strategy = "Random", from = 5000, to = 20000 }
```

### NotArb Compatibility

<!-- ⚠️ REVIEW: Replace with actual NotArb version requirements -->

#### Latest GitHub Release
- Full feature support
- All output files
- Dynamic attributes

Required files:
- `notarb-config.json`
- `markets.json`
- `lookup-tables.json`
- `notarb-attributes.json` (⚠️ REVIEW: Verify this file exists)

## Solana Version Compatibility

### Mainnet Requirements

<!-- ⚠️ REVIEW: Verify actual Solana version requirements -->

| Solana Version | arb-assist Support | Notes |
|----------------|-------------------|--------|
| 1.18.x | ??? | Current mainnet |
| 1.17.x | ??? | Check compatibility |
| 1.16.x | ??? | May have limitations |

## Configuration Migration

<!-- ⚠️ REVIEW: Add actual migration guides based on real version changes -->

### Migration Notes

When upgrading versions:
1. Always backup your configuration
2. Check for breaking changes in release notes
3. Test in development environment first
4. Review new configuration options

## API Changes

<!-- ⚠️ REVIEW: Document actual API/output format changes between versions -->

### Output File Format Changes

Document any changes to:
- SMB TOML format
- NotArb JSON format
- File naming conventions
- New output files

## Deprecation Notices

<!-- ⚠️ REVIEW: Add actual deprecated features and removal timelines -->

### Deprecated Features
- List actual deprecated features
- Provide migration paths
- Set removal timelines

## Best Practices

1. **Test Before Production**
   - Run parallel test instance
   - Verify output formats
   - Check bot acceptance

2. **Keep Documentation**
   - Record working version combinations
   - Document configuration changes
   - Track upgrade experiences

3. **Monitor Releases**
   - Follow GitHub repositories
   - Join Discord for announcements
   - Test new features carefully

## Support Matrix

<!-- ⚠️ REVIEW: Add actual support timelines -->

### Version Support Policy
- Define support periods for each version
- Clarify community vs official support
- Provide upgrade recommendations

## Getting Help

For compatibility issues:
1. Check this guide first
2. Search Discord history
3. Post detailed version info
4. Include error messages
5. Share config (remove secrets)

Remember: When in doubt, matching versions that others report working together is the safest approach.