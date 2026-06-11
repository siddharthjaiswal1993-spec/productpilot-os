# Journey: Sales Blocker to Roadmap Recommendation

## Overview

**Trigger:** 3 AEs log feature gap notes in Salesforce over 2 weeks. The VP Sales sends a Slack message to the Head of Product: "We keep losing enterprise deals over multi-tenant workspace support. Can we discuss?"

**Who is involved:** VP Sales, 3 AEs (signal sources), PM (primary decision-maker), Insight Synthesizer Agent, Prioritization Agent, Stakeholder Alignment Agent

**Outcome:** A structured roadmap recommendation with ACV-weighted business case, evidence table, competitive context, and prioritization comparison against the current top-5 backlog items — ready for PM review within 3 minutes of active time.

---

## Before ProductPilot OS

**Week 1:** AE 1 logs a Salesforce note: "Lost to Competitor X — they have workspace isolation, we don't." AE 2 logs: "TechCorp pilot requires separate workspaces per business unit — feature gap." AE 3 tags PM in Slack: "Hey can we talk about multi-workspace?"

**Week 2:** VP Sales sends Slack message to Head of Product. Meeting is scheduled for Thursday.

**Thursday meeting:** Head of Product arrives having read the 3 Slack messages. No assembled evidence. Meeting is 60 minutes of VP Sales sharing anecdotes, PM asking questions, and both agreeing to "investigate and get back to you."

**Week 3:** PM spends a day reviewing Salesforce, pulling Gong calls, checking the competitive landscape document (6 months out of date), and drafting a 1-page analysis. Sends to VP Sales as "preliminary findings."

**Week 4:** VP Sales reviews and responds with 2 additional deal examples. PM updates the analysis. Schedules another meeting to discuss prioritization implications.

**Total time:** 3 weeks, 2 meetings, 6+ hours of PM analysis, incomplete evidence, no automated prioritization comparison.

---

## With ProductPilot OS

**Day 1 (Slack message from VP Sales, 10:30am):** ProductPilot OS has already ingested the 3 Salesforce notes as signals. When the VP Sales Slack message arrives, it is also ingested. The system detects: new high-priority signal cluster (4 related signals in 14 days, all tagged "multi-tenant workspace," combined ACV context: $1.8M in pipeline).

**Day 1 (10:31am):** The PM receives a proactive alert in their ProductPilot OS inbox: "Sales Blocker Detected — Multi-Tenant Workspace: 4 signals from Sales, $1.8M ACV in pipeline. Generate opportunity brief?"

**Day 1 (10:32am):** PM clicks "Generate Brief." The system runs:
- Insight Synthesizer: retrieves 4 Salesforce signals + 2 additional Gong call mentions it found via search + 1 competitor mention in a prospect email (from Gmail integration) + 3 support tickets that mentioned workspace isolation
- Prioritization Agent: scores the brief against the current top-10 backlog items using RICE + strategic alignment + evidence confidence

**Day 1 (10:34am):** Brief ready. PM reviews.

---

## The Generated Brief

**Title:** Multi-Tenant Workspace Isolation — Enterprise Sales Blocker

**Sales signal summary:** 3 AEs logging $1.8M in ACV pipeline affected. 2 active pilots paused pending feature availability. 1 deal lost to Competitor X in Q1 (documented in Salesforce).

**Evidence Table:**

| Source | Signal | ACV Impact | Date |
|--------|--------|-----------|------|
| Salesforce (AE: Marco) | "TechCorp requires workspace isolation per BU. $420K deal on hold." | $420K | May 14 |
| Salesforce (AE: Lisa) | "GlobalCorp expansion blocked — multi-workspace needed for 3 subsidiaries." | $680K | May 11 |
| Salesforce (AE: James) | "Lost DataFlow to Competitor X. Workspace isolation was deciding factor." | $350K lost | April 28 |
| Gong call | TechCorp technical eval: "Workspace isolation is a hard requirement for our security policy." | $420K | May 7 |
| Competitor analysis | Competitor X feature page: workspace isolation advertised as flagship enterprise feature | N/A | N/A |
| Support ticket #19021 | Enterprise customer: "Need to prevent data bleed between workspace teams" | $120K | May 3 |

**Aggregate ACV at risk:** $1.8M (active pipeline) + $350K (deal already lost)
**Strategic alignment:** High — Q2 OKR "win 5 enterprise deals >$200K ACV"; this directly affects 3 current pipeline deals
**Confidence score:** 82/100 (high signal volume, multi-source corroboration, strong ACV context, recent signals)

**Prioritization comparison (current backlog top-5):**

| Priority | Item | RICE Score | Evidence Confidence | ACV Impact |
|----------|------|-----------|---------------------|------------|
| P1 | API performance improvement | 5,100 | 76% | $340K |
| P2 | Advanced reporting | 4,800 | 65% | $210K |
| **New** | **Multi-tenant workspace** | **6,200** | **82%** | **$1.8M** |
| P3 | Mobile app notifications | 3,200 | 55% | $60K |
| P4 | Bulk import improvements | 2,900 | 62% | $120K |

**Recommended action:** Elevate to P1. Schedule engineering feasibility review. Generate sales briefing for VP to communicate to pipeline accounts.

---

## Human Approval and Decision

PM reviews brief (4 minutes). Agrees with the recommendation. Adjusts the RICE Impact score from 4 to 5 (based on strategic context: these are flagship enterprise deals). Approves the brief and clicks "Notify Stakeholders."

**Stakeholder Alignment Agent** generates:
- VP Sales update: "Multi-tenant workspace has been elevated to P1. Engineering feasibility review scheduled for next week. Timeline TBD — will share 14-day update."
- Engineering Manager update: "New P1 requires architecture review: workspace isolation at the data model level. Please review scope and provide feasibility estimate by end of week."
- Executive summary (for CPO): "Sales signal identified: $1.8M in active pipeline affected by missing multi-tenant workspace support. Elevated to P1 with evidence. See attached brief."

PM reviews and sends all three in 5 minutes.

---

## Time Comparison

| Activity | Before | After | Improvement |
|----------|--------|-------|------------|
| Signal detection | Week 2 (Slack escalation) | Day 1, 10:31am (proactive) | 7 days earlier |
| Evidence assembly | 6+ hours over 2 weeks | 2 minutes (automated) | 98% |
| ACV context | Manual Salesforce research | Automated | 100% |
| Prioritization comparison | Not done | Automated | ∞ |
| PM active decision time | 3 weeks, 2 meetings | 10 minutes (same day) | 96% |
| Stakeholder communication | Separate drafting per audience | Automated personalization | 85% |

---

*From a VP Sales Slack message to a prioritized brief with ACV context and stakeholder updates — in the same morning.*
