---
name: finalize-master-spec
description: Optionally create a sprint PR after `master-spec.md` is ready. Use when the workflow already generated the package artifacts and needs a final yes/no/custom-sprint gate before branching from `spec/<YY.WW>` and opening a PR from `feat/<JIRA_ID>`.
---

# Finalize Master Spec

Optionally create the sprint PR after the package is ready.

Keep this step focused on the user decision, git branching, and PR creation.

## Workflow

1. Require a Jira ID.
2. Require:
   - `temp/<JIRA_ID>/jira-issue.json`
   - `temp/<JIRA_ID>/prd-source.json`
   - `features/<JIRA_ID>/master-spec.md`
3. Compute the current sprint in ISO `year.week` format using the local date, for example `26.16`.
4. Ask the user one concise question:
   - `yes`: create a PR into `spec/<current sprint>`
   - `no`: skip finalize and return success
   - `custom sprint`: let the user provide `YY.WW`, for example `26.17`
5. If the user answers `no`, return a short skip report and stop this skill.
6. Resolve the target sprint:
   - Use the computed current sprint for `yes`
   - Use the user-provided `YY.WW` for `custom sprint`
7. Validate the sprint format as `YY.WW`.
8. Set the target branch to `spec/<sprint>`.
9. Check whether `spec/<sprint>` exists locally or on the remote.
10. If `spec/<sprint>` does not exist, warn the user with `Chưa tạo branch spec/<sprint>` and return `result: stop`.
11. Fetch the latest refs for the target branch.
12. Check out `spec/<sprint>`.
13. Pull the latest code for `spec/<sprint>`.
14. Create a new branch `feat/<JIRA_ID>` from `spec/<sprint>`.
15. Push `feat/<JIRA_ID>` to the remote before creating the PR.
16. Read:
   - Jira link from `temp/<JIRA_ID>/jira-issue.json`
   - PRD link from `temp/<JIRA_ID>/prd-source.json`
17. Create a PR with:
   - title: `feat/<JIRA_ID>`
   - base: `spec/<sprint>`
   - head: `feat/<JIRA_ID>`
   - body: Jira link, then PRD link
18. Return a short status report.
19. Return `result: stop` if the sprint format is invalid, the target branch is missing, the checkout or pull fails, the feature branch cannot be created, or the PR creation fails.

## Output

If the user skips PR creation:

```md
- finalize: skipped
- jira-id: PLMO-1328
- result: continue
```

If the PR is created:

```md
- finalize: ok
- jira-id: PLMO-1328
- target-branch: spec/26.16
- feature-branch: feat/PLMO-1328
- pr: created
- result: continue
```

If the sprint branch is missing:

```md
- finalize: blocked
- jira-id: PLMO-1328
- warning: Chưa tạo branch spec/26.16
- result: stop
```

## Guardrails

- Ask exactly one question for the `yes / no / custom sprint` decision.
- Use ISO `year.week` for the default sprint value.
- Reuse the saved Jira and PRD source links. Do not re-scrape external systems in this step.
- Always branch from `spec/<sprint>`, never from the current branch by accident.
- Pull the latest `spec/<sprint>` code before creating `feat/<JIRA_ID>`.
- Do not overwrite an existing `feat/<JIRA_ID>` branch or an existing PR with the same head branch. Warn and stop instead.
- Keep the PR body minimal: Jira link and PRD link only.
