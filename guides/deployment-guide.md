# FlowMind Labs — Deployment Guide

**Setting up Hostinger VPS + n8n + WhatsApp Agents from scratch**

---

## 1. VPS Setup (Hostinger)

**Recommended plan:** KVM 2 (2 vCPU, 8GB RAM, 100GB SSD) — handles up to 10 active client workflows

### 1.1 Purchase and access
1. Buy VPS at [hostinger.com/vps-hosting](https://www.hostinger.com/vps-hosting) — select Ubuntu 22.04 LTS
2. Set root password during purchase
3. SSH in: `ssh root@YOUR_VPS_IP`

### 1.2 Initial server hardening
```bash
# Update system
apt update && apt upgrade -y

# Create non-root user
adduser flowmind
usermod -aG sudo flowmind

# Disable root SSH login
sed -i 's/PermitRootLogin yes/PermitRootLogin no/' /etc/ssh/sshd_config
systemctl restart sshd

# Basic firewall
ufw allow 22
ufw allow 80
ufw allow 443
ufw allow 5678   # n8n
ufw enable
```

### 1.3 Install Node.js and n8n
```bash
# Install Node.js 20
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
apt install -y nodejs

# Install n8n globally
npm install -g n8n

# Verify
n8n --version
```

### 1.4 Configure n8n as systemd service
```bash
# Create service file
cat > /etc/systemd/system/n8n.service << 'EOF'
[Unit]
Description=n8n Automation Platform
After=network.target

[Service]
Type=simple
User=flowmind
WorkingDirectory=/home/flowmind
Environment=N8N_HOST=0.0.0.0
Environment=N8N_PORT=5678
Environment=N8N_PROTOCOL=https
Environment=WEBHOOK_URL=https://YOUR_DOMAIN
Environment=N8N_ENCRYPTION_KEY=GENERATE_RANDOM_32_CHAR_KEY
Environment=DB_TYPE=postgresdb
Environment=DB_POSTGRESDB_HOST=localhost
Environment=DB_POSTGRESDB_PORT=5432
Environment=DB_POSTGRESDB_DATABASE=n8n
Environment=DB_POSTGRESDB_USER=n8n
Environment=DB_POSTGRESDB_PASSWORD=STRONG_PASSWORD
ExecStart=/usr/bin/n8n start
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload
systemctl enable n8n
systemctl start n8n
```

### 1.5 Install PostgreSQL (recommended for production)
```bash
apt install -y postgresql postgresql-contrib
sudo -u postgres psql -c "CREATE USER n8n WITH PASSWORD 'STRONG_PASSWORD';"
sudo -u postgres psql -c "CREATE DATABASE n8n OWNER n8n;"
```

### 1.6 Nginx reverse proxy + SSL
```bash
apt install -y nginx certbot python3-certbot-nginx

# Create nginx config
cat > /etc/nginx/sites-available/n8n << 'EOF'
server {
    server_name YOUR_DOMAIN;
    location / {
        proxy_pass http://localhost:5678;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_cache_bypass $http_upgrade;
    }
}
EOF

ln -s /etc/nginx/sites-available/n8n /etc/nginx/sites-enabled/
nginx -t && systemctl restart nginx

# Get SSL certificate
certbot --nginx -d YOUR_DOMAIN
```

---

## 2. Deploying a Client Workflow

### 2.1 Pre-deployment checklist
- [ ] Client has dedicated WhatsApp Business number (not personal)
- [ ] BSP account created (Chakra Chat / AiSensy / Interakt / Wati)
- [ ] BSP API key obtained
- [ ] Client Security Agreement signed
- [ ] Business info and FAQ content received from client

### 2.2 Import workflow
1. Download workflow JSON from this repo
2. In n8n: **Workflows** → **Import from File** → select JSON
3. Open the `BUSINESS_CONFIG` / `FAQ_CONFIG` / `LEAD_CONFIG` Set node
4. Fill in all client-specific values

### 2.3 Configure credentials
In n8n **Credentials** → add:
- BSP API key (HTTP Header Auth)
- Claude API key or OpenRouter key (HTTP Header Auth)
- Airtable Personal Access Token (if needed)
- SMTP credentials (if email notifications needed)

### 2.4 Configure webhook
1. In workflow, click the **Webhook** trigger node — copy the webhook URL
2. Go to BSP dashboard → Settings → Webhooks
3. Paste n8n webhook URL
4. Send a test message — check n8n Executions tab for incoming data

### 2.5 Activate and test
1. Toggle workflow **Active = ON**
2. Send test messages covering: normal inquiry, booking/lead request, escalation trigger
3. Verify Airtable records created correctly
4. Verify WhatsApp responses look correct
5. Confirm with client before handing over

---

## 3. Per-Client Isolation

Each client gets:
- Separate workflow (imported fresh, not shared)
- Separate n8n credentials (never shared between clients)
- Separate Airtable base
- Separate BSP account (client owns their number)

Never share credentials or workflows between clients.

---

## 4. Backups

```bash
# Export all workflows (run monthly or before major changes)
n8n export:workflow --all --output=/home/flowmind/backups/workflows-$(date +%Y%m%d).json

# Backup PostgreSQL database
pg_dump n8n > /home/flowmind/backups/n8n-db-$(date +%Y%m%d).sql
```

Set up automated daily backups to Hostinger Object Storage or S3.

---

## 5. Monitoring

- **n8n health check:** `curl http://localhost:5678/healthz`
- **Service status:** `systemctl status n8n`
- **Logs:** `journalctl -u n8n -f`
- **Execution failures:** n8n UI → Executions tab → filter by "Error"

For critical client workflows: set up the **Escalation Detector** workflow (Workflow 08) to alert you via Slack when executions fail.

---

*FlowMind Labs | hello@flowmindlabs.co | flowmindlabs.co*
