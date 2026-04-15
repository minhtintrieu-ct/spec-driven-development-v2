# ct-platform-spec-v2

AI-native platform spec workflow.

## Source of Truth

- `.agents/commands` is the canonical command layer.
- `.agents/skills` is the canonical skill layer.
- `.claude/commands` and `.claude/skills` should point to `.agents`.
- `.cursor/commands` and `.cursor/skills` should point to `.agents`.

Client-specific configuration stays in:

- `.claude/settings.json`
- `.claude/settings.local.json`
- `.cursor/settings.json`
- `.cursor/mcp.json`
- `.mcp.json`

## Phase 1

The first workflow entrypoint is `/analyze`.

Its job is to orchestrate:

1. Node availability check
2. Atlassian, GitHub, and Knowledge Hub availability/auth check
3. Jira issue fetch
4. PRD link detection and Confluence content fetch
5. Master spec package materialization
6. Finalization

Step 1 of the command delegates to `.agents/skills/analyze/check-environment-readiness/SKILL.md`.
