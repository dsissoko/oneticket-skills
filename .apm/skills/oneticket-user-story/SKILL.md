---
name: oneticket-user-story
description: Create user stories with Mike Cohn format and Gherkin acceptance criteria. Use when turning user needs into development-ready work with clear outcomes and testable conditions.
version: 1.0.0
---

# oneticket-user-story

## Purpose

Create clear, concise user stories that combine Mike Cohn's user story format with Gherkin-style acceptance criteria.

Use this to translate user needs into actionable development work that focuses on outcomes, ensures shared understanding between product and engineering, and provides testable success criteria.

---

## Format

### Use Case (Mike Cohn format)

```markdown
- **As a** [user persona/role]
- **I want to** [action to achieve outcome]
- **so that** [desired outcome]
```

### Acceptance Criteria (Gherkin format)

```markdown
- **Scenario:** [Brief description]
- **Given:** [Initial context or preconditions]
- **When:** [Event that triggers the action]
- **Then:** [Expected outcome]
```

---

## Quality Checks

**Use Case:**
- "As a" — specific persona, not generic "user"
- "I want to" — an action the user takes, not a feature being built
- "So that" — the user's motivation, not a restatement of the action

**Acceptance Criteria:**
- Multiple Givens are fine — preconditions stack
- Only one When — multiple Whens = multiple stories
- Only one Then — multiple Thens = multiple stories
- "Then" must be measurable — not "users have a better experience"

---

## Complete Example

```markdown
### User Story:

- **Summary:** Enable Google login for trial users to reduce signup friction

**Use Case:**
- **As a** trial user visiting the app for the first time
- **I want to** log in using my Google account
- **so that** I can access the app without creating and remembering a new password

**Acceptance Criteria:**
- **Scenario:** First-time trial user logs in via Google OAuth
- **Given:** I am on the login page
- **When:** I click "Sign in with Google" and authorize the app
- **Then:** I am logged in and redirected to the onboarding flow
```

---

## Anti-Patterns

- **Technical task disguised as story** — "As a developer, I want to refactor the API" → not a user story, no user value
- **"As a user"** — too generic, use specific personas
- **"So that" restates "I want to"** — dig into the real motivation
- **Multiple When/Then** — sign of scope creep, split the story
- **Untestable "Then"** — "Then the user has a better experience" → make it measurable

---

## Rules

- If the story is too large → use `oneticket-user-story-splitting`
- Every story must deliver observable value to a user
- Stories are conversation starters, not locked-in specs
- Pure technical debt or refactoring = engineering tasks, not user stories
- File placement: `<docs_path>/what/epics/epic-N-<name>/user-stories/us-NNN-<name>.md`
- Use template at `.oneticket/templates/us.md`
