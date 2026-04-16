## Operating Mode

Use **baseline-first platform-aware delta generation**.

Read the package first, prefer an existing iOS platform baseline, then inspect only the repo area needed to finalize iOS-specific decisions for this feature.

## Phase 0: Resolve Repo Baseline

1. Read and validate:

- `master-spec.md`
- `api-contract.md`
- `figma.md`

2. Resolve iOS baseline in this order:

- explicit baseline path from input
- a known default baseline artifact in the working repo

3. If a baseline exists:

- use it as the source of truth for repo-wide conventions
- inherit architecture, navigation, design system, networking, analytics, storage, and testing patterns from that baseline
- inspect only the feature-scoped files or modules needed for this delta

4. If no baseline exists:

- interactive runtime must ask the user to choose one of:
  - provide a baseline path
  - scan all to create or refresh a new baseline
- non-interactive runtime must stop and require an explicit choice
- do not default to `scan all`

5. If the user chooses `scan all`:

- treat that scan as a baseline creation or refresh step first
- write or refresh the iOS baseline artifact
- then generate the delta from the refreshed baseline plus the scoped feature context
- do not dump full-repo knowledge directly into `ios-delta.md`

## Phase 0.5: Scoped Repo Inspection

Inspect only the iOS repository context that is relevant to the feature, for example:

- architecture pattern
- module structure
- navigation pattern
- design system usage
- networking pattern
- storage pattern
- analytics integration
- testing setup

## Phase 1: Basics

Clarify only what is still missing:

- feature naming conventions
- Figma source / node references
- iOS minimum version
- baseline path only if not already resolved

## Phase 2: UI Scope

Determine:

- new screens to create
- existing screens to update
- design system components to extend
- custom components required

Use action labels:

- 🆕 Create
- ✏️ Update
- 🔧 Extend
- ♻️ Reuse

## Phase 3: Platform Concerns

Capture:

- deep links
- storage needs
- lifecycle handling
- network considerations
- iOS version constraints
- autofill / keyboard / native affordances

## Phase 4: Dependencies and Constraints

Capture:

- module dependencies
- component dependencies
- testing priorities
- technical constraints and risks

## Generation Rules

- do not duplicate shared business behavior from `master-spec.md`
- do not redefine the shared API contract
- consume only `platform=mobile` references from `figma.md`
- prefer `inherit by reference` from the iOS baseline instead of restating repo-wide conventions
- write the delta as **exception-first**: record only what this feature creates, updates, extends, reuses, or overrides
- if the baseline already defines architecture, navigation, networking, storage, analytics, testing, or naming conventions, do not restate them unless this feature changes or depends on an exception
- reference Figma frame names and node IDs where relevant
- align with the actual iOS project conventions where known
- read `figma.md` carefully before writing any iOS-specific visual guidance
- if `figma.md` already calls out unresolved visual follow-up for an iOS-visible element, do not replace that gap with guessed tokens, spacing, hierarchy, or state layouts
- when the iOS implementation depends on exact component composition, token choice, spacing, hierarchy, or state layout, add a row in `Detailed Figma Context Follow-Up`
- derive `Detailed Figma Context Follow-Up` from `figma.md` baseline references first:
  - `platform=mobile` baseline rows seed the `iOS` follow-up rows
- add extra follow-up rows only when a non-baseline state or component also needs design drill-down
- if a trustworthy node ID exists in `figma.md`, carry that exact node ID into the delta and say implementation must fetch design context or a screenshot from it
- if the available detail is visual but a screenshot is sufficient for downstream implementation context, say so explicitly in the follow-up note
- if no trustworthy node ID exists yet, write `pending from design` in the follow-up section, explain the blocker, and keep the delta in `Draft`
- do not fabricate exact colors, overlay layouts, or state composition when the design source is still unresolved
- record `Based On Master Spec Package Version`
- initialize `Delta Status = Draft`
- set `Generated At`
- if repository context is limited or key decisions stay assumed, keep the delta in `Draft`
- if the baseline source is missing, stale, or still being created, say so explicitly in the delta metadata and risks
- `scan all` is an explicit fallback for baseline creation or refresh, not the default delta path
- if this delta no longer matches the current package revision, mark `Delta Status = Stale` and fill `Stale Reason`
- if the package revision changes later, regenerate the delta or mark it stale explicitly

## Output Shape

Recommended sections:

- Metadata / baseline source
- Inherited repo baseline
- Architecture decisions
- UI design reference
- Screens
- Shared components
- Component variants
- Screen states
- Platform-specific concerns
- Navigation map
- Dependencies
- Testing strategy
- Analytics events
- Localization
- Open questions / risks

## Success Criteria

- iOS-specific decisions are captured without redefining shared behavior
- repo-wide iOS conventions are inherited from a baseline artifact whenever one exists
- the delta focuses on feature-specific exceptions rather than repeating the full repo baseline
- unresolved design detail is surfaced as explicit follow-up or blocker instead of guessed implementation guidance
- the delta is specific enough for planning and implementation
