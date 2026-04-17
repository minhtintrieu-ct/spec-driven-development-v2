---
name: ship-fetch-kh-context
description: Prepare a package-grounded Knowledge Hub search brief for a non-`qc` ship flow after `repo-sync-baseline` succeeds. Use this to reduce working-repo exploration scope before platform-specific delta work.
---

# Ship Fetch KH Context

Use this skill in the current working repo after `repo-sync-baseline/SKILL.md` and before scoped repo exploration or platform-specific delta work.

## Required Base

Inherit the shared baseline from `../base/SKILL.md` first.

## Required Input

- `feature name`
- `platform`

Require the normalized shared context, registration result, and repo-sync result from the earlier ship baseline skills to already be available to this flow.

## Required Sources

- `~/.config/platform-spec/platform-spec.yml`
- `<spec_repo.path>/features/<feature name>/master-spec.md`
- `<spec_repo.path>/features/<feature name>/knowledge.md`

Optional when present:

- `<spec_repo.path>/features/<feature name>/api-contract.md`

## Goal

Produce one KH-assisted search brief for the current working repo.

This phase exists only to reduce search space before repo exploration. It does not replace repo exploration, does not generate platform delta, and does not create an implementation plan.

Persist the normalized brief to:

- `.planning/<feature>/kh-search-brief.md`

## Required Flow

1. Read `~/.config/platform-spec/platform-spec.yml`.
2. Resolve `spec_repo.path`.
3. Require the current repo to be a non-`qc` working repo.
4. Read the approved package baseline from the registered spec repo:
   - `features/<feature name>/master-spec.md`
   - `features/<feature name>/knowledge.md`
5. If `api-contract.md` exists for the same feature, read it only to extract technical nouns, integration terms, and API-related keywords.
6. Build one compact, package-grounded KH query from:
   - the approved scope and decisions in `master-spec.md`
   - the technical discovery facts in `knowledge.md`
   - the optional contract terms in `api-contract.md`
   - the current `platform`
   - the fact that the target repo is already known and is the current working repo
7. Keep the KH query shape stable across runs. Reuse one fixed template and change only the feature-specific inserts.
8. Ask KH only for search-space reduction hints such as:
   - likely modules or subsystems to inspect first
   - likely touch points in the working repo
   - dependency or integration hints
   - external services this repo may communicate with
   - candidate keywords, class names, config names, or endpoint terms worth searching for
   - related technical flows that may resemble the requested feature
9. Call Knowledge Hub `engineer_query_knowledge` as the default KH tool for this phase.
10. Normalize the KH answer into one search brief for the next phase.
11. Create `.planning/<feature>/` in the current working repo if it does not exist yet.
12. Write the normalized brief to `.planning/<feature>/kh-search-brief.md`.
13. If KH fails, times out, or returns weak guidance, do not stop the ship flow by default. Instead, write a degraded search brief sourced only from package artifacts and mark KH as degraded.
14. Return `result: continue`.

## KH Query Template

Use one stable query template like this:

```text
In <working_repo>, provide search-space reduction hints for <feature_area>.
Focus on:
- <flow_1>
- <flow_2>
- <state_or_event_flow>
- likely modules, components, and keywords to inspect first
- likely file paths or folders if known
- related integration points or dependencies

This is only for repo exploration hints, not source of truth.
```

Keep these parts stable across runs:

- `provide search-space reduction hints`
- the fixed focus bullets about modules, keywords, file paths, and integration points
- `This is only for repo exploration hints, not source of truth.`

Only vary these inserts per feature:

- `<working_repo>`
- `<feature_area>`
- `<flow_1>`
- `<flow_2>`
- `<state_or_event_flow>`

## Output

Return:

```md
- fetch-kh-context: ok|degraded
- feature: PLMO-1328
- platform: web
- output: .planning/PLMO-1328/kh-search-brief.md
- candidate-areas:
  - listing detail module
  - payment adapter
- candidate-keywords:
  - subscription
  - order status
  - checkout payload
- candidate-touch-points:
  - existing order creation flow
  - billing state reducer
- candidate-dependencies:
  - payment service
  - gateway client
- confidence-notes:
  - KH used only for search-space reduction
  - repo exploration must verify all hints against code
- not-source-of-truth: true
- result: continue
```

## Guardrails

- This skill is only for non-`qc` working-repo flows.
- Require `feature name` and `platform`.
- Require `master-spec.md` and `knowledge.md` from the registered spec repo package.
- Use Knowledge Hub only to reduce search space for the next exploration phase.
- Do not use `engineer_find_service` by default here; the target working repo is already known.
- Keep the `engineer_query_knowledge` prompt shape stable across runs. Do not rewrite the whole query style every time.
- Persist only the normalized search brief. Do not persist raw KH dumps or long prompt transcripts.
- Write the brief only into the current working repo under `.planning/<feature>/kh-search-brief.md`.
- Prefer package artifacts over KH when wording or scope differs.
- If KH conflicts with package artifacts, keep the package interpretation and carry the conflict as a note for repo exploration.
- If later repo exploration conflicts with package or KH, the current codebase wins and the delta must record the divergence explicitly.
- Do not generate platform delta here.
- Do not generate an implementation plan or task breakdown here.
- Do not treat KH output as the final source of truth.
