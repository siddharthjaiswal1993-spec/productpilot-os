# Online Evaluations

## Overview

Online evaluations are production-time quality measurements running continuously against real user traffic. Unlike offline evaluations (which run against a static golden dataset), online evals measure actual PM behavior — what they approve, what they reject, what they edit, how quickly they act.

Online evals are the most valuable signal for product quality because they are:
- **Real:** Based on actual PM behavior, not simulated scenarios
- **Continuous:** Always measuring, not just at eval cadences
- **Self-labeling:** PM approvals and rejections are inherently labeled quality signals

---

## Core Online Eval Metrics

### 1. PM Acceptance Rate (Primary)

**Definition:** % of agent outputs approved without major edit (edit_distance_score ≤ 0.20)  
**Granularity:** By output type, agent, model version, PM role, product area  
**Cadence:** Real-time; 7-day and 30-day rolling windows  
**Alert:** If 7-day acceptance rate drops >10% below 30-day baseline → engineering alert

### 2. Hallucination Rate (Safety)

**Definition:** % of outputs containing at least one detected critical hallucination (numeric or entity)  
**Detection:** Post-generation claim-citation validation (automated)  
**Target:** <2%  
**Alert:** >5% over any 24-hour window → P1 engineering alert

### 3. Citation Coverage Rate

**Definition:** % of factual claims in delivered outputs with valid citations  
**Detection:** Citation validator output (automated)  
**Target:** >95%  
**Alert:** <90% over any 24-hour window → engineering alert

### 4. Generation Latency

**Definition:** p50, p95, p99 wall-clock time from PM trigger to output in inbox  
**Granularity:** By agent type, model version  
**SLA targets (p95):**
- Opportunity brief: <90 seconds
- PRD: <3 minutes
- Prioritization brief: <60 seconds
- Stakeholder drafts (all audiences): <90 seconds
- Risk brief: <60 seconds
- Executive summary: <2 minutes

### 5. Error Rate

**Definition:** % of agent invocations that result in a failure (timeout, schema validation failure, API error)  
**Granularity:** By agent, step within agent, integration dependency  
**Target:** <1% of invocations  
**Alert:** >3% over any 1-hour window → engineering alert

### 6. HITL Deferral Rate

**Definition:** % of outputs that a PM defers (neither approves nor rejects) within 24 hours  
**Why it matters:** High deferral may indicate outputs are confusing, overwhelming, or arriving at the wrong time  
**Target:** <20% of outputs deferred beyond 24 hours  
**Note:** Deferral is tracked separately from rejection; deferral indicates "haven't looked at it yet" vs. "looked and dismissed"

### 7. Rejection Rate with Reason

**Definition:** % of outputs rejected, broken down by rejection reason category  
**Rejection categories:**
- `evidence_thin`: "Not enough evidence to justify this priority"
- `framing_wrong`: "The framing doesn't match what I need"
- `incorrect_data`: "A specific data point is wrong"
- `not_relevant`: "This doesn't apply to my product area"
- `other`: Free text reason

**Use:** The rejection reason distribution tells us exactly where agents are failing.

---

## Dashboard Design

The online eval dashboard is an internal engineering tool with:

**Overview panel:** Key metrics in real-time with 7-day and 30-day sparklines
- PM Acceptance Rate (by output type)
- Hallucination Rate
- Citation Coverage Rate
- Error Rate
- p95 Latency by Agent

**Drill-down panel:** Click any metric to see:
- Time-series chart with alert threshold lines
- Breakdown by dimension (agent type, model version, PM role, etc.)
- Comparison to baseline period

**Alert log:** List of all triggered alerts with severity, trigger condition, time, and resolution status

**Rejection analysis panel:** Distribution of rejection reasons over time; free text search on rejection reason text

---

## Online Eval → Product Decision Loop

Online evals drive four types of product decisions:

1. **Model/prompt change approvals:** No change deploys without regression check against baseline online eval metrics.

2. **Feature prioritization:** Low acceptance rate in a specific output type → prioritize improvement in that agent.

3. **Customer success intervention:** A specific tenant's acceptance rate drops significantly → CS reaches out to understand if there's a configuration issue or a product quality gap.

4. **SLA violation detection:** If latency SLA is breached for >5% of users over 7 days → infrastructure scaling or optimization is triggered.
