# OPEN-QUESTIONS-PLMO-1328: Redesign Khách Tab as a Toggle and show Contextual Tooltip

Use this file for unresolved business, product, design, or shared-behavior questions.

Do not track raw API field naming, schema shape, or payload structure here by default; keep those review items in `api-contract.md` unless they change shared behavior.

This artifact should be easy for a reviewer to scan quickly:

- What is currently blocking the shared baseline?
- What still needs a decision later but can stay open for now?
- What was already resolved?
- What was explicitly deferred or kept out of scope?

---

## 1. Register Metadata

| Field                       | Value                                                                                         |
| --------------------------- | --------------------------------------------------------------------------------------------- |
| Feature ID                  | PLMO-1328                                                                                     |
| Feature Name                | Redesign Khách Tab as a Toggle and show Contextual Tooltip                                    |
| Master Spec Package Version | r1                                                                                            |
| Package Status              | Draft                                                                                         |
| Source PRD                  | [PRD CRM Lead Dashboard Adoption Discovery](https://701search.atlassian.net/wiki/spaces/PROD/pages/4792254581/PRD+CRM+lead+dashboard+Adoption+Discovery#Redesign-Kh%C3%A1ch-Tab-as-a-Toggle-and-show-Contextual-Tooltip) |
| Last Updated                | 2026-04-15                                                                                    |
| Owners                      | Minh Tin Trieu (PIC), Pham Thi Ngoc Anh (QA), Vo Ngoc Tin (CC)                               |

---

## 2. Open Question Readout

### 2.1 Next Decisions

| Priority | ID       | Decision Needed                                                                 | Status |
| -------- | -------- | -------------------------------------------------------------------------------- | ------ |
| Blocking | OQ-001   | Should new entry points (home widget, ad CTAs, push) respect CRM experiment treatment? | Open   |
| Blocking | OQ-002   | Should "Khách mới" widget and "Xem liên hệ" CTA be subscription-gated?              | Open   |
| Blocking | OQ-003   | What defines "first-time daily visit" for tooltip trigger — calendar date, 24h rolling, per-device or per-account? | Open   |
| Non-Blocking | OQ-004 | How should shell-level badge aggregate when both Tin nhắn and Khách have unread? | Open   |
| Non-Blocking | OQ-005 | What content format for daily morning push notification — count only, top leads with ad names? | Open   |
| Non-Blocking | OQ-006 | What to show for empty states — ads with zero leads, home widget with no new leads? | Open   |

---

## 3. Blocking Questions

### 3.1 Summary

| ID       | Decision Needed                                                                 | Needed For                          | Current Handling If Unanswered | Status |
| -------- | -------------------------------------------------------------------------------- | ----------------------------------- | ------------------------------ | ------ |
| OQ-001   | Should new entry points (home widget, ad CTAs, push) respect CRM experiment treatment? | Experiment integrity, valid measurement | Block home widget + ad CTAs + push until resolved | Open   |
| OQ-002   | Should "Khách mới" widget and "Xem liên hệ" CTA be subscription-gated?              | Subscription enforcement, access control | Render entry points for all sellers but show upgrade prompt if no subscription | Open   |
| OQ-003   | What defines "first-time daily visit" for tooltip trigger?                              | Tooltip behavior correctness         | Disable tooltip until definition confirmed | Open   |

### 3.2 Details

#### OQ-001

- Type: Experiment / Access Control
- Decision Needed: Should the new home screen widget, "Xem liên hệ" ad card CTAs, and daily push notifications be gated to only sellers who are in the CRM-enabled experiment treatment group?
- Why unclear: Current CRM access is experiment-gated (only treatment group sees Khách tab). New entry points could bypass the chat shell and contaminate experiment group measurement if they serve content to sellers outside treatment.
- Current Handling If Unanswered: Block home widget + ad CTAs + push from being served to sellers outside CRM experiment treatment. Only serve to treatment group until decision is made.
- Owner: Product / Growth Team
- Status: Open
- Resolution: Pending
- Related Requirement / Flow / Figma Ref: STORY-002, STORY-004, daily push notification in Proposed Solution; pm_analyze_feature_impact risk "Experiment bypass"

#### OQ-002

- Type: Subscription / Access Control
- Decision Needed: Should "Khách mới" widget on Trang chủ and "Xem liên hệ" CTA on ad cards appear only for sellers with an active subscription that includes lead management add-on?
- Why unclear: Legacy Lead Management is subscription-gated. CRM access is experiment-gated. The redesign merges these concerns — if widget/CTAs serve CRM leads but seller has no subscription, is that acceptable? If they serve legacy leads instead, subscription check applies.
- Current Handling If Unanswered: Render widget and CTA for all sellers; show upgrade-to-access prompt if seller lacks subscription when they attempt to use the feature.
- Owner: Product / Monetization Team
- Status: Open
- Resolution: Pending
- Related Requirement / Flow / Figma Ref: STORY-002, STORY-004; pm_analyze_feature_impact risk "Subscription inconsistency"

#### OQ-003

- Type: Behavioral Definition
- Decision Needed: What defines "first-time daily visit" for triggering the tooltip on the Liên hệ page?
- Why unclear: Several interpretations are plausible — calendar date in seller's timezone (reset at midnight), 24-hour rolling window from first visit, per-device tracking, or per-account tracking. Cookie clearing or multi-device usage could cause divergent behavior across interpretations.
- Current Handling If Unanswered: Disable tooltip feature until definition is confirmed. Do not ship tooltip in r1.
- Owner: Product / Engineering
- Status: Open
- Resolution: Pending
- Related Requirement / Flow / Figma Ref: STORY-001 tooltip spec ("first time visit 'Liên hệ' section of each day (after 12h)")

---

## 4. Non-Blocking Questions

### 4.1 Summary

| ID       | Decision Needed                                                                    | Owner          | Needed For                            | Current Handling If Unanswered           | Status |
| -------- | --------------------------------------------------------------------------------- | -------------- | ------------------------------------- | ---------------------------------------- | ------ |
| OQ-004   | How should shell-level badge aggregate when both Tin nhắn and Khách have unread?  | Product / Chat | Visual consistency, UX coherence      | Show sum of both tab unreads             | Open   |
| OQ-005   | What content format for daily morning push — count only, or top leads with ad names? | Product / Comm | Push notification spec                | Send count only until content spec ready | Open   |
| OQ-006   | What to show for empty states — ads with zero leads, home widget with no new leads? | Product / Eng  | Empty state UX, widget/card design    | Hide widget/card when no leads           | Open   |

### 4.2 Details

#### OQ-004

- Type: Badge / Notification Behavior
- Decision Needed: When both Tin nhắn and Khách tabs have unread activity, should the shell-level badge show the sum, the max, or only Tin nhắn? Does tapping the shell badge default to Tin nhắn or Khách?
- Why unclear: Currently Tin nhắn is treated as primary. Redesign elevates Khách to equal-weight toggle. Badge aggregation logic wasn't specified in PRD.
- Current Handling If Unanswered: Default shell badge to sum of both tabs; tapping shell badge navigates to last active tab or Tin nhắn as default.
- Owner: Product / Chat Team
- Status: Open
- Resolution: Pending
- Related Requirement / Flow / Figma Ref: STORY-001 equal-weight toggle redesign; pm_analyze_feature_impact risk "Unread count divergence"

#### OQ-005

- Type: Push Notification Content
- Decision Needed: What content should the daily morning push notification include?
- Why unclear: PRD says "daily morning push notification summarizing new leads from the past 24 hours." Content could be a simple unread count, or include top N leads with ad names, thumbnails, or buyer summaries. Each level adds complexity and potential rendering issues on iOS/Android lock screens.
- Current Handling If Unanswered: Send push with lead count only — "Bạn có X khách mới đang đợi" — until content spec is defined.
- Owner: Product / Communication Platform
- Status: Open
- Resolution: Pending
- Related Requirement / Flow / Figma Ref: Proposed Solution bullet 4; pm_analyze_feature_impact risk "Push quota conflict"

#### OQ-006

- Type: Empty State UX
- Decision Needed: What should sellers see when "Xem liên hệ" is tapped on an ad with zero leads, or when "Khách mới" widget has no new leads today?
- Why unclear: PRD doesn't specify empty state behavior. Choices include: hide the widget/CTA entirely when empty, show the widget with a zero-state message, or show a "no new leads" placeholder.
- Current Handling If Unanswered: Hide widget/card when no eligible leads. Show CTA button on ad cards regardless of lead count (tap shows empty state message in lead list).
- Owner: Product / Design
- Status: Open
- Resolution: Pending
- Related Requirement / Flow / Figma Ref: STORY-002, STORY-004; pm_analyze_feature_impact risk "Empty state handling"

---

## 5. Resolved Questions

| ID       | Resolution                                                                               | Resolution Source | Revision |
| -------- | ---------------------------------------------------------------------------------------- | ----------------- | -------- |
| RQ-001   | Tab redesign: Yellow color + toggle design confirmed; outline style filter labels confirmed | Figma design link | r1       |
| RQ-002   | Tooltip content: "X khách mới đang đợi bạn Nhấn để xem thông tin và tư vấn ngay" confirmed | Figma + STORY-001 | r1       |
| RQ-003   | Tooltip number aligns with Khách tab badge count                                         | STORY-001 spec    | r1       |
| RQ-004   | Tooltip triggers only when there are unread leads AND seller is on Tin nhắ" tab         | STORY-001 spec    | r1       |

---

## 6. Deferred / Out Of Scope

| ID        | Reason                                                                                              | Source              | Revision |
| --------- | --------------------------------------------------------------------------------------------------- | ------------------- | -------- |
| DEF-001   | Legacy Lead Management dashboard coexistence — whether to merge or keep separate surfaces is out of scope for this redesign | pm_analyze_feature_impact | r1       |
| DEF-002   | Ad-to-lead filtering scope — STORY-003 (Filter Leads by Ad) tracked separately in PLMO-1237          | Jira STORY-003     | r1       |
| DEF-003   | Push delivery quota rules, throttling, and conflict resolution with other operational pushes         | pm_analyze_feature_impact | r1       |

---

## 7. Active Assumptions

| ID      | Assumption                                                    | Risk   | Mitigation                         |
| ------- | ------------------------------------------------------------ | ------ | ---------------------------------- |
| A-001   | New entry points will be gated to CRM experiment treatment    | High   | Pending OQ-001 resolution          |
| A-002   | Subscription check will be enforced on widget + CTA access    | Medium | Pending OQ-002 resolution          |
| A-003   | Tooltip "first-time daily" uses calendar date in seller TZ   | Medium | Pending OQ-003 definition          |
| A-004   | Daily push will be a new scheduled campaign type in Comm platform | Medium | Comm platform team consultation needed |
| A-005   | Home widget will be rendered by App Wrapper on Trang chủ     | Low    | App Wrapper team alignment needed |
