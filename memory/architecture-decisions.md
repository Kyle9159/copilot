# Architecture Decisions (ADR Log)

> Record of significant architecture choices. Add new entries at the top. Never delete old ones — they document the reasoning at the time.

---

## Template

```
## ADR-NNN: [Title]
**Date**: YYYY-MM-DD
**Status**: Proposed | Accepted | Deprecated | Superseded by ADR-NNN
**Context**: What situation led to this decision?
**Decision**: What was decided?
**Consequences**: What are the trade-offs?
```

---

## ADR-006: Biome as Single Linting/Formatting Tool
**Date**: 2025-Q4  
**Status**: Accepted  
**Context**: ESLint + Prettier configs were getting out of sync across projects, adding setup overhead for each new app.  
**Decision**: Standardize on Biome for all TypeScript/JavaScript projects. Single `biome.json` per project.  
**Consequences**: Faster CI, simpler setup. Minor: some ESLint plugins have no Biome equivalent (acceptable trade-off).

---

## ADR-005: xAI Grok as Primary LLM Provider
**Date**: 2025  
**Status**: Accepted  
**Context**: Multiple apps needed LLM capabilities. Evaluating OpenAI, Anthropic, and xAI.  
**Decision**: xAI Grok as default across all apps. Use `openai` SDK with `baseURL: 'https://api.x.ai/v1'`.  
**Consequences**: Single API key to manage, competitive quality, OpenAI-compatible client. Trade-off: less ecosystem tooling vs OpenAI.

---

## ADR-004: SQLite + Drizzle for Local/Docker Apps
**Date**: 2025  
**Status**: Accepted  
**Context**: Study Planner and Job Ops are single-user local apps. PostgreSQL adds infra complexity for no benefit at this scale.  
**Decision**: SQLite + Drizzle ORM. Volume-mount the `data/` directory in Docker for persistence.  
**Consequences**: Zero infra cost. Fast queries. No connection pool needed. Trade-off: can't scale horizontally (not a concern).

---

## ADR-003: Netlify + Neon for Hevy Upload
**Date**: 2024  
**Status**: Accepted  
**Context**: Hevy dashboard needed persistent storage (workout data) without a running server.  
**Decision**: Netlify Functions (serverless) + Neon PostgreSQL via `@netlify/neon`. Zero-config DB injection.  
**Consequences**: No always-on server cost. Neon auto-pauses when idle. Trade-off: cold start latency (~100ms) on first function call.

---

## ADR-002: Railway for Full-Stack Apps with Backends
**Date**: 2024  
**Status**: Accepted  
**Context**: AI Pulse needed a persistent FastAPI backend + scheduled jobs. Vercel serverless functions have execution limits.  
**Decision**: Railway for any app with a long-running backend or scheduler. Two services (FE + BE) in one Railway project.  
**Consequences**: Simple deployment, internal networking between services, managed Postgres option. Trade-off: $5-20/month cost vs Vercel free tier.

---

## ADR-001: App Launcher as Local Dev Control Plane
**Date**: 2024  
**Status**: Accepted  
**Context**: Running 5+ local apps meant remembering ports, start commands, venv activations.  
**Decision**: Build a meta-control-plane (app-launcher) that starts/stops all apps from a Chrome app window. Register as macOS LaunchAgent.  
**Consequences**: One place to see all app statuses, logs, and control them. Trade-off: another app to maintain.

---

## Pending Decisions

- [ ] **Should Study Planner move to Railway?** — Currently local-only. Would enable access from other devices. Consideration: SQLite → PostgreSQL migration needed.
- [ ] **Should CSP Options App get a proper DB?** — Currently Google Sheets + JSON. More complex strategies may need SQLite trading journal.
- [ ] **Multi-user auth strategy?** — If any app goes public-facing, need an auth layer. Candidates: Clerk, Auth0, Lucia.
