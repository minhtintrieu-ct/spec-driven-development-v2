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

This shared file stores both:

- the registered `spec_repo.path` and current user identity
- per-working-repo registration under `working_repos`, including the reusable `dev_base_branch`

## Phase 1

The first workflow entrypoint is `/analyze`.

## Onboarding

Use `/onboarding` to:

- create or refresh the device-level shared config for this spec repo
- publish the global `ship` skill for Codex, Claude, and Cursor

`/analyze` expects onboarding to be completed first and will stop if `~/.config/platform-spec/platform-spec.yml` is missing or points to a different repo path.

`/onboarding` publishes the global skill by linking:

- `<spec-repo>/.agents/skills/ship`

into:

- `~/.codex/skills/ship`
- `~/.claude/skills/ship`
- `~/.cursor/skills/ship`

## Implementation

Use the `ship` skill to prepare implementation from the platform spec package.

Current supported input:

- `platform = web`
- `platform = android`
- `platform = ios`
- `platform = backend`
- `platform = qc`
- `platform = qe`

`ship` routes by `platform` and currently delegates:

- `platform = web` to `.agents/skills/ship/web/SKILL.md`
- `platform = android` to `.agents/skills/ship/android/SKILL.md`
- `platform = ios` to `.agents/skills/ship/ios/SKILL.md`
- `platform = backend` to `.agents/skills/ship/backend/SKILL.md`
- `platform = qc` to `.agents/skills/ship/qc/SKILL.md`
- `platform = qe` to `.agents/skills/ship/qe/SKILL.md`

`qc` is the only supported platform that runs directly in the spec repo. The other platforms must run in a working repo.
QC artifacts are written under `TCs/<JIRA_ID>/` in the spec repo.
For non-`qc` platforms, the working-repo handoff helper prepares a disposable `.planning/<feature>/` bundle in the current working repo. If the selected feature name is the Jira ID, the bundle path becomes `.planning/<JIRA_ID>/`.
In this repo's current migration state, do not use the legacy helper under `~/.platform-spec/` for `ship`; it may still point to `ct-common-platform-specs` instead of the registered spec repo.
Because `.planning/` is generated in the working repo, ignore it in the working repo's `.gitignore`, not in this spec repo by default.

For every non-`qc` platform, the first `ship` run in a working repo must provide that repo's `dev base branch`. `ship` then reuses the stored value from `~/.config/platform-spec/platform-spec.yml` under `working_repos`.

The web flow preserves the carried web command/template contract from the older platform layer through:

- `.agents/skills/ship/web/references/command.md`
- `.agents/skills/ship/web/references/template.md`
