---
description: Context for Study Planner — Masters coursework organizer with AI study guide generation. Use when working in the Study_Planner directory.
applyTo: "**/Study_Planner/**"
---

# Study Planner — Project Context

Personal app for organizing Masters coursework. Upload course materials (PDF, DOCX, YouTube links), generate AI study guides, export Anki flashcards.

**Status**: Heavily used starting April 2026 when Masters program begins.
**Agents**: Use `@academic` for study features, `@app-builder` for new code.

## Architecture
- **Frontend**: React 18 + Vite 6 + TypeScript + TailwindCSS v4 + shadcn/ui
- **Backend**: Express.js + TypeScript
- **Database**: SQLite + Drizzle ORM at `data/db.sqlite`
- **AI**: xAI Grok primary; GitHub Models alternative
- **File parsing**: pdf-parse, mammoth (DOCX), youtube-transcript
- **Ports**: 5175 (Vite dev) / 3001 (Express)

## Key Files
```
client/src/App.tsx           — Main React app
client/src/pages/            — Route-level pages
server/app.ts                — Express server
server/api/                  — Route handlers
server/db/schema.ts          — Drizzle schema
server/db/migrate.ts         — Runs on startup
server/services/             — AI generation, file processing
shared/types.ts              — Shared TypeScript types
data/uploads/                — Uploaded course files
```

## Database Tables
```
courses        — id, name, description, semester
materials      — id, course_id, type, filename, content, youtube_url
study_guides   — id, material_id, content, generated_at, model_used
projects       — id, course_id, rubric, completion_guide
flashcard_sets — id, study_guide_id, cards_json
```

## AI Provider Selection
User selects model per-request. Supported:
- `xai/grok-3` — default primary
- `xai/grok-3-mini-fast` — fast drafts
- `github/gpt-4o`, `github/claude-sonnet-4-6` — alternatives

## Local Dev
```bash
cd Study_Planner
npm run dev   # Starts both Vite (5175) + Express (3001) concurrently
```
