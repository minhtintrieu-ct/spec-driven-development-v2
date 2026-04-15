# MS-CORE-PLMO-1328: Redesign Khách Tab as a Toggle and show Contextual Tooltip

> **Package Version**: r1
> **Package Status**: Draft
> **Supersedes**: -
> **Source PRD**: `temp/PLMO-1328/prd.md`

This document is the shared-baseline hub for the feature.

Read this file first to understand:

- what the feature is changing
- why it is changing
- the main user and system flow
- the key decisions and blockers
- which supporting artifacts hold deeper detail

Supporting artifacts:

- `knowledge.md`
- `open-questions.md`
- `api-contract.md`

---

## 1. Document Metadata

| Field | Value |
|-------|-------|
| Feature ID | PLMO-1328 |
| Feature Name | Redesign Khách Tab as a Toggle and show Contextual Tooltip |
| Owner | Minh Tin Trieu |
| Package Version | r1 |
| Package Status | Draft |
| Supersedes | - |
| Source PRD | `temp/PLMO-1328/prd.md` |
| Created | 2026-04-15 |
| Last Updated | 2026-04-15 |
| Approved At | — |

---

## 2. One-Minute Readout

- **Main change:** Redesign "Tin nhắn"/"Khách" tab pair as equal-weight toggle with yellow styling and badge counts; add contextual tooltip on Liên hệ page for first-time daily visit; add "Xem liên hệ" CTA on ad listing cards; add "Khách mới" widget on seller's home screen.
- **Current package status:** Draft — 3 blocking open questions prevent shared baseline finalization.
- **Main blockers:** OQ-001 (experiment treatment gating for new entry points), OQ-002 (subscription gating for widget/CTA), OQ-003 (tooltip "first-time daily" definition). Tooltip deferred from r1 if OQ-003 remains unresolved.
- **Backend impact:** 2 net-new endpoints confirmed (`GET /crm/leads` with `list_id` filter; `GET /crm/leads/recent` for home widget); 5 existing CRM APIs confirmed for reuse.
- **Supporting artifacts:** `knowledge.md` (context), `open-questions.md` (blockers), `api-contract.md` (API contract decisions).

---

## 3. What Changes

- Tin nhắn/Khách tab pair redesigned as equal-weight toggle with yellow color and badge counts on both tabs
- Filter label style changed to outline design (mobile + msite)
- Contextual tooltip on Liên hệ page shown to first-time daily visitors with unread leads
- "Xem liên hệ" CTA button added to each ad listing card in Quản lý tin, deep-linking to Khách tab filtered by that ad
- "Khách mới" card/widget added to seller's Trang chủ home screen showing recent leads snapshot
- Daily morning push notification summarizing new leads from past 24 hours

---

## 4. Why

### 4.1 Business Objective

Only ~50% of PTY sellers in the CRM experiment treatment group are aware of the Khách tab. Sellers who discover it lack a habit loop to return daily. Redesign aims to increase lead discovery and consultation rate by improving tab visibility, adding entry points from high-traffic surfaces (ad management, home screen), and reminding sellers of new leads via push.

### 4.2 Target Users

- PTY sellers in CRM experiment treatment group with active listings
- Sellers with lead management subscription entitlement (new entry points may be subscription-gated — pending OQ-002)

---

## 5. Shared Flow

### 5.1 Primary Flow

1. Seller opens Chat app → sees Tin nhắn/Khách equal-weight toggle with badge counts
2. Seller switches to Khách tab → sees CRM lead list
3. Seller taps "Xem liên hệ" on ad card → deep-links to Khách tab filtered by that ad's leads
4. Seller visits Liên hệ for first time today with unread leads → sees tooltip prompting to check Khách tab
5. Seller opens Trang chủ → sees "Khách mới" widget with recent leads snapshot
6. Seller receives daily morning push → taps to open Khách tab with new leads

### 5.2 System Flow Summary

- **Client surfaces:** `ct-web-uni-chat` (toggle + tooltip), `ct-web-uni-adlisting` (CTA), App Wrapper (home widget)
- **Main services:** `ct-logic-uni-platform` (CRM orchestration), `ct-core-uni-crm` (MongoDB CRM core), `ct-logic-uni` (legacy lead entitlement)
- **Key API path:** `ct-web-uni-chat` → `ct-api-uni-platform` → `ct-logic-uni-platform` → `ct-core-uni-crm`
- **New backend work:** `GET /crm/leads` with `list_id` filter (API-001); `GET /crm/leads/recent` snapshot endpoint (API-003); deep-link URL scheme (API-002)
- **Existing reuse:** `POST /crm/unread_count:trigger_push` (API-004); Comm platform push scheduling (API-005)

---

## 6. Key Decisions

| Area | Current Decision | Status | Supporting Artifact |
|------|------------------|--------|---------------------|
| Tab toggle design | Yellow color + toggle design + outline filter labels | Confirmed (RQ-001) | open-questions.md |
| Tooltip content | "X khách mới đang đợi bạn Nhấn để xem thông tin và tư vấn ngay" | Confirmed (RQ-002) | open-questions.md |
| Tooltip badge alignment | Tooltip number aligns with Khách tab badge count | Confirmed (RQ-003) | open-questions.md |
| Tooltip trigger condition | Shows only when unread leads exist AND seller on Tin nhắn tab | Confirmed (RQ-004) | open-questions.md |
| Experiment gating for new entry points | New entry points gated to CRM experiment treatment | Open (OQ-001) | open-questions.md |
| Subscription gating for widget/CTA | Widget/CTA rendered for all sellers; upgrade prompt if no subscription | Open (OQ-002) | open-questions.md |
| "First-time daily visit" definition | Calendar date in seller's timezone | Open (OQ-003) | open-questions.md |
| Badge aggregation at shell level | Sum of both tab unreads; tapping navigates to last active tab or Tin nhắn | Open (OQ-004) | open-questions.md |
| Push notification content | Count only in r1; top leads with ad names deferred | Open (OQ-005) | open-questions.md |
| Empty states | Hide widget/CTA when no leads; CTA on ad cards shows empty state in lead list | Open (OQ-006) | open-questions.md |

---

## 7. Blockers / Follow-Up

### 7.1 Blocking Items

| ID | What Still Needs A Decision | Current Handling | Supporting Artifact |
|----|-----------------------------|------------------|---------------------|
| OQ-001 | Should new entry points (home widget, ad CTAs, push) respect CRM experiment treatment? | Block home widget + ad CTAs + push from sellers outside treatment until resolved | open-questions.md |
| OQ-002 | Should "Khách mới" widget and "Xem liên hệ" CTA be subscription-gated? | Render entry points for all sellers; show upgrade prompt if no subscription | open-questions.md |
| OQ-003 | What defines "first-time daily visit" for tooltip trigger — calendar date, 24h rolling, per-device or per-account? | Disable tooltip feature until definition confirmed | open-questions.md |

### 7.2 Non-Blocking Follow-Up

| ID / Ref | Follow-Up | Current Handling | Supporting Artifact |
|----------|-----------|------------------|---------------------|
| OQ-004 | Shell-level badge aggregation when both tabs have unread | Sum of both; tapping defaults to last active tab or Tin nhắn | open-questions.md |
| OQ-005 | Daily morning push content format | Send count only until content spec defined | open-questions.md |
| OQ-006 | Empty state UX for widget and ad CTA | Hide widget/card when no leads; CTA shows empty state message | open-questions.md |
| TEL-001 | Tooltip impression and CTA tap event naming | Waiting for Data team confirmation | api-contract.md |

---

## 8. Example Details

### 8.1 Example Summary

| Ref | Scenario | Why It Matters |
|-----|----------|----------------|
| EX-001 | Seller with unread leads visits Liên hệ for first time today | Validates tooltip trigger and navigation |
| EX-002 | Seller taps "Xem liên hệ" on ad card with leads | Validates deep-link filter and entry point |
| EX-003 | Seller opens Trang chủ with no new leads | Validates empty state (widget hidden) |
| EX-004 | Seller outside CRM experiment treatment sees home widget | Validates OQ-001 gating — should not render |

### 8.2 Example Details

#### EX-001 — Tooltip shown on first-time daily Liên hệ visit

```gherkin
Given the seller is on the "Tin nhắn" tab
And there are unread CRM leads for this seller
And the current time is before 12:00
And the seller has not visited the "Liên hệ" section today
When the seller navigates to the "Liên hệ" section
Then a tooltip is shown on the "Khách" tab
And the tooltip displays "X khách mới đang đợi bạn"
And X matches the unread lead count on the Khách tab badge
When the seller taps the tooltip
Then the app navigates to the "Khách" tab
And the tooltip is marked as shown for today
```

- Notes: OQ-003 ("first-time daily" definition) is blocking this. If unresolved, tooltip is deferred from r1.
- Supporting refs: STORY-001, OQ-003

#### EX-002 — "Xem liên hệ" CTA deep-links to filtered lead list

```gherkin
Given the seller is on the "Quản lý tin" page
And the seller has at least one active ad listing
And that ad has at least one associated CRM lead
When the seller taps "Xem liên hệ" on the ad card
Then the app navigates to the Khách tab
And the lead list is filtered to only leads associated with that ad
And the count matches the number shown on the CTA badge
```

- Notes: API-001 (list_id filter) needs backend confirmation. OQ-001/OQ-002 gating still applies.
- Supporting refs: STORY-002, API-001, OQ-001, OQ-002

#### EX-003 — Home widget shown with no new leads

```gherkin
Given the seller has no new CRM leads today
When the seller opens the Trang chủ home screen
Then the "Khách mới" widget is not rendered (hidden)
```

- Notes: OQ-006 defines this behavior. OQ-001 gating (experiment treatment) also applies.
- Supporting refs: STORY-004, OQ-006, OQ-001

#### EX-004 — Seller outside CRM treatment does not see home widget

```gherkin
Given the seller is not in the CRM experiment treatment group
When the seller opens the Trang chủ home screen
Then the "Khách mới" widget is not rendered
```

- Notes: OQ-001 gating must be enforced at App Wrapper level. If OQ-001 is unresolved, widget is blocked entirely.
- Supporting refs: STORY-004, OQ-001

---

## 9. Supporting Artifacts

| Artifact | Purpose | Notes |
|----------|---------|-------|
| `knowledge.md` | Supporting context | CRM vs Lead Management distinction, service topology, existing APIs |
| `open-questions.md` | Ambiguity and blockers | 3 blocking OQs, 3 non-blocking OQs, 4 resolved decisions |
| `api-contract.md` | API and backend contract baseline | 2 net-new endpoints, 5 reuse confirmations, 1 telemetry item pending |
