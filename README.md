# smb-copy-public
This script is an extension for automating the config generation for SolanaMevBot On-Chain: https://docs.solanamevbot.com/home/releases
It will automatically identify mints for arbing on and filter them according to your settings.
To use the script, put "smb-copy" and your license file in the same folder as smb-onchain.
Make sure you have Node.js and pm2 installed on your Linux server.
You should run smb-copy continuously using pm2, tmux, or some other method of choice.
To run smb-onchain, you should use pm2 and run this command:

pm2 start smb-onchain --watch -- run smb-config.toml

This will run smb-onchain and tell it to watch the smb-config.toml. Whenever the script generates a new config for you, smb-onchain will automatically restart and load the new config.

