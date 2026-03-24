---
description: Context for CSP Options App — algorithmic options trading with Python/Flask and Schwab API. Use when working in the csp_options_app directory.
applyTo: "**/csp_options_app/**"
---

# CSP Options App — Project Context

Python + Flask algorithmic options trading dashboard. Runs CSP, covered call, LEAPS strategies via Schwab API. Local-only — trading data never leaves machine.

**Agent**: Use `@options-analyst` for strategy work.
**Skill**: Reference `skills/options-trading` for Greeks and strategy rules.

## Architecture
- **Framework**: Python + Flask, port 5000, HTTPS (`ssl_context='adhoc'`)
- **Config**: Pydantic v2 + pydantic-settings + `.env`
- **Market data**: yfinance + Schwab API (live + paper)
- **AI**: xAI Grok via `grok_utils.py` (cached per ticker+date)
- **Persistence**: Google Sheets (legacy trade ledger) + `cache_files/trade_outcomes.db` (SQLite, primary) + local JSON cache
- **Notifications**: Telegram Bot
- **HTML generation**: `generate_dashboard.py` produces `trading_dashboard.html` (gitignored, auto-regens 5s after server start via `threading.Timer(5, _regen_dashboard)`)
- **IMPORTANT**: All JS fetch calls must use **relative paths** (not `http://127.0.0.1:5000/...`) — server runs HTTPS, absolute HTTP URLs cause mixed-content errors

## Key Files
```
config.py                   — Pydantic TradingConfig (Tier 1/2/3 params)
dashboard_server.py         — Flask app + routes + threading workers
generate_dashboard.py       — Jinja2 HTML template generator (huge file, ~6000 lines)
grok_utils.py               — Grok sentiment (cached)
schwab_utils.py             — Schwab API: orders, option chains, get_client()
simple_options_scanner.py   — CSP scanner: Tier1/2/3 symbol lists, regime settings, improved_put_score
trade_outcome_tracker.py    — SQLite trade logger + Schwab order auto-sync
covered_call_bot.py         — CC strategy
dividend_tracker_bot.py     — Dividend tracking
dynamic_exit_targets.py     — Position management
earnings_calendar.py        — Earnings date filtering
position_sizing.py          — Risk-based sizing
open_trade_monitor.py       — Open CSP monitor (Google Sheet fallback, load_trades_from_sheet)
```

## Symbol Tiers (simple_options_scanner.py)
```
TIER_1_SYMBOLS  — Blue chips, ETFs, mega-cap (SPY, AAPL, JPM etc.) — all regimes
TIER_2_SYMBOLS  — Quality growth (PLTR, CRWD, SHOP etc.) — STRONG_BULL/MILD_BULL/NEUTRAL only
TIER_3_SYMBOLS  — Higher risk (TSLA, RIVN, HOOD etc.) — STRONG_BULL only
SIMPLE_WATCHLIST = TIER_1 + TIER_2 + TIER_3
```

## Regime Settings (simple_options_scanner.py)
| Regime | DTE Range | Delta | Tier Limit |
|--------|-----------|-------|------------|
| STRONG_BULL | 10–40 | 0.15–0.40 | None (all) |
| MILD_BULL | 14–45 | 0.15–0.37 | None (all) |
| NEUTRAL_OR_WEAK | 21–45 | 0.15–0.35 | TIER_1_2 |
| CAUTIOUS | 30–45 | 0.10–0.30 | TIER_1 |
| BEARISH_HIGH_VOL | 45–50 | 0.10–0.30 | TIER_1 |

## Trade Outcome Tracker (trade_outcome_tracker.py)
- SQLite at `cache_files/trade_outcomes.db`
- Tables: `recommendations`, `trades`, `schwab_sync_log`
- `sync_from_schwab()` — polls FILLED orders (60d), matches STO→BTC, marks expired/assigned
- `get_trade_stats(days=90)` → win_rate, total_pnl, wins, losses, avg_holding_days, avg_annualized
- `get_recent_trades(limit=20)` → closed trades for Analytics history table
- `get_open_trades()` → trades with outcome='open'
- Auto-sync fires every 4hr during market hours via `threading.Timer(60, _scheduled_schwab_sync)` in `dashboard_server.py`

## API Routes (dashboard_server.py) — Key
```
GET  /api/open_csps              — Live short PUTs from Schwab (falls back to Sheet)
GET  /api/analytics/summary      — Trade stats from SQLite (last 90d)
GET  /api/analytics/history      — Closed trades for history table
GET  /api/analytics/open         — Live open positions (Schwab)
GET  /grok/market_pulse          — Grok market pulse tile (1hr cache)
POST /api/clear_cache            — Clears all in-memory caches + JSON cache files
GET  /api/chain_heatmap/<symbol> — Options chain heatmap
GET  /api/open_csps              — 5-min cache, force_refresh=true to bypass
```

## Schwab Position Parsing
- OCC symbol format: `'AAPL  260319P00150000'` (symbol left-padded to 6 chars)
- Helper: `_parse_schwab_occ_symbol(occ)` → `{symbol, expiration, strike, put_call}`
- Short puts: `shortQuantity > 0` + `instrument.putCall == 'PUT'` + `assetType == 'OPTION'`
- P&L: `averagePrice * shortQty * 100` (total credit) - `abs(marketValue)` (current cost)
- Account hash: `client.get_account_numbers().json()` → match `accountNumber` → `.hashValue`

## Adding a New Strategy (always follow this pattern)
1. Create `strategy_name_bot.py`
2. Add Flask route in `dashboard_server.py`:
   ```python
   @app.route('/api/scan-strategy', methods=['POST'])
   def scan_strategy():
       task_id = str(uuid.uuid4())
       thread = threading.Thread(target=run_worker, args=(task_id,))
       thread.daemon = True
       thread.start()
       return jsonify({'task_id': task_id})
   ```
3. Track progress via `progress_tracker[task_id]` dict
4. Support paper trading mode (`SCHWAB_PAPER_TRADING=true`)
5. Log to Google Sheets via gspread
6. Send Telegram notification on entry/exit

## Critical Rules
- Always use `get_grok_sentiment_cached()` — never call Grok API directly
- Always validate Schwab option symbol format: `{TICKER}  {YYMMDD}{C/P}{STRIKE*1000}` (21 chars)
- Never exceed 5% of account in a single position
- Always check earnings filter (14-day default) before scanning

## Local Dev
```bash
cd csp_options_app
source .venv/bin/activate
python dashboard_server.py
```
