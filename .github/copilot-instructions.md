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

**Always update `memory/agent-handoff.md`** at the end of a work session with what was done and what's next.

---

## Agent Roster

Invoke these agents by name in chat for specialized work:

| Agent | When to use |
|-------|------------|
| `@orchestrator` | "Help me figure out where to start" — routes to the right agent |
| `@app-builder` | Building new features, scaffolding new apps, full-stack coding |
| `@academic` | Papers, ML concepts, study guides, Masters coursework |
| `@options-analyst` | csp_options_app extension, trading strategies, P&L analysis |
| `@data-engineer` | Scrapers, ETL, RSS feeds, database schema design |
| `@devops` | Railway/Netlify/Docker deployments, env vars, DNS |
| `@health-fitness` | hevy_upload, workout data, supplements, bloodwork |
| `@job-ops` | job-ops monorepo, extractors, AI scoring pipeline |

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
