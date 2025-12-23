# 01 – Rootless Docker Setup

## Purpose
Install Docker Engine in **rootless mode** for improved security and portability across laptops, desktops, and servers.

## Prerequisites
- Ubuntu / Zorin OS
- User with sudo access
- Internet connectivity

## Commands

### System update
```bash
sudo apt update && sudo apt upgrade -y
```

### Required packages
```bash
sudo apt install -y \
  ca-certificates \
  curl \
  gnupg \
  lsb-release \
  uidmap \
  dbus-user-session \
  fuse-overlayfs \
  slirp4netns
```

### Install Docker Engine
```bash
curl -fsSL https://get.docker.com | sh
```

⚠️ Do NOT enable or start the system Docker service.

### Enable rootless Docker
```bash
dockerd-rootless-setuptool.sh install
```

### Enable lingering (required for background services)
```bash
sudo loginctl enable-linger $USER
```

### Configure environment
```bash
echo 'export PATH=$HOME/bin:$PATH' >> ~/.bashrc
echo 'export DOCKER_HOST=unix:///run/user/1000/docker.sock' >> ~/.bashrc
source ~/.bashrc
```

### Start rootless Docker
```bash
systemctl --user start docker
systemctl --user enable docker
```

## Verification
```bash
docker info | grep Rootless
docker run hello-world
```

Expected output:
```
Rootless: true
```

## Notes
- Rootless Docker avoids running containers as root
- Required for safer homelab exposure
