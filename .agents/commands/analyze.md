# /analyze

## Intent

Run the phase 1 flow through canonical skills.

## Required Input

- Jira URL (ex: https://701search.atlassian.net/browse/<TicketID>)

## Execution

1. Run `.agents/skills/analyze/check-environment-readiness/SKILL.md`.
2. If step 1 returns `result: continue`, run `.agents/skills/analyze/fetch-jira-issue/SKILL.md` with the same Jira URL.
3. If step 2 returns `result: continue`, run `.agents/skills/analyze/fetch-confluence/SKILL.md`.
4. If step 3 returns `result: continue`, run `.agents/skills/generate-master-spec/SKILL.md` with the same Jira ID.
5. If step 4 returns `result: continue`, run `.agents/skills/finalize-master-spec/SKILL.md` with the same Jira ID.

If any step returns `result: stop`, stop the flow.
