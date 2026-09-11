# Prompt for Codex — Momentix Frontend (paste this as-is)

You are the frontend agent for Momentix.space, an AI workspace SaaS. For this phase, you own the frontend end-to-end: UI, your own tests, and the GitHub repo setup for this workspace. You work in phases below — do not skip a phase, and do not move to the next one until I explicitly say "approved."

## Context you must read first

I'm attaching these project files — read all of them before doing anything:
- `PRD.md` — what we're building, MVP scope, tech stack
- `DESIGN.md` — the only source of truth for colors, typography, spacing, components. Follow it exactly, no invented colors/spacing/radii.
- `ARCHITECTURE.md` — especially §14 (Dashboard File Structure) and DESIGN.md §11–12 for sidebar/topbar layout
- `AGENT.md` — your role boundaries for this phase (frontend + your own tests + GitHub setup — no DB schema, no server secrets, no backend code)
- `RULES.md` — especially §1 (English-only text) and §2 (no popup/alert-style errors, only persistent inline error states)
- `TESTING.md` — the test layers and coverage expectations you're responsible for on the frontend side this phase
- `momentix-logo.png` — the final approved logo and color palette. Match it exactly.

## Phase 0 — Repo setup

- `git init` in the project folder if it isn't already a repo.
- Add a proper `.gitignore` for a Next.js/TypeScript project (node_modules, .env*, .next, build output, etc.).
- Scaffold the monorepo structure from `AGENT.md` §1 (at minimum `apps/web`, `apps/marketing`, `packages/ui`, `packages/shared-types` — even if some are empty placeholders for now).
- Make an initial commit.
- I will give you the GitHub remote URL — connect it (`git remote add origin <url>`) and push the initial commit. If I haven't given you a URL yet, stop and ask me for it before pushing (don't create a new repo on my account without asking).

## Phase 1 — Design mockups only (image-based, no real code yet)

Before building the actual Next.js app, **generate visual design images** (not live code — actual generated mockup images) for these screens, in this order, desktop and mobile versions of each:

1. Login / Sign-in screen (Google OAuth button, per DESIGN.md)
2. Dashboard shell — sidebar + topbar (desktop) and bottom nav (mobile), per DESIGN.md §11–12
3. Home page — today's summary cards, AI Suggested Actions, per PRD.md §3
4. Inbox page — email list with classification badges, AI draft panel, Approve/Edit/Reject actions
5. Leads page — Lead Finder filters + lead cards with match score, per DESIGN.md §16
6. One example of an inline error state (per RULES.md §2 — NOT a popup)

Match `momentix-logo.png` exactly for the brand mark and palette.

**Stop after Phase 1.** Present the generated images to me and wait. Do not write any production code until I reply "approved" or give specific change requests. If I ask for changes, regenerate and re-present — do not silently move to Phase 2.

## Phase 2 — Real implementation (only after I approve the images)

Once I approve:
- Build the actual Next.js (App Router) + TypeScript + Tailwind code inside `apps/web/`, following the exact file structure in `ARCHITECTURE.md` §14.
- Use the design tokens from `DESIGN.md` §26 as real CSS variables — no hardcoded hex values in components.
- Use Lucide icons only.
- Wire up NextAuth.js (Google provider) for the login screen per `DECISIONS.md` D-002 — use placeholder/test Google OAuth credentials for now if real ones aren't set up yet, but the actual sign-in flow must work end to end against them.
- Every error state must be inline/persistent per `RULES.md` §2 — never `window.alert`, never a vanish-only toast as the sole error surface.
- All text (labels, errors, empty states) in English only, per `RULES.md` §1.

### Demo data rule — read carefully

**Use demo/mock data for everything (no real backend yet), but every feature must actually work, not just look right.** Concretely:
- Build a local mock data layer (in-memory store or JSON fixtures) that behaves like a real API — with realistic latency/loading states.
- Inbox: classification badges are real (computed from mock data), Approve/Edit/Reject buttons actually change the message's state in the mock store, and the UI reflects it immediately.
- Leads: filters actually filter the mock lead list, "Save Lead" actually adds it to a saved-leads list you can navigate to, "Generate Outreach" actually produces a draft (can be a canned/templated string for now — the point is the button does something real).
- Forms actually validate (required fields, basic format checks) and show real inline errors per `RULES.md` §2 — not decorative placeholders.
- Navigation, empty states, loading states, and error states must all be real and reachable, not just designed for the happy path.
- No dead buttons. If a button exists, it does something — even against mock data.

### Testing (your responsibility this phase)

- Write component tests (React Testing Library) for each page's interactive states: loading, error, empty, success — per `TESTING.md` §1's Component layer.
- Write basic E2E coverage (Playwright) for at least: sign-in flow, inbox approve/edit/reject flow, lead search + save flow — per `TESTING.md` §1's E2E layer.
- Do not merge/commit code where lint, typecheck, or your own tests are failing.
- Commit and push to GitHub as you complete meaningful chunks (not one giant commit at the end) — clear commit messages, English only.

### End of session

Append a dated entry to `MEMORY.md` — what you built, what's pending, what's mock vs real, and anything the backend agent (Claude Code) needs to know once it starts wiring the real API.

## Ground rules for all phases

- Stay inside `apps/web/`, `apps/marketing/`, `packages/ui/`, `packages/shared-types/` — never touch `apps/api/`, `prisma/`, or any real secrets.
- Match `momentix-logo.png` exactly for the logo/brand mark — do not redesign it.
- If something in `DESIGN.md` or `ARCHITECTURE.md` is ambiguous, ask me rather than guessing.
- If you need a GitHub remote URL and don't have one, ask — don't create a repo on my behalf without confirmation.
