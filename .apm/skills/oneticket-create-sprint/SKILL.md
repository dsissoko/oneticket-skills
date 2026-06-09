---
name: oneticket-create-sprint
description: "Create a sprint document (sprint.md) from selected user stories, including goal description, cross-references, and GitHub Milestone. Owned by @po."
version: 1.0.0
---

# oneticket-create-sprint

Create and maintain sprint documents under `<docs_path>/how/sprints/`.

A sprint is a time-boxed delivery iteration that selects user stories from the backlog,
defines a clear goal, and tracks delivery via a GitHub Milestone.
`@po` owns the sprint document — `@architect` owns only the `## Technical Notes` section.

`docs_path` is always provided in the prompt — never resolve it yourself.

---

## What is a Sprint

A sprint:
- Is time-boxed (typically 1-2 weeks)
- Selects a subset of user stories from the backlog that can be delivered within the timebox
- Has a single, clear sprint goal
- Is tracked via a GitHub Milestone
- Contains cross-references to the epics, user stories, and ADRs it covers
- Has a `## Technical Notes` section completed by `@architect` after `@po` creates the shell

A sprint is NOT:
- A technical decomposition (that is `@architect`'s role via Technical Notes)
- An implementation task list (that is `@leaddev`'s role via manifest)
- A copy of all user stories from an epic

---

## Mandatory Prerequisites

A sprint can only be created after the following artifacts exist and are complete:

```
1. Epics        <docs_path>/what/epics/epic-N-<name>/epic.md
2. User Stories <docs_path>/what/epics/epic-N-<name>/user-stories/
         ↓
3. Sprint       <docs_path>/how/sprints/sprint-N-<name>/sprint.md  ← @po creates shell
4. Tech Notes   ## Technical Notes in sprint.md                    ← @architect completes
```

If epics or user stories are missing → stop, create them first.

---

## Process

### Step 1 — Read the backlog

Read ALL files under `<docs_path>/what/epics/`:
- Each `epic.md` — business scope and goals
- Each `user-stories/us-NNN-<name>.md` — expected behaviors and acceptance criteria

Identify which user stories are not yet assigned to a sprint.

### Step 2 — Select user stories for this sprint

Select the user stories that:
- Form a coherent, deliverable subset
- Together achieve a single, clear sprint goal
- Fit within a reasonable timebox

The sprint goal must be expressible in one sentence.

### Step 3 — Create the sprint file

Use template at `.oneticket/templates/sprint.md`.
Write to `<docs_path>/how/sprints/sprint-N-<name>/sprint.md`.

Fill in:
- Title and goal description (free text — no table, no date fields)
- `## Cross-references` section with links to selected epics, user stories, and any relevant ADRs

Leave `## Technical Notes` section as-is with its `> Section owned by @architect` placeholder.

### Step 4 — Create the GitHub Milestone

Create a GitHub Milestone via the API:
```bash
gh api repos/{owner}/{repo}/milestones \
  --method POST \
  --field title="Sprint N — <goal>" \
  --field description="<sprint goal sentence>"
```

Then assign the selected issues to the milestone:
```bash
gh issue edit <issue-number> --milestone "<milestone-title>"
```

### Step 5 — Update cross-references

Update `## Related Sprints` in:
- Each `epic.md` that has US selected in this sprint
- Each `us-NNN.md` selected in this sprint

Cross-reference link format — filename only, no relative path, no `../`:
| From file | Link format | Example |
|---|---|---|
| `epic.md` → sprint | `[Sprint N — name](sprint-N-name/sprint.md)` | `[Sprint 1 — Foundation](sprint-1-foundation/sprint.md)` |
| `us-NNN.md` → sprint | `[Sprint N — name](sprint-N-name/sprint.md)` | `[Sprint 1 — Foundation](sprint-1-foundation/sprint.md)` |

**CRITICAL — never write `../` in cross-reference links. Filename only.**

---

## Naming Convention

- Directory: `sprint-N-<kebab-name>` — sequential number + kebab-case name reflecting the sprint goal
- Examples: `sprint-1-foundation`, `sprint-2-solfege-theme`, `sprint-3-animated-score`

Numbers start at 1 (not 0): `sprint-1`, `sprint-2`, `sprint-3`.

---

## Rules

- Never overwrite an existing sprint — check before writing
- Never include ALL user stories of an epic in one sprint — sprints are delivery slices of the backlog
- Leave `## Technical Notes` untouched — `@architect` completes it in a subsequent step
- Always create the GitHub Milestone after creating the sprint file
- Always update cross-references in epics and user stories after creating the sprint
- `docs_path` is always provided in the prompt — never resolve it yourself
- H1 must be descriptive: `# Sprint N — <goal>` — never use generic `# Sprint`
- Every sprint must have at least one user story in its cross-references
