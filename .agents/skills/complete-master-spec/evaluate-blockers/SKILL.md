---
name: evaluate-blockers
description: Scan the incoming new information against the existing open questions. Stop the completion flow immediately if any blocking issue remains unresolved. Classify what kind of artifacts will need updates to save tokens down the line.
---

# Evaluate Blockers

Act as an orchestrator to evaluate whether the `complete-master-spec` workflow can proceed.

## Workflow

1. Require `JIRA_ID` and the incoming user input (answers, PRD updates).
2. Resolve the target directory: `features/<JIRA_ID>/`.
3. If no directory exists, return `result: stop`.
4. Read `features/<JIRA_ID>/open-questions.md`.
5. Read `features/<JIRA_ID>/figma.md` (if it exists).
6. Match the incoming answers against the active `blocking` open questions and `Blocking` baseline rows in `figma.md`.
7. **Stop Condition**: For any `blocking` question or `Blocking` Figma row that is STILL unanswered:
   - Output the exact blocking items.
   - Return `result: stop` immediately. Require the user to explicitly answer them before continuing.
8. If all `blocking` questions are answered, analyze the nature of the incoming answers and updates to output a routing hint:
   - `requires_api_update: true|false`
   - `requires_figma_update: true|false`
   - `requires_knowledge_update: true|false`
9. Pass these routing hints down as context for the next step.
10. Return `result: continue`.

## Output

```md
- evaluate: ok
- blocking_unanswered: 0
- routing_hints:
  - api: true
  - figma: false
  - knowledge: false
- result: continue
```

If it failed because of blockers:
```md
- evaluate: failed
- blocking_unanswered: <list of unresolved OQ-#>
- result: stop
```
