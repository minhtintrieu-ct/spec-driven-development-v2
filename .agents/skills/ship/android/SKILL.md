---
name: ship-android
description: Use this skill when `ship` is invoked with `platform = android` in an Android working repo. It prepares the repo for implementation from the platform spec package while preserving the carried Android delta flow from the older platform layer.
---

# Ship Android

Use this skill in the current Android working repo when `ship` routes to `platform = android`.

## Required Base

Inherit the shared baseline from `../base/SKILL.md` first. Do not duplicate generic repo-role or shared-config rules here unless Android needs a stricter version.

## Required Input

- `feature name`

Optional on the first run for this repo:

- `base branch`

## Required References

- `references/command.md`
- `references/template.md`

Use them as the carried contract from the older Android platform layer. Do not restate them loosely from memory.

## Required Flow

1. Ensure onboarding has already been completed in the spec repo and the device-level config exists at `~/.config/platform-spec/platform-spec.yml`.
2. Treat the current working repo as the handoff target by default.
3. If the Android delta is missing or stale, route through the canonical `generate-android-delta` flow first in the spec repo.
4. Do not hand-author `android-delta.md` inline from the working-repo flow.
5. Prepare the implementation bundle with the machine helper in the current working repo:
   - `~/.platform-spec/bin/run-platform-spec-cli.sh prepare-implementation --feature <feature> --platform android`
6. Only add `--base-branch <branch>` when the repo has not been registered yet or auto-detect cannot safely choose one.

## Guardrails

- force explicit `feature name`
- require `platform = android`
- stop if the current repo root matches `spec_repo.path`; Android shipping must run in a working repo, not in the spec repo
- keep the reusable Android delta in the spec repo
- do not implement app code from the spec repo
- do not invent a `generate-android-delta` skill
- do not hand-write `android-delta.md` as an ad-hoc substitute for `generate-android-delta`
