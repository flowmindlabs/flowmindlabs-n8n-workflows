# FlowMind Labs — Deployment Guide

**Hostinger VPS + n8n + WhatsApp Agents setup**

---

## 1. VPS Setup (Hostinger)

**Recommended:** KVM 2 (2 vCPU, 8GB RAM) — handles 10+ client workflows.

### 1.1 Initial server setup
```bash
apt update && apt upgrade -y
adduser flowmind && usermod -aG sudo flowmind
ufw allow 22,80,443,5678/tcp && ufw enable
```

### 1.2 Install Node.js + n8n
```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
apt install -y nodejs
npm install -g n8n
```

### 1.3 n8n as systemd service
```bash
cat > /etc/systemd/system/n8n.service << 'EOF'
[Unit]
Description=n8n
After=network.target
[Service]
Type=simple
User=flowmind
Environment=N8N_HOST=0.0.0.0
Environment=N8N_PORT=5678
Environment=WEBHOOK_URL=https://YOUR_DOMAIN
Environment=N8N_ENCRYPTION_KEY=REPLACE_WITH_32_CHAR_RANDOM_KEY
ExecStart=/usr/bin/n8n start
Restart=always
[Install]
WantedBy=multi-user.target
EOF
systemctl daemon-reload && systemctl enable n8n && systemctl start n8n
```

### 1.4 Nginx + SSL
```bash
apt install -y nginx certbot python3-certbot-nginx
cat > /etc/nginx/sites-available/n8n << 'EOF'
server {
    server_name YOUR_DOMAIN;
    location / {
        proxy_pass http://localhost:5678;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
    }
}
EOF
ln -s /etc/nginx/sites-available/n8n /etc/nginx/sites-enabled/
nginx -t && systemctl restart nginx
certbot --nginx -d YOUR_DOMAIN
```

---

## 2. Deploying a Client Workflow

### Pre-deployment checklist
- [ ] Client has dedicated WhatsApp Business number (not personal)
- [ ] BSP account created + API key obtained
- [ ] Client Security Agreement signed
- [ ] Business info / FAQ content received from client

### Steps
1. Import JSON from this repo into n8n: **Workflows → Import from File**
2. Open `BUSINESS_CONFIG` / `FAQ_CONFIG` / `LEAD_CONFIG` node → fill in client details
3. Add credentials in n8n: BSP API key, Claude/OpenRouter key, Airtable token
4. Copy webhook URL → paste into BSP dashboard → Webhooks
5. Toggle **Active = ON**
6. Test with sample messages — check n8n Executions tab

---

## 3. Client Isolation Rules

- Each client = separate workflow + separate credentials + separate Airtable base
- Never share credentials or workflows between clients
- Client owns their BSP account and WhatsApp number

---

## 4. Backups

```bash
# Monthly workflow export
n8n export:workflow --all --output=/home/flowmind/backups/workflows-$(date +%Y%m%d).json
```

---

## 5. Monitoring

```bash
systemctl status n8n          # Service status
journalctl -u n8n -f          # Live logs
curl http://localhost:5678/healthz  # Health check
```

---

*FlowMind Labs | hello@flowmindlabs.co | flowmindlabs.co*
