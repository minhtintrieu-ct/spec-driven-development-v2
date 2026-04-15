# /analyze

## Intent

Run the phase 1 flow through canonical skills.

## Required Input

- Jira URL (ex: https://701search.atlassian.net/browse/<TicketID>)

## Execution

1. Run `.agents/skills/check-environment-readiness/SKILL.md`.
2. If step 1 returns `result: continue`, run `.agents/skills/fetch-jira-issue/SKILL.md` with the same Jira URL.
3. If step 2 returns `result: continue`, run `.agents/skills/fetch-confluence/SKILL.md`.

If any step returns `result: stop`, stop the flow.
