# Screens and Workflows

## Screen Inventory

| Screen | Priority | Status |
|--------|----------|--------|
| Signal Intelligence Hub | P0 | Prototype required |
| Opportunity Brief (generated + approval) | P0 | Prototype required |
| Prioritization Brief | P0 | Prototype required |
| PRD Draft (view + section approval) | P0 | Prototype required |
| Risk Alert + Risk Brief | P1 | Prototype required |
| Stakeholder Communication Drafts | P1 | Prototype required |
| Signal Detail View | P1 | Wireframe |
| Confidence Score Expansion | P1 | Prototype required |
| Citation Panel (right sidebar) | P0 | Prototype required |
| Audit Trail View | P2 | Wireframe |
| Settings / Integration Config | P2 | Wireframe |
| Onboarding Flow | P1 | Wireframe |

---

## Screen 1: Signal Intelligence Hub

**URL:** `/signals`

**Key UI elements:**
- Stats row at top: total signals, week-over-week change, trending categories, active risk alerts
- Filter toolbar: source type, signal type, date range, product area
- Signal feed (left): chronological list of signal cards
- Cluster panel (right): auto-detected clusters with "Generate Brief" CTA
- Trending indicator: category showing >30% volume increase, highlighted with orange border

**Primary user journey from this screen:**
PM scans feed → spots cluster panel → clicks "Generate Brief" → navigates to Brief Generation (loading state → Brief view)

**Key design decisions:**
- Cluster panel is proactively displayed (PM doesn't need to manually group signals)
- Signal cards are skimmable: source + type + first line + date visible without expansion
- Trending category badge uses volume percentage, not absolute count ("↑62% vs. 7-day baseline")

---

## Screen 2: Opportunity Brief

**URL:** `/briefs/:brief_id`

**UI layout:**
- Top: Brief title + "AI Draft" badge + confidence score badge
- Left/main: Brief content (structured sections)
- Right: Citation panel (slides in on citation click)
- Bottom action bar: "Reject | Edit | Approve Brief"

**Sections:**
1. **TL;DR (3 lines max):** Problem + evidence strength + recommended action
2. **Problem Statement:** 1–2 paragraphs with inline citations
3. **Evidence Table:** Expandable; collapsed by default (shows row count)
4. **Confidence Score:** Collapsed; shows composite score; expandable for component breakdown
5. **Business Impact:** ARR at risk + accounts affected + pipeline implications
6. **Preliminary RICE:** Score with dimension breakdown
7. **Recommended Next Step:** Clear call to action (Prioritize / Research further / Defer)

**Citation panel (right sidebar, appears on citation click):**
- Source type + logo
- Author name
- Date
- Full excerpt
- Relevance score
- Link to original (if available)
- "Mark as incorrect" button

**States:**
- `draft`: Yellow "AI Draft" badge visible; "Approve" button prominent
- `approved`: Badge removed; "Approved by [PM name] on [date]" shown; downstream buttons unlocked
- `rejected`: Red "Rejected" badge; rejection reason shown; "Regenerate" option visible

---

## Screen 3: Prioritization Brief

**URL:** `/briefs/:brief_id/prioritization`

**UI layout:**
- Top: Brief title + "AI Draft" badge + RICE score
- Tabs: RICE Breakdown | Strategic Alignment | Backlog Comparison
- Bottom action bar: "Override | Approve Priority Decision"

**RICE Breakdown tab:**
- Table: Dimension | Value | Evidence | Status
- Each evidence cell shows the source snippet with citation number
- Dimensions with estimates (not cited) show ⚠ icon with tooltip
- PM can click "Override" on any dimension; opens inline edit field with required reason field

**Strategic Alignment tab:**
- OKR list with alignment score per OKR (0–1 scale shown as percentage)
- Most-aligned OKR highlighted
- Weighted total alignment score

**Backlog Comparison tab:**
- Ranked list of top 10 backlog items + new item
- New item highlighted with blue border
- Suggested placement indicated with "Suggested: Position 1" label
- Current Jira priority vs. suggested priority shown for items that would change

---

## Screen 4: PRD Draft

**URL:** `/prd/:prd_id`

**UI layout:**
- Left: Document outline (section names with review checkmarks)
- Main: PRD content in editable sections
- Right: Evidence panel (section-specific evidence for the focused section)
- Top: "AI Draft" badge + "Approve for Engineering" button (disabled until all sections reviewed)

**Section review flow:**
- PM scrolls through section
- "Mark as Reviewed" button at bottom of each section
- Section header shows green checkmark once reviewed
- "Approve for Engineering" button activates when all required sections reviewed

**User story section:**
- Each user story is an expandable card
- Expanded view: user story text + citation count + "View Evidence" link
- Evidence panel on right shows all citations for the focused story

**Edge cases section:**
- Each edge case is an expandable card
- Source label: "From knowledge graph" or "LLM-generated inference" (labeled clearly)
- "Accept" and "Reject" buttons for each edge case

---

## Screen 5: Stakeholder Communication Drafts

**URL:** `/decisions/:decision_id/communications`

**UI layout:**
- Left: Audience tab list (Engineering, Sales, CS, VP/Exec, PM Team)
- Main: Draft content for selected audience
- Bottom: "Reject | Edit | Approve This Draft" per audience

**Audience tabs:**
- Tab shows audience name + approval status icon (draft / approved / rejected)
- Switching tabs switches the draft content; no page navigation

**Per-draft elements:**
- "AI Draft" badge visible until approved
- Draft content in editable text area
- Evidence footnotes at bottom (collapsed by default)
- Audience framing note (e.g., "Engineering framing: technical scope and timeline")

---

## Primary Workflow: End-to-End

```
Signal Hub (browse feed)
    │
    ▼
Cluster Panel → "Generate Brief"
    │
    ▼
Brief loading state (spinner, "Generating with evidence from 6 signals...")
    │
    ▼
Opportunity Brief (AI Draft)
  ├── Review evidence table
  ├── Click citations → Citation panel
  ├── Expand confidence score
  └── Click "Approve Brief"
         │
         ▼
    Brief status → Approved
    Downstream buttons unlock: "Score for Prioritization" | "Generate PRD"
         │
         ▼
    Prioritization Brief (AI Draft)
    ├── Review RICE breakdown
    ├── Override effort dimension
    └── Click "Approve Priority Decision"
             │
             ▼
        Decision recorded
        "Generate Stakeholder Updates" available
             │
             ▼
        Stakeholder Drafts (×3 audiences)
        ├── Review Engineering draft → Approve
        ├── Review Sales draft → Edit → Approve  
        └── Review VP draft → Approve
```
