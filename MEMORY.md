# MEMORY.md — Momentix Project Memory Log

This is the running state of the project. Every agent reads this at the start of a session and appends to it at the end. Newest entries go at the top. Keep entries short and factual — decisions with reasoning belong in `DECISIONS.md`, this file is "what happened and what's next."

---

## [2026-09-11 — Phase 1 design mockups]

- Generated and saved 12 image-based UI mockups under `apps/web/design-mockups/`: desktop and mobile versions of sign-in, dashboard shell, Home, Inbox, Lead Finder, and a persistent inline error state.
- Mockups use the approved Momentix logo reference and the exact palette, typography direction, spacing, radii, navigation, AI-action, lead-score, and error-state rules from `DESIGN.md`.
- No production application code has been written.
- Pending: owner review and explicit approval before Phase 2 implementation.

### Open items for next session

- [ ] Apply any requested mockup revisions, or begin Phase 2 only after explicit approval.
- [ ] Build the functional Next.js frontend with mock data and complete frontend tests after approval.

## [2026-09-11 — Phase 0 repository setup]

- Initialized the frontend monorepo workspace with `apps/web`, `apps/marketing`, `packages/ui`, and `packages/shared-types` placeholders.
- Added the root npm workspace manifest and a Next.js/TypeScript-focused `.gitignore`.
- Moved the approved project documentation and logo to the repository root to match `AGENT.md`.
- No application, backend, database, or production UI code has been written.
- Pending: connect and push to the owner-provided GitHub remote, then wait for approval before Phase 1 image mockups.

### Open items for next session

- [ ] Add the GitHub remote URL supplied by the owner and push the initial commit.
- [ ] After explicit Phase 0 approval, generate the Phase 1 desktop and mobile mockup images in the required order.

## How to use this file

- At session start: read the top entry to know where the project stands.
- At session end: add a new dated entry above the previous one with:
  - What was built/changed
  - What's still open or broken
  - What the next agent (especially if it's a different one — backend↔frontend↔testing) needs to know
  - Any new file/contract that changed shape

---

## [Project Kickoff]

- Project: Momentix.space — AI workspace for inbox management, lead finding, and follow-up automation.
- Docs finalized: `PRD.md`, `AGENT.md`, `DESIGN.md`, `ARCHITECTURE.md`, `RULES.md`, `DECISIONS.md`, `TESTING.md`.
- Stack confirmed: Next.js + TypeScript (frontend), Node.js + Express + TypeScript (backend), PostgreSQL + Prisma (database).
- Auth: **Confirmed** — NextAuth.js (Auth.js) with Google Provider, built directly in Next.js (not Clerk, not Firebase). See `DECISIONS.md` D-002.
- Database hosting: **Confirmed** — Supabase free tier for MVP, migrate to Railway once usage grows. See `DECISIONS.md` D-007.
- AI access model: **Confirmed** — same page-to-provider routing for free and paid, difference is daily query quota, not provider restriction. Page-to-provider mapping documented in `ARCHITECTURE.md` §7.1. See `DECISIONS.md` D-008.
- Transactional email: Resend proposed for account/billing emails.
- Dashboard page/file structure (Next.js App Router, mapped to sidebar) documented in `ARCHITECTURE.md` §14.
- Current build stage: **Stage 0 — not yet started.** Next step is Stage 1 (Foundation) per `AGENT.md` §7 / `PRD.md` build order. Frontend (Codex) about to start dashboard UI exploration against `DESIGN.md`.
- No code has been written yet. No repo scaffolded yet.

### Open items for next session
- [ ] Scaffold the monorepo per `AGENT.md` §1
- [ ] Set up `.env.example` with all known variable names (no values) — include `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET`, `NEXTAUTH_SECRET`, Supabase connection string, `RESEND_API_KEY`, and one env var per AI provider key (Groq, Claude, Perplexity, Gemini, GPT)
- [ ] Initialize `prisma/schema.prisma` from the entity list in `ARCHITECTURE.md` §5
- [ ] Confirm domain/DNS setup for `momentix.space`, `app.`, `api.` subdomains
- [ ] Codex to start frontend design pass against `DESIGN.md` + `ARCHITECTURE.md` §14 page structure
