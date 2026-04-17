---
name: enrich-knowledge
description: Draft `features/<JIRA_ID>/knowledge.md` from Jira temp files and PRD content. Use when the workflow already has Jira and Confluence inputs in `temp/<JIRA_ID>/` and needs one shared Knowledge Hub enrichment baseline before generating package artifacts with one coordinator and parallel tool calls.
---

# Enrich Knowledge

Generate `knowledge.md` first.

Use one coordinator for this phase. Prefer parallel tool use over spawned sub-agents.

## Workflow

1. Require:
   - `temp/<JIRA_ID>/jira-issue.json`
   - `temp/<JIRA_ID>/prd.md`
2. Act as the single coordinator for this phase.
3. Summarize the PRD into a short context block.
   Include the PRD change summary, affected flow, expected backend surface, and known gaps.
4. Prepare four tool queries from the same short context block:
   - `pm_get_product_logic`
   - `pm_analyze_feature_impact`
   - `engineer_search_api`
   - `engineer_query_knowledge`
5. Use parallel tool calls from the coordinator when available.
   Issue the four default tool calls in one batch instead of spawning worker sub-agents.
6. Query `pm_get_product_logic` for:
   - current business rules
   - current user flow
   - constraints
   - edge cases
   - any current-state detail that may affect the PRD
7. Query `pm_analyze_feature_impact` for:
   - affected flows
   - dependent features
   - implementation risks
   - spec gaps
   - missing decisions to clarify
8. Query `engineer_search_api` for:
   - existing endpoints or APIs related to the change
   - reusable request or response shapes
   - auth or permission expectations
   - whether an existing API can be reused
   - whether a new endpoint is likely needed
9. Query `engineer_query_knowledge` for:
   - involved services
   - service ownership
   - data flow
   - backend dependencies
   - side effects or technical constraints that affect the contract
10. Default to one successful tool call per tool. Allow a second successful call only when the first successful result is clearly too incomplete or unusable for the requested section.
11. Failed attempts such as timeout, transport error, or malformed response do not count as successful calls, but do count toward the total attempt budget for that tool.
12. Stop a tool path after `4` total attempts even if it has not yet reached `2` successful calls.
13. Wait for all four default tool results before writing any file.
14. Create `features/<JIRA_ID>/` if missing.
15. Overwrite `features/<JIRA_ID>/knowledge.md` deterministically.
   Keep one section per tool so later steps can read or append findings easily.
16. Return a short status report.
17. Return `result: stop` if any required temp input is missing.
18. Return `result: stop` if any required tool cannot produce useful output.

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
- Default to four successful tool calls total: one successful call per default tool.
- Prefer coordinator-driven parallel tool use over spawned sub-agents.
- Allow at most one extra successful call per tool, and only in the exceptional case where the first successful result is clearly too incomplete or unusable.
- Allow failed attempts without consuming the successful-call quota, but cap each tool path at `4` total attempts.
- Keep the full tool output in `knowledge.md`.
- Keep product and technical sections clearly separated.

## Guardrails

- Use one coordinator for this phase. Do not spawn sub-agents for the four default tool calls.
- Prefer parallel tool use from the coordinator when available.
- Keep the four default tool calls independent so they can run in one batch.
- Do not split the analysis into many small tool calls.
- Lock each query path to the exact owned tool and do not allow substitution with another tool.
- Do not let one tool path exceed two successful tool calls total.
- Do not let one tool path exceed four total attempts.
- Treat the second successful call as an exceptional retry only; the first successful call should normally be enough.
- Do not inspect git history or prior commits before writing `knowledge.md`.
- Do not write any package artifact other than `knowledge.md` in this phase.
- Stop instead of guessing if a required tool call is missing or unusable.
