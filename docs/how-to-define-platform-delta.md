# How To Define Platform Delta

This guide explains how to write a platform delta that matches the current workflow.

Use it when:

- you want to write a delta manually
- you want AI to help draft a delta
- you want the delta to work cleanly with `/implement`

## Short Version

A good platform delta should answer one question:

`Given the approved package and the real codebase, how should this platform implement the feature?`

In the current workflow:

- `package` says what the feature needs
- `kh-search-brief` says where to look first
- `repo-exploration` says what the real codebase looks like
- `delta` says how this platform should implement it

## What A Delta Is

A platform delta is the platform-specific implementation contract for one feature.

It should tell the next implementer:

- what is in scope for this platform
- what parts of the repo actually matter
- what patterns must be reused
- what decisions must be followed
- what risks are still open

A delta is not:

- a copy of the package
- raw KH output
- raw repo exploration notes
- a full execution plan

## The Inputs

Every strong delta should be built from:

- `features/<JIRA_ID>/master-spec.md`
- `features/<JIRA_ID>/knowledge.md`
- `features/<JIRA_ID>/api-contract.md` when present
- `.planning/<feature>/kh-search-brief.md`
- `.planning/<feature>/repo-exploration.md`

Use them in this order:

1. package first
2. KH hints second
3. repo exploration third
4. delta last

The package sets scope.

KH narrows the search space.

Repo exploration confirms repo reality.

The delta turns all of that into platform implementation decisions.

## The Build Flow

### 1. Start From The Package

Read the approved package and lock:

- in-scope behavior
- out-of-scope behavior
- business rules that affect the platform

Do not add scope from memory or assumption.

### 2. Read KH As Hints Only

Use `kh-search-brief.md` to understand:

- likely modules
- useful keywords
- likely integrations

Do not treat KH as source of truth.

### 3. Ground On Repo Exploration

Use `repo-exploration.md` to confirm:

- real touch points
- reusable patterns
- repo constraints
- package vs code conflicts

This is the strongest repo-facing input for the delta.

### 4. Write Platform Decisions

Now convert those inputs into decisions for the selected platform.

At minimum, the delta should clarify:

- which surfaces are affected
- what should be reused
- what should be extended
- what must be created
- what behavior must be preserved
- what boundaries must not be crossed

### 5. Make It Ready For Implement

Before calling the delta done, ask:

- can `/implement full` start from this delta first
- can `/implement multi` phase work from this delta cleanly

If not, the delta is still too weak.

## A Simple Delta Skeleton

Use this as the base structure:

1. `Overview`
2. `Platform Scope`
3. `Verified Repo Reality`
4. `Foundation Strategy`
5. `Implementation Decisions`
6. `Behavior`
7. `Change Surface`
8. `Risks And Gaps`
9. `Implementation Handoff`

The section names can change slightly by platform, but the meaning should stay the same.

## What Each Section Should Do

### Overview

Keep it factual.

Include:

- feature
- platform
- delta status
- target repo
- source artifacts used

### Platform Scope

State:

- in scope
- out of scope
- assumptions

This section protects the implementation from scope creep.

### Verified Repo Reality

Only write what was actually confirmed in the current repo.

Include:

- confirmed touch points
- reusable patterns
- repo constraints
- code vs package conflicts

### Foundation Strategy

Explain what the platform should reuse before creating new work.

Examples:

- web: design system and shared UI components
- mobile: shared UI foundation and screen conventions
- backend: shared service patterns and libraries
- QE: shared automation framework
- QC: manual testing conventions

### Implementation Decisions

This is the heart of the delta.

Capture:

- ownership decisions
- reuse vs extend vs create
- state and data decisions
- persistence, analytics, experiment, or realtime behavior
- boundaries that must be respected

### Behavior

Describe the behavior that matters at runtime.

Examples:

- loading, empty, and error states
- navigation transitions
- screen behavior
- service behavior
- test coverage behavior

### Change Surface

Make the implementation footprint concrete.

Include:

- likely files or modules to change
- likely files or modules to add
- expected edit shape
- explicit non-goals

### Risks And Gaps

Keep uncertainty visible.

Each risk should say:

- what the issue is
- impact
- whether it blocks implementation

### Implementation Handoff

This section helps `/implement`.

Include:

- recommended implementation order
- verification focus
- blockers or dependencies
- narrow fallback references

Do not turn this into a full phased plan unless the workflow needs that later.

## Platform Notes

### Web

Web deltas should be especially clear about:

- route or shell ownership
- component reuse vs extension vs creation
- design system constraints
- state and data patterns
- tracking, experiment, socket, and persistence decisions
- desktop and mobile behavior when relevant

### Android And iOS

Mobile deltas should be especially clear about:

- screen ownership
- navigation flow
- shared UI foundation
- state and data flow choices
- local persistence, analytics, and experiment boundaries

### Backend

Backend deltas should be especially clear about:

- service ownership
- endpoint or event surface
- shared service conventions
- validation and authorization boundaries
- data and side-effect behavior

### QE And QC

Quality deltas should be especially clear about:

- coverage intent
- test or validation boundaries
- dependencies and environments
- evidence or reporting expectations

## A Quick Quality Check

Before marking a delta ready, check:

- does it match the approved package
- does it use verified repo facts
- does it make platform decisions explicit
- does it narrow the likely implementation surface
- does it expose real risks
- can implementation start from it directly

If yes, the delta is probably strong enough.

---

## AI Guidance

This section is for AI-assisted drafting or for humans who want stricter rules.

## How AI Should Use The Inputs

AI should treat the inputs differently:

- `package` = scope baseline
- `kh-search-brief` = search hints only
- `repo-exploration` = verified repo grounding
- `delta` = platform implementation contract

AI should not:

- write a delta from package only
- treat KH as fact
- dump repo exploration into the delta
- invent architecture without repo evidence

## A Good AI Workflow

1. Read `master-spec.md`
2. Read `knowledge.md`
3. Read `api-contract.md` when present
4. Read `kh-search-brief.md`
5. Read `repo-exploration.md`
6. Draft a platform delta from verified inputs
7. Strengthen the delta until implementation can start from it first

## AI Prompt Pattern

Use a prompt shape like this:

```text
Build a <platform> delta for feature <JIRA_ID>.

Use these inputs in order:
- master-spec.md
- knowledge.md
- api-contract.md when present
- .planning/<feature>/kh-search-brief.md
- .planning/<feature>/repo-exploration.md

Rules:
- Treat the package as the feature scope baseline.
- Treat KH as search hints only.
- Treat repo-exploration as the verified code grounding.
- Write the delta as a platform-specific implementation contract.
- Do not restate the whole package.
- Do not dump repo exploration notes.
- Make the delta strong enough that implementation starts from the delta first.
```

Then add platform-specific instructions.

Example for web:

```text
For web:
- keep it design-system-first
- make route, shell, component, state, data, tracking, realtime, and persistence decisions explicit
- make likely file changes concrete
```

## Common Failure Modes

### The Delta Just Repeats The Package

Fix:

- translate scope into platform decisions

### The Delta Is Just Repo Notes

Fix:

- keep only findings that affect implementation

### The Delta Is Too Vague For Codegen

Fix:

- strengthen `Implementation Decisions` and `Change Surface`

### KH Hints Leak In As Facts

Fix:

- only carry forward what repo exploration verified

### The Delta Becomes A Full Plan

Fix:

- keep it as an implementation contract
- let phased execution belong to `/implement multi`
