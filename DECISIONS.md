# DECISIONS.md — Architecture & Product Decision Log

Every meaningful decision gets a record here: context, the decision, alternatives considered, and status. Don't re-litigate a decision without reading its record first — if something needs to change, add a new entry that supersedes the old one rather than editing history.

---

## D-001 — Database: PostgreSQL + Prisma

- **Status:** Confirmed
- **Context:** Earlier drafts of the product spec listed MongoDB in the tech-stack summary, but the architecture section separately recommended PostgreSQL. Users, workspaces, contacts, leads, messages, subscriptions, workflow runs, notifications, and permissions all have strong relational structure.
- **Decision:** PostgreSQL with Prisma ORM is the single database for Momentix.
- **Alternatives considered:** MongoDB (rejected — weaker fit for the relational, permission-heavy data model; would need extra work to enforce referential integrity that Postgres gives for free).
- **Notes:** `pgvector` is a possible future addition for semantic workspace retrieval/RAG (Phase 2+), still on Postgres — not a reason to switch databases.

## D-002 — Auth Provider: NextAuth.js (Auth.js) with Google Provider

- **Status:** Confirmed
- **Context:** Product owner asked for a proper login system with Google login. Clerk and Firebase were both considered as managed options, but the owner wants to implement Google OAuth directly inside Next.js rather than depend on a third-party auth service.
- **Decision:** Use **NextAuth.js (Auth.js)** in `apps/web`, configured with the Google Provider. Session data (JWT or DB session via Prisma adapter) is shared with the Express API through a verified token, so `apps/api` can authenticate requests without duplicating auth logic.
- **Alternatives considered:** Clerk (rejected — owner prefers not to depend on a paid third-party auth vendor for a self-owned login system); Firebase Auth (rejected — same reasoning, and weaker native fit for the Next.js + Express split).
- **Notes:** Workspace/organization membership (multi-tenant model) is modeled in Momentix's own `workspaces` / `workspace_members` tables (see `ARCHITECTURE.md` §5), not delegated to the auth provider, since NextAuth.js has no built-in org concept.

## D-003 — Transactional Email: Resend

- **Status:** Confirmed
- **Context:** Momentix needs to send account/billing transactional emails (welcome, verification, plan activated, payment failed, usage warnings) from a dedicated provider, not a personal mailbox.
- **Decision:** Resend.
- **Alternatives considered:** none formally evaluated yet — flagged as a fast, low-friction default for a Next.js/Node stack. Revisit if deliverability issues appear at scale.

## D-004 — Repo Strategy: Monorepo, Modular Monolith

- **Status:** Confirmed
- **Context:** Three deployable surfaces (marketing site, dashboard, API) with shared design tokens and types.
- **Decision:** Single monorepo (`apps/marketing`, `apps/web`, `apps/api`, `packages/*`). Backend starts as a modular monolith, not microservices.
- **Alternatives considered:** Separate repos per app (rejected — adds coordination overhead for a small team/agent setup); microservices from day one (rejected — premature for pre-validation MVP per `PRD.md` §8).

## D-005 — Agent Division of Labor

- **Status:** Confirmed, updated
- **Context:** Project owner will use Claude Code for backend work, Codex for frontend work. Originally a separate dedicated testing agent was planned (see `TESTING.md`), but for this phase the owner wants Codex to own its own testing and the GitHub repo setup/connection as well, rather than waiting on a third agent.
- **Decision:** Backend (API, DB, integrations, business logic) = Claude Code. **Frontend + its own tests + Git/GitHub repo setup for the frontend workspace = Codex**, for this phase. `TESTING.md`'s test layers still apply as the standard Codex tests against (component, integration where relevant, E2E) — Codex is just the one writing and running them for now instead of a separate agent. A dedicated testing agent can be reintroduced later (e.g., once the backend is further along) without changing this structure — just update this entry when that happens.
- **Alternatives considered:** one agent doing everything across frontend+backend (still rejected — backend/DB/secrets stay with Claude Code per `RULES.md` §3); waiting for a third testing agent before starting (rejected — owner wants to move now).

## D-007 — Database Hosting: Supabase (free tier) → Railway

- **Status:** Confirmed
- **Context:** Need a managed PostgreSQL host to start building without local DB administration, at zero cost during MVP/pre-validation.
- **Decision:** Start on **Supabase's free tier** for managed PostgreSQL during Stage 1–5 (Foundation through Beta). Migrate to **Railway** once the app has real usage/traffic that the free tier can't comfortably support.
- **Alternatives considered:** Self-managed Postgres (rejected — unnecessary ops overhead pre-validation); staying on Supabase long-term (left open — revisit once usage data exists, Railway is the current plan but not locked in beyond that).
- **Action needed:** When migrating Supabase → Railway, log a new decision entry (D-0xx) noting the migration date and any schema/connection changes, and update `ARCHITECTURE.md` §8.

## D-008 — AI Access Model: Quota-based, not provider-restricted

- **Status:** Confirmed — supersedes the "Free tier = Groq only" model in the original spec
- **Context:** Original draft restricted the free tier to the cheap/fast provider (Groq) only, with paid tiers unlocking Claude/Perplexity/Gemini/GPT. Product owner instead wants the same page-to-provider routing (see `ARCHITECTURE.md` §7) available to all users, with the difference being a **daily query quota**, not which providers are reachable.
- **Decision:** Every page/feature keeps its assigned provider (per `ARCHITECTURE.md` §7 mapping) regardless of plan. Free-tier workspaces get a small daily quota across all providers; paid tiers get a much higher (or no) daily quota. Enforcement happens in the AI router via `usage_records`, not by provider access control.
- **Alternatives considered:** Original Groq-only free tier (rejected — owner wants free users to experience the real product quality, not a degraded model, just less of it).
- **Notes:** Cost-control rules in `RULES.md` §7 still apply — the hard per-workspace spending cap is what ultimately protects margins, on top of the daily quota.

## D-006 — Domain: momentix.space

- **Status:** Confirmed, with a flagged risk
- **Context:** `momentix.com` is already registered and in active use by an unrelated company (Momentix Capital), and `momentix.ca` is also live. "Momentix"/"Momentic" have prior (including some cancelled) US trademark filings in software/marketing-services categories.
- **Decision:** Proceed with `momentix.space` for MVP/launch — no legal blocker identified for a `.space` TLD, and the existing "Momentix" users are in unrelated sectors (finance/trade publications).
- **Risk noted:** Before any formal trademark registration or significant marketing spend, run a proper trademark clearance search (not just a domain check) to confirm "Momentix" is safe to register as a mark in the relevant software/SaaS class.
