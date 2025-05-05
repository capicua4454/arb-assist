# smb-copy-public
This script is an extension for automating the config generation for SolanaMevBot On-Chain: https://docs.solanamevbot.com/home/releases
It will automatically identify mints for arbing on and filter them according to your settings.

To use the script, put "smb-copy" and your license file in the same folder as smb-onchain.
Your license file is locked to your server IP address. You can only run smb-copy on the server that has been whitelisted.

# Make sure you have Node.js and pm2 installed on your Linux server.
To install Node.js you can download it here: https://nodejs.org/en/download
or run these commands:

# Download and install nvm:
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash

# in lieu of restarting the shell
\. "$HOME/.nvm/nvm.sh"

# Download and install Node.js:
nvm install 22

# Verify the Node.js version:
node -v # Should print "v22.15.0".
nvm current # Should print "v22.15.0".

# Verify npm version:
npm -v # Should print "10.9.2".

To install pm2, you can visit: https://pm2.keymetrics.io/
or run this command:

npm install pm2 -g

You should run smb-copy continuously using pm2, tmux, or some other method of choice.
To run smb-onchain, you should use pm2 and run this command:

pm2 start smb-onchain --watch -- run smb-config.toml

This will run smb-onchain and tell it to watch the smb-config.toml. Whenever the script generates a new config for you, smb-onchain will automatically restart and load the new config.

# If you get this error when running smb-copy:

called `Result::unwrap()` on an `Err` value: Os { code: 24, kind: Uncategorized, message: "Too many open files" }

Then run this command to increase the limit:

ulimit -n 65536

# How it works
smb-copy will parse transactions and rank the mints according to arbitrage profit:
![image](https://github.com/user-attachments/assets/379ac9f1-1029-4539-84c5-08bb77387009)
In your config.toml you can set minimum threshold on "Txns", "Net Vol/min", and "ROI"
Only the mints passing your filters will be used for generating a config for smb-onchain.
If no mints pass your filters, smb-copy will generate a dummy config that will cause smb-onchain to stop running.

smb-copy will also find the most commonly used ALUTs and use them in your config.
Pools will be selected based on how much arbitrage profit has come from those pools.
![image](https://github.com/user-attachments/assets/861602cb-6367-463f-bbb1-577cb2d0de74)

Dynamic priority fees will also be estimated using the Helius API
![image](https://github.com/user-attachments/assets/ff57af74-9a79-4bdb-8b5d-51df8f28945c)

