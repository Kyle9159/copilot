---
description: Context for Job Ops — automated job hunting platform. Monorepo with TypeScript orchestrator + extractors. Use when working in the job-ops directory. ALWAYS follow AGENTS.md.
applyTo: "**/job-ops/**"
---

# Job Ops — Project Context

Self-hosted job hunting automation. Scrapes 7+ job boards, AI-scores job fit (0–100), tailors resumes, tracks application replies.

**ALWAYS read and follow `job-ops/AGENTS.md`** before touching any route or service.
**Agent**: Use `@job-ops` for all monorepo work.

## Architecture
- **Monorepo**: npm workspaces — orchestrator, shared, extractors/*
- **Frontend**: React + Vite + TypeScript + TailwindCSS v4 + Radix UI (port 5173)
- **Backend**: Express.js + TypeScript (port 3001)
- **Database**: SQLite + Drizzle ORM at `./data/db.sqlite`
- **Docker**: Port 3005 external. Image: `ghcr.io/dakheera47/job-ops`

## Key Files
```
orchestrator/src/server/api/         — Express route handlers
orchestrator/src/server/services/    — Pipeline, scoring, resume tailoring
orchestrator/src/server/infra/       — logger.ts, sse.ts, db/
orchestrator/src/client/             — React Vite UI
shared/types.ts                      — Shared types
AGENTS.md                            — CRITICAL: coding standards
```

## Non-Negotiable Standards (from AGENTS.md)
1. API responses: `{ ok, data/error, meta.requestId }`
2. Honor `x-request-id` header; always return it
3. Log via `infra/logger` — never `console.log`
4. SSE via `infra/sse.ts` — never raw SSE setup
5. Sanitize error details before logging/returning

## CI Checks (run before finishing ANY change)
```bash
./orchestrator/node_modules/.bin/biome ci .
npm run check:types:shared
npm --workspace orchestrator run check:types
npm --workspace orchestrator run test:run
```

## Local Dev
```bash
npm install
npm --workspace orchestrator run dev
```
