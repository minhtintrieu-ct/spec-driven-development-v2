---
name: generate-master-spec
description: Run the master-spec generation flow from prepared inputs. Use when the workflow already has `temp/<JIRA_ID>/` inputs and needs to generate package artifacts under `features/<JIRA_ID>/`.
---

# Generate Master Spec

Run the package generation flow.

## Workflow

1. Require a Jira ID.
2. Run phases in order:
   - `enrich-knowledge`
   - `generate-open-questions`
   - `generate-api-contract`
   - `generate-master-spec-doc`
3. Stop at the first phase that returns `result: stop`.
4. Return a short status report.

## Output

Return:

```md
- master-spec: in-progress
- step: enrich-knowledge -> generate-open-questions -> generate-api-contract -> generate-master-spec-doc
- result: continue
```

## Guardrails

- Keep this skill thin.
- Delegate artifact generation to focused skills.
- Do not duplicate drafting rules from child skills here.
