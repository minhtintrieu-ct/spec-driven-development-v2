---
name: ship-resolve-base
description: Resolve the shared ship context from the registered device config before any platform-specific work runs. Use this to classify the current repo and load the matching working-repo registration entry.
---

# Ship Resolve Base

Use this skill first in every `ship` flow.

It only resolves shared context. It does not write config, run git sync, call KH, or generate delta.

## Required Input

- `platform`

## Required Flow

1. Read `~/.config/platform-spec/platform-spec.yml`.
2. Resolve `spec_repo.path`.
3. Resolve the current repo root with `git rev-parse --show-toplevel`.
4. Classify the current repo:
   - `spec repo` when `current_repo_root == spec_repo.path`
   - `working repo` otherwise
5. If the current repo is a working repo, read `working_repos[<current_repo_root>]` when present.
6. Validate shared stop conditions:
   - the config file exists
   - `spec_repo.path` exists
   - `platform` is supported
   - `qc` runs only in the spec repo
   - non-`qc` platforms run only in a working repo
7. Return one normalized shared-context result.

## Output

```md
- resolve-base: ok
- platform: web
- repo-role: working-repo
- spec-repo-path: /absolute/path/to/ct-platform-spec-v2
- current-repo-root: /absolute/path/to/ct-web-uni-chat
- working-repo-entry: found|missing
- result: continue
```

If shared context is invalid:

```md
- resolve-base: blocked
- result: stop
```

## Guardrails

- Read only `~/.config/platform-spec/platform-spec.yml` as the shared config source of truth.
- Do not fetch git state beyond resolving the current repo root.
- Do not write or refresh any registration entry here.
- Do not call KH here.
- Do not inspect package artifacts here.
- Do not route into platform-specific work before this shared context is resolved.
