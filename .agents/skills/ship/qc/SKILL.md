---
name: ship-qc
description: Use this skill when `ship` is invoked with `platform = qc` in the spec repo. It generates or refreshes the QC manual-quality delta directly from the approved package and does not prepare a working-repo handoff bundle.
---

# Ship QC

Use this skill in the current spec repo when `ship` routes to `platform = qc`.

## Required Base

Inherit the shared baseline from `../base/SKILL.md` first. QC is the only platform allowed to stay in the spec repo.

## Required Input

- `feature name`

## Required References

- `references/command.md`
- `references/template.md`

Use them as the carried contract from the older QC platform layer. Do not restate them loosely from memory.

## Required Flow

1. Ensure onboarding has already been completed in the spec repo and the device-level config exists at `~/.config/platform-spec/platform-spec.yml`.
2. Stay in the spec repo; do not switch into a working repo for QC.
3. Write QC artifacts under `TCs/<JIRA_ID>/` in the spec repo.
4. When the QC delta must be generated or refreshed, route through the canonical `generate-qc-delta` flow.
5. Treat the refreshed QC files under `TCs/<JIRA_ID>/` as the current manual-quality planning artifacts for that feature.

## Guardrails

- force explicit `feature name`
- require `platform = qc`
- require the current repo root to match `spec_repo.path`; QC must run in the spec repo
- keep the reusable QC delta in the spec repo
- write QC artifacts under `TCs/<JIRA_ID>/`; do not write them into `.planning/` or a working repo
- do not treat QC output as a `.planning/<feature>/` handoff bundle
- treat QC as manual-quality planning, not automation implementation
- do not prepare `.planning/<feature>/` handoff bundles for QC
- do not invent a `generate-qc-delta` skill
- do not hand-write `qc-delta.md` as an ad-hoc substitute for `generate-qc-delta`
