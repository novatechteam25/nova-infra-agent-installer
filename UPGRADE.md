# Nova Infrastructure Agent - Self Upgrade

## Overview

The Nova Infrastructure Agent now supports self-upgrade functionality with automatic version detection, safe installation, and rollback capabilities.

## Features

### 1. Version Detection

- Checks current agent version using `agent --version`
- Fetches latest version from `https://s3-jkt-1.fromnovatech.xyz/infra/version`
- Compares versions to determine if upgrade is needed

### 2. Safe Installation

- Creates backup of current agent before upgrade
- Downloads new agent to temporary location
- Verifies new agent version before installation
- Replaces current agent only after verification

### 3. Automatic Restart

- Stops current agent service
- Installs new agent binary
- Restarts agent service
- Verifies service is running correctly

### 4. Rollback Support

- Automatically rolls back to previous version if upgrade fails
- Maintains backup copies of previous agent versions
- Restores service to working state

## Usage

### Manual Upgrade

```bash
# Run upgrade manually
sudo /usr/local/bin/agent-upgrade

# Check upgrade log
tail -f /var/log/agent-upgrade.log
```

### Automatic Upgrade

- Automatically runs daily at 2:00 AM via cron job
- Logs all upgrade activity to `/var/log/agent-upgrade.log`
- No manual intervention required

### Check Current Version

```bash
/usr/local/bin/agent --version
```

## Installation

The self-upgrade functionality is available as a separate script. To install it:

```bash
# First, run the basic installer
./installer.sh

# Then, manually install the upgrade script
sudo cp upgrade.sh /usr/local/bin/agent-upgrade
sudo chmod +x /usr/local/bin/agent-upgrade

# (Optional) Set up automatic upgrades via cron
(crontab -l 2>/dev/null; echo "0 2 * * * /usr/local/bin/agent-upgrade >> /var/log/agent-upgrade.log 2>&1") | crontab -
```

The basic installer (`./installer.sh`) will:

1. Install the agent binary
2. Set up the systemd service

The upgrade functionality must be installed separately using the steps above.

## Server Requirements

For the upgrade functionality to work, the server must provide:

1. **Version endpoint**: `https://s3-jkt-1.fromnovatech.xyz/infra/version`

   - Returns the latest version number (e.g., "Nova Infrastructure Agent version v1.0.0-alpha")

2. **Binary endpoint**: `https://s3-jkt-1.fromnovatech.xyz/infra/agent`
   - Provides the latest agent binary

## Upgrade Process Flow

1. **Check versions**: Compare current vs latest
2. **Backup current**: Save current agent to backup directory
3. **Download new**: Fetch latest agent binary
4. **Verify**: Confirm new agent version matches expected
5. **Install**: Replace current agent with new version
6. **Restart**: Restart the agent service
7. **Verify**: Confirm service is running correctly
8. **Rollback**: If any step fails, restore from backup

## Logs and Monitoring

- Upgrade logs: `/var/log/agent-upgrade.log`
- Service logs: `journalctl -u nova-infra-agent`
- Backup location: `/tmp/agent-backup/`

## Troubleshooting

### Upgrade Fails

- Check `/var/log/agent-upgrade.log` for error details
- Verify network connectivity to upgrade server
- Ensure sufficient disk space for backup and new binary

### Service Won't Start After Upgrade

- Automatic rollback should occur
- If manual intervention needed: `sudo systemctl restart nova-infra-agent`
- Check service logs: `journalctl -u nova-infra-agent -e`

### Manual Rollback

```bash
# List available backups
ls -la /tmp/agent-backup/

# Restore specific backup
sudo cp /tmp/agent-backup/agent.backup.YYYYMMDD_HHMMSS /usr/local/bin/agent
sudo systemctl restart nova-infra-agent
```
