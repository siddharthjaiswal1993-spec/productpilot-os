# Journey: Customer Feedback to Opportunity Brief

## Overview

**Trigger:** A PM notices that several customers have mentioned a similar pain point across multiple channels over the past few weeks but hasn't had time to assess whether it warrants prioritization.

**Who is involved:** IC PM (primary), Insight Synthesizer Agent, Prioritization Agent, PM approval gate

**Outcome:** A structured, cited opportunity brief with evidence table, business impact estimate, confidence score, and prioritization recommendation — ready for PM review in under 2 minutes of active PM effort.

---

## Before ProductPilot OS: The Manual Workflow

**Day 1 (Monday):** PM recalls that three customers mentioned "slow bulk export" in recent calls. Makes a note to investigate.

**Day 2:** Opens Gong and searches for "bulk export" across call recordings. Finds 4 relevant mentions but the search is inexact. Listens to 3 calls to find the specific moments. 45 minutes.

**Day 3:** Opens Zendesk and searches for "export" tickets. Finds 12 tickets but many are for different types of exports. Narrows to 6 relevant tickets. Reads through comments. 40 minutes.

**Day 4:** Checks Slack for any mentions of bulk export performance. Finds 2 customer escalations in #customer-success-escalations and 1 mention in a sales channel. 20 minutes.

**Day 5 (Friday):** Drafts the opportunity brief in Confluence. Has 6 data points, no ACV context from Salesforce (didn't have time to check), and no confidence score. The draft is a summary, not a cited brief. Shares with manager for "quick look."

**Total time:** 2–3 hours across 5 days, incomplete evidence, no business impact calculation, no confidence scoring.

**Quality:** 40% of relevant signals captured; no ACV context; no cross-source corroboration analysis.

---

## With ProductPilot OS

**Day 1 (Monday, 9:15am):** PM sees the Signal Intelligence Hub dashboard. The system has automatically clustered 18 signals tagged "export performance" across the past 45 days. The cluster shows: 7 Zendesk tickets, 5 Gong call mentions, 4 Slack messages (customer channels), 2 Salesforce opportunity notes from AEs. The cluster is flagged as "trending up" — 60% increase in signal volume vs. prior 45 days.

**Day 1 (9:20am):** PM clicks "Generate Opportunity Brief" on the cluster. The Insight Synthesizer Agent begins. Status: "Synthesizing 18 signals across 5 sources."

**Day 1 (9:21:50am — 90 seconds later):** Opportunity brief is ready for PM review.

---

## Agent Reasoning Steps

**Step 1 — Signal ingestion and classification**
The Insight Synthesizer Agent retrieves all 18 signals from the vector index. Each signal has been pre-chunked, embedded, and tagged with: source system, author/customer, date, sentiment, product area, and entity tags. Classification: 15 of 18 signals confirmed as related to bulk export performance; 3 are general export questions, excluded.

**Step 2 — Cross-source pattern synthesis**
Agent identifies: the core pain is bulk export timeout and slow performance for datasets >10K rows. Affected customers are primarily in the "advanced analytics" user segment. The issue appears consistently across all 3 source types (support, sales, calls) — high corroboration score.

**Step 3 — Business impact calculation**
Agent queries the Salesforce integration. 4 of the 18 signals are linked to identifiable accounts. Combined ARR: $340K. 1 signal is from an AE noting "export performance is a blocker for the TechCorp expansion" (ACV: $85K). Confidence in business impact: medium (partial coverage of total affected accounts).

**Step 4 — Evidence assembly**
Agent selects the 6 highest-quality evidence segments (highest relevance score + corroboration score) for the evidence table. Each segment includes: excerpt, source, author, date.

**Step 5 — Confidence scoring**
Composite confidence score: 74/100. Components: signal volume (high), source diversity (high), recency (medium — oldest signal 38 days ago), business impact coverage (medium — ACV linked for 4 of 15 affected signals), customer representativeness (medium — all from same segment).

**Step 6 — Prioritization pre-scoring**
Reach: ~180 customers in the analytics segment showing similar usage patterns (from analytics integration). Impact: 3/5 (workflow-blocking for heavy users). Confidence: 74%. Effort estimate: medium (2–3 sprints from engineering estimate template). Preliminary RICE: 4,212. Strategic alignment check: aligns with Q2 OKR "improve performance for enterprise analytics users."

---

## The Generated Brief

The PM sees a structured brief with:

- **Title:** Bulk Export Performance — Timeout and Slowness for Large Datasets (>10K rows)
- **Evidence Table:** 6 cited signals with source, author, date, excerpt

| # | Source | Author/Customer | Date | Signal |
|---|--------|----------------|------|--------|
| 1 | Zendesk #18421 | Apex Analytics (Enterprise) | May 12 | "Export times out at 9,000 rows. We process 50K rows daily. Unusable." |
| 2 | Gong call | DataCore Inc. (Mid-Market) | May 8 | "The bulk export is so slow our team stopped using it and built a workaround API call." |
| 3 | Slack #cs-escalations | @lisa.chen (CSM) | May 15 | "Third enterprise customer this month saying bulk export is a renewal risk." |
| 4 | Salesforce Opp | AE Marcus Webb | May 3 | "TechCorp blocked on bulk export — $85K expansion." |
| 5 | Zendesk #17902 | Meridian Group (Enterprise) | Apr 28 | "Performance degradation on exports >5K rows noticed since March update." |
| 6 | Gong call | NorthStar Data (SMB) | May 10 | "We had to move export jobs to 2am to avoid timeouts. Not sustainable." |

- **Business Impact:** $340K ARR in identified affected accounts. $85K ACV expansion blocked. Potential churn risk: medium-high for enterprise accounts.
- **Confidence Score:** 74/100 | Breakdown: Signal Volume: 85, Source Diversity: 90, Recency: 70, Business Coverage: 60, Representativeness: 65
- **Preliminary RICE:** 4,212 | Reach: 180 | Impact: 3 | Confidence: 74% | Effort: M
- **Strategic Alignment:** Aligned — Q2 OKR "enterprise analytics performance"
- **Recommended Next Step:** Prioritize. Generate PRD and assign to integrations squad.

---

## Human Approval

**PM review time:** 4 minutes. PM reads the brief, checks 2 citations in the source panel (clicks through to original Zendesk tickets), edits the business impact estimate upward (knows of 2 additional accounts from a recent call not yet ingested), and approves.

**PM edits:** Business impact estimate updated from "$340K ARR" to "$420K ARR (PM added 2 accounts from recent call recordings)." Confidence score auto-adjusts to 76/100.

**Time from signal cluster to approved brief:** 7 minutes (1.5 min agent synthesis + 4 min PM review + 1.5 min edit/approve).

---

## Output and Next Actions

Approved brief is stored in the Product Knowledge Graph with PM approval, edit history, and decision record. PM can now:
1. Click "Generate PRD" to trigger the PRD Agent with this brief as input
2. Click "Add to Prioritization Queue" to run against the full backlog scoring
3. Click "Notify Stakeholders" to alert the CS and Sales teams that this signal is being investigated

---

## Time Comparison

| Task | Before | After | Improvement |
|------|--------|-------|------------|
| Signal assembly | 2–3 hours | 90 seconds (automated) | 97% |
| Evidence table creation | 45 minutes | Automated | 100% |
| Business impact research | 30 minutes | Automated (with CRM) | 100% |
| Confidence scoring | Not done | Automated | ∞ |
| Brief writing | 60 minutes | PM edits draft | 85% |
| **Total PM active time** | **3+ hours over 5 days** | **7 minutes on Day 1** | **96%** |

---

*The signal existed. The intelligence did not — until now.*
