# Agent Handoff

> Shared state between agents for the current active work. Update this file at the end of each session so the next agent (or next session) can pick up exactly where you left off.

**Instructions**: Keep this CURRENT — overwrite stale state. Last updated date is at the top.

---

## Last Updated
March 21, 2026 (hevy_upload blend routines session)

## Active Work

### Current Focus
Regime Dashboard (hevy_upload) blend workout routines — complete.

### What Was Just Completed (March 21, 2026)
- Added two new blend workout panels to `index.html` (`wkpanel-blend`): "Legs + Full Body" and "Arms + Full Body"
- Applied 6 hypertrophy-optimized exercise swaps (Pec Deck, Single Arm Cable Row, Leaning Cable Lateral Raise, Seated Calf Raise added, Incline DB Curl, Face Pulls)
- Added Rack Pulls to Arms + Full Body blend
- Reordered both blends by optimal lifting sequence (heaviest compounds first, not grouped by body part label)
- **Created both routines directly in Hevy via API** using `POST /v1/routines`
  - Hevy API key: stored in Netlify env var `HEVY_API_KEY`; also in `/memories/hevy-api.md`
  - Routine IDs saved for reference in `/memories/hevy-api.md`
- All changes deployed to Netlify via GitHub push to `Kyle9159/regime-dashboard`

### What's Next (in priority order)
1. Job search begins April 2026 — Job Ops improvements are high priority
2. Study Planner will be heavily used starting April 2026 — improve AI study guide quality
3. Optional: Consider moving Study Planner to Railway for remote access from school

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
