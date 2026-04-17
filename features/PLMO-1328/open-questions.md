# OPEN-QUESTIONS-PLMO-1328: Redesign Khách Tab as a Toggle and show Contextual Tooltip

---

## 1. Register Metadata

| Field                       | Value                                                                                                       |
| --------------------------- | ----------------------------------------------------------------------------------------------------------- |
| Feature ID                  | PLMO-1328                                                                                                   |
| Feature Name                | Redesign Khách Tab as a Toggle and show Contextual Tooltip                                                  |
| Master Spec Package Version | r1                                                                                                          |
| Package Status              | Draft                                                                                                       |
| Source PRD                  | https://701search.atlassian.net/wiki/spaces/PROD/pages/4792254581/PRD+CRM+lead+dashboard+Adoption+Discovery |
| Last Updated                | 2026-04-16                                                                                                  |
| Owners                      | Minh Tin Trieu (assignee), Pham Thi Ngoc Anh (QA RT)                                                        |

---

## 2. Open Question Readout

### 2.1 Next Decisions

| Priority     | ID     | Decision Needed                                                 | Status |
| ------------ | ------ | --------------------------------------------------------------- | ------ |
| Blocking     | OQ-001 | "after 12h" trigger — calendar time or 24h cooldown?            | Resolved |
| Blocking     | OQ-002 | Tooltip state storage — client-side or server-side?             | Resolved |
| Blocking     | OQ-003 | Tooltip experiment gating — should control-group users see it?  | Resolved |
| Blocking     | OQ-004 | Tooltip minimum unread threshold — any lead or a minimum count? | Resolved |
| Non-Blocking | OQ-005 | "Liên hệ section" — what exactly is the tooltip anchor/view?    | Skipped |
| Non-Blocking | OQ-006 | Tooltip dismissibility — permanent or daily re-show?            | Skipped |
| Non-Blocking | OQ-007 | Yellow toggle conflicts with existing chat color system?        | Skipped |

---

## 3. Blocking Questions

### 3.1 Summary

| ID     | Decision Needed                                    | Needed For                        | Current Handling If Unanswered   | Status |
| ------ | -------------------------------------------------- | --------------------------------- | -------------------------------- | ------ |
| OQ-001 | "after 12h" trigger interpretation                 | Tooltip display logic             | 12:00 PM local time             | Resolved |
| OQ-002 | Tooltip "shown today" state storage                | Cross-session / cross-device sync | Client-side (localStorage)       | Resolved |
| OQ-003 | Tooltip experiment gating alignment with Khách tab | Access control                    | Tooltip hidden for control group | Resolved |
| OQ-004 | Minimum unread lead count for tooltip trigger      | UX threshold                      | Any unread lead (≥1)            | Resolved |

### 3.2 Details

#### OQ-001

- Type: PRD ambiguity
- Decision Needed: Does "first time visit Liên hệ section of each day (after 12h)" mean (a) first visit of the calendar day AND current time is after 12:00 PM local time, or (b) first visit that occurs more than 12 hours since the previous visit?
- Why unclear: PRD wording is ambiguous. "after 12h" could be interpreted as a clock time (12:00 PM) or a duration since last visit.
- Current Handling If Unanswered: Default to 12:00 PM local time interpretation (calendar-day approach). Tooltip shows on first Liên hệ visit of the day after 12:00 PM.
- Owner: PM (Nhung Pham — PRD author)
- Status: Resolved
- Resolution: Calendar-day approach — tooltip shows on first Liên hệ visit of the day after 12:00 PM local time (option a). Confirmed with PM.
- Related Requirement / Flow / Figma Ref: PRD STORY-001 — Tooltips trigger condition

#### OQ-002

- Type: Technical decision
- Decision Needed: Where is the "tooltip shown today" state stored — client-side (localStorage/session), or server-side (user preference API)?
- Why unclear: The PRD does not specify state persistence. Cross-device users could see the tooltip multiple times if stored client-side only.
- Current Handling If Unanswered: Default to client-side (localStorage). Users on the same device won't see repeat tooltips; cross-device sync not guaranteed.
- Owner: FE lead (Minh Tin Trieu)
- Status: Resolved
- Resolution: Client-side (localStorage/session) — cross-device sync not required for v1.
- Related Requirement / Flow / Figma Ref: PRD STORY-001 — Tooltip mark as shown logic

#### OQ-003

- Type: Scope / experiment alignment
- Decision Needed: Should users who are in the control group (not in `CT-PLATFORM.ab_collect_lead_lead_pty` experiment) see the tooltip? The tooltip navigates to the Khách tab, which is only accessible to treatment-group users.
- Why unclear: PRD doesn't address experiment gating for the tooltip specifically. Showing a tooltip that deep-links to an inaccessible feature would be a broken UX.
- Current Handling If Unanswered: Tooltip is hidden for control-group users — same gating as Khách tab.
- Owner: PM (Nhung Pham)
- Status: Resolved
- Resolution: Tooltip is hidden for control group users — same gating as Khách tab. Only treatment-group users see the tooltip.
- Related Requirement / Flow / Figma Ref: PRD STORY-001; Figma 4474-5144

#### OQ-004

- Type: UX threshold
- Decision Needed: Does the tooltip appear if there is exactly 1 unread lead, or is there a minimum threshold (e.g., 3+ unread leads)?
- Why unclear: PRD says "there are unread leads" as the condition but doesn't specify a minimum count. The tooltip number should match the Khách badge count exactly per PRD.
- Current Handling If Unanswered: Show tooltip for any unread lead count ≥ 1. Number in tooltip matches `leadUnreadCountAtom` badge value.
- Owner: PM (Nhung Pham) + Design
- Status: Resolved
- Resolution: Show tooltip for any unread lead count ≥ 1. Number in tooltip matches `leadUnreadCountAtom` badge value directly.
- Related Requirement / Flow / Figma Ref: PRD STORY-001 — "X khách mới đang đợi bạn" aligns with badge

---

## 4. Non-Blocking Questions

### 4.1 Summary

| ID     | Decision Needed                                            | Owner  | Needed For            | Current Handling If Unanswered   | Status |
| ------ | ---------------------------------------------------------- | ------ | --------------------- | -------------------------------- | ------ |
| OQ-005 | "Liên hệ section" navigation anchor definition             | PM     | Tooltip trigger scope | Assume full Chat/Liên hệ view    | Skipped |
| OQ-006 | Tooltip dismissibility — does it re-show next day?         | PM     | UX behavior           | Re-shows on next valid trigger   | Skipped |
| OQ-007 | Yellow toggle color — conflicts with existing chat system? | Design | Visual design         | Use yellow only for Khách toggle | Skipped |

### 4.2 Details

#### OQ-005

- Type: PRD ambiguity
- Decision Needed: What is the "Liên hệ section" that triggers the first-visit condition? Is it (a) the CRM page at `/crm-leading`, (b) the full Chat/Liên hệ tab, or (c) any navigation into the CRM flow?
- Why unclear: If users navigate directly to `/chat` (not `/crm-leading`), they may not trigger the tooltip. The tooltip anchor/page is undefined.
- Current Handling If Unanswered: Tooltip triggers when user first opens the CRM `/crm-leading` page after 12h.
- Owner: PM (Nhung Pham)
- Status: Skipped
- Resolution: Completion Pass: Skipped pending answer. Not blocking for v1 implementation.
- Related Requirement / Flow / Figma Ref: PRD STORY-001 — "first time visit Liên hệ section"

#### OQ-006

- Type: UX behavior
- Decision Needed: If a user dismisses the tooltip (clicks away or clicks Khách tab), does it reappear the next day on the next valid trigger, or is it permanently hidden for the day?
- Why unclear: PRD says tooltip is marked as shown when "has been shown" or "seller has been in Khách tab" — but doesn't specify if dismissing (clicking away) also marks it as shown.
- Current Handling If Unanswered: Any interaction with the tooltip (click or navigate to Khách) marks it as shown for that day. Re-shows next calendar day on valid trigger.
- Owner: PM (Nhung Pham) + Design
- Status: Skipped
- Resolution: Completion Pass: Skipped pending answer. Not blocking for v1 implementation.
- Related Requirement / Flow / Figma Ref: PRD STORY-001 — "Tooltip mark as shown when"

#### OQ-007

- Type: Visual design
- Decision Needed: Does the yellow toggle color conflict with any existing color in the Chat shell's unread or action system? Are there accessibility contrast requirements?
- Why unclear: Knowledge Hub analysis shows yellow is a new color addition to the Chat shell. The existing chat system has its own unread/warning color conventions.
- Current Handling If Unanswered: Apply yellow (#FFD700 or similar per Figma 4474-5144) to Khách toggle only. Conduct accessibility check for contrast ratios.
- Owner: Design
- Status: Skipped
- Resolution: Completion Pass: Skipped pending answer. Not blocking for v1 implementation.
- Related Requirement / Flow / Figma Ref: Figma 4474-5144 — "Mobile: Use the color YELLOW and toggle design"

---

## 5. Resolved Questions

| ID     | Resolution                                                                                        | Resolution Source              | Revision |
| ------ | ------------------------------------------------------------------------------------------------- | ------------------------------ | -------- |
| OQ-R01 | A/B experiment uses `CT-PLATFORM.ab_collect_lead_lead_pty` treatment group                        | knowledge.md / ct-web-uni-chat | r1       |
| OQ-R02 | Unread count sourced from `leadUnreadCountAtom` via `crm_leads_unread_total_updated` socket event | knowledge.md                   | r1       |
| OQ-R03 | Yellow toggle applies to both Mobile and Msite (same design)                                      | PRD STORY-001                  | r1       |
| OQ-R04 | Tooltip content: "X khách mới đang đợi bạn Nhấn để xem thông tin và tư vấn ngay"                  | PRD STORY-001                  | r1       |
| OQ-001 | Calendar-day approach — tooltip shows on first Liên hệ visit of the day after 12:00 PM local time | PM confirmation                | r1       |
| OQ-002 | Client-side (localStorage/session) — cross-device sync not required for v1                        | FE lead decision               | r1       |
| OQ-003 | Tooltip hidden for control group — same gating as Khách tab. Only treatment group sees tooltip.     | PM confirmation                | r1       |
| OQ-004 | Show tooltip for any unread lead count ≥ 1; number in tooltip matches badge value                  | PM + Design confirmation       | r1       |

---

## 6. Deferred / Out Of Scope

| ID      | Reason                                                               | Source                | Revision |
| ------- | -------------------------------------------------------------------- | --------------------- | -------- |
| DEF-001 | "Khách mới" widget on Trang chủ (STORY-004) — separate initiative    | PRD STORY-004         | r1       |
| DEF-002 | Daily morning push notification — separate initiative                | PRD Proposed Solution | r1       |
| DEF-003 | Entry Point from Quản lý tin (STORY-002) — separate ticket PLMO-1237 | PRD STORY-002         | r1       |
| DEF-004 | Filter Leads by Ad dropdown (STORY-003) — separate ticket PLMO-1237  | PRD STORY-003         | r1       |

---

## 7. Active Assumptions

| ID    | Assumption                                                | Confidence | Risk If Wrong                    |
| ----- | --------------------------------------------------------- | ---------- | -------------------------------- |
| A-005 | Yellow toggle does not conflict with existing chat colors | Medium     | Visual regression                |
