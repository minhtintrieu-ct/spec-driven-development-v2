## 1. Repository Baseline

Use this section to capture only the repo-wide conventions actually inherited by this feature delta.

If the delta came from scoped discovery only, say that explicitly instead of inventing a baseline artifact.

| Item | Value | Source / Reference |
|------|-------|--------------------|
| Framework |  |  |
| Routing Model |  |  |
| UI Library / DS | `clad-ui` |  |
| State Management |  |  |
| Data Fetching |  |  |
| Styling System |  |  |
| Testing Setup |  |  |
| Similar Page / Flow |  |  |

## 2. Scoped Repo Context

List only the app, route group, feature module, similar page, shared component set, and data layer surfaces that were actually inspected for this feature.

| Area | Scoped Target | Why It Was Chosen | Reference |
|------|---------------|-------------------|-----------|
| App / Package |  |  |  |
| Route / Module |  |  |  |
| Similar Page / Flow |  |  |  |
| Shared Components |  |  |  |
| Data / Query Layer |  |  |  |

## 3. Architecture Decisions

| Decision | Choice | Rationale | Reference |
|----------|--------|-----------|-----------|
| Route Group / Module |  |  |  |
| Design System Strategy | `clad-ui-first` | Reuse or extend `clad-ui` first; apply local customization only when needed |  |
| Rendering Strategy |  |  |  |
| Data Layer Pattern |  |  |  |
| Local State Pattern |  |  |  |
| Error Handling Pattern |  |  |  |
| Loading Pattern |  |  |  |
| SEO Pattern |  |  |  |

Write only the feature-specific decisions or exceptions here. Do not restate repo-wide defaults that already live in the baseline or scoped repo findings.

If the repo does not currently expose `clad-ui`, record the required bootstrap or adaptation work explicitly instead of switching to another component library.

## 4. Delivery Phases

Split the web implementation into two explicit phases.

- `Msite` is derived from `platform=mobile` baseline references in `figma.md`
- `Desktop` is derived from `platform=web` baseline references in `figma.md`

### 4.1 Msite

| Area | Decision | Reference |
|------|----------|-----------|
| Canonical Design Source | `platform=mobile` baseline refs from `figma.md` |  |
| Route / Surface |  |  |
| UI Scope | Create / Update / Reuse |  |
| Key Risks / Follow-Up |  |  |

### 4.2 Desktop

| Area | Decision | Reference |
|------|----------|-----------|
| Canonical Design Source | `platform=web` baseline refs from `figma.md` |  |
| Route / Surface |  |  |
| UI Scope | Create / Update / Reuse |  |
| Key Risks / Follow-Up |  |  |

## 5. Pages

| Route | Rendering | Action | Target Location | Reference |
|-------|-----------|--------|-----------------|-----------|
|  | SSR / SSG / CSR / ISR | Create / Update / Reuse |  |  |

## 6. Component Usage

| Component | Source | Action | Target | Reason | Reference |
|-----------|--------|--------|--------|--------|-----------|
|  | Existing / New | Reuse / Extend / Create |  |  |  |

### 6.1 Component Variants

| Component | Variant / Change | Implementation Note | Reference |
|-----------|------------------|---------------------|-----------|
|  |  |  |  |

### 6.2 Screen States

| Screen / Route | Loading | Empty | Error | Notes |
|----------------|---------|-------|-------|-------|
|  |  |  |  |  |

### 6.3 Detailed Figma Context Follow-Up

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

## 7. API Integration

| Endpoint / Contract Ref | Consumption Pattern | Cache / Revalidation | Error Handling |
|-------------------------|---------------------|----------------------|----------------|
|  |  |  |  |

## 8. Navigation

| From | To | Trigger | Notes |
|------|----|---------|-------|
|  |  |  |  |

## 9. Responsive, Accessibility, SEO

### 9.1 Responsive Notes

| Breakpoint | Expected Behavior |
|------------|-------------------|
|  |  |

### 9.2 Accessibility Notes

| Area | Requirement |
|------|-------------|
|  |  |

### 9.3 SEO Notes

| Area | Requirement |
|------|-------------|
|  |  |

## 10. Performance Constraints

| Metric / Concern | Target / Decision | Notes |
|------------------|-------------------|-------|
|  |  |  |

## 11. Analytics Events

| Feature | Event | Status | Trigger | Implementation Note | Reference |
|---------|-------|--------|---------|---------------------|-----------|
|  |  | Ready / Waiting for event / Reuse existing |  |  |  |

### 11.1 ECS Tracking Notes

Use this section only when ECS is part of the web repo path for this feature.

| Area | Decision | Notes |
|------|----------|-------|
| ECS Setup | Existing / Bootstrap required / Not applicable |  |
| `.ecsrc` | Reuse / Add / Not applicable |  |
| Generated Tracking Module | Reuse / Extend / New module needed |  |
| `setClientInfo(...)` Path | Existing / Add follow-up |  |

Do not list full ECS payload schemas here. Record only the feature binding, event names, trigger points, and any bootstrap or follow-up work the web repo needs.

## 12. Implementation Checklist

- [ ] `clad-ui` usage or bootstrap path is explicit
- [ ] Msite and Desktop phases are separated clearly
- [ ] Routes and rendering strategy confirmed
- [ ] Reuse vs extend vs create decisions captured
- [ ] Contract consumption pattern aligned with `api-contract.md`
- [ ] Loading / empty / error states covered
- [ ] Analytics and SEO expectations captured
- [ ] ECS feature binding and follow-up status are explicit when telemetry is in scope
- [ ] Figma follow-up rows are derived from `figma.md` baseline references before adding extra rows

## 13. Open Questions / Risks

- 
