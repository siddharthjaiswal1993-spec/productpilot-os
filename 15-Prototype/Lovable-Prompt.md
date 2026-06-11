# Lovable Build Prompt: ProductPilot OS Prototype

**Use this prompt to build the ProductPilot OS V1 prototype in Lovable.dev.**

---

## Product Description

Build a professional B2B SaaS product called **ProductPilot OS** — an AI-powered product intelligence platform for product managers. It helps PMs synthesize customer signals from Slack, Zendesk, Gong, and Salesforce into evidence-backed opportunity briefs, prioritization decisions, PRD drafts, and stakeholder communications.

The design style is: clean, professional, dark sidebar, light content area. Think Linear meets Notion meets Notion AI. No consumer-app vibes. This is a tool for senior product managers at enterprise SaaS companies.

---

## Pages to Build

### Page 1: Signal Intelligence Hub (Main Dashboard)

**Left sidebar:**
- ProductPilot OS logo (top)
- Navigation items: Signal Hub, Briefs, Prioritization, PRDs, Risk Alerts, Stakeholder, Settings
- Current user avatar (bottom)

**Main content area:**
- Page title: "Signal Intelligence Hub"
- Top row of stats: "247 signals this week | +18% vs last week | 3 trending categories | 2 active risk alerts"
- Filter bar: "All Sources | Feature Request | Bug Report | Churn Risk | Competitive | Date range"
- Signal feed (list of cards). Each signal card shows:
  - Source logo (Slack, Zendesk, Gong, Salesforce icons)
  - Signal type badge (e.g., "Feature Request" in blue, "Bug Report" in red)
  - First line of signal text (truncated)
  - Author name, source channel/account, date
  - Classification confidence percentage
- On the right side: a highlighted "Cluster" panel showing "6 signals clustered around: SSO / SCIM Integration" with a "Generate Brief" button

**Sample signals to show:**
1. Zendesk — "Enterprise SSO Request from Acme Corp — customer cannot proceed without SCIM provisioning..."
2. Gong (Sales call) — "Acme deal: stalled on SSO. Roberto: 'They won't sign until SSO is on the roadmap.'"
3. Slack (#customer-success) — "Getting 3-4 SSO questions per week now. Mostly from enterprise accounts."
4. Salesforce — "Opportunity Note: NovaSystems - SSO requirement listed as deal blocker."
5. Zendesk — "GlobalTech ticket: SCIM provisioning needed for IT compliance"
6. Slack (#sales) — "Just got off with PinePoint. SSO + SCIM is their #1 requirement before Q3 signature."

---

### Page 2: Opportunity Brief (Generated)

This is the core product experience. A generated opportunity brief for "Enterprise SSO / SCIM Integration."

**Header:**
- "AI Draft" badge (yellow)
- Title: "Enterprise SSO / SCIM Integration — Opportunity Brief"
- Confidence score badge: "87/100 — High Confidence" (green)
- Buttons: "Edit", "Reject", "Approve Brief" (primary blue button)

**Brief content (structured sections):**

**Problem Statement:**
"Enterprise customers require SSO (Single Sign-On) and SCIM (System for Cross-domain Identity Management) provisioning to meet their IT security and compliance requirements. Without these capabilities, enterprise deals are stalling and existing enterprise accounts cannot expand their seat count."

**Evidence Table (key section):**
A table with columns: Source | Type | Author | Date | Excerpt | Strength

Rows:
1. Zendesk | Support Ticket | Aisha Johnson | Mar 1 | "Our enterprise customers are asking about SSO. 4 open tickets this week." | ●●●●○
2. Gong | Sales Call | Roberto Diaz | Mar 5 | "Acme deal: They won't sign until SSO is on the roadmap." | ●●●●●
3. Salesforce | Opp. Note | Roberto Diaz | Feb 28 | "NovaSystems: SSO requirement listed as deal blocker. $580K ACV at risk." | ●●●●●
4. Slack | CS Channel | Wei Zhang | Mar 8 | "Getting 3-4 SSO questions per week. Mostly enterprise accounts." | ●●●○○
5. Zendesk | Support Ticket | Aisha Johnson | Mar 3 | "GlobalTech: SCIM provisioning required for IT compliance" | ●●●●○

Each row has a small expand icon (>) that opens the full citation detail.

**Confidence Score (expandable):**
"87/100 — High Confidence"
Bar chart showing:
- Signal Volume: 17/20
- Source Diversity: 16/20
- Recency: 18/20
- Business Impact Coverage: 20/20
- Representativeness: 16/20

**Business Impact:**
"$2.1M ARR at risk — 5 enterprise accounts have identified SSO/SCIM as a deal blocker or expansion prerequisite."

**Preliminary RICE:**
"RICE Score: ~11,900 (preliminary — full scoring in Prioritization step)"

**Next Steps (after approval):**
"Score for Prioritization | Generate PRD"

---

### Page 3: Prioritization Brief

**Header:**
- Title: "Enterprise SSO/SCIM — Prioritization Brief"
- "AI Draft" badge
- "Approve Priority Decision" button (primary)

**RICE Breakdown:**
A clean table:
| Dimension | Score | Evidence | |
|-----------|-------|----------|--|
| Reach | 800 users/Q | "5 enterprise accounts, avg 160 users per account" [Source 1] | ✓ Cited |
| Impact | 3 (High) | "Deal blocker status: direct impact on conversion" [Source 2] | ✓ Cited |
| Confidence | 87% | "Strong multi-source evidence base" | ✓ Cited |
| Effort | 6 weeks | "[Engineering estimate required — default applied]" | ⚠ Estimate |
| **RICE Score** | **11,963** | | |

**Strategic Alignment:**
"9.2/10 — Directly supports OKR: 'Expand into enterprise accounts — target 20% revenue from enterprise by Q3'"

**Backlog Comparison:**
A table showing the top 5 backlog items with their RICE scores, with this new item highlighted as the highest-scoring.

---

### Page 4: PRD Draft (Partial View)

**Header:**
- "AI Draft" badge (yellow throughout)
- Title: "Enterprise SSO / SCIM Integration — Product Requirements"
- Progress indicator: "Section review: 2 of 7 sections marked reviewed"
- "Approve for Engineering" button (disabled until all sections reviewed)

**Section: Problem Statement** (marked as reviewed — green checkmark)

**Section: User Stories** (expanded):
Three user stories with citation panels:

Story 1: "As an IT admin at an enterprise customer, I want to configure SSO through our corporate identity provider (Okta/Azure AD) so that my team members can access ProductPilot without separate credentials."
Citation panel: Zendesk ticket 12847, Gong call 8847

Story 2: "As a PM admin, I want to provision and deprovision team members automatically via SCIM so that we maintain access control compliance without manual user management."
Citation panel: Salesforce note, GlobalTech Zendesk ticket

**Section: Edge Cases** (collapsed — click to expand)
"Suggested: 4 edge cases identified from knowledge graph"

---

### Page 5: Risk Alert View

**Alert banner at top:** "⚠ P2 Risk Alert — Webhook Integration Failures | 14 enterprise accounts affected | $1.2M ARR at risk"

**Risk Brief:**
- "Volume spike: +62% vs. 7-day baseline"
- Release correlation: "Correlated with v2.4.1 release (March 10)"
- Affected accounts table with ARR
- 3 mitigation options with buttons: "Revert", "Hotfix", "Monitor + Notify"
- "Escalate to Engineering" button (requires separate confirmation)

---

## Design Specifications

**Colors:**
- Background: #FAFAFA (light gray)
- Sidebar: #1A1A2E (dark navy)
- Primary action: #4A6FFF (blue)
- Success/approved: #22C55E (green)
- Warning/draft: #EAB308 (yellow)
- Risk/alert: #EF4444 (red)
- Text primary: #1A1A1A
- Text secondary: #6B7280

**Typography:**
- Headings: Inter or Geist, 20–28px, semibold
- Body: Inter, 14–16px, regular
- Code/citations: Mono font, 13px

**Components:**
- Cards with subtle border (1px, #E5E7EB) and light shadow
- Evidence table with alternating row shading
- Citation badges: small superscript numbers, blue
- Confidence bars: colored progress bars
- Source logos: 16x16px icons (Slack, Zendesk, Salesforce, Gong) next to all citations

---

## Interaction Design

- **Clicking a citation superscript:** Opens a right-side panel without navigating away
- **"Approve" button:** Turns green, "AI Draft" badge disappears, approval record visible
- **Evidence table row expand:** Inline expansion showing full source details
- **Confidence score click:** Expands to show component breakdown in a modal or inline section
