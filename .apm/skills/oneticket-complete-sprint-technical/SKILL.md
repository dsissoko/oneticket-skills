---
name: oneticket-complete-sprint-technical
description: "Complete the Technical Notes section of a sprint.md file with architecture decisions, ADR references, and implementation guidance. Owned by @architect."
version: 1.0.0
---

# oneticket-complete-sprint-technical

Complete the `## Technical Notes` section of sprint documents created by `@po`.

`@architect` owns this section exclusively — `@po` creates the sprint shell and cross-references,
`@architect` adds the technical substance.

`docs_path` is always provided in the prompt — never resolve it yourself.

---

## What belongs in Technical Notes

The `## Technical Notes` section of a sprint contains:
- Technical implementation guidance for the user stories selected in this sprint
- References to ADRs that apply to this sprint's implementation
- Key technical constraints or decisions that `@leaddev` and `@dev` must be aware of
- Component boundaries, interface contracts, or data flow specifics relevant to this sprint

Technical Notes do NOT contain:
- Cross-references to epics or user stories (owned by `@po` in `## Cross-references`)
- Task decomposition or manifest content (owned by `@leaddev`)
- Business requirements or acceptance criteria (those live in the user stories)

---

## Mandatory Prerequisites

```
1. sprint.md shell   <docs_path>/how/sprints/sprint-N-<name>/sprint.md  ← created by @po
2. architecture.md   <docs_path>/how/architecture.md                     ← must exist
         ↓
3. ## Technical Notes completed in sprint.md                             ← @architect writes
4. ADRs created if needed  <docs_path>/how/adr-NNN-<name>.md            ← @architect authors
```

If sprint.md does not exist → stop, ask `@po` to create it first.
If architecture.md is missing → stop, create it first using `oneticket-c4`.

---

## Process

### Step 1 — Read the sprint

Read `<docs_path>/how/sprints/sprint-N-<name>/sprint.md`:
- Understand the sprint goal
- Identify the user stories selected (from `## Cross-references`)

### Step 2 — Read the architecture

Read `<docs_path>/how/architecture.md` — technical components, boundaries, interfaces.
Read `<docs_path>/how/c4/` — C4 diagrams if available.
Read existing ADRs in `<docs_path>/how/adr-NNN-*.md` if any.

### Step 3 — Identify technical decisions needed

For each user story in the sprint:
- Which components are involved?
- Are there architectural decisions to make or record?
- Are there existing ADRs that apply?
- What interfaces, data flows, or constraints must `@dev` know about?

### Step 4 — Author ADRs if needed

If a significant architectural decision must be made for this sprint, create an ADR:
- Use template at `.oneticket/templates/adr.md`
- Write to `<docs_path>/how/adr-NNN-<name>.md` (flat in `how/`, not in a subdirectory)
- Reference the ADR in `## Technical Notes` of the sprint

### Step 5 — Complete ## Technical Notes

Open `<docs_path>/how/sprints/sprint-N-<name>/sprint.md`.
Replace the `> Section owned by @architect` placeholder with the actual technical content.

Keep the section focused and actionable — `@leaddev` and `@dev` will read this before implementing.

If ADRs were created in step 4, reference them here:
```markdown
## Technical Notes

<technical implementation guidance>

ADRs applicable to this sprint:
- [adr-NNN — title](../adr-NNN-title.md)
```

---

## ADR Naming Convention

- File: `adr-NNN-<kebab-name>.md` — zero-padded 3-digit number + kebab-case title
- Examples: `adr-001-auth-strategy.md`, `adr-002-state-management.md`, `adr-003-audio-timing.md`
- Location: flat in `<docs_path>/how/` — never in a subdirectory

---

## Rules

- Never overwrite `## Cross-references` — that section is owned by `@po`
- Never modify any file outside `## Technical Notes` in sprint.md — only append/replace that section
- Always read architecture.md before writing Technical Notes
- Never invent technical decisions not grounded in the architecture or the user stories
- `docs_path` is always provided in the prompt — never resolve it yourself
- ADR H1 must be: `# ADR-NNN — <title>` — never use generic `# ADR`
- Sprint Technical Notes must be actionable — `@dev` must be able to implement without asking questions
