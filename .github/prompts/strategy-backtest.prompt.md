---
agent: ask
description: Evaluate a new options trading strategy before implementing it in csp_options_app
---

# Options Strategy Evaluation

Fill in the strategy details for a rigorous analysis before implementation.

---

**Strategy name**: [e.g., "Iron Condor on SPY", "LEAPS + CC Diagonal"]

**Strategy type**:
- [ ] CSP (Cash-Secured Put)
- [ ] Covered Call
- [ ] LEAPS
- [ ] Iron Condor
- [ ] Vertical spread
- [ ] Calendar spread
- [ ] Other: [describe]

**Market outlook this strategy requires**: [Neutral / Bullish / Bearish / High IV / Low IV]

**Target underlyings**: [e.g., "SPY, QQQ, high-IV tech stocks > $50"]

**Entry rules**:
- IV Rank threshold: 
- Delta target:
- DTE range:
- Other conditions:

**Exit rules**:
- Take profit at: [e.g., "50% of max profit"]
- Stop loss at: [e.g., "2x premium received"]
- Roll triggers:

**Max risk per trade**: [$ or % of account]

**Paper trade first?**: [Yes / No]

---

## Agent Instructions

Act as the **Options Analyst agent** and use the `options-trading` skill to:

1. Analyze the strategy mathematically:
   - Max profit / max loss / breakeven points
   - Probability of profit (approximate from delta)
   - Capital requirement per contract

2. Review the entry/exit rules:
   - Are they complete? Are there gaps?
   - How does this perform in high-IV vs. low-IV environments?
   - What's the biggest risk (gap-down, IV crush, assignment)?

3. Compare to existing strategies in csp_options_app:
   - How does this differ from current CSP/CC approaches?
   - Does it complement or overlap with the Wheel strategy?

4. Implementation plan for csp_options_app:
   - New module file name
   - Scan logic outline
   - Integration with Schwab API (option chain fetching)
   - Telegram alert format
   - Google Sheets columns to add

5. Paper trading test plan:
   - Minimum sample size before going live
   - Metrics to track
   - Success criteria to graduate to live trading

6. Final recommendation: **Implement / Needs refinement / Don't implement** + reasoning
