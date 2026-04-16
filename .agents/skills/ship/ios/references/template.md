## 1. Inherited Repo Baseline

Use this section to point at the iOS baseline artifact and list only the repo-wide conventions this delta is inheriting by reference.

| Area | Inherited Decision | Baseline Reference |
|------|--------------------|--------------------|
| Architecture |  |  |
| Navigation |  |  |
| Design System |  |  |
| Networking |  |  |
| Storage |  |  |
| Analytics |  |  |
| Testing |  |  |

If no baseline exists yet:

- say so explicitly here
- explain whether this delta is blocked, draft, or based on a scan-all baseline refresh

## 2. Architecture Decisions

| Decision | Choice | Rationale | Reference |
|----------|--------|-----------|-----------|
| Pattern |  |  |  |
| Module Location |  |  |  |
| UI Framework | UIKit / SwiftUI |  |  |
| State Management |  |  |  |
| Async Model |  |  |  |
| Navigation |  |  |  |

Write only the feature-specific decisions or exceptions here. Do not restate repo-wide defaults that already live in the baseline.

## 3. UI Design Reference

### 3.1 Screens

| Screen | Frame / Node | Action | Target Location | Reference |
|--------|--------------|--------|-----------------|-----------|
|  |  | Create / Update / Reuse |  |  |

### 3.2 Shared Components

| Component | Source | Action | Target | Reason | Reference |
|-----------|--------|--------|--------|--------|-----------|
|  | DS / Existing / New | Reuse / Extend / Create |  |  |  |

### 3.3 Component Variants

| Component | Variant / Change | Implementation Note | Reference |
|-----------|------------------|---------------------|-----------|
|  |  |  |  |

### 3.4 Screen States

| Screen | Loading | Empty | Error | Notes |
|--------|---------|-------|-------|-------|
|  |  |  |  |  |

### 3.5 Detailed Figma Context Follow-Up

Derive this section from `Baseline References By Platform` in `figma.md` first.

- seed `iOS` rows from `platform=mobile` baseline references
- add extra rows only when a non-baseline state or component also needs design drill-down

If `figma.md` already calls out unresolved visual follow-up for an iOS-visible element, do not leave this section empty.

- carry the concrete node ID here when the design source is trustworthy
- write `pending from design` when the node ID is still missing
- keep the delta in `Draft` when a blocking visual detail still has no trustworthy node ID or token

| Delivery Phase | Screen / State | Node ID / Status | Why Drill Down Is Needed | Blocking For Implementation | Implementation Phase Note |
|----------------|----------------|------------------|--------------------------|-----------------------------|---------------------------|
| iOS |  |  |  | Yes / No | Call `get_design_context` or `get_screenshot` before implementing the final UI details |

## 4. Platform-Specific Concerns

### 4.1 Deep Linking

| Type | URL / Scheme | Handling |
|------|--------------|----------|
|  |  |  |

### 4.2 Storage / Persistence

| Data | Storage | Key / Entity | Reference |
|------|---------|--------------|-----------|
|  |  |  |  |

### 4.3 Lifecycle / Native Affordances

| Concern | Decision | Notes |
|---------|----------|-------|
|  |  |  |

### 4.4 Version Constraints / Networking

| Constraint / Need | Decision | Notes |
|-------------------|----------|-------|
|  |  |  |

## 5. Navigation Map

| From | To | Trigger | Notes |
|------|----|---------|-------|
|  |  |  |  |

## 6. Dependencies

| Module / Framework | Usage | Reference |
|--------------------|-------|-----------|
|  |  |  |

## 7. Testing Strategy

| Area | Target / Focus | Reference |
|------|----------------|-----------|
| ViewModels / State |  |  |
| Navigation |  |  |
| Network / Repository |  |  |
| UI States |  |  |

## 8. Analytics Events

| Feature | Event | Status | Trigger | Implementation Note | Reference |
|---------|-------|--------|---------|---------------------|-----------|
|  |  | Ready / Waiting for event / Reuse existing |  |  |  |

### 8.1 Tracking Notes

Keep this section lightweight. Record only the feature binding, event name, status, trigger point, and any implementation follow-up needed by the iOS repo.

| Area | Decision | Notes |
|------|----------|-------|
| Tracking Setup | Existing / Bootstrap required / Not applicable |  |
| Tracking Module / Wrapper | Reuse / Extend / New module needed |  |
| Screen / Lifecycle Hook | Existing / Add follow-up |  |

Do not list full tracking payload schemas here. If an event is still `Waiting for event`, keep it as follow-up work and update later through `/complete-master-spec`.

## 9. Localization

| Item | Requirement |
|------|-------------|
| Key Prefix |  |
| String Source |  |
| RTL / Dynamic Type |  |

## 10. Open Questions / Risks

- 
