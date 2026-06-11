# Success Metrics Framework

## Overview

ProductPilot OS tracks success across four tiers: company health, product health, intelligence quality, and customer outcomes. Each tier feeds the next — company health follows product health, which follows intelligence quality, which follows customer outcomes.

The most important metrics for product investment decisions are Tier 3 (intelligence quality) and Tier 4 (customer outcomes). These are the metrics that reflect whether the product is working. Tier 1 and Tier 2 metrics reflect whether the business is healthy.

---

## Tier 1: Company Health Metrics

| Metric | Definition | Target (Year 1) | Target (Year 2) |
|--------|-----------|-----------------|-----------------|
| ARR | Annual Recurring Revenue | $1M | $5M |
| Net Revenue Retention | Expansion - Churn / Prior ARR | >110% | >120% |
| CAC Payback Period | Customer Acquisition Cost / Monthly Revenue | <18 months | <12 months |
| Gross Margin | (Revenue - COGS) / Revenue | >70% | >75% |
| Monthly Burn Multiple | Net Burn / New ARR | <1.5x | <1x |

---

## Tier 2: Product Health Metrics

| Metric | Definition | Target (30 days) | Target (90 days) |
|--------|-----------|-----------------|-----------------|
| Weekly Active PMs | PMs who use ProductPilot OS at least once per week | >60% of licensed PMs | >80% |
| Feature Adoption | % of PMs who have used each core module at least once | >50% per module | >75% |
| PM Acceptance Rate | % of agent outputs approved without major edit | >50% | >75% |
| Time to First Value | Hours from account creation to first approved brief | <8 hours | <4 hours |
| Integration Depth | Average # of connected sources per team | >2 | >4 |
| Briefs Generated Per PM Per Week | Frequency of opportunity brief generation | >1 | >3 |

---

## Tier 3: Intelligence Quality Metrics

These are the most technically important metrics — they measure whether the AI system is producing reliable, trustworthy outputs.

| Metric | Definition | Target | Measurement Method |
|--------|-----------|--------|--------------------|
| Evidence Citation Rate | % of generated outputs that include at least 3 cited sources | >95% | Automated citation audit on every output |
| Hallucination Rate | % of generated claims that cannot be verified against cited source | <2% | Weekly LLM-as-judge verification sample |
| Confidence Calibration Error (CCE) | Difference between stated confidence and actual correctness rate | <10% | Comparison of confidence scores vs PM validation |
| Retrieval Relevance | % of retrieved evidence segments rated relevant by PM sampling | >80% | Human eval sample (50 queries/week) |
| Context Coverage Score | % of relevant signals in the corpus included in brief evidence | >70% | Comparison of brief citations vs full signal corpus for same query |
| Agent Failure Rate | % of agent workflows that fail without producing output | <1% | Automated monitoring |
| p95 Brief Generation Latency | 95th percentile latency for opportunity brief generation | <90 seconds | Infrastructure monitoring |

---

## Tier 4: Customer Outcome Metrics

These metrics measure whether ProductPilot OS is creating the outcomes it promises — better decisions, less wasted time, better stakeholder alignment.

| Metric | Definition | Target (90 days) | Target (12 months) |
|--------|-----------|-----------------|-------------------|
| Evidence-Backing Rate | % of roadmap decisions backed by cited evidence (North Star) | >60% | >80% |
| Time Recovered | Average hours/PM/week recovered from signal assembly | >3 hours | >5 hours |
| Time-to-Decision | Average hours from signal ingestion to priority decision | <12 hours | <4 hours |
| Roadmap Churn Rate | % of roadmap items changed within 30 days of commitment | Baseline - 20% | Baseline - 35% |
| Stakeholder NPS | NPS from stakeholders on quality of product team communication | Baseline + 10 pts | Baseline + 20 pts |
| PM Confidence Score | Self-reported PM confidence in defending priority decisions | >4/5 | >4.5/5 |

---

## OKR Example: Q1 Year 1

**Objective:** Establish a reliable evidence-backed decision workflow with design partners

**Key Result 1:** 10 design partner teams onboarded with >3 data sources connected
**Key Result 2:** PM acceptance rate >60% across all design partners by end of quarter
**Key Result 3:** Evidence-backing rate >50% at the 60-day mark for first 5 design partners
**Key Result 4:** Zero hallucination incidents verified by human eval (>200 samples reviewed)
**Key Result 5:** p95 brief generation latency <90 seconds (measured over 1,000 real brief generations)

---

## Metric Governance

**Review cadence:**
- Tier 1 (Company health): Monthly business review
- Tier 2 (Product health): Weekly product review
- Tier 3 (Intelligence quality): Continuous automated + weekly human eval
- Tier 4 (Customer outcomes): Bi-weekly customer success review

**Escalation thresholds:**
- Hallucination rate >2%: Immediate engineering escalation, output gating review
- PM acceptance rate <40% at 30-day mark: Product quality review, design partner deep-dives
- Agent failure rate >2%: Infrastructure reliability review
- Evidence-backing rate not improving after 60 days: Discovery interviews with design partners

---

*Metrics are not scorecards — they are navigation instruments. Track the right ones, act on what they say, and update them as the product and market mature.*
