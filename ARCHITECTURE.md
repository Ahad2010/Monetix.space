# ARCHITECTURE.md — Momentix.space System Architecture

## 1. High-Level System Diagram

```
momentix.space                 → Marketing / SEO website (Next.js)
        │
        ├──→ app.momentix.space   → Next.js SaaS dashboard (auth, onboarding, product)
        │
        └──→ api.momentix.space   → Node.js + Express API
                    │
      ┌─────────────┼──────────────┐
      ↓              ↓               ↓
  PostgreSQL     Redis/Queue      AI Router
      │                                │
      │                  ┌─────────────┼─────────────┐
      │                  ↓             ↓             ↓
      │              Writing       Research      Fast Tasks
      │             (Claude/GPT) (Perplexity)   (Groq/Gemini)
      │
      ├── users
      ├── workspaces
      ├── contacts
      ├── leads
      ├── messages
      ├── tasks
      ├── notifications
      ├── workflows
      ├── subscriptions
      └── usage
```

## 2. Domain & Subdomain Structure

```
momentix.space          → Marketing website: features, pricing, blog, SEO
app.momentix.space      → Auth, onboarding, SaaS dashboard
api.momentix.space      → Express backend API
docs.momentix.space     → Documentation (later)
status.momentix.space   → Status page (later)
```

Marketing site and dashboard are separate Next.js apps (see AGENT.md repo layout) but share the design system in `packages/ui`.

## 3. Frontend

- Next.js, TypeScript, Tailwind CSS
- Inter font, Lucide icons (per DESIGN.md)
- Framer Motion where useful (motion rules in DESIGN.md §24)
- TanStack Query for data fetching against the API contract
- Auth via Clerk SDK (see DECISIONS.md)

## 4. Backend

- Node.js + Express.js + TypeScript
- REST APIs, webhooks (Stripe, Clerk), OAuth callbacks (Gmail)
- AI orchestration (routing layer, see §7)
- Usage metering, billing logic, notification logic
- Background jobs (via Redis/BullMQ once needed — not required for earliest prototype)

## 5. Database

**PostgreSQL + Prisma ORM.** Chosen because users, workspaces, contacts, leads, messages, subscriptions, workflow runs, notifications, and permissions all have strong relational structure — a relational DB fits better than a document store here (see DECISIONS.md for the record of this choice).

Possible future addition: `pgvector` for semantic workspace retrieval/RAG (Phase 2+).

### Core Entities

```
users
workspaces
workspace_members
subscriptions
connected_accounts
contacts
leads
conversations
messages
tasks
notifications
calendar_events
workflows
workflow_runs
workflow_steps
ai_requests
usage_records
files
audit_logs
```

## 6. Redis / Queues

Not mandatory for the earliest prototype. Add Redis + BullMQ once Momentix needs to run:
- Background AI tasks
- Email processing
- Lead processing
- Scheduled follow-ups
- Research jobs
- Notifications
- Workflow executions

## 7. AI Router

```
Incoming Request
      ↓
Identify Task Type (classification / draft / research / summary)
      ↓
Identify User Plan (free / paid)
      ↓
Route to Assigned Provider
      ↓
If provider fails or rate-limited → fallback provider
      ↓
Return Structured Result
```

| Provider | Assigned To | Why |
|---|---|---|
| Groq (Llama) | Inbox classification, tagging, spam/noise detection | Fast + cheap — high-frequency, low-complexity tasks |
| Claude | Email drafting, outreach copywriting, complex reasoning | Best quality for writing that represents the user professionally |
| Perplexity | Lead/competitor research, web-grounded answers | Purpose-built for real-time, sourced web research |
| Gemini | Document/image analysis, quick summaries | Strong multimodal support, competitive cost |
| GPT | Fallback/secondary drafting & reasoning | Redundancy if Claude has downtime or rate limits |

**Access model (see DECISIONS.md D-008):** every page/feature keeps the same assigned provider for all users. Free vs paid is a **daily query quota**, not a provider restriction — free workspaces get a small daily quota across all providers, paid workspaces get a much higher (or no) quota. Enforced via `usage_records` in the AI router, on top of the hard per-workspace spending cap in `RULES.md` §7. The routing layer is centralized so a provider can be swapped later without touching feature code.

### 7.1 Page-to-Provider Mapping

Each sidebar page/feature has one assigned primary provider and one fallback, so it's always clear which API key is doing the work for a given screen:

| Page / Feature | Primary Provider | Fallback | Why |
|---|---|---|---|
| Inbox — classification (New Lead/Client/Follow-up/Important/Noise) | Groq | Gemini | High-frequency, low-complexity — needs speed |
| Inbox — AI draft reply | Claude | GPT | Writing quality represents the user professionally |
| Leads — lead scoring (0–100) | Groq | Gemini | Runs on every lead, high volume |
| Leads — outreach email generation | Claude | GPT | Same writing-quality bar as inbox drafts |
| Research (lead/competitor research) | Perplexity | GPT | Purpose-built for sourced, web-grounded answers |
| Follow-ups — suggested follow-up draft | Claude | GPT | Writing quality |
| Files — document/image analysis, quick summaries | Gemini | GPT | Strong multimodal support |
| Any provider timeout/rate-limit | — | Next in that row's fallback column | Centralized fallback logic in the router |

This table is the reference `apps/api`'s AI router config maps to directly — one entry per route/feature, not a global "free = X, paid = Y" switch.

## 8. Local Development vs Cloud

```
Local Machine
  Next.js      → localhost:3000 (or 3001 for dashboard)
  Express API  → localhost:5000
       ↓
Supabase PostgreSQL (free tier)
       ↓
External AI / Email / Payment APIs
```

Production:
```
Marketing Site   → Vercel
SaaS Dashboard   → Vercel
Express Backend  → Managed Node host
PostgreSQL       → Supabase (free tier) → migrate to Railway once usage grows (see DECISIONS.md D-007)
Redis            → Managed Redis (when needed)
Object Storage   → S3-compatible (when needed)
```

Start as a **modular monolith**, not microservices.

## 9. Security & Permission Boundaries

Never expose to the client:
- AI provider secret keys
- Payment secrets
- OAuth client secrets
- Email provider credentials
- Database credentials

```
Next.js Client → Momentix Express API → Authorized Integration / AI Provider
```

Safeguards required:
- OAuth for all third-party connections
- Encryption in transit
- Secure token storage for connected-account tokens
- Role-based permissions
- Workspace isolation (a workspace can never read another workspace's data)
- Rate limiting
- Input validation
- Webhook signature verification (Stripe, Clerk)
- Audit logs for consequential actions
- Prompt-injection defenses for any tool-using AI
- Data export/deletion controls
- Explicit user approval before high-risk actions execute

## 10. Product Safety Levels

**Low risk — automatic:** classify, summarize, extract, suggest, draft, score lead.

**Medium risk — configurable:** create internal task, add internal tag, update contact metadata, save lead.

**High risk — approval required by default:** send external email, modify external calendar, delete information, trigger consequential third-party actions.

```
AI proposes → User reviews → User approves → Momentix executes → Audit log
```

## 11. Notification System

### Data Model
```
id
userId
workspaceId
type
title
message
actionUrl
isRead
metadata
createdAt
```

### Delivery
In-app notification center (navbar bell) + selected transactional emails via Resend (welcome, verification, security alerts, plan activated/changed, payment failed, usage warnings).

## 12. Connected Email + Approval Flow

```
Connected Gmail → New Email → Momentix Ingestion → AI Classification
   → Relevant Context Retrieval → AI Draft → Notification
   → Review/Edit/Reject → Approve & Send → Email Provider API
   → Sent from User's Connected Account → Contact Timeline Updated
```

Launch behavior: draft-only/analysis is automatic; external sending always requires approval. A future "Auto-send Trusted Workflows" setting is Phase 2+, and only ships with explicit opt-in, audit logs, and clear boundaries.

## 14. Dashboard File Structure (Next.js App Router — `apps/web`)

Maps directly to the sidebar nav in `DESIGN.md` §11. One route group per sidebar item, each with its own `page.tsx`, loading/error states, and colocated components. Shared pieces (layout, sidebar, topbar) live at the root.

```
apps/web/
├── app/
│   ├── (auth)/
│   │   ├── sign-in/page.tsx
│   │   └── sign-up/page.tsx
│   ├── (dashboard)/
│   │   ├── layout.tsx              # sidebar + topbar shell (DESIGN.md §11/§12)
│   │   ├── home/
│   │   │   ├── page.tsx            # Home — today's summary, AI suggested actions
│   │   │   └── components/
│   │   ├── inbox/
│   │   │   ├── page.tsx            # Inbox list
│   │   │   ├── [messageId]/page.tsx
│   │   │   └── components/         # email row, AI draft panel, approve/edit/reject
│   │   ├── leads/
│   │   │   ├── page.tsx            # Lead Finder — filters + results
│   │   │   ├── [leadId]/page.tsx   # lead detail — score, signals, outreach draft
│   │   │   └── components/
│   │   ├── contacts/
│   │   │   ├── page.tsx
│   │   │   ├── [contactId]/page.tsx  # contact timeline
│   │   │   └── components/
│   │   ├── tasks/
│   │   │   ├── page.tsx
│   │   │   └── components/
│   │   ├── workflows/              # Phase 2 — scaffold only, not built at MVP
│   │   │   └── page.tsx
│   │   ├── research/               # Phase 2 — scaffold only
│   │   │   └── page.tsx
│   │   ├── calendar/               # Phase 2 — scaffold only
│   │   │   └── page.tsx
│   │   ├── files/
│   │   │   ├── page.tsx
│   │   │   └── components/
│   │   ├── analytics/
│   │   │   ├── page.tsx            # key product metrics (PRD.md §7)
│   │   │   └── components/
│   │   ├── integrations/
│   │   │   ├── page.tsx            # connect Gmail, Stripe portal link
│   │   │   └── components/
│   │   └── settings/
│   │       ├── page.tsx
│   │       ├── billing/page.tsx
│   │       └── components/
│   ├── api/
│   │   └── auth/[...nextauth]/route.ts   # NextAuth.js Google provider handler
│   ├── layout.tsx                  # root layout — Inter font, providers
│   └── globals.css                 # design tokens from DESIGN.md §26
├── components/                     # cross-page shared components (Sidebar, Topbar, NotificationBell)
├── lib/
│   ├── api-client.ts                # typed client against packages/shared-types
│   └── auth.ts                      # NextAuth.js config
└── middleware.ts                    # route protection for (dashboard) group
```

Each MVP page (Home, Inbox, Leads, Contacts, Tasks, Files, Analytics, Integrations, Settings) is fully built for Stage 1–5. Workflows/Research/Calendar get a route scaffold with a "coming soon" state only — no real functionality until Phase 2, per `PRD.md` §6 scope discipline.

## 15. Multi-Device Experience

MVP is a responsive web SaaS usable on desktop, tablet, and mobile browser from day one — no native app required to validate the MVP. Native apps are Phase 3.
