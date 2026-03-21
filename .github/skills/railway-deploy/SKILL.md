---
name: railway-deploy
description: "Use when: deploying an app to Railway, configuring Railway environment variables, setting up a custom domain via GoDaddy, writing railway.toml, adding a PostgreSQL database on Railway, or debugging Railway build failures."
---

# Railway Deployment

> Used by: @devops, @app-builder

## Setup

```bash
npm install -g @railway/cli
railway login
railway link          # link to existing project
railway init          # create new project
```

## Service Types

| Service | Nixpacks Detects | Start Command |
|---------|-----------------|---------------|
| Next.js | package.json with "next" | `npm run start` |
| Express/Node | package.json | `node dist/index.js` |
| FastAPI/Python | requirements.txt with fastapi | `uvicorn app.main:app --host 0.0.0.0 --port $PORT` |

**Critical**: Railway injects `PORT` — app must use `process.env.PORT` or `$PORT`.

## Environment Variables

```bash
railway variables set XAI_API_KEY=your-key
railway variables set KEY1=val1 KEY2=val2
railway variables set --from-file .env   # sync from local
railway variables                        # view all
```

## PostgreSQL Database

```bash
railway add --plugin postgresql
railway variables           # find DATABASE_URL
railway run alembic upgrade head         # Python migrations
railway run npm run db:migrate           # Node migrations
```

## Custom Domains

1. Railway service → Settings → Custom Domains → Add Domain
2. Copy the Railway-provided hostname
3. GoDaddy DNS:
   ```
   Type: CNAME
   Host: app
   Value: [railway-hostname].railway.app
   TTL: 300 (increase to 3600 after stable)
   ```
4. Railway auto-provisions HTTPS via Let's Encrypt (2–5 min)

## railway.toml

```toml
[build]
builder = "nixpacks"

[deploy]
startCommand = "uvicorn app.main:app --host 0.0.0.0 --port $PORT"
healthcheckPath = "/health"
healthcheckTimeout = 30
restartPolicyType = "on_failure"
restartPolicyMaxRetries = 3
```

## Deploy Checklist

- [ ] `$PORT` env var honored in startup command
- [ ] All required env vars set in Railway dashboard
- [ ] Custom domain CNAME pointed from GoDaddy
- [ ] Health check endpoint responding (`/health`)
- [ ] Database migrations run as deploy step (not in main process)
- [ ] `railway logs` checked after deploy for errors
