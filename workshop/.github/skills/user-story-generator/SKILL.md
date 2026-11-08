---
name: user-story-generator
description: Generates INVEST-compliant user stories with acceptance criteria and concrete acceptance tests (input plus expected result) from a product or technical spec. Use when the user provides a spec, PRD, BRD, feature description, or requirements and asks for user stories, acceptance criteria, backlog items, or test cases. Not for writing code or full test automation.
---

# User Story Generator

Convert a spec into a backlog-ready set of user stories. Every story must satisfy INVEST, carry testable acceptance criteria, and include acceptance tests with explicit inputs and expected results.

## Inputs

- A spec (text, file, or link). Read it fully before writing anything.
- Optional: personas, glossary, existing backlog, definition of done, tech constraints, preferred AC style.

If the spec is missing, too vague to test, or contradictory, do not guess. Collect the issues into an **Open Questions** list (see step 7) and continue with what is clear, marking affected stories `Needs clarification`.

## Workflow

1. **Extract.** List actors/personas, goals, functional requirements, business rules, data fields, states, integrations, non-functional requirements (performance, security, accessibility, compliance), and explicit out-of-scope items. Give each requirement an ID (`R1`, `R2`...) or reuse the spec's section numbers.
2. **Group into epics.** Cluster requirements by user goal or capability, not by technical layer.
3. **Slice into stories.** One story = one small, valuable, user-visible outcome. Slice vertically (UI to data), never horizontally ("build the database"). Splitting patterns when a story is too big:
   - Workflow steps (create, then edit, then delete)
   - Happy path first, then variations and errors
   - Business rule variations
   - Data types or channels
   - CRUD operations
   - Simple to complex
   - Defer performance or polish to a later story
4. **Write each story** in the template below.
5. **Write acceptance criteria** (rules the story must satisfy).
6. **Write acceptance tests** (concrete scenarios with input and expected result).
7. **Self-check** against INVEST and the quality gates, fix failures, then output the result with a traceability matrix and open questions.

## INVEST checklist (apply to every story)

| Letter | Test | Fail signal |
|---|---|---|
| **I**ndependent | Can be built and released without another story being finished first? | "after story X", shared unfinished work. Reorder, merge, or re-slice. |
| **N**egotiable | States the need and outcome, not the implementation? | Names a table, endpoint, library, or UI widget. Move to Notes only if the spec demands it. |
| **V**aluable | A user or the business gets a clear benefit? | "So that" is circular or missing. Tie to a persona goal. |
| **E**stimable | A team could size it from what is written? | Unknown scope, vague terms ("fast", "easy"), missing data. Add detail or an open question. |
| **S**mall | Fits in one sprint, ideally 1-3 days of work, 3-7 acceptance criteria? | More than about 8 criteria or several actors. Split. |
| **T**estable | Every criterion can be passed or failed objectively? | Words like "user-friendly", "appropriate", "quickly". Replace with measurable values. |

Record the result per story as `INVEST: I✓ N✓ V✓ E✓ S✓ T✓`. A story with any ✗ must be fixed before output, or flagged `Needs clarification` with the reason.

## Story template

```
### US-<nn>: <short title>
**Epic:** <epic>  |  **Source:** <R-ids / spec section>  |  **Priority:** <Must/Should/Could>  |  **Status:** Ready | Needs clarification

**As a** <specific persona>
**I want** <capability>
**So that** <benefit>

**Acceptance criteria**
- AC1: <single, objective, testable statement>
- AC2: ...

**Acceptance tests**
| ID | Scenario | Given (preconditions) | Input | Expected result | Type |
|----|----------|-----------------------|-------|-----------------|------|
| AT-<nn>.1 | <name> | <state> | <exact values> | <exact outcome> | Happy |

**Notes / out of scope:** <only if needed>
**INVEST:** I✓ N✓ V✓ E✓ S✓ T✓
```

Rules for the story statement:
- Persona is specific ("registered shopper", not "user"), taken from the spec.
- One capability per story. If you wrote "and", consider splitting.
- No implementation detail.

## Writing good acceptance criteria

- One verifiable condition per line; start from observable behavior ("The system rejects...", "The user sees...").
- Use measurable values from the spec: limits, formats, timings, counts, messages. If the spec has none, write `[TBC]` and raise an open question rather than inventing a number.
- Cover business rules, validation, permissions, error handling, and relevant non-functional needs for that story only.
- Avoid vague terms: fast, simple, appropriate, user-friendly, etc.
- Avoid duplicating the same rule across stories; reference the owning story.
- Optional Given/When/Then phrasing is fine for behavior-heavy criteria; keep the same rigor.

## Writing better acceptance tests (input plus expected results)

Every acceptance criterion must be covered by at least one test, and every test must name the criterion it verifies (add an `AC` column if helpful). A test is acceptable only if someone who has never seen the spec could run it and know pass or fail.

Each test needs:
- **Preconditions (Given):** exact system and data state, including role and permissions.
- **Input:** literal values, not descriptions. Write `email = "a@b"` rather than "an invalid email".
- **Expected result:** exact, observable outcome: message text, status, field values, record created or not, state change, timing threshold. Include side effects and what must *not* happen when relevant.
- **Type:** Happy, Alternate, Negative, Boundary, Permission, Error/Failure, or Non-functional.

Coverage rules per story, as applicable:
- One happy path.
- Boundary values: just below, at, and just above each limit (min-1, min, max, max+1); empty, null, and maximum-length input.
- Invalid and missing input, each with the exact error expected.
- Equivalence classes: one representative per class rather than many near-duplicates.
- Roles and permissions: allowed, denied, unauthenticated.
- Failure and recovery: dependency down, timeout, duplicate submission, concurrent update.
- State transitions: valid and invalid.
- Non-functional checks only when the spec states a target (for example "responds in under 2 s at 100 concurrent users").

Prefer a table when there are many input/output combinations (decision-table style). Keep each test independent and repeatable. Use realistic but obviously fake test data. Do not use "etc.", "and so on", or "valid data" in an input or expected-result cell.

Example (password rule: 8-64 characters, at least one digit):

| ID | Scenario | Given | Input | Expected result | Type |
|----|----------|-------|-------|-----------------|------|
| AT-03.1 | Accepts valid password | Registration form open | password = "Passw0rdOK" | Account created; user redirected to dashboard | Happy |
| AT-03.2 | Rejects too short | Registration form open | password = "Pass1" (5 chars) | Not created; message: "Password must be 8-64 characters" | Boundary |
| AT-03.3 | Accepts minimum length | Registration form open | password = "Passw0rd" (8 chars) | Account created | Boundary |
| AT-03.4 | Rejects no digit | Registration form open | password = "PasswordOnly" | Not created; message: "Password must include a number" | Negative |
| AT-03.5 | Rejects over maximum | Registration form open | password = 65 characters incl. a digit | Not created; message: "Password must be 8-64 characters" | Boundary |

## Quality gates (verify before output)

- [ ] Every spec requirement maps to at least one story or is listed as out of scope.
- [ ] Every story passes INVEST or is flagged with the reason.
- [ ] Every acceptance criterion is objective and has at least one acceptance test.
- [ ] Every test has literal input and exact expected result.
- [ ] No invented requirements, numbers, or messages. Anything assumed is labeled `Assumption:`.
- [ ] No duplicate or overlapping stories.
- [ ] Story IDs, AC IDs, and test IDs are unique and consistent.

## Output structure

1. **Summary:** spec name, personas found, number of epics/stories/tests, count of open questions.
2. **Stories by epic**, each in the template above.
3. **Traceability matrix:** table of `Requirement | Story | ACs | Tests`, including requirements deliberately excluded.
4. **Assumptions** (labeled, each with its impact).
5. **Open questions:** numbered, each linked to the affected story or requirement, with the options you see where known.
6. **Suggested release order** (optional): dependency-aware, highlighting the thinnest valuable slice first.

## Delivery

- Short result: reply in chat.
- Larger backlog, or when the user will keep, edit, or share it: create a document. If they want it for tools, offer CSV or spreadsheet columns (ID, Epic, Title, Persona, Want, So that, AC, Priority, Status, Source) or Jira-ready text.
- Match the language of the spec (for example Thai or English); keep test input values and system messages exactly as the spec states them.

## Things to avoid

- Technical or horizontal tasks written as user stories.
- Epics presented as stories (too large to estimate).
- Acceptance criteria that restate the story or describe the UI layout.
- Expected results like "works correctly", "success", or "error shown".
- Inventing business rules to fill gaps. Ask instead.
