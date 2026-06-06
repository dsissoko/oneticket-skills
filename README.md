# oneticket-skills

Reusable agent skills for the [OneTicket](https://github.com/dsissoko/oneticket-core) multi-agent framework.

Skills are instruction sets loaded by agents at runtime via [APM](https://github.com/microsoft/apm). Each skill encodes domain knowledge that guides agent behavior — what to produce, how to structure it, what rules to follow.

---

## Installation

```bash
apm install dsissoko/oneticket-skills
```

Or reference in your `apm.yml`:

```yaml
dependencies:
  apm:
    - dsissoko/oneticket-skills#main
```

---

## Skill catalog

### Documentation

Structured documentation covering the full product lifecycle — from product vision to implementation slices.

- `oneticket-doc-structure` — documentation structure, templates, placement rules and frontmatter conventions
- `oneticket-reverse-doc` — reverse-document an existing app by inferring missing documentation from the source code

### Product & Analysis

Tools for framing product problems, decomposing epics, and understanding user needs.

- `oneticket-epic-breakdown` — break down epics into user stories using 9 Humanizing Work split patterns
- `oneticket-jobs-to-be-done` — uncover customer jobs, pains, and gains in a structured JTBD format
- `oneticket-domain-analysis` — analyze a business domain to identify entities, relationships, and process flows
- `oneticket-opportunity-solution-tree` — build an Opportunity Solution Tree from outcomes to solutions

### Architecture

Technical architecture, diagrams and implementation slicing.

- `oneticket-c4` — generate C4 architecture documentation using Mermaid diagrams
- `oneticket-vertical-slice` — derive and document vertical implementation slices from epics and architecture

### Frontend — React + Vite

Skills for building production-grade React + Vite applications with the AppShell template.

- `oneticket-scaffold-appshell` — scaffold a new React+Vite app from the AppShell template with parallel-safe task decomposition
- `oneticket-stack-vite-react-primer` — React + Vite + TypeScript + Primer stack conventions and patterns
- `oneticket-design-for-vite-react-primer` — design and implement frontend interfaces using GitHub Primer
- `oneticket-ui-for-vite-react-primer` — align a React + Primer SPA with GitHub visual conventions

### Testing

Testing strategies, E2E smoke tests and runtime sanity checks.

- `oneticket-testing-for-js-ts` — comprehensive testing strategies using Jest, Vitest and Testing Library
- `oneticket-e2e-for-playwright` — minimal browser-based E2E smoke test for any frontend
- `oneticket-frontend-runtime-sanity` — minimal runtime smoke-test setup for frontend stacks

### Code Quality

Code review, error handling, commit conventions and CI workflows.

- `oneticket-code-review` — code reviews following Sentry engineering practices
- `oneticket-error-handling-patterns` — error handling patterns across languages for resilient applications
- `oneticket-commit-conventions` — commit message conventions with release-please effects

### Game Development

Skills for building 2D browser games with HTML5 Canvas and React.

- `oneticket-2d-arcade` — patterns for 2D arcade games (Space Invaders, Breakout, Pac-Man style)
- `oneticket-game-engine` — build 2D browser games using HTML5 Canvas API and React

### Product Strategy

Business analysis, customer understanding and strategic frameworks.

- `oneticket-customer-journey-map` — create a customer journey map across stages, touchpoints and emotions
- `oneticket-business-health-diagnostic` — diagnose SaaS business health across growth, retention and efficiency

---

## Agent profiles

This repository also includes agent profiles for the OneTicket team:

| Agent | Role |
|---|---|
| `@po` | Product Owner — product knowledge, epics, user stories, documentation |
| `@architect` | Software Architect — architecture, C4 diagrams, implementation slices |
| `@leaddev` | Lead Developer — task decomposition, delegation to @dev |
| `@dev` | Developer — code implementation |
| `@qa` | QA Engineer — testing, code review, quality |
| `@analyst` | Business Analyst — domain modeling, functional documentation |

---

## License

MIT — see [LICENSE](LICENSE)
