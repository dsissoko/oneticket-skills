---
name: oneticket-epic-breakdown
description: "Break down epics into user stories using 9 Humanizing Work split patterns. Use when a backlog item is too large to estimate, sequence, or deliver safely."
version: "2.0.0"
---

# oneticket-epic-breakdown

Guide through breaking down epics into user stories using Richard Lawrence's complete Humanizing Work methodology — a systematic approach that applies 9 splitting patterns sequentially. Use this to identify which pattern applies, split while preserving user value, and evaluate splits based on what they reveal about low-value work to eliminate. This ensures vertical slicing (end-to-end value) rather than horizontal slicing (technical layers).

---

## Key Concepts

### Core Principles: Vertical Slices Preserve Value

A user story is "a description of a change in system behavior from the perspective of a user." Splitting must maintain **vertical slices** — work that touches multiple architectural layers and delivers observable user value — not horizontal slices addressing single components (e.g., "front-end story" + "back-end story").

### The Three-Step Process

1. **Pre-Split Validation** — Check if story satisfies INVEST criteria (except "Small")
2. **Apply Splitting Patterns** — Work through 9 patterns sequentially until one fits
3. **Evaluate Splits** — Choose the split that reveals low-value work or produces equal-sized stories

### The 9 Splitting Patterns (In Order)

1. **Workflow Steps** — Thin end-to-end slices, not step-by-step
2. **Operations (CRUD)** — Create, Read, Update, Delete as separate stories
3. **Business Rule Variations** — Different rules = different stories
4. **Data Variations** — Different data types/structures
5. **Data Entry Methods** — Simple UI first, fancy UI later
6. **Major Effort** — "Implement one + add remaining"
7. **Simple/Complex** — Core simplest version first, variations later
8. **Defer Performance** — "Make it work" before "make it fast"
9. **Break Out a Spike** — Time-box investigation when uncertainty blocks splitting

### Meta-Pattern (Applies Across All Patterns)

1. Identify the core complexity
2. List all variations
3. Reduce variations to **one complete slice**
4. Make other variations separate stories

### Why This Works

- **Prevents arbitrary splitting** — Methodical checklist prevents guessing
- **Preserves user value** — Every story delivers observable value
- **Reveals waste** — Good splits expose low-value work you can deprioritize
- **Repeatable** — Apply to any epic consistently

---

## Process

### Step 1 — Pre-Split Validation (INVEST Check)

Before splitting, verify the story satisfies INVEST criteria (except "Small"):

- **Independent?** Can it be prioritized without hard dependencies on other stories?
- **Negotiable?** Does it leave room for implementation discovery?
- **Valuable?** Does it deliver observable value to a user? If not — don't split, combine.
- **Estimable?** Can the team size this relatively?
- **Testable?** Does it have concrete acceptance criteria?

**⚠️ Critical:** If story fails "Valuable," STOP. Don't split. Instead, combine with other work to create a meaningful increment.

If any check fails → fix the issue before splitting.

---

### Step 2 — Apply Splitting Patterns Sequentially

Work through patterns in order. Stop when one applies.

---

**Pattern 1 — Workflow Steps**

Split into **thin end-to-end slices**. Each story covers the full workflow with increasing sophistication, not individual steps.

- **Original:** "Publish content (requires editorial review, legal approval, staging)"
- **❌ Wrong split (step-by-step):** Story 1 = Editorial review, Story 2 = Legal approval, Story 3 = Publish
- **✅ Right split (thin end-to-end):**
  - Story 1: Publish content (simple path — no reviews)
  - Story 2: Add editorial review step
  - Story 3: Add legal approval step

Each story delivers **full workflow**, just with increasing sophistication.

---

**Pattern 2 — Operations (CRUD)**

"Manage", "handle", "maintain" signal multiple operations. Split into Create, Read, Update, Delete.

- **Original:** "Manage user accounts"
- **Split:** Create / View / Edit / Delete user account (one story each)

---

**Pattern 3 — Business Rule Variations**

Different rules for different scenarios (user types, regions, tiers) → one story per rule.

- **Original:** "Flight search with flexible dates (date range, specific weekends, date offsets)"
- **Split:** One story per rule variation

---

**Pattern 4 — Data Variations**

Different data types or structures → deliver simplest first, add variations separately.

- **Original:** "Geographic search (counties, cities/towns, custom areas)"
- **Split:** One story per data type, simplest first

---

**Pattern 5 — Data Entry Methods**

Fancy UI (date pickers, autocomplete) is independent of core functionality → basic input first, UI enhancement after.

- **Original:** "Search with calendar date picker"
- **Split:** Story 1 = basic text input, Story 2 = visual calendar picker

---

**Pattern 6 — Major Effort**

When initial implementation carries most complexity and additions are trivial → "implement one + add remaining".

- **Original:** "Accept credit card payments (Visa, Mastercard, Amex, Discover)"
- **Split:** Story 1 = Accept Visa (build full payment infrastructure), Story 2 = Add Mastercard, Amex, Discover

---

**Pattern 7 — Simple/Complex**

Identify the simplest version that still delivers value. Extract variations into separate stories.

- **Original:** "Flight search (with max stops, nearby airports, flexible dates)"
- **Split:** Story 1 = Basic search, Story 2-4 = individual enhancements

---

**Pattern 8 — Defer Performance**

Split "make it work" from "make it fast". Non-functional requirements can follow functional delivery.

- **Original:** "Real-time search with <100ms response time"
- **Split:** Story 1 = Search works (functional), Story 2 = Optimize to <100ms

---

**Pattern 9 — Break Out a Spike**

Last resort when uncertainty prevents splitting. Time-box investigation (1-2 days) to answer specific questions, then restart at Pattern 1.

**⚠️ Spikes produce learning, not shippable code. After the spike, restart at Pattern 1.**

---

### Step 3 — Evaluate Split Quality

After splitting:

1. **Does this split reveal low-value work to deprioritize or eliminate?**
   - Good splits expose the 80/20 principle: most value concentrates in a small portion of functionality
2. **Does this split produce more equally-sized stories?**
   - Equal-sized stories give Product Owners greater prioritization flexibility

If split doesn't satisfy either criterion → try a different pattern.

---

### Cynefin Domain Considerations

Strategy shifts based on complexity domain:

- **Low uncertainty (Obvious/Complicated)** — "We know what to build; it's just engineering work"
  → Find all stories, prioritize by value/risk

- **High uncertainty (Complex domain)** — "We're not sure what customers want or what will work"
  → Identify 1-2 **learning stories**; avoid exhaustive enumeration (work itself teaches what matters)

- **Chaos** — "Everything is on fire; priorities shift daily"
  → **Defer splitting** until stability emerges; focus on stabilization first

---

## Output Format

```markdown
# Epic Breakdown Plan

**Epic:** [Original epic]
**Pre-Split Validation:** ✅ Passes INVEST (except Small)
**Splitting Pattern Applied:** [Pattern name]
**Rationale:** [Why this pattern fits]

---

## Story Breakdown

### Story 1: [Title] (Simplest Complete Slice)

**Summary:** [User-value-focused title]

**Use Case:**
- **As a** [persona]
- **I want to** [action]
- **so that** [outcome]

**Acceptance Criteria:**
- **Given:** [Preconditions]
- **When:** [Action]
- **Then:** [Outcome]

**Why This First:** [Delivers core value; simpler variations follow]
**Estimated Effort:** [Days/points]

---

### Story 2: [Title] (First Variation)

[Repeat...]

---

## Split Evaluation

✅ **Does this split reveal low-value work?**
- [Analysis: Which stories could be deprioritized/eliminated?]

✅ **Does this split produce equal-sized stories?**
- [Analysis: Are stories roughly equal in effort?]

---

## INVEST Validation (Each Story)

✅ Independent — Stories can be developed in any order
✅ Negotiable — Implementation details can be discovered collaboratively
✅ Valuable — Each story delivers observable user value
✅ Estimable — Team can size each story
✅ Small — Each story fits in 1-5 days
✅ Testable — Clear acceptance criteria for each

---

## Next Steps

1. **Review with team:** Do PM, design, engineering agree?
2. **Check for further splitting:** Are any stories still >5 days? If yes, restart at Pattern 1.
3. **Prioritize:** Which story delivers most value first?
4. **Consider eliminating:** Did split reveal low-value stories? Kill or defer them.
```

---

## Examples

### Example 1: Pattern 1 Applied (Workflow Steps — Thin End-to-End)

**Epic:** "Publish blog post (requires editorial review, legal approval, staging)"

**Pre-Split Validation:** ✅ Passes INVEST

**Pattern 1:** "Does this have workflow steps?" → YES ✅

**❌ Wrong Split (Step-by-Step):**
1. Editorial review story
2. Legal approval story
3. Publish story
→ Problem: Story 1 doesn't deliver value (users see nothing)

**✅ Right Split (Thin End-to-End):**
1. **Publish post (simple path)** — Author uploads, post goes live immediately (no reviews)
2. **Add editorial review** — Post now waits for editor approval before going live
3. **Add legal approval** — Post waits for legal + editorial before going live

**Why this works:** Each story delivers **full workflow**, just with increasing sophistication.

---

### Example 2: Pattern 2 Applied (CRUD Operations)

**Epic:** "Manage user profiles"

**Pattern 2:** "Does this say 'manage'?" → YES ✅ (signals CRUD)

**Split:**
1. Create user profile
2. View user profile details
3. Edit user profile info
4. Delete user profile

**Split Evaluation:**
- ✅ **Reveals low-value work:** "Delete profile" is rarely used → deprioritize
- ✅ **Equal-sized stories:** Each 1-2 days

---

### Example 3: Pattern 7 Applied (Simple/Complex)

**Epic:** "Flight search with max stops, nearby airports, flexible dates"

**Pattern 7:** "What's the simplest version?" → Basic search ✅

**Split:**
1. Basic flight search (origin, destination, date) — **Core value**
2. Add max stops filter — **Enhancement**
3. Add nearby airports option — **Enhancement**
4. Add flexible dates option — **Enhancement**

**Split Evaluation:**
- ✅ **Reveals low-value work:** "Flexible dates" rarely used → kill or defer
- ✅ **Equal-sized stories:** Story 1 = 3 days, others = 1 day each

---

### Example 4: Iterative Splitting (Multiple Patterns)

**Epic:** "Checkout flow with discounts (member, VIP, first-time) and payment (Visa, Mastercard, Amex)"

**First Pass — Pattern 1 (Workflow):** YES ✅
- Story 1: Add items to cart
- Story 2: Apply discount
- Story 3: Complete payment

**Check Story 2 ("Apply discount"):** Still 4 days → Too large, re-split

**Second Pass on Story 2 — Pattern 3 (Business Rules):** YES ✅
- Story 2a: Apply member discount (10%)
- Story 2b: Apply VIP discount (20%)
- Story 2c: Apply first-time discount (5%)

**Check Story 3 ("Complete payment"):** Still 5 days → Too large, re-split

**Third Pass on Story 3 — Pattern 6 (Major Effort):** YES ✅
- Story 3a: Accept Visa payments (build payment infrastructure)
- Story 3b: Add Mastercard, Amex support

**Final Breakdown:** 6 stories, all 1-2 days each

---

## Common Pitfalls

### Pitfall 1: Skipping Pre-Split Validation
**Symptom:** Jump straight to splitting without checking INVEST
**Consequence:** Split a story that shouldn't be split
**Fix:** Always run Step 1 (INVEST check) before Step 2

---

### Pitfall 2: Step-by-Step Workflow Splitting (Pattern 1 Done Wrong)
**Symptom:** Story 1 = "Editorial review," Story 2 = "Legal approval"
**Consequence:** Stories don't deliver end-to-end value
**Fix:** Each story should cover **full workflow** (thin end-to-end slice), just with increasing sophistication

---

### Pitfall 3: Horizontal Slicing (Technical Layers)
**Symptom:** "Story 1: Build API. Story 2: Build UI."
**Consequence:** Neither story delivers user value
**Fix:** Vertical slicing — each story includes front-end + back-end to deliver observable user behavior

---

### Pitfall 4: Forcing a Pattern That Doesn't Fit
**Symptom:** Applying a pattern that doesn't naturally fit the epic
**Consequence:** Arbitrary, meaningless split
**Fix:** If pattern doesn't apply, say NO and continue to next pattern

---

### Pitfall 5: Not Re-Splitting Large Stories
**Symptom:** Split epic into 3 stories, but each is still 5+ days
**Consequence:** Stories too large for sprint
**Fix:** **Restart at Pattern 1** for each large story until all are 1-5 days

---

### Pitfall 6: Ignoring Split Evaluation (Step 3)
**Symptom:** Split but don't evaluate if it reveals low-value work
**Consequence:** Miss opportunity to eliminate waste
**Fix:** After splitting, ask: "Does this reveal work we can kill or defer?"

---

## Rules

- Always run INVEST check before applying patterns
- Never horizontal-slice (front-end story + back-end story = no user value)
- Each story must deliver observable value to a user
- Stories are conversation starters, not locked-in specs
- Pure technical debt or refactoring = engineering tasks, not user stories
- If stories are still >5 days after splitting → restart at Pattern 1
- Spikes produce learning, not shippable code — after a spike, restart at Pattern 1

---

## References

- Richard Lawrence & Peter Green, *The Humanizing Work Guide to Splitting User Stories* — Complete methodology
- Bill Wake, *INVEST in Good Stories* (2003) — Quality criteria
- https://www.humanizingwork.com/the-humanizing-work-guide-to-splitting-user-stories/
- Related skills: `oneticket-user-story`, `oneticket-user-story-splitting`, `oneticket-epic-hypothesis`
