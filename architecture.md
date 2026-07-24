# FreelanceOS — System Architecture

## 1. Overview

FreelanceOS is a modular automation platform built entirely on n8n, consisting
of 5 independent, event-driven workflows that together automate the complete
freelance business lifecycle — from lead capture to business analytics.

## 2. High-Level Architecture


Client Inquiry
│
▼
[Workflow 1: Lead Discovery & Client Onboarding]
│ (qualified lead → client record created)
▼
[Workflow 2: AI Proposal Generation & Client Communication]
│ (proposal approved → active project)
▼
[Workflow 3: Project Milestone Tracking & Client Updates] ← runs daily (Cron)
│ (milestone marked complete)
▼
[Workflow 4: Invoice Generation & Payment Follow-up]
│ (final invoice paid → project closed)
▼
[Workflow 5: Project Closure, Testimonials & Business Analytics] ← weekly report (Cron)


All workflows share a common Postgres (Supabase) database and communicate via
triggers and shared data records rather than direct calls — keeping each
workflow independently testable and maintainable.

## 3. Workflow Interaction & Event Flow

| From → To | Trigger Condition |
|---|---|
| WF1 → WF2 | Lead scored ≥60 by AI → client record created → proposal workflow invoked |
| WF2 → WF3 | Client approves proposal → active project record created |
| WF3 → WF4 | Milestone status changes to `complete` |
| WF4 → WF5 | Final invoice marked `paid` → project closed |
| WF5 → Freelancer | Weekly Cron independently aggregates data across all records |

## 4. Workflow Breakdown

### Workflow 1 — Lead Discovery & Client Onboarding
Webhook Trigger → Normalize Data → AI Lead Qualification (OpenAI) → IF (Score ≥60)
→ [Create Client Record (Postgres) → Notify Freelancer (Slack)] / [Auto Decline Email (Gmail)]

**Tested:** ✅ Success in 33.3s — see `screenshots/01-lead-discovery-execution.jpeg`

### Workflow 2 — AI Proposal Generation & Client Communication
Trigger: Qualified Lead → Fetch Past Similar Projects (Postgres) → AI Generate
Proposal + AI Pricing Suggestion (parallel, OpenAI) → Human Approval (Slack
sendAndWait) → IF Approved → Send Proposal Email (Gmail) → Wait 3 Days →
IF Client Responded → Follow-up Email (Gmail)

**Tested:** ✅ Success in 1m 24.2s (includes human-in-the-loop approval) —
see `screenshots/02-proposal-generation-execution.jpeg`

### Workflow 3 — Project Milestone Tracking & Client Updates
Daily Cron → Fetch Active Milestones (Postgres) → AI Progress Summary (OpenAI)
→ Send Client Update (Gmail) → Audit Log Entry (Google Sheets)
Parallel branch: IF Milestone Overdue → Slack Alert
Separate branch: Error Trigger → Retry/Notify on Failure (Slack)

**Tested:** ✅ Success in 8.1s, overdue alert fired correctly —
see `screenshots/03-milestone-tracking-execution.jpeg`

### Workflow 4 — Invoice Generation & Payment Follow-up
Trigger: Milestone Complete → Generate Invoice (Code) → Send Invoice Email (Gmail)
Separate branch: Daily Cron → Fetch Payment History (Postgres) → AI Payment
Risk Flag (OpenAI) → Switch (Escalation Tier: Day 3 / 7 / 14) → Reminder Emails (Gmail)

**Tested:** ✅ All 3 escalation tiers verified —
see `screenshots/04-invoice-payment-execution.jpeg`

### Workflow 5 — Project Closure, Testimonials & Business Analytics
Trigger: Project Closed → Request Testimonial (Gmail)
Separate branch: Testimonial Form Webhook → Update Portfolio (Notion API)
Separate branch: Weekly Cron → Aggregate Business Data (Postgres) → AI Business
Insight (OpenAI) → Send Weekly Business Report (Gmail)

**Tested:** ✅ "Workflow executed successfully" —
see `screenshots/05-closure-analytics-execution.jpeg`

## 5. Database Schema (Supabase / Postgres)

```sql
clients (id, name, email, brief, budget, status, created_at)
projects (id, client_id, title, amount, rating, closed_at)
milestones (id, project_id, milestone_name, due_date, status, client_email, client_name)
invoices (id, project_id, invoice_id, amount, status, days_overdue, client_email, client_name)
```

## 6. Integrations Used

| Integration | Purpose |
|---|---|
| OpenAI (GPT) | Lead scoring, proposal generation, pricing suggestion, risk flagging, business insights |
| Gmail | All client and freelancer email communication |
| Slack | Internal alerts, human-approval steps |
| Supabase (Postgres) | Client, project, milestone, and invoice records |
| Google Sheets | Audit log |
| Notion API | Portfolio auto-update |

## 7. Advanced Features Implemented

- **AI-powered decision making**: lead scoring, pricing suggestion, payment risk flag, business insights
- **Human approval step**: Slack sendAndWait on proposal review (Workflow 2)
- **Error handling & retry logic**: dedicated Error Trigger + Slack notification (Workflow 3)
- **Logging & audit trail**: Google Sheets audit log (Workflow 3)
- **Scheduled workflows**: daily milestone check, daily invoice check, weekly analytics (Cron)
- **Webhook-triggered workflows**: new inquiry, testimonial submission
- **Conditional branching**: lead quality, approval status, overdue check, escalation tiers
