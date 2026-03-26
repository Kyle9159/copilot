---
agent: ask
description: Start an academic paper, technical document, or study guide draft
---

# Paper / Document Draft

Fill in the details below, then the Academic Advisor agent will draft the document.

---

**Document type**:
- [ ] Academic research paper
- [ ] Technical report / white paper
- [ ] Literature review
- [ ] Study guide
- [ ] Project proposal
- [ ] Presentation outline

**Course / Context**: [Course name, or "independent" if not for class]

**Topic**: [Be specific — "Comparing transformer attention mechanisms for time series forecasting" not "AI"]

**Required length**: [e.g., "8–10 pages double-spaced" or "2000 words"]

**Citation style**: [IEEE / APA / Chicago / MLA / None]

**Rubric / Prompt**: [Paste assignment prompt or grading criteria here, or "none"]

**Deadline**: [Date]

**Draft or final?**: [Early draft for feedback / Final polish]

**Kyle's angle** (what unique perspective/projects to reference):
[e.g., "I can reference my options trading bot as a real RL application"]

---

## Agent Instructions

Act as the **Academic Advisor agent** and:

1. Read the rubric/prompt carefully — structure the document to score maximum rubric points
2. Use the `academic-writing` skill for style, citation format, and structure
3. Start with an **outline** — present it and wait for approval before drafting
4. Draft section by section — Abstract → Introduction → [Body] → Conclusion → References
5. Flag any claims that need citations with `[CITATION NEEDED: topic]`
6. Connect to Kyle's real projects where relevant (see `memory/project-context.md`)
7. End with a checklist of what's missing before submission
