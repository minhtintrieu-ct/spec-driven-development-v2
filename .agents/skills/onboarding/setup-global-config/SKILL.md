---
name: setup-global-config
description: Create or refresh the shared device-level `platform-spec.yml` config for platform spec workflows. Use when a user runs `/onboarding`, wants to register this spec repo on their device, or needs to detect the current GitHub identity and map it to `team.yml`.
---

# Setup Global Config

Create the shared device-level config for this workflow.

Write only one global config file and keep the flow deterministic.

## Execution Contract

Use this fixed toolchain in the happy path:

- `git` CLI to resolve the current repo root
- `gh` CLI to detect the authenticated GitHub user
- local repo files `team.yml` and `config.yml` as the source of truth

Write the shared config to this exact path:

- `~/.config/platform-spec/platform-spec.yml`

Do not publish global skills in this step. That happens in `setup-global-skills`.
Do not write client-specific data into `.claude/` or `.cursor/` in this step.

## Workflow

1. Resolve the current repo root with `git rev-parse --show-toplevel`.
2. Require the repo root to contain:
   - `config.yml`
   - `team.yml`
3. Read the absolute repo root path.
4. Read the current GitHub login with `gh api user --jq '.login'`.
5. If the GitHub login cannot be resolved, return `result: stop`.
6. Read `team.yml`.
7. Match the current GitHub login against `team[].github` using exact string match.
8. Build the shared config payload:
   - `spec_repo.path`: absolute path to this spec repo
   - `user.github_login`: detected GitHub login
   - `user.team_member`: the matched `team.yml` record when present, else `null`
   - `working_repos`: initialize as an empty mapping when missing
9. Create the parent directory for `~/.config/platform-spec/platform-spec.yml` if needed.
10. If the config file already exists, preserve any existing `working_repos` entries while refreshing `spec_repo` and `user`.
11. Write the config file.
12. Return a short status report.
13. Return `result: stop` if the repo root cannot be resolved, the required repo files are missing, the GitHub login cannot be detected, or the config file cannot be written.

## Global Config Shape

Write this YAML shape:

```yml
spec_repo:
  path: "/absolute/path/to/ct-platform-spec-v2"

user:
  github_login: "minhtintrieu-ct"
  team_member:
    name: "Tin Trieu Minh"
    github: "minhtintrieu-ct"
    role: "fe"
    jira_account_id: "712020:343b69ba-8636-4519-b639-a6b3b3cd5927"

working_repos: {}
```

If no `team.yml` match is found:

```yml
spec_repo:
  path: "/absolute/path/to/ct-platform-spec-v2"

user:
  github_login: "unknown-user"
  team_member: null

working_repos: {}
```

After a working repo has been registered through `ship`, the same file may grow like this:

```yml
spec_repo:
  path: "/absolute/path/to/ct-platform-spec-v2"

user:
  github_login: "minhtintrieu-ct"
  team_member:
    name: "Tin Trieu Minh"
    github: "minhtintrieu-ct"
    role: "fe"
    jira_account_id: "712020:343b69ba-8636-4519-b639-a6b3b3cd5927"

working_repos:
  "/absolute/path/to/working-repo":
    remote_url: "git@github.com:org/repo.git"
    dev_base_branch: "develop"
```

## Command Sequence

Use this command order in the happy path.

```bash
git rev-parse --show-toplevel
gh api user --jq '.login'
mkdir -p ~/.config/platform-spec
```

Use the repo root from `git rev-parse --show-toplevel` to read:

- `<repo-root>/config.yml`
- `<repo-root>/team.yml`

Then write:

- `~/.config/platform-spec/platform-spec.yml`

## Output

If onboarding succeeds with a team match:

```md
- onboarding: ok
- config: ~/.config/platform-spec/platform-spec.yml
- github-login: minhtintrieu-ct
- team-member: matched
- result: continue
```

If onboarding succeeds without a team match:

```md
- onboarding: ok
- config: ~/.config/platform-spec/platform-spec.yml
- github-login: unknown-user
- team-member: no-match
- result: continue
```

If onboarding fails:

```md
- onboarding: blocked
- result: stop
```

## Guardrails

- Use `git rev-parse --show-toplevel` to resolve the repo root. Do not guess the path.
- Require both `config.yml` and `team.yml` at the repo root before writing the global config.
- Use `gh api user --jq '.login'` as the GitHub identity source of truth in this step.
- Match `team.yml` by exact `github` username only. Do not infer by display name.
- Write only one shared config file: `~/.config/platform-spec/platform-spec.yml`.
- Store the absolute repo path, not a relative path.
- Keep the written config deterministic: `spec_repo.path`, `user.github_login`, `user.team_member`, and top-level `working_repos`.
- Preserve the matched `team_member` record as-is from `team.yml`.
- Preserve existing `working_repos` entries when refreshing onboarding for the same device.
- If no team member matches the detected GitHub login, write `team_member: null` and still return success.
- Do not fetch Jira, Confluence, or GitHub PR data in this step.
