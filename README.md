# arb-assist (formerly known as smb-copy)

`arb-assist` is an automated config generator for [SolanaMevBot On-Chain](https://docs.solanamevbot.com/home/releases). 

It can also generate the config, markets.json, and lookup-tables.json files for use with [NotArb onchain-bot](https://github.com/NotArb/Release/tree/main).

It analyzes recent on-chain activity to identify profitable mints for arbitrage and generates a config file accordingly.

For support, join us on [Discord](https://discord.gg/ADtnjdy5m5)

## 🕹️ Features:
- 📈 Auto Bot Control: Turn your bot on when markets pump, off when they cool down.,
- 💰 Maximize Profits: Keep more of your wins, waste less on gas.,
- 📝 Dynamic Config Generation: Create smb-onchain configs using arb-assist.,
- 🛠️ Market Intelligence: Copy mints and pools from any arb program or wallet.,
- 📊 Smart Filtering: Filter mints by successful arb transactions, ROI, and net swap volume.,
- ⚙️ Adaptive Fee Scaling: Set priority fees dynamically via Helius or copied wallets using percentiles.,
- 🍆 Dynamic Jito Tips: Set Jito tips dynamically using the Jito Bundle Floor Tracker,
- ⚡ Supports WSOL and USDC: Use spam or jito, exclude unwanted intermedium mints.,
- 🔍 ALUTs: Automatically retrieves most commonly used ALUTs from arb transactions.

If no mints meet your criteria, a dummy config is generated to stop `smb-onchain`.

---

## 🔧 Setup Instructions

### 1. Prerequisites

- Your license file must be in the same folder as `arb-assist` and `smb-onchain` or `NotArb`
- Your license is locked to the server IP and must be run from a whitelisted server
- You must have either Yellowstone GRPC or ThorStreamer to stream transaction data
- You should run it on a 8 core Ryzen VPS or better because arb-assist uses a very heavy GRPC stream

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

### 4. Install TMUX
```bash
sudo apt-get install tmux
```

### 5. Download Solana Mev Bot Onchain
```bash
sudo apt install wget
sudo apt install unzip
mkdir smb
cd smb
wget https://sourceforge.net/projects/solanamevbotonchain/files/smb-onchain-0.8.1.zip
unzip smb-onchain-0.8.1.zip
```

### 6. Download arb-assist
```bash
wget https://github.com/capicua4454/arb-assist/raw/refs/heads/main/arb-assist
wget https://github.com/capicua4454/arb-assist/raw/refs/heads/main/config.toml.example
```

After you are done, your directory should look like this:
![image](https://github.com/user-attachments/assets/f7b602c1-e099-4a6a-93a3-c7366b47e0a5)


---


## 📦 Running

Make sure to give all files the proper permissions to run:

```bash
chmod +x *
```

- You should encrypt your private key using smb-onchain or NotArb locally, then transfer only the encrypted private key to your server.
- Also, you need to transfer your license file for arb-assist that is tied to your server IP address.
- You should rename config.toml.example to config.toml - this is the config file used by arb-assist
- Then, you should open config.toml using nano or another text editor to configure your settings for arb-assist.
- You can use one of the examples on the arb-assist github as a starting template.

Your directory should look something like this:

![image](https://github.com/user-attachments/assets/9716935c-5b33-432e-a088-d0ea38d96f33)

- *.license is your license file. It should have your IP address instead of 0.0.0.0
- config.toml is the config file used by arb-assist, the config file used by smb-onchain will be generated after running arb-assist
- arb-assist is this script
- smb-onchain is the SolanaMevBot On-Chain bot
- If you are using NotArb, you will have the NotArb files and executables instead

First, increase the ulimit

```bash
ulimit -n 65536
```

Then, test arb-assist to make sure everything is working:

```bash
./arb-assist
```

On start-up, you will see a few log messages that confirm your license is valid and that you are successfully connected to your GRPC datastream:

![image](https://github.com/user-attachments/assets/d98c011c-8bed-4966-85b1-1cb8b7b97e0f)

After a short time, you will see a lot of data printed on the screen:

This shows the ranking of top 10 mints and important statistics:

![image](https://github.com/user-attachments/assets/405b67e3-ca40-4070-ba45-1f9e62d040ce)

For any mints that pass your filters, more detailed information will be printed out, including the ALUT usage and the associated pools:

![image](https://github.com/user-attachments/assets/eccf6c4c-7147-4f7f-867a-986b125d8dcf)

You will also see data on priority fees and jito tips broken down by percentiles:

![image](https://github.com/user-attachments/assets/1b43ddd7-e4d5-426c-b14f-b9f9e53e0519)

After you have confirmed that arb-assist is working, you can run it in the background with either pm2 or tmux.

To run it with tmux, open up a tmux instance:

```bash
tmux
```

Inside the tmux instance, run arb-assist:

```bash
ulimit -n 65536
./arb-assist
```

![image](https://github.com/user-attachments/assets/532c5b19-9518-4cec-a17c-0aecc1a1d622)

To exit from the tmux instance and leave it running in the background, type:

```bash
CTRL-b
d
```

To see what tmux instances you have in the background type:

```bash
tmux ls
```

![image](https://github.com/user-attachments/assets/275f9bde-1a9f-4db1-8af5-9b16a4301dcc)

To re-attach the instance type:

```bash
tmux attach -t 0
```

Keep `arb-assist` running using `pm2`, `tmux`, or similar tools.

For more info on [tmux](https://hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/)

For more info on [pm2](https://pm2.keymetrics.io/docs/usage/quick-start/)

Start `smb-onchain` with:

```bash
pm2 start smb-onchain --watch -- run smb-config.toml
```

![image](https://github.com/user-attachments/assets/c20c0f02-9bdb-479c-818f-416fbdde311a)

Make sure to modify this command if you rename smb-config.toml to something else. You cannot name it "config.toml" because that is the config file used by arb-assist.
This watches `smb-config.toml` for changes. When `arb-assist` updates the config, `smb-onchain` restarts automatically.


To monitor program started with pm2, type:

```bash
pm2 monit
```

![image](https://github.com/user-attachments/assets/01cd6bf7-f22a-4504-bb17-f9c7cf903655)


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
## NotArb onchain-bot support

To use with NotArb onchain-bot, you should copy arb-assist, config.toml, and your license file to the onchain-bot/ folder

In the arb-assist config.toml make sure you set:

mode = "na"

You will also need to run NotArb onchain using pm2:

```bash
pm2 start ./notarb.sh --interpreter bash --name notarb-onchain --watch onchain-bot/notarb-config.toml -- onchain-bot/notarb-config.toml
```

This will generate a markets.json file formatted as a 2D array of market addresses

![image](https://github.com/user-attachments/assets/1360f0f8-ecfb-4768-8ec1-9ce80c5879c8)

It will also generate a lookup-tables.json file formatted as a 1D array of lookup table addresses

Now, arb-assist will update your notarb-config.toml with dynamic priority fees and jito tips.


## 📊 Example Output

`mints` ranked by arbitrage profitability:

![Arb Ranking](https://github.com/user-attachments/assets/379ac9f1-1029-4539-84c5-08bb77387009)

Pools chosen based on historical arb activity:

![Pool Selection](https://github.com/user-attachments/assets/861602cb-6367-463f-bbb1-577cb2d0de74)

Estimated priority fees using Helius:

![Priority Fees](https://github.com/user-attachments/assets/ff57af74-9a79-4bdb-8b5d-51df8f28945c)

## 🧠 Optimizing your Filters

To start, I recommend you filter arb programs to only copy Solana MEV bot on-chain transactions. Run arb-assist and take note of the statistics for the top 10 mints. Pay attention to the Txns, net vol/min, and ROI. You should check these numbers during times when the market is hot and when the market is cold. For your filter settings, choose numbers that will shut down the bot when the market is cold and turn on the bot when the market is hot.
