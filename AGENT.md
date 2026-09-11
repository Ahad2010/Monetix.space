# AGENT.md — Momentix Project Handbook for AI Coding Agents

This file tells any AI agent (Claude Code, Codex, or a testing agent) how the Momentix project is organized, who owns what, what commands to run, and what rules are non-negotiable. Read this file first, every session, before touching code.

Read alongside:
- `PRD.md` — what we're building and why
- `ARCHITECTURE.md` — how the system is structured
- `DESIGN.md` — the only source of truth for visual output
- `RULES.md` — hard constraints every agent must follow
- `DECISIONS.md` — why past choices were made (don't re-litigate without reading this)
- `MEMORY.md` — running log of project state; update it at the end of every session

## 1. Repo Structure (monorepo)

```
momentix/
├── apps/
│   ├── marketing/        # Next.js — momentix.space public site
│   ├── web/               # Next.js — app.momentix.space dashboard (full page/file layout in ARCHITECTURE.md §14)
│   └── api/                # Node.js + Express — api.momentix.space
├── packages/
│   ├── shared-types/      # Shared TS types (DB entities, API contracts)
│   ├── ui/                  # Shared design-system components (built to DESIGN.md)
│   └── config/             # Shared eslint/tsconfig/tailwind config
├── prisma/
│   └── schema.prisma       # Single source of truth for DB schema
├── PRD.md
├── AGENT.md
├── DESIGN.md
├── ARCHITECTURE.md
├── RULES.md
├── MEMORY.md
├── DECISIONS.md
├── TESTING.md
└── .env.example
```

## 2. Agent Roles

| Agent | Owns | Does NOT touch |
|---|---|---|
| **Claude Code** | `apps/api/`, `prisma/`, backend business logic, AI router, OAuth/webhook handlers, billing logic, background jobs | `apps/web/` UI components, `apps/marketing/` |
| **Codex** (current phase — see DECISIONS.md D-005) | `apps/web/`, `apps/marketing/`, all UI built strictly to `DESIGN.md` tokens, client-side data fetching (via TanStack Query) against the API contracts Claude Code exposes, **its own tests for that code** (per `TESTING.md` layers), and **Git/GitHub repo setup and connection** for the frontend workspace | Database schema, server-side secrets, AI provider keys |

For this phase there is no separate testing agent — Codex writes and runs its own tests. A dedicated testing agent (as originally scoped) can be reintroduced later; update `DECISIONS.md` D-005 when that changes.

Rule of thumb: **backend agent never writes UI, frontend agent never writes DB schema or touches secrets, testing agent never ships feature code.** If a task needs both, it is split into two handoffs, not done by one agent wearing both hats.

## 3. Contract Between Frontend and Backend

- The backend (Claude Code) owns and documents the REST API contract (routes, request/response shapes) in `packages/shared-types/`.
- The frontend (Codex) consumes only through those typed contracts — never guesses a shape or calls the DB directly.
- Any contract change is a breaking change: update `shared-types`, note it in `DECISIONS.md`, and flag it in `MEMORY.md` so the other agent picks it up next session.

## 4. Commands

```bash
# Install (root, monorepo)
npm install

# Dev — run everything
npm run dev            # marketing:3000, web:3001, api:5000 (concurrently)

# Dev — single app
npm run dev --workspace=apps/api
npm run dev --workspace=apps/web

# Database
npx prisma migrate dev     # apply schema changes locally
npx prisma studio          # inspect DB
npx prisma generate        # regenerate client after schema edits

# Lint / typecheck
npm run lint
npm run typecheck

# Tests — see TESTING.md for full breakdown
npm run test                # unit
npm run test:integration    # API integration
npm run test:e2e            # Playwright

# Build
npm run build
```

## 5. Non-Negotiable Rules (full list in RULES.md)

1. Never hardcode secrets. Everything server-side comes from `.env` (see `.env.example`), never committed.
2. High-risk actions (send email, modify calendar, delete data) require explicit user approval — no exceptions, no "smart" auto-send shortcuts.
3. All UI ships exactly to `DESIGN.md` tokens — no ad hoc colors, spacing, or radii.
4. **No error message in any language other than English**, anywhere a user can see it (UI text, API error bodies, validation messages).
5. **No popup/alert-style error UI** (no `window.alert`, no dismiss-and-forget toast as the only error surface) — errors render as persistent inline states using the DESIGN.md error tokens until the user resolves or explicitly dismisses them.
6. Every new architectural or product decision gets a short entry in `DECISIONS.md` before or alongside the code that implements it.
7. Update `MEMORY.md` at the end of every work session: what changed, what's still open, what the next agent needs to know.

## 6. Handoff Protocol

When an agent finishes a session:
1. Run lint, typecheck, and relevant tests — do not hand off broken code.
2. Append a dated entry to `MEMORY.md` (what was done, what's pending, any new decisions).
3. If the API contract changed, update `packages/shared-types/` and flag it clearly in the `MEMORY.md` entry so the other agent doesn't build against a stale shape.
4. If a new architectural choice was made, add it to `DECISIONS.md` with context and status.

## 7. Current Build Stage

Tracked live in `MEMORY.md`. Build order (from PRD.md):

1. Foundation — branding/design system, auth, user/workspace model, dashboard shell, Postgres, Express API
2. AI Inbox — Gmail OAuth, ingestion, classification, drafts, approval/send flow, notifications
3. Lead Finder — search, filters, qualification, scoring, saved leads, outreach drafts
4. CRM + Follow-ups — contacts, timeline, follow-up detection, tasks
5. Monetization — free/paid limits, Stripe checkout, webhooks, usage metering, transactional emails
6. Beta — security review, error handling, analytics, performance, 15–20 target users
