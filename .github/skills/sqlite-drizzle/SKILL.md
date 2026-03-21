---
name: sqlite-drizzle
description: "Use when: setting up SQLite with Drizzle ORM in a TypeScript app, defining table schemas, writing migrations, or performing queries (insert, upsert, select, update, delete, transactions)."
---

# SQLite + Drizzle ORM

> Used by: @app-builder, @job-ops, @data-engineer

## Install

```bash
npm install drizzle-orm better-sqlite3
npm install -D drizzle-kit @types/better-sqlite3
```

## Schema Definition

```typescript
// server/db/schema.ts
import { sqliteTable, text, integer } from 'drizzle-orm/sqlite-core';
import { sql } from 'drizzle-orm';

const baseColumns = {
  id: text('id').primaryKey(),   // cuid2
  createdAt: text('created_at')
    .notNull()
    .default(sql`(strftime('%Y-%m-%dT%H:%M:%SZ', 'now'))`),
  updatedAt: text('updated_at')
    .notNull()
    .default(sql`(strftime('%Y-%m-%dT%H:%M:%SZ', 'now'))`),
};

export const jobs = sqliteTable('jobs', {
  ...baseColumns,
  source:      text('source').notNull(),
  title:       text('title').notNull(),
  company:     text('company').notNull(),
  url:         text('url').notNull().unique(),
  description: text('description'),
  aiScore:     integer('ai_score'),
});
```

## Database Connection

```typescript
// server/db/index.ts
import Database from 'better-sqlite3';
import { drizzle } from 'drizzle-orm/better-sqlite3';
import * as schema from './schema';
import path from 'path';

const DB_PATH = process.env.DB_PATH ?? path.join(process.cwd(), 'data', 'db.sqlite');
const sqlite = new Database(DB_PATH);
sqlite.pragma('journal_mode = WAL');   // Always enable WAL
sqlite.pragma('foreign_keys = ON');

export const db = drizzle(sqlite, { schema });
```

## Migrations (drizzle.config.ts)

```typescript
import type { Config } from 'drizzle-kit';
export default {
  schema: './server/db/schema.ts',
  out: './server/db/migrations',
  dialect: 'sqlite',
  dbCredentials: { url: process.env.DB_PATH ?? './data/db.sqlite' },
} satisfies Config;
```

```json
// package.json scripts
{
  "db:generate": "drizzle-kit generate",
  "db:migrate": "tsx server/db/migrate.ts",
  "db:studio": "drizzle-kit studio"
}
```

## Query Patterns

### Insert
```typescript
import { createId } from '@paralleldrive/cuid2';
await db.insert(jobs).values({ id: createId(), source: 'adzuna', title, company, url });
```

### Upsert (conflict handling)
```typescript
import { sql } from 'drizzle-orm';
await db.insert(jobs)
  .values({ id: createId(), source, title, url })
  .onConflictDoUpdate({
    target: jobs.url,
    set: { title, updatedAt: sql`(strftime('%Y-%m-%dT%H:%M:%SZ', 'now'))` },
  });
```

### Select with filter + order
```typescript
import { eq, desc, gte, and } from 'drizzle-orm';
const results = await db.select()
  .from(jobs)
  .where(and(eq(jobs.source, 'adzuna'), gte(jobs.aiScore, 70)))
  .orderBy(desc(jobs.createdAt))
  .limit(50);
```

### Update
```typescript
await db.update(jobs)
  .set({ aiScore: 85, updatedAt: sql`(strftime('%Y-%m-%dT%H:%M:%SZ', 'now'))` })
  .where(eq(jobs.id, jobId));
```

### Transaction
```typescript
await db.transaction(async (tx) => {
  await tx.insert(pipelineRuns).values({ id: runId, status: 'running' });
  await tx.insert(jobs).values(jobRows);
  await tx.update(pipelineRuns).set({ status: 'done' }).where(eq(pipelineRuns.id, runId));
});
```
