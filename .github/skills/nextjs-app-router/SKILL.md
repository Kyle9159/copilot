---
name: nextjs-app-router
description: "Use when: building Next.js 15/16 App Router apps, deciding between Server and Client Components, setting up TanStack Query on the client, writing API routes, configuring layouts, or setting up TailwindCSS v4 in a Next.js project."
---

# Next.js App Router

> Used by: @app-builder

## Project Structure

```
src/
├── app/
│   ├── layout.tsx           -- Root layout
│   ├── page.tsx             -- Home page (Server Component by default)
│   ├── globals.css          -- @import "tailwindcss" + @theme {}
│   └── api/
│       └── health/route.ts
├── components/
│   ├── ui/                  -- Primitive components (Button, Input, etc.)
│   └── features/            -- Feature-specific components
├── lib/
│   ├── api.ts               -- Client-side API fetch functions
│   └── utils.ts             -- cn() helper
└── types/index.ts
```

## Server vs Client Components

```typescript
// Server Component (default — no directive, can be async, DB access OK)
export default async function Page() {
  const data = await fetchFromDB();
  return <div>{data.title}</div>;
}

// Client Component — needs useState, effects, event handlers
'use client';
import { useState } from 'react';
export function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(c => c + 1)}>{count}</button>;
}
```

**Rule**: Start Server. Add `'use client'` only when you need: state, event listeners, browser APIs, or effects.

## Data Fetching

### Server Component (initial page data)
```typescript
async function getJobs() {
  const res = await fetch(`${process.env.API_URL}/api/jobs`, {
    next: { revalidate: 60 }  // ISR: revalidate every 60 seconds
  });
  return res.json();
}

export default async function JobsPage() {
  const { data: jobs } = await getJobs();
  return <JobList jobs={jobs} />;
}
```

### Client with TanStack Query
```typescript
'use client';
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';

function useJobs() {
  return useQuery({
    queryKey: ['jobs'],
    queryFn: () => fetch('/api/jobs').then(r => r.json()).then(r => r.data),
  });
}

function useScoreJob() {
  const qc = useQueryClient();
  return useMutation({
    mutationFn: (jobId: string) =>
      fetch(`/api/jobs/${jobId}/score`, { method: 'POST' }).then(r => r.json()),
    onSuccess: () => qc.invalidateQueries({ queryKey: ['jobs'] }),
  });
}
```

## API Routes

```typescript
// app/api/jobs/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { z } from 'zod';

export async function GET(req: NextRequest) {
  const { searchParams } = new URL(req.url);
  // ...
  return NextResponse.json({ ok: true, data: jobs });
}

export async function POST(req: NextRequest) {
  const body = await req.json();
  const parsed = BodySchema.safeParse(body);
  if (!parsed.success) {
    return NextResponse.json(
      { ok: false, error: { code: 'INVALID_REQUEST', message: 'Validation failed' } },
      { status: 400 }
    );
  }
  return NextResponse.json({ ok: true, data: result }, { status: 201 });
}
```

## TailwindCSS v4 Setup

```css
/* app/globals.css */
@import "tailwindcss";

@theme {
  --color-primary:    oklch(65% 0.20 220);
  --color-primary-fg: oklch(98% 0.01 220);
  --color-surface:    oklch(10% 0.01 240);
  --color-border:     oklch(30% 0.02 240);
  --font-sans: 'Inter', system-ui, sans-serif;
  --radius-md: 0.5rem;
}
```

## next.config.ts (Railway deployment)

```typescript
import type { NextConfig } from 'next';

const config: NextConfig = {
  output: 'standalone',   // Required for Railway/Docker deployment
  env: {
    API_URL: process.env.API_URL ?? 'http://localhost:8000',
  },
};

export default config;
```
