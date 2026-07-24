# FreelanceOS 🚀
### AI-Powered Freelance Business Automation Platform

FreelanceOS is a modular automation platform built on **n8n** that streamlines the entire freelance business lifecycle — from lead discovery to project closure — using AI-powered decision-making, human-in-the-loop approvals, and automated client communication.

Built as a capstone project during the IIT Jammu AI Summer School (in collaboration with Techible and I3C-IIT Jammu).

---

## 📌 Problem Statement

Freelancers spend a significant amount of time on repetitive administrative tasks — screening leads, writing proposals, tracking milestones, generating invoices, and following up on payments — instead of focusing on actual project work. FreelanceOS automates this entire lifecycle while keeping the freelancer in control at key decision points.

---

## 🎯 Objectives

- Automate client inquiry handling and lead qualification
- Generate AI-powered, context-aware proposals
- Track project milestones and send automated client updates
- Automate invoicing and payment follow-ups with risk-based escalation
- Handle project closure, testimonial collection, and business analytics

---

## 🏗️ Architecture Overview

FreelanceOS consists of **5 interconnected n8n workflows**, totaling 28–30 nodes:

| # | Workflow | Purpose |
|---|----------|---------|
| 1 | **Lead Discovery & Client Onboarding** | Captures inquiries, AI-scores lead quality, routes to onboarding or auto-decline |
| 2 | **AI Proposal Generation & Client Communication** | Generates personalized proposals + pricing, requires human approval, sends to client, tracks response |
| 3 | **Project Milestone Tracking & Client Updates** | Daily check on active milestones, AI progress summaries, overdue alerts |
| 4 | **Invoice Generation & Payment Follow-up** | Generates invoices, flags payment risk with AI, sends tiered reminder escalations |
| 5 | **Project Closure, Testimonials & Business Analytics** | Requests testimonials, updates portfolio, generates weekly AI business insights |

### Event Flow Between Workflows


[Client Inquiry]
↓
Workflow 1 (Lead Discovery)
↓ (qualified lead)
Workflow 2 (Proposal + Approval + Send)
↓ (project running)
Workflow 3 (Milestone Tracking) ──→ Workflow 4 (Invoicing)
↓ (project done)
Workflow 5 (Closure + Analytics)

---

## ⚙️ Tech Stack

- **Automation Engine:** [n8n](https://n8n.io) (Cloud)
- **AI:** OpenAI (GPT-3.5-turbo) — lead scoring, proposal generation, pricing suggestions, payment risk flagging, business insights
- **Database:** Supabase (Postgres) — clients, projects, milestones, invoices
- **Communication:** Gmail, Slack (including Slack's native Approve/Reject human-in-the-loop flow)
- **Other Integrations:** Google Sheets (audit logging), Notion API (portfolio updates)

---

## 🧠 Advanced Features Implemented

- ✅ AI-powered decision-making (lead scoring, pricing, risk flagging, business insights)
- ✅ Human-in-the-loop approval step (Slack Approve/Reject before sending proposals)
- ✅ Conditional branching (lead quality routing, escalation tiers, response tracking)
- ✅ Scheduled (Cron) workflows — daily milestone/invoice checks, weekly analytics
- ✅ Webhook-triggered workflows — inquiry intake, testimonial requests
- ✅ Error handling / retry logic (dedicated error-trigger workflow)
- ✅ Centralized audit logging (Google Sheets)
- ✅ Cross-workflow orchestration via Execute Workflow nodes

---

## 📂 Repository Structure

FreelanceOS/
├── README.md
├── workflows/
│ ├── 1-lead-discovery-onboarding.json
│ ├── 2-ai-proposal-generation.json
│ ├── 3-milestone-tracking.json
│ ├── 4-invoice-payment-followup.json
│ └── 5-project-closure-analytics.json
├── docs/
│ └── architecture.md
└── screenshots/
└── (execution screenshots)

---

## 🚀 Setup Instructions

1. **Import workflows** — In n8n, go to Workflows → Import from File, and import each JSON from the `/workflows` folder.
2. **Set up credentials** in n8n for:
   - OpenAI
   - Gmail
   - Slack
   - Postgres (Supabase connection)
   - Google Sheets
3. **Set up the database** — Create the following tables in Supabase: `clients`, `projects`, `milestones`, `invoices` (see `docs/architecture.md` for schema).
4. **Configure webhooks** — Update webhook URLs in your form/API tools (e.g., ReqBin, Typeform) to point to your n8n instance.
5. **Test each workflow individually** before connecting them end-to-end.

> ⚠️ **Note:** This project was built and tested using simulated/mock client data for demonstration purposes. No real client information is used.

---

## 📸 Demo

*(Demo video link and screenshots to be added)*

---

## 👤 Author

**Sushant** — B.Tech CSE, Bennett University
Built during the IIT Jammu AI Summer School (June–July 2026)

---

## 📄 License

This project is for educational/portfolio purposes.
