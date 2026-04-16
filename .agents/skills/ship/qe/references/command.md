## Operating Mode

Use **automation-repo-aware QE synthesis**.

Read the approved package first, then inspect the automation repository context needed to turn the shared baseline into executable automation scope.

## Phase 0: Initialization

Read and validate:

- `master-spec.md`
- `api-contract.md`
- `figma.md`
- `open-questions.md`

Inspect automation repository context where available for:

- test framework and runner conventions
- suite layout and ownership
- fixture, factory, and mock patterns
- selector, page-object, or screen-model patterns
- API stubbing or network interception patterns
- CI jobs, tags, and reporting conventions
- environment and secret handling

## Phase 1: Automation Scope

Clarify:

- which requirements should be automated now
- which journeys belong in API, UI, or E2E automation
- which areas should stay manual for now

## Phase 2: Automation Design

Capture:

- candidate suites or files to create or extend
- fixtures, mocks, and test data needs
- selector or contract dependencies
- environment dependencies and rollout constraints

## Phase 3: Automation Risks

Capture:

- flakiness risks
- coverage gaps
- blocked automation dependencies
- CI or execution constraints

## Rules

- do not restate the full Master Spec
- do not pretend manual-only scenarios are automated
- classify automation work with action types where relevant:
  - Create
  - Update
  - Extend
  - Reuse
- consume all known-platform references from `figma.md` for automation planning and skip `unknown` references
- record `Based On Master Spec Package Version`
- initialize `Delta Status = Draft`
- set `Generated At`
- if automation repository context is missing or assumptions remain material, keep the delta in `Draft`
- if this delta no longer matches the current package revision, mark `Delta Status = Stale` and fill `Stale Reason`
- if the package revision changes later, regenerate the delta or mark it stale explicitly
- reference requirement IDs, API contract refs, design states, and execution constraints where possible

## Output Shape

Recommended sections:

- metadata / baseline
- automation scope summary
- suite mapping
- API automation plan
- UI or E2E automation plan
- fixtures / mocks / test data
- environment / CI setup
- blocked automation / risks
- traceability matrix

## Success Criteria

- QE can map the approved package into executable automation scope
- the delta is specific enough for automation planning in the working repo
- blocked automation dependencies are explicit instead of hidden in generic prose
