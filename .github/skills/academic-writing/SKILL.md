---
name: academic-writing
description: "Use when: writing or structuring an academic paper, formatting citations in IEEE or APA, writing an abstract, following technical writing style rules, reporting statistics or ML results, or creating a paper outline."
---

# Academic Writing

> Used by: @academic

## Paper Structure (CS/AI/Engineering)

```
1. Abstract          (150–250 words, standalone)
2. Introduction      (problem, motivation, contributions, roadmap)
3. Related Work      (prior art, contrast with this work)
4. Background        (theory readers need)
5. Methodology       (what + how — must be reproducible)
6. Experiments       (setup, datasets, metrics, baselines)
7. Results           (tables, figures, interpretation)
8. Discussion        (what results mean, surprises)
9. Limitations       (what you didn't do, threats to validity)
10. Conclusion       (summary + future work)
11. References
```

## Abstract Formula

```
[Context/Motivation]: Why this problem matters (1–2 sentences)
[Problem]: The specific gap or challenge (1 sentence)
[Approach]: What you did (1–2 sentences, technical)
[Results]: Key finding with numbers (1–2 sentences)
[Implication]: Why it matters (1 sentence)
```

## Citation Formats

### IEEE
```
[1] A. Smith and B. Jones, "Attention is all you need," in Proc. NIPS, 2017, pp. 5998–6008.
[2] Y. LeCun, "Deep learning," Nature, vol. 521, no. 7553, pp. 436–444, May 2015.
```
In-text: `[1]` or `[1, 3]`

### APA
```
Smith, A., & Jones, B. (2017). Attention is all you need. Advances in Neural Information Processing Systems, 30, 5998–6008.
```
In-text: `(Smith & Jones, 2017)` or `Smith and Jones (2017) found...`

## Style Rules

### DO:
- Passive voice for methodology: *"The model was trained on..."*
- Active voice for contributions: *"We propose a novel framework..."*
- Define every acronym on first use: *"Reinforcement Learning from Human Feedback (RLHF)"*
- Quantify: not *"significantly faster"* but *"4.2× faster (p < 0.01)"*
- Hedge uncertain claims: *"suggests,"* *"appears to,"* *"may indicate"*

### AVOID:
- Contractions in formal research papers (don't → do not). Contractions are acceptable in discussion posts, reflective essays, and informal assignments.
- Informal language ("a lot of," "pretty good," "basically") in formal papers
- Unsupported superlatives ("best," "state-of-the-art" without citation)
- Vague quantifiers ("many," "several") — be specific
- Starting sentences with "Also" or "But"

## Banned Words & Phrases (AI Pattern Avoidance)

Never use these in any essay output. Force synonyms or full rephrasing.

### Banned Words
delve, tapestry, underscore, pivotal, realm, landscape, robust, leverage, multifaceted, nuanced (as filler), utilize (use "use"), facilitate, elucidate, aforementioned, commenced

### Banned Phrases
shed light on, it is worth noting, in today's rapidly evolving, a testament to, at the forefront, serves as a, plays a crucial role, in conclusion (find a less mechanical closer)

### Banned Connectors (overuse)
furthermore, moreover — limit to 1 combined per essay. Never use both.

### Banned Patterns
- Rule-of-three triplets used as a repeated rhetorical crutch ("X, Y, and Z" more than twice per essay)
- 3+ consecutive paragraphs starting with the same syntactic structure
- Opening with a dictionary definition or "Since the dawn of time..."
- Ending every paragraph with a restatement of its opening sentence

## Statistics Reporting

```
t-test:        t(48) = 3.21, p = .002, d = 0.91
ANOVA:         F(2, 147) = 8.34, p < .001, η² = .10
Correlation:   r(98) = .43, p < .001
Regression:    β = 0.31, SE = 0.07, t(198) = 4.43, p < .001
Accuracy:      Accuracy = 87.3% (95% CI: [84.1%, 90.5%])
```

## ML-Specific Conventions

- Always report: train/val/test split sizes
- Always report: random seed for reproducibility
- Report mean ± std over multiple runs (minimum 3, prefer 5)
- Baseline comparisons required — never report single-model results without context
- Use bold for best result in tables, underline for second-best

## Paper Checklist

- [ ] Thesis statement is specific and falsifiable
- [ ] Every claim supported by evidence or citation
- [ ] Related work distinguishes prior work from this work
- [ ] Methodology reproducible from description alone
- [ ] Results include error bars / significance tests
- [ ] Limitations honestly stated
- [ ] Abstract stands alone (no forward references)
- [ ] Citations in correct format (APA or IEEE as required)
- [ ] No contractions, no informal language
- [ ] All acronyms defined on first use
