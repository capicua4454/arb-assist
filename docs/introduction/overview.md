# Overview

## What is arb-assist?

**arb-assist** is an automated configuration generator for Solana on-chain arbitrage bots. It acts as an intelligent middleware that analyzes blockchain activity in real-time to identify profitable arbitrage opportunities and automatically generates optimized configuration files for your trading bot.

## Core Functionality

### Real-time Market Analysis
arb-assist continuously monitors the Solana blockchain through GRPC streams, tracking:
- Successful arbitrage transactions
- Trading volumes and patterns
- Pool liquidity and imbalances
- Market volatility and trends

### Intelligent Configuration Generation
Based on the analyzed data, arb-assist:
- Ranks tokens by profitability potential
- Filters opportunities using customizable thresholds
- Generates bot-specific configuration files
- Updates settings dynamically as market conditions change

### Multi-Bot Support
arb-assist supports multiple arbitrage bot implementations:
- **SolanaMevBot On-Chain**: Generates `.toml` configuration files
- **NotArb onchain-bot**: Generates `.json` configuration files and market data

## How It Works

{% mermaid %}
graph TD
    A[GRPC Stream] -->|Transaction Data| B[arb-assist]
    B -->|Analyze| C[Market Intelligence]
    C -->|Filter & Rank| D[Profitable Opportunities]
    D -->|Generate| E[Bot Configuration]
    E -->|SMB| F[smb-config.toml]
    E -->|NotArb| G[notarb-config.json]
    F --> H[SMB-Onchain Bot]
    G --> I[NotArb Bot]
{% endmermaid %}

## Key Benefits

### 1. **Automated Operation**
No manual configuration updates needed - arb-assist continuously monitors markets and updates your bot settings automatically.

### 2. **Market-Adaptive**
Responds to market conditions by adjusting:
- Priority fees based on network congestion
- Jito tips based on competition
- Active trading pairs based on profitability

### 3. **Risk Management**
Built-in filtering prevents your bot from:
- Trading unprofitable pairs
- Wasting funds on excessive fees
- Operating during unfavorable market conditions

### 4. **Performance Optimization**
- Focuses resources on the most profitable opportunities
- Minimizes failed transactions
- Maximizes ROI through intelligent fee management

## Architecture

arb-assist operates as a standalone service that:
1. Connects to Solana via RPC and GRPC endpoints
2. Streams and analyzes transaction data
3. Maintains an in-memory database of market statistics
4. Generates configuration files at regular intervals
5. Optionally serves configurations via HTTP for remote bots

## Use Cases

### Solo Traders
Run arb-assist alongside your bot on the same VPS for automated configuration management.

### Fleet Management
Use the file server mode to manage multiple bots from a central arb-assist instance.

### Market Research
Leverage arb-assist's analytics to understand arbitrage dynamics and optimize strategies.

## Next Steps

Ready to get started? Head to the [Prerequisites](../getting-started/prerequisites.md) section to ensure your system meets the requirements.