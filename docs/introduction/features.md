# Key Features

## 📈 Auto Bot Control

### Dynamic Activation
arb-assist monitors market activity and automatically:
- **Activates** your bot when profitable opportunities emerge
- **Deactivates** when markets cool down to prevent losses
- **Adjusts** strategy levels based on market intensity

### Configurable Thresholds
Define multiple market condition tiers:
```toml
filter_thresholds = [
  {min_profit = 1_000_000, min_roi = 1, min_txns = 1},    # Low activity
  {min_profit = 10_000_000, min_roi = 2, min_txns = 5},   # Medium activity
  {min_profit = 100_000_000, min_roi = 5, min_txns = 10}, # High activity
]
```

## 💰 Profit Maximization

### Intelligent Fee Management
- **Dynamic Priority Fees**: Adjusts based on network congestion
- **Jito Tip Optimization**: Sets tips based on competition levels
- **Multi-tier Strategies**: Different fee levels for different market conditions

### ROI-Based Filtering
Ensures every trade is profitable by considering:
- Transaction costs (gas fees)
- Expected profit margins
- Success probability

## 📝 Dynamic Config Generation

### Real-time Updates
- Configurations regenerate every 10 seconds (configurable)
- Instant response to market changes
- No manual intervention required

### Multi-Format Support
- **SMB-Onchain**: TOML format with full feature support
- **NotArb**: JSON format with markets and lookup tables
- **Both**: Generate configs for multiple bots simultaneously

## 🛠️ Market Intelligence

### Copy Trading Strategies
Monitor and replicate strategies from:
- Successful arbitrage programs
- Profitable trader wallets
- Top-performing bots

### Comprehensive Data Analysis
Track and analyze:
- Arbitrage success rates
- Trading volumes (buy/sell)
- Pool liquidity and imbalances
- Market volatility

## 📊 Smart Filtering System

### Multi-dimensional Filters
Filter opportunities by:
- **Profit**: Minimum profit thresholds
- **ROI**: Return on investment ratios
- **Volume**: Trading volume requirements
- **Success Rate**: Historical performance
- **Liquidity**: Pool depth requirements
- **Imbalance**: Buy/sell pressure ratios

### Cascading Filter Levels
```toml
# Tier 1: Conservative
{min_profit = 1_000_000, min_roi = 2, min_txns = 10}

# Tier 2: Moderate
{min_profit = 10_000_000, min_roi = 5, min_txns = 50}

# Tier 3: Aggressive
{min_profit = 100_000_000, min_roi = 10, min_txns = 100}
```

## ⚙️ Adaptive Fee Scaling

### Multiple Pricing Strategies
- **Random**: Varies fees within defined ranges
- **Linear**: Gradual fee increases
- **Exponential**: Aggressive scaling for competitive markets

### Data Sources
- **Helius API**: Real-time priority fee estimates
- **Transaction Parsing**: Learn from successful transactions
- **Percentile-based**: Use network-wide fee distributions

## 🍆 Dynamic Jito Tips

### Intelligent Tip Management
- **Floor Tracking**: Monitor Jito bundle floor prices
- **Competition Analysis**: Adjust based on other bundle submissions
- **Profit-based Scaling**: Higher tips for higher profit opportunities

### Bundle Optimization
- **Group Management**: Different tips for different token groups
- **Success Mode**: Ensure bundle execution when critical
- **Cost Control**: Maximum tip limits to prevent overspending

## ⚡ Multi-token Support

### Base Token Flexibility
- **WSOL**: Native Solana wrapped token
- **USDC**: Stablecoin arbitrage
- **Custom Tokens**: Any SPL token as base
- **Multi-hop**: 3-hop and 4-hop arbitrage paths

### Token Exclusions
Automatically filter out:
- Specified intermediary tokens
- Low-liquidity tokens
- Problematic token implementations

## 🔍 ALUTs Management

### Automatic Discovery
- Extracts ALUTs from successful transactions
- Identifies most commonly used tables
- Updates dynamically as patterns change

### Optimization
- Limits ALUTs per transaction
- Prioritizes by usage frequency
- Reduces transaction size

## 🚀 Performance Features

### Half-life Decay System
Weights recent activity more heavily:
- Configurable decay rates
- Prevents stale data influence
- Responsive to market shifts

### Parallel Processing
- Multi-threaded GRPC processing
- Efficient data aggregation
- Minimal latency

### Resource Management
- Memory-efficient data structures
- Automatic cleanup of old data
- Configurable update intervals

## 🔐 Security Features

### License Protection
- IP-locked licensing system
- Secure key management
- Encrypted private key support

### Safe Operations
- Read-only blockchain monitoring
- No direct trading execution
- Configuration-only output

## 📡 Integration Features

### File Server Mode
- Serve configs over HTTP
- Central management for bot fleets
- Remote configuration updates

### Monitoring Integration
- Detailed logging output
- Performance metrics
- Integration with PM2/systemd

## 🎯 Advanced Analytics

### Profitability Metrics
- Profit per arbitrage
- Success rate tracking
- Gas efficiency analysis

### Market Metrics
- Turnover rates
- Volatility measurements
- Liquidity depth analysis

### Custom Sorting
Sort opportunities by:
- Total profit
- ROI percentage
- Success count
- Volume metrics
- Pool age
- Custom combinations