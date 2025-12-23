
# 🐳 Rootless Docker + UFW + Traefik Homelab Setup (Zorin / Ubuntu)

This README documents how to:
- Install **Rootless Docker**
- Secure the host using **UFW**
- Prepare Docker networking for **Traefik reverse proxy**
- Organize a **homelab-ready repository**
- Upload everything cleanly to **GitHub**

Tested on **Zorin OS (Ubuntu-based)**.

---

## 1️⃣ System Preparation

### Update system
```bash
sudo apt update && sudo apt upgrade -y
```

### Install required packages
```bash
sudo apt install -y   ca-certificates   curl   gnupg   lsb-release   uidmap   dbus-user-session   fuse-overlayfs   slirp4netns
```

---

## 2️⃣ Install Docker Engine (no daemon yet)

```bash
curl -fsSL https://get.docker.com | sh
```

⚠️ Do **not** enable or start the system Docker service.

---

## 3️⃣ Set Up Rootless Docker

### Install rootless components
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

---

## 4️⃣ Start Rootless Docker

```bash
systemctl --user start docker
systemctl --user enable docker
```

### Verify
```bash
docker info | grep Rootless
docker run hello-world
```

Expected:
```
Rootless: true
```

---

## 5️⃣ Install Docker Compose Plugin (Rootless)

```bash
mkdir -p ~/.docker/cli-plugins
curl -SL https://github.com/docker/compose/releases/download/v2.27.0/docker-compose-linux-x86_64 -o ~/.docker/cli-plugins/docker-compose
chmod +x ~/.docker/cli-plugins/docker-compose
```

Verify:
```bash
docker compose version
```

---

## 6️⃣ Configure UFW Firewall

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh
sudo ufw allow 80
sudo ufw allow 443
sudo ufw enable
```

Verify:
```bash
sudo ufw status verbose
```

---

## 7️⃣ Docker Networking for Traefik

Create shared network:
```bash
docker network create traefik
```

---

## 8️⃣ Traefik Setup

### Directory structure
```bash
mkdir -p ~/homelab/traefik/{data,logs}
cd ~/homelab
```

### traefik/docker-compose.yml
```yaml
version: "3.9"

services:
  traefik:
    image: traefik:v3.0
    container_name: traefik
    command:
      - "--providers.docker=true"
      - "--providers.docker.exposedbydefault=false"
      - "--entrypoints.web.address=:80"
      - "--entrypoints.websecure.address=:443"
      - "--api.dashboard=true"
    ports:
      - "80:80"
      - "443:443"
      - "8088:8080"
    volumes:
      - /run/user/1000/docker.sock:/var/run/docker.sock:ro
      - ./data:/data
      - ./logs:/logs
    networks:
      - traefik
    restart: unless-stopped

networks:
  traefik:
    external: true
```

---

## 9️⃣ Example Traefik App

```yaml
services:
  whoami:
    image: traefik/whoami
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.whoami.rule=Host(`whoami.localhost`)"
      - "traefik.http.services.whoami.loadbalancer.server.port=80"
    networks:
      - traefik

networks:
  traefik:
    external: true
```

---

## 📁 Recommended Repository Layout

```text
homelab/
├── traefik/
│   ├── docker-compose.yml
│   ├── data/
│   └── logs/
├── apps/
│   ├── uptime-kuma/
│   ├── n8n/
│   └── vaultwarden/
├── README.md
└── docs/
```

---

## 🔼 Uploading to GitHub

```bash
git init
git add .
git commit -m "Initial homelab setup with rootless Docker and Traefik"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/homelab.git
git push -u origin main
```

---

## ✅ Verification Checklist

- Rootless Docker enabled
- UFW only allows SSH, 80, 443
- Traefik dashboard reachable
- Containers attach to traefik network

---

## 🚀 Future Enhancements

- HTTPS with Let’s Encrypt
- Authelia / OAuth
- Backups (restic)
- Monitoring (Netdata)
- CI checks for compose files
