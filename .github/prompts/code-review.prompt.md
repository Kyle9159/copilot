---
agent: ask
description: Thorough code review before merging a feature or finishing a session
---

# Code Review

Use this at the end of a feature branch or before marking work complete.

---

**App**: [Which app?]

**Files changed**: [List the main files modified]

**What does this change do?**: [One paragraph summary]

**Branch / PR link** (if any):

**Checklist type**:
- [ ] Feature — new functionality
- [ ] Bug fix — patching existing behavior
- [ ] Refactor — no behavior change
- [ ] Performance — optimization
- [ ] Security — auth / data handling

---

## Review Checklist

### Correctness
- [ ] Does the code do what the spec says?
- [ ] Are all edge cases handled (null, empty, error)?
- [ ] Are there off-by-one errors or logic bugs?

### Security (OWASP Top 10)
- [ ] No SQL injection / command injection vectors
- [ ] User input is validated (Zod schemas for TypeScript, Pydantic for Python)
- [ ] Sensitive data is redacted from logs
- [ ] Auth/authorization checks are in place
- [ ] No secrets hardcoded

### API Contract (for /api/* routes)
- [ ] Returns `{ ok: true, data, meta.requestId }` on success
- [ ] Returns `{ ok: false, error: { code, message }, meta.requestId }` on error
- [ ] Correct HTTP status code used
- [ ] `x-request-id` header honored and returned

### Code Quality
- [ ] No `console.log` in production paths (use logger)
- [ ] Structured logging with context fields
- [ ] No `any` type without documented reason
- [ ] Small focused functions — nothing doing 3 things at once

### Tests
- [ ] New behavior has test coverage
- [ ] Existing tests still pass
- [ ] For job-ops: all CI checks pass (Biome, types, test:run)

---

## Agent Instructions

Act as the most relevant specialized agent for this app and:

1. Read the changed files via `@workspace` or listed paths
2. Work through each checklist section above systematically
3. For each issue found:
   - Severity: **Critical** (must fix) / **Major** (should fix) / **Minor** (nice to fix)
   - Location: exact file + line range
   - Problem: what's wrong
   - Fix: specific code change
4. After the list: give an overall **Approve / Request Changes** verdict
5. If security issues are found, fix them immediately — don't just flag
