---
description: Context for Hevy Upload / Regime Dashboard — personal health PWA on Netlify + Neon. Use when working in the hevy_upload directory.
applyTo: "**/hevy_upload/**"
---

# Hevy Upload (Regime Dashboard) — Project Context

Personal health PWA on Netlify. Shows workout history, Zone 2 cardio, nutrition, foodwork. Data baked into HTML or fetched from Neon PostgreSQL via Netlify Functions.

**Agent**: Use `@health-fitness` for domain features.
**Skill**: Reference `skills/netlify-neon` for function boilerplate.

## Architecture
- **Frontend**: Vanilla HTML/CSS/JS — `kyle_routine_dashboard.html`
- **Data**: "Baked data" — workout history as JS literals (`WH`, `WH_TMILL`)
- **Backend**: Netlify Functions (ESM, `netlify/functions/*.mjs`)
- **Database**: Neon PostgreSQL via `@netlify/neon`
- **Hosting**: Netlify static + serverless

## Key Files
```
kyle_routine_dashboard.html   — Main dashboard (all tabs + JS)
styles.css                    — Global styles
netlify/functions/migrate.mjs — DDL: create tables if not exists
netlify.toml                  — Config + redirects
manifest.json                 — PWA manifest
sw.js                         — Service worker
```

## Database Tables
```sql
workout_sessions      (id, date, title, duration_secs, notes)
workout_exercises     (id, session_id, title, order_index, superset_id)
workout_set_details   (id, exercise_id, set_type, weight_lbs, reps, e1rm)
treadmill_sessions    (id, date, duration_min, distance_mi, avg_hr, calories)
fitbit_activities     (id, date, steps, calories, active_min, weight_lbs)
```

## UI Conventions
- Dark theme: `#0a0a0a` background, `#00d4ff` cyan accent
- Glassmorphism cards: `background: rgba(255,255,255,0.05); backdrop-filter: blur(10px)`
- Mobile-first: used from iPhone at the gym
- Accordion pattern for grouping (existing pattern throughout)

## Adding a New Netlify Function
```javascript
// netlify/functions/feature.mjs
import { neon } from '@netlify/neon';
export default async function handler(req, context) {
  const sql = neon(process.env.NEON_DATABASE_URL);
  return new Response(JSON.stringify({ ok: true }), {
    headers: { 'Content-Type': 'application/json' }
  });
}
```
Then add redirect in `netlify.toml`.
