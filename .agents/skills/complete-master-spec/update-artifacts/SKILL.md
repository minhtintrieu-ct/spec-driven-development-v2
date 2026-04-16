---
name: update-artifacts
description: Lazily apply the validated incoming answers into the specific artifacts like `api-contract.md` and `knowledge.md` only when they are directly impacted.
---

# Update Artifacts

Run targeted updates on specific contract files without loading the entire package. Token optimization is paramount here.

## Workflow

1. Require `JIRA_ID`, incoming answers, and the routing hints passed down from previous steps.
2. If `routing_hints.api == true`:
   - Read `features/<JIRA_ID>/api-contract.md`
   - Apply the approved contract updates logically.
   - Preserve formatting and endpoints.
3. If `routing_hints.knowledge == true`:
   - Read `features/<JIRA_ID>/knowledge.md` (if it exists).
   - Apply new facts affecting the product logic sequence.
4. Record whether or not these files were mutated, so the finalizing step knows if it must bump revisions.
5. Return `result: continue`.

## Output

```md
- update-artifacts: ok
- mutated:
  - api-contract: true|false
  - knowledge: true|false
- result: continue
```
