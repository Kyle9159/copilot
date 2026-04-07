# Kyle Hansen — Global Copilot Instructions

You are working with Kyle Hansen's personal development workspace. This file is auto-read by GitHub Copilot on every session. Use it to stay grounded in the established tech stack, app portfolio, and personal context.

---

## Who Kyle Is

- Full-stack developer building AI-augmented apps
- Starting **MS in AI Engineering / Software Engineering** in April 2026
- Active options trader running automated CSP/CC strategies
- Workspace: macOS, VS Code, GitHub Copilot as primary AI layer

---

## The App Portfolio

| App | Stack | Hosting | Port | Purpose |
|-----|-------|---------|------|---------|
| `ai_pulse` | FastAPI + Next.js 15 | Railway | BE:8000 FE:3003 | AI news aggregator |
| `study_planner` | Express + React Vite + SQLite | Local | 5175 | Academic study tool for Masters |
| `job-ops` | Express + React Vite + SQLite + Docker | GHCR/Docker | 5173 | Automated job hunting platform |
| `csp_options_app` | Python Flask + Schwab API | Local | 5000 | Options trading automation |
| `hevy_upload` | Netlify Functions + Neon PostgreSQL | Netlify | — | Workout dashboard + health tracking |
| `supplement_explorer` | Next.js 16 SSG | Vercel | — | Supplement research database |
| `app-launcher` | Express + Vanilla JS | Local (LaunchAgent) | 4321 | macOS control panel for all apps |

---

## Canonical Tech Stack

**Always use these. Never introduce alternatives without explicit request.**

| Layer | Choice |
|-------|--------|
| FE framework | Next.js 15/16 App Router + React 19 |
| FE styling | TailwindCSS v4 (CSS `@theme {}` tokens, OKLCh colors) |
| FE components | Radix UI + CVA + clsx + tailwind-merge |
| FE icons | Lucide React |
| FE state | TanStack Query v5 |
| FE forms | React Hook Form + Zod |
| BE (TS) | Express.js + TypeScript |
| BE (Python) | FastAPI + Pydantic v2 + pydantic-settings |
| ORM (TS) | Drizzle ORM + better-sqlite3 (SQLite) or pg (PostgreSQL) |
| ORM (Python) | SQLAlchemy 2 async + asyncpg |
| Migrations (TS) | Drizzle Kit |
| Migrations (PY) | Alembic |
| Linting/formatting | Biome (replaces ESLint + Prettier) |
| IDs | `@paralleldrive/cuid2` |
| AI provider | xAI Grok — `https://api.x.ai/v1`, openai-compatible SDK |
| Hosting (full-stack) | Railway |
| Hosting (static/PWA) | Netlify + `@netlify/neon` |
| Hosting (SSG) | Vercel |
| Self-hosted | Docker + Docker Compose |
| DNS | GoDaddy (CNAME only, never host here) |

---

## API Response Contract (non-negotiable for /api/* routes)

```typescript
// Success
{ ok: true, data: T, meta?: { requestId: string } }

// Error
{ ok: false, error: { code: string, message: string, details?: unknown }, meta: { requestId: string } }
```

Status codes: `400 INVALID_REQUEST` | `401 UNAUTHORIZED` | `403 FORBIDDEN` | `404 NOT_FOUND` | `408 REQUEST_TIMEOUT` | `409 CONFLICT` | `422 UNPROCESSABLE_ENTITY` | `500 INTERNAL_ERROR` | `502 UPSTREAM_ERROR` | `503 SERVICE_UNAVAILABLE`

---

## Memory & Context Files

When you need persistent context, read these files in `memory/`:

| File | Contains |
|------|---------|
| `memory/stack-decisions.md` | Canonical tech choices with reasoning |
| `memory/app-registry.md` | All apps: ports, hosting, key files, status |
| `memory/accounts-services.md` | External accounts: xAI, Railway, Netlify, Schwab, etc. |
| `memory/project-context.md` | Kyle's personal goals, aesthetic preferences, Masters program |
| `memory/architecture-decisions.md` | ADR log for important decisions |
| `memory/agent-handoff.md` | Cross-session active work state — update at end of each session |
| `memory/cost-reference.md` | Model pricing, token tier estimates, per-agent cost profiles |

**Always update `memory/agent-handoff.md`** at the end of a work session with what was done and what's next.

---

## Agent Roster

Invoke these agents by name in chat for specialized work:

| Agent | Model | When to use |
|-------|-------|------------|
| `@orchestrator` | gpt-4.1 | "Help me figure out where to start" — routes to the right agent |
| `@product-planner` | gpt-4.1 | PRD, roadmaps, user stories, feature specs, effort estimates — always before `@app-builder` |
| `@app-builder` | claude-sonnet-4-6 | Building new features, scaffolding new apps, full-stack coding |
| `@academic` | claude-sonnet-4-6 | Papers, ML concepts, study guides, Masters coursework |
| `@options-analyst` | claude-sonnet-4-6 | csp_options_app extension, trading strategies, P&L analysis |
| `@data-engineer` | gpt-4.1-mini | Scrapers, ETL, RSS feeds, database schema design |
| `@devops` | gpt-4.1-mini | Railway/Netlify/Docker deployments, env vars, DNS |
| `@health-fitness` | gpt-4.1-mini | hevy_upload, workout data, supplements, bloodwork |
| `@job-ops` | gpt-4.1 | job-ops monorepo, extractors, AI scoring pipeline |
| `@growth` | claude-sonnet-4-6 | App monetization, SEO, pricing strategy, user acquisition, revenue models |

---

## Output Efficiency Rules

All agents follow these rules on every response. No exceptions.

1. **Never restate the user's request** — start directly with output or questions
2. **No opener fillers** — never begin with "Sure!", "Great idea!", "Of course!", "I'll now..."
3. **No post-completion summaries** — do not recap what was just done at the end of a response
4. **Reference code by path, not by reprinting** — cite `file.ts:functionName` instead of re-pasting existing code unless a full rewrite is being shown
5. **Tables for comparisons, bullets for lists** — no numbered prose paragraphs when structure works better
6. **Skip explaining obvious stack choices** — don't justify using Express, Drizzle, Zod, etc. unless a deviation is being made
7. **Omit file-header block comments** from code output unless explicitly requested
8. **Show only changed sections** for multi-file edits — include 3–5 lines of context around each change, never the full file
9. **One-sentence completion acknowledgment** — after implementing, a single line confirming done; no re-listing what changed
10. **Hand off to the cheaper agent** when the plan block identifies one — don't do another agent's work at higher cost

---

## Pre-Implementation Planning Standard

All agents MUST follow this flow for any non-trivial request (anything beyond a single-line fix).

### Step 1 — Clarifying Questions (before the plan)

If the request has ambiguities where a wrong assumption would waste tokens or produce a wrong plan, ask first in one grouped block:

```
Before I finalize the plan, a few quick questions:

1. **Scope**: Should this cover X only, or also Y?
2. **DB**: New table or extend existing?
3. **UI**: New page or embed in existing view?
4. **Auth**: Admin-gated or public?

Reply with answers or "skip" to let me assume and proceed.
```

- Ask in one shot — never follow up with more questions after answers are given
- For trivially obvious tasks (typo fix, single-line change), skip questions entirely
- If "skip" is received, list assumptions made at the top of the plan block

### Step 2 — Plan Block (output before any code)

```
## Plan: [Feature Name]

| # | Step | Files Affected | Agent | Model | Est. Cost |
|---|------|----------------|-------|-------|-----------|
| 1 | Description of step | path/to/file.ts | @self | claude-sonnet-4-6 | $0.04 |
| 2 | Description of step | path/to/other.ts | @app-builder | claude-sonnet-4-6 | $0.105 |

**Total estimated cost**: $0.145
**Recommended model**: claude-sonnet-4-6 — [one-line reason why this model for this task]
**Complexity**: Small / Medium / Large
**Assumptions made**: [only if "skip" was used; omit otherwise]

⏸ Waiting for approval. Reply "go" to proceed, or suggest changes.
```

### Step 3 — Implementation (only after explicit "go")

No agent writes implementation code before receiving "go". Hard stop is universal across all agents.

**Cost estimation reference**: See `memory/cost-reference.md` for model pricing and token tier estimates.

---

## Logging Rules (all projects)

- Use structured logger — never `console.log` in production paths
- Always include: `requestId`, `route`, `status` in request logs
- Redact before logging: `authorization`, `cookie`, `password`, `secret`, `token`, `apiKey`
- Propagate `requestId` into all async flows

---

## Code Style Rules

- TypeScript strict mode — no `any` unless documented with a comment explaining why
- Zod for all external input validation
- Shared types in `shared/types.ts` accessible by both client and server
- Small focused files over large monoliths
- Explicit over clever: clear names, no magic numbers
- Always provide `.env.example` when creating new services

---

## Design Aesthetic

- Dark themes: near-black background (`oklch(10% 0 0)`) with cyan accent (`#00d4ff`)
- Glassmorphism cards: `background: rgba(255,255,255,0.05); backdrop-filter: blur(10px)`
- Data tables for precision, charts for trends
- Mobile-first (hevy dashboard is used at the gym from iPhone)
