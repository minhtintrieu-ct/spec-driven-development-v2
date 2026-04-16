## Operating Mode

Use **baseline-first repository-aware mode**.

Prefer an existing Android platform baseline first, then inspect only the scoped repo area needed to complete the delta.

## Phase 0: Resolve Repo Baseline

1. Read and validate:

- `master-spec.md`
- `api-contract.md`
- `figma.md`

2. Resolve Android baseline in this order:

- explicit baseline path from input
- a known default baseline artifact in the working repo

3. If a baseline exists:

- use it as the source of truth for repo-wide conventions
- inherit architecture, module boundaries, navigation, DI, storage, networking, design system, analytics, and testing patterns from that baseline
- inspect only the feature-scoped files or modules needed for this delta

4. If no baseline exists:

- interactive runtime must ask the user to choose one of:
  - provide a baseline path
  - scan all to create or refresh a new baseline
- non-interactive runtime must stop and require an explicit choice
- do not default to `scan all`

5. If the user chooses `scan all`:

- treat that scan as a baseline creation or refresh step first
- write or refresh the Android baseline artifact
- then generate the delta from the refreshed baseline plus the scoped feature context
- do not dump full-repo knowledge directly into `android-delta.md`

## Phase 0.5: Scoped Repository Exploration

Before asking questions, inspect only the repo area relevant to the feature, for example:

- feature module structure
- design system components
- similar features or screens
- storage patterns
- network patterns
- navigation patterns
- DI patterns
- testing patterns

Discovery checklist:

- reusable UI components
- existing feature modules to reference
- storage approach: DataStore / SharedPreferences / Room
- repository / use case patterns
- navigation graph patterns
- module boundaries
- API level constraints already present in the app

## Phase 1: Feature Basics

Clarify:

- feature naming conventions
- Figma source / node references
- minimum Android API level
- baseline path only if not already resolved

## Phase 2: UI Scope

Determine:

- screens to create or update
- design system components to reuse or extend
- custom UI components required

Use action labels:

- 🆕 Create
- ✏️ Update
- 🔧 Extend
- ♻️ Reuse

## Phase 3: Platform and Dependencies

Capture:

- deep links
- storage requirements
- lifecycle handling
- background work
- module dependencies
- API-level constraints
- testing priorities

## Generation Rules

- do not duplicate shared business behavior from `master-spec.md`
- do not redefine the shared API contract
- consume only `platform=mobile` references from `figma.md`
- prefer `inherit by reference` from the Android baseline instead of restating repo-wide conventions
- write the delta as **exception-first**: record only what this feature creates, updates, extends, reuses, or overrides
- if the baseline already defines architecture, navigation, DI, storage, networking, analytics, testing, or naming conventions, do not restate them unless this feature changes or depends on an exception
- reference Figma frame names and node IDs where relevant
- align with actual Android repository conventions
- read `figma.md` carefully before writing any Android-specific visual guidance
- if `figma.md` already calls out unresolved visual follow-up for an Android-visible element, do not replace that gap with guessed tokens, spacing, hierarchy, or state layouts
- when the Android implementation depends on exact component composition, token choice, spacing, hierarchy, or state layout, add a row in `Detailed Figma Context Follow-Up`
- derive `Detailed Figma Context Follow-Up` from `figma.md` baseline references first:
  - `platform=mobile` baseline rows seed the `Android` follow-up rows
- add extra follow-up rows only when a non-baseline state or component also needs design drill-down
- if a trustworthy node ID exists in `figma.md`, carry that exact node ID into the delta and say implementation must fetch design context or a screenshot from it
- if the available detail is visual but a screenshot is sufficient for downstream implementation context, say so explicitly in the follow-up note
- if no trustworthy node ID exists yet, write `pending from design` in the follow-up section, explain the blocker, and keep the delta in `Draft`
- do not fabricate exact colors, overlay layouts, or state composition when the design source is still unresolved
- record `Based On Master Spec Package Version`
- initialize `Delta Status = Draft`
- set `Generated At`
- if working repo context is missing or key decisions stay assumed, keep the delta in `Draft`
- if the baseline source is missing, stale, or still being created, say so explicitly in the delta metadata and risks
- `scan all` is an explicit fallback for baseline creation or refresh, not the default delta path
- if this delta no longer matches the current package revision, mark `Delta Status = Stale` and fill `Stale Reason`
- if the package revision changes later, regenerate the delta or mark it stale explicitly

## Output Shape

Recommended sections:

- Metadata / baseline source
- Inherited repo baseline
- Architecture decisions
- Module structure
- UI design reference
- Screens
- Component usage
- Component variants
- Screen states
- Platform-specific concerns
- Navigation map
- Dependencies
- Testing strategy
- Analytics events
- Localization
- Implementation checklist
- Open questions / risks

## Success Criteria

- Android-specific decisions are explicit and grounded in the working repo
- repo-wide Android conventions are inherited from a baseline artifact whenever one exists
- the delta focuses on feature-specific exceptions rather than repeating the full repo baseline
- unresolved design detail is surfaced as explicit follow-up or blocker instead of guessed implementation guidance
- the delta is specific enough for planning and implementation
