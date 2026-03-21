---
name: xai-grok
description: "Use when: integrating xAI Grok API into any app, selecting the right Grok model, writing structured output (JSON mode), streaming responses, or caching LLM calls to reduce cost."
---

# xAI / Grok API Integration

> Used by: @app-builder, @data-engineer, @academic, @options-analyst, @job-ops

## API Basics

```
Base URL: https://api.x.ai/v1
Auth:     Authorization: Bearer $XAI_API_KEY
SDK:      openai npm package (OpenAI-compatible)
```

### TypeScript Setup
```typescript
import OpenAI from 'openai';

const xai = new OpenAI({
  apiKey: process.env.XAI_API_KEY,
  baseURL: 'https://api.x.ai/v1',
});
```

### Python Setup
```python
from openai import AsyncOpenAI

xai = AsyncOpenAI(
    api_key=os.getenv("XAI_API_KEY"),
    base_url="https://api.x.ai/v1",
)
```

## Model Selection

| Model | Speed | Cost | Best For |
|-------|-------|------|----------|
| `grok-3-mini-fast` | Fastest | Lowest | Bulk classification, scoring loops, filtering |
| `grok-3-mini` | Fast | Low | Summarization, light reasoning, structured output |
| `grok-3` | Slower | Higher | Complex reasoning, nuanced writing, paper help |

**Rule**: Use `grok-3-mini-fast` for loops. Reserve `grok-3` for user-facing generation.

## Structured Output (JSON Mode)

### TypeScript
```typescript
const response = await xai.chat.completions.create({
  model: 'grok-3-mini-fast',
  messages: [
    { role: 'system', content: 'Respond only with valid JSON.' },
    { role: 'user', content: prompt }
  ],
  response_format: { type: 'json_object' },
  temperature: 0.1,
});
const data = JSON.parse(response.choices[0].message.content ?? '{}');
```

### Python
```python
response = await xai.chat.completions.create(
    model="grok-3-mini-fast",
    messages=[
        {"role": "system", "content": "Respond only with valid JSON."},
        {"role": "user", "content": prompt},
    ],
    response_format={"type": "json_object"},
    temperature=0.1,
)
data = json.loads(response.choices[0].message.content)
```

## Streaming (for user-facing generation)

```typescript
const stream = await xai.chat.completions.create({
  model: 'grok-3',
  messages,
  stream: true,
});

for await (const chunk of stream) {
  const delta = chunk.choices[0]?.delta?.content ?? '';
  res.write(`data: ${JSON.stringify({ delta })}\n\n`);
}
res.write('data: [DONE]\n\n');
res.end();
```

## Caching (always cache in production)

### Python — In-memory (matches csp_options_app pattern)
```python
from functools import lru_cache
from datetime import date

@lru_cache(maxsize=512)
def get_xai_cached(cache_key: str, prompt: str) -> str:
    """cache_key should include ticker + date to expire daily"""
    response = xai_client.chat.completions.create(...)
    return response.choices[0].message.content

# Call with:
result = get_xai_cached(f"{ticker}:{date.today()}", prompt)
```

### TypeScript — Map cache (simple in-process)
```typescript
const cache = new Map<string, { value: string; expiresAt: number }>();

async function cachedXai(key: string, prompt: string, ttlMs = 3_600_000): Promise<string> {
  const hit = cache.get(key);
  if (hit && hit.expiresAt > Date.now()) return hit.value;

  const response = await xai.chat.completions.create({
    model: 'grok-3-mini-fast',
    messages: [{ role: 'user', content: prompt }],
  });
  const value = response.choices[0].message.content ?? '';
  cache.set(key, { value, expiresAt: Date.now() + ttlMs });
  return value;
}
```

## Rate Limits & Cost Rules

- Never call Grok in a tight loop without caching
- Add 100–200ms delay between batch calls to avoid rate limit errors
- Log token usage in development (`response.usage`) to track cost
- Use `temperature: 0` for deterministic/scoring tasks
