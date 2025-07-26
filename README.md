# arb-assist

Automated configuration generator for Solana on-chain arbitrage bots.

## Overview

**arb-assist** analyzes recent blockchain activity to identify profitable mints for arbitrage and generates optimized configuration files for [SolanaMevBot On-Chain](https://docs.solanamevbot.com) and [NotArb onchain-bot](https://github.com/NotArb/Release).

## 📚 Documentation

For comprehensive documentation, installation guides, and configuration details, visit our GitBook:

**[📖 Full Documentation](https://cosmic-hiker.gitbook.io/arb-assist/)**

## 🚀 Quick Start

1. **Prerequisites**
   - 8-core+ dedicated Ryzen VPS
   - Yellowstone GRPC or ThorStreamer access
   - Node.js v22+
   - Valid arb-assist license

2. **Download**
   ```bash
   wget https://github.com/capicua4454/arb-assist/raw/refs/heads/main/arb-assist
   wget https://github.com/capicua4454/arb-assist/raw/refs/heads/main/config.toml.example
   chmod +x arb-assist
   ```

3. **Configure**
   - Rename `config.toml.example` to `config.toml`
   - Edit settings for your GRPC connection and preferences

4. **Run**
   ```bash
   ./arb-assist
   ```

## 🔑 Key Features

- 📈 **Auto Bot Control** - Turn bots on/off based on market conditions
- 💰 **Smart Fee Management** - Optimize priority fees and Jito tips
- 🛠️ **Market Intelligence** - Copy strategies from successful arbitrageurs
- 📊 **Advanced Filtering** - Filter by ROI, volume, success rate
- ⚡ **Multi-bot Support** - Works with SMB-Onchain and NotArb

## 🤝 Community & Support

- **Discord**: [Join our community](https://discord.gg/ADtnjdy5m5)
- **Documentation**: [arb-assist docs](https://cosmic-hiker.gitbook.io/arb-assist/)
- **Support**: [#support on Discord](https://discord.com/channels/1305715493448974336/1377389330930204803)

## 📄 License

arb-assist requires a valid license tied to your server IP address. Contact us on Discord for licensing information.

---

*For detailed setup instructions, configuration options, and troubleshooting, please refer to the [full documentation](https://cosmic-hiker.gitbook.io/arb-assist/).*