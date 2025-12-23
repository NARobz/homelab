# 02 – UFW Firewall Setup

## Purpose
Secure the host by allowing only required inbound traffic.

## Prerequisites
- Rootless Docker installed
- SSH access confirmed

## Commands

### Default rules
```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

### Allow SSH
```bash
sudo ufw allow ssh
```

### Allow HTTP / HTTPS (Traefik)
```bash
sudo ufw allow 80
sudo ufw allow 443
```

### Enable firewall
```bash
sudo ufw enable
```

## Verification
```bash
sudo ufw status verbose
```

## Notes
- Adjust SSH port if non-standard
- Docker rootless does not modify iptables
