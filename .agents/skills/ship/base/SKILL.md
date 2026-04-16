---
name: ship-base
description: Shared baseline for all `ship/*` platform skills. Use this for common preflight, repo-role guardrails, shared config resolution, and generic stop conditions before applying platform-specific logic.
---

# Ship Base

Use this skill as the shared baseline for every platform under `ship/`.

Use it to centralize common preflight, repo-role classification, and generic stop conditions before applying platform-specific logic.

Do not use this as a standalone platform entrypoint.

## Shared Preflight

1. Read `~/.config/platform-spec/platform-spec.yml`.
2. Resolve `spec_repo.path`.
3. Resolve the current repo root.
4. Classify the current repo:
   - if current repo root matches `spec_repo.path`, it is the spec repo
   - otherwise, it is a working repo
5. Require the selected platform skill to use this classification before doing platform-specific work.

## Shared Repo Guardrails

- All non-`qc` platforms must run in a working repo.
- `qc` is the only platform allowed to run directly in the spec repo.
- Always use the registered `spec_repo.path`; do not guess a spec repo from the current working directory.
- If `~/.config/platform-spec/platform-spec.yml` is missing, incomplete, or points to a missing repo, return `result: stop`.
- If the selected platform skill does not exist yet in the registered spec repo, return `result: stop`.

## Shared Delivery Rules

- Keep reusable platform deltas in the spec repo.
- Do not implement product code from the spec repo for non-`qc` platforms.
- Reuse the approved package from the registered spec repo instead of regenerating shared package artifacts inline from the working repo flow.
- Let platform-specific skills add only their own delta rules on top of this base.
