# Welcome to arb-assist

<figure><img src="../.gitbook/assets/arb-assist-banner.png" alt=""><figcaption><p>arb-assist - Automated Config Generator for Solana Arbitrage Bots</p></figcaption></figure>

## What is arb-assist?

**arb-assist** (formerly known as smb-copy) is an automated configuration generator designed for Solana on-chain arbitrage bots. It analyzes recent blockchain activity to identify profitable mints for arbitrage and generates optimized configuration files automatically.

## Supported Bots

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><strong>SolanaMevBot On-Chain</strong></td><td>Generate configs for SMB-Onchain with dynamic priority fees and Jito bundle support</td><td><a href="bot-integration/smb-onchain.md">smb-onchain.md</a></td></tr><tr><td><strong>NotArb onchain-bot</strong></td><td>Create config, markets.json, and lookup-tables.json files for NotArb</td><td><a href="bot-integration/notarb.md">notarb.md</a></td></tr></tbody></table>

## Key Features

* 📈 **Auto Bot Control**: Automatically activate your bot when markets pump, deactivate when they cool down
* 💰 **Maximize Profits**: Keep more of your wins, waste less on gas with intelligent fee management
* 📝 **Dynamic Config Generation**: Create optimized configs based on real-time market analysis
* 🛠️ **Market Intelligence**: Copy profitable mints and pools from any arbitrage program or wallet
* 📊 **Smart Filtering**: Filter opportunities by ROI, volume, success rate, and more
* ⚙️ **Adaptive Fee Scaling**: Set priority fees dynamically using multiple strategies
* 🍆 **Dynamic Jito Tips**: Automatically adjust Jito tips based on market conditions
* ⚡ **Multi-token Support**: Works with WSOL, USDC, and custom base tokens
* 🔍 **ALUTs Management**: Automatically retrieves and manages lookup tables

## Quick Links

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><strong>Discord Community</strong></td><td>Join for support and updates</td><td></td><td><a href="https://discord.gg/ADtnjdy5m5">https://discord.gg/ADtnjdy5m5</a></td></tr><tr><td><strong>GitHub Repository</strong></td><td>Source code and releases</td><td></td><td><a href="https://github.com/capicua4454/arb-assist">https://github.com/capicua4454/arb-assist</a></td></tr><tr><td><strong>Quick Start Guide</strong></td><td>Get up and running in minutes</td><td></td><td><a href="getting-started/quick-start.md">quick-start.md</a></td></tr></tbody></table>

## Getting Started

{% content-ref url="getting-started/prerequisites.md" %}
[prerequisites.md](getting-started/prerequisites.md)
{% endcontent-ref %}

{% content-ref url="getting-started/installation.md" %}
[installation.md](getting-started/installation.md)
{% endcontent-ref %}

{% content-ref url="getting-started/quick-start.md" %}
[quick-start.md](getting-started/quick-start.md)
{% endcontent-ref %}

## License Requirements

{% hint style="warning" %}
**Important**: arb-assist requires a valid license file that is locked to your server IP address. The license must be placed in the same directory as the `arb-assist` executable.
{% endhint %}

## System Requirements

* **VPS**: 8-core Ryzen or better (heavy GRPC stream processing)
* **Network**: Yellowstone GRPC or ThorStreamer access
* **OS**: Linux (Ubuntu recommended)
* **Node.js**: Version 22 or higher