---
name: llm-wiki
description: "Build and maintain a persistent Markdown knowledge wiki inspired by Andrej Karpathy's LLM Wiki pattern. Use when ingesting input material, integrating knowledge into an existing wiki, querying accumulated knowledge, or linting the wiki for coherence."
version: 0.1.0
---

# llm-wiki

## Purpose

This skill defines the behavior for an LLM-maintained wiki: a persistent, human-readable knowledge base that accumulates synthesis over time instead of rediscovering the same input material on every query.

Input material remains available as evidence, while the wiki stores durable synthesized knowledge, explicit relationships, and provenance.

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

---

## Default Repository Structure

The skill is designed to work immediately in the active repository of an AI coding agent or CLI. Unless explicitly overridden, use these paths relative to the repository root:

```text
docs/
├── input/                 # incoming information not yet integrated into the wiki
├── wiki/                  # synthesized persistent knowledge
└── ontology/              # project-local ontology
    └── ontology.md
```

Default paths:

- `input_path`: `docs/input/`
- `wiki_path`: `docs/wiki/`
- `ontology_path`: `docs/ontology/`

`docs/input/` contains external or newly produced information that has not yet been integrated into the wiki ontology. It may contain documents, analyses, notes, transcripts, extracted information, or other material prepared by upstream skills or agents.

`docs/wiki/` contains the persistent synthesized knowledge maintained by this skill.

`docs/ontology/` contains the ontology governing how knowledge is represented and related in the wiki. If `docs/ontology/ontology.md` exists, treat it as authoritative. Otherwise use `references/default-ontology.md` as the reference ontology.

The default paths may be overridden by the caller when a repository requires a different layout.

---

## Operations

### INGEST

Use when new material enters `docs/input/` or is otherwise provided for integration.

1. Read the active ontology.
2. Inspect the new input and identify the knowledge it contributes.
3. Locate existing wiki pages covering the same concepts or topics.
4. Integrate new information into existing pages whenever possible.
5. Create a new page only when the knowledge represents a genuinely new concept, topic, actor, or coherent body of knowledge.
6. Record provenance for non-trivial claims.
7. Add or update cross-links to related pages.
8. Update any relevant indexes or navigation pages.

Do not merely summarize each input into a new file. The goal is to evolve the knowledge base.

### QUERY

Use when answering a question from the accumulated wiki.

1. Start from `docs/wiki/`, not from the input corpus.
2. Traverse related pages when the answer requires multiple concepts.
3. Consult `docs/input/` when provenance, ambiguity, or missing detail requires verification.
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

- Never delete input evidence solely because its knowledge has been synthesized into the wiki.
- Never fabricate provenance.
- Never create a new page when an existing page can be coherently extended.
- Never force project knowledge into the default ontology when a project-local ontology exists.
- Keep ontology concepts few and composable; avoid premature specialization.
- Treat uncertainty as first-class information.
- Prefer links and explicit relations over repeated prose.
- Keep implementation concerns separate from the conceptual ontology: storage engines, vector databases, event buses, and UI frameworks are tooling choices, not ontology concepts.

---

## Specialization

This skill is domain-independent. Its core INGEST / QUERY / LINT behavior can operate against different project ontologies.

Examples of possible specializations include software delivery, scientific research, recipe discovery, investment research, or any other domain where the macro objective requires different entities, relations, or lifecycle semantics.
