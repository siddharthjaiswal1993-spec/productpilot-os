# Journey: Roadmap to Stakeholder Update

## Overview

**Trigger:** PM approves a prioritization decision: Multi-tenant workspace support elevated to P1, advanced reporting moved from P2 to P3. This decision affects 6 stakeholder groups who all need to know — with different context and framing.

**Who is involved:** PM (decision-maker), Stakeholder Alignment Agent, 6 stakeholder groups

**Outcome:** Personalized update drafts for Engineering, Sales, Customer Success, Executive Leadership, the PM team, and external customer-facing communication — all generated from the same evidence base, tuned to each audience, ready for PM review in parallel.

---

## Before ProductPilot OS

**Hour 0:** PM makes the prioritization decision in Jira (priority change) and Confluence (roadmap update).

**Day 1:** PM writes the engineering update in Jira comments: technical scope, timeline, dependencies. 30 minutes.

**Day 1:** PM drafts a Sales update explaining why multi-workspace was prioritized and what it means for pipeline. 25 minutes.

**Day 2:** PM writes the CS update explaining the change and what CSMs should tell customers waiting for reporting. 20 minutes.

**Day 2:** PM prepares an executive summary for the weekly VP Product update. 30 minutes.

**Day 3:** Head of Product sends an all-PM team update about the priority change. 15 minutes.

**Week 2:** PM realizes 2 customers were waiting for the reporting update and sends individual "roadmap update" emails. 30 minutes each.

**Total:** 3 days, 3.5 hours of communication work — all about the same decision, all reformatted for different audiences. All from memory.

---

## With ProductPilot OS

**Hour 0 (at the moment PM approves the priority decision):** Stakeholder Alignment Agent is automatically triggered. It retrieves:
- The approved opportunity brief for multi-tenant workspace (including evidence, ACV context, confidence score)
- The approved opportunity brief for advanced reporting (including evidence, expected delay, alternative plan)
- The list of configured stakeholder audiences and their communication preferences
- Prior communications to each audience about these features (to maintain context continuity)

**Hour 0:02 (2 minutes later):** 5 personalized update drafts generated simultaneously. PM receives a notification: "Stakeholder updates ready for review — 5 drafts."

---

## The Generated Updates (Summary)

### Draft 1: Engineering Team (EM + Squad)

*Tone: Technical, action-oriented*

"**Priority Change: Multi-Workspace (P1) / Advanced Reporting (P3)**

**Multi-tenant workspace** has been elevated to P1 effective immediately based on $1.8M in active pipeline blocked and a strategic OKR alignment check. The opportunity brief is linked below.

Architecture scope: workspace isolation at the data model and API layer. We need a feasibility assessment and scope estimate by EOW. The supporting customer evidence is attached — 4 enterprise accounts have hard requirements for workspace isolation.

**Advanced reporting** is moved to P3. The evidence base is solid (12 customer signals, $420K ARR requesting) but multi-workspace is higher ACV and more strategically urgent. Estimated delay: 2 quarters. Nothing changes in the existing design work — it's paused, not cancelled.

Open questions for sprint planning sync: [auto-populated from engineering feasibility checklist]"

---

### Draft 2: Sales Team (VP Sales + AEs)

*Tone: Commercial, commitment-focused*

"**Update: Multi-Workspace Is P1 — Timeline Coming EOW**

Based on the sales signals your team surfaced ($1.8M in pipeline, 3 deals explicitly blocked), multi-tenant workspace has been elevated to P1. Thank you for the detailed signals — the business case was clear and directly informed this decision.

Timeline: We'll have an engineering scope estimate by end of week and will share a delivery target. Please do not make specific date commitments to prospects until we confirm scope with engineering.

For current pipeline: TechCorp, GlobalCorp, and DataFlow — you can confirm this is now P1 and in active engineering planning. Do not commit a specific date yet.

Advanced reporting: This has moved to P3. If you have pipeline dependent on reporting, let me know separately so we can assess urgency."

---

### Draft 3: Customer Success Team

*Tone: Customer-relationship-focused, practical*

"**Roadmap Update: Multi-Workspace P1 / Reporting Delayed**

**For accounts waiting on multi-workspace:** This feature is now P1. We don't have a ship date yet — I'll share as soon as we have an engineering estimate (end of week target). Feel free to let those customers know this is actively in development. Hold on specific commitments until we have a date.

**For accounts waiting on advanced reporting:** Reporting has moved to P3 due to a higher-urgency enterprise priority. Expected delay is approximately 2 quarters. Here's a suggested customer communication:

*'We wanted to update you on advanced reporting: we've made a prioritization adjustment this quarter due to a critical enterprise requirement. Advanced reporting is still on our roadmap and expected in [Q3/Q4 — confirm before using]. We appreciate your patience and will reach out as soon as we have a confirmed timeline.'*

Please do not send this without confirming the timeline with me first."

---

### Draft 4: VP Product (Executive Update)

*Tone: Strategic, evidence-forward*

"**Roadmap Decision: Multi-Workspace Elevated to P1**

A priority adjustment this week based on a strong evidence signal: $1.8M in active pipeline blocked by missing multi-tenant workspace support, with 3 enterprise deals dependent on Q3 availability. The decision was made against the full evidence base (see linked opportunity brief — confidence score: 82/100).

Advanced reporting moves to P3. It has 12 customer signals and $420K ARR requesting, but is lower urgency than the pipeline-blocking multi-workspace gap.

Engineering scope estimate expected EOW. I'll update with timeline once we have it.

This change is aligned with the Q2 OKR: 'Win 5 enterprise deals >$200K ACV.'"

---

### Draft 5: PM Team Update

*Tone: Collegial, process-transparent*

"**Priority Change — FYI + Process Note**

Multi-workspace is P1 as of today. Advanced reporting moves to P3. Brief and supporting evidence are in the shared intelligence hub — worth reviewing if you have accounts asking about multi-workspace.

Process note: this decision was surfaced by ProductPilot OS based on a $1.8M sales signal cluster that appeared 4 days ago. The brief was approved with 82/100 confidence. The full evidence chain is linked for any PM who wants to understand the methodology."

---

## Human Review and Send

PM reviews all 5 drafts. Total review time: 12 minutes.

Edits made:
- Draft 2: Added a specific AE name to the pipeline context ("AE Marco's TechCorp opportunity")
- Draft 3: Added a date placeholder note ("confirm [Q3/Q4] before sending")
- Drafts 1, 4, 5: Approved without changes

All 5 sent within 15 minutes of the priority decision being made.

---

## Time Comparison

| Activity | Before | After | Improvement |
|----------|--------|-------|------------|
| Engineering update | 30 min | 0 (auto-generated) | 100% |
| Sales update | 25 min | 0 (auto-generated) | 100% |
| CS update | 20 min | 0 (auto-generated) | 100% |
| Executive update | 30 min | 0 (auto-generated) | 100% |
| PM team update | 15 min | 0 (auto-generated) | 100% |
| **Total PM communication time** | **3+ hours over 3 days** | **15 minutes same hour** | **95%+** |

---

*One decision. Five audiences. Fifteen minutes. All from the same evidence base, each tailored to its audience.*
