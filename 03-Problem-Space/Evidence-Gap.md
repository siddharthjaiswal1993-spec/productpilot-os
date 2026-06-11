# The Evidence Gap: Decisions Made Without Ground Truth

## Defining the Evidence Gap

The evidence gap is the difference between the product decisions a team makes and the product decisions they would make if they had systematically assembled all available organizational evidence.

It is not a lack of data. Most B2B SaaS companies have abundant data — Gong libraries with hundreds of calls, Zendesk with thousands of tickets, Amplitude with millions of events, Salesforce with thousands of CRM notes. The data exists. The gap is in assembly: converting raw data into cited, structured evidence that is accessible at the moment of decision-making.

The evidence gap produces decisions that feel informed — PMs are smart, experienced, and customer-oriented — but are actually under-evidenced. The consequences compound across hundreds of decisions per year.

---

## Why Evidence Is Hard to Assemble

### Problem 1: Evidence Is Distributed and Unstructured

Relevant evidence for a single product decision may exist in:
- A Gong call transcript from 3 months ago
- 6 Zendesk tickets across 4 customer accounts over 12 months
- 2 Salesforce notes from an AE who lost a deal
- A Slack message in a channel the PM doesn't monitor
- An Amplitude funnel showing a specific drop-off
- A GitHub issue that hasn't been connected to the customer use case it affects

Finding, reading, and connecting these 12 evidence sources for one decision — before the next three decisions also need evidence — is not a realistic expectation for a PM managing 15+ active items.

### Problem 2: Evidence Has No Standardized Format

A Gong call is a 45-minute audio file with a transcript. A Zendesk ticket is a text conversation with metadata. A Salesforce note is free-form text. An Amplitude chart is a visualization with no associated narrative. A Slack message is a fragment in a thread of 40 messages.

There is no common format for "evidence." Assembling evidence requires not just finding the source but extracting the relevant signal, understanding the context, and translating it into a format that can be cited in a decision document.

### Problem 3: Evidence Quality Is Variable

Not all signals are equal. A customer quote from a 50-minute discovery call with the champion of a $500K ARR account is stronger evidence than a feature request from a free-tier user. A pattern of 30 Zendesk tickets reporting the same issue is stronger evidence than a single escalation from a vocal customer.

Weighting evidence quality requires judgment that takes time. In the absence of systematic evidence quality scoring, PMs default to weighting evidence by recency and visibility — the call they heard last week, the customer who emailed yesterday.

---

## The PRD Without Customer Quotes Problem

The evidence gap is most visible in PRD quality. Reviewing a random sample of PRDs from product teams without evidence infrastructure reveals a consistent pattern:

- **Problem statement:** "Customers need better reporting capabilities" (unattributed, no evidence of how many customers, what the gap is, or what the business impact is)
- **User stories:** "As a user, I want to export data so that I can analyze it in Excel" (generic, not citing specific customers or use cases)
- **Success metrics:** "Improve user satisfaction" (no baseline, no target, no measurement method)
- **Evidence:** None cited

This PRD pattern is not the PM's fault. The PM wrote the PRD from their best recollection of customer conversations and stakeholder requests. They did not have time to pull 6 months of Gong calls, cross-reference with Zendesk tickets, and check CRM notes for every feature they described.

The problem is structural: evidence assembly is expected but evidence infrastructure does not exist.

**The downstream cost:**
- Engineering questions the PRD ("how many customers actually need this?") and the PM can't answer with data
- Executive stakeholders challenge priorities without evidence backing ("why is this more important than X?")
- The feature ships and adoption is low — the problem was smaller than the PM thought, because the evidence was thin
- Post-mortems can't identify what went wrong because the original evidence basis wasn't documented

---

## The Prioritization Score Without Data Problem

RICE, ICE, and comparable frameworks require numerical inputs:
- Reach: how many customers/users will this affect?
- Impact: what is the business impact?
- Confidence: how confident are we in these estimates?

Without evidence infrastructure, these numbers are guesses. Sophisticated guesses, made by experienced PMs — but still guesses. And guess-based RICE scores produce guess-quality prioritization outputs.

The evidence gap in prioritization manifests as:
- **Reach underestimates:** PMs estimate reach based on the customers they've talked to recently, missing the broader population visible in usage data
- **Impact overestimates:** Impact for high-visibility requests gets inflated by social pressure; impact for unsexy but high-value work gets deflated
- **Confidence miscalibration:** Confidence is high when evidence is thin, because PMs don't know what they don't know

Evidence-backed RICE requires: pulling actual user counts from analytics, calculating ACV impact from CRM, checking support ticket volumes for urgency, and citing the evidence for each estimate. Without a system to assemble this evidence, the RICE score is impressionistic.

---

## What "Evidence-Backed" Actually Means

Evidence-backed does not mean "every decision has perfect data." It means:

1. **Assembled:** Relevant signals from all available sources have been systematically collected for this decision — not just the signals the PM happened to encounter.

2. **Cited:** Every claim in the decision document is linked to a specific source: a customer quote with attribution, a metric with a data source and date, an escalation with a ticket ID.

3. **Weighted:** Different types of evidence are weighted appropriately — a pattern across 15 customers is stronger than a single anecdote; a Gong call from a $500K ARR account is weighted accordingly.

4. **Confidence-calibrated:** The decision record reflects what the evidence actually supports, not what the PM wishes it supported. If evidence is thin, confidence is explicitly low.

5. **Retrievable:** The evidence is stored in a format that can be retrieved in the future — to answer "why did we make this decision?" six months later.

---

## The Difference Between Anecdote and Evidence

The most common substitute for evidence is anecdote. Anecdotes are real — real customer conversations, real Slack messages, real escalations. But anecdotes are not evidence until they are:
- Systematically collected (not just the most memorable)
- Cross-referenced with other signals (corroborated, not isolated)
- Weighted by representativeness (is this customer typical or atypical?)
- Documented with provenance (who said this, when, in what context)

A PM saying "our customers need better reporting — I heard it on three calls this week" is sharing anecdotes. The same PM producing a brief that says "8 customers in the enterprise segment have raised reporting limitations in the past 60 days across 12 Gong calls, 6 Zendesk tickets, and 3 Salesforce notes, representing $2.1M in combined ARR" is sharing evidence.

The difference is not the underlying raw material — both start from real customer conversations. The difference is systematic assembly, citation, and quantification.

ProductPilot OS converts anecdotes into evidence by doing the assembly work automatically: aggregating signals across sources, cross-referencing patterns, weighting by business impact, and generating cited evidence briefs that replace anecdote with structured documentation.

---

*The evidence gap is not a knowledge problem — the knowledge exists in the organization's data. It is an assembly problem. The knowledge must be connected, weighted, cited, and surfaced. That is the job of the intelligence layer.*
