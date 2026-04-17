---
name: ship-base
description: Shared baseline for all `ship/*` platform skills. Use this for common preflight, repo-role guardrails, shared config resolution, and generic stop conditions before applying platform-specific logic.
---

# Ship Base

Use this skill as the thin orchestrator for the shared `ship` baseline.

Do not use this as a standalone platform entrypoint.

## Execution Flow

1. Run `resolve-base/SKILL.md` first.
2. If `platform != qc`, run `base-registration/SKILL.md` next.
3. If `platform != qc`, run `repo-sync-baseline/SKILL.md` after registration succeeds.
4. If `platform != qc`, run `fetch-kh-context/SKILL.md` after repo sync baseline succeeds.
5. If `platform != qc`, run `explore-working-repo/SKILL.md` after KH context succeeds.
6. Pass the normalized shared context, registration result, repo-sync result, KH search brief, and repo exploration brief down to the later ship phases.

## Shared Repo Guardrails

- All non-`qc` platforms must run in a working repo.
- `qc` is the only platform allowed to run directly in the spec repo.
- Treat `resolve-base/SKILL.md` as the canonical place for shared config resolution and repo-role classification.
- Treat `base-registration/SKILL.md` as the canonical place for working-repo registration.
- Treat `repo-sync-baseline/SKILL.md` as the canonical place for repo sync baseline.
- Treat `fetch-kh-context/SKILL.md` as the canonical place for KH-assisted search-space reduction before later ship phases continue.
- Treat `explore-working-repo/SKILL.md` as the canonical place for confirming real code touch points before later ship phases continue.

## Shared Delivery Rules

- Keep this skill thin.
- Stop at the first shared step that returns `result: stop`.
- Let later phases add KH, repo exploration, delta, and handoff rules separately.
