# Agent Handoff

> Shared state between agents for the current active work. Update this file at the end of each session so the next agent (or next session) can pick up exactly where you left off.

**Instructions**: Keep this CURRENT — overwrite stale state. Last updated date is at the top.

---

## Last Updated
March 26, 2026 (csp_options_app Phase 3 complete)

## Active Work

### Current Focus
csp_options_app improvements — Phases 1, 2, and 3 all complete. No active work in flight.

### What Was Just Completed (March 26, 2026)

#### csp_options_app — Phase 3 complete ✅

**Phase 1** (9 steps) — Telegram centralization, JSON batch scoring, gspread elimination, CC bot fixes, SQLite Greek columns

**Phase 2** (5 steps) — Sector heatmap tile + API, earnings/roll badges, token cost card, regime timeline widget

**Phase 3** (5 steps):
- **Step 15** — Two-pass Grok scoring: FAST on all 100, then MODEL_MID re-scores top 20; `mid_scored: True` flag set
- **Step 16** — `get_current_regime()` now returns `{"regime": str, "vix_ratio": float}`; `improved_put_score()` gets `vix_term_mult` (0.93× backwardation, 1.08× contango); wired into both sort calls
- **Step 17** — `_calculate_short_put_targets()` takes `regime_key` param; stop-loss buffer scales 1.5%→4% by regime; `/api/exit_targets` route fixed (wrong arg names, attribute→dict access, regime passed in)
- **Step 18** — CC bot `_CC_REGIME` dict was already complete from Phase 1; no-op
- **Step 19** — `/api/position_size` route fixed: correct param names (`account_balance`, `underlying_price`), dict key access, correlation-adjusted sizing (same-sector ≥2 → halve contracts + warning)

All files syntax-checked clean.

### Next Steps (Future Sessions)
- Phase 4 / Phase 5 were not documented before session summarization — original list is lost
- Good stopping point; can pick up new features as needed
- `hevy_upload`: committed and pushed manifest rename fix (`Regime` -> `Regiment`) to `main`
- `Study_Planner`: committed and pushed Vite port update (`5173` -> `5175`) to `main`
- `where_should_i_move`: committed and pushed large feature set to `main` (auth routes/pages, dashboard, AI city summaries, map heatmap API/UI, robots/sitemap, SEO/meta updates)
- `job-ops`: detected untracked sensitive cookie debug artifacts under `orchestrator/storage/...`; added `orchestrator/storage/` to `.gitignore` and committed locally
- `job-ops` push failed with GitHub permission error: `403 Permission to DaKheera47/job-ops.git denied to Kyle9159`
- `language_learner`: initialized local git repo, connected `origin` to `https://github.com/Kyle9159/language_learner.git`, and pushed initial `main`
- `job-ops`: local `main` now tracks `kyle/main`; clean PR branch `chore/ignore-orchestrator-storage` created from `origin/main`, cherry-picked with only the ignore-rule commit, and pushed to fork
- `language_learner`: added and pushed first-release `README.md` with setup, env vars, scripts, architecture, and verification steps

#### Current Git Status Snapshot
- `hevy_upload`: clean, synced with `origin/main`
- `Study_Planner`: clean, synced with `origin/main`
- `where_should_i_move`: clean, synced with `origin/main`
- `language_learner`: clean, synced with `origin/main`
- `job-ops`: `main` tracks `kyle/main`; PR-ready branch `chore/ignore-orchestrator-storage` tracks `kyle/chore/ignore-orchestrator-storage`

### What Was Just Completed (March 25, 2026)

#### Danish Language Learner — Production ready ✅
#### Language Learner — Production ready ✅
- **All server + client code complete** with zero TypeScript errors
- **Architecture**: Vite+React19 client (port 5176) + Express+SQLite server (port 3002)
- **Repo rename**: `/Users/kylehansen/repos/danish_language_learner` → `/Users/kylehansen/repos/language_learner`
- **App Launcher update**: entry renamed to `Language Learner`, id updated to `language-learner`, path updated to new repo folder
- **Fixed this session**:
  - `server/index.ts`: Changed `import { runMigrations }` → `import './db/migrate'` (side-effect)
  - `server/api/vocabulary.ts`: Fixed `sql\`correct_count + 1\`` (was using column reference instead of SQL template)
  - `server/api/vocabulary.ts`: Fixed event type `totalFlashcardsReviewed` (was `totalReviewed`)
  - `server/api/profile.ts`: Removed unused imports (`and`, `count`, `userLevel`); fixed `vocabularyProgress.stage` → `vocabularyProgress.srsStage`
  - `server/api/lessons.ts`: Cleaned up unused imports; added `or` import
  - `server/api/ai.ts`: Added level→difficulty mapping (`A0/A1→easy, A2→medium, B1→hard`) for `generateQuiz`
  - `server/services/gamification.ts`: Removed unused `LEVEL_ORDER`, `vocabularyProgress` imports
  - `client/src/lib/api.ts`: Replaced all inline `import('../../shared/types')` with static imports via `@shared/*` alias
  - `client/src/components/ui/Badge.tsx`: Fixed `HTMLAttributes<'span'>` → `HTMLAttributes<HTMLSpanElement>`
  - `client/src/components/features/LessonCard.tsx`: Changed prop type `Lesson` → `LessonWithProgress`
  - `client/src/components/features/ExerciseRenderer.tsx`: Removed unused `AudioButton` import
  - `client/src/pages/Curriculum.tsx`: Changed `Lesson[]` → `LessonWithProgress[]`
  - `client/src/pages/Dashboard.tsx`: Removed unused `Button`, `UserLevel` imports; typed map callbacks
  - `client/src/pages/Conversation.tsx`: Removed unused `Badge` import
  - `client/src/pages/Lesson.tsx`: Removed unused `cn` import; simplified `finishExercises`; cast quiz `Exercise`; added `lesson!` assertions
  - `client/src/hooks/queries/useProfile.ts`: Removed unused type imports
  - `client/src/components/Layout.tsx`: Removed unused `useLocation` import
  - `vite.config.ts` → `vite.config.mts` (renamed to force ESM, fixes `@tailwindcss/vite` ESM-only error)
  - `tsconfig.json` + `vite.config.mts`: Added `@shared/*` alias pointing to `./shared`
  - DB seeded: 233 words, 20 lessons, 18 achievements
  - App-launcher registered: port 5176, `npm run dev`

### Current State
- ✅ `http://localhost:3002/api/health` — server live
- ✅ `http://localhost:5176` — Vite client serving (200)
- ✅ `tsc --noEmit` clean (both client and server tsconfigs)
- ✅ `tsc --noEmit -p tsconfig.server.json` clean
- ✅ DB seeded with real data
- App registered in app-launcher

### Next Steps (Future Sessions)
1. **Test in browser** — open `http://localhost:5176`, walk through each page
2. **Verify flashcard SRS flow** — grade words, check `next_review_date` updates
3. **Test AI features** — requires `.env` with `XAI_API_KEY` and `OPENAI_API_KEY`
4. **Conversation SSE** — test streaming works end-to-end
5. Potential: add `.env` setup instructions to README

### Key Files
- `/Users/kylehansen/repos/language_learner/`
- `server/index.ts` → port 3002
- `client/src/App.tsx` → React Router
- `shared/types.ts` → canonical types
- `data/db.sqlite` → live database
- All three Jinja2 blocks in Analytics Performance sub-tab replaced with `<div id="analytics-*-content">` placeholders
- `loadAnalytics()` JS function + `_loadAnalyticsSummary()`, `_loadAnalyticsOpen()`, `_loadAnalyticsHistory()` helpers added
- Auto-fires when user opens Analytics tab (`openTab('analytics')`) or clicks Performance sub-tab

#### Scanner tier badges
- Tier system (T1/T2/T3) was already in `simple_options_scanner.py` but **never displayed**
- Now shown as colored pill badges:
  - 🏆 Tier 1 = green — blue chips, ETFs, mega-caps you'd be happy holding if assigned
  - ⭐ Tier 2 = blue — quality growth, higher vol but institutional backing
  - ⚠️ Tier 3 = orange — higher risk, only scanned in STRONG_BULL regime
- Appears on: Top-10 Grok ranked tiles (compact `T1/T2/T3` badge) and main scanner grid tile headers (full `Tier 1/2/3` pill)

#### Earlier in same session (confirmed pushed)
- `trade_outcome_tracker.py` — SQLite trade outcome logger at `cache_files/trade_outcomes.db` with Schwab order auto-sync (4hr, market hours)
- `_get_schwab_csp_positions()` added to `dashboard_server.py` (standalone module-level helper, not inside route)
- DTE ranges tightened: NEUTRAL 60→45, CAUTIOUS 60→45, BEARISH 60→50
- `/grok/market_pulse` route added (fixed "unavailable" tile)
- Clear Cache button + `/api/clear_cache` route (single copy fixed)
- Mixed content / stale HTML fixed: all JS fetch calls use relative paths, `trading_dashboard.html` auto-regens 5s after server startup via `threading.Timer`
- All 8 P0-P3 scanner strategy improvements (tier cleanup, OTM buffer regime-dependent, earnings hard-block, improved_put_score formula, Grok safety-first prompt, filter cutoff 80, VIX term structure, regime_key param)

### What's Next (in priority order)
1. **Job search begins April 2026** — Job Ops improvements are high priority
2. **Study Planner** will be heavily used starting April 2026 — improve AI study guide quality
3. **CSP Options App** — Analytics will populate once Schwab sync runs and `trade_outcomes.db` has real closed-trade data; may need manual seeding if no BTC orders exist yet
4. Optional: Consider moving Study Planner to Railway for remote access from school

---

## Previous Session Summary
Full rebuild of copilot infrastructure to production `.github/` structure:

- `.github/copilot-instructions.md` — global memory auto-read every session
- `.github/agents/` — 8 proper `.agent.md` agent files (orchestrator, app-builder, academic, options-analyst, data-engineer, devops, health-fitness, job-ops)
- `.github/skills/` — 10 `SKILL.md` files in named subfolders (xai-grok, railway-deploy, netlify-neon, sqlite-drizzle, express-typescript, nextjs-app-router, fastapi-python, tailwind-radix, options-trading, academic-writing)
- `.github/instructions/` — 7 per-app `.instructions.md` files with correct `applyTo` globs (ai-pulse, study-planner, job-ops, csp-options-app, hevy-upload, supplement-explorer, app-launcher)
- `.github/prompts/` — 6 prompt templates (new-app, feature-spec, debug-session, paper-draft, strategy-backtest, code-review)
- Updated `README.md` to document the new `.github/` structure

Old folders (`agents/`, `skills/`, `per-app/`, `prompts/`) left in place as reference — superseded by `.github/`.

### What's Next (in priority order)
1. Job search begins April 2026 — Job Ops improvements are high priority
2. Study Planner will be heavily used starting April 2026 — improve AI study guide quality
3. Optional: Consider moving Study Planner to Railway for remote access from school

---

## Open Questions / Decisions Needed

- [ ] Should `.github/copilot-instructions.md` files be added to each repo? (Pro: Copilot auto-reads them; Con: scattered context)
- [ ] Should Study Planner move to Railway for remote access from school?
- [ ] Any new job boards to add to Job Ops as job search begins in April 2026?
- [ ] CSP Analytics history tab will be empty until `trade_outcomes.db` has real data — may want a manual CSV import or seed script from Google Sheet Trade_History

---

## Notes for Next Agent

- The copilot folder is at `/Users/kylehansen/repos/copilot/`
- Kyle starts Masters program in **April 2026** — Study Planner and Academic agent will be heavily used
- Job search begins alongside school — Job Ops improvements are high priority
- Always read `memory/project-context.md` for personal context before making suggestions
- Always read `memory/stack-decisions.md` before introducing any new technology

---

## Parking Lot (Ideas to Revisit)

- **Workout ML**: Use Hevy history to predict optimal next-session weights (regression on progressive overload data)
- **Options backtest**: Build a historical CSP backtest engine using yfinance historical option chains
- **Study Planner AI grading**: Grade assignment drafts before submission using rubric + AI
- **AI Pulse personalization**: Score articles based on Kyle's interests (ML > general tech > business)
- **Supplement research agent**: Scan PubMed abstracts to update supplement scoring data
