---
name: ship-web
description: Use this skill when `ship` is invoked with `platform = web` in a web working repo. It prepares the repo for implementation from the platform spec package while preserving the carried web command/template contract from the older platform layer.
---

# Ship Web

Use this skill in the current web working repo when `ship` routes to `platform = web`.

## Required Base

Inherit the shared baseline from `../base/SKILL.md` first. Do not duplicate generic repo-role or shared-config rules here unless web needs a stricter version.

## Required Input

- `feature name`

Optional on the first run for this repo:

- `base branch`

## Required References

- `references/command.md`
- `references/template.md`

Use them as the carried contract from the older web platform layer. Do not restate them loosely from memory.

## Required Flow

1. Ensure onboarding has already been completed in the spec repo and the device-level config exists at `~/.config/platform-spec/platform-spec.yml`.
2. Treat the current working repo as the handoff target by default.
3. If the web delta is missing or stale, route through the canonical `generate-web-delta` flow first in the spec repo.
4. Do not hand-author `web-delta.md` inline from the working-repo flow.
5. Keep the resulting web delta `clad-ui-first` before preparing the bundle.
6. Prepare the implementation bundle with the machine helper in the current working repo:
   - `~/.platform-spec/bin/run-platform-spec-cli.sh prepare-implementation --feature <feature> --platform web`
7. Only add `--base-branch <branch>` when the repo has not been registered yet or auto-detect cannot safely choose one.

## Guardrails

- force explicit `feature name`
- require `platform = web`
- stop if the current repo root matches `spec_repo.path`; web shipping must run in a working repo, not in the spec repo
- keep the reusable web delta in the spec repo
- do not implement web code from the spec repo
- do not hand off a web delta that silently switches away from `clad-ui`; if the repo lacks it, record the bootstrap or adaptation work explicitly
- do not invent a `generate-web-delta` skill
- do not hand-write `web-delta.md` as an ad-hoc substitute for `generate-web-delta`
