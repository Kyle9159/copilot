# Agent Handoff

> Shared state between agents for the current active work. Update this file at the end of each session so the next agent (or next session) can pick up exactly where you left off.

**Instructions**: Keep this CURRENT — overwrite stale state. Last updated date is at the top.

---

## Last Updated
March 24, 2026 (csp_options_app major feature session)

## Active Work

### Current Focus
CSP Options App — all items below are complete and pushed to `Kyle9159/csp_options_app` on `main`.

### What Was Just Completed (March 24, 2026)

#### Open CSPs tab — Schwab API upgrade
- `/api/open_csps` now uses **`_get_schwab_csp_positions()`** as primary source (reads live short PUT positions directly from Schwab account via `get_account(hash, fields='positions')`)
- Falls back to Google Sheet + live stock quote enrichment if Schwab returns empty
- New helper `_parse_schwab_occ_symbol()` decodes 21-char OCC format (`AAPL  260319P00150000`) → symbol/expiration/strike
- P&L computed from `averagePrice` (entry) vs `marketValue` (current), no extra quote API round-trips
- Response now includes `data_source: 'schwab'|'sheet'|'none'`

#### Analytics tab — fully dynamic (was Jinja2 static)
- Three new API endpoints:
  - `GET /api/analytics/summary` → `get_trade_stats()` from `trade_outcome_tracker.py`, augments `open_count` with live Schwab position count
  - `GET /api/analytics/history?limit=50` → `get_recent_trades()` mapped to `{Symbol, Strike, Entry Date, Exit Date, Days Held, Entry Premium, Exit Premium, Net Profit $, ROI %, Win/Loss}`
  - `GET /api/analytics/open` → `_get_schwab_csp_positions()` for live position table
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
