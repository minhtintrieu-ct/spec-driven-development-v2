---
name: ship-explore-working-repo
description: Explore the current working repo after `fetch-kh-context` succeeds. Use this to verify KH hints against real code, confirm touch points, and write a feature-scoped repo exploration brief for later ship phases.
---

# Ship Explore Working Repo

Use this skill in the current working repo after `fetch-kh-context/SKILL.md` and before platform-specific delta work.

## Required Input

- `feature name`
- `platform`

Require the earlier ship baseline results plus:

- `.planning/<feature>/kh-search-brief.md`

## Required Sources

- `.planning/<feature>/kh-search-brief.md`
- `<spec_repo.path>/features/<feature name>/master-spec.md`
- `<spec_repo.path>/features/<feature name>/knowledge.md`

Probe `api-contract.md` for the same feature:

- if `features/<feature name>/api-contract.md` exists, read it
- if it does not exist, continue and mark `api_contract_status: missing`
- if `master-spec.md` or `knowledge.md` clearly implies API/backend dependency but `api-contract.md` is absent, mark `api_contract_status: missing-but-expected`

## Goal

Verify package scope and KH hints against the current working repo.

This phase confirms actual touch points in code. It does not generate platform delta and does not implement product code.

Persist the normalized exploration result to:

- `.planning/<feature>/repo-exploration.md`

## Required Flow

1. Read `.planning/<feature>/kh-search-brief.md`.
2. Read `master-spec.md` and `knowledge.md` from the registered spec repo package.
3. Probe `api-contract.md` and classify:
   - `present`
   - `missing`
   - `missing-but-expected`
4. Convert the KH brief into one short search plan:
   - folders or modules to inspect first
   - keywords or symbols to grep first
   - dependencies or integrations to verify first
5. Explore the current working repo with targeted search and file reads only.
6. Confirm actual touch points such as:
   - owning modules
   - relevant pages, components, providers, hooks, atoms, reducers, services, or handlers
   - existing reusable patterns similar to the requested feature
7. Record repo constraints such as:
   - architecture boundaries
   - shared component ownership
   - state management constraints
   - experiment, tracking, socket, or integration requirements
8. Record conflicts:
   - package vs code
   - KH hint vs code
9. Write one normalized exploration brief to `.planning/<feature>/repo-exploration.md`.
10. Return `result: continue`.

## Output

```md
- explore-working-repo: ok
- feature: PLMO-1328
- platform: web
- output: .planning/PLMO-1328/repo-exploration.md
- api-contract-status: present|missing|missing-but-expected
- confirmed-touch-points:
  - src/components/AppShell/index.tsx
  - src/providers/UnreadCountProvider.tsx
- reusable-patterns:
  - existing tooltip visibility state pattern
  - existing tab toggle component
- repo-constraints:
  - lead unread state is atom-driven
  - socket event wiring lives in provider layer
- code-vs-package-conflicts:
  - no conflict found
- recommended-focus-order:
  - app shell toggle
  - unread count state flow
  - tooltip visibility state
- result: continue
```

## Guardrails

- This skill is only for non-`qc` working-repo flows.
- Require `.planning/<feature>/kh-search-brief.md` before exploration starts.
- Always regenerate `.planning/<feature>/repo-exploration.md` for the current feature. Do not reuse an old exploration brief blindly.
- Prefer targeted search over broad repo-wide wandering.
- Treat current code as the source of truth for this phase.
- If KH hints are not confirmed in code, drop them from the confirmed result and keep them only as notes when useful.
- Do not generate platform delta here.
- Do not create an implementation plan here.
- Do not modify product code here.
