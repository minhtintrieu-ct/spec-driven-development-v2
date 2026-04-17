# ct-platform-spec-v2

AI-native platform spec workflow.

## Quick Start

1. Open this repo.
2. Run `/onboarding` once on your machine.
3. When a new Jira story arrives, run `/analyze <JIRA_URL>`.
4. When the package is approved and implementation needs to start, go to the target working repo and run `ship`.
5. When `.planning/<JIRA_ID>/` is ready, run `/implement`.

## Commands

### `/onboarding`

Use this when setting up a machine for the first time or refreshing shared config.

This command will:

- create or refresh `~/.config/platform-spec/platform-spec.yml`
- publish the global `ship` skill for Codex, Claude, and Cursor
- publish the global MCP config

Run this command inside the `spec repo`.

### `/analyze <JIRA_URL>`

Use this to create the initial package for a Jira story.

This command will:

- check the environment
- fetch the Jira issue
- fetch the linked Confluence PRD if it exists
- generate the package under `features/<JIRA_ID>/`
- finalize the master spec flow

Main outputs:

- `features/<JIRA_ID>/master-spec.md`
- `features/<JIRA_ID>/knowledge.md`
- `features/<JIRA_ID>/open-questions.md`
- `features/<JIRA_ID>/api-contract.md`

### `/complete`

Use this when the package already exists and new information needs to be folded in.

This command will:

- evaluate blockers
- update open questions
- update impacted artifacts when needed
- finalize the master package again

Typical use cases:

- BA answered open questions
- the PRD changed
- the API contract or requirements became clearer

### `ship`

Use this to prepare implementation from an approved package.

For `web`, `android`, `ios`, `backend`, and `qe`:

- run it inside the target `working repo`
- sync to the registered `dev_base_branch`
- enrich KH context
- explore the real repo
- generate the platform delta
- write artifacts under `.planning/<feature>/`

For `qc`:

- run it inside the `spec repo`
- do not use `.planning/`

On the first `ship` run in a working repo, provide the `dev base branch`.

### `/implement`

Use this after `.planning/<feature>/` is ready in the `working repo`.

This command checks that these artifacts exist:

- `kh-search-brief.md`
- `repo-exploration.md`
- the platform delta

`/implement` supports 2 modes:

- `full`: implement directly from the delta in one pass
- `multi`: create a plan, implement by phase, and stop after each phase for user review

If `mode` is missing, the command explains the modes briefly and stops so the user can choose.

## Repo Roles

### Spec Repo

This repo is the source of truth for the package and the shared workflow.

It owns:

- `.agents/commands`
- `.agents/skills`
- `features/<JIRA_ID>/...`
- `config.yml`
- `team.yml`

### Working Repo

This is the real implementation repo for a platform.

Non-`qc` flows create temporary artifacts under:

- `.planning/<feature>/`

Ignore `.planning/` in the `working repo`, not in the `spec repo`.

## Shared Config

The shared device-level config lives at:

- `~/.config/platform-spec/platform-spec.yml`

This file stores:

- `spec_repo.path`
- the current user identity
- `working_repos`
- the reusable `dev_base_branch` for each working repo
