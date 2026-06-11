# Journey: Bug to Risk Assessment

## Overview

**Trigger:** A critical bug is filed in Jira: "Data export fails silently for accounts using custom field mappings — data loss possible."

**Who is involved:** Risk Watchdog Agent, Insight Synthesizer Agent, PM (decision-maker), Engineering Manager

**Outcome:** A structured risk assessment with affected customer analysis, revenue at risk, SLA exposure, competitive risk, and recommended escalation path — generated in 90 seconds and surfaced to both PM and EM before either has read the Jira ticket.

---

## Before ProductPilot OS

**Hour 0:** Bug filed in Jira by QA engineer. Priority: P2 (default). Assigned to integrations squad.

**Hour 2:** EM sees the ticket in the sprint board. Marks it for investigation.

**Hour 6:** PM sees the ticket in a Jira notification. Doesn't know which customers are affected or how severe the business impact is. The Jira ticket has technical details but no customer context.

**Day 2:** Engineering investigates. Confirms: data export has been silently failing for accounts with >20 custom field mappings since the last release (8 days ago). The failure is silent — customers don't see an error, they just don't receive export data.

**Day 2 (afternoon):** PM starts manually checking: which customers use custom field mappings? Opens Salesforce, tries to find accounts with this feature enabled. Salesforce doesn't have this data — it's in the product database. Asks the data team for a query. Data team turnaround: 24 hours.

**Day 3:** PM receives customer list: 12 enterprise accounts affected. Starts checking renewal dates, ARR, and whether any are in active sales cycles. Manual process. 2 hours.

**Day 4:** PM drafts a risk assessment. Sends to engineering and VP Product. Engineering has already started the fix. The risk assessment is post-hoc.

**Total time:** 4 days, 4 hours of PM research, risk assessment arrives after engineering has already begun fixing.

---

## With ProductPilot OS

**Hour 0:** Bug filed in Jira. Risk Watchdog Agent is notified via Jira webhook within 90 seconds. Begins automated risk assessment.

**Hour 0:01:30 (90 seconds after filing):** Risk assessment draft ready. PM and EM both receive alerts.

---

## Agent Reasoning Steps

**Step 1 — Bug classification**
Risk Watchdog classifies: data integrity risk (silent failure = data loss for affected customers). Severity: P1 candidate (data loss is highest severity class). Triggers full risk assessment workflow.

**Step 2 — Affected customer identification**
Risk Watchdog queries the knowledge graph: "Which customer accounts have custom field mappings enabled?" Cross-references with CRM for ARR, tier, and renewal dates. Finds: 12 enterprise accounts, $2.3M combined ARR. 3 accounts in active renewal cycle within 90 days.

**Step 3 — Exposure duration assessment**
Bug was introduced 8 days ago in the May 7 release. Export jobs run nightly for most enterprise accounts. Estimated data loss exposure: 8 days × estimated daily export jobs per affected account = approximately 80–90 export events that may have failed silently.

**Step 4 — SLA exposure check**
Risk Watchdog checks: do any affected accounts have SLA guarantees covering data export reliability? Finds: 2 enterprise accounts have SLAs mentioning "data delivery reliability." Flags for legal/CS review.

**Step 5 — Competitive risk assessment**
Knowledge graph check: "Are any affected accounts currently evaluating competitors?" CRM data: 2 of 12 accounts have open competitive evaluation notes. One account (Apex Corp, $340K ARR) has a Salesforce note from 3 weeks ago: "Evaluating DataPipeline Pro as an alternative — decision in Q3."

**Step 6 — Confidence and severity scoring**
Severity: P1 (confirmed). Business impact: $2.3M ARR at direct exposure risk. Confidence: 89/100 (confirmed technical evidence from Jira, customer list from product database, CRM data).

---

## The Generated Risk Assessment

**Risk Title:** Silent Data Export Failure — Custom Field Mapping Accounts

**Severity:** P1 — Data Integrity Risk

**Summary:** Export jobs have failed silently for 12 enterprise accounts since May 7 release. Customers are unaware. Estimated 80–90 export events have failed without error notification.

**Affected Accounts:**

| Account | ARR | Renewal | Competitive Risk | SLA? |
|---------|-----|---------|-----------------|------|
| Apex Corp | $340K | 45 days | High (active eval) | No |
| TechFlow | $280K | 210 days | Low | Yes |
| DataCore Inc | $195K | 90 days | Medium | No |
| ... (9 more) | ... | ... | ... | ... |
| **Total** | **$2.3M** | | **2 High, 3 Medium** | **2 accounts** |

**Recommended Actions:**

1. **Immediate (today):** Engineering hotfix — P1, target resolution <24 hours
2. **Immediate:** Customer communication drafted for CSM delivery — proactive disclosure of the issue before customers discover it (reduces churn risk by ~60% based on historical incident data in knowledge graph)
3. **Within 24 hours:** Replay failed export jobs once fix is confirmed
4. **Within 48 hours:** Legal review for 2 SLA accounts — assess whether SLA penalty applies
5. **Within 72 hours:** Post-mortem scheduled — root cause analysis and prevention mechanism

**Customer Communication Draft (from Stakeholder Alignment Agent):**
"We've identified an issue affecting data exports for accounts using custom field mappings, introduced in our May 7 release. Your export jobs between May 7–15 may not have completed successfully. We expect to have a fix deployed within 24 hours and will re-run affected export jobs automatically. We apologize for the disruption and are committed to proactive communication on any future issues."

---

## Human Approval Points

**PM review (5 minutes):** Reads brief, confirms affected account list looks complete (cross-checks one known account manually), adds a note for the post-mortem agenda. Approves the risk assessment, approves the customer communication draft with one edit (adds a direct CSM contact for the high-risk accounts).

**EM review (3 minutes):** Confirms P1 classification, assigns hotfix to a specific engineer, confirms 24-hour target.

---

## Time Comparison

| Activity | Before | After | Improvement |
|----------|--------|-------|------------|
| Risk detection and classification | 6 hours (manual ticket triage) | 90 seconds (automated) | 99.6% |
| Affected customer identification | Day 3 (database query) | Instant (knowledge graph) | 2.5 days earlier |
| ARR exposure calculation | 2 hours | Automated | 100% |
| SLA exposure check | Not done | Automated | ∞ |
| Competitive risk flagging | Not done | Automated | ∞ |
| Customer communication draft | Day 4 | Day 0 (same hour as bug) | 4 days earlier |
| **Engineering hotfix start** | **Day 2** | **Hour 0** | **2 days earlier** |

---

*A P1 bug with $2.3M ARR at risk, properly assessed and communicated in 90 seconds — versus 4 days of manual research.*
