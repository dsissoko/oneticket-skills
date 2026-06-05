---
name: oneticket-reverse-doc
description: "Reverse-document an existing app by inferring missing or outdated documentation from the source code. Incremental update — never overwrite existing non-empty content. Use when asked to generate, update, or complete product spec, architecture, epics, user stories, C4 diagrams, or slices from an existing codebase."
version: "1.0.0"
---

# oneticket-retrodoc

## Purpose

Infer and generate structured documentation from an existing codebase.

Use this skill when the user asks to:
- generate documentation from code (`reverse-doc`, `retrodoc`, `doc from code`)
- update or complete missing documentation artifacts (epics, user stories, slices, C4, architecture)
- synchronize documentation with the current state of the implementation

This skill **orchestrates** — it delegates format and placement rules to the specialized skills below. Load them explicitly in the order listed.

`docs_path` and `app_path` are always provided in the prompt — never resolve them yourself.

---

## Skills to load

Load each skill explicitly before using its rules:

```
skill("oneticket-doc-structure")   — structure, templates, placement, frontmatter rules
skill("oneticket-user-story")      — user story format (Mike Cohn + Gherkin)
skill("oneticket-epic-breakdown")  — epic decomposition patterns
skill("oneticket-c4")              — C4 architecture diagrams in Mermaid
skill("oneticket-vertical-slice")  — vertical slice derivation from epics + architecture
```

Load only what you need based on the user request and the audit results.
If the request targets only user stories → load `oneticket-doc-structure` + `oneticket-user-story`.
If the request targets slices → also load `oneticket-c4` + `oneticket-vertical-slice`.
If the request is global → load all five.

---

## The two pivot documents

All documentation artifacts derive from two source-of-truth files:

```
product-spec.md   → functional vision → epics → user stories
architecture.md   → technical vision  → C4 diagrams → slices
```

**If these files are absent or empty** → infer them from the code **first**, before deriving any downstream artifact.

**If these files exist and are non-empty** → read them as context before generating anything downstream. Never overwrite them unless the user explicitly asks for an update.

---

## Phase 1 — Audit existing documentation

```
1. Load skill("oneticket-doc-structure")
2. Read docs_path/ completely — list every existing file
3. Compare against the reference structure from oneticket-doc-structure
4. Identify:
   - Files that exist and are non-empty     → read as context, do not overwrite
   - Files that exist but are empty/stub    → candidate for generation
   - Files that are missing entirely        → candidate for creation
5. Scope the work to what the user request targets + what is missing
```

Do not create anything yet. Complete the audit first.

---

## Phase 2 — Code discovery

Read `app_path/src/` to infer what the application actually does:

```
src/models/        → data models, types, domain concepts
src/hooks/         → business logic, state management, persistence
src/screens/       → user-facing screens and their responsibilities
src/components/    → UI components and their roles
src/utils/         → utility logic (filtering, formatting, color, etc.)
src/main.tsx       → routing — which screen is on which route
package.json       → stack, dependencies, project name
vite.config.ts     → build configuration, base path
```

From this reading, derive:
- **User-facing features** — what can a user do in this app?
- **Data flows** — how is data created, stored, retrieved, filtered?
- **Technical stack** — frameworks, libraries, persistence mechanism
- **Architectural boundaries** — frontend only? API calls? localStorage? MSW?

---

## Phase 3 — Ordered generation

Execute only what the user request targets and the audit identifies as missing or stale.
Always respect this order — upstream artifacts must exist before downstream ones:

```
1. product-spec.md      → inferred from codebase — what does this app do?
2. architecture.md      → inferred from stack + src/ structure
3. epic.md              → derived from product-spec — what are the functional epics?
4. user stories         → load skill("oneticket-user-story")
                          derived from epic + code — one story per observable feature
5. C4 diagrams          → load skill("oneticket-c4")
                          derived from architecture + code
6. slices               → load skill("oneticket-vertical-slice")
                          derived from user stories + architecture
```

**Skip any step if the target file exists and is non-empty**, unless the user explicitly asked to update it.

---

## Inference rules

- **Every claim must be traceable to the code** — never invent features, users, or technical components that do not exist in `app_path/src/`
- **If ambiguous** → add a `<!-- TODO: verify -->` comment in the generated doc rather than guessing
- **Each user story** = one observable, testable feature in the code (a screen, a hook behavior, a filter, a form submission)
- **Each slice** = one coherent group of files in `src/` that together deliver end-to-end value
- **Personas** = infer from screen names and use case descriptions — use specific names (`journal user`, `reader`) not generic `user`

---

## Writing rules

- Load `oneticket-doc-structure` first — use its templates for every new file
- Never overwrite a non-empty file — read it, then update or append only what is missing
- Always include `title:` frontmatter — required by Starlight, build will fail without it
- `docs_path` and `app_path` are provided in the prompt — never resolve them yourself
- Commit message: `docs: @po reverse-doc <scope> — <current_project>`

---

## Scope examples

| User request | What to generate |
|---|---|
| "generate full doc from code" | All missing artifacts in order (phases 1→3) |
| "update epics and user stories for tag filtering" | Audit what/epics/ → update or create relevant US only |
| "generate C4 diagrams" | Audit how/c4/ → load oneticket-c4 → generate missing diagrams |
| "sync the slices with the current implementation" | Audit how/slices/ → load oneticket-vertical-slice → update stale slices |
| "create the product spec" | Infer product-spec.md from code — only if absent or empty |
