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
5. If the page contains multiple ticket slices, try auto-detect first by matching the current Jira ID in the page title or section headings.
6. If one slice matches clearly, use it.
7. If no clear match exists, ask the user to choose the correct slice.
8. Save the selected markdown to `temp/<JIRA_ID>/prd.md`.
9. Save the source metadata and selection mode to `temp/<JIRA_ID>/prd-source.json`.
10. Return a short status report.
11. Return `result: stop` if the temp file is missing, the PRD link is missing, the Confluence fetch fails, or slice selection is unresolved.

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
- Auto-detect the right slice from title or headings first.
- If auto-detect is not clear, ask the user and remember the choice in `prd-source.json`.
- Fetch Confluence only. Do not rewrite Jira data here.
- Write under `temp/<JIRA_ID>/`.
- Keep `temp/` out of git.
