# Tech Stack Decisions

> Canonical record of technology choices and the reasoning behind each. Update when a decision changes — do not append old decisions.

---

## Frontend

| Tech | Choice | Why |
|---|---|---|
| Framework | **Next.js 15/16 (App Router)** | RSC, ISR/SSG/SSR flexibility, Railway + Vercel deploy targets, App Router is the future |
| Alt framework | **Vite + React** | For desktop/local apps (Study Planner, job-ops dev) — faster HMR, no Node server needed in dev |
| Styling | **TailwindCSS v4** | CSS-native config, zero-runtime, v4 is massive improvement on v3 |
| Components | **Radix UI** | Accessible headless primitives, minimal footprint, pairs perfectly with Tailwind |
| Icons | **Lucide React** | Clean, consistent, tree-shakeable |
| Class composition | **CVA + clsx + tailwind-merge** | Best-in-class pattern for variant-driven components |
| State (server) | **TanStack Query v5** | Auto-caching, background refetch, devtools; no Redux needed |
| Forms | **React Hook Form + Zod** | Minimal re-renders, type-safe validation, resolver pattern |
| Linting | **Biome** | Single tool replaces ESLint + Prettier, order of magnitude faster |

---

## Backend — TypeScript

| Tech | Choice | Why |
|---|---|---|
| Framework | **Express.js** | Minimal, well-understood, sufficient for these apps |
| ORM | **Drizzle ORM** | Type-safe, migration-first, no magic, fast |
| Database | **SQLite (better-sqlite3)** | Zero-infra for self-hosted/Docker apps; sync API simplifies code |
| ID generation | **@paralleldrive/cuid2** | Collision-resistant, URL-safe, better than UUID for distributed insert |
| Type sharing | `shared/types.ts` | Single source of truth for client+server types in monorepo |

---

## Backend — Python

| Tech | Choice | Why |
|---|---|---|
| Framework | **FastAPI** | Async-first, auto-OpenAPI docs, Pydantic-native, modern |
| ORM | **SQLAlchemy 2 async** | Battle-tested, supports both sync (Alembic) and async (app) use |
| Migrations | **Alembic** | Standard for SQLAlchemy; autogenerate from model changes |
| Config | **pydantic-settings** | Type-safe env var loading, consistent with Pydantic models |
| HTTP client | **httpx** | Async-first, cleaner API than `requests`, session pooling |
| Scheduler | **APScheduler** | Stateful async scheduling without a separate Redis/Celery stack |

---

## Databases

| Use Case | Database | Why |
|---|---|---|
| Self-hosted / Docker apps | **SQLite + Drizzle** | Zero infra, no auth complexity, volume-mounted |
| Netlify apps | **Neon PostgreSQL** | Native Netlify integration, auto-injected `NEON_DATABASE_URL` |
| Railway apps | **Railway PostgreSQL or Neon** | Railway-managed Postgres or connect to Neon via env var |
| Caching (Python) | **lru_cache / functools** | In-process caching sufficient; no Redis for current scale |

---

## AI / LLM

| Use Case | Provider | Model |
|---|---|---|
| Primary AI (all apps) | **xAI Grok** | `grok-3-mini-fast` (bulk), `grok-3` (quality) |
| Alternative (Study Planner) | **GitHub Models** | GPT-4o, Claude Sonnet, Gemini — via one API key |
| Alternative (Job Ops) | OpenAI / OpenRouter / Ollama | User-configurable, multi-provider support |

**Decision**: xAI as primary because: (1) already used across 4/7 apps, (2) competitive quality, (3) single API key to manage, (4) `openai` SDK compatible (drop-in).

---

## Hosting / Infrastructure

| App | Platform | Why |
|---|---|---|
| AI Pulse | **Railway** (FE + BE) | Full-stack with PostgreSQL, custom domain |
| Hevy Upload | **Netlify** | Static PWA + serverless functions + Neon integration |
| Supplement Explorer | **Vercel** | Pure SSG, Next.js native deploy |
| Job Ops | **Docker** (self-hosted) | Data privacy, port 3005, volume for SQLite |
| Study Planner | **Local / Docker** | Personal tool, SQLite, runs via app-launcher |
| CSP Options App | **Local (Flask)** | Brokerage data never leaves local machine |
| App Launcher | **Local (macOS LaunchAgent)** | Meta control plane, macOS-native |

---

## DNS

- **GoDaddy**: DNS only. CNAME records pointing to Railway/Netlify hostnames.
- Never host apps on GoDaddy directly.

---

## Auth Strategy

- **Admin endpoints**: simple shared secret in `ADMIN_PASSWORD` env var (current pattern)
- **User auth**: not needed yet for personal apps — add when multi-user is required
- **Webhook security**: `x-migrate-token` header pattern from Netlify migrations

---

## What We Consciously Avoid

| What | Why |
|---|---|
| Prisma | Drizzle is lighter, faster, and more TypeScript-friendly |
| Redux / Zustand | TanStack Query + context is sufficient |
| ESLint + Prettier | Biome replaces both with better performance |
| MongoDB | No document store needs; relational fits all current data |
| Redis | In-process caching (lru_cache, Map) is sufficient at current scale |
| Celery / BullMQ | APScheduler (Python) + threading handles current async needs |
| Next.js API Routes for heavy workloads | Use Express for anything with long-running tasks or SSE |
