# Functional Requirements: ProductPilot OS V1

**Format:** FR-[ID] | Module | Requirement | Priority | Acceptance Test

---

## Signal Intelligence Hub

| ID | Requirement | Priority | Acceptance Test |
|----|------------|---------|----------------|
| FR-001 | The system shall ingest signals from Slack channels via the Events API in <15 minutes of posting | P0 | Slack message appears in signal feed within 15 minutes of sending |
| FR-002 | The system shall ingest signals from Jira via webhooks within 5 minutes of ticket creation or status change | P0 | Jira ticket appears in signal feed within 5 minutes |
| FR-003 | The system shall ingest signals from Zendesk and Intercom within 10 minutes of ticket creation or update | P0 | Zendesk ticket appears in signal feed within 10 minutes |
| FR-004 | The system shall ingest CRM notes and opportunity changes from Salesforce and HubSpot on an hourly polling cycle | P1 | CRM note created within the last hour appears in signal feed |
| FR-005 | The system shall ingest Gong call transcripts within 30 minutes of call completion | P1 | Gong call transcript available in signal corpus within 30 minutes |
| FR-006 | The system shall classify each ingested signal into one of: feature request, bug report, churn risk, competitive signal, technical risk, general feedback | P0 | Classification accuracy >85% on human eval sample of 100 signals |
| FR-007 | The system shall detect duplicate signals (same issue, different sources) and display them as a linked cluster | P1 | Two Zendesk tickets about the same issue show as a cluster in the signal feed |
| FR-008 | The system shall calculate a signal trend score indicating volume change over the past 7 days vs. prior 7 days | P1 | Signal with 10 items this week vs. 4 last week shows a "+150%" trend indicator |
| FR-009 | The system shall allow the PM to manually submit a signal from any source not yet integrated | P1 | PM can submit a free-text signal with source and date; it appears in the signal feed |
| FR-010 | The system shall display a daily digest of the top 10 signals by priority score in the PM's inbox | P1 | Daily digest email/notification contains 10 signals, sorted by composite priority score |

---

## Opportunity Brief Engine

| ID | Requirement | Priority | Acceptance Test |
|----|------------|---------|----------------|
| FR-011 | The system shall generate an opportunity brief from a PM-selected signal cluster within 90 seconds (p95) | P0 | 95th percentile of brief generation time <90 seconds over 100 generations |
| FR-012 | Every opportunity brief shall include an evidence table with a minimum of 3 cited evidence items, each with source, author, date, and verbatim excerpt | P0 | Brief with <3 citations is blocked from display; every citation includes all 4 required fields |
| FR-013 | Every opportunity brief shall include a confidence score between 0 and 100 with component breakdown | P0 | Confidence score displayed on every brief with expandable component view |
| FR-014 | The system shall auto-populate business impact estimates from CRM data when connected accounts are identified | P1 | Brief for issue affecting 3 Salesforce accounts shows their combined ARR in the business impact section |
| FR-015 | The system shall calculate a preliminary RICE score for each opportunity with evidence-backed dimension values | P1 | RICE score displayed with evidence citation for each dimension |
| FR-016 | The PM shall be able to edit any field in the opportunity brief before approval | P0 | PM can edit problem statement, evidence table, impact estimates, and RICE dimensions; all edits saved |
| FR-017 | An opportunity brief shall not advance to PRD generation, stakeholder notification, or Jira update without explicit PM approval | P0 | Clicking "Generate PRD" without PM approval returns an error: "Brief must be approved first" |
| FR-018 | An approved opportunity brief shall be stored in the product knowledge graph with full audit trail | P1 | Approved brief queryable from knowledge graph interface with PM name, timestamp, and edit summary |

---

## Prioritization Engine

| ID | Requirement | Priority | Acceptance Test |
|----|------------|---------|----------------|
| FR-019 | The system shall generate a prioritization brief for any approved opportunity brief on PM request | P0 | Prioritization brief generated within 60 seconds of request |
| FR-020 | The prioritization brief shall include RICE + strategic alignment composite scoring | P0 | Prioritization brief displays RICE score, strategic alignment score, and composite score |
| FR-021 | The prioritization brief shall include a comparison table showing the new item scored against the top 10 backlog items | P1 | Comparison table shows 10 existing items with their scores alongside the new item |
| FR-022 | The system shall sync the active Jira backlog (read-only) for display in the prioritization view | P1 | Jira backlog items displayed in prioritization view, updated within 15 minutes of Jira changes |
| FR-023 | The system shall require explicit PM approval before writing any priority change back to Jira | P0 | Priority change staged for PM review; Jira not updated until PM approves |
| FR-024 | The system shall calculate strategic alignment score based on current OKRs configured by the PM | P1 | OKR configuration interface available; alignment score updates when OKRs are changed |
| FR-025 | Every priority decision (approval + resulting Jira change) shall be logged in the audit trail | P0 | Priority decision queryable in audit log with PM name, timestamp, and before/after state |
| FR-026 | The system shall allow PM to override any automated score with a note explaining the override | P1 | PM can set any RICE dimension value manually; override note required; override logged in audit trail |

---

## PRD Copilot

| ID | Requirement | Priority | Acceptance Test |
|----|------------|---------|----------------|
| FR-027 | The system shall generate a PRD first draft from an approved opportunity brief within 3 minutes (p95) | P0 | 95th percentile PRD generation time <3 minutes over 50 generations |
| FR-028 | The PRD shall be generated according to the PM team's configured template structure | P1 | PRD sections match team-configured template; custom sections rendered correctly |
| FR-029 | Every factual claim in the PRD shall be cited to a specific source with source ID, author, date, and excerpt | P0 | PM review of 50 PRDs shows 0 instances of unattributed factual claims |
| FR-030 | Every user story in the PRD shall reference at least one customer signal that motivated it | P1 | Each user story has a minimum of 1 citation displayed in the citation panel |
| FR-031 | The system shall generate acceptance criteria for each user story | P1 | Each user story has associated acceptance criteria in the Given/When/Then format |
| FR-032 | The system shall suggest edge cases based on similar features in the knowledge graph | P2 | At least 3 edge case suggestions generated per PRD, sourced from knowledge graph history |
| FR-033 | The PM shall be able to edit any section of the PRD with a full-featured markdown editor | P0 | PM can edit all PRD sections; changes auto-saved; edit history accessible |
| FR-034 | A PRD shall not be shareable with engineering until the PM explicitly approves it | P0 | "Share with Engineering" button disabled until PM clicks "Approve PRD" |

---

## Risk Watchdog

| ID | Requirement | Priority | Acceptance Test |
|----|------------|---------|----------------|
| FR-035 | The system shall monitor signal volume by category and alert when volume increases >30% in 7 days | P0 | Test: inject 14 test signals in week 2 vs. 10 in week 1 (40% increase) → alert triggers |
| FR-036 | The system shall correlate signal spikes with recent deployment events from Jira release notes | P1 | Signal spike correlated with release within 72 hours shows "Possible regression: [release name]" in alert |
| FR-037 | The system shall identify affected customer accounts and their ARR/renewal date for any detected risk | P1 | Risk brief shows affected accounts with ARR and renewal date for all accounts with matching signals |
| FR-038 | The system shall generate a structured risk brief within 90 seconds of alert trigger | P0 | Risk brief generated within 90 seconds; includes severity, affected accounts, evidence table, mitigation options |
| FR-039 | The system shall require PM approval before creating any Jira ticket or sending any customer communication as part of risk response | P0 | No Jira ticket or customer email sent without PM approval action |
| FR-040 | The system shall check for SLA provisions in affected accounts when a risk is detected | P1 | Risk brief includes "SLA Exposure: Yes/No" with contract reference for each affected account |
| FR-041 | Risk alerts shall be delivered to the PM via in-app notification and email within 15 minutes of threshold trigger | P1 | Test alert delivered to PM inbox and email within 15 minutes |

---

## Stakeholder Alignment Center

| ID | Requirement | Priority | Acceptance Test |
|----|------------|---------|----------------|
| FR-042 | The PM shall be able to configure named stakeholder audiences with role context (engineering, sales, executive, CS) | P1 | PM can create, name, and describe 5+ stakeholder audiences |
| FR-043 | The system shall generate a personalized communication draft for each configured audience from a PM-approved decision | P0 | Given 3 configured audiences, 3 distinct drafts generated from same evidence base, each with audience-appropriate framing |
| FR-044 | Engineering audience drafts shall include technical context, scope, and dependencies | P1 | Engineering draft includes at minimum: technical scope summary, dependencies identified, open questions |
| FR-045 | Sales audience drafts shall include customer commitment context and commercial implications | P1 | Sales draft includes: relevant deal impact, pipeline implications, what AEs can communicate |
| FR-046 | Executive audience drafts shall include business impact summary and strategic alignment | P1 | Executive draft includes: business impact ($), OKR alignment, risk summary |
| FR-047 | All stakeholder communications shall require PM approval before delivery | P0 | "Send" and "Copy" buttons inactive until PM clicks "Approve" for each draft |

---

## Knowledge Graph

| ID | Requirement | Priority | Acceptance Test |
|----|------------|---------|----------------|
| FR-048 | The system shall maintain an entity graph with at minimum: Feature, Customer, Signal, Decision, Outcome entity types | P1 | Entity browser displays all 5 entity types with counts |
| FR-049 | Every approved brief, PRD, and risk assessment shall be stored as a linked node in the knowledge graph | P0 | Query "show decisions in authentication area" returns all decisions with linked briefs and PRDs |
| FR-050 | The PM shall be able to query the knowledge graph using natural language | P2 | PM query "what customer evidence supported the SSO prioritization?" returns the approved brief with citations |

---

*All requirements marked P0 are prerequisite to V1 launch. P1 requirements are target for V1 with P2 in V1.1.*
