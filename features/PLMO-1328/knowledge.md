# Knowledge Enrichment — PLMO-1328

**Jira ID:** PLMO-1328
**Summary:** Redesign Khách Tab as a Toggle and show Contextual Tooltip
**PRD:** Redesign "Tin nhắn"/"Khách" tab pair as equal-weight toggle with badge counts; show contextual tooltips on Liên hệ page; add "Xem liên hệ" CTA on ad listing cards; send daily morning push notification; add "Khách mới" widget on home screen.

---

## Context Block

**Change summary:** CRM lead discovery redesign — tab toggle redesign, contextual tooltips, new entry points from ad management, push notifications, home screen widget.

**Affected flows:** Lead discovery, tab navigation in CRM, ad-to-lead deep linking, push notification.

**Stories:**
- STORY-001: Tab redesign (yellow toggle, outline filter) + contextual tooltip on Liên hệ page for first-time daily visit
- STORY-002: Entry point from "Quản lý tin" to Lead Management ("Xem liên hệ" CTA on ad cards)
- STORY-003: Filter Leads by Ad in CRM
- STORY-004: "Khách mới" card/widget on seller's home screen ("Trang chủ")

**Known gaps:** Experiment treatment alignment for new entry points; subscription enforcement on new surfaces; empty state handling; push content spec; tooltip "first-time daily" definition.

---

## Section 1 — pm_get_product_logic (Worker 1)

Based on the knowledge hub documentation, here's a comprehensive overview of Chotot's lead management, CRM, and related features:

### Lead Management vs Lead CRM: Two Systems

**Lead Management** (Quản Lý Khách Hàng Tiềm Năng) — seller dashboard for tracking buyer inquiries from listings.
- Access: Only sellers with active subscription containing lead management add-on
- Leads filtered by subscription category (e.g., Property subscription → Property leads only)
- Contact status tracking: _Chưa liênệc_, _Đã liênệc_, _Lienệc sau_
- Follow-up reminders with alert times
- Schedule appointments with confirmation status

**Lead CRM** (Customer Workspace inside Chat) — newer integrated CRM inside Chat application.
- Tin nhắn tab (standard message inbox) + Khách tab (CRM customer workspace)
- Tracks buyer intent — latest demand signals, changed fields, interested ads
- Phone reveal as controlled action (not clear text by default)
- Seller-initiated consultation chat directly from CRM
- CRM unread counts pushed via socket events, not manual refresh
- Only appears for sellers in experiment treatment with CRM enabled

### Tab Navigation & Pair Toggle

When a seller opens the Chat app:
1. Web app checks authentication and rollout experiment
2. If CRM enabled → Khách tab rendered alongside normal inbox
3. Page fetches paginated CRM leads and initial unread count
4. Shell keeps CRM unread badges visible alongside chat unread counts

### Badge Counts & Notifications

| System | How Badge Counts Work |
|--------|---------------------|
| Lead Management | Status-based counts (Not Contacted, Contacted, Contact Later) update when sellers change lead status |
| Lead CRM | Unread totals increase when new buyer activity arrives, pushed via socket events to chat shell in real-time |
| Chat | Unread counts change when new messages arrive and clear when user opens the thread |

Notifications come from **Communication** platform. Follow-up reminders trigger alert-time notifications. New buyer activity in CRM triggers unread push events.

### Lead Discovery & User Flows

**Lead Management Discovery Flow:**
1. Seller clicks "Leads" or "Khách hàng tiềm năng" section
2. System checks for active lead management subscription
3. If subscription found → load lead dashboard filtered by category
4. If no subscription → show upgrade prompt or CTA

**Lead CRM Discovery Flow:**
1. Seller opens the chat application
2. Switches to the Khách tab (if CRM enabled in treatment)
3. Web app checks auth and experiment before rendering `/crm-leading`
4. Page fetches paginated CRM leads and unread count from platform CRM APIs
5. Seller can open lead drawer to inspect buyer profile, intent, interested ads
6. Seller can choose to call (reveal phone) or start consultation chat

**Lead Sources:**
- Lead form submissions — classic lead-registration flow (`make_lead_lms` contact intent)
- Interaction signals — buyer behaviors promoted into CRM (`make_lead_chat`, `make_lead_call`)
- Duplicate handling — repeated buyer interactions merge into one working record

### Constraints Summary

| Constraint | Lead Management | Lead CRM |
|-----------|----------------|----------|
| Access control | Subscription with lead-management add-on | Experiment treatment with CRM enabled |
| Category filtering | Leads filtered by subscription category | Leads consolidated across buyer interactions |
| Phone reveal | Visible by default | Controlled action, not clear text by default |
| Feature availability | Standalone dashboard | Inside Chat application only |

### Key Findings Relevant to PLMO-1328

1. **Current tab pair exists** — Tin nhắn/Khách tabs already co-exist in Chat. PRD wants to redesign as "equal-weight toggle with badge counts" — current UI may not treat them as equal weight.

2. **CRM access is experiment-gated** — "Lead CRM only appears for sellers in the experiment treatment with CRM enabled." Redesign must consider this two-tier user population.

3. **Badge counts are already push-driven** — CRM unread counts already flow via socket events. PRD's morning push notification is a new notification type on top of existing real-time pushes.

4. **Deep linking to filtered Khách tab** — Currently leads are consolidated. PRD's "Xem liên hệ" CTA from ad listing card would need to filter CRM leads by that specific ad.

5. **First-time daily visit tooltip** — Net new behavior not in current documentation.

6. **Khách mới widget on Trang chủ** — Home screen is separate from Lead CRM inside Chat. Net new entry point not in current lead discovery flows.

*Sources: knowledge/product/lead_management/overview.md, knowledge/product/lead_crm/overview.md, knowledge/product/chat/overview.md, knowledge/product/communication/overview.md*

---

## Section 2 — pm_analyze_feature_impact (Worker 2)

### What's Changing

Redesigns seller's lead discovery experience: equal-weight toggle tabs with badge counts, contextual tooltips for first-time daily visitors, new entry points from ad management, daily morning push notifications with lead summaries, and "Khách mới" widget on seller's home screen.

### Direct Impact on Lead CRM

**Business rules affected:**
- *Current rule:* "Lead CRM is surfaced from the chat application and only appears for the experiment treatment that has CRM enabled." — New home widget and ad-card CTAs would bypass the chat shell entry point, potentially creating access outside the experiment treatment.
- *Current rule:* "Unread counts must update when meaningful new buyer activity arrives and must be pushed to the seller UI." — Badge counts on equal-weight tabs mean unread accuracy becomes more critical; discrepancies between Chat and CRM badges would be more visible.
- *Current rule (Lead Management):* "Only sellers with an active subscription containing lead management add-on can access leads" — New entry points need subscription-aware rendering or will show leads to ineligible users.

**User flows affected:**
- *Opening CRM Customer Workspace* — Currently: Chat shell → Khách tab. New: Home widget → Direct deep link, or Ad management → "Xem liên hệ" → Lead list filtered by ad.
- *Lead discovery* — Currently passive (seller opens Chat). New: push-triggered (daily morning notification) and widget-driven (home screen card).
- *Working a Lead* — New ad-to-lead filtering enables jumping from ad card to leads specific to that listing.

### Cross-Feature Impact

**Lead Management** *(High severity)*
- Subscription-gated access with own lead status tracking. Sellers may see inconsistent counts between legacy Lead Management dashboard and new CRM if surface definitions differ.

**Communication** *(Medium severity)*
- Daily morning push notifications for lead summaries is a new scheduled campaign type. Spec doesn't address delivery rules (quota conflicts, what happens if seller already received pushes).
- First-time daily visit tooltips depend on correctly tracking "daily visit" state — if tracking fails, tooltips could show repeatedly or never.

**Chat** *(Medium severity)*
- Chat has own unread counts and badge behavior. Equal-weight toggles change visual hierarchy — currently Tin nhắn is primary.
- Spec doesn't address how combined unread counts work: Is shell badge the sum of both tabs? Does tapping shell badge default to one tab?

**Ad Listing / Insert Ads** *(Low severity)*
- "Quản lý tin" shows ad status, performance metrics, actions. Adding "Xem liên hệ" creates new navigation pattern.
- Spec doesn't address what "Xem liên hệ" shows for ads with no leads — empty state? Hide the button?

**App Wrapper** *(Low severity)*
- Home screen ("Trang chủ") already surfaces seller shortcuts, balances, premium-service entry points. Adding "Khách mới" widget competes for space.
- Spec doesn't address personalization rules: Does widget appear for all sellers or only those with unread leads? Only CRM experiment-enabled?

### Risk Assessment

| Risk | Severity | Description |
|------|----------|-------------|
| Experiment bypass | High | New home widget and ad-card CTAs may give sellers access to CRM leads outside experiment treatment, contaminating experiment group. |
| Subscription inconsistency | High | Sellers without lead management subscriptions could see leads through new entry points if subscription checks aren't enforced consistently. |
| Unread count divergence | Medium | Chat and CRM badges calculated separately; visible discrepancies create trust issues. |
| Push quota conflict | Medium | Daily lead-summary pushes could trigger delivery controls or suppress other operational messages if not carved out from normal quotas. |
| Empty state handling | Low | Ad cards with "Xem liên hệ" but no leads; home widget when no new leads — unclear what users see. |
| Tooltip state tracking | Low | Daily-visit tooltip depends on reliable "last visit" tracking; clock skew, multiple devices, or cookie clearing could cause incorrect behavior. |

### Spec Gaps & Open Questions

- **Experiment treatment alignment:** Should new entry points (home widget, ad CTAs, push notifications) respect existing CRM experiment treatment?
- **Subscription enforcement:** Should "Khách mới" widget and "Xem liên hệ" CTA appear for all sellers or only those with lead management subscriptions?
- **Unread badge aggregation:** When both tabs have unread activity, what does shell-level badge show? Sum? Max? Does tapping badge default to Tin nhắn or Khách?
- **Push notification content:** Count only? Top 3 leads with ad names? Does each lead tap deep-link to that specific lead?
- **Empty states:** What does "Xem liên hệ" show for ads with zero leads? What does "Khách mới" widget show when no new leads today?
- **Tooltip behavior:** What defines "first-time daily visit"? Calendar date in seller's timezone? 24-hour rolling window? Per-device or per-account?
- **Ad-to-lead filtering:** When tapping "Xem liên hệ" from an ad card, does it show ALL leads for that ad, or only unread? Does it respect existing lead management filters?
- **Legacy lead management coexistence:** How does this redesign relate to existing Lead Management feature? Are these being merged or remaining separate surfaces?

*Sources: lead_crm/overview.md, lead_management/overview.md, communication/overview.md, chat/overview.md, ad_listing/overview.md, app_wrapper/overview.md*

---

## Section 3 — engineer_search_api (Worker 3)

### CRM Lead APIs for Khách (Customers) Tab

**Request Architecture:** `ct-web-uni-chat` → `ct-api-uni-platform` → `ct-logic-uni-platform` → `ct-core-uni-crm`

**Gateway URL:** `https://gateway.chotot.org/api-platform/private/crm/*`

| Method | Endpoint | Purpose |
|--------|----------|---------|
| GET | `/api-platform/private/crm/leads` | Load paginated CRM leads for authenticated seller |
| POST | `/api-platform/private/crm/unread_count:trigger_push` | Trigger initial unread count push |
| POST | `/api-platform/private/crm/leads/:buyerId:mark_as_read` | Mark a lead as read |
| GET | `/api-platform/private/crm/leads/:accountOid/phone-number` | Reveal phone number for a lead |
| POST | `/api-chat/v1/private/seller/channels` | Start consultation chat |

**Key Methods (ct-logic-uni-platform):**
- `GetLeads` — Returns leads with buyer profiles, listing details, intent properties, contact intent labels (`make_lead_lms`, `make_lead_chat`, `make_lead_call`)
- `GetLead` — Single lead lookup by buyer ID with optional field exclusions
- `MarkLeadAsRead` — Updates read state and returns updated lead
- `UpdateNotes` — Updates seller notes on lead document
- `GetPhoneNumber` — Controlled phone reveal for buyer OID
- `TriggerUnreadCountPush` — Recalculates and pushes unread totals

### Lead Discovery API Response Shape

**Endpoint:** `GET /api-platform/private/crm/leads`

**Response Structure:**

| Field | Type | Description |
|-------|------|-------------|
| `leads` | `Lead[]` | Array of CRM lead records |
| `total` | `number` | Total count of leads for the seller |
| `total_unread` | `number` | Count of unread leads |

**Lead Object:**

| Field | Type | Description |
|-------|------|-------------|
| `buyer_id` | `string` | Buyer account identifier |
| `buyer_oid` | `string` | Buyer object ID |
| `buyer_name` | `string` | Buyer's full name |
| `is_read` | `boolean` | Whether seller has viewed this lead |
| `read_at` | `timestamp` | When lead was marked as read |
| `status` | `string` | Contact status (workflow state) |
| `notes` | `string` | Seller's personal notes about this lead |
| `phone_number` | `string?` | Buyer phone (revealed on demand) |
| `contact_intent` | `string` | Type: `make_lead_lms`, `make_lead_chat`, `make_lead_call` |
| `indicator_type` | `string` | Lead signal indicator type |
| `intent_updated_at` | `timestamp` | When buyer intent was last updated |
| `changed_fields` | `string[]` | Fields that changed in buyer intent |
| `interested_ads` | `InterestedAd[]` | Ads this buyer interacted with |

**InterestedAd Object:**

| Field | Type | Description |
|-------|------|-------------|
| `list_id` | `string` | Listing/ad identifier |
| `ad_title` | `string` | Listing title |
| `thumbnail` | `string` | URL to ad image |
| `category` | `number` | Ad category ID |
| `vertical` | `string` | Vertical name |
| `platform` | `string` | Source platform |
| `event_type` | `string` | Source event type |
| `event_time` | `timestamp` | When interaction occurred |

**Request Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `exclude` | `string[]` | Optional: exclude fields like `info`, `interested_ads` |
| `limit` | `number` | Pagination limit |
| `offset` | `number` | Pagination offset |

### Ad Listing & Lead APIs

**Gateway URL:** `https://gateway.chotot.org/api-uni-ads`

**Main Ad Management APIs:**

| Method | Endpoint | Purpose |
|--------|----------|---------|
| POST | `/InsertAd` | Create new ad listing |
| POST | `/LoadAd` | Load ad for editing |
| POST | `/EditAdInfo` | Edit existing ad |
| POST | `/HideAdByAdId` | Hide (deactivate) ad |
| POST | `/ProlongAdByAdId` | Prolong/renew ad |
| GET | `/GetDashboardAds` | Get seller's dashboard ads |
| POST | `/ListAdByState` | List ads by lifecycle state |

**Service Architecture:**

```
Ad Listing:
ct-web-uni-adlisting → ct-api-uni-ads → ct-logic-uni-ad-listing → ct-core-uni-ad-listing (Elasticsearch)

Lead Management:
ct-web-vertical-modules → ct-logic-uni → ct-core-uni-lead (PostgreSQL)

Lead CRM (Khách tab):
ct-web-uni-chat → ct-api-uni-platform → ct-logic-uni-platform → ct-core-uni-crm (MongoDB)
```

*Sources: knowledge/product/lead_crm/technical/ct-web-uni-chat.md, knowledge/product/lead_crm/technical/ct-api-uni-platform.md, knowledge/product/lead_crm/technical/ct-logic-uni-platform.md, knowledge/product/lead_crm/technical/ct-core-uni-crm.md, knowledge/product/ad_listing/technical/ct-api-uni-ads.md, knowledge/product/ad_listing/technical/overview.md*

---

## Section 4 — engineer_query_knowledge (Worker 4)

### Lead Management (Legacy Vertical CRM)

**Domain:** `vertical` (Vertical Team)

**Services:**
- `ct-web-vertical-modules` — Frontend React module for the lead dashboard
- `ct-api-uni-vertical` — Backend API for lead endpoints

**Data Flow:**
```
Seller → ct-web-vertical-modules → ct-api-uni-vertical → Lead storage
```

**Key APIs:**
- `GET /lead/number-of-lead-by-phone-status` — Filter leads by phone and status
- `GET /lead/get-lead-registration-grouped` — Get grouped lead registrations
- `PUT /lead/update-lead-registrations` — Update contact status, notes, alert times
- `PUT /lead/update-lead-contact-status` — Update lead contact status

**Access Control:** Gated by subscription with `lead_management` add-on

### Lead CRM (New Chat-Embedded CRM)

**Domain:** `platform` (Platform Team)

**Services:**

| Service | Role |
|---------|------|
| `ct-web-uni-chat` | Chat-embedded CRM customer workspace at `/crm-leading` |
| `ct-api-uni-platform` | Authenticated CRM API gateway |
| `ct-logic-uni-platform` | CRM orchestration, enrichment, and legacy-to-CRM sync worker |
| `ct-core-uni-crm` | Event-driven CRM core — consolidates leads, buyer intent, unread push |
| `ct-logic-uni` | Legacy lead HTTP orchestration and entitlement checks |
| `ct-core-uni-lead` | Legacy lead registration system of record |

**Data Flow:**

```
┌─────────────────────────────────────────────────────────────────┐
│                    Seller CRM Workspace                         │
│                      ct-web-uni-chat                            │
└────────────────────────────┬────────────────────────────────────┘
                             │ REST API
                             ▼
                   ct-api-uni-platform
                             │
                             ▼
                   ct-logic-uni-platform
                    │                    │
                    ▼                    ▼
            ct-core-uni-crm      Legacy Sync Worker
            (CRM system of         (Kafka consumer)
             record)                    │
                    │                    │
                    ▼                    ▼
           CRM docs + buyer    Kafka: Lead registration
           intent enrichment   topic ← ct-core-uni-lead
                    │                    ▲
                    │                    │
           Socket push               ct-logic-uni
         (unread totals)               (legacy lead
                                        orchestration)

Legacy Lead Form Flow:
Buyer → ct-logic-uni → ct-core-uni-lead → Kafka event
                                                    ↓
                                         ct-logic-uni-platform worker
                                                    ↓
                                              ct-core-uni-crm
```

### Key Cross-Service Flows

1. *Seller opens CRM tab:*
   `ct-web-uni-chat` → `ct-api-uni-platform` → `ct-logic-uni-platform` → `ct-core-uni-crm`

2. *New lead from legacy form:*
   Buyer submits form → `ct-logic-uni` → `ct-core-uni-lead` → Kafka → `ct-logic-uni-platform` worker → `ct-core-uni-crm`

3. *Seller follows up:*
   - Phone reveal: `PUT /api-platform/private/crm/leads/:accountOid/phone-number`
   - Start chat: `/api-chat/v1/private/seller/channels`

4. *Unread push:*
   `ct-core-uni-crm` → Socket event `crm_leads_unread_total_updated` → `ct-web-uni-chat`

### Summary

| System | Team | Core Services | Data Store |
|--------|------|---------------|------------|
| Lead Management (Legacy) | Vertical | `ct-web-vertical-modules`, `ct-api-uni-vertical` | Lead database via vertical API |
| Lead CRM (New) | Platform | `ct-logic-uni-platform`, `ct-core-uni-crm`, `ct-logic-uni`, `ct-core-uni-lead` | `ct-core-uni-crm` (MongoDB) + legacy `ct-core-uni-lead` |

*Sources: knowledge/product/lead_management/overview.md, knowledge/product/lead_management/technical/overview.md, knowledge/product/lead_crm/overview.md, knowledge/product/lead_crm/technical/overview.md, data/features.yaml (lines 363-382)*
