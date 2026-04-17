## Operating Mode

Use **scope-first repository-aware mode**.

That means the AI should identify the most likely app, route, module, similar pages, and reusable components first, then inspect only the repo area needed to complete the delta.

## Phase 0: Scoped Discovery

Before asking questions, automatically identify the smallest relevant scope possible:

- target app or package
- route group or feature module
- similar pages, components, and flows already in the repo
- shared UI / design system components likely to be reused
- data layer or query hooks likely to be reused

Do not scan the full web repo by default.

## Phase 0.5: Repository Exploration Within Scope

Once the scope is identified, inspect only that scoped area and the minimum shared dependencies it relies on:

- project structure
- framework and version
- routing model
- UI library / design system usage
- state management patterns
- data fetching patterns
- styling conventions
- testing setup
- similar pages, components, and flows already in the repo

Discovery checklist:

- project type: Next.js App Router / Pages Router / Vite / Nuxt / other
- route structure
- existing `clad-ui` components and wrappers that can be reused
- similar pages that should be referenced
- API hook / query patterns
- SEO metadata patterns
- loading / error state patterns
- styling system and breakpoints

The AI must present exploration findings before generation.

## Phase 0.75: Baseline Fallback For Large Repos

If the web repo is a large monorepo, multi-app workspace, or the scoped discovery still cannot identify stable conventions:

- prefer an existing web platform baseline artifact if one exists
- use the explicit baseline path from input if provided

If no baseline exists and scoped discovery is still insufficient:

- interactive runtime must ask the user to choose one of:
  - provide a baseline path
  - scan all to create or refresh a new baseline
- non-interactive runtime must stop and require an explicit choice
- do not default to `scan all`

If the user chooses `scan all`:

- treat that scan as a baseline creation or refresh step first
- write or refresh the web baseline artifact
- then generate the delta from the refreshed baseline plus scoped feature context
- do not dump full-repo knowledge directly into `web-delta.md`

## Phase 1: Feature Basics

Only ask what the repo and package do not already answer:

- feature slug or route naming decisions
- Figma source if not already pinned clearly
- pages and rendering strategy that still need confirmation
- baseline path only if scoped discovery and auto-discovery are still insufficient

## Phase 2: Pages and Components

Gather and decide:

- pages or routes to create or update
- rendering strategy per page
- components to reuse, extend, or create
- how to implement the feature through `clad-ui` first, then local customization only where needed
- responsive priorities
- accessibility-critical areas

Use action labels:

- 🆕 Create
- ✏️ Update
- 🔧 Extend
- ♻️ Reuse

## Phase 3: Data and Performance

Gather and decide:

- API integration pattern
- caching / query strategy
- state management choices
- loading / error / empty states
- performance budgets
- SEO requirements
- analytics / tracking points

## Phase 3.5: ECS Tracking Adaptation

When the package includes telemetry expectations for web, adapt them to the working repo's ECS setup instead of inventing a generic analytics implementation.

Discovery checklist for ECS:

- whether the repo already has `.ecsrc`
- whether the repo already depends on `@carousell/ecs-cli` and `@carousell/ecs-template-ct`
- whether generated helpers already live under a path such as `src/helpers/tracking/`
- whether `ecslib.ts` or equivalent generated ECS library already exists
- where `setClientInfo(...)` is initialized early in the app or layout
- which generated feature module already matches the target surface, if any

Delivery rules for ECS:

- if `api-contract.md` marks a telemetry item as `Ready`, record the target `feature`, `event_name`, and trigger point in the delta
- if `api-contract.md` marks a telemetry item as `Waiting for event`, keep it as a follow-up only
- do not auto-generate, auto-confirm, or invent ECS events from the delta
- do not expand `Waiting for event` into fake payload fields just to make the delta look complete
- when ECS is already set up in the repo, the delta should say how to reuse the existing `.ecsrc`, generated tracking helpers, and `setClientInfo(...)` path
- when ECS is not set up in the repo, record the bootstrap work explicitly:
  - package install
  - `.ecsrc`
  - generated output path
  - early `setClientInfo(...)` initialization

Implementation guidance for web repos that already use ECS:

- prefer feature-scoped ECS generation instead of hand-written tracking wrappers
- record the ECS `feature` value explicitly so the web team can run the repo's generation command for that feature
- record only the event names and trigger points needed by the UI flow
- leave ECS payload specifics to the generated helpers and the event schema owned by Product / Data
- if the repo already has a generated feature module that should absorb the new events, record that reuse target explicitly
- if a new generated feature module is needed, record the intended module path only; do not fabricate the generated code in the delta

## Generation Rules

- do not restate the shared behavior already defined in `master-spec.md`
- do not copy `api-contract.md` verbatim
- treat the web delta as the output of this artifact chain:
  - `package`: what needs to be built
  - `kh-search-brief`: where to look first in the working repo
  - `repo-exploration`: what the current code actually looks like
  - `web-delta`: how web should implement the change
- require `.planning/<feature>/kh-search-brief.md` and `.planning/<feature>/repo-exploration.md` as grounding inputs before writing the final web delta
- use `kh-search-brief` only as scoped hints; do not promote KH hints into facts unless repo exploration confirmed them
- treat `repo-exploration.md` as the main grounding input for web touch points, reusable patterns, constraints, and conflicts
- make `web-delta.md` strong enough that implementation AI should start from the delta first and open `repo-exploration.md` or other source artifacts only for narrow detail checks
- consume all known-platform references from `figma.md`; treat `platform=web` as the canonical web baseline and use `platform=mobile` as supporting context only when useful
- structure the web delta in two delivery phases:
  - `Msite`, derived from `platform=mobile` baseline references in `figma.md`
  - `Desktop`, derived from `platform=web` baseline references in `figma.md`
- do not collapse Msite and Desktop into one mixed UI plan when their design references differ
- use scoped repository findings to avoid duplicating existing pages or components
- prefer `inherit by reference` from a web baseline when a baseline artifact exists
- write the delta as **exception-first**: record only what this feature creates, updates, extends, reuses, or overrides
- treat `clad-ui` as the required default design-system strategy for web implementation
- prefer `clad-ui` components, wrappers, and tokens before introducing new primitives
- when feature-specific UI is needed, implement it as local customization on top of `clad-ui` instead of switching to another component library
- if the working repo does not currently expose `clad-ui`, record the gap explicitly and add the required bootstrap or adaptation work instead of silently selecting another UI library
- if scoped discovery already identifies the relevant route, components, and patterns, do not widen exploration to the full repo
- if a baseline already defines framework, routing, state, data fetching, styling, analytics, testing, or naming conventions, do not restate them unless this feature changes or depends on an exception
- reference Figma frames and node IDs where relevant
- align with the actual working repo conventions, not hardcoded defaults
- keep the delta implementation-facing:
  - narrow scope
  - preserve web-specific decisions
  - make likely change areas explicit
  - leave execution ordering to the later implementation plan
- when the repo exploration confirmed likely file or module targets, record them explicitly in the delta instead of leaving change surface abstract
- when reuse / extend / create is known for an important web surface, say it explicitly instead of leaving it as an implied choice
- when a risk or gap remains, classify its implementation impact clearly:
  - `Safe`: implementation can continue normally
  - `Caution`: implementation can continue but should keep the gap visible
  - `Stop`: implementation should not proceed until the blocker is resolved
- do not rely on later implementation AI to rediscover hidden boundaries such as:
  - shared-module ownership
  - state ownership boundaries
  - tracking integration constraints
  - styling or token constraints
- treat ECS as the canonical web tracking path when the repo already uses it; do not silently switch to GTM-only or custom event wrappers for ECS-scoped events
- if telemetry items are `Waiting for event`, keep them visible as follow-up work instead of pretending they are implementation-ready
- read `figma.md` carefully before writing any web-specific visual guidance
- if `figma.md` already calls out unresolved visual follow-up for a web-visible element, do not replace that gap with guessed CSS tokens, placeholder colors, or assumed state layouts
- when the web implementation depends on exact tooltip composition, token choice, spacing, hierarchy, or state layout, add a row in `Detailed Figma Context Follow-Up`
- derive `Detailed Figma Context Follow-Up` from `figma.md` baseline references first:
  - `platform=mobile` baseline rows seed the `Msite` phase
  - `platform=web` baseline rows seed the `Desktop` phase
- add extra follow-up rows only when a non-baseline state or component also needs design drill-down
- if a trustworthy node ID exists in `figma.md`, carry that exact node ID into the delta and say implementation must fetch design context from it
- if the available detail is visual but a screenshot is sufficient for downstream implementation context, say so explicitly instead of forcing full design-context retrieval
- if no trustworthy node ID exists yet, write `pending from design` in the follow-up section, explain the blocker, and keep the delta in `Draft`
- do not fabricate exact hex values, final tooltip layout, or platform-specific state composition when the design source is still unresolved
- record `Based On Master Spec Package Version`
- initialize `Delta Status = Draft`
- set `Generated At`
- if working repo context is missing or key decisions stay assumed, keep the delta in `Draft`
- if the delta still requires broad reading of `repo-exploration.md` just to know where to start coding, keep the delta in `Draft`
- set `Delta Status = Ready` only when the delta is self-sufficient enough for implementation AI to begin coding from the delta itself, using fallback references only for narrow detail checks
- if baseline or scope resolution is incomplete, say so explicitly in the delta metadata and risks
- `scan all` is an explicit fallback for baseline creation or refresh, not the default delta path
- if this delta no longer matches the current package revision, mark `Delta Status = Stale` and fill `Stale Reason`
- if the package revision changes later, regenerate the delta or mark it stale explicitly

## Output Shape

The resulting `web-delta.md` should be structured, implementation-oriented, and scoped only to web-specific decisions.

Recommended sections:

- Overview
- Web Scope
- Verified Repo Reality
- Design System Strategy
- Implementation Decisions
- UI Behavior
- Change Surface
- Risks And Gaps
- Implementation Handoff

## Success Criteria

- web-specific decisions are explicit
- repo-aware recommendations are grounded in the actual codebase
- the delta uses `repo-exploration.md` as verified grounding instead of re-running broad discovery inside the delta itself
- the delta focuses on feature-specific exceptions rather than repeating the full web repo baseline
- the likely file and module change surface is explicit enough for implementation AI to know where to start
- major reuse / extend / create decisions are explicit for the important web surfaces
- fallback references are narrow and optional, not a substitute for missing delta content
- full-repo scanning happens only when explicitly chosen as a baseline refresh fallback
- unresolved design detail is surfaced as explicit follow-up or blocker instead of guessed implementation guidance
- Msite and Desktop implementation guidance stay clearly separated while sharing one web delta
- the delta can be used directly by planning/execution tools like GSD
