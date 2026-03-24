# FlowMind Labs — n8n Workflow Library

**Production-ready AI automation workflows built by [FlowMind Labs](https://flowmindlabs.com)**

> 21 high-value workflows across AI Ops, Call Center, Business, Content, and OpenClaw — all powered by Claude AI.

---

## 🚀 Quick Start

1. [Install n8n](https://docs.n8n.io/hosting/installation/) (self-hosted or n8n Cloud)
2. Download any workflow JSON file
3. In n8n: **Import** → paste JSON → configure credentials → activate

**Need credentials?**
- [Claude API](https://console.anthropic.com/settings/keys)
- [OpenRouter](https://openrouter.ai/keys) (alternative to Claude — 100+ models, cheaper)
- [OpenClaw](https://github.com/openclaw/openclaw) (self-hosted messaging gateway)

---

## 📦 Workflow Library

### 🤖 AI Ops / IT Operations

| # | Workflow | What it Does |
|---|----------|-------------|
| 01 | [Incident Alert Enricher](ai-ops/01-incident-alert-enricher.json) | Receives alerts from Prometheus/Grafana/PagerDuty, enriches with AI diagnosis, posts to Slack |
| 02 | [Server Health Monitor](ai-ops/02-server-health-monitor.json) | Pings servers every 5 minutes, AI diagnoses downtime, alerts team |
| 03 | [Log Anomaly Detector](ai-ops/03-log-anomaly-detector.json) | Receives log batches, Claude scans for anomalies/security issues, health score |
| 04 | [Runbook Generator](ai-ops/04-runbook-generator.json) | Given an incident type, generates complete operational runbook with commands and rollback |

### 📞 Call Center / Customer Support

| # | Workflow | What it Does |
|---|----------|-------------|
| 05 | [Support Ticket AI Triage](call-center/05-support-ticket-ai-triage.json) | Classifies tickets by priority, department, sentiment — routes to right team |
| 06 | [AI First Response Bot](call-center/06-ai-first-response-bot.json) | Sends personalized AI-crafted first response within seconds of ticket creation |
| 07 | [Call Summary Generator](call-center/07-call-summary-generator.json) | Turns call transcripts into structured notes, action items, CRM-ready entries |
| 08 | [Escalation Detector](call-center/08-escalation-detector.json) | Detects anger/churn signals in real-time, instantly alerts manager |

### 💼 Business / Sales

| # | Workflow | What it Does |
|---|----------|-------------|
| 09 | [Lead Enrichment + Scoring](business-sales/09-lead-enrichment-scoring.json) | Scores new leads 0-100 against your ICP, generates personalized outreach angle |
| 10 | [AI Meeting Notes](business-sales/10-ai-meeting-notes.json) | Turns meeting transcripts into structured notes, action items, follow-up email |
| 18 | [E-commerce Order Processing](business-sales/18-ecommerce-order-processing.json) | Shopify order webhook → Airtable inventory sync → customer Gmail confirmation |
| 19 | [RAG Company Docs Chatbot](business-sales/19-rag-company-docs-chatbot.json) | Google Drive docs → Pinecone vector index → Slack Q&A bot powered by Claude |
| 20 | [Invoice Generator & Reminder](business-sales/20-invoice-reminder.json) | Daily Stripe overdue check → email reminders → Slack escalation after 2nd reminder |

### 📱 Content / Marketing

| # | Workflow | What it Does |
|---|----------|-------------|
| 11 | [Social Post Generator](content/11-ai-social-post-generator.json) | One topic → optimized posts for LinkedIn, Twitter/X, and Instagram simultaneously |
| 12 | [Newsletter Digest Builder](content/12-newsletter-digest-builder.json) | Weekly cron: fetches RSS feeds, Claude curates top stories, builds newsletter |
| 13 | [Content Quality Gate](content/13-content-quality-gate.json) | AI scores content 0–100 across grammar, brand voice, audience fit, and CTA — HTML report by email |

### 🦞 OpenClaw Integrations

| # | Workflow | What it Does |
|---|----------|-------------|
| 13 | [Multi-Channel Support Bot](openclaw/13-openclaw-multichannel-support-bot.json) | AI support bot across Telegram, WhatsApp, Discord, Slack — powered by Claude |
| 14 | [Voice-to-Task Creator](openclaw/14-openclaw-voice-to-task.json) | WhatsApp/Telegram voice note → transcribe → extract tasks → create in task manager |
| 15 | [Cron Intelligence Report](openclaw/15-openclaw-cron-intelligence-report.json) | Daily AI briefing delivered to your phone via any OpenClaw channel |

### 📖 Guides

| # | Guide | Description |
|---|-------|-------------|
| 16 | [OpenClaw VPS Setup](guides/16-openclaw-vps-setup-guide.md) | Complete guide: Hetzner VPS → Ubuntu → OpenClaw → Nginx → SSL → systemd |
| 17 | [Automation Services](guides/automation-services.md) | What FlowMind Labs does for you — packages, INR pricing, how to get started |

---

## 🔑 Credential Setup

### Option A: Claude API (Anthropic)
Best quality, direct from Anthropic.

1. Get API key: [console.anthropic.com/settings/keys](https://console.anthropic.com/settings/keys)
2. In n8n: **Credentials** → New → **HTTP Header Auth**
3. Name: `Claude API Key`
4. Header Name: `x-api-key`
5. Header Value: `sk-ant-api03-...`
6. Also add header `anthropic-version: 2023-06-01`

### Option B: OpenRouter (Recommended for cost savings)
Access 100+ models including Claude through one API.

1. Get API key: [openrouter.ai/keys](https://openrouter.ai/keys)
2. In n8n: **Credentials** → New → **HTTP Header Auth**
3. Name: `OpenRouter API Key`
4. Header Name: `Authorization`
5. Header Value: `Bearer sk-or-v1-...`
6. In each workflow, change the HTTP node URL to: `https://openrouter.ai/api/v1/chat/completions`
7. Change model to: `anthropic/claude-opus-4-6` or `anthropic/claude-sonnet-4-6`

### Slack Webhook
For Slack notifications in most workflows:
1. Go to [api.slack.com/apps](https://api.slack.com/apps) → Create App
2. Enable **Incoming Webhooks**
3. Add to workspace → copy webhook URL
4. In n8n: Set as a workflow variable `SLACK_WEBHOOK_URL`

### SMTP (Email)
For the AI First Response Bot and Invoice Reminder:
1. In n8n: **Credentials** → New → **SMTP**
2. Use Gmail App Password, SendGrid, or Postmark

### Airtable
For E-commerce Order Processing and Invoice Reminder:
1. Get API token: [airtable.com/create/tokens](https://airtable.com/create/tokens) → Create token → scope: `data.records:write`
2. In n8n: **Credentials** → New → **Airtable Personal Access Token**
3. Paste your token
4. Update `AIRTABLE_BASE_ID` in the workflow's Config/Set node with your base ID (found in the base URL: `airtable.com/appXXXXXX/...`)

### Stripe
For Invoice Generator & Reminder:
1. Get secret key: [dashboard.stripe.com/apikeys](https://dashboard.stripe.com/apikeys)
2. In n8n: **Credentials** → New → **HTTP Header Auth**
3. Header Name: `Authorization`
4. Header Value: `Bearer sk_live_...`
5. Or set as environment variable `STRIPE_SECRET_KEY` in n8n

### Google Drive
For RAG Company Docs Chatbot:
1. In n8n: **Credentials** → New → **Google Drive OAuth2**
2. Follow the OAuth2 setup wizard — sign in with your Google account
3. Grant Drive read access
4. Set your folder ID in the Google Drive trigger node (found in the folder URL: `drive.google.com/drive/folders/FOLDER_ID`)

### Pinecone
For RAG Company Docs Chatbot:
1. Sign up at [pinecone.io](https://www.pinecone.io) → create an index
2. Index settings: **Dimensions: 1536**, **Metric: cosine**
3. Get API key from Pinecone dashboard
4. Set environment variables in n8n:
   - `PINECONE_API_KEY` — your API key
   - `PINECONE_INDEX` — your index name
   - `PINECONE_PROJECT` — your project ID
   - `PINECONE_ENV` — your environment (e.g. `us-east-1-aws`)

---

## 🦞 OpenClaw Setup

OpenClaw is a self-hosted personal AI gateway connecting 20+ messaging platforms.

**Quick install:**
```bash
npm install -g openclaw
openclaw init
openclaw start
```

Full setup guide: [guides/16-openclaw-vps-setup-guide.md](guides/16-openclaw-vps-setup-guide.md)

---

## 💰 Want These Workflows Customized?

**FlowMind Labs** offers:

| Service | Price |
|---------|-------|
| Workflow customization | From ₹2,999 |
| Full automation setup (3 workflows) | ₹7,999 |
| Monthly managed automation | From ₹9,999/month |
| White-label automation platform | From ₹19,999/month |

→ Email: contact@flowmindlabs.co
→ Website: flowmindlabs.co

---

## 🤝 Contributing

Found a bug? Have a better workflow? PRs welcome.

1. Fork this repo
2. Create your branch: `git checkout -b workflow/my-new-workflow`
3. Add your workflow JSON with sticky notes + README section
4. Submit PR

---

## 📄 License

MIT License — free to use, modify, and sell. Attribution appreciated but not required.

---

*Built with love by [FlowMind Labs](https://flowmindlabs.co) — AI automation for every team.*
