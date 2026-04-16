---
name: finalize-master-spec
description: Optionally create a sprint PR after `master-spec.md` is ready. Use when the workflow already generated the package artifacts and needs a final yes/no/custom-sprint gate before branching from `spec/<YY.WW>`, opening a PR from `feat/<JIRA_ID>`, and commenting the Jira story with the PR handoff.
---

# Finalize Master Spec

Optionally create the sprint PR after the package is ready.

Keep this step focused on the user decision, git branching, PR creation, GitHub reviewer requests, and Jira handoff comment.

## Execution Contract

This skill is not free-style.

Use this fixed toolchain in the happy path:

- `git` CLI for branch discovery, checkout, pull, branch creation, add, commit, and push
- `gh` CLI for PR discovery, PR creation, and reviewer requests
- Atlassian MCP only for posting the final Jira comment

Do not use a GitHub plugin in the happy path.
Do not substitute browser actions or manual UI steps for these commands.

## Workflow

1. Require a Jira ID.
2. Require:
   - `temp/<JIRA_ID>/jira-issue.json`
   - `temp/<JIRA_ID>/prd-source.json`
   - `features/<JIRA_ID>/master-spec.md`
   - `team.yml`
   - `config.yml`
3. Compute the current sprint in ISO `year.week` format using the local date with this exact command:
   - `date +"%g.%V"`
   - example expected shape: `26.16`
   - do not use `date +"%y.%W"` because `%W` is not ISO week numbering
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
9. Run `git fetch origin spec/<sprint>`.
10. Verify that `refs/remotes/origin/spec/<sprint>` now exists.
11. If `spec/<sprint>` does not exist, warn the user with `Chưa tạo branch spec/<sprint>` and return `result: stop`.
12. Run `git checkout spec/<sprint>`.
13. Run `git pull --ff-only origin spec/<sprint>`.
14. Check whether `feat/<JIRA_ID>` already exists locally or on the remote with human-readable probes:
   - local check: `git show-ref --verify --quiet refs/heads/feat/<JIRA_ID> && echo "local exists" || echo "local missing"`
   - remote check: `git ls-remote --exit-code --heads origin feat/<JIRA_ID> >/dev/null 2>&1 && echo "remote exists" || echo "remote missing"`
15. If either probe reports `exists`, warn and return `result: stop`.
16. Run `git checkout -b feat/<JIRA_ID>`.
17. Stage only the generated package files for this ticket with `git add features/<JIRA_ID>`.
18. Verify that `features/<JIRA_ID>` actually has staged changes:
   - use `git diff --cached --quiet -- features/<JIRA_ID> && echo "no staged diff" || echo "has staged diff"`
   - if there is no staged diff, warn and return `result: stop`
19. Create one commit for the package update, for example `git commit -m "feat(<JIRA_ID>): update package"`.
20. Push `feat/<JIRA_ID>` with upstream using `git push -u origin feat/<JIRA_ID>`.
21. Read:
   - Jira link from `temp/<JIRA_ID>/jira-issue.json`
   - PRD link from `temp/<JIRA_ID>/prd-source.json`
22. Check whether a PR for `feat/<JIRA_ID>` already exists using `gh pr view feat/<JIRA_ID>`.
23. Interpret the PR state:
   - if the PR state is `OPEN`, warn and return `result: stop`
   - if the PR state is `CLOSED`, continue this flow and treat it as non-blocking for the pre-check only
   - if no PR is found, continue this flow
24. Create a PR with `gh pr create` only when no existing PR is found or the existing PR is `CLOSED`:
   - title: `feat/<JIRA_ID>`
   - base: `spec/<sprint>`
   - head: `feat/<JIRA_ID>`
   - body: Jira link, then PRD link
   - do not skip `gh pr create` only because an older PR is `CLOSED`
   - if `gh pr create` fails, return `result: stop`
25. Resolve GitHub reviewer candidates from `temp/<JIRA_ID>/jira-issue.json`:
   - include the root story assignee
   - include `qaRt`
   - include all linked issue assignees from `issueLinks`
   - dedupe candidates by Jira account ID
26. Resolve reviewer GitHub usernames from `team.yml`:
   - match `jira_account_id` from `team.yml` against the deduped Jira account IDs
   - keep only entries that expose a non-empty `github` username
   - treat these usernames as reviewer candidates, not guaranteed-valid reviewers for the current repo
27. If at least one reviewer username is resolved, request reviewers one by one with `gh pr edit --add-reviewer <username>`:
   - if the command succeeds, keep that reviewer in the requested list
   - if the command fails with reviewer-not-found or reviewer-not-assignable feedback, skip that username and continue
   - do not fail the whole finalize step only because one reviewer username is invalid or unavailable for the repo
28. Read `jira.cloud_id` from `config.yml`.
29. Build one Jira comment on the root story that includes:
   - a short review request
   - the created PR link
30. Resolve Jira mention recipients from `temp/<JIRA_ID>/jira-issue.json`:
   - `PIC` from the root story assignee
   - `QA` from `qaRt`
   - `CC` from stakeholders resolved from `issueLinks`
31. Resolve stakeholder recipients from all snapshot `issueLinks`:
   - use the saved linked issue assignee account IDs and display names from the merged snapshot
   - do not filter by `linkType`
   - dedupe recipients by account ID
32. Build the mention tail in this order:
   - `PIC`
   - `QA`
   - `CC`
33. Post exactly one Jira comment on the root story using Atlassian MCP and Jira Cloud ADF mention nodes, not plain-text `@name`.
   - use `cloudId`: value from `config.yml`
   - do not derive `cloudId` from the Jira URL, Jira domain, or aliases such as `701search`
34. Build the Jira comment ADF with this exact structure:
   - paragraph 1: `PR sẵn sàng review: ` text node, followed by the PR URL as a text node with a `link` mark
   - paragraph 2: `PIC: ` text node, one `mention` node, ` QA: ` text node, one `mention` node, ` CC: ` text node, then zero or more `mention` nodes separated by plain text spaces
   - every Jira mention must be:
     - `type: "mention"`
     - `attrs.id`: raw Jira account ID
     - `attrs.text`: `@` + display name
   - do not emit `inlineCard`
   - do not add a second `type` key inside a mention node
35. Return a short status report.
36. Return `result: stop` if the sprint format is invalid, the target branch is missing, the checkout or pull fails, the feature branch cannot be created, the package commit cannot be created, the PR creation fails, the reviewer request fails, `config.yml` is missing, `jira.cloud_id` is missing, or the Jira comment cannot be posted.

## Jira Comment Shape

Use this ADF shape exactly:

```json
{
  "type": "doc",
  "version": 1,
  "content": [
    {
      "type": "paragraph",
      "content": [
        { "type": "text", "text": "PR sẵn sàng review: " },
        {
          "type": "text",
          "text": "<pr-url>",
          "marks": [
            {
              "type": "link",
              "attrs": {
                "href": "<pr-url>"
              }
            }
          ]
        }
      ]
    },
    {
      "type": "paragraph",
      "content": [
        { "type": "text", "text": "PIC: " },
        {
          "type": "mention",
          "attrs": {
            "id": "<pic-account-id>",
            "text": "@<pic-display-name>"
          }
        },
        { "type": "text", "text": " QA: " },
        {
          "type": "mention",
          "attrs": {
            "id": "<qa-account-id>",
            "text": "@<qa-display-name>"
          }
        },
        { "type": "text", "text": " CC: " },
        {
          "type": "mention",
          "attrs": {
            "id": "<cc-account-id-1>",
            "text": "@<cc-display-name-1>"
          }
        },
        { "type": "text", "text": " " },
        {
          "type": "mention",
          "attrs": {
            "id": "<cc-account-id-2>",
            "text": "@<cc-display-name-2>"
          }
        }
      ]
    }
  ]
}
```

## Command Sequence

Use this command order in the happy path.

```bash
git fetch origin spec/<sprint>
git checkout spec/<sprint>
git pull --ff-only origin spec/<sprint>
git show-ref --verify --quiet refs/heads/feat/<JIRA_ID> && echo "local exists" || echo "local missing"
git ls-remote --exit-code --heads origin feat/<JIRA_ID> >/dev/null 2>&1 && echo "remote exists" || echo "remote missing"
git checkout -b feat/<JIRA_ID>
git add features/<JIRA_ID>
git diff --cached --quiet -- features/<JIRA_ID> && echo "no staged diff" || echo "has staged diff"
git commit -m "feat(<JIRA_ID>): update package"
git push -u origin feat/<JIRA_ID>
gh pr view feat/<JIRA_ID>
gh pr create --title "feat/<JIRA_ID>" --base "spec/<sprint>" --head "feat/<JIRA_ID>" --body "<jira link>\n\n<prd link>"
gh pr edit --add-reviewer <github1>
gh pr edit --add-reviewer <github2>
gh pr edit --add-reviewer <github3>
```

Interpretation rules:

- `local exists` means the local feature branch already exists, so stop.
- `remote exists` means the remote feature branch already exists, so stop.
- `local missing` and `remote missing` means it is safe to create the feature branch.
- `no staged diff` means there is no staged package diff, so stop.
- `has staged diff` means the package diff is ready to commit.
- `gh pr view feat/<JIRA_ID>` returning an `OPEN` PR means stop.
- `gh pr view feat/<JIRA_ID>` returning a `CLOSED` PR means continue to the create step, not skip the create step.
- `gh pr view feat/<JIRA_ID>` not finding a PR means continue.
- Skip reviewer requests only when the resolved reviewer candidate list is empty.
- A failed `gh pr edit --add-reviewer <username>` for one username means skip that username and continue trying the remaining reviewer candidates.

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
- reviewers: requested
- jira-comment: posted
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
- Compute the default sprint with `date +"%g.%V"` only. Do not use `%W`, `%U`, or other non-ISO week formats.
- Reuse the saved Jira and PRD source links. Do not re-scrape external systems in this step.
- Always branch from `spec/<sprint>`, never from the current branch by accident.
- Use `git fetch origin spec/<sprint>` before checking out the sprint branch.
- Use `git pull --ff-only origin spec/<sprint>` before creating `feat/<JIRA_ID>`.
- Check both local and remote branch existence before creating `feat/<JIRA_ID>`.
- Stage only `features/<JIRA_ID>` in this step. Do not stage `temp/`, skill docs, or unrelated repo changes.
- Create exactly one package commit before pushing the feature branch.
- If `features/<JIRA_ID>` has no staged diff, stop before `git commit`, `git push`, or `gh pr create`.
- Check existing PR state with `gh pr view feat/<JIRA_ID>` before `gh pr create`.
- Treat an existing `OPEN` PR as blocking.
- Treat an existing `CLOSED` PR as non-blocking for pre-check only. A closed PR never satisfies the finalize objective by itself.
- After a `CLOSED` PR is detected, still run `gh pr create`. Do not silently skip PR creation.
- Do not overwrite an existing `feat/<JIRA_ID>` branch or an existing `OPEN` PR with the same head branch. Warn and stop instead.
- Keep the PR body minimal: Jira link and PRD link only.
- Use `gh` CLI for PR creation and reviewer requests. Do not use a GitHub plugin in the happy path.
- Resolve GitHub reviewers from `team.yml`, not from free-text names.
- Request review from all mapped stakeholders found in the ticket snapshot: root assignee, `qaRt`, and linked issue assignees.
- Dedupe GitHub reviewers by username before requesting review.
- If a Jira stakeholder cannot be mapped through `team.yml`, skip that reviewer silently and continue with the mapped reviewers.
- Request reviewers one by one, not in a single comma-separated batch command.
- Treat `team.yml` GitHub usernames as candidates only. A username may still be invalid or non-assignable for the target repo.
- If `gh pr edit --add-reviewer <username>` reports reviewer-not-found or reviewer-not-assignable, skip that username and continue.
- Post the Jira handoff comment only after the PR is created successfully and reviewer requests are attempted.
- Include `PIC`, `QA`, then `CC` in the Jira mention tail.
- Use `qaRt` as the QA source of truth. Do not infer QA from old Jira comments.
- Resolve stakeholder `CC` mentions from all linked issue assignees in the merged `issueLinks` snapshot produced by `fetch-jira-issue`.
- Treat snapshot `issueLinks` as the source of truth for linked issue assignee data in this step.
- Use Atlassian MCP only for the final Jira comment in this step.
- Read Jira `cloud_id` from `config.yml` before posting the Jira comment.
- Use the configured Jira `cloud_id` directly for Atlassian MCP comment calls.
- Do not derive, infer, or shorten the Jira `cloud_id` from the Jira URL or domain.
- Build Jira mentions with `type: "mention"` only. Never serialize them as `inlineCard`.
- Prefix every Jira mention `attrs.text` with `@`.
- Do not fake plain-text `@name` mentions such as `PIC: @Minh Tin Trieu`.
- Keep the Jira handoff comment short: review request, PR link, and a mention tail with `PIC`, `QA`, and `CC` only.
