# Updating arb-assist

## Quick Update

```bash
# 1. Stop arb-assist
killall arb-assist

# 2. Backup current version (optional but recommended)
cp arb-assist arb-assist.backup

# 3. Download new version
wget -O arb-assist https://github.com/capicua4454/arb-assist/raw/refs/heads/main/arb-assist

# 4. Make executable
chmod +x arb-assist

# 5. Start again
./arb-assist -c config.toml
```

## Important Notes

- Your config.toml and license.json files are never overwritten
- The download URL always points to the latest stable version
- Check [Discord](https://discord.gg/ADtnjdy5m5) for update announcements

## Rollback if Needed

```bash
# Stop current version
killall arb-assist

# Restore backup
mv arb-assist.backup arb-assist

# Start old version
./arb-assist -c config.toml
```

## Troubleshooting

If the update fails to start:
```bash
# Check executable permissions
chmod +x arb-assist

# Check license file exists
ls -la license.json

# Test manually to see error messages
./arb-assist -c config.toml
```