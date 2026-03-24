# Turning Automations Into a SaaS — The Complete Guide

**By FlowMind Labs** — How to package your n8n workflows as a recurring revenue business.

---

## The Core Concept

You've built powerful automation workflows. Instead of using them only for yourself, you can:
1. **Sell them as templates** (one-time revenue)
2. **Offer them as a managed service** (recurring revenue)
3. **White-label them for businesses** (enterprise revenue)
4. **Build a workflow marketplace** (platform revenue)

The difference between a freelancer and a SaaS company is **recurring revenue at scale**.

---

## The 4 Business Models

### Model 1: Workflow Template Marketplace
**What:** Sell downloadable n8n workflow JSON files
**Revenue:** ₹2,499–₹24,999 per workflow (one-time)
**Effort:** Low
**Scale:** High (same workflow, unlimited buyers)

**How to start:**
- List on Gumroad, Lemon Squeezy, or your own website
- Create a simple landing page per workflow
- Add video demo + setup guide
- Price based on value delivered (e.g., "saves 5 hours/week = worth ₹8,499")

**Target:** Developers, startup founders, operations teams

---

### Model 2: Automation Retainer Agency
**What:** You run and maintain workflows for clients monthly
**Revenue:** ₹24,999–₹2,49,999/client/month
**Effort:** Medium
**Scale:** Medium (limited by your capacity)

**Service tiers:**
| Tier | Price/month | What's included |
|------|-------------|-----------------|
| Starter | ₹4,999 | 3 pre-built workflows, Slack support |
| Growth | ₹9,999 | 8 workflows, custom builds, priority support |
| Scale | ₹19,999 | Unlimited workflows, dedicated setup, SLA |

**How to get first clients:**
1. Offer free 2-week trial with one workflow
2. Show the ROI: "I'll save you 10 hours/week — at ₹500/hour that's ₹20,000/month value for ₹4,999"
3. Target businesses already using Zapier/Make.com — show them n8n is 10x cheaper and more powerful

---

### Model 3: White-Label Platform
**What:** Deploy n8n + your workflows as a branded automation tool for a business
**Revenue:** ₹84,999–₹4,24,999/month per client
**Effort:** High (setup), Low (maintenance)
**Scale:** High once systematized

**Setup for white-label:**
```bash
# Deploy n8n with custom branding
docker run -d \
  -p 5678:5678 \
  -e N8N_BASIC_AUTH_USER=admin \
  -e N8N_BASIC_AUTH_PASSWORD=yourpassword \
  -e N8N_HOST=automation.clientdomain.com \
  -e WEBHOOK_URL=https://automation.clientdomain.com \
  -v n8n_data:/home/node/.n8n \
  n8nio/n8n

# Client gets their own branded automation portal
```

**Positioning:** "Your team's private AI automation platform" — not a shared tool, their own instance

---

### Model 4: Embedded Automation (Best for Scale)
**What:** Build workflow execution into a SaaS product via n8n's API
**Revenue:** Included in SaaS pricing or add-on feature
**Example:** Offer "AI automation" as a premium tier in any SaaS

**n8n API integration:**
```javascript
// Trigger any n8n workflow via API
const response = await fetch('https://your-n8n.com/api/v1/workflows/{id}/activate', {
  method: 'POST',
  headers: {
    'X-N8N-API-KEY': process.env.N8N_API_KEY,
    'Content-Type': 'application/json'
  }
});
```

---

## The FlowMind Labs 90-Day SaaS Plan

### Month 1 — Foundation (₹0 → ₹42,000 MRR)
**Week 1-2:**
- [ ] Set up n8n on Hetzner VPS (~₹700/month)
- [ ] Create GitHub repo with 5 free workflows (this repo)
- [ ] Build simple landing page: flowmindlabs.co/workflows
- [ ] Set up Gumroad or Razorpay for payments

**Week 3-4:**
- [ ] Launch 5 premium workflows at ₹4,149 each on Gumroad
- [ ] Post 1 LinkedIn post/day showing workflow demos
- [ ] Goal: 10 workflow sales = ₹41,490 revenue

### Month 2 — Retainers (₹42,000 → ₹1,70,000 MRR)
- [ ] Reach out to 20 startup founders on LinkedIn
- [ ] Offer: "Free 2-week automation audit"
- [ ] Convert 3 to ₹9,999/month retainers = ₹29,997 MRR
- [ ] Add workflow request form to website

### Month 3 — Scale (₹1,70,000 → ₹3,65,000 MRR)
- [ ] First white-label client at ₹19,999/month
- [ ] Raise retainer pricing for new clients to ₹12,999/month
- [ ] Hire 1 part-time developer for workflow builds
- [ ] Total: 3 retainers (₹29,997) + 1 white-label (₹19,999) + workflow sales (₹85,000+) ≈ **₹3,65,000 MRR**

---

## Technical Stack for Your Automation SaaS

```
Customer pays
    ↓
Razorpay / Stripe (payments)
    ↓
Supabase (user management + workflow library database)
    ↓
Your Website (Next.js or Framer)
    ↓
n8n (workflow engine — self-hosted on Hetzner)
    ↓
Claude API / OpenRouter (AI brain)
    ↓
Customer's tools (Slack, Notion, CRM, etc.)
```

**Monthly infrastructure cost: ~₹1,200–₹2,500/month**
- Hetzner CX31 (4 vCPU, 8GB): ~₹700/month
- Supabase Free tier: ₹0
- Domain: ~₹100/month
- SSL: Free (Certbot)

---

## Packaging Your Workflows for Sale

### What to include in each workflow package:

**1. The JSON workflow file** (what they import into n8n)

**2. A README with:**
- What it does (1 paragraph)
- Prerequisites (tools + accounts needed)
- Step-by-step setup guide
- Screenshot/GIF of the workflow in action
- Customization guide

**3. A demo video** (Loom, 3-5 minutes)
- Show the workflow running
- Show the output
- Show how to configure it

**4. Support channel** (email or Discord)

---

## Pricing Strategy

**Charge for value, not time:**

| Workflow saves per week | Fair price (INR) |
|------------------------|-----------------|
| 1-2 hours | ₹2,499–₹4,149 |
| 3-5 hours | ₹6,749–₹12,649 |
| 5-10 hours | ₹12,649–₹24,999 |
| 10+ hours | ₹24,999–₹49,999 |

**Bundles sell better than singles:**
- "AI Ops Pack" (4 workflows) at ₹12,499 instead of 4 × ₹4,149 = ₹16,596
- "Complete Automation Kit" (all 17 workflows) at ₹41,999

---

## The n8n Workflow as SaaS — n8n's Own Approach

n8n itself now offers n8n Cloud at ₹1,700–₹10,200/month — proof the model works. You're doing the same but adding:
- Done-for-you setup
- Pre-built workflows for specific use cases
- Ongoing support and customization
- AI-powered workflows (your differentiator)

---

## Legal & Business Setup

**Minimum viable setup:**
1. Register as sole proprietor / one-person company
2. Open a current bank account (HDFC/ICICI/Kotak recommended)
3. Use Razorpay for Indian payments, Stripe for international
4. Add Terms of Service + Privacy Policy (use free generator at termly.io)
5. Invoice clients via Zoho Invoice or Wave (both free)

**India-specific:**
- Register as Individual/Freelancer initially
- Once >₹20 lakh revenue, register as LLP or Pvt Ltd
- Get GST registration when applicable (mandatory above ₹20 lakh turnover)

---

## Getting Your First 5 Clients — Scripts That Work

**LinkedIn DM (connection request message):**
> "Hey [Name], I saw you're building [Company]. We've automated similar workflows for founders — saving 10+ hours/week. Happy to show you one free for your specific stack. Worth 15 mins?"

**Cold email subject:** `"I built an automation that would save [Company] ~₹1.7L/month"`

**Twitter/X approach:**
Post workflow demos with this caption format:
> "I automated [boring task] in 10 minutes with n8n + Claude.
> Here's the exact workflow → [link]
> Saving [X] hours/week."

---

## Resources

- **n8n docs:** docs.n8n.io
- **n8n community:** community.n8n.io
- **OpenRouter (affordable AI):** openrouter.ai
- **Claude API:** console.anthropic.com
- **FlowMind Labs workflows:** github.com/flowmindlabs/flowmindlabs-n8n-workflows
- **Payment processing India:** razorpay.com
- **Payment processing global:** gumroad.com or lemonsqueezy.com
- **Host your n8n:** hetzner.com/cloud

---

## n8n Workflow: Automation Business Dashboard

This companion workflow tracks your automation business metrics:
- Number of active clients
- Workflows running this month
- Revenue tracked
- Client satisfaction scores

Import `17-automation-saas-dashboard.json` for the full tracking workflow.

---

*Built by FlowMind Labs — We build AI automation that works.*
*Website: flowmindlabs.co | GitHub: github.com/flowmindlabs | Email: contact@flowmindlabs.co*
