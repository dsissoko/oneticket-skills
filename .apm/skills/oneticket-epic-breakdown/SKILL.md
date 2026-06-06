---
name: oneticket-epic-breakdown
description: "Break down epics into user stories using 9 Humanizing Work split patterns. Use when a backlog item is too large to estimate, sequence, or deliver safely."
version: 1.0.0
---

# oneticket-epic-breakdown

## Purpose

Guide through breaking down epics into user stories using Richard Lawrence's Humanizing Work methodology — 9 splitting patterns applied sequentially.

Use this to identify which pattern applies, split while preserving user value, and evaluate splits based on what they reveal about low-value work to eliminate.

This ensures vertical slicing (end-to-end value) rather than horizontal slicing (technical layers).

---

## Key Concepts

### Core Principle: Vertical Slices Preserve Value

A user story is "a description of a change in system behavior from the perspective of a user." Splitting must maintain vertical slices — work that touches multiple architectural layers and delivers observable user value.

### The Three-Step Process

1. **Pre-Split Validation** — Check if story satisfies INVEST criteria (except "Small")
2. **Apply Splitting Patterns** — Work through 9 patterns sequentially until one fits
3. **Evaluate Splits** — Choose the split that reveals low-value work or produces equal-sized stories

### The 9 Splitting Patterns (In Order)

1. **Workflow Steps** — Thin end-to-end slices, not step-by-step
2. **Operations (CRUD)** — Create, Read, Update, Delete as separate stories
3. **Business Rule Variations** — Different rules = different stories
4. **Data Variations** — Different data types/structures
5. **Data Entry Methods** — Simple UI first, fancy UI later
6. **Major Effort** — "Implement one + add remaining"
7. **Simple/Complex** — Core simplest version first, variations later
8. **Defer Performance** — "Make it work" before "make it fast"
9. **Break Out a Spike** — Time-box investigation when uncertainty blocks splitting

---

## Process

### Step 1 — Pre-Split Validation (INVEST Check)

Before splitting, verify the story satisfies INVEST criteria (except "Small"):

- **Independent?** Can it be prioritized without hard dependencies on other stories?
- **Negotiable?** Does it leave room for implementation discovery?
- **Valuable?** Does it deliver observable value to a user? If not — don't split, combine.
- **Estimable?** Can the team size this relatively?
- **Testable?** Does it have concrete acceptance criteria?

If any check fails → fix the issue before splitting.

### Step 2 — Apply Splitting Patterns Sequentially

Work through patterns in order. Stop when one applies.

**Pattern 1 — Workflow Steps**
Split into thin end-to-end slices. Each story covers the full workflow with increasing sophistication, not individual steps.

**Pattern 2 — Operations (CRUD)**
"Manage", "handle", "maintain" signal multiple operations. Split into Create, Read, Update, Delete stories.

**Pattern 3 — Business Rule Variations**
Different rules for different scenarios (user types, regions, tiers) → one story per rule.

**Pattern 4 — Data Variations**
Different data types or structures → deliver simplest first, add variations separately.

**Pattern 5 — Data Entry Methods**
Fancy UI (date pickers, autocomplete) is independent of core functionality → basic input first, UI enhancement after.

**Pattern 6 — Major Effort**
When initial implementation carries most complexity and additions are trivial → "implement one + add remaining".

**Pattern 7 — Simple/Complex**
Identify the simplest version that still delivers value. Extract variations into separate stories.

**Pattern 8 — Defer Performance**
Split "make it work" from "make it fast". Non-functional requirements can follow functional delivery.

**Pattern 9 — Break Out a Spike**
Last resort when uncertainty prevents splitting. Time-box investigation (1-2 days) to answer specific questions, then restart at Pattern 1.

### Step 3 — Evaluate Split Quality

After splitting:
1. **Does this split reveal low-value work to deprioritize or eliminate?**
2. **Does this split produce more equally-sized stories?**

If neither criterion is satisfied → try a different pattern.

---

## Output Format

```markdown
# Epic Breakdown Plan

**Epic:** [Original epic]
**Splitting Pattern Applied:** [Pattern name]
**Rationale:** [Why this pattern fits]

## Story Breakdown

### Story 1: [Title] (Simplest Complete Slice)
- **As a** [persona]
- **I want to** [action]
- **so that** [outcome]

**Acceptance Criteria:**
- **Given:** [Preconditions]
- **When:** [Action]
- **Then:** [Outcome]

### Story 2: [Title]
[...]

## Split Evaluation
- Does this reveal low-value work? [Analysis]
- Does this produce equal-sized stories? [Analysis]
```

---

## Rules

- Always run INVEST check before applying patterns
- Never horizontal-slice (front-end story + back-end story = no user value)
- Each story must deliver observable user value independently
- If stories are still >5 days after splitting → restart at Pattern 1
- Spikes produce learning, not shippable code — after a spike, restart at Pattern 1
