# Problem Statement

The "Khách" (Leads) tab on Chợ Tốt's CRM Lead Dashboard is live in A/B testing, but **only ~50% of PTY sellers in the treatment group are aware of the feature**

==> Sellers miss leads, reducing consultation rate and undermining the CRM value prop

No habit loop, even sellers who discover the Khách tab don't return daily

**Why seller don't discovery "Khách"**

* The current "Tin nhắn"/ "Khách" tab design looks like a filter, not a primary navigation switch
* No onboarding moment - There is not first time education 
* While the Ad Dashboard ranks as a top entry point for pro-sellers' sessions alongside chat and ad_view, there is currently no direct link from ad management to the leads management page.

# Proposed Solution

Problem: Low awareness and No discovery path

* Redesign the tab pair as a **toggle** : "Tin nhắn"  and "Khách"  are equal-weight options with badge count
* Show **tooltips** on Liên hệ page to 
* Add a **"Xem liên hệ" (View Contacts)** CTA button on each ad listing card in Quản lý tin that deep-links to the Khách tab filtered by that ad's leads.
* Send a **daily morning push notification** summarizing new leads from the past 24 hours, driving sellers to open the Khách tab.
* Add a **"Khách mới" card/widget** on the seller's home screen ("Trang chủ") that shows a snapshot of recent leads and links to the Khách tab.


| Story ID | **User story** | **Desc** | **UI/UX** |
| --- | --- | --- | --- |
| STORY-001 | As a seller, when I access the "Liên hệ" section, I want to immediately notice key updates (messages and leads) through **high-contrast design** and **tooltips** so that I can focus on high-priority tasks without missing potential deals. | Redesign Khách Tab as a Toggle and show Contextual Tooltip **Design update:** Mobile: Use the color **YELLOW** and toggle design to make the "Khách" tab look more attractive and eye-catching Filter label change to outline style design Msite: the same as Mobile **Tooltips:** Trigger show: Show tooltip for "Khách" tab when: There are unread leads And seller stay on the "Tin nhắn" tab And this is the **first time visit "Liên hệ" section of each day (after 12h)** User click the tooltip → navigate to "Khách" tab Tooltip mark as shown when: Has been shown Seller has been in "Khách" tab Content:  "X khách mới đang đợi bạn Nhấn để xem thông tin và tư vấn ngay" The number of lead in tooltip align with the badge number of "Khách" tab   | Ticket: https://701search.atlassian.net/browse/PLMO-1224  Design: https://www.figma.com/design/d0PLCcgixlUoQS2zdrpnkO/Platform-Handoff-%E2%80%A2-2026?node-id=4474-5144&t=FIZsu1yiH8XHPLEY-4 |
| STORY-002 | As a seller  reviewing my ad performance in "Quản lý tin",  I want to see how many and who are the potential buyers that contacted me per ad so I can evaluate each ad's effectiveness and follow up with interested buyers directly | Entry Point from "Quản lý tin" to Lead Management | |
| STORY-003 | As a PTY seller with multiple active ads, I want to filter my lead list by a specific ad   so I can focus on buyers interested in a particular property and consult them more effectively. |  Filter Leads by Ad | Ticket: https://701search.atlassian.net/browse/PLMO-1237 |
| STORY-004 | | **"Khách mới" card/widget** on the seller's home screen | |
