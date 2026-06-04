---
name: oneticket-user-story-splitting
description: Break large stories or epics into smaller deliverable stories using 8 proven split patterns. Use when backlog items are too big for estimation, sequencing, or independent release.
version: 1.0.0
---

# oneticket-user-story-splitting

## Purpose

Break down large user stories, epics, or features into smaller, independently deliverable stories using systematic splitting patterns.

Use this to make work more manageable, reduce risk, enable faster feedback cycles, and maintain flow in agile development.

---

## The 8 Splitting Patterns

Work through patterns in order. Stop when one applies.

**Pattern 1 — Workflow Steps**
Does this story contain multiple sequential steps?
→ Split along those steps, each delivering end-to-end value.

**Pattern 2 — Business Rule Variations**
Does this story have different rules for different scenarios?
→ One story per rule variation.

**Pattern 3 — Data Variations**
Does this story handle different types of data or inputs?
→ One story per data type, simplest first.

**Pattern 4 — Acceptance Criteria Complexity**
Does this story have multiple "When" or "Then" statements?
→ Split along each When/Then pair — most common indicator.

**Pattern 5 — Major Effort**
Does this story require significant technical work deliverable incrementally?
→ Split by technical milestones.

**Pattern 6 — External Dependencies**
Does this story depend on multiple external systems or APIs?
→ One story per dependency.

**Pattern 7 — DevOps Steps**
Does this story require complex deployment or infrastructure work?
→ Split by operational complexity.

**Pattern 8 — Tiny Acts of Discovery (TADs)**
None of the above apply → unknowns block splitting.
→ Time-box an investigation experiment, then return to Pattern 1.

---

## Validation

After splitting, check each story:
1. Does it deliver user value independently?
2. Can it be developed independently?
3. Can it be tested independently?
4. Is it small enough for a sprint (1-5 days)?
5. Do all splits together equal the original?

---

## Anti-Patterns

- **Horizontal slicing** — "Story 1: Build API. Story 2: Build UI." → neither delivers user value
- **Over-splitting** — a 2-day story doesn't need splitting
- **Meaningless splits** — "First half of feature / Second half of feature"
- **Creating hard dependencies** — splits must be independently deliverable

---

## Rules

- Use `oneticket-user-story` to write the split stories
- Every split must have a distinct user outcome
- If split stories are still >5 days → apply patterns again
- TADs are experiments, not stories — use them to de-risk before writing stories
