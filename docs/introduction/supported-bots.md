# Supported Bots

arb-assist currently supports two major Solana arbitrage bot implementations, each with its own configuration format and features.

## SolanaMevBot On-Chain (SMB)

### Overview
SolanaMevBot On-Chain is a high-performance arbitrage bot that executes trades directly on the Solana blockchain. arb-assist generates TOML configuration files optimized for SMB's requirements.

### Key Features
- **Flash Loan Support**: Borrow capital for arbitrage without upfront liquidity
- **Multi-mint Merging**: Combine multiple arbitrage opportunities in single transactions
- **Jito Bundle Integration**: Submit transactions through Jito for MEV protection
- **Spam Mode**: Rapid transaction submission for competitive markets

### Configuration Output
When `mode = "smb"` or `mode = "both"`, arb-assist generates:
- `smb-config.toml`: Complete bot configuration
- Dynamic updates every 10 seconds (configurable)
- Automatic bot activation/deactivation

### Example Configuration Structure
```toml
[bot]
merge_mints = true
compute_unit_limit = 600_000

[[routing.mint_config_list]]
mint = "TokenMintAddress..."
pump_pool_list = ["PoolAddress1..."]
meteora_dlmm_pool_list = ["PoolAddress2..."]
lookup_table_accounts = ["ALUTAddress..."]
process_delay = 400

[spam]
enabled = true
compute_unit_price = { strategy = "Random", from = 10000, to = 50000 }

[jito]
enabled = true
tip = { strategy = "Random", from = 3000, to = 12000 }
```

### Integration Guide
For detailed SMB setup instructions, see [SMB-Onchain Setup](../bot-integration/smb-onchain.md).

## NotArb onchain-bot

### Overview
NotArb is a Java-based arbitrage bot with advanced features for high-frequency trading. arb-assist generates JSON configuration files and supporting data files for NotArb.

### Key Features
- **JVM Optimization**: Leverages Java's performance capabilities
- **Protected Keypairs**: Enhanced security for private keys
- **Multi-region Jito**: Submit to multiple Jito endpoints
- **Dynamic Threading**: Adaptive thread pool management

### Configuration Output
When `mode = "na"` or `mode = "both"`, arb-assist generates:
- `notarb-config.json`: Main configuration file
- `markets.json`: 2D array of market addresses
- `lookup-tables.json`: Array of lookup table addresses
- `notarb-attributes.json`: Dynamic fees and settings

### Example Output Files

**notarb-config.json:**
```json
{
  "jvm_args": ["-server", "-Xmx8192m"],
  "keypair_path": "${DEFAULT_KEYPAIR_PATH}",
  "flash_loan": true,
  "max_lookup_tables": 10
}
```

**markets.json:**
```json
{
  "groups": [
    [
      "Market1Address...",
      "Market2Address..."
    ]
  ],
  "update_timestamp": 1753221082643
}
```

**notarb-attributes.json:**
```json
{
  "cu_limit": 320002,
  "jito_enabled": true,
  "jito_min_tip": 28305,
  "jito_max_tip": 60000,
  "spam_enabled": true,
  "spam_min_fee": 11385,
  "spam_max_fee": 32000
}
```

### Integration Guide
For detailed NotArb setup instructions, see [NotArb Setup](../bot-integration/notarb.md).

## Comparison Table

| Feature | SMB-Onchain | NotArb |
|---------|-------------|---------|
| **Language** | Rust | Java |
| **Config Format** | TOML | JSON |
| **Flash Loans** | ✅ Cetiloan | ✅ Built-in |
| **Jito Bundles** | ✅ | ✅ |
| **Multi-mint** | ✅ Merge mode | ✅ Groups |
| **Performance** | Very High | High |
| **Memory Usage** | Low | Medium-High |
| **Setup Complexity** | Medium | Medium |
| **Remote Config** | Via file server | Via file server |

## Choosing the Right Bot

### Use SMB-Onchain if you:
- Want the fastest possible execution
- Have limited server resources
- Prefer Rust-based tools
- Need mint merging capabilities

### Use NotArb if you:
- Want JVM-based performance tuning
- Need multi-region Jito submission
- Prefer Java ecosystem tools
- Require advanced threading control

## Running Both Bots

arb-assist supports generating configurations for both bots simultaneously:

```toml
mode = "both"  # Generates configs for both SMB and NotArb
```

This allows you to:
- Test both implementations
- Use different bots for different strategies
- Maximize arbitrage coverage
- Compare performance

## Future Support

arb-assist is designed to be extensible. Future versions may support additional arbitrage bots. The modular architecture allows for easy integration of new bot types.

## Getting Help

- **SMB Support**: [SolanaMevBot Documentation](https://docs.solanamevbot.com/)
- **NotArb Support**: [NotArb GitHub](https://github.com/NotArb/Release)
- **arb-assist Support**: [Discord Community](https://discord.gg/ADtnjdy5m5)