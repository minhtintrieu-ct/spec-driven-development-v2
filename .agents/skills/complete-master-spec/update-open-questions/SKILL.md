---
name: update-open-questions
description: Process answers and ambiguity resolution for the package. Fold answers into the exact target files and explicitly mark unresolved non-blocking issues as `Skip`. 
---

# Update Open Questions

Resolve questions directly.

## Workflow

1. Require `JIRA_ID`, the incoming answers, and the routing hints passed from `evaluate-blockers`.
2. Read `features/<JIRA_ID>/open-questions.md`.
3. If new, unhandled topics are introduced, append them as new `OQ-*` items.
4. For existing `OQ-*` items with explicit answers provided:
   - Update their status to `Resolved`.
   - Fill the `Resolution` block concisely.
   - Record the `Source`.
5. For any `OQ-*` item that is `non-blocking` and NO explicit answer was provided:
   - Keep status as `Open`.
   - Set resolution handling as `Skipped`. Do not invent an answer.
   - Example Note: `Completion Pass: Skipped pending answer.`
6. Repeat the exact same resolution rules for `figma.md`'s baseline rows (if the evaluator reported Figma needs update or if Figma gaps exist). Unanswered `Non-blocking` baseline rows become `Skipped` pending design answer.
7. Write the files back.
8. Return `result: continue`.

## Output

```md
- update-questions: ok
- skipped_non_blocking: <count>
- result: continue
```
