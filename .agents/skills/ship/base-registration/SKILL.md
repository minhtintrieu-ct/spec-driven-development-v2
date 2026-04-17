---
name: ship-base-registration
description: Register or refresh the current working repo in the shared ship config after `resolve-base` succeeds. Use this to resolve and persist the reusable `dev_base_branch` for later ship phases.
---

# Ship Base Registration

Use this skill only after `resolve-base/SKILL.md` succeeds.

It only handles working-repo registration. It does not inspect git working tree state beyond the remote URL, and it does not run repo sync.

## Required Input

- `platform`
- `repo_role`
- `current_repo_root`
- `working_repo_entry`

Conditionally required when the working repo has no reusable registration:

- `dev base branch`

## Required Flow

1. Require `repo_role = working-repo`.
2. Read `~/.config/platform-spec/platform-spec.yml`.
3. Resolve the current repo remote URL with `git remote get-url origin`.
4. Decide the registration action:
   - if no reusable `working_repo_entry` exists, require explicit `dev base branch` and create a new entry
   - if a `working_repo_entry` exists, reuse `dev_base_branch` when the stored registration is still plausible
   - if the stored `remote_url` exists and differs from the current repo remote URL, require explicit `dev base branch` and refresh the entry
5. Persist `working_repos[<current_repo_root>]` with:
   - `remote_url`
   - `dev_base_branch`
6. Preserve unrelated shared-config fields and other working-repo entries.
7. Return one normalized registration result.

## Output

```md
- base-registration: ok
- platform: web
- repo-role: working-repo
- dev-base-branch: develop
- current-remote-url: git@github.com:org/repo.git
- registration: created|reused|refreshed
- result: continue
```

If registration fails:

```md
- base-registration: blocked
- result: stop
```

## Guardrails

- This skill is only for non-`qc` working-repo flows.
- Require `platform`, `repo_role`, `current_repo_root`, and `working_repo_entry` from `resolve-base`.
- Require explicit `dev base branch` only when no reusable registration is available.
- Reuse the stored `dev_base_branch` only when the stored registration is still plausible for the current repo.
- Do not read package artifacts here.
- Do not inspect dirty working tree state here.
- Do not fetch or verify remote refs here.
- Do not run KH here.
- Do not generate delta here.
