## 1. Input Status

Write the QC output package under `TCs/<JIRA_ID>/` in the spec repo.

### 1.1 Shared Package Inputs

| Input | Status | Notes |
|-------|--------|-------|
| `master-spec.md` | Current / Missing |  |
| `api-contract.md` | Current / Missing |  |
| `figma.md` | Current / Missing / Skipped |  |
| `open-questions.md` | Current / Missing |  |

### 1.2 Optional Platform Delta Inputs

List platform deltas only when they exist.

| Delta | Status | Consumed? | Notes |
|-------|--------|-----------|-------|
| `backend-delta.md` | Current / Stale / Missing | Yes / No |  |
| `web-delta.md` | Current / Stale / Missing | Yes / No |  |
| `ios-delta.md` | Current / Stale / Missing | Yes / No |  |
| `android-delta.md` | Current / Stale / Missing | Yes / No |  |

## 2. Readiness Summary

### 2.1 One-Minute Summary

- State what QC can already plan directly from the shared package.
- State which coverage areas are still blocked by open questions.
- State whether any platform-specific coverage is still pending because a platform delta is missing or stale.

### 2.2 Coverage Readout

| Area | Status | Current Readout | Blocking Ref |
|------|--------|-----------------|--------------|
| Shared behavior coverage | Draft / Review-ready / Final |  |  |
| API / contract coverage | Draft / Review-ready / Final |  |  |
| Design / UI coverage | Draft / Review-ready / Final |  |  |
| Platform-specific enrichment | Draft / Partial / Final |  |  |
| Environment / test data readiness | Draft / Review-ready / Final |  |  |

## 3. Test Strategy Overview

### 3.1 Scope & Objectives

| Objective | Description |
|-----------|-------------|
|  |  |

### 3.2 Test Types Breakdown

| Type | Coverage Goal | Owner | Notes |
|------|---------------|-------|-------|
| Manual UI |  |  |  |
| Platform Logic |  |  |  |
| E2E |  |  |  |

### 3.3 Risk Areas

| Risk | Impact | Mitigation |
|------|--------|------------|
|  |  |  |

## 4. Manual UI Test Coverage

### 4.1 Shared Screen / Surface Matrix

| Surface | Platform Scope | Test Focus | Priority | Source |
|---------|----------------|------------|----------|--------|
|  |  |  |  |  |

### 4.2 Accessibility / Responsive Checklist

- [ ] Accessibility expectations captured from the shared package
- [ ] Responsive checkpoints captured when relevant
- [ ] Design states captured from `figma.md` when available

## 5. Platform-Specific Coverage Enrichment

Use this section only for additional QC coverage that becomes clearer because a current platform delta exists.

| Platform | Enrichment Source | Added Coverage | Status |
|----------|-------------------|----------------|--------|
| Backend | `backend-delta.md` |  | Draft / Review-ready / Final |
| Web | `web-delta.md` |  | Draft / Review-ready / Final |
| iOS | `ios-delta.md` |  | Draft / Review-ready / Final |
| Android | `android-delta.md` |  | Draft / Review-ready / Final |

## 6. Integration / E2E Scenarios

| Scenario ID | Journey | Platforms | Priority | Source |
|-------------|---------|-----------|----------|--------|
|  |  |  |  |  |

## 7. Test Data Requirements

### 7.1 Test Users

| Account Type | Purpose | Notes |
|--------------|---------|-------|
|  |  |  |

### 7.2 Data Sets / Mocks

| Data Set | Environment | Purpose |
|----------|-------------|---------|
|  |  |  |

## 8. Environment & Setup

### 8.1 Environment Configuration

| Environment | Base URL / Target | Feature Flags | Notes |
|-------------|-------------------|---------------|-------|
|  |  |  |  |

### 8.2 Prerequisites

| Item | Setup Steps | Owner |
|------|-------------|-------|
|  |  |  |

## 9. Blocked Or Pending Coverage

List coverage that cannot be finalized yet because of open questions, missing design inputs, or missing / stale platform deltas.

| Coverage Area | Why It Is Pending | Upstream Ref |
|---------------|-------------------|--------------|
|  |  |  |

## 10. Traceability Matrix

| Requirement / Example / Contract Ref | Covered By | Coverage Status |
|--------------------------------------|------------|-----------------|
|  |  |  |

## 11. Open Questions / Risks

- 
