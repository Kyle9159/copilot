---
agent: ask
description: Structured debugging flow for any app
---

# Debug Session

Fill in what you know, then let's systematically find the root cause.

---

**App**: [Which app?]

**What were you doing when the bug appeared?**: [Steps to reproduce]

**What did you expect to happen?**:

**What actually happens?**: [Exact error message, screenshot description, or behavior]

**Error message (paste verbatim)**:
```
[paste error here]
```

**Stack trace** (if any):
```
[paste stack trace here]
```

**When did this start?**: [Always broken / Worked yesterday / After a specific change]

**What changed recently?**: [New code, updated deps, env var change, data change]

**What have you already tried?**: [So we don't repeat it]

---

## Agent Instructions

Act as the most relevant specialized agent for this app and:

1. Read the error and stack trace carefully — identify the most likely root cause
2. Propose the **minimum diagnostic step** first (don't jump to solutions)
3. Structure the debug in layers:
   - **Layer 1**: Is it a config/env problem? (wrong env var, missing API key, wrong port)
   - **Layer 2**: Is it a data problem? (null/undefined, wrong shape, encoding issue)
   - **Layer 3**: Is it a logic problem? (wrong condition, off-by-one, race condition)
   - **Layer 4**: Is it an integration problem? (API changed, auth expired, CORS)
4. For each hypothesis: state it clearly → provide an exact command/code to test it → interpret the result
5. Never make multiple changes at once — fix one thing, verify, proceed
6. When root cause is found: explain WHY it happened, not just what to change
7. Suggest how to prevent this class of bug in the future

**Don't guess — diagnose.**
