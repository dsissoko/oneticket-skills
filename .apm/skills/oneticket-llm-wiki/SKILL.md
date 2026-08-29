---
name: oneticket-llm-wiki
description: "Build and maintain a persistent Markdown knowledge wiki inspired by Andrej Karpathy's LLM Wiki pattern. Use when ingesting source material, integrating knowledge into an existing wiki, querying accumulated knowledge, or linting the wiki for coherence."
version: 0.1.0
---

# oneticket-llm-wiki

## Purpose

This skill defines the behavior for an LLM-maintained wiki: a persistent, human-readable knowledge base that accumulates synthesis over time instead of rediscovering the same source material on every query.

The wiki is not a RAG index. Raw sources remain available as evidence, while the wiki stores durable synthesized knowledge, explicit relationships, and provenance.

The default conceptual model is defined in `references/default-ontology.md`.

---

## Core Principles

1. **Persistent synthesis** — integrate useful knowledge into durable Markdown pages.
2. **Raw sources are immutable evidence** — do not rewrite source material to make it fit the wiki.
3. **Synthesize before duplicating** — update an existing concept or topic when appropriate instead of creating near-duplicates.
4. **Preserve provenance** — important claims must remain traceable to one or more sources.
5. **Prefer explicit relationships** — connect related concepts, claims, sources, people, organizations, and topics.
6. **Human-readable first** — the wiki must remain understandable and editable without specialized tooling.
7. **Ontology is project-specific** — use the default ontology as a starting point, then respect any project-local ontology that supersedes it.

---

## Expected Project Structure

A project may use any physical layout, but the following logical areas must exist:

```text
knowledge/
├── raw/                  # source material; immutable after ingestion
├── wiki/                 # synthesized persistent knowledge
├── ontology.md           # project-local ontology, if specialized
└── ingestion-log.md      # optional ingestion/provenance log
```

If `ontology.md` exists, treat it as authoritative for that project. Otherwise use `references/default-ontology.md`.

---

## Operations

### INGEST

Use when new source material enters the knowledge base.

1. Read the active ontology.
2. Inspect the new source and identify the knowledge it contributes.
3. Locate existing wiki pages covering the same concepts or topics.
4. Integrate new information into existing pages whenever possible.
5. Create a new page only when the knowledge represents a genuinely new concept, topic, actor, or coherent body of knowledge.
6. Record provenance for non-trivial claims.
7. Add or update cross-links to related pages.
8. Update any relevant indexes or navigation pages.
9. Record the ingestion event when an ingestion log is used.

Do not merely summarize the source into a new file. The goal is to evolve the knowledge base.

### QUERY

Use when answering a question from the accumulated wiki.

1. Start from the synthesized wiki, not the raw corpus.
2. Traverse related pages when the answer requires multiple concepts.
3. Consult raw sources when provenance, ambiguity, or missing detail requires verification.
4. Distinguish established knowledge from inference or unresolved uncertainty.
5. Prefer answers that reuse accumulated synthesis rather than rebuilding it from scratch.

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

When safe, repair structural issues directly. When a semantic conflict cannot be resolved from available evidence, record it explicitly rather than inventing a resolution.

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

- Never delete raw evidence solely because its knowledge has been synthesized into the wiki.
- Never fabricate provenance.
- Never create a new page when an existing page can be coherently extended.
- Never force project knowledge into the default ontology when a project-local ontology exists.
- Keep ontology concepts few and composable; avoid premature specialization.
- Treat uncertainty as first-class information.
- Prefer links and explicit relations over repeated prose.
- Keep implementation concerns separate from the conceptual ontology: storage engines, vector databases, event buses, and UI frameworks are tooling choices, not ontology concepts.

---

## Relationship to OneTicket

This skill is intentionally generic. It provides the knowledge-maintenance behavior and a minimal default ontology.

A future Agile Dev Wiki specialization can extend the ontology with software-delivery concepts such as `Project`, `Requirement`, `Epic`, `UserStory`, `Sprint`, `Decision`, `ArchitectureElement`, `QualityGate`, `Release`, `Environment`, and their lifecycle transitions without changing the core INGEST / QUERY / LINT model.
