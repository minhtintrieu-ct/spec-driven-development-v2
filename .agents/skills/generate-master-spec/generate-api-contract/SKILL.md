---
name: generate-api-contract
description: Draft `features/<JIRA_ID>/api-contract.md` from Jira temp files, PRD content, and shared knowledge. Use when the workflow already has Jira and Confluence inputs in `temp/<JIRA_ID>/` plus `features/<JIRA_ID>/knowledge.md` and needs a technical discovery-backed API contract baseline.
---

# Generate Api Contract

Generate `api-contract.md`.

Use the bundled template in this skill.

## Workflow

1. Require:
   - `temp/<JIRA_ID>/jira-issue.json`
   - `temp/<JIRA_ID>/prd.md`
   - `temp/<JIRA_ID>/prd-source.json`
   - `features/<JIRA_ID>/knowledge.md`
2. Read:
   - `references/api-contract-template.md`
3. Create `features/<JIRA_ID>/` if missing.
4. Draft `features/<JIRA_ID>/api-contract.md` from:
   - `temp/<JIRA_ID>/jira-issue.json`
   - `temp/<JIRA_ID>/prd.md`
   - `temp/<JIRA_ID>/prd-source.json`
   - `features/<JIRA_ID>/open-questions.md` if present
   - `features/<JIRA_ID>/knowledge.md`
5. Return a short status report.
6. Return `result: stop` if any required input is missing.

## Output

Write:

- `features/<JIRA_ID>/api-contract.md`

Return:

```md
- api-contract: ok
- jira-id: PLMO-1328
- output: features/PLMO-1328/api-contract.md
- result: continue
```

## Draft Rules

- Keep the template structure.
- Fill metadata from Jira, PRD, and shared knowledge results.
- Keep only contract-relevant findings in `api-contract.md`.
- End the artifact with a Mermaid `flowchart TD` service-flow diagram.
- Use one node per client surface or backend service.
- Inside each node, list existing APIs, new APIs, and follow-up APIs when relevant.
- If backend support is clearly needed but not confirmed, default to `New endpoint required`.
- If the need itself is unclear, use `Pending decision`.
- Use `Confirmed reuse` only when a concrete existing binding is found.
- Do not invent detailed schemas unless the PRD is specific enough.

## References

- `references/api-contract-template.md`

## Guardrails

- Use the bundled references in this skill, not the external repo paths.
- Do not query Knowledge Hub directly in this phase.
- Stop instead of guessing if `knowledge.md` is missing or unusable.
- Keep later package files for later steps.
