---
name: enrich-knowledge
description: Draft `features/<JIRA_ID>/knowledge.md` from Jira temp files and PRD content. Use when the workflow already has Jira and Confluence inputs in `temp/<JIRA_ID>/` and needs one shared Knowledge Hub enrichment baseline before generating package artifacts.
---

# Enrich Knowledge

Generate `knowledge.md` first.

## Workflow

1. Require:
   - `temp/<JIRA_ID>/jira-issue.json`
   - `temp/<JIRA_ID>/prd.md`
2. Summarize the PRD into a short context block.
   Include the PRD change summary, affected flow, expected backend surface, and known gaps.
3. In parallel, use that context block to call `pm_get_product_logic` once.
   Shape the query to look for:
   - current business rules
   - current user flow
   - constraints
   - edge cases
   - any current-state detail that may affect the PRD
4. In parallel, use the same context block to call `pm_analyze_feature_impact` once.
   Shape the query to look for:
   - affected flows
   - dependent features
   - implementation risks
   - spec gaps
   - missing decisions to clarify
5. In parallel, use the same context block to call `engineer_search_api` once.
   Shape the query to look for:
   - existing endpoints or APIs related to the change
   - reusable request or response shapes
   - auth or permission expectations
   - whether an existing API can be reused
   - whether a new endpoint is likely needed
6. In parallel, use the same context block to call `engineer_query_knowledge` once.
   Shape the query to look for:
   - involved services
   - service ownership
   - data flow
   - backend dependencies
   - side effects or technical constraints that affect the contract
7. Wait for all four tool calls to finish before writing any file.
8. Create `features/<JIRA_ID>/` if missing.
9. Write the full Knowledge Hub results to `features/<JIRA_ID>/knowledge.md`.
   Keep one section per tool so later steps can read or append findings easily.
10. Return a short status report.
11. Return `result: stop` if any required temp input is missing.
12. Return `result: stop` if any required tool call cannot produce useful output.

## Output

Write:

- `features/<JIRA_ID>/knowledge.md`

Return:

```md
- knowledge: ok
- jira-id: PLMO-1328
- output: features/PLMO-1328/knowledge.md
- result: continue
```

## Draft Rules

- Keep `knowledge.md` easy to append later.
- Default to four tool calls total.
- Run the four default tool calls in parallel.
- Keep the full tool output in `knowledge.md`.
- Keep product and technical sections clearly separated.

## Guardrails

- Do not split the analysis into many small tool calls.
- Do not write any package artifact other than `knowledge.md` in this phase.
- Stop instead of guessing if a required tool call is missing or unusable.
