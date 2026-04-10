# FlowMind Labs — Client Security Agreement

**Version 1.0 | Effective from the date of service commencement**

---

## Parties

**Service Provider:** FlowMind Labs (`www.flowmindlabs.co`)
**Client:** As named in the service order / onboarding form

---

## 1. Scope of Service

FlowMind Labs provides AI automation agents built on n8n, deployed and managed on behalf of the Client. This includes WhatsApp Business agents, lead capture agents, FAQ/support agents, and other automation workflows as agreed in the service order.

---

## 2. What FlowMind Labs Is Responsible For

- Building, deploying, and maintaining automation workflows
- Storing all credentials securely inside n8n's encrypted credential vault
- Ensuring no Client credentials are hardcoded in workflow files or shared across clients
- Providing each Client an isolated instance — no shared infrastructure with other clients
- Notifying the Client within 24 hours of any suspected security incident affecting their instance

---

## 3. What the Client Is Responsible For

- Providing a **dedicated WhatsApp Business number** registered through an official BSP (Chakra Chat, AiSensy, Interakt, or Wati)
- **Not sharing** the WhatsApp Business number or BSP account with any other application while the agent is active
- Keeping their BSP account credentials (API keys, tokens) confidential
- Rotating API keys immediately if they suspect a breach and notifying FlowMind Labs
- Providing accurate and up-to-date business information for the knowledge base / agent configuration
- Complying with WhatsApp Business API Terms of Service — no spam, no bulk unsolicited messages

---

## 4. Data Ownership

- The Client owns all their customer data (names, phone numbers, conversation history, bookings, leads)
- FlowMind Labs does **not** sell, share, or use Client data for any purpose other than operating the agreed service
- On termination of service, all Client data is exported and handed back within 7 business days
- FlowMind Labs does not retain copies of Client data after termination

---

## 5. Credential Security

- All third-party API keys (Airtable, Stripe, Google, BSP keys) provided by the Client are stored **only** in n8n's encrypted vault
- Credentials are never stored in workflow JSON files, logs, or documentation
- FlowMind Labs staff do not have access to Client credentials beyond what is required for deployment and maintenance

---

## 6. WhatsApp Policy Compliance

- The Client is responsible for ensuring their messaging complies with WhatsApp Business Policy
- FlowMind Labs will configure agents to avoid spam triggers, bulk messaging, and policy violations
- However, FlowMind Labs is **not liable** for WhatsApp account bans resulting from:
  - Client-initiated bulk/broadcast campaigns outside agreed workflows
  - Client sharing the number with other tools simultaneously
  - Meta/WhatsApp policy changes outside our control

---

## 7. Limitations of Liability

- FlowMind Labs is not liable for business losses arising from agent downtime, third-party API outages (WhatsApp, Airtable, OpenRouter, Claude), or force majeure events
- FlowMind Labs' maximum liability is limited to the monthly fee paid for the affected month
- FlowMind Labs does not guarantee 100% uptime — target SLA is 99% for Business and Enterprise plans

---

## 8. Confidentiality

Both parties agree to keep confidential:
- Client's customer data, business processes, and pricing
- FlowMind Labs' workflow designs, agent configurations, and technical implementation
- Neither party will disclose the other's confidential information to third parties without written consent

---

## 9. Termination

- Either party may terminate with **30 days written notice**
- FlowMind Labs may terminate immediately if the Client violates WhatsApp ToS, uses the service for spam/illegal activity, or fails payment for 30+ days
- On termination: Client data exported, credentials deleted, instance decommissioned within 7 business days

---

## 10. Governing Law

This agreement is governed by the laws of India. Any disputes will be resolved through mutual discussion first. If unresolved, disputes will be subject to arbitration under the Arbitration and Conciliation Act, 1996, in Bangalore, India.

---

## Acceptance

By proceeding with onboarding and payment, the Client acknowledges they have read, understood, and agreed to this Security Agreement.

| | Service Provider | Client |
|---|---|---|
| **Name** | FlowMind Labs | |
| **Date** | | |
| **Signature** | | |

---

*For questions about this agreement, contact: hello@flowmindlabs.co*
