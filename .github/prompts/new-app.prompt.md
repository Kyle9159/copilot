---
agent: ask
description: Bootstrap a new full-stack app in Kyle's exact tech stack
---

# New App Scaffold

Answer the following to scaffold a new app. Fill in each `[BRACKET]`.

---

**App name**: [short-name, e.g., "budget-tracker"]

**Purpose in one sentence**: [What does this app do?]

**Hosting target**:
- [ ] Railway (full-stack with backend/DB)
- [ ] Netlify (static/PWA with serverless functions)
- [ ] Vercel (pure SSG, no backend)
- [ ] Local / Docker (self-hosted)

**Backend needed?**:
- [ ] Yes — TypeScript/Express
- [ ] Yes — Python/FastAPI
- [ ] No — frontend only

**Database**:
- [ ] SQLite + Drizzle (local/Docker)
- [ ] Neon PostgreSQL (Netlify)
- [ ] Railway PostgreSQL (Railway)
- [ ] None

**AI features?**: [Yes/No — if yes, describe what]

**Port (local dev)**: [Choose an unused port from the app-registry]

---

## Agent Instructions

Using the selections above, act as the **App Builder agent** and:

1. Read `memory/stack-decisions.md` and `memory/app-registry.md` first
2. Generate the complete folder structure
3. Write the key files: `package.json`, entry points, `biome.json`, `.env.example`, `README.md`, and `docker-compose.yml` if needed
4. Set up the database schema if applicable
5. Implement a basic `/health` endpoint
6. Register the new app in `memory/app-registry.md`
7. List next steps for adding features

Use the relevant skills: `xai-grok`, `railway-deploy` or `netlify-neon`, `sqlite-drizzle`, `express-typescript` or `fastapi-python`, `nextjs-app-router`, `tailwind-radix`.
