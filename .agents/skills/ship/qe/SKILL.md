---
name: ship-qe
description: Use this skill when `ship` is invoked with `platform = qe` in an automation working repo. It prepares the repo for implementation from the platform spec package while preserving the carried QE automation delta flow from the older platform layer.
---

# Ship QE

Use this skill in the current automation working repo when `ship` routes to `platform = qe`.

## Required Base

Inherit the shared baseline from `../base/SKILL.md` first. Do not duplicate generic repo-role or shared-config rules here unless QE needs a stricter version.

## Required Input

- `feature name`

Optional on the first run for this repo:

- `base branch`

## Required References

- `references/command.md`
- `references/template.md`

Use them as the carried contract from the older QE platform layer. Do not restate them loosely from memory.

## Required Flow

1. Ensure onboarding has already been completed in the spec repo and the device-level config exists at `~/.config/platform-spec/platform-spec.yml`.
2. Treat the current working repo as the handoff target by default.
3. If the QE delta is missing or stale, route through the canonical `generate-qe-delta` flow first in the spec repo.
4. Do not hand-author `qe-delta.md` inline from the working-repo flow.
5. Keep the resulting QE delta automation-repo-aware before preparing the bundle.
6. Prepare the implementation bundle with the machine helper in the current working repo:
   - `~/.platform-spec/bin/run-platform-spec-cli.sh prepare-implementation --feature <feature> --platform qe`
7. Only add `--base-branch <branch>` when the repo has not been registered yet or auto-detect cannot safely choose one.

## Guardrails

- force explicit `feature name`
- require `platform = qe`
- stop if the current repo root matches `spec_repo.path`; QE shipping must run in a working repo, not in the spec repo
- keep the reusable QE delta in the spec repo
- do not implement automation code from the spec repo
- do not hand off a QE delta that hides missing fixtures, environments, or automation scope gaps
- do not invent a `generate-qe-delta` skill
- do not hand-write `qe-delta.md` as an ad-hoc substitute for `generate-qe-delta`
