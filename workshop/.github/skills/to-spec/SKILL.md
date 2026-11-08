---
name: to-spec
description: Read the accepted `intent.md` and produce requirements and design in ome pass and constrained by the organisation's standards.
---

## When to use
* Start process when `intent.md` is accepted only. other stop now.

## Workflow
1. Read `intent.md` and confirm it is explicitly accepted. Stop if acceptance is missing or unclear.
2. Read the organisation's applicable standards and `./templates/spec.md`. Stop and request any missing inputs.
3. Identify the accepted goals, scope, constraints, and success criteria. Ask the user about gaps or conflicts and stop until they are resolved; do not assume answers.
4. Derive requirements and testable acceptance criteria from the accepted intent, following the organisation's standards.
5. Describe the design needed to satisfy those requirements, including applicable components, interfaces, data flows, and error handling, without expanding the scope.
6. Populate the template with requirements and design in one pass. Check that each requirement traces to the intent and each design decision supports a requirement and complies with the standards.
7. Use the accepted intent's slug to save the completed specification to `./output/<slug>/spec.md`. If the slug is not defined, ask the user before saving.

## Rules
* Don't change the request or add your own assumptions.
* Don't answer the questions yourself.

## Output format
Use template from `./templates/spec.md` and create output file at `./outputs/<slug>/spec.md`