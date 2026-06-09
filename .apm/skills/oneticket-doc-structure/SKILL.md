---
name: oneticket-doc-structure
description: "Know and maintain the documentation structure for any project managed by OneTicket. Use to initialize, complete, or extend project documentation."
version: 2.0.0
---

# oneticket-doc-structure

## Purpose

This skill gives the agent full knowledge of the documentation structure used by OneTicket projects.

Use it to:
- initialize a documentation structure that does not exist yet
- add a missing artifact (epic, user story, sprint, ADR, etc.)
- complete or extend an existing structure
- verify that a document is in the right place

The agent never resolves `docs_path` — it is always provided in the prompt as a resolved value.

---

## Reference Structure

Every project documentation lives under `<docs_path>/` with the following structure:

```
<docs_path>/
├── what/
│   ├── README.md
│   ├── product-spec.md
│   └── epics/
│       └── epic-0-mvp/
│           ├── epic.md
│           └── user-stories/
│               ├── us-001-<name>.md
│               └── us-002-<name>.md
├── how/
│   ├── README.md
│   ├── architecture.md
│   ├── c4/
│   │   ├── system-context.md
│   │   ├── containers.md
│   │   ├── components.md
│   │   └── deployment.md
│   ├── sprints/
│   │   └── sprint-N-<name>/
│   │       └── sprint.md
│   └── adr-NNN-<name>.md
├── ship/
│   └── README.md
└── run/
    └── README.md
```

---

## Section Roles

| Section | Describes |
|---|---|
| `what/` | Product and business needs — technology-agnostic |
| `what/product-spec.md` | Global product vision |
| `what/epics/` | Functional epics and their user stories |
| `how/` | Technical solution implementing `what/` |
| `how/architecture.md` | Macro architecture |
| `how/c4/` | C4 architectural views |
| `how/sprints/` | Implementation sprints — time-boxed delivery iterations |
| `how/adr-NNN-<name>.md` | Architecture Decision Records — flat in `how/`, one file per decision |
| `ship/` | Build, packaging, CI/CD, release pipelines |
| `run/` | Infrastructure, operations, observability |

---

## File Templates

All templates are located in `.oneticket/templates/`:

| Document | Template |
|---|---|
| Product specification | `.oneticket/templates/product-spec.md` |
| Architecture | `.oneticket/templates/architecture.md` |
| Epic | `.oneticket/templates/epic.md` |
| User Story | `.oneticket/templates/us.md` |
| Sprint | `.oneticket/templates/sprint.md` |
| ADR | `.oneticket/templates/adr.md` |

Always use the corresponding template when creating a new document.
Copy the template content and fill in the sections — never create a document from scratch.

---

## Placement Rules

| Artifact | Location |
|---|---|
| Product specification | `<docs_path>/what/product-spec.md` |
| Epic | `<docs_path>/what/epics/epic-N-<name>/epic.md` |
| User Story | `<docs_path>/what/epics/epic-N-<name>/user-stories/us-NNN-<name>.md` |
| Architecture | `<docs_path>/how/architecture.md` |
| C4 system context | `<docs_path>/how/c4/system-context.md` |
| C4 containers | `<docs_path>/how/c4/containers.md` |
| C4 components | `<docs_path>/how/c4/components.md` |
| C4 deployment | `<docs_path>/how/c4/deployment.md` |
| Sprint | `<docs_path>/how/sprints/sprint-N-<name>/sprint.md` |
| ADR | `<docs_path>/how/adr-NNN-<name>.md` |
| CI/CD, build pipeline, deployment config | `<docs_path>/ship/<name>.md` |
| Operational runbook, infrastructure, observability | `<docs_path>/run/<name>.md` |

---

## Naming Conventions

| Artifact | Pattern | Example |
|---|---|---|
| Epic directory | `epic-N-<kebab-name>` | `epic-0-mvp`, `epic-1-authentication` |
| User Story file | `us-NNN-<kebab-name>.md` | `us-001-login.md`, `us-002-logout.md` |
| Sprint directory | `sprint-N-<kebab-name>` | `sprint-1-foundation`, `sprint-2-dashboard` |
| ADR file | `adr-NNN-<kebab-name>.md` | `adr-001-auth-strategy.md`, `adr-002-state-management.md` |

Numbers are zero-padded: `epic-0`, `epic-1`, `us-001`, `us-002`, `sprint-1`, `sprint-2`, `adr-001`, `adr-002`.

---

## Initialization

If the documentation structure does not exist yet at `<docs_path>`:

Run `node src/init-doc.mjs <docs_path>` — this creates the base directory structure with README files.
Then create content files as needed using the templates in `.oneticket/templates/`.

---

## Completion

If the structure already exists at `<docs_path>`:

- Create only what is missing
- Never overwrite existing content
- Use the templates for any new document
- Respect naming conventions and placement rules

---

## Rules

- Never overwrite an existing file — always check before writing
- Never create a document without using its template
- Always place documents in their canonical location
- Never invent content — only what the user explicitly provided
- `docs_path` is always provided in the prompt — never resolve it yourself
- **Every documentation file must include a `title:` frontmatter field** — required by the Starlight rendering engine:
  ```markdown
  ---
  title: 'Your Page Title'
  ---
  ```
  Without it, the doc site build will fail. This applies to every new file created in `<docs_path>/`.

---

## File Ownership Matrix — Parallel Task Safety

When documentation work is decomposed into parallel tasks (FAN-OUT manifest), file ownership rules **must** be respected to prevent `add/add` merge conflicts.

### Shared files — ONE task only, always sequential (last in DAG)

These files aggregate references to multiple other artifacts. If a manifest has multiple doc tasks, only **one task** may write to each of these files, and that task **must depend on ALL tasks** that produce the artifacts it references.

| File | Why shared |
|---|---|
| `what/product-spec.md` | Global product vision — reflects all features and epics |
| `what/epics/epic-N/epic.md` | References all user stories and sprints of the epic |
| `how/architecture.md` | Global technical vision — reflects all components and layers |

### Exclusive files — safe for parallel tasks

Each of these files belongs exclusively to one task. Two parallel tasks may safely produce these files simultaneously without conflict.

| File pattern | Ownership |
|---|---|
| `what/epics/epic-N/user-stories/us-NNN-*.md` | One task per user story |
| `how/c4/system-context.md` | One dedicated task |
| `how/c4/containers.md` | One dedicated task |
| `how/c4/components.md` | One dedicated task |
| `how/c4/deployment.md` | One dedicated task |
| `how/sprints/sprint-N-*/sprint.md` | `@po` creates the sprint shell + cross-references — `@architect` completes `## Technical Notes` — these are two sequential tasks on the same file, never parallel |
| `how/adr-NNN-*.md` | One task per ADR — owned by `@architect` |

### Sprint file — special two-phase ownership

A sprint file is written in two sequential phases:
1. `@po` creates `sprint.md` — goal description, cross-references (epic ↔ US ↔ sprint ↔ ADR links)
2. `@architect` completes `## Technical Notes` — depends on `@po`'s task

These two tasks **must never run in parallel** — the second must declare `depends_on` on the first.

### Canonical decomposition pattern

```
Tasks A, B, C...  → parallel   — exclusive files only (US, C4 diagrams, ADRs)
Task P            → sequential — @po creates sprint.md shell + cross-references
                                  depends_on: [A, B, C...]
Task Q (last)     → sequential — @architect completes ## Technical Notes in sprint.md
                                  depends_on: [P]
```

**Before finalizing any manifest:** scan for duplicate `file` values across tasks. If any two tasks declare the same file, sequence them with `depends_on` — never run them in parallel.
