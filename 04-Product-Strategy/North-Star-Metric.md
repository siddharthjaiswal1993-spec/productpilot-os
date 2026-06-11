# North Star Metric: Evidence-Backed Decisions

## The North Star

**Percentage of roadmap decisions backed by cited customer, technical, and business evidence.**

This is the single metric that best captures the value ProductPilot OS creates in the world. When this metric moves up, product teams are making better decisions with stronger evidence foundations. When it is high and sustained, the entire downstream chain improves: better products get built, customers are better served, roadmaps are more trusted by stakeholders, and product organizations become more accountable.

---

## Why This Metric and Not Others

**Why not Daily Active Users (DAU)?**
DAU measures engagement, not value creation. A PM could check the ProductPilot OS dashboard every day without using it to make better decisions. DAU conflates activity with impact. The north star should measure the outcome we care about — decision quality — not the behavior that might correlate with it.

**Why not Net Revenue Retention (NRR)?**
NRR is a financial health metric, not a product value metric. It is an important business metric (tracked as a Tier 1 metric in our success framework) but does not capture whether customers are getting the core product value. A team paying for ProductPilot OS and renewing could still be using it as a documentation tool without improving evidence-backing rates.

**Why not NPS?**
NPS measures satisfaction, not impact. A PM might love the product experience without it changing the quality of their decisions. We want to measure what we uniquely create: evidence-backed decisions.

**Why this metric:**
It is directionally aligned with the product's core value proposition. It is measurable (can be computed from decision records in the system). It is actionable — product teams that see this number below 50% have a clear instruction: use the opportunity brief flow before making priority decisions. And it is a compelling customer value metric: a VP Product who sees this number at 85% in their team's ProductPilot OS dashboard has a concrete ROI story for their CPO.

---

## Precise Definition

**Numerator:** Number of roadmap decisions (priority changes, feature confirmations, scope decisions) in a given period that have an associated, PM-approved opportunity brief with at least 3 cited evidence sources covering at least 2 of the 3 evidence categories (customer signals, business impact, technical context).

**Denominator:** Total number of roadmap decisions made in the same period, as tracked in the system.

**Period:** Rolling 30-day window, updated daily.

**Minimum threshold for "cited":** An evidence source must include: source system (e.g., "Zendesk"), source ID (ticket #), date, and an extracted quote or metric. A claim with no source citation does not count.

**Target:** >80% within 90 days of adoption for a team that has connected at least 3 data sources.

---

## Leading Indicators

These metrics predict future movement in the north star:

| Leading Indicator | Definition | Why It Predicts NSM |
|-------------------|------------|---------------------|
| Opportunity Briefs Generated | Briefs created per PM per week | Briefs are the precursor to evidence-backed decisions |
| PM Acceptance Rate | % of briefs approved without major edit | High acceptance → briefs are trusted → PM uses briefs for decisions |
| Evidence Sources Connected | # of active integrations per team | More sources → richer evidence → higher evidence coverage rate |
| Signal Volume | Signals ingested per day per team | More signals → better evidence coverage for any given decision |
| PRDs with Citations | % of PRDs that include cited evidence | Cited PRDs reflect and reinforce evidence-backed decision culture |

---

## Lagging Indicators

These metrics confirm that north star improvement is producing business outcomes:

| Lagging Indicator | Definition | Why It Follows NSM |
|-------------------|------------|-------------------|
| Roadmap Churn Rate | % of roadmap items changed/deprioritized within 30 days of commitment | Evidence-backed decisions are more stable → lower churn |
| Stakeholder NPS | NPS score from stakeholders (engineering, sales, exec) on product team decisions | Evidence backing improves stakeholder trust → higher NPS |
| Time-to-Decision | Average hours from signal ingestion to priority decision | Evidence assembly is automated → decisions happen faster |
| PM Accept Rate (90-day cohort) | PM acceptance rate for 90-day cohorts of new users | Tracks learning curve and platform maturity |

---

## Anti-Metrics: What We Explicitly Do Not Optimize For

**Briefs Generated (without quality threshold):** Generating briefs is not the goal; generating *good* briefs that PMs use to make better decisions is. Volume without quality degrades the metric.

**Time Spent in ProductPilot OS:** Engagement time is inversely correlated with value in an intelligence product. A PM who spends 20 minutes reviewing a brief and approving it has achieved the same outcome as a PM who spends 2 minutes — but the 20-minute PM may just have a slower workflow. We optimize for decision quality, not engagement time.

**Features Used:** Feature adoption breadth is not the goal. A PM who uses only the opportunity brief and PRD copilot but makes 90% evidence-backed decisions is more valuable than a PM who explores every feature but makes only 40% evidence-backed decisions.

---

## North Star Metric Rollout: Introducing as a Customer Value Metric

The north star metric is also a customer value communication tool. When onboarding new customers:

1. **Baseline assessment:** Measure the current evidence-backing rate by asking PMs to tag their last 10 decisions as evidence-backed or not. Typical baseline: 15–25%.

2. **30-day target:** Set a 90-day target (60–70%) with the customer. Create a shared dashboard visible to the VP Product.

3. **Monthly reporting:** VP Product receives a monthly evidence-backing rate report. The metric becomes a management tool — how is the PM team improving decision quality?

4. **Renewal conversation:** At contract renewal, the VP Product can see their evidence-backing rate over the past 12 months, the decisions it affected, and the business outcomes correlated with high-evidence decisions. This is the ROI story.

---

*The north star metric is both the product design compass and the customer value proof point. Every feature decision should ask: does this improve the percentage of roadmap decisions backed by cited evidence? If no, reconsider.*
