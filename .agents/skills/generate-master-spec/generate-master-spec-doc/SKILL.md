---
name: generate-master-spec-doc
description: Draft `features/<JIRA_ID>/master-spec.md` from Jira temp files and the generated package artifacts. Use when `knowledge.md`, `open-questions.md`, and `api-contract.md` already exist and the workflow needs a concise shared-baseline hub document.
---

# Generate Master Spec Doc

Generate `master-spec.md`.

Use the bundled template in this skill.

## Workflow

1. Require:
   - `temp/<JIRA_ID>/jira-issue.json`
   - `temp/<JIRA_ID>/prd.md`
   - `features/<JIRA_ID>/knowledge.md`
   - `features/<JIRA_ID>/open-questions.md`
   - `features/<JIRA_ID>/api-contract.md`
2. Read:
   - `references/master-spec-template.md`
3. Create `features/<JIRA_ID>/` if missing.
4. Draft `features/<JIRA_ID>/master-spec.md` from:
   - `temp/<JIRA_ID>/jira-issue.json`
   - `temp/<JIRA_ID>/prd.md`
   - `features/<JIRA_ID>/knowledge.md`
   - `features/<JIRA_ID>/open-questions.md`
   - `features/<JIRA_ID>/api-contract.md`
5. Return a short status report.
6. Return `result: stop` if any required input is missing.

## Output

Write:

- `features/<JIRA_ID>/master-spec.md`

Return:

```md
- master-spec: ok
- jira-id: PLMO-1328
- output: features/PLMO-1328/master-spec.md
- result: continue
```

## Draft Rules

- Keep the document concise, decision-first, and readable in under a few minutes.
- Use `knowledge.md` as supporting context, not as a second source of truth.
- Use `open-questions.md` to surface blockers and follow-up items.
- Use `api-contract.md` to summarize backend impact, API reuse, and new endpoint decisions.
- Keep detailed logs in the supporting artifacts instead of duplicating them here.
- Include a short one-minute readout near the top.
- Make current package status and main blockers obvious without reading every section.

## References

- `references/master-spec-template.md`

## Guardrails

- Use the bundled template in this skill, not the external repo paths.
- Do not query Knowledge Hub directly in this phase.
- Do not duplicate large tables or raw outputs from supporting artifacts.
- Keep this file as the shared baseline hub, not the detailed log.
