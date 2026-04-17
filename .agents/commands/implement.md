# /implement

## Intent

Run the implementation phase from an existing `.planning/<feature>/` bundle in the current working repo.

This command is intentionally thin. It only resolves the minimum required context, checks that the expected planning bundle exists, and routes to the selected implementation mode.

## Required Input

- `mode = full | multi`

If `mode` is missing, stop and explain it briefly so the user can choose:

- `full`: implement directly from the delta in one pass
- `multi`: create a plan, implement by phases, and stop after each phase for user verification

Only require these when missing from current context:

- `JIRA_ID`
- `platform`

## Execution

1. Resolve the target feature from `.planning/<feature>/` in the current working repo.
2. If `JIRA_ID` is still missing, stop and require it explicitly.
3. Resolve the target platform from current context.
4. If `platform` is still missing, stop and require it explicitly.
5. If `mode` is still missing, stop and show this short explanation:
   - `full`: implement directly from the delta in one pass
   - `multi`: create a plan, implement by phases, and stop after each phase for user verification
6. Check that `.planning/<feature>/` exists in the current working repo.
7. Check that `.planning/<feature>/kh-search-brief.md` exists.
8. Check that `.planning/<feature>/repo-exploration.md` exists.
9. Check that the platform delta exists for the same feature.
10. If `mode = full`, run `.agents/skills/implement/full/SKILL.md`.
11. If `mode = multi`, run `.agents/skills/implement/multi/SKILL.md`.
12. If the selected skill returns `result: stop`, stop the flow.

## Guardrails

- Do not over-validate the artifacts in the command layer.
- Only check artifact existence here.
- Let the selected implementation mode decide whether the delta is strong enough to continue.
