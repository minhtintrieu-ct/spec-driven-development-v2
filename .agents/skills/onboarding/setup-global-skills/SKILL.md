---
name: setup-global-skills
description: Publish the registered platform shipping skill globally for Codex, Claude, and Cursor by linking the spec repo skill into each client's user-level skills directory. Use during `/onboarding` after the shared config has been written.
---

# Setup Global Skills

Publish the global shipping skill for all supported clients.

## Execution Contract

Use this fixed toolchain in the happy path:

- `git` CLI to resolve the current repo root
- `mkdir` to create client skill directories when needed
- `ln -sfn` to refresh global skill symlinks deterministically
- `~/.config/platform-spec/platform-spec.yml` as the registered spec repo source of truth

Publish this exact skill:

- `<repo-root>/.agents/skills/ship`

To these exact destinations:

- `~/.codex/skills/ship`
- `~/.claude/skills/ship`
- `~/.cursor/skills/ship`

Do not write into `~/.cursor/skills-cursor`.

## Workflow

1. Resolve the current repo root with `git rev-parse --show-toplevel`.
2. Require `~/.config/platform-spec/platform-spec.yml` to exist.
3. Read `spec_repo.path` from that config.
4. Require the current repo root to exactly match `spec_repo.path`.
5. Require `<repo-root>/.agents/skills/ship/SKILL.md` to exist.
6. Create these parent directories if needed:
   - `~/.codex/skills`
   - `~/.claude/skills`
   - `~/.cursor/skills`
7. Refresh the symlink for each client so `ship` points to `<repo-root>/.agents/skills/ship`.
8. Return a short status report.
9. Return `result: stop` if the repo root cannot be resolved, the shared config is missing, `spec_repo.path` does not match the current repo, or the source skill folder is missing.

## Command Sequence

Use this command order in the happy path.

```bash
git rev-parse --show-toplevel
mkdir -p ~/.codex/skills
mkdir -p ~/.claude/skills
mkdir -p ~/.cursor/skills
ln -sfn <repo-root>/.agents/skills/ship ~/.codex/skills/ship
ln -sfn <repo-root>/.agents/skills/ship ~/.claude/skills/ship
ln -sfn <repo-root>/.agents/skills/ship ~/.cursor/skills/ship
```

## Output

If publishing succeeds:

```md
- onboarding: ok
- global-skill: ship
- codex: linked
- claude: linked
- cursor: linked
- result: continue
```

If publishing fails:

```md
- onboarding: blocked
- global-skill: ship
- result: stop
```

## Guardrails

- Use `git rev-parse --show-toplevel` to resolve the repo root. Do not guess the path.
- Require `~/.config/platform-spec/platform-spec.yml` before linking global skills.
- Require `spec_repo.path` from the global config to match the current repo root exactly.
- Publish only one global shipping skill for this flow: `ship`.
- Refresh symlinks deterministically. Do not copy the skill folder.
- Do not write into repo-local `.claude/`, `.cursor/`, or `.codex` folders in this step.
- Do not touch `~/.cursor/skills-cursor`.
