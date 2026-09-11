# RULES.md — Non-Negotiable Rules

These apply to every agent (Claude Code, Codex, testing agents) and every piece of code, copy, and UI shipped in this project. Rules here override convenience or "it's faster this way" shortcuts. If a rule seems to block a task, stop and flag it in `MEMORY.md` rather than working around it silently.

## 1. Language Rules

- **All user-facing text must be in English** — this includes every error message, validation message, toast, empty state, API error body (`message` field), log message meant for support/debugging, and UI copy. No Roman Urdu, no mixed-language strings, no transliteration, anywhere the user or a developer reading logs will see it.
- Code comments, commit messages, and documentation are also English-only, for consistency across agents.

## 2. Error UI Rules

- **No native browser popups for errors** — no `window.alert`, no `window.confirm` used as an error surface.
- **No dismiss-and-vanish-only toast as the sole error indicator** for anything that affects user data or a submitted action. A toast may supplement, but the actual error state must persist inline (form field error, banner, inline card) using the Error/Danger tokens from `DESIGN.md` §4 until the user resolves it or explicitly dismisses it.
- Every error must tell the user what happened and, where possible, what to do next — never a raw stack trace or generic "Something went wrong" with no context.
- Network/loading errors get a retry affordance, not a dead end.

## 3. Security Rules

- Secrets (AI provider keys, Stripe keys, Clerk secret key, Resend API key, DB credentials, OAuth client secrets) live only in `.env` on the server side. Never in client code, never committed to git, never logged.
- `.env.example` is kept up to date with every variable name (no real values) whenever a new secret is introduced.
- All third-party account connections (Gmail, etc.) use OAuth — Momentix never asks for or stores a user's email password.
- Webhook endpoints (Stripe, Clerk) always verify signatures before trusting payload contents.
- Every query and mutation enforces workspace isolation — a user can never read or write another workspace's data, even by guessing an ID.

## 4. Action Safety Rules

- Low-risk AI actions (classify, summarize, draft, score) may run automatically.
- Medium-risk actions (create internal task, tag, update contact metadata, save lead) run automatically but stay visibly logged/undoable.
- High-risk actions (send external email, modify external calendar, delete data, any consequential third-party action) **always require explicit user approval** before execution — no exceptions, no "auto-send" shortcuts until a dedicated, opt-in, audited feature is built for it in Phase 2+.
- Every high-risk action writes an audit log entry.

## 5. Design Rules

- All UI is built to `DESIGN.md` tokens exactly — colors, spacing, radius, typography. No inline arbitrary hex values, no one-off spacing numbers.
- One icon family only: Lucide.
- Lead scores and status always show a label/number, never color alone.
- AI-related UI clearly communicates what the AI is about to do before it does it.

## 6. Process Rules

- Any new architectural or product decision gets a short entry in `DECISIONS.md` before or alongside the code that implements it — don't let a decision live only in someone's head or a chat log.
- `MEMORY.md` gets a dated entry at the end of every work session: what changed, what's pending, what the next agent needs to know.
- No merge without passing lint, typecheck, and the tests defined in `TESTING.md`.
- Scope discipline: MVP is the 3 workflows in `PRD.md` §3. Anything else proposed mid-build gets logged as a Phase 2/3 idea, not built early.

## 7. Cost Rules

- All AI calls are metered per workspace via `usage_records`.
- Cheap/fast models (Groq) handle high-frequency, low-complexity tasks; expensive models (Claude, GPT, Perplexity) are reserved for tasks where quality directly affects the user's business outcome.
- A hard spending cap per workspace applies regardless of plan.
- Never promise unlimited expensive AI usage until real production cost per active user has been measured.
