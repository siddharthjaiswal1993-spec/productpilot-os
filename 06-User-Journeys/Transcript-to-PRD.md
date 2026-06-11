# Journey: Transcript to PRD

## Overview

**Trigger:** PM completes a 45-minute discovery call with a key enterprise prospect and receives 2 additional customer interview recordings from the CS team. All three contain detailed product requirements for a new feature area: advanced reporting and custom dashboards.

**Who is involved:** Meeting Intelligence Agent, Insight Synthesizer Agent, PRD Agent, PM (review and approval)

**Outcome:** A structured PRD draft with user stories, acceptance criteria, edge cases, and cited customer quotes — generated from the transcript evidence in under 5 minutes of agent processing time.

---

## Before ProductPilot OS

**Day 1 (after call):** PM takes raw notes during the call. The notes are fragmented ("want custom date ranges," "need to export to PDF," "their analyst team uses Tableau — need API access"). 30 minutes of note cleanup after the call.

**Day 2:** PM listens to 2 additional customer interview recordings from CS (90 minutes total). Takes more fragmented notes. Cross-references with Day 1 notes to find patterns. 2.5 hours.

**Day 3:** PM synthesizes notes into a draft problem statement and user stories. 1.5 hours.

**Day 4:** PM writes the full PRD in Confluence — problem statement, user stories, acceptance criteria, out-of-scope items. 3–4 hours. No citations. Uses memory for customer quotes ("one customer said something about Tableau integration but I don't remember exactly what they said").

**Day 5:** PM shares PRD with engineering for review. Engineering asks: "Which customer said they need PDF export? Can we get more context?" PM doesn't have a citation and has to re-listen to the call. 1 hour.

**Total time:** 9–10 hours over 5 days. No citations. Customer quotes are paraphrased from memory.

---

## With ProductPilot OS

**Day 1 (4:30pm — 30 minutes after the call):** PM opens Meeting Intelligence interface. Uploads the Gong call recording and pastes links to the 2 CS interview recordings.

**Day 1 (4:31pm):** Meeting Intelligence Agent begins processing. Status: "Transcribing and extracting requirements from 3 recordings (total: 135 minutes)."

**Day 1 (4:38pm — 7 minutes later):** Meeting Intelligence outputs:

- **Requirements extracted:** 18 distinct functional requirements across 3 interviews
- **Customer quotes:** 12 verbatim quotes, each timestamped and attributed to speaker and recording
- **Feature themes:** Grouped into 4 themes: Date/time customization, Export formats, API access, Dashboard layout customization
- **Signals extracted:** 3 new signals added to knowledge graph (enterprise customer requirements)
- **Action items detected:** 2 follow-up commitments made during calls

PM reviews extracted requirements. Sees they're well-grouped and cites the key quotes. Approves the extraction and clicks "Generate PRD."

**Day 1 (4:40pm):** PRD Agent begins. Retrieves:
- The 18 extracted requirements from today's calls
- 6 related requirements from the knowledge graph (previous customer signals about reporting)
- 4 related Zendesk tickets mentioning reporting limitations
- Engineering constraints note from a past Jira spike on dashboard performance
- Team's PRD template (pre-configured)

**Day 1 (4:43pm — 3 minutes later):** PRD draft ready.

---

## The Generated PRD Draft

**Title:** Advanced Reporting and Custom Dashboard — PRD v0.1 (AI Draft — Pending PM Review)

**Problem Statement:**
Enterprise analytics and operations teams need customizable reporting capabilities to integrate product data into their existing workflows and tools. Current reporting is fixed in format and limited in export options, preventing teams from adapting outputs to their internal processes.

Evidence: [3 customer interviews, May 15] [4 Zendesk tickets, April–May] — *cited inline*

**Scope:**
- **In scope:** Custom date ranges, scheduled report delivery, PDF/CSV/Excel export, API endpoint for report data retrieval, 3 saveable dashboard layouts, Tableau/Looker data connector
- **Out of scope (V1):** Drag-and-drop widget customization, custom formula fields, real-time streaming dashboards, white-labeling

**User Stories (12 stories, 3 shown here):**

> **US-01:** As an enterprise analyst, I want to set custom date ranges for any report so that I can analyze periods relevant to my fiscal calendar — not just the last 7/30/90 days.
>
> *Evidence: "Our fiscal year ends in October, so Q4 for us is August–October. Your current 'last quarter' option doesn't match our calendar at all." — [CTO, TechCorp, Gong call May 15, 0:14:23]*

> **US-04:** As a BI team member, I want to pull report data via API so that I can load it into our Tableau dashboards without manual export steps.
>
> *Evidence: "We have a Tableau environment that all our data flows into. If we can't connect your data via API, we'll need to export and re-import manually every day — that's not sustainable at our scale." — [Data Lead, InnovateCo, CS interview May 12, 0:08:47]*

> **US-09:** As an operations manager, I want to schedule automated report delivery to email so that I don't have to log in and run reports manually each morning.
>
> *Evidence: 3 customers (TechCorp, DataCore, Meridian Group) explicitly requested scheduled delivery — [Zendesk tickets #18901, #17432, #19204]*

**Acceptance Criteria (sample):**

> **AC-01:** Custom date range — User can select any start and end date (up to 24 months of history). Range selection validates that start < end. Range applies to all metrics in the selected report. Custom range is saveable as a named preset.

**Edge Cases (8 identified):**
- Date range spans a data migration: report returns partial data with a warning banner
- Export requested for >1M rows: system chunks into multiple files with index file
- API rate limit exceeded: returns 429 with Retry-After header
- Scheduled report delivery fails (email bounce): logged in audit, PM notified, retry attempted after 1 hour

---

## Human Approval and Editing

**PM review time:** 22 minutes.

Edits made:
1. Added out-of-scope item: "Embedded iframe reporting (requested separately)"
2. Updated US-07 acceptance criteria based on engineering discussion memory
3. Removed 1 extracted requirement (duplicate of existing feature, PM recognized)
4. Approved 11 of 12 user stories without change

**Total PM time from recording upload to approved PRD draft:** 32 minutes (7 min extraction + 3 min PRD generation + 22 min review/edit).

---

## Time Comparison

| Activity | Before | After | Improvement |
|----------|--------|-------|------------|
| Transcript processing | 3 hours (manual notes) | 7 minutes (automated) | 97% |
| Requirements synthesis | 2 hours | Included above | 100% |
| PRD first draft | 4 hours | 3 minutes (generated) | 99% |
| Citation lookup | 1 hour (re-listening) | 0 (auto-cited) | 100% |
| **Total PM time** | **9–10 hours over 5 days** | **32 minutes Day 1** | **95%** |

---

*Three customer recordings became a cited, structured PRD draft before the end of the same afternoon.*
