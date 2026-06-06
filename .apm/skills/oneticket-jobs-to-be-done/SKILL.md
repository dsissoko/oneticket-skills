---
name: oneticket-jobs-to-be-done
description: "Uncover customer jobs, pains, and gains in a structured JTBD format. Use when clarifying unmet needs, repositioning a product, or improving discovery and messaging."
version: 1.0.0
---

# oneticket-jobs-to-be-done

## Purpose

Systematically explore what customers are trying to accomplish (functional, social, emotional jobs), the pains they experience, and the gains they seek.

Use this to uncover unmet needs, validate product ideas, and ensure the solution addresses real motivations — not just surface-level feature requests.

---

## Key Concepts

### Customer Jobs

- **Functional jobs** — tasks customers need to perform
- **Social jobs** — how customers want to be perceived
- **Emotional jobs** — emotional states customers seek or avoid

### Pains

Challenges, costliness, common mistakes, unresolved problems that get in the way.

### Gains

Expectations, savings, adoption factors, outcomes that improve the customer's situation.

---

## Process

1. Define the target customer segment and situation
2. Explore functional, social, and emotional jobs
3. Identify pains (challenges, costs, mistakes, gaps)
4. Uncover gains (expectations, savings, adoption factors)
5. Prioritize by intensity — not all pains are equal
6. Validate with data or customer interviews

---

## Output Format

```markdown
## Customer Segment: [Description]

### Functional Jobs
- [Job 1]
- [Job 2]

### Social Jobs
- [Job 1]

### Emotional Jobs
- [Job 1]

### Top Pains (ranked by intensity)
1. [Pain 1 — intensity: high]
2. [Pain 2 — intensity: medium]

### Top Gains
1. [Gain 1]
2. [Gain 2]
```

---

## Rules

- Jobs are solution-agnostic ("communicate with my team", not "use email")
- Social and emotional jobs drive adoption as much as functional ones
- Never fabricate JTBD without real customer research or information provided by the user
- Rank pains by intensity before acting on them
- Use findings to inform `oneticket-epic-hypothesis` and `oneticket-user-story`
