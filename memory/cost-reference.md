# Cost Reference

> Used by all agents to populate the `Est. Cost` column in plan blocks. Prices as of April 2026.

---

## Model Pricing

| Model | Input (per 1M tokens) | Output (per 1M tokens) | Notes |
|-------|-----------------------|------------------------|-------|
| `claude-sonnet-4-6` | $3.00 | $15.00 | Primary for reasoning-heavy tasks |
| `claude-opus-4-6` | $15.00 | $75.00 | Reserve for highest-stakes writing |
| `claude-haiku-3-5` | $0.25 | $1.25 | Ultra-cheap for simple tasks |
| `gpt-4.1` | $2.00 | $8.00 | Good for structured planning and large context |
| `gpt-4.1-mini` | $0.40 | $1.60 | Default for infra, scraper, and low-complexity tasks |
| `grok-4.1-fast` (xAI) | $0.20 | $0.20 | Flat rate — bulk scoring/classification loops |

**Formula**:
```
cost = (input_tokens / 1,000,000 × input_price) + (output_tokens / 1,000,000 × output_price)
```

**Example** — Claude Sonnet, 15K in / 8K out:
```
(15,000 / 1,000,000 × $3.00) + (8,000 / 1,000,000 × $15.00) = $0.045 + $0.120 = $0.165
```

---

## Task-Size Token Tier Estimates

Use these to estimate costs when exact token counts aren't known.

| Size | Description | Input tokens | Output tokens |
|------|-------------|-------------|---------------|
| **XS** | Single-file tweak, typo fix, one-liner | 3K | 1K |
| **S**  | Small feature, single component, schema addition | 5K | 2K |
| **M** | Multi-file feature, new API route + UI | 15K | 8K |
| **L** | Full feature with tests, new app section | 40K | 20K |
| **XL** | New app scaffold, monorepo-wide change | 80K | 30K |

---

## Per-Agent Cost Profiles

Pre-calculated for common task sizes. Use as quick estimates in plan blocks.

### `@app-builder` (claude-sonnet-4-6)

| Size | Est. Cost |
|------|-----------|
| XS | $0.024 |
| S | $0.045 |
| M | $0.165 |
| L | $0.42 |
| XL | $0.69 |

### `@academic` (claude-sonnet-4-6)

Same as `@app-builder` above — same model, same pricing.

### `@options-analyst` (claude-sonnet-4-6)

Same as `@app-builder` above.

### `@growth` (claude-sonnet-4-6)

Same as `@app-builder` above.

### `@orchestrator` (gpt-4.1)

| Size | Est. Cost |
|------|-----------|
| XS | $0.014 |
| S | $0.026 |
| M | $0.094 |
| L | $0.24 |
| XL | $0.40 |

### `@job-ops` (gpt-4.1)

Same as `@orchestrator` above.

### `@product-planner` (gpt-4.1)

Same as `@orchestrator` above.

### `@data-engineer` (gpt-4.1-mini)

| Size | Est. Cost |
|------|-----------|
| XS | $0.003 |
| S | $0.005 |
| M | $0.019 |
| L | $0.048 |
| XL | $0.080 |

### `@devops` (gpt-4.1-mini)

Same as `@data-engineer` above.

### `@health-fitness` (gpt-4.1-mini)

Same as `@data-engineer` above.

---

## Tokens Saved by Output Efficiency Rules

The 10 Output Efficiency Rules (no request restatement, no opener fillers, no post-completion summaries, reference-by-path instead of code reprinting, changed-sections-only for multi-file edits, no stack justifications, no file-header comments) eliminate output tokens per response. Use these to populate the `Est. tokens saved` line in plan blocks.

### Tokens saved per task size (output tokens eliminated)

| Size | Est. tokens saved | claude-sonnet-4-6 ($15/1M out) | gpt-4.1 ($8/1M out) | gpt-4.1-mini ($1.60/1M out) |
|------|-------------------|-------------------------------|----------------------|-----------------------------|
| XS   | ~200              | ~$0.003                       | ~$0.002              | <$0.001                     |
| S    | ~400              | ~$0.006                       | ~$0.003              | ~$0.001                     |
| M    | ~3,000            | ~$0.045                       | ~$0.024              | ~$0.005                     |
| L    | ~8,000            | ~$0.120                       | ~$0.064              | ~$0.013                     |
| XL   | ~14,000           | ~$0.210                       | ~$0.112              | ~$0.022                     |

**How to use in a plan block**: Identify the task size, find the row, pick the column matching the agent's model. Output:
```
**Est. tokens saved**: ~3,000 tokens (~$0.045 saved vs. unoptimized output)
```

---

## Quick Cross-Agent Comparison (Medium task)

| Agent | Model | M-task cost |
|-------|-------|-------------|
| `@health-fitness` | gpt-4.1-mini | $0.019 |
| `@devops` | gpt-4.1-mini | $0.019 |
| `@data-engineer` | gpt-4.1-mini | $0.019 |
| `@orchestrator` | gpt-4.1 | $0.094 |
| `@product-planner` | gpt-4.1 | $0.094 |
| `@job-ops` | gpt-4.1 | $0.094 |
| `@app-builder` | claude-sonnet-4-6 | $0.165 |
| `@academic` | claude-sonnet-4-6 | $0.165 |
| `@options-analyst` | claude-sonnet-4-6 | $0.165 |
| `@growth` | claude-sonnet-4-6 | $0.165 |

**Rule**: Always route to the cheapest agent that can handle the quality bar. See `@orchestrator` routing rules for decision logic.
