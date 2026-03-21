---
name: netlify-neon
description: "Use when: deploying a static or PWA app to Netlify, writing Netlify serverless functions in ESM format, connecting to Neon PostgreSQL from a Netlify function, or configuring netlify.toml redirects."
---

# Netlify + Neon PostgreSQL

> Used by: @devops, @health-fitness, @app-builder

## netlify.toml (required)

```toml
[build]
  command = "npm run build"
  publish = "."           # or "out" for Next.js static export

[functions]
  directory = "netlify/functions"
  node_bundler = "esbuild"

[[redirects]]
  from = "/api/*"
  to = "/.netlify/functions/:splat"
  status = 200
```

## Neon Database

```bash
npm install @netlify/neon
```

```javascript
// netlify/functions/my-endpoint.mjs
import { neon } from '@netlify/neon';

export default async function handler(req, context) {
  // NEON_DATABASE_URL auto-injected by Netlify integration
  const sql = neon(process.env.NEON_DATABASE_URL);
  const rows = await sql`SELECT * FROM workout_sessions ORDER BY created_at DESC LIMIT 10`;
  return new Response(JSON.stringify({ ok: true, data: rows }), {
    status: 200,
    headers: { 'Content-Type': 'application/json' }
  });
}
```

## Function Boilerplate (ESM required)

```javascript
// netlify/functions/example.mjs
import { neon } from '@netlify/neon';

export default async function handler(req, context) {
  const url = new URL(req.url);
  const method = req.method;

  if (method === 'OPTIONS') {
    return new Response(null, {
      status: 204,
      headers: {
        'Access-Control-Allow-Origin': '*',
        'Access-Control-Allow-Methods': 'GET, POST, OPTIONS',
        'Access-Control-Allow-Headers': 'Content-Type',
      }
    });
  }

  if (method === 'GET') {
    const sql = neon(process.env.NEON_DATABASE_URL);
    const rows = await sql`SELECT * FROM my_table`;
    return new Response(JSON.stringify({ ok: true, data: rows }), {
      headers: { 'Content-Type': 'application/json', 'Access-Control-Allow-Origin': '*' }
    });
  }

  if (method === 'POST') {
    const body = await req.json();
    // validate + insert
    return new Response(JSON.stringify({ ok: true }), {
      status: 201,
      headers: { 'Content-Type': 'application/json' }
    });
  }
}
```

## DDL Pattern (run-once migration function)

```javascript
// netlify/functions/migrate.mjs
import { neon } from '@netlify/neon';

export default async function handler(req, context) {
  const sql = neon(process.env.NEON_DATABASE_URL);
  await sql`
    CREATE TABLE IF NOT EXISTS workout_sessions (
      id          TEXT PRIMARY KEY,
      date        TEXT NOT NULL,
      title       TEXT NOT NULL,
      duration_secs INTEGER,
      created_at  TIMESTAMPTZ NOT NULL DEFAULT NOW()
    )
  `;
  return new Response(JSON.stringify({ ok: true, message: 'migrations complete' }), {
    headers: { 'Content-Type': 'application/json' }
  });
}
```

## Deploy Checklist

- [ ] `netlify.toml` present with correct publish dir and functions dir
- [ ] All env vars set in Netlify site → Environment
- [ ] Neon integration connected (auto-injects `NEON_DATABASE_URL`)
- [ ] All functions are `.mjs` ESM format
- [ ] Redirect rules cover all `/api/*` routes
- [ ] CORS headers on all function responses
