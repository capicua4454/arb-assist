# smb-copy-public

`smb-copy` is an automated config generator for [SolanaMevBot On-Chain](https://docs.solanamevbot.com/home/releases). It analyzes recent on-chain activity to identify profitable mints for arbitrage and generates a config file accordingly.

## 🚀 Features

- Automatically identifies and filters mints based on:
  - Transaction count
  - Net volume per minute
  - ROI (return on investment)
- Dynamically generates config files for `smb-onchain`
- Selects most profitable pools based on historical data
- Auto-detects most used ALUTs (Address Lookup Tables)
- Estimates dynamic priority fees via Helius API

If no mints meet your criteria, a dummy config is generated to stop `smb-onchain`.

---

## 🔧 Setup Instructions

### 1. Prerequisites

- Your license file must be in the same folder as `smb-copy` and `smb-onchain`
- Your license is locked to the server IP and must be run from a whitelisted server
- You must have either Yellowstone GRPC or ThorStreamer to stream transaction data

### 2. Install Node.js

Using NVM:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
. "$HOME/.nvm/nvm.sh"
nvm install 22
```

Verify installation:

```bash
node -v     # Should print "v22.15.0"
npm -v      # Should print "10.9.2"
nvm current # Should print "v22.15.0"
```

### 3. Install PM2

```bash
npm install -g pm2
```

---

## 📦 Running

Make sure to give all files the proper permissions to run:

```bash
chmod +x *
```

Start `smb-onchain` with:

```bash
pm2 start smb-onchain --watch -- run smb-config.toml
```

This watches `smb-config.toml` for changes. When `smb-copy` updates the config, `smb-onchain` restarts automatically.

Keep `smb-copy` running using `pm2`, `tmux`, or similar tools.

For more info on [tmux](https://hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/)
For more info on [pm2](https://pm2.keymetrics.io/docs/usage/quick-start/)

---

## 🛠 Troubleshooting

**Error:**

```
called `Result::unwrap()` on an `Err` value: Os { code: 24, kind: Uncategorized, message: "Too many open files" }
```

**Fix:**

```bash
ulimit -n 65536
```

---

## 📊 Example Output

`mints` ranked by arbitrage profitability:

![Arb Ranking](https://github.com/user-attachments/assets/379ac9f1-1029-4539-84c5-08bb77387009)

Pools chosen based on historical arb activity:

![Pool Selection](https://github.com/user-attachments/assets/861602cb-6367-463f-bbb1-577cb2d0de74)

Estimated priority fees using Helius:

![Priority Fees](https://github.com/user-attachments/assets/ff57af74-9a79-4bdb-8b5d-51df8f28945c)

## 🧠 Optimizing your Filters

To start, I recommend you filter arb programs to only copy Solana MEV bot on-chain transactions. Run smb-copy and take note of the statistics for the top 10 mints. Pay attention to the Txns, net vol/min, and ROI. You should check these numbers during times when the market is hot and when the market is cold. For your filter settings, choose numbers that will shut down the bot when the market is cold and turn on the bot when the market is hot.
