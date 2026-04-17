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
5. Keep the resulting web delta grounded in this artifact chain:
   - `package`
   - `.planning/<feature>/kh-search-brief.md`
   - `.planning/<feature>/repo-exploration.md`
6. Keep the resulting web delta `clad-ui-first` before preparing the bundle.
7. Treat the web delta as the primary codegen artifact for web implementation.
8. Make the web delta strong enough that implementation AI should start from the delta first and open `.planning/<feature>/repo-exploration.md` or other sources only for narrow detail checks.
9. Do not turn the web delta into the full execution order; that belongs to the later implementation plan when needed.
10. Treat the target handoff output as `.planning/<feature>/` in the current working repo.
11. Do not call the legacy global helper under `~/.platform-spec/bin/run-platform-spec-cli.sh` from this repo's ship flow; it may still point to another spec repo.
12. If this repo does not yet own a canonical bundle-preparation path for web, stop after confirming the package and delta are ready and tell the user the working-repo bundle still needs the repo-local migration.

## Guardrails

- force explicit `feature name`
- require `platform = web`
- stop if the current repo root matches `spec_repo.path`; web shipping must run in a working repo, not in the spec repo
- keep the reusable web delta in the spec repo
- treat `.planning/<feature>/` as the disposable working-repo handoff bundle for web
- stop instead of calling the legacy `~/.platform-spec` helper from this repo's ship flow
- do not implement web code from the spec repo
- treat `kh-search-brief` as hints only and `repo-exploration` as the verified grounding source for the web delta
- require the final web delta to be self-sufficient enough for implementation AI to code from it first
- keep implementation ordering in a later implementation plan instead of encoding it as the main purpose of the web delta
- do not hand off a web delta that silently switches away from `clad-ui`; if the repo lacks it, record the bootstrap or adaptation work explicitly
- do not invent a `generate-web-delta` skill
- do not hand-write `web-delta.md` as an ad-hoc substitute for `generate-web-delta`
