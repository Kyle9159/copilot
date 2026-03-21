---
mode: ask
description: Spec out a new feature before building it — clarify scope, approach, and risks
---

# Feature Spec

Use this before writing a single line of code. A good spec prevents 80% of rework.

---

**App**: [Which app? e.g., "job-ops", "csp_options_app", "study_planner"]

**Feature name**: [Short name, e.g., "iron-condor-scanner"]

**User story**: 
> As a [user], I want to [action], so that [benefit].

**What triggers this feature?**: [Button click / API call / scheduled / data event]

**Inputs**: [What data goes in?]

**Outputs**: [What does the user see or receive?]

**Expected behavior (happy path)**:
1. 
2. 
3. 

**Edge cases to handle**:
- [ ] Empty state (no data)
- [ ] Error state (API down, validation failure)
- [ ] Loading state
- [ ] [Other specific edge cases]

**Out of scope (for now)**:
- 

**Rough priority**: [Must have / Nice to have / Future]

---

## Agent Instructions

Act as the most relevant specialized agent for this app and:

1. Read `memory/stack-decisions.md` for technology constraints
2. Read the relevant `per-app/` file for this app's specific context
3. Review the feature spec for completeness — ask clarifying questions if anything is ambiguous
4. Propose an implementation plan:
   - Files to create/modify
   - Database schema changes needed
   - API endpoints needed
   - UI components needed
5. Identify the biggest technical risks or unknowns
6. Estimate complexity: Small (< 2 hours) / Medium (half day) / Large (multiple sessions)
7. Only begin implementation after the spec is confirmed

**Do not write code until the spec is approved.**
