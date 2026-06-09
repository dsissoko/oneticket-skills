---
name: oneticket-init-knowledge
description: "Initialize and validate the mandatory knowledge base before any documentation or development cycle. Enforces sequential human-gated validation of product-spec.md, architecture.md, and epic-0-mvp."
version: 2.0.0
---

# oneticket-init-knowledge

Generate the mandatory project knowledge files from all available context.
At each run: read `docs_path` to know what exists, read the issue body and previous comments for available information.
Ask questions only for what is genuinely missing after reading everything.
One run = one manifest produced, or one question set if information is insufficient.

`docs_path` is always provided in the prompt — never resolve it yourself.

---

## Production process

Refer to `oneticket-doc-structure` for all files to produce, their placement and naming conventions, and their templates.

**All 9 steps must be included in a single manifest — never split across multiple runs or handoffs.**

Respect these dependencies when building the manifest:

1. `<docs_path>/what/product-spec.md` — no dependency — role: analyst
2. `<docs_path>/what/epics/epic-0-mvp/epic.md` — depends on product-spec.md — role: analyst
3. `<docs_path>/what/epics/epic-0-mvp/user-stories/us-*.md` — depends on epic.md — role: analyst
4. `<docs_path>/how/architecture.md` — depends on product-spec.md + all us-*.md — role: architect
5. `<docs_path>/how/c4/system-context.md` — depends on architecture.md — role: architect
6. `<docs_path>/how/c4/containers.md` — depends on architecture.md — role: architect
7. `<docs_path>/how/sprints/sprint-1-<name>/sprint.md` — depends on all us-*.md — role: po
   `@po` creates the sprint shell: goal description, selected US, cross-references (epic ↔ US ↔ sprint).
   The set of sprints must cover all user stories of the epic.
8. `## Technical Notes` in sprint.md — depends on step 7 + architecture.md — role: architect
   `@architect` completes the Technical Notes section of each sprint file produced in step 7.
   This task modifies the file produced in step 7 — it must declare `depends_on` on step 7.
9. cross-references — depends on ALL task IDs from steps 2, 3, 7 — role: po
   **Always the last task in the manifest — no exception.**
   **Must declare `depends_on` on every task whose output files it modifies.**
   One single task — read all produced sprint files and update:
   - `## Related Sprints` in `epic.md`
   - `## Related Sprints` in all `us-*.md`
   - Cross-reference links in each `sprint.md` (epic ↔ US back-links)

   This task modifies files produced by steps 2, 3, and 7 — it must therefore depend on ALL
   tasks from steps 2, 3, and 7. Running it in parallel with any of these tasks will cause
   an `add/add` merge conflict that blocks the pipeline.

   The `depends_on` list must include every task ID from steps 2, 3, and 7 — no exception.

   Cross-reference links — filename only convention:

   When writing `## Related Sprints`, `## Related Epics`, `## Related User Stories` sections,
   write the **filename only** — no relative path, no `../`.
   The build script resolves the correct path automatically.

   | From file | Link format | Example |
   |---|---|---|
   | `epic.md` → sprint | `[Sprint N — name](sprint-N-name/sprint.md)` | `[Sprint 1 — Foundation](sprint-1-foundation/sprint.md)` |
   | `epic.md` → us | `[US-NNN — name](us-NNN-name.md)` | `[US-001 — Setup](us-001-setup.md)` |
   | `us-NNN.md` → sprint | `[Sprint N — name](sprint-N-name/sprint.md)` | `[Sprint 1 — Foundation](sprint-1-foundation/sprint.md)` |
   | `us-NNN.md` → epic | `[Epic N — name](epic-N-name/epic.md)` | `[Epic 0 — MVP](epic-0-mvp/epic.md)` |
   | `sprint.md` → epic | `[Epic N — name](epic-N-name/epic.md)` | `[Epic 0 — MVP](epic-0-mvp/epic.md)` |
   | `sprint.md` → us | `[US-NNN — name](us-NNN-name.md)` | `[US-001 — Setup](us-001-setup.md)` |

   **CRITICAL — never write `../` in cross-reference links. Filename only.**

---

## Sequence

The goal is to complete the knowledge base under `docs_path`
following the structure defined in ## Production process.

Some files require the expertise of specialized agents to be produced correctly —
architecture.md and Technical Notes are better handled by @architect who owns the technical decisions,
product-spec, epics, user stories, and sprints are better handled by @analyst/@po who own the functional documentation.
Delegating to the right role produces better results than doing everything yourself.

All content is derived from what the user has provided.
If the available information is not sufficient to produce a file,
use the relevant question set to collect what is missing before producing anything.

Once the information is sufficient, delegate to the most competent role for each file
by producing a manifest as defined in ## Production process — do not create the files directly.
Assign roles to tasks as defined in ## Production process.

---

## Question sets

### For `product-spec.md`

If the information provided is insufficient to produce `product-spec.md`, ask:

1. What is the product name?
2. What problem does it solve, and for whom? (2-3 sentences)
3. What are the 2-3 main capabilities for V1?
4. Who are the main users or actors?
5. Are there any business rules or constraints to note from the start?

### For `architecture.md`

If the information provided is insufficient to produce `architecture.md`, ask:

1. What is the technical stack? (languages, frameworks, persistence)
2. What are the main system components?
3. What are the key interfaces or integration points?
4. Are there any technical constraints or non-functional requirements?

### For `epic-0-mvp/epic.md` and user stories

If the information provided is insufficient, ask:

1. Does this MVP scope seem correct? (propose 2-3 sentences from product-spec.md)
2. What are the 2-3 most important user stories?
   (Format: As a <user>, I want <action>, so that <outcome>)

---

## When all files exist and are valid

Post a comment confirming the knowledge base is complete and suggesting next steps:
- Decompose additional epics → `oneticket-epic-breakdown`
- Document architecture → `oneticket-c4`
- Plan next sprint → `oneticket-create-sprint`

---

## Rules

- Read before writing — never overwrite a valid file
- Never invent content — only use what the human explicitly provided
- Use templates in `.oneticket/templates/` when creating files
- `docs_path` is always provided in the prompt — never resolve it yourself
- One question set per run — do not chain multiple question sets in one run
- When creating `product-spec.md`, replace the `<!-- SITE_DESCRIPTION: ... -->` placeholder with a real one-sentence description of the product (max 160 chars)
- After creating any documentation file, immediately commit and push it
