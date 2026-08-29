---
name: llm-wiki
description: "Build and maintain a persistent Markdown knowledge wiki inspired by Andrej Karpathy's LLM Wiki pattern. Use when ingesting input material, integrating knowledge into an existing wiki, querying accumulated knowledge, or linting the wiki for coherence."
version: 0.1.0
---

# llm-wiki

## Purpose

This skill defines the behavior for an LLM-maintained wiki: a persistent, human-readable knowledge base that accumulates synthesis over time instead of rediscovering the same input material on every query.

Input material remains available as evidence, while the wiki stores durable synthesized knowledge, explicit relationships, provenance, a navigable index, and an append-only activity log.

The default conceptual model is defined in `references/default-ontology.md`.

---

## Core Principles

1. **Persistent synthesis** — integrate useful knowledge into durable Markdown pages.
2. **Input material remains evidence** — do not rewrite input material to make it fit the wiki.
3. **Synthesize before duplicating** — update an existing concept or topic when appropriate instead of creating near-duplicates.
4. **Preserve provenance** — important claims must remain traceable to one or more inputs.
5. **Prefer explicit relationships** — connect related concepts, claims, inputs, people, organizations, and topics.
6. **Human-readable first** — the wiki must remain understandable and editable without specialized tooling.
7. **Ontology is project-specific** — use the default ontology as a starting point, then respect any project-local ontology that supersedes it.
8. **Index first** — `docs/wiki/index.md` is the primary entry point into the accumulated wiki.
9. **Keep an activity trail** — record INGEST, QUERY, and LINT operations in `docs/wiki/log.md`.

---

## Default Repository Structure

The skill is designed to work immediately in the active repository of an AI coding agent or CLI. Unless explicitly overridden, use these paths relative to the repository root:

```text
docs/
├── input/                 # incoming information not yet integrated into the wiki
├── wiki/                  # synthesized persistent knowledge
│   ├── index.md           # primary catalog and navigation entry point
│   ├── log.md             # append-only activity log
│   └── ...                # synthesized wiki pages
└── ontology/              # project-local ontology
    └── ontology.md        # optional specialization
```

Default paths:

- `input_path`: `docs/input/`
- `wiki_path`: `docs/wiki/`
- `index_path`: `docs/wiki/index.md`
- `log_path`: `docs/wiki/log.md`
- `ontology_path`: `docs/ontology/`

`docs/input/` contains external or newly produced information that has not yet been integrated into the wiki ontology. It may contain documents, analyses, notes, transcripts, extracted information, or other material prepared by upstream skills or agents.

`docs/wiki/` contains the persistent synthesized knowledge maintained by this skill.

`docs/wiki/index.md` is the primary entry point into the wiki. It must provide a concise catalog of wiki pages with links and short descriptions sufficient for an agent or human to discover relevant knowledge before scanning the full wiki.

`docs/wiki/log.md` is an append-only chronological log of significant INGEST, QUERY, and LINT operations. Each entry should be concise and include the operation type, date/time when available, relevant inputs or pages, and a short description of what changed or was queried.

`docs/ontology/` contains the ontology governing how knowledge is represented and related in the wiki. If `docs/ontology/ontology.md` exists, treat it as authoritative. Otherwise use `references/default-ontology.md` as the reference ontology.

If `docs/input/` or `docs/wiki/` do not exist when the skill first needs to write to them, create the required directories. If `docs/wiki/index.md` or `docs/wiki/log.md` do not exist, initialize them automatically before the first INGEST operation.

Do not create `docs/ontology/ontology.md` automatically. The absence of a project-local ontology means the default ontology from this skill remains active.

The default paths may be overridden by the caller when a repository requires a different layout.

---

## Knowledge Discovery and Indexing

`docs/wiki/index.md` is part of the wiki itself. It is persistent, versioned, human-readable, and maintained by this skill. It remains the canonical navigation map for the wiki regardless of which retrieval tools are available.

External search and indexing capabilities exposed by the agent environment may complement this navigation model. They are useful for larger wikis, semantic discovery, hybrid retrieval, or locating material that is difficult to identify from the index alone.

When appropriate, use available retrieval tooling to improve discovery, then read the authoritative Markdown pages before synthesizing an answer. Such tooling is an access layer over the wiki, not part of the wiki's storage model or ontology.

QMD is one example of a compatible retrieval tool. When QMD search capabilities are exposed to the agent, they may be used for lexical, semantic, or hybrid discovery over the Markdown corpus. QMD configuration, collection management, indexing, and embedding are independent operational concerns and are not responsibilities of this skill.

Any external search index must be treated as derived and rebuildable. It must not replace `docs/wiki/index.md`, become a source of truth, or be required for the wiki lifecycle to remain valid.

---

## Operations

### INGEST

Use when new material enters `docs/input/` or is otherwise provided for integration.

1. Ensure `docs/wiki/index.md` and `docs/wiki/log.md` exist; initialize them if necessary.
2. Read the active ontology.
3. Read `docs/wiki/index.md` before scanning individual wiki pages.
4. Inspect the new input and identify the knowledge it contributes.
5. Locate existing wiki pages covering the same concepts or topics, using appropriate repository search or retrieval capabilities when useful.
6. Integrate new information into existing pages whenever possible.
7. Create a new page only when the knowledge represents a genuinely new concept, topic, actor, or coherent body of knowledge.
8. Record provenance for non-trivial claims.
9. Add or update cross-links to related pages.
10. Update `docs/wiki/index.md` with links and concise descriptions for all relevant pages.
11. Append a concise INGEST entry to `docs/wiki/log.md` describing the input processed and the wiki pages created or changed.

Do not merely summarize each input into a new file. The goal is to evolve the knowledge base.

### QUERY

Use when answering a question from the accumulated wiki.

1. Start with `docs/wiki/index.md` as the persistent navigation map of the wiki.
2. Discover the most relevant knowledge using the index, links, repository search, and any appropriate retrieval capabilities exposed by the agent environment.
3. Read the relevant synthesized Markdown pages rather than relying on search-result snippets or derived indexes as authoritative content.
4. Traverse related pages when the answer requires multiple concepts.
5. Consult `docs/input/` when provenance, ambiguity, or missing detail requires verification.
6. Distinguish established knowledge from inference or unresolved uncertainty.
7. Prefer answers that reuse accumulated synthesis rather than rebuilding it from scratch.
8. Append a concise QUERY entry to `docs/wiki/log.md` when the query materially uses or tests the accumulated wiki.

### LINT

Use to maintain wiki quality over time.

Check for:
- duplicate or overlapping pages
- contradictory claims
- orphan pages
- broken or weak cross-links
- unsupported claims
- stale summaries after newer evidence was ingested
- concepts that should be merged or split
- ontology drift
- inconsistent naming
- missing or stale entries in `docs/wiki/index.md`

When safe, repair structural issues directly. Keep `docs/wiki/index.md` synchronized with the actual wiki contents. When a semantic conflict cannot be resolved from available evidence, record it explicitly rather than inventing a resolution.

Append a concise LINT entry to `docs/wiki/log.md` summarizing checks performed and any repairs or unresolved issues.

### EVOLVE ONTOLOGY

Use only when the current ontology repeatedly fails to represent the knowledge being accumulated.

1. Identify the recurring modeling problem.
2. Prefer the smallest extension that solves it.
3. Preserve backward compatibility where practical.
4. Document the reason for the change.
5. Migrate existing pages only when the semantic benefit is clear.

Do not evolve the ontology for one-off exceptions.

---

## Rules

- Never delete input evidence solely because its knowledge has been synthesized into the wiki.
- Never fabricate provenance.
- Never create a new page when an existing page can be coherently extended.
- Never force project knowledge into the default ontology when a project-local ontology exists.
- Never rewrite or truncate existing `docs/wiki/log.md` history; append new entries.
- Keep `docs/wiki/index.md` concise and synchronized with the actual wiki contents.
- Treat external search indexes as derived, disposable access layers rather than canonical knowledge.
- Do not make the validity of the wiki dependent on QMD or any other external retrieval engine.
- Keep ontology concepts few and composable; avoid premature specialization.
- Treat uncertainty as first-class information.
- Prefer links and explicit relations over repeated prose.
- Keep implementation concerns separate from the conceptual ontology: storage engines, vector databases, event buses, search engines, and UI frameworks are tooling choices, not ontology concepts.

---

## Specialization

This skill is domain-independent. Its core INGEST / QUERY / LINT behavior can operate against different project ontologies.

Examples of possible specializations include software delivery, scientific research, recipe discovery, investment research, or any other domain where the macro objective requires different entities, relations, or lifecycle semantics.
