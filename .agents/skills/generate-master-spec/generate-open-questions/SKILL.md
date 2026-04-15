---
name: generate-open-questions
description: Draft `features/<JIRA_ID>/open-questions.md` from Jira temp files, PRD content, and shared knowledge. Use when the workflow already has Jira and Confluence inputs in `temp/<JIRA_ID>/` plus `features/<JIRA_ID>/knowledge.md` and needs to surface unresolved ambiguity before generating `api-contract.md`.
---

# Generate Open Questions

Generate `open-questions.md`.

Use the bundled template and PRD validation guide in this skill.

## Workflow

1. Require:
   - `temp/<JIRA_ID>/jira-issue.json`
   - `temp/<JIRA_ID>/prd.md`
   - `features/<JIRA_ID>/knowledge.md`
2. Read:
   - `references/open-questions-template.md`
   - `references/validate-prd.md`
3. Create `features/<JIRA_ID>/` if missing.
4. Draft `features/<JIRA_ID>/open-questions.md` from:
   - `temp/<JIRA_ID>/jira-issue.json`
   - `temp/<JIRA_ID>/prd.md`
   - `features/<JIRA_ID>/knowledge.md`
5. Return a short status report.
6. Return `result: stop` if any required input is missing.

## Output

Write:

- `features/<JIRA_ID>/open-questions.md`

Return:

```md
- open-questions: ok
- jira-id: PLMO-1328
- output: features/PLMO-1328/open-questions.md
- result: continue
```

## Draft Rules

- Keep the template structure.
- Focus on unresolved ambiguity, missing decisions, and unclear scope.
- Use `knowledge.md` as supporting context for open questions.
- Prefer explicit open questions over guessing.
- Keep questions concrete and reviewable.

## References

- `references/open-questions-template.md`
- `references/validate-prd.md`

## Guardrails

- Use the bundled references in this skill, not the external repo paths.
- Do not query Knowledge Hub directly in this phase.
- Stop instead of guessing if `knowledge.md` is missing or unusable.
- Write `open-questions.md` before `api-contract.md`.
- Keep later package files for later steps.
