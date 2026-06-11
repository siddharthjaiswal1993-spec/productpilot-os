# Acceptance Criteria: ProductPilot OS V1

**Format:** AC-[ID] | User Story | Given/When/Then

---

## Signal Ingestion

**AC-001 — Slack signal ingestion**
- **Given** a connected Slack workspace with channel access configured
- **When** a message is posted in a monitored channel
- **Then** the message appears as a classified signal in the Signal Intelligence Hub within 15 minutes

**AC-002 — Signal classification accuracy**
- **Given** a newly ingested Slack message containing "customers keep asking for bulk export, we should prioritize this"
- **When** the classification pipeline runs
- **Then** the signal is classified as "feature request" with confidence >80%; the product area tag matches "export" or "data"; the signal appears in the PM's feed

**AC-003 — Volume trend detection**
- **Given** a PM has signal category "integration errors" with a baseline of 5 signals per week for 3 weeks
- **When** 8 new signals of type "integration error" arrive in a 7-day period
- **Then** the system flags this category as "trending" with a "+60% vs prior week" indicator; the PM receives an in-app notification

---

## Opportunity Brief Generation

**AC-004 — Brief generation from signal cluster**
- **Given** a PM selects a cluster of 6 signals related to "export performance" and clicks "Generate Brief"
- **When** the Insight Synthesizer Agent runs
- **Then** a structured opportunity brief is ready within 90 seconds; the brief contains: problem statement, evidence table with ≥3 cited items (source/author/date/excerpt), confidence score with component breakdown, business impact estimate, and preliminary RICE score

**AC-005 — Citation completeness**
- **Given** an opportunity brief has been generated
- **When** the PM reviews the evidence table
- **Then** every evidence row contains: source system name, author or originator identifier, date, and verbatim excerpt or metric; no evidence row is missing any of these fields

**AC-006 — Confidence score display**
- **Given** an opportunity brief is displayed
- **When** the PM expands the confidence score
- **Then** the PM sees: a composite score (0–100), individual component scores (signal volume, source diversity, recency, business impact coverage, representativeness), and a one-sentence interpretation

**AC-007 — PM approval gating**
- **Given** an opportunity brief has been generated but not yet approved by the PM
- **When** the PM attempts to click "Generate PRD" or "Notify Stakeholders"
- **Then** both actions are disabled; an explanatory message is displayed: "Approve this brief before proceeding"

**AC-008 — PM edit and re-score**
- **Given** a PM edits the business impact estimate in an opportunity brief
- **When** the edit is saved
- **Then** the composite priority score recalculates automatically; the edit is logged in the brief's edit history with PM name, timestamp, and field changed

---

## Prioritization Engine

**AC-009 — Prioritization scoring**
- **Given** an opportunity brief is approved and the PM clicks "Score for Prioritization"
- **When** the Prioritization Agent runs
- **Then** a prioritization brief is ready within 60 seconds; it contains: RICE breakdown with evidence citations, strategic alignment score with OKR mapping, and composite priority score

**AC-010 — Backlog comparison**
- **Given** a Jira backlog is connected and a prioritization brief is generated
- **When** the PM views the comparison table
- **Then** the table shows the new item's scores alongside the top 10 backlog items, sorted by composite score; the suggested backlog placement is highlighted

**AC-011 — Jira priority update approval gate**
- **Given** the PM has approved a priority decision that changes an item's Jira priority
- **When** the system attempts to write the priority to Jira
- **Then** a confirmation dialog is shown: "This will change [Item] from P2 to P1 in Jira. Confirm?" — Jira is not updated until PM clicks "Confirm"

---

## PRD Copilot

**AC-012 — PRD generation from approved brief**
- **Given** an opportunity brief has been PM-approved
- **When** the PM clicks "Generate PRD"
- **Then** a full PRD draft is ready within 3 minutes; the draft includes: problem statement, user stories (≥5), acceptance criteria for each user story, out-of-scope items, and success metrics — all formatted to the team's PRD template if configured

**AC-013 — User story citation**
- **Given** the PRD draft has been generated
- **When** the PM opens any user story
- **Then** a citation panel shows the customer signal(s) that motivated this user story, with source, author, date, and excerpt — identical to the evidence table format in the opportunity brief

**AC-014 — Edge case suggestions**
- **Given** the PRD draft has been generated for a feature in the "authentication" product area
- **When** the PM scrolls to the edge cases section
- **Then** ≥3 edge cases are suggested; each edge case includes: scenario description, expected system behavior, and a note on the source (knowledge graph historical data or LLM inference clearly labeled)

**AC-015 — PRD approval gating**
- **Given** the PM has reviewed the PRD draft but not yet clicked "Approve"
- **When** the PM attempts to share the PRD via the "Share with Engineering" option
- **Then** the share option is disabled; message displayed: "Approve PRD before sharing. All edits will be version-tracked after approval."

---

## Risk Watchdog

**AC-016 — Volume spike alert**
- **Given** support ticket volume for "webhook failures" increases from 8 to 14 in 7 days (75% increase, above 30% threshold)
- **When** the Risk Watchdog daily analysis runs
- **Then** a P2 risk alert is generated; PM receives in-app notification within 15 minutes; risk brief generated within 90 seconds of alert trigger

**AC-017 — Risk brief affected accounts**
- **Given** a risk alert has been triggered for "webhook failures"
- **When** the PM opens the risk brief
- **Then** the brief displays: a table of accounts with active webhook-related support tickets, their ARR, renewal date, and whether they have SLA provisions; total ARR at risk is displayed prominently

**AC-018 — Escalation approval gate**
- **Given** the PM has reviewed a risk brief and selects "Escalate to Engineering (create Jira ticket)"
- **When** the PM clicks the escalation option
- **Then** a preview of the Jira ticket to be created is displayed for PM review; the ticket is not created until PM clicks "Confirm"; the Jira ticket creation is logged in the audit trail

---

## Stakeholder Alignment

**AC-019 — Personalized draft generation**
- **Given** a PM has 3 stakeholder audiences configured (Engineering, Sales, Executive)
- **When** the PM approves a priority decision and clicks "Generate Stakeholder Updates"
- **Then** 3 distinct communication drafts are generated within 60 seconds (parallel generation); each draft references the same evidence base but uses audience-appropriate framing; Engineering draft includes technical scope; Sales draft includes commercial context; Executive draft includes business impact

**AC-020 — Communication approval gate**
- **Given** stakeholder communication drafts have been generated
- **When** the PM views the draft for the Sales audience
- **Then** the draft is clearly labeled "Draft — Pending PM Approval"; a "Send" or "Copy" action is available only after the PM clicks "Approve This Draft" for the specific audience

---

## Audit Log

**AC-021 — Agent invocation logging**
- **Given** a PM triggers an opportunity brief generation
- **When** the brief is generated and the PM approves it
- **Then** the audit log contains two entries: (1) agent invocation — timestamp, agent name, input hash, output hash, duration; (2) PM approval — timestamp, PM name, edit summary, approval status

**AC-022 — Audit log immutability**
- **Given** an audit log entry has been created
- **When** an admin user attempts to delete or modify the entry via any available interface
- **Then** the delete or modify operation is rejected; an error is returned: "Audit log entries are immutable"

---

*Acceptance criteria are the contract between PM and engineering. Any implementation that does not satisfy the Given/When/Then must be considered incomplete, regardless of implementation quality.*
