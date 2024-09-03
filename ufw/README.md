# UFW Configuration Guide

**UFW** (Uncomplicated Firewall) is a user-friendly tool for managing firewall rules on Linux systems, simplifying the `iptables` configuration.

## Installation

If UFW is not already installed, you can install it using:

```bash
sudo apt update
sudo apt install ufw
```

## Basic UFW Commands

###### Allow SSH Connections

`sudo ufw allow ssh`

##### Enable UFW:

`sudo ufw enable`

##### Disable UFW:

`sudo ufw disable`

##### View Available Applications:

`sudo ufw app list`

##### View UFW Status:

`sudo ufw status`

## Managing Applications and Ports

##### Allow Specific Application:

`sudo ufw allow APP_NAME`

##### Deny Specific Application:

`sudo ufw deny APP_NAME`

##### Delete Allow Rule for an Application:

`sudo ufw delete allow APP_NAME`

##### Delete Deny Rule for an Application:

`sudo ufw delete deny APP_NAME`

## Advanced UFW Commands

##### Reset UFW:

`sudo ufw reset`

##### Reload UFW:

`sudo ufw reload`

##### Logging on:

`sudo ufw logging on`

##### Logging off:

`sudo ufw logging off`

## Tips

- **Always allow SSH**: Before enabling UFW, ensure that SSH is allowed to prevent being locked out of your remote server.
- **Use specific rules**: When creating rules, specify the protocol (TCP/UDP) and ports to avoid overly broad permissions.
- **Backup configurations**: Consider backing up your UFW configuration before making major changes.
