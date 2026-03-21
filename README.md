# Kyle Hansen — Personal AI Copilot Infrastructure

> A production-grade multi-agent system powering all development, trading, academic, and health-tracking work. All active files live in `.github/` — Copilot discovers them automatically.

---

## Structure

```
copilot/
├── .github/
│   ├── copilot-instructions.md   ← GLOBAL memory — auto-read every session
│   ├── agents/                   ← Custom agents (8) — invoke with @agent-name
│   ├── skills/                   ← Skills (10) — discovered automatically
│   ├── instructions/             ← Per-app context (7) — auto-injected by glob
│   └── prompts/                  ← Prompt templates (6) — open from chat
├── memory/                       ← Persistent reference files
├── agents/                       ← (legacy — superseded by .github/agents/)
├── skills/                       ← (legacy — superseded by .github/skills/)
├── per-app/                      ← (legacy — superseded by .github/instructions/)
└── prompts/                      ← (legacy — superseded by .github/prompts/)
```

---

## How To Use This System

### Invoking an Agent
Type `@` in the chat input — all `.github/agents/*.agent.md` files appear as selectable agents with their assigned models. Pick the one matching your task.

### Skills
Skills are loaded automatically when their SKILL.md description matches your request. Copilot reads the full `SKILL.md` as reference before responding. You can also ask explicitly: *"Use the railway-deploy skill"*.

### Per-App Instructions
The `.github/instructions/*.instructions.md` files auto-inject into context when your open file matches the `applyTo` glob. No manual loading needed — just be in the right repo.

### Prompt Templates
Open a `.prompt.md` file or reference it in chat. Fill in the brackets, then send. The frontmatter `mode` controls whether it's `ask` (interactive) or `agent` (autonomous).

### Ending a Session
Update the handoff: *"Update memory/agent-handoff.md with what we decided."*

---

## Agent Roster

| Agent | Model | Best For |
|---|---|---|
| `@orchestrator` | GPT-4.1 | "Help me figure out where to start" — routing and decomposition |
| `@app-builder` | Claude Sonnet 4.6 | Scaffolding and building new apps in the canonical stack |
| `@academic` | Claude Sonnet 4.6 | Masters coursework, papers, AI/ML concepts |
| `@options-analyst` | Gemini 3 Pro | Trading strategies, Greeks, csp_options_app extension |
| `@data-engineer` | Grok Code Fast 1 | Scrapers, ETL, RSS, database schema |
| `@devops` | Grok Code Fast 1 | Railway, Netlify, Docker, DNS, env vars |
| `@health-fitness` | GPT-4.1 Mini | Hevy dashboard, workout features, bloodwork trends |
| `@job-ops` | GPT-5.3-Codex | Job Ops monorepo — AGENTS.md strict compliance |

All agent files: [`.github/agents/`](./.github/agents/)

---

## Skill Library

| Skill | Triggers when you ask about... |
|---|---|
| `xai-grok` | Grok API, model selection, JSON mode, streaming |
| `railway-deploy` | Railway, railway.toml, custom domain, Postgres |
| `netlify-neon` | Netlify Functions, Neon PostgreSQL, PWA |
| `sqlite-drizzle` | Drizzle ORM, SQLite schema, migrations |
| `express-typescript` | Express backend, requestId, SSE, Biome |
| `nextjs-app-router` | Next.js, Server Components, TanStack Query |
| `fastapi-python` | FastAPI, SQLAlchemy async, Alembic |
| `tailwind-radix` | TailwindCSS v4 @theme tokens, CVA, Radix UI |
| `options-trading` | Greeks, CSP/CC/LEAPS, IV Rank, Schwab symbols |
| `academic-writing` | Paper structure, citations, IEEE/APA style |

All skill files: [`.github/skills/`](./.github/skills/)

---

## Per-App Context Files

Auto-injected when working in the matching repo:

| Instructions file | Auto-activates for |
|---|---|
| `ai-pulse.instructions.md` | `ai_pulse/**` |
| `study-planner.instructions.md` | `Study_Planner/**` |
| `job-ops.instructions.md` | `job-ops/**` |
| `csp-options-app.instructions.md` | `csp_options_app/**` |
| `hevy-upload.instructions.md` | `hevy_upload/**` |
| `supplement-explorer.instructions.md` | `supplement_explorer/**` |
| `app-launcher.instructions.md` | `app-launcher/**` |

All instruction files: [`.github/instructions/`](./.github/instructions/)

---

## Prompt Templates

| Prompt | Use Case |
|---|---|
| `new-app` | Bootstrap a new app in your stack |
| `feature-spec` | Spec out a feature before writing code |
| `debug-session` | Structured root-cause debugging |
| `paper-draft` | Start an academic paper or technical doc |
| `strategy-backtest` | Evaluate a new options trading strategy |
| `code-review` | Pre-merge code review with OWASP + API contract check |

All prompt files: [`.github/prompts/`](./.github/prompts/)

---

## Memory Files

| File | Contains |
|---|---|
| [memory/stack-decisions.md](./memory/stack-decisions.md) | Canonical tech stack + why each choice was made |
| [memory/accounts-services.md](./memory/accounts-services.md) | All external accounts, services, API keys used |
| [memory/app-registry.md](./memory/app-registry.md) | All apps: purpose, ports, hosting, status |
| [memory/architecture-decisions.md](./memory/architecture-decisions.md) | ADR log for cross-app decisions |
| [memory/project-context.md](./memory/project-context.md) | Personal context: Masters program, goals, preferences |
| [memory/agent-handoff.md](./memory/agent-handoff.md) | Active cross-agent shared state (update each session) |
└── per-app/                     ← Per-project Copilot instructions
    ├── supplement-explorer.md
    ├── csp-options-app.md
    ├── hevy-upload.md
    ├── job-ops.md
    ├── study-planner.md
    ├── ai-pulse.md
    └── app-launcher.md
```

---

## Design Principles

1. **Agents are specialists** — each knows one domain deeply
2. **Skills are evergreen** — updated as your stack evolves, not per-session
3. **Memory is summaries** — always favor overwriting stale context over appending
4. **Prompts reduce friction** — fill templates rather than re-explaining context
5. **Handoff is explicit** — always write to `agent-handoff.md` when switching agents

---

*Last updated: March 2026*
