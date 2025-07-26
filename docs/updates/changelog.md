# Changelog

All notable changes to arb-assist will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- Multi-region support improvements
- Enhanced performance monitoring
- New sorting metrics

### Changed
- Optimized GRPC stream processing
- Improved memory management

### Fixed
- Race condition in config updates
- Memory leak in long-running instances

## [1.2.0] - 2024-01-26

### Added
- **Token2022 Support**: Full support for Token Extensions Program
  - New config option: `include_token2022`
  - Automatic detection of Token2022 mints
  - Compatible pool discovery

- **Profit Per Arbitrage Metric**: New sorting and filtering option
  - `metric = "profit_per_arb"` in sort strategies
  - `min_profit_per_arb` in filter thresholds
  - Better consistency measurement

- **Multi-Base Token Support**: Trade with multiple base tokens
  - `base_mint` → `base_mints` array format
  - Support for WSOL + USDC simultaneously
  - 3-hop and 4-hop arbitrage paths

- **Enhanced Logging**: Detailed operational logging
  - New `log_output = true` option
  - Performance metrics in logs
  - Debug information for troubleshooting

### Changed
- **Configuration Format**: Minor updates for new features
  - `base_mint` is now `base_mints` (array)
  - Additional fields in filter thresholds
  - Expanded strategy level options

- **Performance Improvements**:
  - 20% faster GRPC processing
  - Reduced memory usage
  - Better CPU utilization

### Fixed
- NotArb output format compatibility
- Memory leak in pool tracking
- Jito tip calculation accuracy
- File server concurrent access

### Security
- Updated dependencies
- Improved input validation
- Enhanced license verification

## [1.1.5] - 2024-01-15

### Added
- **Multi-Strategy Levels**: Different strategies for different market conditions
  - `spam_levels`, `jito_levels`, `fast_levels`
  - Tier-based activation
  - Dynamic strategy switching

### Changed
- Improved Jito bundle success rates
- Better priority fee estimation
- Optimized config generation

### Fixed
- GRPC reconnection issues
- Config file atomicity
- Race conditions in updates

## [1.1.0] - 2023-12-20

### Added
- **Dynamic Fee Strategies**: Flexible fee configuration
  - Random, Linear, Exponential strategies
  - Percentile-based pricing
  - Min/max ranges

- **Advanced Jito Integration**:
  - Dynamic tip calculation
  - Tip floor tracking
  - Multi-region support
  - Bundle optimization

- **Helius Integration**: Real-time priority fees
  - API key support
  - Percentile-based estimates
  - Network congestion awareness

### Changed
- **Output Format Overhaul**:
  - Structured routing configuration
  - Pool categorization by DEX
  - Improved ALUT management

- **Configuration Structure**:
  - Strategy-based organization
  - Clearer parameter names
  - Better defaults

### Deprecated
- Fixed fee values (use strategies instead)
- Single tip amounts (use ranges)
- Legacy output format

### Fixed
- Transaction parsing accuracy
- Memory usage optimization
- GRPC stream handling

## [1.0.8] - 2023-11-30

### Added
- Helius API support for priority fees
- File server authentication options
- Performance monitoring endpoints

### Changed
- Improved error messages
- Better config validation
- Updated documentation

### Fixed
- File server security issues
- Config update race conditions
- TOML parsing edge cases

## [1.0.5] - 2023-11-15

### Added
- **File Server Mode**: Serve configs over HTTP
  - Port configuration
  - Multi-bot support
  - Remote management

### Changed
- Network timeout handling
- Connection pooling
- Resource cleanup

### Fixed
- Memory leaks in long runs
- File descriptor exhaustion
- Concurrent write issues

## [1.0.0] - 2023-10-01

### Added
- Initial public release
- SMB-Onchain support
- NotArb support
- Basic arbitrage detection
- Configuration generation
- GRPC streaming (Yellowstone/Thor)
- Multi-DEX support
- Filter thresholds
- Sort strategies
- Flash loan support
- ALUTs discovery

### Known Issues
- High memory usage on large datasets
- Limited strategy options
- Basic fee management

## Version History Summary

| Version | Release Date | Major Changes |
|---------|--------------|---------------|
| 1.2.0 | 2024-01-26 | Token2022, Multi-base tokens |
| 1.1.5 | 2024-01-15 | Multi-strategy levels |
| 1.1.0 | 2023-12-20 | Dynamic fees, Advanced Jito |
| 1.0.8 | 2023-11-30 | Helius integration |
| 1.0.5 | 2023-11-15 | File server mode |
| 1.0.0 | 2023-10-01 | Initial release |

## Upgrade Notes

### To 1.2.0
- Update `base_mint` to `base_mints` array format
- Add `include_token2022 = false` if not using Token2022
- Review filter thresholds for new `min_profit_per_arb` option

### To 1.1.0
- Migrate fixed fees to strategy format
- Update Jito configuration structure
- Review output format changes

### To 1.0.5
- Add `port = 0` to disable file server
- Review security settings if enabling

## Roadmap

### Planned Features
- [ ] WebSocket real-time updates
- [ ] Historical data analysis
- [ ] Machine learning integration
- [ ] Cross-chain arbitrage
- [ ] Advanced backtesting
- [ ] GUI configuration tool
- [ ] Mobile monitoring app

### Under Consideration
- Database persistence
- Kubernetes deployment
- Advanced analytics
- Strategy marketplace
- Community strategies

## Contributing

We welcome contributions! Please see our [Contributing Guide](https://github.com/capicua4454/arb-assist/blob/main/CONTRIBUTING.md) for details.

### Reporting Issues
1. Check existing issues first
2. Provide detailed reproduction steps
3. Include configuration (remove sensitive data)
4. Share relevant logs
5. Specify versions used

### Feature Requests
1. Describe the use case
2. Explain expected behavior
3. Provide examples if possible
4. Consider implementation impact

## Security Updates

Security issues are taken seriously. Please report security vulnerabilities privately to the development team.

### Security Policy
- Regular dependency updates
- Code security audits
- Vulnerability disclosure process
- Rapid patch releases

## License

arb-assist is proprietary software. See LICENSE file for details.

---

For more detailed information about each release, visit the [GitHub Releases](https://github.com/capicua4454/arb-assist/releases) page.