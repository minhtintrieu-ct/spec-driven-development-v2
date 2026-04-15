---
name: enrich-ticket
description: Enrich a Jira ticket with linked context and clearer working notes. Use when the workflow needs to turn a raw ticket into a more usable intake artifact by gathering linked PRD or reference material, extracting the key intent, and surfacing assumptions, risks, or open questions.
---

# Enrich Ticket

Turn a raw ticket into a cleaner working artifact.

Keep the output concise and directly useful for the next workflow step.

## Workflow

1. Read the ticket source.
2. Identify linked references that materially improve understanding.
3. Extract the business intent, scope, and constraints.
4. Summarize the ticket in a clearer working-note format.
5. List missing information as open questions instead of guessing silently.

## Output

Prefer this shape:

- `Intent`
- `Scope`
- `Constraints`
- `References`
- `Open questions`

## Guardrails

- Do not invent facts that are not present in the ticket or linked references.
- Prefer explicit gaps over false certainty.
- Keep enriched output short enough to scan quickly.
