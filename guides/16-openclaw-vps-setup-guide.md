# OpenClaw VPS Setup Guide

**By FlowMind Labs** — Get OpenClaw running securely on a VPS in under 30 minutes.

---

## What is OpenClaw?

OpenClaw is a self-hosted personal AI assistant gateway that connects to 20+ messaging platforms (Telegram, WhatsApp, Discord, Slack, Teams, SMS, and more). You run it on your own VPS — your data never leaves your server.

**GitHub:** https://github.com/openclaw/openclaw

> **v2026.3.22 Breaking Changes:** If upgrading from an older version, see the [Breaking Changes](#breaking-changes-v20263.22) section before proceeding.

---

## Recommended VPS

| Provider | Plan | Cost | Why |
|----------|------|------|-----|
| **Hostinger** | KVM 2 (2 vCPU, 8GB RAM) | ~₹649/month | Best for Indian teams, hPanel Docker Manager built-in |
| DigitalOcean | Basic (2GB RAM) | ~₹1,020/month | Simple, good docs |
| AWS Lightsail | $3.50/month | ~₹300/month | AWS ecosystem |

**Recommended OS:** Ubuntu 22.04 LTS

---

## Option A — Hostinger VPS (Recommended for Indian Teams)

Hostinger offers two ways to install OpenClaw on their VPS. **Docker Manager method is recommended.**

### Method 1 — Pre-installed at Checkout

1. Go to [hostinger.com/vps-hosting](https://www.hostinger.com/vps-hosting)
2. Select your VPS plan
3. During checkout, look for **"Pre-installed applications"**
4. Select **OpenClaw** from the list
5. Complete checkout — OpenClaw will be ready when VPS provisions

Access your instance from **hPanel → VPS Overview** button.

---

### Method 2 — Docker Manager in hPanel (Recommended)

If you already have a Hostinger VPS or skipped pre-install:

```bash
# 1. Log in to hPanel → VPS → your server → Docker Manager
# 2. Click "Add Container"
# 3. Set image: openclaw/openclaw:latest
# 4. Set the following environment variables:
```

**Required environment variables:**

| Variable | Value |
|----------|-------|
| `OPENCLAW_GATEWAY_TOKEN` | Your secret gateway token (generate a random string) |
| `WHATSAPP_NUMBER` | Your WhatsApp number in international format (e.g. `+919876543210`) |
| `TELEGRAM_BOT_TOKEN` | Token from @BotFather |
| `ANTHROPIC_API_KEY` | Your Claude API key |
| `OPENCLAW_WEBHOOK_URL` | `http://your-n8n-ip:5678/webhook/openclaw-message` |

```bash
# 5. Set port mapping: 18789:18789
# 6. Set restart policy: Always
# 7. Click "Deploy"
```

Access OpenClaw at: `http://your-vps-ip:18789`

---

### Updating OpenClaw on Hostinger

```bash
# Via Docker Manager in hPanel:
# 1. Go to your container → "Update Image"
# 2. Pull latest: openclaw/openclaw:latest
# 3. Recreate container (settings are preserved)
```

---

## Option B — Manual Install on Any VPS

Use this if you are not on Hostinger or prefer full control.

### Step 1 — Initial Server Setup

```bash
# SSH into your server
ssh root@YOUR_SERVER_IP

# Update system
apt update && apt upgrade -y

# Create non-root user (security best practice)
adduser openclaw
usermod -aG sudo openclaw
su - openclaw

# Enable linger (keeps services running after logout)
loginctl enable-linger $USER
```

### Step 2 — Install OpenClaw

> **Important (v2026.3.22+):** Use the official install script instead of `npm install -g openclaw` — the npm install path has a known WhatsApp sidecar regression.

```bash
# Install via official script
curl -fsSL https://get.openclaw.io/install.sh | bash

# Verify installation
openclaw --version

# State directory is now ~/.openclaw (was .moltbot in older versions)
mkdir -p ~/.openclaw
```

### Step 3 — Configure OpenClaw

```bash
# Initialize config
openclaw init

# Edit config
nano ~/.openclaw/config.json
```

Example config:
```json
{
  "port": 18789,
  "host": "127.0.0.1",
  "channels": {
    "telegram": {
      "enabled": true,
      "token": "YOUR_TELEGRAM_BOT_TOKEN"
    },
    "whatsapp": {
      "enabled": true,
      "number": "YOUR_WHATSAPP_NUMBER"
    }
  },
  "webhook_output": "http://localhost:5678/webhook/openclaw-message",
  "ai_backend": "none"
}
```

> **v2026.3.22+:** Environment variables now use `OPENCLAW_*` prefix (e.g. `OPENCLAW_GATEWAY_TOKEN`, `OPENCLAW_WEBHOOK_URL`). The old `CLAWDBOT_*` prefix is no longer supported.

**Get a Telegram Bot Token:**
1. Message @BotFather on Telegram
2. Send `/newbot`
3. Follow prompts — copy the token

### Step 4 — Run as systemd Service

```bash
sudo nano /etc/systemd/system/openclaw.service
```

```ini
[Unit]
Description=OpenClaw AI Assistant Gateway
After=network.target

[Service]
Type=simple
User=openclaw
WorkingDirectory=/home/openclaw/.openclaw
ExecStart=/usr/local/bin/openclaw start
Restart=on-failure
RestartSec=10
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=openclaw
Environment=NODE_ENV=production
Environment=OPENCLAW_GATEWAY_TOKEN=your-secret-token

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable openclaw
sudo systemctl start openclaw
sudo systemctl status openclaw
```

### Step 5 — Nginx Reverse Proxy

```bash
sudo apt install nginx -y
sudo nano /etc/nginx/sites-available/openclaw
```

```nginx
server {
    listen 80;
    server_name YOUR_DOMAIN_OR_IP;

    location / {
        proxy_pass http://127.0.0.1:18789;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;

        add_header X-Frame-Options DENY;
        add_header X-Content-Type-Options nosniff;
    }
}
```

```bash
sudo ln -s /etc/nginx/sites-available/openclaw /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

### Step 6 — SSL with Certbot

```bash
sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx -d your-domain.com
sudo certbot renew --dry-run
```

### Step 7 — Firewall

```bash
sudo ufw allow OpenSSH
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw deny 18789
sudo ufw enable
sudo ufw status
```

---

## Option C — Emergent.sh (Managed Cloud)

[Emergent.sh](https://emergent.sh) offers a managed OpenClaw / MoltBot deployment — no VPS management needed.

- No server setup required
- Runs OpenClaw in a managed container
- Good for testing before committing to a VPS

Visit [emergent.sh/tutorial/moltbot-on-emergent](https://emergent.sh/tutorial/moltbot-on-emergent) for their official setup tutorial.

---

## Connect to n8n

In OpenClaw config (or env var `OPENCLAW_WEBHOOK_URL`), set your n8n webhook:

```json
"webhook_output": "http://127.0.0.1:5678/webhook/openclaw-message"
```

If n8n is on the same machine:
```bash
sudo npm install -g n8n
n8n start
# Access at: http://YOUR_IP:5678
```

---

## Test Your Setup

```bash
# Check OpenClaw is running
curl http://127.0.0.1:18789/health

# View logs
sudo journalctl -u openclaw -f

# Send a message to your Telegram bot — it should trigger the n8n webhook
```

---

## Breaking Changes — v2026.3.22

If upgrading from a version older than March 2026:

| Old | New |
|-----|-----|
| `CLAWDBOT_*` env vars | `OPENCLAW_*` env vars |
| `~/.moltbot` state directory | `~/.openclaw` state directory |
| `npm install -g openclaw` | Use official install script (WhatsApp sidecar regression in npm path) |
| Plugin SDK at `~/.moltbot/plugins` | Now at `~/.openclaw/plugins` |

**Migration steps:**
```bash
# 1. Stop the service
sudo systemctl stop openclaw

# 2. Copy state directory
cp -r ~/.moltbot ~/.openclaw

# 3. Update env vars in systemd service file (CLAWDBOT_ → OPENCLAW_)
sudo nano /etc/systemd/system/openclaw.service

# 4. Reinstall via script
curl -fsSL https://get.openclaw.io/install.sh | bash

# 5. Restart
sudo systemctl daemon-reload
sudo systemctl start openclaw
```

---

## Security Checklist

- [ ] Non-root user running OpenClaw
- [ ] SSH key authentication (disable password auth)
- [ ] UFW firewall enabled, port 18789 blocked externally
- [ ] Nginx reverse proxy with SSL
- [ ] Secrets in environment variables, not config files
- [ ] `loginctl enable-linger` enabled for your user
- [ ] Automatic security updates: `sudo apt install unattended-upgrades`
- [ ] Fail2ban installed: `sudo apt install fail2ban`

---

## Monthly Cost Estimate

| Item | Cost |
|------|------|
| Hostinger KVM 2 VPS | ~₹649/month |
| Domain (optional) | ~₹850/year |
| SSL (Certbot) | Free |
| **Total** | **~₹649–₹720/month** |

---

## Troubleshooting

**OpenClaw won't start:**
```bash
sudo journalctl -u openclaw -n 50
sudo lsof -i :18789
```

**Telegram bot not responding:**
- Verify `TELEGRAM_BOT_TOKEN` in config/env vars
- Check n8n webhook URL is correct and n8n is running: `curl http://localhost:5678/healthz`

**WhatsApp not connecting:**
- Use the official install script (not npm) — known npm sidecar regression in v2026.3.22+
- Verify `WHATSAPP_NUMBER` is in full international format

**SSL certificate issues:**
```bash
sudo certbot certificates
sudo certbot renew --force-renewal
```

---

## References

- [Hostinger — How to Install OpenClaw on VPS](https://www.hostinger.com/support/how-to-install-openclaw-on-hostinger-vps/)
- [OpenClaw GitHub](https://github.com/openclaw/openclaw)
- [Emergent.sh MoltBot Tutorial](https://emergent.sh/tutorial/moltbot-on-emergent)

---

*Built by FlowMind Labs — flowmindlabs.co | contact.flowmindlabs@gmail.com*
