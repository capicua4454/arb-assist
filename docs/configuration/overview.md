# Configuration Overview

arb-assist uses a TOML configuration file (`config.toml`) to control all aspects of its operation. This section provides a comprehensive guide to all configuration options.

## Configuration File Structure

The configuration file is organized into several main sections:

```toml
# 1. Connection Settings
rpc_url = "..."
grpc_url = "..."

# 2. Core Settings  
mode = "smb"
update_interval = 10000

# 3. DEX & Program Configuration
dexes = [...]
arb_programs = [...]

# 4. Filtering & Ranking
filter_thresholds = [...]
intermint_sort_strategy = {...}

# 5. Trading Strategies
spam_levels = [...]
jito_levels = [...]

# 6. Bot-Specific Settings
[smb]
...

[notarb]
...
```

## Configuration Hierarchy

arb-assist processes configuration in this order:

1. **Connection Settings** - Establishes blockchain connections
2. **Core Operation** - Defines basic behavior
3. **Market Analysis** - Specifies what to monitor
4. **Filtering Logic** - Determines profitable opportunities
5. **Execution Strategies** - Controls how to trade
6. **Bot Integration** - Outputs appropriate configs

## Key Concepts

### Filter Thresholds

Filter thresholds define multiple tiers of market conditions:

```toml
filter_thresholds = [
  { min_profit = 1_000_000, ... },   # Tier 0: Low activity
  { min_profit = 10_000_000, ... },  # Tier 1: Medium activity
  { min_profit = 100_000_000, ... }, # Tier 2: High activity
]
```

Each tier can have different:
- Minimum profit requirements
- ROI thresholds
- Volume requirements
- Success rate criteria

### Strategy Levels

Strategy levels map to filter thresholds:

```toml
spam_levels = [
  { filter_level = 0, ... },  # Uses tier 0 settings
  { filter_level = 1, ... },  # Uses tier 1 settings
]
```

This allows different trading strategies for different market conditions.

### Mint Groups

Mints are organized into transaction groups:

```toml
mints_to_arb = [2, 2, 3]  # Group 1: 2 mints, Group 2: 2 mints, Group 3: 3 mints
```

Different strategies can target different groups.

## Configuration Files

### Input File: config.toml

Your main configuration file that controls arb-assist behavior.

**Location**: Same directory as arb-assist binary

**Format**: TOML (Tom's Obvious, Minimal Language)

### Output Files

Based on your `mode` setting:

#### SMB Mode (`mode = "smb"`)
- **File**: `{output}.toml` 
- **Format**: TOML
- **Updates**: Every `update_interval` milliseconds

#### NotArb Mode (`mode = "na"`)
- **Files**: 
  - `{output}.json` - Main configuration
  - `markets.json` - Market addresses
  - `lookup-tables.json` - ALUTs
  - `notarb-attributes.json` - Dynamic settings
- **Format**: JSON
- **Updates**: Every `update_interval` milliseconds

#### Both Mode (`mode = "both"`)
Generates both SMB and NotArb outputs simultaneously.

## Environment Variables

Some settings can use environment variables:

```toml
keypair_path = "${DEFAULT_KEYPAIR_PATH}"  # Expands to env var
```

Supported variables:
- `DEFAULT_KEYPAIR_PATH` - Default wallet location
- Custom variables you define

## Configuration Validation

arb-assist validates configuration on startup:

1. **Required Fields** - Ensures essential settings exist
2. **Value Ranges** - Checks numeric bounds
3. **Logical Consistency** - Verifies related settings
4. **Connection Tests** - Validates RPC/GRPC access

### Common Validation Errors

```
Error: Invalid RPC URL
```
Check your `rpc_url` format and accessibility.

```
Error: mints_to_rank must be >= max(mints_to_arb)
```
Increase `mints_to_rank` to match your largest group.

```
Error: filter_thresholds must be in ascending order
```
Order thresholds from lowest to highest requirements.

## Best Practices

### 1. Start Conservative

Begin with:
- High filter thresholds
- Low transaction counts
- Moderate fees
- Single mint groups

### 2. Use Multiple Tiers

Define 3-5 filter threshold tiers:
- **Tier 0**: Minimal activity baseline
- **Tier 1-2**: Normal market conditions  
- **Tier 3-4**: High activity periods

### 3. Monitor and Adjust

- Review logs regularly
- Track success rates
- Adjust thresholds based on performance
- Document changes

### 4. Version Control

- Keep config.toml in git
- Use meaningful commit messages
- Tag working configurations
- Maintain changelog

### 5. Security

- Never commit private keys
- Use encrypted keypair files
- Restrict file permissions
- Rotate API keys regularly

## Configuration Templates

arb-assist provides example configurations:

- `config.toml.example` - Basic template
- `config.toml (smb)` - SMB-optimized settings
- `config.toml (notarb)` - NotArb-optimized settings

## Dynamic Updates

Configuration changes are applied:
- **On startup** - Full configuration load
- **During runtime** - Output files updated
- **On bot restart** - Bots reload when configs change

## Performance Impact

Configuration affects performance:

### High Impact Settings
- `update_interval` - Processing frequency
- `mints_to_rank` - Memory usage
- `grpc_engine` - Network efficiency

### Medium Impact Settings  
- `filter_thresholds` - Computation complexity
- `sort_strategy` - Ranking time
- `halflife` - Decay calculations

### Low Impact Settings
- `output` - File I/O
- `memo` - Metadata only
- `log_output` - Debug logging

## Next Steps

Dive deeper into specific configuration sections:

- [Connection Settings](connection-settings.md) - RPC, GRPC setup
- [DEX Configuration](dex-configuration.md) - Market selection
- [Mint Selection](mint-selection.md) - Ranking strategies
- [Trading Strategies](trading-strategies.md) - Execution options
- [Advanced Settings](advanced-settings.md) - Fine-tuning