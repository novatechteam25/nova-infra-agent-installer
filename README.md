# Nova Infra Agent Installer Guide

This guide explains how to install and run the Nova Infra Agent as a systemd service using the provided installer script.

## Prerequisites

- Linux system with `curl` and `systemd`
- `sudo` privileges

## Installation Steps

1. **Download and extract the installer package**

   Download the archive and extract its contents:

   ```bash
   mkdir agent-installer
   cd agent-installer

   wget https://s3-jkt-1.fromnovatech.xyz/infra/nova-infra-agent-installer.tar.gz?nocache=12345

   # Note: Replace the URL with the actual download link if it changes
   # If the URL has a cache-busting query parameter, you can rename it after download

   # Example:
   mv 'nova-infra-agent-installer.tar.gz?nocache=12345' nova-infra-agent-installer.tar.gz
   
   tar xzvf nova-infra-agent-installer.tar.gz
   chmod +x installer.sh
   ```

2. **Run the installer**

   ```bash
   sudo bash installer.sh
   ```

   The script will:

   - Download the agent binary to `/usr/local/bin/agent`
   - Install the systemd service file to `/etc/systemd/system/nova-infra-agent.service`
   - Reload systemd, enable, and start the service

3. **Check service status and logs**

   ```bash
   sudo systemctl status nova-infra-agent
   sudo tail -f /var/log/nova-infra-agent.log
   ```

## Customizing the Service

Edit `nova-infra-agent.service` before running the installer to adjust the agent arguments (such as `-server`, `-agent-id`, etc.) as needed for your environment.

## Uninstall

To stop and remove the service and binary:

```bash
sudo systemctl stop nova-infra-agent
sudo systemctl disable nova-infra-agent
sudo rm /etc/systemd/system/nova-infra-agent.service
sudo rm /usr/local/bin/agent
sudo systemctl daemon-reload
```
