# Ship Flow

This document explains how `ship` currently works in a human-friendly way.

Use it when you want to understand:

- where `ship` should be invoked
- what each shared phase is responsible for
- what artifacts are produced along the way
- how package, KH context, repo exploration, and delta relate to each other

## Short Version

For non-`qc` platforms, `ship` is a preparation flow that starts in a working repo, reads the approved package from the registered spec repo, narrows the search space, verifies real code touch points, and only then moves into platform-specific delta work.

The current shared flow is:

1. `resolve-base`
2. `base-registration`
3. `repo-sync-baseline`
4. `fetch-kh-context`
5. `explore-working-repo`
6. route to the selected platform skill

`qc` is the exception. It runs in the spec repo directly and does not use the working-repo `.planning/` flow.

## Where To Run Ship

### Non-`qc` platforms

Run `ship` in the target working repo:

- web
- android
- ios
- backend
- qe

Examples:

- run `ship` for web in the web working repo
- run `ship` for backend in the backend working repo

### `qc`

Run `ship` for `qc` in the spec repo.

`qc` does not prepare a working-repo bundle under `.planning/`.

## Shared Config

`ship` uses the shared device-level config at:

- `~/.config/platform-spec/platform-spec.yml`

This config stores:

- the registered `spec_repo.path`
- user identity
- per-working-repo registration under `working_repos`
- the reusable `dev_base_branch` for each registered working repo

## Phase 1: Resolve Base

Purpose:

- resolve the shared ship context
- classify the current repo
- load the working-repo registration entry if one already exists

What it does:

1. Read `~/.config/platform-spec/platform-spec.yml`
2. Resolve `spec_repo.path`
3. Resolve `current_repo_root`
4. Classify the current repo as either:
   - `spec repo`
   - `working repo`
5. Load `working_repos[<current_repo_root>]` if present

What it returns:

- `repo_role`
- `spec_repo.path`
- `current_repo_root`
- `working_repo_entry`

What it does not do:

- no git sync
- no Knowledge Hub
- no delta generation
- no code exploration

## Phase 2: Base Registration

Purpose:

- register the current working repo if needed
- reuse or refresh the repo's `dev_base_branch`

This phase only applies to non-`qc` working-repo flows.

What it does:

1. Resolve the current repo remote URL
2. Check whether the repo already has a stored registration entry
3. If this is the first run for the repo:
   - require an explicit `dev_base_branch`
4. If a registration already exists:
   - reuse the stored `dev_base_branch`
   - or refresh it if the repo identity changed
5. Persist the normalized registration back into `working_repos`

What it returns:

- `dev_base_branch`
- `registration: created|reused|refreshed`

What it does not do:

- no git checkout
- no git pull
- no KH query
- no repo exploration

## Phase 3: Repo Sync Baseline

Purpose:

- stop on local dirt
- switch the working repo onto the reusable base branch
- make sure the local base branch is up to date with remote
- prepare the working repo to hold `.planning/` artifacts safely

What it does:

1. Check `git status --porcelain`
2. If local changes exist, stop and ask the user to clean or stash first
3. Ensure `.planning/` is ignored in the working repo's `.gitignore`
4. Switch to `dev_base_branch`
5. Run `git fetch origin <dev_base_branch>`
6. Run `git pull --ff-only origin <dev_base_branch>`
7. Verify `origin/<dev_base_branch>` exists

What it returns:

- `repo_sync: ok`
- `current_branch`
- `remote_base_ref`

Why this phase matters:

- later repo exploration should not be done on stale code
- later `.planning/` artifacts should not end up accidentally tracked

## Phase 4: Fetch KH Context

Purpose:

- reduce search space before exploring the working repo

This phase does not replace repo exploration. It only gives better starting hints.

Required package inputs:

- `<spec_repo.path>/features/<feature>/master-spec.md`
- `<spec_repo.path>/features/<feature>/knowledge.md`

Optional when present:

- `<spec_repo.path>/features/<feature>/api-contract.md`

What it does:

1. Read the approved package from the spec repo
2. Build one stable `engineer_query_knowledge` query
3. Ask KH for search-space reduction hints
4. Normalize the answer into one short brief
5. Write the result to:
   - `.planning/<feature>/kh-search-brief.md`

What it returns:

- `fetch-kh-context: ok|degraded`
- normalized search hints for later repo exploration

What the KH brief is for:

- candidate modules
- candidate keywords
- candidate touch points
- candidate dependencies or integrations

What the KH brief is not:

- not source of truth
- not a delta
- not an implementation plan

## Phase 5: Explore Working Repo

Purpose:

- verify package scope and KH hints against the actual code in the working repo

Required inputs:

- `.planning/<feature>/kh-search-brief.md`
- `master-spec.md`
- `knowledge.md`

Probe:

- `api-contract.md` when present

What it does:

1. Read the KH search brief
2. Read the package artifacts
3. Convert the KH brief into a short search plan
4. Explore the repo with targeted search and file reads
5. Confirm real touch points in code
6. Capture reusable patterns
7. Capture repo constraints
8. Capture conflicts between:
   - package and code
   - KH hints and code
9. Write the result to:
   - `.planning/<feature>/repo-exploration.md`

What it returns:

- `confirmed-touch-points`
- `reusable-patterns`
- `repo-constraints`
- `code-vs-package-conflicts`

What it does not do:

- no delta generation
- no implementation planning
- no product code changes

## Platform Routing

After the shared baseline finishes, `ship` routes into the selected platform skill:

- `web`
- `android`
- `ios`
- `backend`
- `qe`
- `qc`

The platform skill should not restart the whole discovery process from scratch.

Instead, it should build on the earlier artifacts:

- package
- `kh-search-brief`
- `repo-exploration`

## Artifact Chain

The easiest way to think about the prep flow is:

- `package` answers: what needs to be built
- `kh-search-brief` answers: where to look first in the working repo
- `repo-exploration` answers: what the current code actually looks like
- `delta` answers: how the selected platform should implement the change

In other words:

1. `package` defines scope and approved decisions
2. `kh-search-brief` reduces search space
3. `repo-exploration` verifies real code touch points
4. `delta` turns all of that into a platform-specific implementation contract

## Artifact Locations

### In the spec repo

Approved package artifacts live under:

- `features/<feature>/master-spec.md`
- `features/<feature>/knowledge.md`
- `features/<feature>/api-contract.md` when present
- platform deltas under `features/<feature>/deltas/`

### In the working repo

Non-`qc` disposable planning artifacts live under:

- `.planning/<feature>/kh-search-brief.md`
- `.planning/<feature>/repo-exploration.md`

These are working-repo artifacts, not spec-repo artifacts.

That is why `.planning/` should be ignored in the working repo's `.gitignore`.

## Delta Mindset

Delta should not repeat the whole package and should not repeat raw repo exploration.

Delta should:

- translate package scope into platform language
- stay grounded in real repo touch points
- narrow implementation scope
- preserve decisions that the implementer should not have to rediscover
- become the platform-specific implementation contract

Short version:

- package says what to build
- KH says where to look first
- repo exploration says what code really exists
- delta says how this platform should implement it

## Current Migration Constraint

Do not rely on the legacy helper under:

- `~/.platform-spec/`

for this repo's `ship` flow.

That helper may still point to `ct-common-platform-specs` instead of the registered spec repo in `~/.config/platform-spec/platform-spec.yml`.

## Practical Rule Of Thumb

If a later ship phase feels like it is rediscovering the whole repo from scratch, the flow is drifting.

The intended progression is:

1. package
2. KH brief
3. repo exploration
4. delta
5. implementation
