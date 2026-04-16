## Operating Mode

Use **master-spec-first manual quality planning**.

Read the shared package first and generate a reviewable QC delta directly from that baseline. Incorporate platform deltas only as optional enrichment when they already exist and are current.

## Phase 0: Initialization

Read:

- `master-spec.md`
- `api-contract.md`
- `figma.md`
- `open-questions.md`
- available current platform deltas only when present

Validate:

- package revision alignment
- whether any optional platform delta is stale
- whether open questions still affect test scope

## Phase 0.5: Input Readiness

Determine:

- which test scope is already fully derivable from the shared package
- which test scope is still blocked by open questions
- whether any available platform delta adds useful platform-specific implementation detail

If a platform delta is missing:

- do not block QC generation by default
- mark that platform-specific coverage as pending enrichment rather than pretending it is final

If a platform delta is `Stale`:

- do not consume it
- record the affected coverage as blocked or pending enrichment

## Phase 1: Test Scope and Strategy

Clarify:

- critical areas to focus on
- platform priorities
- known risk areas

## Phase 2: Test Infrastructure

Clarify:

- environments
- feature flags
- test data requirements
- external integration dependencies

## Phase 3: Coverage and Priorities

Clarify:

- manual coverage depth and priority distribution
- priority distribution
- cross-platform checks
- E2E journeys that must be covered

## Generation Rules

- QC delta should derive tests from the shared baseline first
- platform deltas are optional enrichment, not mandatory prerequisites for the first QC draft
- consume all known-platform references from `figma.md` for cross-platform coverage planning and skip `unknown` references
- unresolved ambiguity must be raised back to the spec owners
- record `Based On Master Spec Package Version`
- initialize `Delta Status = Draft`
- set `Generated At`
- do not consume platform deltas marked `Stale`
- if platform deltas are missing, keep QC generation going and mark the affected platform-specific coverage as pending enrichment
- if upstream package ambiguity still blocks meaningful QC planning, keep QC delta in `Draft`
- if this delta no longer matches the current package revision, mark `Delta Status = Stale` and fill `Stale Reason`
- if consumed platform deltas later change materially, refresh the QC delta or mark the impacted coverage as stale
- if the package revision changes later, regenerate the QC delta or mark it stale explicitly
- reference requirement IDs, contract expectations, behavior examples, and design states where possible
- prefer traceability and blocked-coverage visibility over long generic test prose

## Output Shape

Recommended sections:

- Metadata / input status
- Readiness summary
- Test strategy overview
- Manual UI test coverage
- Platform-specific coverage enrichment
- Integration / E2E scenarios
- Test data requirements
- Environment and setup
- Blocked or pending coverage
- Traceability matrix
- Open questions / risks

## Success Criteria

- QC can plan validation directly from the delta
- QC can start from the shared baseline without waiting for every platform delta
- platform-specific differences are added when current deltas are available, and explicitly marked as pending when they are not
