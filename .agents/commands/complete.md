# /complete

## Intent

Complete the Master Spec package in one step after the initial draft exists. Use this command when new information, PRD updates, or answered Q&As need to be folded into the current package baseline.

To minimize token usage, the command delegates work explicitly through multiple specific sub-skills in sequence.

## Required Input

- `JIRA_ID` (the story ID, e.g. `PLMO-1328`)
- The new information or answers to be folded in (provided as text, files, or direct context).

## Execution

1. Run `.agents/skills/complete-master-spec/evaluate-blockers/SKILL.md` to classify inputs and check for blocking items.
2. Run `.agents/skills/complete-master-spec/update-open-questions/SKILL.md` to resolve Q&A and mark unhandled non-blocking items as `Skip`.
3. Run `.agents/skills/complete-master-spec/update-artifacts/SKILL.md` if the evaluator step determined that API or Figma contracts need updates.
4. Run `.agents/skills/complete-master-spec/finalize-master-doc/SKILL.md` to normalize the master package, bump revisions, and transition statuses from Draft to Approve.

If any skill returns `result: stop`, stop the flow immediately.
