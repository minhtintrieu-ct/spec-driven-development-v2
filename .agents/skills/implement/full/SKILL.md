---
name: implement-full
description: Use this skill to implement directly from the prepared delta in one pass. It treats the delta as the primary codegen artifact and uses other planning artifacts only as narrow fallback references when needed.
---

# Implement Full

Use this skill in the current working repo when `/implement` routes to `mode = full`.

## Required Input

- `feature name`
- `platform`

Require these artifacts to already exist in the current working repo:

- `.planning/<feature>/`
- the platform delta for the same feature

Optional fallback references:

- `.planning/<feature>/repo-exploration.md`
- `.planning/<feature>/kh-search-brief.md`

## Goal

Implement directly from the delta in one pass.

Treat the delta as the primary codegen artifact. Use the fallback references only for narrow detail checks when the delta points to them explicitly or when a small ambiguity remains.

## Required Flow

1. Read the platform delta first and treat it as the implementation contract.
2. Perform `Delta Intake`:
   - read the full delta end to end
   - treat all current delta sections as the active implementation contract
   - do not hardcode assumptions about a fixed subsection list here
   - use the delta's own structure, decisions, change surface, risks, and fallback references as written
3. Perform `Codegen`:
   - implement directly in the current working repo from the delta
   - follow the delta's change surface and implementation rules
   - keep changes inside the defined scope
4. Perform `Verification`:
   - run the most relevant verification steps that are feasible for the touched code
   - prefer high-signal checks such as tests, lint, and typecheck/build when appropriate
5. Return one final implementation summary.

## Output

Return:

```md
- implement-mode: full
- feature: PLMO-1328
- platform: web
- changed-files:
  - src/components/AppShell/index.tsx
  - src/providers/UnreadCountProvider.tsx
- verification:
  - unit tests: pass|fail|not-run
  - lint: pass|fail|not-run
- unresolved-risks:
  - none
- result: continue|stop
```

## Guardrails

- Start from the delta first.
- Do not generate `implementation-plan.md` in this mode.
- Do not stop after intermediate phases for user confirmation.
- Do not re-resolve context or re-run command-layer existence checks here.
- Use `.planning/<feature>/repo-exploration.md` or `.planning/<feature>/kh-search-brief.md` only as narrow fallback references, not as the main implementation source.
- Do not use fallback references to replace a weak delta globally.
- Do not rediscover the repo broadly from scratch in this mode.
