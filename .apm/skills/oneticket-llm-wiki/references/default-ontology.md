# Default LLM Wiki Ontology

## Purpose

This is the minimal reference ontology for a general-purpose LLM-maintained knowledge wiki.

It intentionally models knowledge, not workflow. Projects may specialize it when their macro objective requires richer concepts or lifecycle semantics.

## Core Entities

### Source

An external piece of evidence ingested into the knowledge base.

Examples: paper, article, book, transcript, repository, dataset, note, conversation export.

A source should retain enough provenance to identify its origin.

### Topic

A broad area used to organize related knowledge.

Topics are navigational and may contain many concepts and claims.

### Concept

A distinct idea, mechanism, method, object, or abstraction worth representing independently.

Concepts should be reusable across topics.

### Claim

A meaningful proposition supported, contradicted, qualified, or discussed by sources.

Claims make provenance explicit and allow conflicting evidence to coexist.

### Actor

A person, organization, team, project, or other identifiable participant relevant to the knowledge base.

Use a more specialized actor type only when the project ontology needs it.

## Core Relations

```text
Source  --supports------> Claim
Source  --contradicts---> Claim
Source  --discusses-----> Concept
Claim   --about----------> Concept
Concept --belongs_to-----> Topic
Concept --related_to-----> Concept
Actor   --associated_with> Source | Claim | Concept | Topic
```

Relations may carry qualifiers such as confidence, date, scope, or context when useful.

## Provenance

For important synthesized knowledge, preserve at minimum:

- source identity
- relationship to the knowledge (`supports`, `contradicts`, `discusses`, etc.)
- relevant date when temporal context matters

Do not treat a generated wiki page as its own evidence source unless it contains genuinely new human-provided information.

## Uncertainty and Conflict

Conflicting sources are valid knowledge. Do not silently collapse disagreement into a single claim.

Prefer:

```text
Source A --supports----> Claim X
Source B --contradicts-> Claim X
```

over an unsupported synthesized certainty.

## Specialization

This ontology is deliberately small. Extend it when the objective of the knowledge base requires concepts with distinct semantics.

For example, an Agile Dev Wiki may introduce:

```text
Project
Requirement
Epic
UserStory
Sprint
Decision
ArchitectureElement
QualityGate
Release
Environment
Deployment
```

and lifecycle concepts such as states, transitions, guards, and actions.

The specialization should preserve the generic knowledge concepts where they remain useful rather than replacing them unnecessarily.
