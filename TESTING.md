# TESTING.md — Testing Strategy

Testing is owned by a **dedicated testing agent**, separate from the backend agent (Claude Code) and frontend agent (Codex). The testing agent writes and maintains tests and flags gaps back to the owning agent — it does not silently patch business logic to make a test pass.

## 1. Test Layers

| Layer | Scope | Owner | Tooling |
|---|---|---|---|
| Unit | Pure functions, utilities, AI-router logic, lead-scoring logic, validation schemas | Testing agent, reviewed against code from Claude Code / Codex | Vitest (or Jest) |
| API / Integration | Express routes, DB queries via Prisma, webhook handlers (Stripe/Clerk), OAuth callback flows | Testing agent, against `apps/api` | Supertest + Vitest, test DB (Postgres, isolated schema/container) |
| Component | UI components in `packages/ui` and `apps/web` — rendering, states (loading/error/empty), design-token compliance | Testing agent, against `apps/web` | React Testing Library + Vitest |
| End-to-End | Full user flows: signup → connect Gmail (mocked) → classify → approve/send draft; lead search → score → save → generate outreach; follow-up detection → approve | Testing agent | Playwright |

## 2. What Must Always Be Tested

- **Action safety** (RULES.md §4): any high-risk action (send email, delete data, modify calendar) has a test proving it cannot execute without explicit approval.
- **Workspace isolation** (RULES.md §3): a test proving workspace A can never read/write workspace B's data.
- **Auth flows**: sign up, sign in, Google OAuth, session handling, protected route redirects.
- **Billing**: Stripe checkout success/failure, webhook idempotency, plan unlock/lock on subscription change.
- **AI router fallback**: primary provider failure correctly falls back to the secondary provider.
- **Error UI compliance** (RULES.md §2): error states render as persistent inline UI, not a native `alert()`, and disappear only on resolution/explicit dismissal — covered by component tests.
- **Usage metering**: AI actions correctly increment `usage_records` and respect per-plan/per-workspace caps.

## 3. CI Gate

No merge to main without, in order:
1. `npm run lint` passes
2. `npm run typecheck` passes
3. `npm run test` (unit) passes
4. `npm run test:integration` passes
5. `npm run test:e2e` passes on the core flows in §1
6. No secrets or `.env` values present in the diff

## 4. Coverage Targets (guideline, not a hard gate at MVP stage)

- Backend business logic (AI router, lead scoring, billing logic): aim for high coverage — this is where bugs cost money or trust.
- UI components: cover all interactive states (loading, error, empty, success) rather than chasing a percentage number.
- E2E: cover the 3 MVP workflows end to end (AI Inbox, Lead Finder, Follow-ups/CRM) before anything else.

## 5. Test Data / Fixtures

- Use seeded, clearly-fake fixture data (fake companies, fake emails) — never real user data or real third-party credentials in tests.
- Mock external providers (Gmail API, Google Places, Stripe, AI providers) at the integration/E2E layer; only hit real sandboxes/staging keys in a dedicated, gated CI job if needed later — not on every PR.

## 6. Reporting Back

When the testing agent finds a bug or a gap in coverage, it:
1. Does **not** fix business logic itself.
2. Adds a failing test that reproduces the issue (or documents the missing case).
3. Notes it in `MEMORY.md` so the owning agent (Claude Code for backend, Codex for frontend) picks it up next session.
