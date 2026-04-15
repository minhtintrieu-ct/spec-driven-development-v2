---
name: fetch-confluence
description: Detect a Confluence PRD link from the Jira issue temp file and fetch the PRD markdown into temp storage. Use when the workflow already has `temp/<JIRA_ID>/jira-issue.json` and needs to save the linked PRD as `temp/<JIRA_ID>/prd.md`.
---

# Fetch Confluence

Fetch the PRD from Confluence.

Read the Jira temp file, detect the PRD link from `descriptionMarkdown`, fetch the page, and save the markdown beside the Jira issue payload.

## Workflow

1. Require `temp/<JIRA_ID>/jira-issue.json`.
2. Read `descriptionMarkdown` from that file.
3. Extract the first valid Confluence URL from `descriptionMarkdown`.
4. Fetch the Confluence page through Atlassian.
5. Save the markdown to `temp/<JIRA_ID>/prd.md`.
6. Save the source metadata to `temp/<JIRA_ID>/prd-source.json`.
7. Return a short status report.
8. Return `result: stop` if the temp file is missing, the PRD link is missing, or the Confluence fetch fails.

## Output

Return a short report only.

Use this shape:

```md
- confluence: ok
- jira-id: PLMO-1366
- prd-link: found
- prd-path: temp/PLMO-1366/prd.md
- result: continue
```

If the PRD link is missing:

```md
- confluence: blocked
- prd-link: missing
- result: stop
```

## Saved Files

Write:

- `temp/<JIRA_ID>/prd.md`
- `temp/<JIRA_ID>/prd-source.json`

## Guardrails

- Read the PRD link from `descriptionMarkdown` first.
- Use the first valid Confluence URL.
- Fetch Confluence only. Do not rewrite Jira data here.
- Write under `temp/<JIRA_ID>/`.
- Keep `temp/` out of git.
