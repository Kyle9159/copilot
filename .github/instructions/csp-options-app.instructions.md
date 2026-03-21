---
description: Context for CSP Options App — algorithmic options trading with Python/Flask and Schwab API. Use when working in the csp_options_app directory.
applyTo: "**/csp_options_app/**"
---

# CSP Options App — Project Context

Python + Flask algorithmic options trading dashboard. Runs CSP, covered call, LEAPS strategies via Schwab API. Local-only — trading data never leaves machine.

**Agent**: Use `@options-analyst` for strategy work.
**Skill**: Reference `skills/options-trading` for Greeks and strategy rules.

## Architecture
- **Framework**: Python + Flask, port 5000
- **Config**: Pydantic v2 + pydantic-settings + `.env`
- **Market data**: yfinance + Schwab API (live + paper)
- **AI**: xAI Grok via `grok_utils.py` (cached per ticker+date)
- **Persistence**: Google Sheets (trade ledger) + local JSON
- **Notifications**: Telegram Bot

## Key Files
```
config.py                   — Pydantic TradingConfig (Tier 1/2/3 params)
dashboard_server.py         — Flask app + routes + threading workers
grok_utils.py               — Grok sentiment (cached)
schwab_utils.py             — Schwab API: orders, option chains
covered_call_bot.py         — CC strategy
dividend_tracker_bot.py     — Dividend tracking
dynamic_exit_targets.py     — Position management
earnings_calendar.py        — Earnings date filtering
position_sizing.py          — Risk-based sizing
```

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
