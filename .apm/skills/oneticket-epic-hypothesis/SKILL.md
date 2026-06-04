---
name: oneticket-epic-hypothesis
description: Frame an epic as a testable hypothesis with target user, expected outcome, and validation method. Use when defining a major initiative before roadmap, discovery, or delivery planning.
version: 1.0.0
---

# oneticket-epic-hypothesis

## Purpose

Frame epics as testable hypotheses using an if/then structure that articulates the action, the target beneficiary, the expected outcome, and how to validate success.

Use this to manage uncertainty by making assumptions explicit, defining lightweight experiments, and establishing measurable success criteria before committing to full build-out.

This is not a requirements spec — it is a hypothesis being tested, not a feature committed to shipping.

---

## Format

### If/Then Hypothesis

```markdown
**If we** [action or solution on behalf of target persona]
**for** [target persona]
**Then we will** [attain or achieve a desirable outcome]
```

### Tiny Acts of Discovery Experiments

```markdown
**We will test our assumption by:**
- [Experiment 1 — low-cost, fast test]
- [Experiment 2 — another low-cost, fast test]
```

### Validation Measures

```markdown
**We know our hypothesis is valid if within** [timeframe]
**we observe:**
- [Quantitative measurable outcome]
- [Qualitative measurable outcome]
```

---

## Quality Checks

**"If we"** — specific action, not "improve the product"
**"For"** — specific persona, not generic "users"
**"Then we will"** — user outcome, not feature output
**Experiments** — days/weeks, not months; falsifiable; no full engineering builds
**Validation measures** — specific and measurable, realistic timeframe (2-4 weeks)

---

## Examples

✅ "If we add one-click Google Calendar integration for trial users, then we will increase activation rates by 20% within 30 days"

✅ "If we provide bulk delete functionality for power users managing 1000+ items, then we will reduce time spent on cleanup tasks by 70%"

❌ "If we build a dashboard, then users will use it" — vague, not measurable

---

## Experiment Types

- **Prototype + user testing** — fake the feature, test with 5-10 users
- **Concierge test** — manually perform the feature for a few users
- **Landing page test** — describe the feature, measure interest
- **Wizard of Oz test** — present as automated, do manually behind the scenes

---

## Process

1. Draft the If/Then hypothesis
2. Design lightweight experiments (not full builds)
3. Define validation measures with timeframe
4. Run experiments
5. Evaluate: validated → proceed to user stories | invalidated → kill or pivot | inconclusive → run more experiments

---

## Rules

- Hypotheses can (and should) be invalidated — that is the point
- Never skip experiments and go straight to build
- Frame epics as hypotheses *before* making stakeholder commitments
- Once validated → break down with `oneticket-epic-breakdown` and `oneticket-user-story`
