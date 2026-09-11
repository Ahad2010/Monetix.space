# Momentix.space — Product Requirements Document (PRD)

> Tagline: Work Smarter. Grow Faster.
> One-line pitch: An AI workspace that connects a user's inbox, leads, tasks, and calendar — surfaces what needs attention, drafts responses, finds new leads automatically, and executes approved actions.

---

## 1. Problem Statement

Freelancers, agencies, and small businesses lose hours every day to:
- Reading and replying to routine emails
- Manually searching for new leads
- Following up with clients who went quiet
- Switching between inbox, calendar, CRM, and notes just to stay on top of work

There is no single tool that combines lead generation, inbox triage, and follow-up automation in one AI-assisted workspace built specifically for small outreach-driven teams.

## 2. Target User

- **Primary:** Freelancers and small agencies doing local client outreach (web design, marketing, local service businesses) who need a steady lead pipeline plus inbox management.
- **Expansion path:** broader knowledge workers, only after this niche is validated with paying users. Do not build for "everyone" at launch.

## 3. Core Features (MVP Scope — 3 workflows only)

### Workflow 1 — AI Inbox
- Connect Gmail via OAuth (never ask for email passwords)
- AI classifies messages: New Lead / Client / Follow-up / Important / Noise
- AI drafts replies; user must approve, edit, or reject before anything sends
- Draft-only and analysis actions run automatically; sending is always gated by approval at launch

### Workflow 2 — Lead Finder (core differentiator)
- Data source: Google Maps / Google Places API
- Filters: niche/industry, country, city/region, has-website vs no-website, business signals, result limit
- AI lead scoring (0–100) using qualification signals (outdated website, no online booking, weak mobile experience, poor reviews)
- One-click personalized outreach email generation per lead
- Must respect provider rules, privacy law, and anti-spam requirements — built for legitimate, user-controlled prospecting, not bulk spam

### Workflow 3 — Follow-ups & Lightweight CRM
- Auto-detect conversations gone quiet (default: no reply in 4+ days)
- Suggested action: "Follow up with [Contact]" with a pre-drafted message
- Simple contact timeline: last contact, status, potential deal value

Everything else (Workflow Builder, Research module, Analytics, Calendar automation, Outlook, Team workspaces, mobile apps) is explicitly **out of scope for MVP** — see Section 6.

## 4. Future Features (Phase 2 / Phase 3)

**Phase 2** (after MVP validated with paying users):
- Calendar automation
- Advanced research module
- Visual drag-drop Workflow Builder
- Outlook integration
- Team workspaces / shared inboxes
- Advanced analytics
- Workflow templates
- More integrations, better workspace RAG/search
- Browser notifications

**Phase 3**:
- Android / iOS apps
- Desktop companion
- Enterprise SSO, advanced permissions
- Integration marketplace, developer API, public webhooks
- Custom AI agents

## 5. Tech Stack (confirmed)

| Layer | Choice |
|---|---|
| Frontend | Next.js + TypeScript + Tailwind CSS |
| Backend | Node.js + Express.js + TypeScript |
| Database | PostgreSQL + Prisma ORM — hosted on Supabase (free tier) initially, migrating to Railway as usage grows |
| Auth | NextAuth.js (Auth.js) with Google Provider, built directly into Next.js — see DECISIONS.md D-002 |
| Transactional email | Resend |
| Payments | Stripe (Billing, Checkout, Webhooks, Customer Portal) |
| AI providers | Groq (classification/fast), Claude (drafting/reasoning), Perplexity (research), Gemini (multimodal/summaries), GPT (fallback) |
| Lead data | Google Maps / Google Places API |
| Queue (Phase 1.5+) | Redis + BullMQ |
| Hosting | Vercel (frontend), managed Node host (API), managed cloud PostgreSQL |

Full detail in ARCHITECTURE.md. Full visual system in DESIGN.md.

## 6. Constraints

- **Action safety:** low-risk AI actions (classify, summarize, draft, score) run automatically; high-risk actions (send email, modify calendar, delete data) always require explicit user approval.
- **No password harvesting:** email accounts connect via OAuth only.
- **Cost control:** AI usage metered per workspace; hard spending cap regardless of plan; cheap models absorb high-volume tasks, expensive models reserved for quality-critical writing.
- **Security:** all provider secrets, payment secrets, OAuth secrets, and DB credentials stay server-side only — never exposed to the client.
- **Scope discipline:** MVP is 3 workflows only. Do not expand scope before the validation question (Section 8) is answered.
- **Error/UX constraints:** see RULES.md — no non-English text in errors, no popup/alert-style error UI.
- **Start as a modular monolith**, not microservices.

## 7. Success Metrics

Tracked and shown inside the product (this is what justifies the subscription):
- AI Actions Completed
- Leads Found
- Emails Handled
- Follow-ups Sent
- Meetings Assisted
- Workflow Runs
- Estimated Time Saved

Business metrics:
- Activation rate
- Connected-inbox rate
- Lead-search usage
- Draft approval rate
- Follow-up completion rate
- Free → paid conversion
- Monthly recurring revenue (MRR)
- Churn
- AI cost per active user
- Gross margin
- Retention

## 8. Validation Question

**Before building further than the MVP: will 15–20 real users in the target niche pay for AI Inbox + Lead Finder + Follow-ups alone?**

- If yes → expand toward Phase 2.
- If no → the niche or the core workflow needs to change before adding more features or more AI providers.

## 9. Pricing (provisional — validate against real API cost before locking in)

| Tier | Price | Includes |
|---|---|---|
| Free | $0 | 1 inbox, small daily AI query quota (same page-to-provider routing as paid, just fewer queries/day — see DECISIONS.md D-008), basic lead search, basic follow-up detection |
| Individual | ~$19/mo | Claude-powered drafts, higher daily limit, full follow-up automation, contact timeline |
| Pro | ~$49/mo | Full Lead Finder (Google Maps sourced), lead scoring + outreach generation, priority processing, advanced follow-ups |
| Business | ~$89+/mo | Multiple seats/inboxes, shared leads/workflows, team permissions, analytics dashboard |
