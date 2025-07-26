# Configuration Schema

{% hint style="info" %}
ℹ️ **REVIEW NEEDED**: This schema documentation should be verified against the actual arb-assist code. Some configuration fields or options may not exist or have different defaults than documented here.
{% endhint %}

Complete reference for the arb-assist configuration file (`config.toml`).

## Top-Level Configuration

### Connection Settings

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `rpc_url` | string | Yes | - | Main RPC endpoint URL |
| `grpc_url` | string | Yes | - | GRPC streaming endpoint |
| `grpc_token` | string | No | `""` | Authentication token for GRPC |
| `grpc_engine` | string | Yes | - | GRPC engine type: `"yellowstone"` or `"thor"` |
| `mode` | string | Yes | - | Output mode: `"smb"`, `"na"`, or `"both"` |
| `port` | integer | No | `0` | HTTP server port (0 = disabled) |
| `helius_key` | string | No | `""` | Helius API key for fee estimation |

### Core Settings

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `update_interval` | integer | No | `10000` | Config update frequency (ms) |
| `run_interval` | integer | No | `60000` | Min time between trades per mint (ms) |
| `halflife` | integer | No | `120000` | Statistics decay half-life (ms) |
| `ignore_filters` | boolean | No | `false` | Bypass all filter thresholds |
| `include_token2022` | boolean | No | `false` | Include Token-2022 mints |
| `log_output` | boolean | No | `false` | Enable verbose logging |

### DEX Configuration

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `dexes` | array[string] | Yes | - | DEX program IDs to monitor |
| `filter_programs` | boolean | No | `false` | Only analyze specified programs |
| `arb_programs` | array[string] | Conditional | - | Programs to monitor (required if `filter_programs = true`) |
| `exclude_mints` | array[string] | No | `[]` | Token mints to exclude |
| `base_mints` | array[string] | Yes | - | Base tokens for arbitrage paths |

### Mint Selection

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `mints_to_arb` | array[integer] | Yes | - | Mints per transaction group |
| `mints_to_rank` | integer | Yes | - | Total mints to track |
| `output` | string | Yes | - | Output filename (without extension) |

### Sorting Strategies

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `intermint_sort_strategy` | object | Yes | - | Mint ranking strategy |
| `pool_sort_strategy` | object | Yes | - | Pool ranking strategy |

Strategy object structure:
```toml
{
  metric = "profit",           # Metric name
  direction = "descending"     # "ascending" or "descending"
}
```

### RPC Configuration

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `config_rpc_url` | string | No | Same as `rpc_url` | RPC for generated config |
| `sending_rpc_urls` | array[string] | Yes (SMB) | - | RPCs for sending transactions |

### ALUTs Configuration

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `aluts_per_pool` | integer | No | `20` | ALUTs to collect per pool |
| `aluts` | array[string] | No | `[]` | Custom ALUTs to include |

### Dynamic Tips

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `dynamic_jito_tip_mode` | string | No | `"none"` | Tip mode: `"parsed"`, `"tipstream"`, or `"none"` |
| `jito_uuid` | string | No | `""` | UUID for Jito tracking |

## Filter Thresholds

Array of threshold objects with increasingly strict requirements:

```toml
filter_thresholds = [
  { threshold_object_1 },
  { threshold_object_2 },
  ...
]
```

### Threshold Object Fields

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `min_profit` | integer | No | `0` | Minimum profit (lamports) |
| `min_profit_per_arb` | integer | No | `0` | Min average profit per trade |
| `min_roi` | float | No | `0.0` | Minimum ROI ratio |
| `min_txns` | integer | No | `0` | Min successful arbitrages |
| `min_fails` | integer | No | `0` | Maximum failed transactions |
| `min_net_volume` | integer | No | `0` | Min directional volume |
| `min_total_volume` | integer | No | `0` | Min total trading volume |
| `min_imbalance_ratio` | float | No | `0.0` | Min volume imbalance (0-1) |
| `max_imbalance_ratio` | float | No | `1.0` | Max volume imbalance (0-1) |
| `min_liquidity` | integer | No | `0` | Min pool liquidity |
| `min_turnover` | float | No | `0.0` | Min volume/liquidity ratio |
| `min_volatility` | float | No | `0.0` | Min price volatility |
| `max_cu_limit` | integer | Yes | - | Max compute units |
| `top_pool_num` | integer | Yes | - | Pools to use (min 2) |
| `memo` | string | No | `""` | Optional memo field |

## Strategy Levels

### Spam Levels

Array of spam strategy configurations:

```toml
spam_levels = [
  { spam_config_1 },
  { spam_config_2 },
  ...
]
```

#### Spam Configuration Fields

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `filter_level` | integer | Yes | - | Maps to filter_thresholds index |
| `bundle_groups` | array[integer] | Yes | - | Which mint groups to use |
| `process_delay` | integer | No | `400` | Delay between attempts (ms) |
| `min_cu_percentile` | integer | No | `0` | Min fee percentile (0-100) |
| `max_cu_percentile` | integer | No | `100` | Max fee percentile (0-100) |
| `min_cu_price` | integer | No | `0` | Min compute unit price |
| `max_cu_price` | integer | No | `1000000` | Max compute unit price |
| `tx_count` | integer | No | `1` | Transactions per burst |
| `fee_strategy` | string | No | `"Random"` | Strategy: `"Random"`, `"Linear"`, `"Exponential"` |

### Jito Levels

Array of Jito bundle configurations:

```toml
jito_levels = [
  { jito_config_1 },
  { jito_config_2 },
  ...
]
```

#### Jito Configuration Fields

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `filter_level` | integer | Yes | - | Maps to filter_thresholds index |
| `bundle_groups` | array[integer] | Yes | - | Which mint groups to use |
| `process_delay` | integer | No | `400` | Delay between attempts (ms) |
| `min_tip` | integer | No | `0` | Minimum Jito tip |
| `max_tip` | integer | No | `1000000` | Maximum Jito tip |
| `min_tip_percentile` | integer | No | `0` | Min tip percentile (0-100) |
| `max_tip_percentile` | integer | No | `100` | Max tip percentile (0-100) |
| `tx_count` | integer | No | `1` | Transactions per bundle |
| `tip_strategy` | string | No | `"Random"` | Strategy: `"Random"`, `"Linear"`, `"Exponential"` |
| `sending_strategy` | string | No | `"AllAtOnce"` | `"AllAtOnce"` or `"OneByOne"` |
| `no_failure_mode` | boolean | No | `false` | Ensure all txs succeed |

### Fast Levels

Array of fast lane configurations:

```toml
fast_levels = [
  { fast_config_1 },
  { fast_config_2 },
  ...
]
```

#### Fast Configuration Fields

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `filter_level` | integer | Yes | - | Maps to filter_thresholds index |
| `bundle_groups` | array[integer] | Yes | - | Which mint groups to use |
| `process_delay` | integer | No | `400` | Delay between attempts (ms) |
| `min_tip` | integer | No | `0` | Minimum tip amount |
| `max_tip` | integer | No | `1000000` | Maximum tip amount |
| `min_tip_percentile` | integer | No | `0` | Min tip percentile (0-100) |
| `max_tip_percentile` | integer | No | `100` | Max tip percentile (0-100) |
| `tx_count` | integer | No | `1` | Transactions per bundle |
| `tip_strategy` | string | No | `"Random"` | Strategy: `"Random"`, `"Linear"`, `"Exponential"` |
| `sending_strategy` | string | No | `"AllAtOnce"` | `"AllAtOnce"` or `"OneByOne"` |
| `no_failure_mode` | boolean | No | `false` | Ensure all txs succeed |
| `min_cu_percentile` | integer | No | `0` | Min fee percentile (0-100) |
| `max_cu_percentile` | integer | No | `100` | Max fee percentile (0-100) |
| `min_cu_price` | integer | No | `0` | Min compute unit price |
| `max_cu_price` | integer | No | `1000000` | Max compute unit price |
| `fee_strategy` | string | No | `"Random"` | Fee strategy |

### Astralane Levels

Array of Astralane configurations with same structure as fast_levels.

### Nextblock Levels

Array of Nextblock configurations with same structure as fast_levels.

### Node1 Levels  

Array of Node1 configurations with same structure as fast_levels.

### Blockrazor Levels

Array of Blockrazor configurations with same structure as fast_levels.

## SMB-Specific Configuration

```toml
[smb]
output = "smb-config"
max_retries = 0
enable_simple_send = false
cetiloan = true
merge_mints = true
skip_ata_creation = false
```

### SMB Fields

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `output` | string | No | `"smb-config"` | Output filename |
| `max_retries` | integer | No | `0` | Transaction retry attempts |
| `enable_simple_send` | boolean | No | `false` | Use simple send mode |
| `cetiloan` | boolean | No | `true` | Enable flash loans |
| `merge_mints` | boolean | No | `true` | Allow mint merging |
| `skip_ata_creation` | boolean | No | `false` | Skip ATA creation |

### SMB Sub-configurations

#### Jito Configuration
```toml
[smb.jito_config]
ips = ["192.168.1.0/24"]
min_profit = 5_000
use_min_profit = true
use_separate_tip_account = false
```

#### Fast Configuration
```toml
[smb.fast_config]
min_profit = 5_000
use_min_profit = true
auth_value = ""
request_params = { frontRunningProtection = false }
urls = ["https://fast.circular.fi/transactions/no-failure/smb"]
```

#### Astralane Configuration
```toml
[smb.astralane_config]
min_profit = 5_000
use_min_profit = true
auth_value = ""
request_params = { mevProtect = false }
```

#### Nextblock Configuration
```toml
[smb.nextblock_config]
min_profit = 5_000
use_min_profit = true
auth_value = ""
request_params = { 
  skipPreFlight = true, 
  snipeTransaction = true, 
  frontRunningProtection = false, 
  disableRetries = true, 
  revertOnFail = false 
}
```

#### Node1 Configuration
```toml
[smb.node1_config]
min_profit = 5_000
use_min_profit = true
auth_value = ""
```

#### Blockrazor Configuration
```toml
[smb.blockrazor_config]
min_profit = 5_000
use_min_profit = true
auth_value = ""
```

### Strategy Configuration Fields

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `ips` | array[string] | No | `[]` | Allowed IP ranges (jito_config only) |
| `min_profit` | integer | No | `0` | Min profit to use strategy |
| `use_min_profit` | boolean | No | `false` | Enforce minimum profit |
| `auth_value` | string | No | `""` | Authentication value |
| `use_separate_tip_account` | boolean | No | `false` | Jito-specific |
| `request_params` | object | No | `{}` | Service-specific parameters |
| `urls` | array[string] | No | `[]` | Service URLs (fast_config only) |

## NotArb-Specific Configuration

```toml
[notarb]
output = "notarb-config"
jvm_args = ["-server", "-Xmx8192m"]
keypair_path = "${DEFAULT_KEYPAIR_PATH}"
protect_keypair = true
threads = 0
flash_loan = true
# ... more fields
```

### NotArb Fields

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `output` | string | No | `"notarb-config"` | Output filename |
| `jvm_args` | array[string] | No | `[]` | JVM arguments |
| `keypair_path` | string | Yes | - | Path to keypair file |
| `protect_keypair` | boolean | No | `true` | Use keypair protection |
| `threads` | integer | No | `0` | Thread pool size (0=dynamic) |
| `flash_loan` | boolean | No | `true` | Enable flash loans |
| `max_lookup_tables` | integer | No | `10` | Max ALUTs per transaction |
| `meteora_bin_limit` | integer | No | `20` | Max Meteora DLMM bins |
| `ips_file_path` | string | No | `""` | Path to proxy IPs file |
| `prefunded_keypairs_path` | string | No | `""` | Path to keypairs file |
| `jito_targets` | array[string] | No | `[]` | Jito endpoint URLs |
| `max_bundle_transactions` | integer | No | `1` | Max txs per bundle |
| `borrow_amount` | integer | No | `500000000000` | Flash loan amount |

### NotArb Sub-configurations

```toml
[notarb]
token_accounts_checker = { rpc_url = "", delay_seconds = 3 }
blockhash_updater = { rpc_url = "", delay_ms = 400 }
account_size_loader = { 
  rpc_url = "", 
  invalid_account_size = 200, 
  buffer_size = 1500 
}
market_loader = ""
lookup_table_loader = ""
wsol_unwrapper = {
  enabled = true,
  check_minutes = 1,
  trigger_sol = 0.5,
  target_sol = 5,
  priority_fee_lamports = 190,
  reader_rpc_url = ""
}
```

## Metric Types

Valid values for sorting metrics:

### Financial Metrics
- `profit` - Total arbitrage profit
- `roi` - Return on investment
- `profit_per_arb` - Average profit per trade
- `fee` - Transaction fees

### Volume Metrics
- `buy_volume` - Total buy volume
- `sell_volume` - Total sell volume
- `net_volume` - |buy - sell| volume
- `total_volume` - buy + sell volume
- `imbalance` - Volume imbalance ratio

### Success Metrics
- `successful_arbs` - Successful trade count
- `fails` - Failed transaction count
- `buy_count` - Number of buys
- `sell_count` - Number of sells

### Market Metrics
- `liquidity` - Pool liquidity
- `pool_age` - Time since first seen
- `turnover` - Volume/liquidity ratio
- `volatility` - Price volatility measure

## Environment Variables

Supported environment variable expansions:

| Variable | Description | Example |
|----------|-------------|---------|
| `${DEFAULT_KEYPAIR_PATH}` | Default Solana keypair location | `~/.config/solana/id.json` |
| Custom variables | Any env var you define | `${MY_RPC_URL}` |

## Validation Rules

1. **Required Fields**: Must be present
2. **Type Matching**: Values must match expected types
3. **Range Validation**: Numeric values within bounds
4. **Logical Consistency**: Related fields must align
5. **Array Lengths**: `mints_to_rank >= sum(mints_to_arb)`
6. **Threshold Order**: Filter thresholds in ascending strictness
7. **Valid Enums**: String values from allowed options