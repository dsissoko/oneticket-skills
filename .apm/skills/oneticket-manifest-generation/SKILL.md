---
name: oneticket-manifest-generation
description: "Know the exact manifest format to produce when decomposing a request into sequential or parallel executable tasks for OneTicket."
version: 1.0.0
---

# Skill: oneticket-manifest-generation

## Role

This skill describes the exact manifest format to produce when a request
requires decomposition into agent-executable tasks.

---

## Mandatory format

The manifest must be written to `.oneticket/tasks/issue-{issue_number}/manifest.json`.
Content must be exactly this JSON (no comments, no surrounding markdown):

```json
{
  "issue": 42,
  "tasks": [
    {
      "id": "A",
      "branch": "task/issue-42-A",
      "file": "path/to/file.ext",
      "content": "complete and self-sufficient instruction for the executing agent",
      "depends_on": [],
      "status": "pending"
    },
    {
      "id": "B",
      "branch": "task/issue-42-B",
      "file": "path/to/other-file.ext",
      "content": "complete and self-sufficient instruction for the executing agent",
      "depends_on": ["A"],
      "status": "pending"
    }
  ]
}
```

---

## Validation rules — field by field

| Field | Rule |
|---|---|
| `issue` | Integer — exact GitHub issue number |
| `tasks` | Non-empty array |
| `id` | Unique uppercase letter: A, B, C... — no duplicates |
| `branch` | Always `task/issue-{issue}-{id}` — mechanically derived from id |
| `file` | Relative path from repo root — no leading `/` |
| `content` | Complete, precise, self-sufficient instruction — the agent has no other context |
| `depends_on` | Array of ids defined in this manifest — `[]` if no dependency |
| `status` | Always `"pending"` at creation — never anything else |
| `role` | **Optional** — agent profile name (`architect`, `dev`, `qa`, `analyst`). If present, opencode loads the agent profile via `default_agent`. If absent, generic worker (default behavior). |

---

## Dependency graph rules

- **Number of tasks**: the manifest must not exceed `max_tasks` tasks in total. Before finalizing, count tasks produced. If this exceeds `max_tasks`, group closest tasks (same role, same functional scope) to bring total to `max_tasks` or less. Never ignore this constraint.
- **Sequential vs parallel**: by default, use strictly sequential tasks (each task depends on the previous one: A → B → C). Only use parallel tasks when the prompt explicitly contains `--parallel` AND tasks touch strictly distinct files with no shared writes.
- **No cycles**: A cannot depend on B if B depends on A
- **Valid referenced ids**: `depends_on` can only contain ids defined in `tasks`
- **Granularity**: a task may produce multiple files — but no two parallel tasks may write to the same file. If two tasks need the same file, sequence them with `depends_on`.

### Merge conflict prevention — absolute rules

These rules are critical. Violating them causes `add/add` merge conflicts that block the pipeline and require human intervention.

- **No shared output files** — no two tasks in the manifest may declare the same `file`. If two tasks need to contribute to the same file, they must be strictly sequenced via `depends_on`, never run in parallel.
- **Write once per file** — once a task produces a file, no other task may write to that same file, except a dedicated cross-reference task (see below).
- **Cross-reference task** — if a task updates files already produced by other tasks (e.g. adding `## Related Slices`, `## Related Epics` sections to existing files), it must be the **last task** in the manifest and must declare `depends_on` on **every task whose output files it touches**. Never run it in parallel with file-producing tasks.
- **Read ≠ write dependency** — a task that only reads a file for context does not need a `depends_on`. Only tasks that write to the same file create a hard dependency.
- **Before finalizing**: scan the manifest for duplicate `file` values. If any two tasks share the same `file`, fix the manifest before committing.

---

## Rule on the `content` field

The `content` field is the **sole instruction** the executing agent will receive.
It must be:
- **Self-sufficient**: the agent has access to no other context than this field
- **Precise**: specify exactly what the file must contain or do
- **Complete**: include references to spec files if needed (`read docs/us/A1.md`)
- **Not just a title**: `"Implement login"` is insufficient — `"Read docs/us/login.md and implement src/auth/login.ts with associated tests"` is correct

---

## Example — 3 sequential tasks (default behavior)

```json
{
  "issue": 42,
  "tasks": [
    { "id": "A1", "branch": "task/issue-42-A1", "file": "src/models/user.ts",
      "content": "Read docs/epics/A.md section User Model. Implement src/models/user.ts with the described fields.",
      "depends_on": [], "status": "pending" },
    { "id": "A2", "branch": "task/issue-42-A2", "file": "src/models/product.ts",
      "content": "Read docs/epics/A.md section Product Model. Implement src/models/product.ts.",
      "depends_on": [], "status": "pending" },
    { "id": "B1", "branch": "task/issue-42-B1", "file": "src/services/auth.ts",
      "content": "Read docs/epics/B.md section Auth. Implement src/services/auth.ts using src/models/user.ts.",
      "depends_on": ["A1", "A2"], "status": "pending" },
    { "id": "C1", "branch": "task/issue-42-C1", "file": "src/api/routes.ts",
      "content": "Read docs/epics/C.md. Implement src/api/routes.ts integrating auth and services.",
      "depends_on": ["B1"], "status": "pending" }
  ]
}
```

---

## Mandatory commit after writing the manifest

```bash
git add .oneticket/tasks/issue-<issue_number>/manifest.json
git commit -m "feat: decompose issue #<issue_number>"
```

Replace `<issue_number>` with the actual issue number. No variation accepted.

---

## Absolute constraints

- **Never push** — push is handled by the deterministic pipeline
- **Never create a PR** — PR creation is a user decision
- **Single file to produce**: `.oneticket/tasks/issue-{N}/manifest.json`
- **Valid JSON**: verify syntax before committing
