---
description: Context for AI Pulse — AI news aggregator with Grok summaries. FastAPI backend + Next.js frontend on Railway. Use when working in the ai_pulse directory.
applyTo: "**/ai_pulse/**"
---

# AI Pulse — Project Context

AI Pulse is a personal AI news aggregator that scrapes RSS feeds, summarizes articles with Grok, scores them for engineering relevance, and displays them in a clean feed.

## Architecture
- **Backend**: FastAPI + Uvicorn on Railway. Async SQLAlchemy 2 + asyncpg.
- **Database**: PostgreSQL (Railway-managed). Alembic migrations.
- **Scheduler**: APScheduler — daily refresh at 06:00 (configurable timezone).
- **Frontend**: Next.js 15 + TailwindCSS on Railway. Separate service.
- **AI**: xAI Grok (`grok-3-mini-fast` for bulk summaries, `grok-3` for quality).
- **DNS**: GoDaddy CNAME → Railway hostname.
- **Agents**: Use `@app-builder` for features, `@devops` for Railway deploy.

## Key Files
```
backend/app/main.py          — FastAPI factory + lifespan + CORS
backend/app/config.py        — pydantic-settings Config
backend/app/services/        — Scraper, refresh orchestrator
backend/app/routers/         — admin, posts, ratings, cron
backend/alembic/             — Migrations
frontend/app/page.tsx        — Main feed page
```

## Environment Variables
```bash
DATABASE_URL=postgresql://...   # Railway provides
XAI_API_KEY=
ADMIN_PASSWORD=                 # Protects POST /api/admin/refresh
NEXT_PUBLIC_API_URL=            # Frontend → Backend URL
```

## Critical Patterns
- Always use `process.env.PORT` — Railway injects it, never hardcode 8000
- Admin endpoint: `POST /api/admin/refresh` requires `x-admin-password` header
- CORS configured for Railway frontend URL + `http://localhost:3003`
- Scraper uses `feedparser` + sliding `since_hours` window

## Local Dev
```bash
# Backend
cd backend && source .venv/bin/activate
uvicorn app.main:app --reload --port 8000

# Frontend
cd frontend && npm run dev -- --port 3003
```

## Common Tasks
- **Trigger manual refresh**: `POST /api/admin/refresh` with header
- **Run migrations**: `cd backend && alembic upgrade head`
- **Add RSS feed**: edit `backend/app/services/scraper.py` feeds list
- **Add route**: follow pattern in `backend/app/routers/posts.py` + register in `main.py`
