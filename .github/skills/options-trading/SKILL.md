---
name: options-trading
description: "Use when: analyzing options positions, calculating Greeks, evaluating CSP/CC/LEAPS strategies, understanding IV Rank vs IV Percentile, designing entry/exit rules, or sizing positions for the wheel strategy."
---

# Options Trading Domain Knowledge

> Used by: @options-analyst

## Core Terminology

| Term | Definition |
|------|-----------|
| **CSP** | Cash-Secured Put — sell a put, hold cash to buy shares if assigned |
| **CC** | Covered Call — sell a call against 100 owned shares |
| **LEAPS** | Option with 1+ year to expiration |
| **OTM** | Out of the Money |
| **ITM** | In the Money |
| **DTE** | Days to Expiration |
| **IV** | Implied Volatility |
| **IV Rank** | Current IV relative to 52-week range (0–100%) |
| **IV Percentile** | % of days past year where IV was lower than current |
| **Premium** | Credit received when selling |
| **Roll** | Close existing + open new at different strike/expiry |
| **Wheel** | Sell CSP → assigned → sell CC → repeat |

## The Greeks

| Greek | Measures | CSP Rule |
|-------|----------|----------|
| Δ Delta | Directional exposure | Sell at Δ 0.20–0.30 |
| Γ Gamma | Delta change rate | Explodes near expiry — avoid holding short options through expiry week |
| Θ Theta | Time decay (daily gain for sellers) | Accelerates last 30 DTE |
| V Vega | IV sensitivity | Sell high IV, benefit from IV crush |
| ρ Rho | Interest rate sensitivity | Minor for short-term options |

## Volatility

```python
# IV Rank (range-normalized)
iv_rank = (current_iv - low_52w) / (high_52w - low_52w) * 100

# IV Percentile (distribution-based, more robust)
iv_pct = days_iv_was_lower / total_days * 100
```

**Entry filter**: IV Rank > 20% minimum. Prefer > 35% for best premium quality.

**Vega crush**: Sell when IV is elevated → IV contracts after entry → short vega position profits.

## Entry Rules (CSP)

1. IV Rank > 20% (prefer 35%+)
2. Underlying above 50-day and 200-day MA
3. Earnings > 14 days away
4. Select delta 0.20–0.30 (go lower in high VIX environments)
5. Target 45 DTE entry
6. Max loss if assigned: position fits within 5% of account

## Exit / Management Rules

- **Take profit**: close at 50% of premium received
- **Close or roll**: at 50% of max loss
- **Roll timing**: 21 DTE if not yet at profit target (avoid gamma risk)
- **Roll direction**: out in time first, then down in strike if needed

## Position Sizing

```python
def max_contracts(account_size: float, stock_price: float, max_pct: float = 0.05) -> int:
    """Max contracts respecting 5% account risk rule"""
    max_capital = account_size * max_pct
    capital_per_contract = stock_price * 100
    return int(max_capital / capital_per_contract)
```

## Risk Factors (always consider)

1. **Gap down scenario**: what if stock drops 20% overnight?
2. **Earnings**: IV spikes then crushes — verify exact earnings date
3. **VIX Environment**: VIX > 30 → reduce all deltas by 5–10 points
4. **Double calendar risk**: two expirations = two gamma exposures
5. **Assignment risk**: puts can be assigned early on ex-dividend dates

## Schwab Symbol Format

```
{TICKER}  {YYMMDD}{C/P}{STRIKE * 1000}
Example:  SPY   260319P00540000  (SPY put, strike $540, expires 2026-03-19)
Total: 21 characters, ticker left-padded with spaces to 6 chars
```

## Performance Metrics to Track

- Win rate by strategy and underlying
- Average P&L per contract
- Annualized return on capital deployed
- Maximum drawdown (peak to trough)
- Sharpe ratio (weekly returns)
- IV Rank at entry vs. outcome (does higher IVR → better returns?)
