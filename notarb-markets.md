# NotArb System Documentation

This document serves as the comprehensive reference for the NotArb ecosystem. It contains:
1.  **[Client Configuration](#1-client-configuration)**: How to set up and control your local bot.
2.  **[API Reference](#2-api-reference)**: Technical details of the `/notarb/markets` HTTP endpoint.

---

# 1. Client Configuration

**File:** `notarb-arb-assist.toml`

This file is the central control unit for your NotArb client. It manages execution limits, safety settings, and the specific strategies used to fetch market data from the API.

> **Note:** This file supports **Hot Reloading**. You do not need to restart your bot when making changes; simply save the file, and the bot will apply the new settings in the next cycle.

## General Settings `[notarb]`

These settings control the local behavior of your client, including safety caps and transaction limits.

| Parameter | Type | Description |
| :--- | :--- | :--- |
| `dry_run` | `bool` | If `true`, simulates transactions without sending them. **Recommended for testing.** |
| `max_txs_per_strategy` | `int` | Max transaction bundles generated per cycle. |
| `max_tokens_per_tx` | `int` | Max distinct tokens to target in one transaction. |
| `max_markets_per_token` | `int` | Max pools to check for a single token. |
| `max_priority_fee` | `int` | **Hard Cap (Lamports)** for Compute Unit (CU) fees. |
| `max_jito_tip` | `int` | **Hard Cap (Lamports)** for Jito tips. |

## Arb Assist Settings `[arb_assist]`

This section configures the API request sent to the NotArb Markets endpoint.

### Filter Strategy
You can define multiple filter "tiers". The API processes these intelligently to find opportunities that match your criteria, falling back to looser filters if necessary.

**⚠️ Important: Order Matters**
You must order your `filters` array from **Loosest (Index 0)** to **Strictest (Index N)**.
* **The API processes filters in reverse order** (strictest first).
* If the strict filter finds no matches, it falls back to the previous one.

### Configuration Template

Create a file named `notarb-arb-assist.toml` in your root directory and paste the following content:

```toml
# Changes to this file are dynamically loaded.

[notarb]
dry_run = true                  # Set to false to execute real trades
max_txs_per_strategy = 1
max_tokens_per_tx = 1
max_markets_per_token = 5
max_priority_fee = 100_000_000  # 0.1 SOL Cap
max_jito_tip = 100_000_000      # 0.1 SOL Cap

[arb_assist]
# ----------------------------------------------------------------
# FILTERS: Ordered from Loosest (Index 0) to Strictest (Index N)
# ----------------------------------------------------------------
filters = [
  # Filter 0 (Fallback): Moderate profit, low volume requirement
  { min_profit = 1_000_000, min_profit_per_arb = 0, min_roi = 0.0, min_txns = 0.1, min_fails = 0, min_net_volume = 0, min_total_volume = 1_000_000_000, min_liquidity = 1_000_000_000 },

  # Filter 1 (Standard): Higher profit (25M lamports), requires traction
  { min_profit = 25_000_000, min_profit_per_arb = 0, min_roi = 0.3, min_txns = 1, min_fails = 0, min_net_volume = 0, min_total_volume = 25_000_000_000, min_liquidity = 1_000_000_000 },
]

# Mints to exclude (e.g. known honeypots)
exclude_mints = []

# DEX Program IDs to scan
dexes = [
  "675kPX9MHTjS2zt1qfr1NYHuzeLXfQM9H24wFSUt1Mp8", # Raydium AMM v4
  "CPMMoo8L3F4NbTegBCKVNunggL7H1ZpdTHKxQB5qKP1C", # Raydium CPMM
  "pAMMBay6oceH9fJKBRHGP5D4bD4sWpmSwMn52FMfXEA", # PumpFun AMM
  "LBUZKhRxPF3XUpBCjp4YzTKgLccjZhTSDM9YuVaPwxo", # Meteora DLMM
  "cpamdpZCGKUy5JxQXB4dcpGPiikHawvSWAd6mEn1sGG", # Meteora DAMM v2
  "FUTARELBfJfQ8RDGhg1wdhddq1odMAJUePHFuBYfUxKq", # Futarchy
]

sort_strategy = { metric = "profit", direction = "descending" }
max_mints = 5
pools_per_mint = 3

# Base tokens (e.g. SOL, USDC)
base_mints = [
  "So11111111111111111111111111111111111111112",
  "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v",
  "USD1ttGY1N17NEEHLmELoaybftRBUSErhqYiQzvEmuB",
]
```

---

# 2. API Reference

### Supported DEX Pubkeys

These IDs correspond to the `dexes` array in your request.

| DEX | Program Pubkey |
|-----|----------------|
| Raydium AMM v4 | `675kPX9MHTjS2zt1qfr1NYHuzeLXfQM9H24wFSUt1Mp8` |
| Raydium CPMM | `CPMMoo8L3F4NbTegBCKVNunggL7H1ZpdTHKxQB5qKP1C` |
| PumpFun AMM | `pAMMBay6oceH9fJKBRHGP5D4bD4sWpmSwMn52FMfXEA` |
| Meteora DLMM | `LBUZKhRxPF3XUpBCjp4YzTKgLccjZhTSDM9YuVaPwxo` |
| Meteora DAMM v2 | `cpamdpZCGKUy5JxQXB4dcpGPiikHawvSWAd6mEn1sGG` |
| Futarchy | `FUTARELBfJfQ8RDGhg1wdhddq1odMAJUePHFuBYfUxKq` |
| Orca Whirlpools | `whirLbMiicVdio4qvUfM5KAg6Ct8VwpYzGff3uctyCc` |
| Raydium CLMM | `CAMMCzo5YL8w4VFF8KVHrK22GGUsp5VTaW7grrKgrWqK` |

### Filter Object Fields

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `min_profit` | u64 | 0 | Minimum total profit in lamports |
| `min_profit_per_arb` | u64 | 0 | Minimum profit per arbitrage |
| `min_roi` | f64 | 0.0 | Minimum ROI percentage |
| `min_txns` | f64 | 0.0 | Minimum transactions |
| `min_fails` | f64 | 0.0 | Minimum failed transactions |
| `min_net_volume` | u64 | 0 | Minimum net volume |
| `min_total_volume` | u64 | 0 | Minimum total volume |
| `min_liquidity` | u64 | 0 | Minimum liquidity |
| `max_cu_limit` | u64 | 0 | Maximum compute units (0 = no limit) |

---

## Response Specification

### Success Response (200 OK)

```json
{
  "filter_index": 2,
  "tokens": [
    {
      "mint": "3K2gEnouv7wjh8CMYtpfiLwHV2HQ9kJZnSpp9wXn4MzB",
      "total_profit": 150000000,
      "markets": [
        {
          "address": "PoolAddress...",
          "dex": "675kPX9MHTjS2zt1qfr1NYHuzeLXfQM9H24wFSUt1Mp8",
          "profit": 80000000
        }
      ]
    }
  ],
  "percentiles": {
    "p50": 500000,
    "j50": 100000
  },
  "timestamp": 1706123456
}
```

### Response Fields

* **`filter_index`**: The index of the filter that matched. `-1` means no filters matched.
* **`tokens`**: Array of opportunities found.
* **`percentiles`**: Network fee stats.
    * `p25-p99`: Compute Unit (CU) fee percentiles.
    * `j25-j99`: Jito tip percentiles.
* **`unknown_fields`**: Returns a list of any fields in your request that the API did not recognize (useful for debugging typos).

