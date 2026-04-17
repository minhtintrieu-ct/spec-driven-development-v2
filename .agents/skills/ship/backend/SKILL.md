---
name: ship-backend
description: Use this skill when `ship` is invoked with `platform = backend` in a backend working repo. It prepares the repo for implementation from the platform spec package while preserving the carried backend delta flow from the older platform layer.
---

# Ship Backend

Use this skill in the current backend working repo when `ship` routes to `platform = backend`.

## Required Base

Inherit the shared baseline from `../base/SKILL.md` first. Do not duplicate generic repo-role or shared-config rules here unless backend needs a stricter version.

## Required Input

- `feature name`

Optional on the first run for this repo:

- `base branch`

## Required References

- `references/command.md`
- `references/template.md`

Use them as the carried contract from the older backend platform layer. Do not restate them loosely from memory.

## Required Flow

1. Ensure onboarding has already been completed in the spec repo and the device-level config exists at `~/.config/platform-spec/platform-spec.yml`.
2. Treat the current working repo as the handoff target by default.
3. If the backend delta is missing or stale, route through the canonical `generate-backend-delta` flow first in the spec repo.
4. Do not hand-author `backend-delta.md` inline from the working-repo flow.
5. Treat the target handoff output as `.planning/<feature>/` in the current working repo.
6. Do not call the legacy global helper under `~/.platform-spec/bin/run-platform-spec-cli.sh` from this repo's ship flow; it may still point to another spec repo.
7. If this repo does not yet own a canonical bundle-preparation path for backend, stop after confirming the package and delta are ready and tell the user the working-repo bundle still needs the repo-local migration.

## Guardrails

- force explicit `feature name`
- require `platform = backend`
- stop if the current repo root matches `spec_repo.path`; backend shipping must run in a working repo, not in the spec repo
- keep the reusable backend delta in the spec repo
- treat `.planning/<feature>/` as the disposable working-repo handoff bundle for backend
- stop instead of calling the legacy `~/.platform-spec` helper from this repo's ship flow
- do not implement service code from the spec repo
- do not invent a `generate-backend-delta` skill
- do not hand-write `backend-delta.md` as an ad-hoc substitute for `generate-backend-delta`
