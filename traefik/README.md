# Traefik Reverse Proxy

## Purpose
Traefik acts as the **single entry point** for all homelab services.
It routes HTTP/HTTPS traffic to containers using Docker labels.

This setup is designed for:
- Rootless Docker
- Local testing (localhost / internal DNS)
- Later expansion to HTTPS with Let's Encrypt

---

## Prerequisites
- Rootless Docker installed and running
- `traefik` Docker network created
- Ports 80 and 443 allowed in UFW

---

## Files
- `docker-compose.yml` – Traefik service definition
- `.env.example` – Environment variables template
- `data/` – Persistent Traefik state
- `logs/` – Access and error logs

---

## Setup

### 1. Copy environment file
```bash
cp .env.example .env
```

Edit `.env` if needed.

### 2. Start Traefik
```bash
docker compose up -d
```

---

## Access

| Service | URL |
|------|----|
| Traefik Dashboard | http://localhost:8088 |

---

## Notes
- Dashboard is unsecured by default (local testing only)
- Do not expose dashboard publicly without auth
- All apps must join the external `traefik` network
