# OpenClaw VPS Setup Guide

**By FlowMind Labs** — Get OpenClaw running securely on a VPS in under 30 minutes.

---

## What is OpenClaw?

OpenClaw is a self-hosted personal AI assistant gateway that connects to 20+ messaging platforms (Telegram, WhatsApp, Discord, Slack, Teams, SMS, and more). You run it on your own VPS — your data never leaves your server.

**GitHub:** https://github.com/openclaw/openclaw

---

## Recommended VPS

| Provider | Plan | Cost | Why |
|----------|------|------|-----|
| **Hetzner** | CX21 (2 vCPU, 4GB RAM) | ~€4/month | Best price/performance in EU/India |
| DigitalOcean | Basic (2GB RAM) | $12/month | Simple, good docs |
| Contabo | VPS S | ~€5/month | Cheap EU option |
| AWS Lightsail | $3.50/month | $3.50/month | AWS ecosystem |

**Recommended OS:** Ubuntu 22.04 LTS

---

## Step 1 — Create Your VPS

### Hetzner (Recommended)
```bash
# 1. Sign up at hetzner.com/cloud
# 2. Create project → Add Server
# 3. Choose: Ubuntu 22.04, CX21 (4GB), Nuremberg datacenter
# 4. Add your SSH key
# 5. Note the server IP address
```

---

## Step 2 — Initial Server Setup

```bash
# SSH into your server
ssh root@YOUR_SERVER_IP

# Update system
apt update && apt upgrade -y

# Create non-root user (security best practice)
adduser openclaw
usermod -aG sudo openclaw
su - openclaw

# Install Node.js 20+ (required by OpenClaw)
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs

# Verify
node --version  # Should show v20.x+
npm --version
```

---

## Step 3 — Install OpenClaw

```bash
# Install OpenClaw globally
sudo npm install -g openclaw

# Verify installation
openclaw --version

# Create config directory
mkdir -p ~/.openclaw
cd ~/.openclaw
```

---

## Step 4 — Configure OpenClaw

```bash
# Initialize config
openclaw init

# This creates ~/.openclaw/config.json
# Edit it:
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
    "slack": {
      "enabled": false,
      "token": "YOUR_SLACK_BOT_TOKEN"
    }
  },
  "webhook_output": "http://localhost:5678/webhook/openclaw-message",
  "ai_backend": "none"
}
```

**Get a Telegram Bot Token:**
1. Message @BotFather on Telegram
2. Send `/newbot`
3. Follow prompts — copy the token

---

## Step 5 — Run as systemd Service (Auto-restart)

```bash
# Create systemd service
sudo nano /etc/systemd/system/openclaw.service
```

Paste this content:
```ini
[Unit]
Description=OpenClaw AI Assistant Gateway
After=network.target

[Service]
Type=simple
User=openclaw
WorkingDirectory=/home/openclaw/.openclaw
ExecStart=/usr/bin/openclaw start
Restart=on-failure
RestartSec=10
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=openclaw
Environment=NODE_ENV=production

[Install]
WantedBy=multi-user.target
```

```bash
# Enable and start
sudo systemctl daemon-reload
sudo systemctl enable openclaw
sudo systemctl start openclaw

# Check status
sudo systemctl status openclaw

# View logs
sudo journalctl -u openclaw -f
```

---

## Step 6 — Secure with Nginx Reverse Proxy

```bash
# Install Nginx
sudo apt install nginx -y

# Create config
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

        # Security headers
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

---

## Step 7 — Add SSL (HTTPS) with Certbot

```bash
# Install Certbot
sudo apt install certbot python3-certbot-nginx -y

# Get certificate (replace with your domain)
sudo certbot --nginx -d your-domain.com

# Auto-renewal test
sudo certbot renew --dry-run
```

---

## Step 8 — Configure Firewall

```bash
# Allow only necessary ports
sudo ufw allow OpenSSH
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw deny 18789  # Block direct access to OpenClaw port
sudo ufw enable

# Verify
sudo ufw status
```

---

## Step 9 — Connect to n8n

If you're running n8n on the same VPS:

```bash
# Install n8n
sudo npm install -g n8n

# Run n8n (or set up as systemd service)
n8n start

# n8n runs on port 5678 by default
# Access at: http://YOUR_IP:5678
```

In OpenClaw config, set:
```json
"webhook_output": "http://127.0.0.1:5678/webhook/openclaw-message"
```

---

## Step 10 — Test Everything

```bash
# Check OpenClaw is running
curl http://127.0.0.1:18789/health

# Send test message via Telegram
# (Message your bot, it should trigger n8n webhook)

# View OpenClaw logs
sudo journalctl -u openclaw -f
```

---

## Security Checklist

- [ ] Non-root user running OpenClaw
- [ ] SSH key authentication (disable password auth)
- [ ] UFW firewall enabled, port 18789 blocked externally
- [ ] Nginx reverse proxy with SSL
- [ ] Secrets in environment variables, not config files
- [ ] Automatic security updates enabled: `sudo apt install unattended-upgrades`
- [ ] Fail2ban installed: `sudo apt install fail2ban`

---

## Monthly Cost Estimate

| Item | Cost |
|------|------|
| Hetzner CX21 VPS | €4/month |
| Domain (optional) | €1/month |
| SSL (Certbot) | Free |
| **Total** | **~€5/month** |

---

## Troubleshooting

**OpenClaw won't start:**
```bash
sudo journalctl -u openclaw -n 50
# Check for port conflicts
sudo lsof -i :18789
```

**Telegram bot not responding:**
- Verify bot token in config
- Check n8n webhook URL is correct
- Ensure n8n is running: `curl http://localhost:5678/healthz`

**SSL certificate issues:**
```bash
sudo certbot certificates
sudo certbot renew --force-renewal
```

---

*Built by FlowMind Labs — flowmindlabs.co | contact@flowmindlabs.co*
