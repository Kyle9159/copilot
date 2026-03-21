---
name: express-typescript
description: "Use when: building an Express.js + TypeScript backend, setting up request ID middleware, writing route handlers, configuring error handling, adding SSE endpoints, or setting up Biome linting."
---

# Express + TypeScript Backend

> Used by: @app-builder, @job-ops

## Project Structure

```
server/
├── app.ts                    -- Express factory (middleware, routers)
├── index.ts                  -- Entry point (starts server, migrations)
├── api/                      -- Route handlers (one file per resource)
├── services/                 -- Business logic (no HTTP concerns)
└── infra/
    ├── db/                   -- Drizzle connection + schema + migrate
    ├── logger.ts             -- Structured logger
    └── middleware/
        ├── requestId.ts
        └── errorHandler.ts
```

## App Factory

```typescript
// server/app.ts
import express from 'express';
import cors from 'cors';
import { requestIdMiddleware } from './infra/middleware/requestId';
import { errorHandler } from './infra/middleware/errorHandler';
import { jobsRouter } from './api/jobs';

export function createApp() {
  const app = express();
  app.use(express.json({ limit: '2mb' }));
  app.use(cors({ origin: process.env.CLIENT_ORIGIN ?? 'http://localhost:5173' }));
  app.use(requestIdMiddleware);
  app.use('/health', (_req, res) => res.json({ ok: true }));
  app.use('/api/jobs', jobsRouter);
  app.use(errorHandler);
  return app;
}
```

## Request ID Middleware

```typescript
// infra/middleware/requestId.ts
import { randomUUID } from 'crypto';
import type { Request, Response, NextFunction } from 'express';

export function requestIdMiddleware(req: Request, res: Response, next: NextFunction) {
  const requestId = (req.headers['x-request-id'] as string) ?? randomUUID();
  req.requestId = requestId;
  res.setHeader('x-request-id', requestId);
  next();
}

declare global {
  namespace Express {
    interface Request { requestId: string; }
  }
}
```

## Route Handler Pattern

```typescript
// api/jobs.ts
import { Router } from 'express';
import { z } from 'zod';
import { db } from '../infra/db';
import { logger } from '../infra/logger';

export const jobsRouter = Router();

const QuerySchema = z.object({
  limit: z.coerce.number().int().min(1).max(100).default(50),
});

jobsRouter.get('/', async (req, res, next) => {
  try {
    const { limit } = QuerySchema.parse(req.query);
    const data = await db.query.jobs.findMany({ limit });
    res.json({ ok: true, data, meta: { requestId: req.requestId } });
  } catch (err) {
    next(err);
  }
});
```

## Error Handler

```typescript
// infra/middleware/errorHandler.ts
import type { Request, Response, NextFunction } from 'express';
import { ZodError } from 'zod';

export function errorHandler(err: unknown, req: Request, res: Response, _next: NextFunction) {
  const requestId = req.requestId ?? 'unknown';

  if (err instanceof ZodError) {
    return res.status(400).json({
      ok: false,
      error: { code: 'INVALID_REQUEST', message: 'Validation failed', details: err.flatten() },
      meta: { requestId },
    });
  }

  res.status(500).json({
    ok: false,
    error: { code: 'INTERNAL_ERROR', message: 'An unexpected error occurred' },
    meta: { requestId },
  });
}
```

## SSE Endpoint

```typescript
// api/pipeline.ts — SSE progress streaming
import { setupSSE, writeSSEData, writeSSEComment } from '../infra/sse';

pipelineRouter.get('/stream/:runId', (req, res) => {
  setupSSE(res);
  const { runId } = req.params;

  const cleanup = subscribeToRun(runId, (event) => {
    writeSSEData(res, event);
    if (event.type === 'done') res.end();
  });

  req.on('close', cleanup);
});
```

## biome.json

```json
{
  "$schema": "https://biomejs.dev/schemas/1.9.4/schema.json",
  "organizeImports": { "enabled": true },
  "linter": {
    "enabled": true,
    "rules": { "recommended": true }
  },
  "formatter": {
    "enabled": true,
    "indentStyle": "space",
    "indentWidth": 2,
    "lineWidth": 100
  }
}
```
