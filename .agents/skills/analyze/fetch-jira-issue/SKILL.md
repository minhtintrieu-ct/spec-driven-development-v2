---
name: fetch-jira-issue
description: Fetch a Jira issue through Atlassian and save the normalized result into temp storage. Use when the workflow needs the raw story intake before PRD fetching or package writing, and keep the fetched issue out of git by writing it under `temp/<JIRA_ID>/`.
---

# Fetch Jira Issue

Fetch the Jira issue only.

Save the result to temp storage and stop if the issue cannot be fetched.

## Workflow

1. Require a Jira URL.
2. Fetch the issue through Atlassian.
3. Extract the Jira ID as uppercase, for example `PLMO-1328`.
4. Create `temp/<JIRA_ID>/`.
5. Save the normalized issue payload to `temp/<JIRA_ID>/jira-issue.json`.
6. Return a short status report.
7. Return `result: stop` if Jira fetch fails or the ticket ID cannot be resolved.

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
- `issueType`
- `status`
- `priority`
- `assignee`
- `reporter`
- `descriptionMarkdown`
- `comments`
- `remoteLinks`
- `extractedLinks`

`descriptionMarkdown` is the canonical PRD link source for the next step.

## Guardrails

- Fetch Jira only. Do not fetch PRD here.
- Write under `temp/<JIRA_ID>/`.
- Keep `temp/` out of git.
- Do not write into `features/` in this step.
- Preserve `descriptionMarkdown` as-is because the PRD link is expected to live there.
