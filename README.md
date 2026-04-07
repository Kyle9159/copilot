# AI Copilot System — Personal Development Infrastructure

> A production-grade multi-agent AI system built on GitHub Copilot. It powers all development, trading, academic, and health-tracking work across 7+ apps. This README explains how it works, why it's built this way, and how to extend it.

---

## Table of Contents

- [What is This?](#what-is-this)
- [Core Concepts](#core-concepts)
  - [Agents](#agents)
  - [Skills](#skills)
  - [Instructions](#instructions)
  - [Prompts](#prompts)
  - [Memory](#memory)
- [How It All Fits Together](#how-it-all-fits-together)
- [The Planning & Approval Flow](#the-planning--approval-flow)
- [Cost Optimization](#cost-optimization)
- [Agent Roster](#agent-roster)
- [Skill Library](#skill-library)
- [Per-App Instructions](#per-app-instructions)
- [Prompt Templates](#prompt-templates)
- [Memory Files](#memory-files)
- [Repository Structure](#repository-structure)
- [How to Create a New Agent](#how-to-create-a-new-agent)
- [How to Create a New Skill](#how-to-create-a-new-skill)
- [Design Principles](#design-principles)

---

## What is This?

This repository is a **Copilot configuration layer** — a set of structured Markdown files that teach GitHub Copilot how to work as a specialized team rather than a generic assistant.

Out of the box, GitHub Copilot is helpful but generic. It doesn't know your stack, your apps, your coding standards, or your preferences. Without guidance, it guesses — and guesses wrong often enough to be frustrating.

This system solves that by giving Copilot **persistent context** (memory files), **domain specialists** (agents), **reusable know-how** (skills), **automatic project context** (instructions), and **structured workflows** (prompts). The result is a Copilot that behaves like a team of senior developers who already understand your codebase.

### What it powers

| App | Purpose |
|-----|---------|
| `ai_pulse` | AI news aggregator (FastAPI + Next.js) |
| `Study_Planner` | Masters coursework organizer |
| `job-ops` | Automated job hunting platform |
| `csp_options_app` | Options trading automation via Schwab API |
| `hevy_upload` | Personal health & workout dashboard |
| `supplement_explorer` | Supplement research database |
| `app-launcher` | macOS control panel for all local apps |

---

## Core Concepts

### Agents

An **agent** is a specialized AI persona with its own name, model, tool access, and system prompt. When you invoke `@app-builder` instead of generic Copilot, you get an agent that already knows the entire tech stack, has strong opinions about code structure, asks clarifying questions before writing code, and plans before acting.

**Why agents instead of one generic assistant?**

- A routing agent (`@orchestrator`) doesn't need deep coding ability — GPT-4.1 handles it cheaply.
- A deployment agent (`@devops`) doesn't need to reason about React components — GPT-4.1 Mini is sufficient.
- A complex multi-file feature needs real reasoning depth — Claude Sonnet is worth the cost.

Each agent runs on the **right model for the right task**, which improves output quality and reduces cost simultaneously.

**How agents work technically:**
Every `.github/agents/*.agent.md` file has a YAML frontmatter block and a Markdown body. The frontmatter declares the name, model, and available tools. The Markdown body is the system prompt — it shapes every response the agent gives.

```yaml
---
name: app-builder
description: "Use when: building new features, scaffolding new apps..."
model: claude-sonnet-4-6
tools:
  - edit/editFiles
  - execute/runInTerminal
  - search/codebase
  # ... more tools
---

# App Builder Agent

You are a senior full-stack engineer who knows Kyle Hansen's exact
technology stack...
```

GitHub Copilot discovers all `*.agent.md` files automatically and surfaces them when you type `@` in the chat.

---

### Skills

A **skill** is a deep reference document for a specific technology or domain. It contains battle-tested patterns, common pitfalls, setup checklists, and code examples — things that would waste tokens to re-explain every session.

When Copilot's skill-matching logic detects your request overlaps with a skill's description, it loads the full `SKILL.md` before generating a response. You can also load one explicitly: *"Use the railway-deploy skill."*

**Why skills instead of just putting everything in agents?**

Skills are modular and reusable. The `sqlite-drizzle` skill can be loaded by `@app-builder`, `@data-engineer`, or even generic Copilot when you're working on database code. Writing it once and referencing it everywhere avoids duplication across 10 agent files.

Skills live in `.github/skills/<skill-name>/SKILL.md`.

---

### Instructions

**Instructions** are per-project context files that auto-inject into Copilot's context whenever you're working in a matching file path. They're scoped with an `applyTo` glob so the right context loads automatically — no manual action needed.

```yaml
---
applyTo: "**/ai_pulse/**"
---
# AI Pulse — Context
This is a FastAPI backend + Next.js 15 frontend deployed to Railway...
```

When you open a file in `ai_pulse/`, Copilot automatically knows the architecture, ports, deployment target, and conventions for that specific app. When you switch to `job-ops/`, entirely different context loads.

**Why not just put this in the global instructions file?**

The global `copilot-instructions.md` is loaded on every session. Loading all 7 apps' full context every time would waste tokens on irrelevant information. Scoped instructions load on demand.

---

### Prompts

**Prompts** are reusable workflow templates for recurring task types. Instead of re-explaining what you want from scratch every time you need a code review or want to spec a feature, you open a `.prompt.md` file, fill in the brackets, and send.

Prompts have a `mode` frontmatter field:
- `ask` — interactive Q&A mode
- `agent` — autonomous execution mode (the agent takes action without waiting for confirmation at each step)

```yaml
---
mode: agent
description: Bootstrap a new app in the canonical stack
---
Create a new app named {{APP_NAME}} hosted on {{HOSTING_TARGET}}...
```

---

### Memory

**Memory files** are persistent Markdown documents that agents read at the start of a session to get oriented. Unlike agent system prompts (which are static), memory files evolve as your apps, decisions, and priorities change.

The memory layer solves a fundamental problem with AI assistants: **they don't remember anything between sessions by default.** Without memory files, you'd spend the first 10 minutes of every session re-explaining your stack, your apps, and what you were working on last time.

With memory files, any agent can pick up exactly where the last session left off.

Key memory files:
- `agent-handoff.md` — what was done last session, what's next
- `app-registry.md` — all apps, their ports, hosting, and status
- `stack-decisions.md` — canonical tech choices with reasoning
- `cost-reference.md` — model pricing and task-size cost estimates

---

## How It All Fits Together

Here's the information flow for a typical task:

```
You type a request
        │
        ▼
copilot-instructions.md    ← always loaded (global stack, standards, rules)
        │
        ▼
*.instructions.md          ← auto-loaded if you're in a matching file path
        │
        ▼
@agent invoked             ← you pick the right specialist with @
        │
        ├── reads memory/  ← agent gets current project state
        │
        ├── loads SKILL.md ← agent loads relevant how-to reference
        │
        ▼
  asks clarifying questions (if ambiguous)
        │
        ▼
  outputs a plan block with cost estimates
        │
        ▼
  ⏸ waits for your "go"
        │
        ▼
  executes implementation
```

Every layer that's loaded costs tokens. The system is designed so each layer loads **only when relevant**, keeping the total context tight and the responses accurate.

---

## The Planning & Approval Flow

Every agent follows a mandatory three-step gate before writing any implementation code. This prevents wasted tokens on wrong assumptions, keeps you in control of scope, and makes cost predictable.

### Step 1 — Clarifying Questions

If the request has any ambiguity that would cause a wrong plan, the agent asks one grouped question block:

```
Before I finalize the plan, a few quick questions:

1. Scope: Should this cover X only, or also Y?
2. DB: New table or extend existing?
3. UI: New page or embed in existing view?

Reply with answers or "skip" to let me assume and proceed.
```

Rules:
- One shot only. The agent asks everything it needs in a single message.
- If you say "skip," the agent lists its assumptions at the top of the plan.
- For simple, obvious tasks (single-line fix, typo), questions are skipped entirely.

### Step 2 — Plan Block

Before writing a single line of implementation code, the agent outputs a structured plan:

```
## Plan: [Feature Name]

| # | Step | Files Affected | Agent | Model | Est. Cost |
|---|------|----------------|-------|-------|-----------|
| 1 | Create DB schema | server/db/schema.ts | @self | claude-sonnet-4-6 | $0.024 |
| 2 | Add API route | server/routes/items.ts | @self | claude-sonnet-4-6 | $0.045 |
| 3 | Build UI component | client/src/components/... | @self | claude-sonnet-4-6 | $0.096 |

**Total estimated cost**: $0.165
**Complexity**: Medium

⏸ Waiting for approval. Reply "go" to proceed, or suggest changes.
```

This gives you full visibility into scope and cost before committing.

### Step 3 — Hard Stop

**No agent writes implementation code until you explicitly reply "go."** This is enforced in every agent's system prompt. The `⏸` gate is not optional.

This matters because AI models with tool access can make real changes to your filesystem. A misunderstood spec executed autonomously is harder to reverse than a plan that gets refined before execution.

---

## Cost Optimization

AI API usage has real dollar costs. This system is architected to minimize waste without sacrificing output quality.

### Model Selection by Task

Not all tasks need the most capable (expensive) model. The system routes tasks to the cheapest model that can handle them well:

| Model | Cost Tier | Best For |
|-------|-----------|----------|
| `claude-sonnet-4-6` | $$$ | Complex reasoning, multi-file features, architecture decisions |
| `gpt-4.1` | $$ | Structured planning, routing, large-context tasks |
| `gpt-4.1-mini` | $ | Infra config, scrapers, simple ETL, deployment tasks |

**Example savings**: Deploying a Railway service through `@devops` (GPT-4.1 Mini) costs ~$0.005. Doing the same task through `@app-builder` (Claude Sonnet) wastes ~$0.165 for no quality gain.

### Output Efficiency Rules

All agents follow 10 output rules to eliminate token waste:

1. Never restate the user's request — start directly with output
2. No opener fillers ("Sure!", "Great idea!", "Of course!")
3. No post-completion summaries — don't recap what was just done
4. Reference code by path instead of reprinting it (`file.ts:functionName`)
5. Tables for comparisons, bullets for lists — no numbered prose paragraphs
6. Skip explaining obvious stack choices
7. Omit file-header block comments unless explicitly requested
8. Show only changed sections for multi-file edits (3–5 lines of context)
9. One-sentence completion acknowledgment only
10. Hand off to the cheaper agent when the plan identifies one

### Context Window Efficiency

- **Global instructions** (`copilot-instructions.md`) are trimmed to only what every session needs
- **Per-app instructions** auto-inject only for the active project
- **Memory files** are written as summaries and overwritten (not appended) to prevent unbounded growth
- **Skills** load on-demand, not always

See [`memory/cost-reference.md`](./memory/cost-reference.md) for full pricing tables and task-size (XS → XL) cost estimates per agent.

---

## Agent Roster

Invoke agents by typing `@` in GitHub Copilot Chat. All 10 agents appear as selectable options.

| Agent | Model | Best For |
|---|---|---|
| `@orchestrator` | GPT-4.1 | "Help me figure out where to start" — routes to the right specialist |
| `@product-planner` | GPT-4.1 | PRDs, roadmaps, user stories, effort estimates — always before `@app-builder` |
| `@app-builder` | Claude Sonnet 4.6 | Building features, scaffolding new apps, full-stack TypeScript/Python work |
| `@academic` | Claude Sonnet 4.6 | Masters coursework, papers, AI/ML concepts, study guides |
| `@growth` | Claude Sonnet 4.6 | Monetization, SEO, pricing strategy, user acquisition |
| `@options-analyst` | Claude Sonnet 4.6 | Trading strategies, Greeks, csp_options_app extension |
| `@data-engineer` | GPT-4.1 Mini | Scrapers, ETL, RSS feeds, database schema design |
| `@devops` | GPT-4.1 Mini | Railway, Netlify, Docker, DNS, env vars, CI/CD |
| `@health-fitness` | GPT-4.1 Mini | Hevy dashboard, workout features, bloodwork trends |
| `@job-ops` | GPT-4.1 | Job Ops monorepo — strict AGENTS.md compliance required |

### Routing Guide

```
Not sure which agent? → @orchestrator

Planning a new feature? → @product-planner first, then @app-builder

Building/extending an existing app? → @app-builder

Deploying to Railway/Netlify/Docker? → @devops

Scraping data or designing a schema? → @data-engineer

Working in job-ops monorepo? → @job-ops (never @app-builder for this repo)

Trading strategy or Schwab API? → @options-analyst

Masters coursework or research paper? → @academic

Monetizing an app or improving SEO? → @growth

hevy_upload features or health data? → @health-fitness
```

All agent files: [`.github/agents/`](./.github/agents/)

---

## Skill Library

Skills load automatically when your request matches a skill's domain. You can also request one explicitly: *"Use the nextjs-app-router skill."*

| Skill | Triggers when you ask about... |
|---|---|
| `xai-grok` | Grok API, model selection, JSON mode, streaming, cost optimization |
| `railway-deploy` | Railway, `railway.toml`, custom domains, Railway PostgreSQL |
| `netlify-neon` | Netlify Functions (ESM), Neon PostgreSQL, PWA deployment |
| `sqlite-drizzle` | Drizzle ORM, SQLite schema definitions, migrations, queries |
| `express-typescript` | Express + TypeScript backend, request IDs, SSE, Biome config |
| `nextjs-app-router` | Next.js 15/16 App Router, Server/Client Components, TanStack Query |
| `fastapi-python` | FastAPI, SQLAlchemy 2 async, Pydantic v2, Alembic migrations |
| `tailwind-radix` | TailwindCSS v4 `@theme {}` tokens, Radix UI, CVA variants |
| `options-trading` | Greeks, CSP/CC/LEAPS strategies, IV Rank, Schwab API |
| `academic-writing` | Paper structure, IEEE/APA citations, abstract writing, stats reporting |

All skill files: [`.github/skills/`](./.github/skills/)

---

## Per-App Instructions

These files auto-inject into context when you open a file matching the `applyTo` glob. No manual action required — just open a file in the right repo.

| Instructions file | Auto-activates for | What it contains |
|---|---|---|
| `ai-pulse.instructions.md` | `ai_pulse/**` | Railway deploy config, backend/frontend port split, Grok integration |
| `study-planner.instructions.md` | `Study_Planner/**` | SQLite schema, Vite+Express setup, AI study guide generation |
| `job-ops.instructions.md` | `job-ops/**` | Monorepo structure, AGENTS.md mandate, SSE patterns, CI commands |
| `csp-options-app.instructions.md` | `csp_options_app/**` | Schwab API auth, Flask routes, scanner logic, dashboard structure |
| `hevy-upload.instructions.md` | `hevy_upload/**` | Netlify Functions, Neon schema, PWA manifest, Hevy API patterns |
| `supplement-explorer.instructions.md` | `supplement_explorer/**` | Vercel SSG, Next.js static generation, affiliate link structure |
| `app-launcher.instructions.md` | `app-launcher/**` | macOS LaunchAgent, app registry config, Express control panel |

All instruction files: [`.github/instructions/`](./.github/instructions/)

---

## Prompt Templates

Reusable templates for recurring workflows. Open a `.prompt.md` file in VS Code, fill in the `{{VARIABLE}}` placeholders, and send.

| Prompt | Mode | Use Case |
|---|---|---|
| `new-app` | agent | Bootstrap a new app in the canonical stack from scratch |
| `feature-spec` | ask | Spec out a feature fully before handing to @app-builder |
| `debug-session` | agent | Structured root-cause debugging with hypothesis tracking |
| `paper-draft` | ask | Start an academic paper or technical document |
| `strategy-backtest` | ask | Evaluate a new options trading strategy against historical data |
| `code-review` | agent | Pre-merge review: OWASP Top 10 + API contract compliance |

All prompt files: [`.github/prompts/`](./.github/prompts/)

---

## Memory Files

Agents read these at the start of each session to orient themselves. Update them when things change — don't let them go stale.

| File | What's in it | Update when... |
|---|---|---|
| [`memory/agent-handoff.md`](./memory/agent-handoff.md) | Active work state, last session summary, next steps | End of every session |
| [`memory/app-registry.md`](./memory/app-registry.md) | All apps: purpose, ports, hosting, current status | You add/change/retire an app |
| [`memory/stack-decisions.md`](./memory/stack-decisions.md) | Canonical tech choices + reasoning for each | You adopt or abandon a tool |
| [`memory/architecture-decisions.md`](./memory/architecture-decisions.md) | ADR log for cross-app decisions and their rationale | You make a significant arch decision |
| [`memory/accounts-services.md`](./memory/accounts-services.md) | External accounts: Railway, Netlify, xAI, Schwab, etc. | You add a new service |
| [`memory/project-context.md`](./memory/project-context.md) | Personal context: Masters program, goals, preferences | Priorities shift or program starts |
| [`memory/cost-reference.md`](./memory/cost-reference.md) | Model pricing, task-size tiers, per-agent cost profiles | Model pricing changes |

**The golden rule for memory**: Always overwrite stale entries — never append indefinitely. A 50-line `agent-handoff.md` that's current is far more useful than a 500-line one that's 80% outdated.

---

## Repository Structure

```
copilot/
├── .github/
│   ├── copilot-instructions.md   ← Global system prompt — loaded every session
│   ├── agents/                   ← 10 custom agents (*.agent.md)
│   ├── skills/                   ← 10 domain skills (*/SKILL.md)
│   ├── instructions/             ← 7 per-app context files (*.instructions.md)
│   └── prompts/                  ← 6 workflow templates (*.prompt.md)
└── memory/                       ← 7 persistent reference files
```

Files outside `.github/` are not discovered by Copilot automatically. The `.github/` directory is the active system — everything else is legacy or reference.

---

## How to Create a New Agent

1. **Create the file** at `.github/agents/<name>.agent.md`

2. **Write the frontmatter** — this is what Copilot reads to register the agent:

```yaml
---
name: my-agent
description: "Use when: [specific triggers that tell Copilot to suggest this agent]"
model: gpt-4.1          # or claude-sonnet-4-6, gpt-4.1-mini
tools:
  - read/readFile
  - search/codebase
  - edit/editFiles
  - execute/runInTerminal
  - agent/runSubagent
  # Add only what the agent actually needs
---
```

3. **Write the system prompt** (the Markdown body after the `---` frontmatter closing). Key sections to include:

   - **Memory — Read First**: which `memory/` files to load
   - **When to use this agent**: cost guidance, best-fit tasks
   - **Domain knowledge**: the specific expertise this agent brings
   - **Hard-stop rules**: when to stop and require explicit "go"
   - **Pre-Implementation Plan (Required)**: planning mandate + cost profile

4. **Add the cost profile** at the bottom — copy the table from an existing agent and use the pricing from `memory/cost-reference.md`.

5. **Register it** — add a row to the Agent Roster table in this README and in `.github/copilot-instructions.md`.

### Tool Access Best Practices

Don't give every agent every tool. Over-permissioning makes the agent harder to predict and wastes context on unused capability declarations.

| Agent type | Typical tools |
|---|---|
| Read-only advisor | `read/readFile`, `search/codebase`, `web/fetch` |
| Planner (no code) | above + `vscode/askQuestions`, `agent/runSubagent` |
| Full builder | all of the above + `edit/editFiles`, `execute/runInTerminal` |
| Infra/DevOps | above + `execute/runTests`, `search/changes` |

---

## How to Create a New Skill

Skills are the right tool when you have domain patterns that apply across multiple agents or projects and would waste tokens to re-explain each time.

1. **Create the directory**: `.github/skills/<skill-name>/`

2. **Create the SKILL.md** with this structure:

```markdown
---
description: >
  Use when: [exact triggers that cause Copilot to auto-load this skill].
  Match the language of the trigger descriptions to what users actually type.
---

# [Skill Name]

## Setup Checklist
- [ ] Step one
- [ ] Step two

## Key Patterns

### Pattern Name
[code example + explanation of why this pattern, not another]

## Common Pitfalls
- **Problem**: description → **Fix**: solution

## Quick Reference
[table or code snippet for the most-referenced thing in this domain]
```

3. The `description` frontmatter is critical — Copilot uses it to decide when to auto-load the skill. Write it in terms of what a user would actually type, not abstract categories.

---

## Design Principles

**1. Specialists over generalists**
Each agent knows one domain deeply. A specialist gives better answers, uses the right model, and applies domain-specific hard stops (e.g., `@options-analyst` always hard-stops before live trading changes).

**2. Plan before act**
No agent executes implementation without a plan block and explicit "go." This prevents AI autonomy from outrunning human review. The cost of a wrong plan reviewed before execution is near zero; the cost of a wrong execution that needs reverting is high.

**3. Right model for the task**
Routing infra work to GPT-4.1 Mini over Claude Sonnet saves ~97% per token at equivalent quality for that class of task. This isn't about being cheap — it's about not burning budget on capability you don't need.

**4. Memory is living documentation**
Memory files aren't a "nice to have" — they're what makes the system work across sessions. A Copilot session without current memory is like a new developer who just joined and has to be re-onboarded every time. Update `agent-handoff.md` at the end of every session.

**5. Skills are evergreen**
Skills get better over time as you discover patterns that work. When you find yourself re-explaining the same thing to an agent (e.g., "remember, in this app we always do X"), that's a signal to capture it in a skill or memory file rather than re-explain it each session.

**6. Context is load-bearing**
Every file in `.github/` costs tokens. Don't add files to be comprehensive — add them when the payoff (fewer wrong answers, less re-explaining) clearly exceeds the cost (context bloat on every session).

---

*Last updated: April 2026*
