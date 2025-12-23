# 03 – GitHub SSH Authentication

## Purpose
Enable passwordless, secure GitHub access for infrastructure repositories.

## Commands

### Generate SSH key
```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

Accept defaults or specify a filename.

### Start SSH agent
```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/your_key_name
```

### Copy public key
```bash
cat ~/.ssh/your_key_name.pub
```

## GitHub Web Steps
1. Go to https://github.com/settings/keys
2. New SSH key
3. Paste public key
4. Save

### Configure Git remote
```bash
git remote set-url origin git@github.com:USERNAME/REPO.git
```

## Verification
```bash
ssh -T git@github.com
```

Expected:
```
You've successfully authenticated
```

## Notes
- Never use sudo with ssh-add
- Keys must reside in ~/.ssh
