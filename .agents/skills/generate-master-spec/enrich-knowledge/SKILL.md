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
2. Act as the coordinator for this phase.
3. Summarize the PRD into a short context block.
   Include the PRD change summary, affected flow, expected backend surface, and known gaps.
4. Spawn four sub-agents in parallel.
   Give each sub-agent the same context block and exactly one tool to call.
5. Worker 1 owns `pm_get_product_logic`.
   Shape the query to look for:
   - current business rules
   - current user flow
   - constraints
   - edge cases
   - any current-state detail that may affect the PRD
6. Worker 2 owns `pm_analyze_feature_impact`.
   Shape the query to look for:
   - affected flows
   - dependent features
   - implementation risks
   - spec gaps
   - missing decisions to clarify
7. Worker 3 owns `engineer_search_api`.
   Shape the query to look for:
   - existing endpoints or APIs related to the change
   - reusable request or response shapes
   - auth or permission expectations
   - whether an existing API can be reused
   - whether a new endpoint is likely needed
8. Worker 4 owns `engineer_query_knowledge`.
   Shape the query to look for:
   - involved services
   - service ownership
   - data flow
   - backend dependencies
   - side effects or technical constraints that affect the contract
9. Wait for all four sub-agents to finish before writing any file.
10. Create `features/<JIRA_ID>/` if missing.
11. Write the full Knowledge Hub results to `features/<JIRA_ID>/knowledge.md`.
   Keep one section per tool so later steps can read or append findings easily.
12. Return a short status report.
13. Return `result: stop` if any required temp input is missing.
14. Return `result: stop` if any worker cannot produce useful output.

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
- Default to four worker calls total.
- Run the four default workers in parallel.
- Keep the full tool output in `knowledge.md`.
- Keep product and technical sections clearly separated.

## Guardrails

- Do not split the analysis into many small tool calls.
- Do not let one worker call more than one tool.
- Do not let the coordinator call the four default tools directly.
- Do not write any package artifact other than `knowledge.md` in this phase.
- Stop instead of guessing if a required tool call is missing or unusable.
