---
name: ship
description: Use this skill from any repo to prepare implementation against the registered platform spec repo. It reads `~/.config/platform-spec/platform-spec.yml` to locate the spec repo, then routes to the selected platform skill under `.agents/skills/ship/`.
---

# Ship

Use this skill as the canonical shipping router.

## Required Base

Read `base/SKILL.md` first. Treat it as the shared baseline for every platform under `ship/`.

## Required Input

- `feature name`
- `platform`: `web` | `android` | `ios` | `backend` | `qc` | `qe`

Optional on the first run for this repo:

- `base branch`

## Workflow

1. Use `base/SKILL.md` to resolve shared config, classify the current repo, and apply shared stop conditions.
2. Route by `platform`.
3. If `platform = web`, read `web/SKILL.md`.
4. If `platform = android`, read `android/SKILL.md`.
5. If `platform = ios`, read `ios/SKILL.md`.
6. If `platform = backend`, read `backend/SKILL.md`.
7. If `platform = qc`, read `qc/SKILL.md`.
8. If `platform = qe`, read `qe/SKILL.md`.
9. If another platform is requested and no skill exists yet, return `result: stop`.
10. If the selected platform skill returns `result: stop`, stop the flow.

## Guardrails

- This router owns the shipping entrypoint. Do not keep a separate router in parallel.
- `base/SKILL.md` owns the shared baseline. Platform-specific skills should add only their own delta rules.
