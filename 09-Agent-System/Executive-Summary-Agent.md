# Executive Summary Agent

## Purpose

The Executive Summary Agent generates concise, structured executive-level summaries of product intelligence for C-suite reviews, board updates, investor conversations, and leadership team meetings. It synthesizes the full breadth of product signals, decisions, and outcomes into a brief, evidence-backed narrative appropriate for time-constrained senior audiences. It is the agent that answers "what does product tell us this week?" in a format a CPO can read in 3 minutes.

---

## Inputs

| Input | Source | Format | Required |
|-------|--------|--------|---------|
| Approved opportunity briefs (past 30 days) | Brief approval records | Structured JSON | Yes |
| Priority decisions (past 30 days) | Prioritization approval records | Decision log | Yes |
| Risk briefs (past 30 days) | Risk Watchdog outputs | Risk records | Yes |
| North Star metric and OKR progress | PM configuration + analytics | Current OKR status, metric values | If available |
| Business impact estimates from briefs | Aggregated brief data | ARR at risk, pipeline unblocked, opportunity value | Yes |
| Knowledge graph summary statistics | Knowledge graph | Entity counts, decision history, signal volume trends | Yes |

---

## Outputs

| Output | Format | Recipient |
|--------|--------|----------|
| Weekly executive summary | Structured brief (1–2 pages) | CPO / VP Product |
| Quarterly product narrative | Longer narrative with evidence table | Board / Investor audience |
| Stakeholder-specific snapshot | Audience-specific version of summary | Individual executive |
| Decision quality report | % of decisions backed by evidence, acceptance rate, confidence calibration | Leadership review |

---

## Executive Summary Structure

**Standard weekly summary structure:**

```
1. Signal Volume Snapshot (4 lines max)
   - Total signals ingested this week: [N]
   - Top 3 signal categories by volume: [list]
   - Week-over-week trend: up/flat/down
   - Active risk alerts: [N] open

2. Key Opportunities (top 3 from approved briefs)
   - Each opportunity: 2 lines. Title. Evidence summary. Confidence score. Recommended action.

3. Priority Decisions Made (this week)
   - Each decision: 1 line. What was decided. Evidence strength. Business impact estimate.

4. Active Risks (P1/P2 only)
   - Each risk: 2 lines. Issue. ARR at risk. Current mitigation status.

5. Evidence-Backing Rate
   - This week: X% of roadmap decisions backed by cited evidence (vs. target 70%)
   - Trend vs. last 4 weeks

6. PM Acceptance Rate
   - Agent output acceptance rate this week: X% (vs. target 75%)

7. Looking Ahead (optional, PM-added)
   - [PM adds forward-looking items; agent does not speculate about the future]
```

---

## Reasoning Pattern

```
Quarterly board-level synthesis:

Step 1 [ACT]: Load all approved briefs and priority decisions from the past quarter
Step 2 [ACT]: Load all risk events and their resolutions
Step 3 [REASON]: What are the 3–5 most significant themes this quarter?
Step 4 [ACT]: Retrieve evidence: what business impact do these decisions collectively represent?
Step 5 [REASON]: What is the narrative of this quarter's product intelligence? What changed?
Step 6 [REASON]: What does the evidence-backing rate trend say about how the team makes decisions?
Step 7 [ACT]: Generate structured narrative with evidence citations
Step 8 [REASON]: Is this appropriate for a board audience? Is it too tactical? Too long?
Step 9 [ACT]: Trim to board-appropriate length (max 1 page)
Step 10 [OUTPUT]: Deliver to PM for review
```

---

## Guardrails

1. **No forward speculation:** The Executive Summary Agent summarizes what happened and what the evidence shows. It does not make predictions about future market conditions, competitive dynamics, or product outcomes that are not grounded in data from the corpus.

2. **Aggregated, not granular:** Executive summaries do not include individual customer names unless specifically configured (and authorized) for the specific audience. Business impact is expressed as ranges ("$500K–$1.2M ARR at risk") not as specific account-level figures.

3. **Evidence-backed claims only:** Every metric cited in an executive summary must be sourced from the actual data in the system — not estimated or approximated from LLM training data. If a metric is unavailable, the summary says "[Metric unavailable — data pipeline not connected]" rather than interpolating.

---

## Quality Metrics

| Metric | Target |
|--------|--------|
| Executive summary acceptance rate | >80% approved without major edit |
| CPO/VP time to review (self-reported) | <5 minutes for weekly summary |
| Metric accuracy (values verified against source) | 100% of cited metrics match source data |
| Quarterly narrative adoption rate | >70% of prepared narratives used in leadership reviews |
| Summary generation latency (p95) | <2 minutes for weekly summary |
