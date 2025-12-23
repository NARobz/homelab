# 04 – Traefik Prerequisites

## Purpose
Prepare Docker networking and host requirements for Traefik.

## Commands

### Create shared network
```bash
docker network create traefik
```

## Verification
```bash
docker network ls
```

Expected:
```
traefik
```

## Notes
- Network must exist before starting Traefik
- All apps attach to this network
