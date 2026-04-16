# ct-platform-spec-v2

AI-native platform spec workflow.

## Source of Truth

- `.agents/commands` is the canonical command layer.
- `.agents/skills` is the canonical skill layer.
- `config.yml` is the canonical shared workflow config.
- `.claude/commands` and `.claude/skills` should point to `.agents`.
- `.cursor/commands` and `.cursor/skills` should point to `.agents`.

Client-specific configuration stays in:

- `.claude/settings.json`
- `.claude/settings.local.json`
- `.cursor/settings.json`
- `.cursor/mcp.json`
- `.mcp.json`

Shared workflow configuration stays in:

- `config.yml`

Device-level shared configuration stays in:

- `~/.config/platform-spec/platform-spec.yml`

## Phase 1

The first workflow entrypoint is `/analyze`.

## Onboarding

Use `/onboarding` to create or refresh the device-level shared config for this spec repo.

`/analyze` expects onboarding to be completed first and will stop if `~/.config/platform-spec/platform-spec.yml` is missing or points to a different repo path.

Its job is to orchestrate:

1. Node availability check
2. Atlassian, GitHub, and Knowledge Hub availability/auth check
3. Jira issue fetch
4. PRD link detection and Confluence content fetch
5. Master spec package materialization
6. Finalization

Step 1 of the command delegates to `.agents/skills/analyze/check-environment-readiness/SKILL.md`.
