---
name: finalize-master-doc
description: Normalize cross-file summaries, finalize the master-spec.md, transition status to Approve, and bump package revision if meaning changed.
---

# Finalize Master Doc

The culminating step in the `/complete-master-spec` workflow. Rebuild shared summaries and apply package-level status transitions.

## Workflow

1. Require `JIRA_ID`, incoming inputs, and the mutation flags from `update-artifacts`.
2. Read `features/<JIRA_ID>/master-spec.md`.
3. If `<JIRA_ID>/changelog.md` exists, read it.
4. Normalize cross-file metadata:
   - Synchronize summarized decision lists so they match the detailed contract items and resolved questions exactly.
5. **Draft to Approve Rule**:
   - Because `evaluate-blockers` already guaranteed no `blocking` items remain unanswered, you MUST change the Package Status metadata field in `master-spec.md` from `Draft` to `Approve` (or `Approved`).
6. **Revision Rule**:
   - If `update-artifacts` mutated `api-contract.md` or you had to mutate `master-spec.md` to reflect new rules, bump the revision marker (e.g., `r1` -> `r2`).
   - If only `open-questions.md` changed and the core shared baseline did not deviate, do NOT bump the revision.
7. Update `changelog.md` when bumping the revision by appending a new dated entry detailing what changed.
8. Before concluding, write an explicit completion summary for the user covering:
   - Package status (Approved)
   - Revision result
   - Non-blocking items marked `Skipped`
   - Any follow-up the PIC needs to take.
9. Return `result: finish`.

## Output

```md
- finalize: ok
- status: Approved
- new_revision: r2 (or untouched)
- result: finish
```
