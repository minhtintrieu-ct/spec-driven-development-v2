---
name: fetch-jira-issue
description: Fetch a Jira issue through Atlassian and save the normalized result into temp storage. Use when the workflow needs the raw story intake before PRD fetching or package writing, and keep the fetched issue out of git by writing it under `temp/<JIRA_ID>/`.
---

# Fetch Jira Issue

Fetch the Jira issue only.

Save the result to temp storage and stop if the issue cannot be fetched.

## Workflow

1. Require a Jira URL.
2. Read `jira.cloud_id` from root `config.yml`.
3. Use that configured cloud ID for all Atlassian Jira calls in this step.
4. Fetch the main Jira issue through Atlassian exactly once.
5. Extract the Jira ID as uppercase, for example `PLMO-1328`.
6. Read the Jira user-picker field `QA [RT]` or its equivalent custom field when present.
7. Read `issueLinks` relation metadata from the main issue.
8. Run one Jira JQL search for all linked issues, for example `issue in linkedIssues(<JIRA_ID>)`.
9. Merge the linked-issue JQL results into the saved `issueLinks` list:
   - keep the main issue snapshot as the source of truth for relation type and direction
   - use the JQL result as the source of truth for expanded linked issue assignee data
   - if a linked issue is present in the main issue snapshot but missing from the JQL result, keep the relation metadata and leave assignee fields null
10. Create `temp/<JIRA_ID>/`.
11. Save the normalized issue payload to `temp/<JIRA_ID>/jira-issue.json`.
12. Return a short status report.
13. Return `result: stop` if `config.yml` is missing, `jira.cloud_id` is missing, the main issue fetch fails, the linked-issue JQL search fails, or the ticket ID cannot be resolved.

## Call Budget

Use the cheapest safe call pattern.

Happy path minimum:

1. One `Get issue` call for the main story
2. One `Search with JQL` call for all linked issues

Do not call `List accessible resources` in the happy path when `config.yml` already provides the Jira `cloud_id`.
Do not call field metadata in the happy path when the Jira field ID for `QA [RT]` is already known for this project.
Do not call `Get issue` more than once for the same story in this step.
Do not make a follow-up `Get issue` call just to request a different field subset or a larger `expand` value after the first successful main-issue fetch.

Use this exact request shape as the happy-path target:

1. Main issue `Get issue`
   - `cloudId`: value from `config.yml`
   - `issueIdOrKey`: resolved Jira key
   - `fields`: only the minimum fields needed for the normalized payload and linked-issue merge, including the known `QA [RT]` custom field
   - `expand`: omit unless a concrete downstream need is documented
2. Linked issues `Search with JQL`
   - `cloudId`: value from `config.yml`
   - `jql`: `issue in linkedIssues(<JIRA_ID>)`
   - `fields`: only the minimum linked-issue fields needed to hydrate `issueLinks`

## Output

Return a short report only.

Use this shape:

```md
- jira: ok
- ticket-id: PLMO-1328
- temp-dir: temp/PLMO-1328
- result: continue
```

If fetch fails:

```md
- jira: blocked
- result: stop
```

## Normalized Payload

Keep only the fields needed by later steps:

- `issueKey`
- `issueUrl`
- `summary`
- `assignee`
- `assigneeAccountId`
- `qaRt`
- `qaRtAccountId`
- `descriptionMarkdown`
- `issueLinks`
- `extractedLinks`

`descriptionMarkdown` is the canonical PRD link source for the next step.

Keep this payload minimal. Do not store fields that are not used by downstream steps.

Normalize `issueLinks` into a compact list that still preserves stakeholder routing data for later steps.

Keep these fields for each linked issue when available:

- `linkType`
- `direction`
- `issueKey`
- `issueUrl`
- `assignee`
- `assigneeAccountId`

Keep `qaRt` and `qaRtAccountId` when the Jira issue exposes the `QA [RT]` user-picker field or its equivalent.

Treat the saved `issueLinks` list as a merged snapshot:

- relation metadata comes from the main issue fetch
- assignee identity comes from the linked-issue JQL search when available

## Guardrails

- Fetch Jira only. Do not fetch PRD here.
- Read `jira.cloud_id` from root `config.yml` first.
- Write under `temp/<JIRA_ID>/`.
- Keep `temp/` out of git.
- Do not write into `features/` in this step.
- Preserve `descriptionMarkdown` as-is because the PRD link is expected to live there.
- Read `QA [RT]` from the actual Jira custom field, not from comment text.
- Preserve account identifiers for the story assignee, QA, and linked issue assignees when the Atlassian route exposes them.
- Use exactly one linked-issue JQL search per story intake, not one Jira fetch per linked issue.
- Use the configured Jira `cloud_id` directly. Do not probe with `List accessible resources` in the happy path.
- Do not refetch the same Jira story in this step after the first successful `Get issue` call.
- Do not make a second or third `Get issue` call for the same story to inspect changelog, transitions, comments, or alternate renderings.
- Do not call field metadata in the happy path just to rediscover the `QA [RT]` field ID.
- If the Jira field ID for `QA [RT]` is stable for this project, request it directly in the main issue fetch.
- Request only the minimum main-issue fields needed for the normalized payload and linked-issue merge.
- Do not request `transitions`, `changelog`, or broad `expand` values such as `fields,changelog,comments` in the happy path.
- Do not request `created`, `updated`, `labels`, `priority`, `status`, `reporter`, or other unused story fields in the happy path.
- Do not request `comment` in the main issue fetch unless a downstream step explicitly needs Jira comments from this snapshot.
- Do not request `renderedFields` in the happy path when `fields.description` already provides the needed PRD link source.
- Keep `issueLinks` compact and normalized. Do not dump the raw Jira payload.
- Avoid storing reporter, email, comment, status, priority, or other fields unless a downstream step actually consumes them.
