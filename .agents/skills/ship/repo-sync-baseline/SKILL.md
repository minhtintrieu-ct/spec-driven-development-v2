---
name: ship-repo-sync-baseline
description: Sync the current working repo onto the registered reusable base branch after `base-registration` succeeds. Use this to stop on local changes, switch to `dev_base_branch`, and verify a valid `origin/<dev_base_branch>` baseline before later ship phases continue.
---

# Ship Repo Sync Baseline

Use this skill only after `base-registration/SKILL.md` succeeds.

It only validates and syncs the reusable base branch. It does not mutate registration, inspect package artifacts, or touch platform delta.

## Required Input

- `platform`
- `repo_role`
- `dev_base_branch`

## Required Flow

1. Require `repo_role = working-repo`.
2. Read the working tree state with `git status --porcelain`.
3. Stop if the working tree is dirty. Tell the user to clean or stash local changes before ship continues.
4. Ensure the working repo `.gitignore` contains `.planning/`.
   - if `.gitignore` does not exist yet, create it
   - if `.planning/` is missing, append it once
   - if `.planning/` is already present, leave the file unchanged
5. Switch to `dev_base_branch` with `git checkout <dev_base_branch>`.
6. Fetch `origin/<dev_base_branch>`.
7. Pull the local `dev_base_branch` with `git pull --ff-only origin <dev_base_branch>`.
8. Verify `origin/<dev_base_branch>`.
9. Resolve the current branch name with `git branch --show-current`.
10. Confirm the current branch is now exactly `dev_base_branch`.
11. Return one normalized repo-sync result.

## Command Sequence

Use this command order in the happy path:

```bash
git status --porcelain
touch .gitignore
# append `.planning/` only when missing
git checkout <dev_base_branch>
git fetch origin <dev_base_branch>
git pull --ff-only origin <dev_base_branch>
git rev-parse --verify origin/<dev_base_branch>
git branch --show-current
```

## Output

```md
- repo-sync-baseline: ok
- platform: web
- repo-role: working-repo
- dev-base-branch: develop
- gitignore-planning: ensured
- current-branch: develop
- repo-sync: ok
- result: continue
```

If repo sync fails:

```md
- repo-sync-baseline: blocked
- result: stop
```

## Guardrails

- This skill is only for non-`qc` working-repo flows.
- Require `platform`, `repo_role`, and `dev_base_branch`.
- Stop on dirty working tree.
- Ensure `.planning/` is ignored in the working repo `.gitignore` before later ship phases write scratch artifacts.
- Stop if `git checkout <dev_base_branch>` fails.
- Stop if `git pull --ff-only origin <dev_base_branch>` fails.
- Stop if `origin/<dev_base_branch>` cannot be resolved.
- Do not refresh registration here.
- Do not create a feature branch here.
- Do not merge or rebase here.
- Do not run KH here.
- Do not generate delta here.
