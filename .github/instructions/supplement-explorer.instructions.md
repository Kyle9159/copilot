---
description: Context for Supplement Explorer — static supplement comparison site on Vercel. Use when working in the supplement_explorer directory.
applyTo: "**/supplement_explorer/**"
---

# Supplement Explorer — Project Context

Static supplement comparison website. Browse by category, compare brands side-by-side. No backend — all data is static TypeScript files. Deployed on Vercel.

**Agent**: Use `@app-builder` for new features.

## Architecture
- **Framework**: Next.js 16 + React 19 + TypeScript
- **Styling**: TailwindCSS v4 + PostCSS
- **Components**: Radix UI, Lucide React, CVA + clsx + tailwind-merge
- **Search**: Fuse.js (client-side fuzzy search)
- **Data**: Static TypeScript files in `src/data/`
- **Hosting**: Vercel (SSG — no backend, no server)

## Key Files
```
src/data/supplements/       — All supplement data as typed TS objects
src/data/categories.ts      — Category definitions
src/lib/scoring.ts          — Scoring: quality 45% + value 30% + popularity 25%
src/app/page.tsx            — Home page
src/app/supplements/[slug]/ — Detail page
src/app/categories/[slug]/  — Category browse page
scripts/fetch-asins.js      — Fetch Amazon ASINs
scripts/apply-asins.js      — Apply fetched ASINs to data files
```

## Scoring System
```typescript
score = (quality * 0.45) + (value * 0.30) + (popularity * 0.25)
// Only appears in featured list if: researched === true
```

## Adding a New Supplement
1. Add entry to `src/data/supplements/` file
2. Set `researched: false` initially
3. `node scripts/list-placeholders.js` — find missing ASINs
4. `node scripts/fetch-asins.js && node scripts/apply-asins.js`
5. Set `researched: true` when data verified

## Constraints
- No backend — this is pure SSG. Never add API routes or server-side data fetching
- No user accounts or dynamic data
- Keep bundle size small — no heavy dependencies
