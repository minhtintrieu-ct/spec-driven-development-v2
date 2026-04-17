## 1. Overview

Use this section to anchor the web delta to the exact inputs it was generated from.

| Item | Value | Source / Reference |
|------|-------|--------------------|
| Feature |  |  |
| Platform | `web` |  |
| Based On Master Spec Package Version |  | `master-spec.md` |
| Delta Status | `Draft` / `Ready` / `Stale` |  |
| Generated At |  |  |
| Working Repo Target |  |  |
| Source Artifacts Used | `master-spec.md`, `knowledge.md`, `api-contract.md` when present, `.planning/<feature>/kh-search-brief.md`, `.planning/<feature>/repo-exploration.md` |  |

Keep this section factual. Do not add interpretation here.

## 2. Web Scope

Capture only the web-specific scope.

### 2.1 In Scope

- 

### 2.2 Out Of Scope

- 

### 2.3 Assumptions

- 

## 3. Verified Repo Reality

Write only what was confirmed from the current working repo.

### 3.1 Confirmed Touch Points

| Area | Confirmed Target | Why It Matters | Reference |
|------|------------------|----------------|-----------|
| Route / Surface |  |  |  |
| Page / Layout / Shell |  |  |  |
| Component / Provider / Hook / Atom |  |  |  |
| Data / Query Layer |  |  |  |

### 3.2 Reusable Patterns

| Pattern | Reuse Target | Reason | Reference |
|---------|--------------|--------|-----------|
|  |  |  |  |

### 3.3 Repo Constraints

| Constraint | Impact On Web Implementation | Reference |
|------------|-------------------------------|-----------|
|  |  |  |

### 3.4 Code vs Package Conflicts

| Conflict | Current Code Reality | Delta Decision | Reference |
|----------|----------------------|----------------|-----------|
|  |  |  |  |

If no conflict was found, say that explicitly instead of leaving the section ambiguous.

## 4. Design System Strategy

Use this section to explain how web should reuse the repo's UI foundation before custom work.

| Decision | Choice | Rationale | Reference |
|----------|--------|-----------|-----------|
| Design System Strategy | `clad-ui-first` | Reuse or extend `clad-ui` first; apply local customization only when needed |  |
| Reuse Target Components |  |  |  |
| Extend Target Components |  |  |  |
| Net New UI Needed |  |  |  |
| Token / Styling Constraints |  |  |  |
| Bootstrap / Adaptation Gap |  |  |  |

If the repo does not currently expose `clad-ui`, record the required bootstrap or adaptation work explicitly instead of switching to another component library.

## 5. Implementation Decisions

This is the core decision section of the delta.

| Decision | Choice | Rationale | Reference |
|----------|--------|-----------|-----------|
| Route / Surface |  |  |  |
| Rendering Strategy |  |  |  |
| Page / Layout Ownership |  |  |  |
| Component Strategy | `Reuse` / `Extend` / `Create` summary |  |  |
| Data Layer Pattern |  |  |  |
| Local State Pattern |  |  |  |
| Error Handling Pattern |  |  |  |
| Loading Pattern |  |  |  |
| Tracking / Experiment Strategy |  |  |  |
| Socket / Realtime Strategy |  |  |  |
| Local Storage / Persistence Strategy |  |  |  |

Write only feature-specific decisions or exceptions here. Do not restate repo-wide defaults unless this feature changes or depends on an exception.

### 5.1 Implementation Rules

Use this section to make code generation safer and less ambiguous.

| Rule Area | Rule / Decision | Why It Matters | Reference |
|-----------|-----------------|----------------|-----------|
| Reuse Before Create |  |  |  |
| Extension Boundary |  |  |  |
| State Ownership Boundary |  |  |  |
| API Consumption Boundary |  |  |  |
| Tracking / Telemetry Boundary |  |  |  |
| Styling / Token Boundary |  |  |  |
| Do Not Change |  |  |  |

If a later implementation AI should avoid touching a specific shared module or cross-cutting abstraction, say that explicitly here.

## 6. UI Behavior

Use this section to capture actual web behavior, not just structure.

### 6.1 Delivery Phases

Split the web implementation into two explicit phases.

- `Msite` is derived from `platform=mobile` baseline references in `figma.md`
- `Desktop` is derived from `platform=web` baseline references in `figma.md`

#### Msite

| Area | Decision | Reference |
|------|----------|-----------|
| Canonical Design Source | `platform=mobile` baseline refs from `figma.md` |  |
| Route / Surface |  |  |
| UI Scope | Create / Update / Reuse |  |
| Key Risks / Follow-Up |  |  |

#### Desktop

| Area | Decision | Reference |
|------|----------|-----------|
| Canonical Design Source | `platform=web` baseline refs from `figma.md` |  |
| Route / Surface |  |  |
| UI Scope | Create / Update / Reuse |  |
| Key Risks / Follow-Up |  |  |

### 6.2 Screen States

| Screen / Route | Loading | Empty | Error | Notes |
|----------------|---------|-------|-------|-------|
|  |  |  |  |  |

### 6.3 Navigation

| From | To | Trigger | Notes |
|------|----|---------|-------|
|  |  |  |  |

### 6.4 Responsive, Accessibility, SEO

#### Responsive Notes

| Breakpoint | Expected Behavior |
|------------|-------------------|
|  |  |

#### Accessibility Notes

| Area | Requirement |
|------|-------------|
|  |  |

#### SEO Notes

| Area | Requirement |
|------|-------------|
|  |  |

### 6.5 Detailed Figma Context Follow-Up

Derive this section from `Baseline References By Platform` in `figma.md` first.

- seed `Msite` rows from `platform=mobile` baseline references
- seed `Desktop` rows from `platform=web` baseline references
- add extra rows only when a non-baseline state or component also needs design drill-down

- carry the concrete node ID here when the design source is trustworthy
- write `pending from design` when the node ID is still missing
- keep the delta in `Draft` when a blocking visual detail still has no trustworthy node ID or token

| Delivery Phase | Screen / State | Node ID / Status | Why Drill Down Is Needed | Blocking For Implementation | Implementation Phase Note |
|----------------|----------------|------------------|--------------------------|-----------------------------|---------------------------|
| Msite / Desktop |  |  |  | Yes / No | Call `get_design_context` or `get_screenshot` before implementing the final UI details |

## 7. Change Surface

Use this section to make the web implementation scope concrete.

### 7.1 Files Or Modules Likely To Change

| Target | Action | Why | Confidence | Reference |
|--------|--------|-----|------------|-----------|
|  | Update / Extend / Reuse |  | High / Medium / Low |  |

### 7.2 Files Or Modules Likely To Add

| Target | Purpose | Confidence | Reference |
|--------|---------|------------|-----------|
|  |  | High / Medium / Low |  |

### 7.3 Expected Edit Shape

Use this section to say what kind of edits are expected, not to write the code itself.

| Area | Expected Edit Shape | Reference |
|------|---------------------|-----------|
| Page / Shell |  |  |
| Component Layer |  |  |
| State Layer |  |  |
| Data / Query Layer |  |  |
| Tracking Layer |  |  |
| Tests / Verification Surface |  |  |

### 7.4 Non-Goals

- 

## 8. Risks And Gaps

Use this section for unresolved but still visible implementation risk.

| Risk / Gap | Impact | Follow-Up Needed | Blocking | Codegen Impact | Reference |
|------------|--------|------------------|----------|----------------|-----------|
|  |  |  | Yes / No | Safe / Caution / Stop |  |

## 9. Implementation Handoff

Use this section to make the delta directly usable by implementation AI.

### 9.1 Recommended Implementation Order

1. 
2. 
3. 

### 9.2 Verification Focus

- 

### 9.3 Dependencies / Blockers

- 

### 9.4 Fallback References

Only list the extra references that implementation AI may open when the delta alone is still insufficient for a narrow detail.

| When To Open | Fallback Reference | Why |
|--------------|--------------------|-----|
|  | `.planning/<feature>/repo-exploration.md` |  |
|  | `.planning/<feature>/kh-search-brief.md` |  |
|  | `figma.md` / exact node ID |  |
|  | `api-contract.md` |  |

### 9.5 Codegen Readiness Checklist

- [ ] The likely change surface is explicit enough that an implementation AI knows where to start
- [ ] Reuse / extend / create decisions are explicit for the important UI and state surfaces
- [ ] Data, state, API, tracking, socket, and local storage behavior are explicit when they are in scope
- [ ] Loading, empty, error, and key edge-case behavior are explicit
- [ ] Risks are classified clearly enough that implementation AI knows whether to continue, continue with caution, or stop
- [ ] Fallback references are listed only for narrow detail checks, not as a replacement for missing delta content
- [ ] The delta is self-sufficient enough that implementation should start from this file first, not from raw package or raw repo exploration
