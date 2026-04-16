# Knowledge — PLMO-1328

## PRD Summary

**Story:** Redesign Khách Tab as a Toggle and show Contextual Tooltip
**Jira:** PLMO-1328
**PRD:** CRM lead dashboard Adoption & Discovery — STORY-001

The Khách (Leads) tab on Chợ Tốt's CRM Lead Dashboard redesigns the Tin nhắn/Khách tab pair as a toggle with yellow color, and adds a contextual tooltip on the Khách tab that appears on first daily visit to Liên hệ after 12h when there are unread leads.

---

## pm_get_product_logic

### Current Business Rules — Khách Tab

| Aspect | Current State |
|--------|--------------|
| Tab toggle | Khách tab mounted at `/crm-leading` inside Chat shell (`src/components/AppShell/index.tsx`) |
| Experiment gating | Tab only visible to users in `CT-PLATFORM.ab_collect_lead_lead_pty` treatment; others redirected to `/chat` |
| Authentication | Auth check before rendering — unauthenticated users redirected to login |
| Unread badge | `UnreadCountProvider.tsx` merges chat + CRM unread totals |
| Badge calculation | On auth → `POST /api-platform/private/crm/unread_count:trigger_push` → sets `leadUnreadCountAtom` → socket event `crm_leads_unread_total_updated` updates atom |
| Lead read | Opening a lead via `IntentItem.tsx` marks it as read |
| Shell badge | Combined chat + CRM unread totals reflected in favicon and document title |

### Tooltip & First-Time Visitor Logic
**Not documented in knowledge hub.** No current implementation found.

---

## pm_analyze_feature_impact

### Direct Impact on Lead CRM

| Current Rule | Proposed Change | Consideration |
|---|---|---|
| Khách tab at `/crm-leading` with experiment guard `CT-PLATFORM.ab_collect_lead_lead_pty` | Toggle UI replaces tab design; route and experiment guard remain | Ensure toggle redirects correctly to `/crm-leading` |
| Unread counts via `leadUnreadCountAtom` + socket `crm_leads_unread_total_updated` | Tooltip reads same unread state | Tooltip unread count must match badge count |
| Shell/favicon badge shows combined chat + CRM totals | Yellow toggle adds new visual indicator | Yellow must be distinguishable from existing chat indicators |

### Cross-Feature Impact

- **Chat shell** (ct-web-uni-chat): Toggle redesign affects `AppShell/index.tsx` layout. Combined unread behavior must be preserved.
- **Communication / Socket layer**: `crm_leads_unread_total_updated` event drives unread state. Tooltip "first daily visit after 12h" trigger needs new state management.
- **Lead Management** (legacy): Low risk — separate "Khách hàng tiềm năng" surface; tooltip should reference "Khách tab" not legacy section.

### Risks

| Risk | Severity |
|------|----------|
| Yellow toggle color conflicts with existing chat unread/warning colors | Medium |
| "after 12h" trigger ambiguous — is it 12:00 PM local time, or 12h cooldown? | Medium |
| Tooltip state not synchronized across devices (client vs server storage) | Medium |
| Tooltip experiment gating unclear — should users outside treatment see tooltip? | Low-Medium |
| Tooltip dismissibility not specified | Low |

### Spec Gaps (from analysis)

- What is the "Liên hệ section" as a navigation target?
- Does "first daily visit after 12h" mean calendar-day (after 12:00 PM) or 24h-cooldown?
- Where is tooltip "shown today" state stored?
- Is tooltip experiment-gated alongside Khách tab?
- Can users permanently dismiss the tooltip?
- Is there a minimum unread threshold for showing the tooltip?

---

## engineer_search_api

### Known CRM Lead APIs (ct-api-uni-platform)

| Action | Endpoint | Method |
|--------|----------|--------|
| Load CRM leads | `GET /api-platform/private/crm/leads` | GET |
| Trigger unread push | `POST /api-platform/private/crm/unread_count:trigger_push` | POST |
| Mark lead as read | `POST /api-platform/private/crm/leads/:buyerId:mark_as_read` | POST |
| Reveal phone number | `GET /api-platform/private/crm/leads/:accountOid/phone-number` | GET |
| Start consultation chat | `POST /api-chat/v1/private/seller/channels` | POST |

### Socket Event
| Event | Purpose |
|-------|---------|
| `crm_leads_unread_total_updated` | Push updated CRM unread totals to Chat shell |

### Not Documented
- Tooltip/tracking state persistence API (proposed new feature)
- Toggle state API (proposed new feature)

---

## engineer_query_knowledge

### Services Involved

| Service | Role |
|---------|------|
| ct-web-uni-chat | Frontend — CRM page at `/crm-leading`, AppShell, UnreadCountProvider, CRMLeading components |
| ct-api-uni-platform | API Gateway — CRM lead endpoints |
| ct-logic-uni-platform | Backend — CRM lead orchestration, enrichment, ingestion |
| ct-core-uni-crm | Backend — Event-driven CRM core; emits `crm_leads_unread_total_updated` socket event |
| ct-api-chat-exp | Chat API — consultation channel creation |

### Data Flow
1. Seller authenticates → `UnreadCountProvider` calls `trigger_push` → sets `leadUnreadCountAtom`
2. Socket listener subscribes to `crm_leads_unread_total_updated`
3. New buyer activity → `ct-core-uni-crm` emits event → UI unread count updates
4. Seller opens lead → `mark_as_read` called → unread decrements

### Tooltip State (Proposed)
**No current implementation.** Requires new state management (client-side or server-side user preference store) to track:
- Whether tooltip was already shown today
- Last "Liên hệ" section visit timestamp
