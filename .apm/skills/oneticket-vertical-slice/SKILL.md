---
name: oneticket-vertical-slice
description: Derive and document vertical implementation slices from epics, user stories and architecture. Writes to how/slices/ in the project docs_path.
version: 1.0.0
---

# oneticket-vertical-slice

Derive and document vertical implementation slices from the product and architecture knowledge available in `docs_path`.

A slice covers one end-to-end feature from user input to observable output, crossing all technical layers involved. A slice is never a technical layer alone.

The set of slices produced must cover all user stories of the epic — every US must appear in at least one slice.

`docs_path` is always provided in the prompt — never resolve it yourself.

---

## What is a Slice

A vertical slice is a small, testable, end-to-end implementation unit that:
- Crosses all technical layers (frontend, backend, API, data, etc.)
- Delivers observable value independently
- Maps to one or more user stories
- Is sized to be close to a PR in scope

A slice is NOT:
- A horizontal technical layer ("build the API", "build the UI")
- A task decomposition
- A copy of a user story

---

## Mandatory Prerequisites

Slices can only be derived after all of the following artifacts exist and are complete:

```
1. Epics        <docs_path>/what/epics/epic-N-<name>/epic.md
2. User Stories <docs_path>/what/epics/epic-N-<name>/user-stories/
3. Architecture <docs_path>/how/architecture.md
4. C4 diagrams  <docs_path>/how/c4/  (if available)
         ↓
5. Slices       <docs_path>/how/slices/slice-N-<name>/slice.md
```

If epics or user stories are missing → stop, create them first using `oneticket-user-story`.
If architecture is missing → stop, create it first using `oneticket-c4`.

---

## Process

### Step 1 — Read epics and user stories

Read ALL files under `<docs_path>/what/epics/`:
- Each `epic.md` — business scope and goals
- Each `user-stories/us-NNN-<name>.md` — expected behaviors and acceptance criteria

Do not proceed until every epic and user story has been read.

### Step 2 — Read architecture

Read `<docs_path>/how/architecture.md` — technical components, boundaries, interfaces.
Read `<docs_path>/how/c4/` — C4 diagrams if available.

Do not derive slices before understanding the technical boundaries.

### Step 3 — Identify slice boundaries

For each user story or group of related user stories:
1. Identify which technical components are involved (from architecture)
2. Determine the end-to-end flow
3. Define the smallest deliverable increment that is testable

### Step 4 — Write the slices

Use template at `.oneticket/templates/slice.md`.
Write to `<docs_path>/how/slices/slice-N-<name>/slice.md`.

---

## Slice Types

**Foundation slice (Walking Skeleton):** Always first. Minimal skeleton crossing all technical layers — configuration, layout, routing, theme, shared setup. Sequential — all other slices depend on it.

**Feature slices:** Each covers one end-to-end feature independently. Can be parallelized if their files are disjoint. All depend on the foundation slice.

This maps directly to the manifest structure:
- Task 0 = foundation slice → sequential, no `depends_on`
- Tasks 1..N = feature slices → parallel when files are disjoint, all with `depends_on: [task-0]`

---

## Sizing Guidelines

A well-sized slice:
- Can be implemented in 1-5 days
- Has clear acceptance criteria derived from its user stories
- Can be tested end-to-end independently
- Corresponds roughly to a PR

If a slice feels too large → split it using `oneticket-user-story-splitting` logic at the implementation level.

---

## Naming Convention

- Directory: `slice-N-<kebab-name>` — zero-padded number + kebab-case name
- Examples: `slice-1-user-login`, `slice-2-dashboard`, `slice-3-export`

---

## Rules

- The foundation slice must group all shared technical setup — never spread shared setup across multiple slices
- Always produce at least 2 slices: one foundation slice + one or more feature slices
- Never overwrite an existing slice — check before writing
- Never create a slice without reading the architecture first
- `docs_path` is always provided in the prompt — never resolve it yourself
- Slices derive from `what/epics/` — never invent content not backed by a user story
- Every user story must be referenced in at least one slice
- H1 must be descriptive: `# Slice N — <name>` — never use generic `# Slice`
