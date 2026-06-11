# Confidence Engine

## Purpose

The Confidence Engine computes a 0–100 composite confidence score for every agent output. The score tells the PM how much to trust the evidence base behind an opportunity brief, a risk alert, or a prioritization recommendation. It is not a measure of LLM certainty — it is a measure of evidence quality and coverage.

---

## Why Confidence Scoring Matters

Without a confidence score, the PM faces a binary choice: trust the AI's output or don't. Both extremes are wrong.

A confidence score gives the PM calibrated information: "I can trust this brief (87/100) enough to approve it and move to PRD. This other brief (31/100) needs more evidence before I should prioritize it."

The Confidence Engine makes the system honest about what it knows and what it's uncertain about.

---

## The Five Confidence Components

Each component contributes up to 20 points to the composite score (total: 100 points):

### Component 1: Signal Volume (0–20 points)
**Question:** How many independent signals support this opportunity?

| Signal Count | Score |
|-------------|-------|
| 0 | 0 |
| 1 | 5 |
| 2–3 | 10 |
| 4–7 | 15 |
| 8+ | 20 |

**Rationale:** A single signal could be an outlier. Eight independent signals strongly suggest a real pattern.

### Component 2: Source Diversity (0–20 points)
**Question:** How many distinct data source types contributed evidence?

| Distinct Source Types | Score |
|----------------------|-------|
| 1 source type | 5 |
| 2 source types | 10 |
| 3 source types | 15 |
| 4+ source types | 20 |

**Rationale:** A pattern confirmed across Slack, Zendesk, CRM, and Gong is far more credible than one appearing only in Slack. Source diversity reduces the risk that a single noisy channel is distorting the picture.

### Component 3: Recency (0–20 points)
**Question:** How recently were the supporting signals generated?

| Recency Profile | Score |
|----------------|-------|
| All signals older than 90 days | 5 |
| Mix — most signals 31–90 days old | 10 |
| Most signals 8–30 days old | 15 |
| Most signals within 7 days | 20 |

**Rationale:** A need expressed 6 months ago may have been addressed, changed, or replaced by a new need. Recency indicates the signal represents the current state of the problem.

### Component 4: Business Impact Coverage (0–20 points)
**Question:** How well can we quantify the business impact of this opportunity?

| Coverage | Score |
|----------|-------|
| No revenue/business data available | 5 |
| Some business signal but no ARR data | 8 |
| ARR data for some accounts | 12 |
| ARR data for most accounts + business case estimate | 16 |
| ARR data + pipeline data + renewal risk data | 20 |

**Rationale:** A brief recommending we invest engineering time should ideally know the revenue implication. Thin business impact data doesn't mean the opportunity isn't real — but it means the prioritization case is weaker.

### Component 5: Representativeness (0–20 points)
**Question:** Do the signals represent a broad cross-section of customers, or are they clustered in one account, one team, or one channel?

| Representativeness | Score |
|-------------------|-------|
| All signals from 1 account or 1 channel | 5 |
| Signals from 2–3 accounts or channels | 10 |
| Signals from 4–7 accounts or 3+ channels | 15 |
| Signals from 8+ accounts or 5+ channels | 20 |

**Rationale:** A single enterprise customer loudly requesting a feature is qualitatively different from the same feature requested quietly across 15 accounts. Both are valuable signals, but they warrant different prioritization confidence.

---

## Score Interpretation

| Score Range | Interpretation | Recommended PM Action |
|------------|----------------|----------------------|
| 0–29 | Very low confidence — insufficient evidence | Do not prioritize yet; gather more signals |
| 30–49 | Low confidence — thin evidence | Generate brief with explicit warning; PM should validate independently |
| 50–69 | Moderate confidence — evidence supports the pattern | Proceed with standard PM review |
| 70–84 | High confidence — strong multi-source evidence | Can move to prioritization with standard review |
| 85–100 | Very high confidence — comprehensive, diverse, recent evidence | Can move forward with high trust in evidence base |

---

## Score Display

In the UI, the confidence score is displayed at three levels of detail:

**Level 1 (summary view):** Composite score (e.g., "87/100") with a color-coded badge (green >70, yellow 50–70, red <50)

**Level 2 (expanded):** Bar chart showing each component's contribution with a one-line interpretation per component

**Level 3 (evidence view):** Full evidence table with all source citations that contributed to the score

PM can click from any level to a deeper level without leaving the brief.

---

## Score Calibration

The Confidence Engine is calibrated against PM approval behavior:
- Briefs with scores >70 that are approved by PMs validate the scoring model
- Briefs with scores >70 that are rejected by PMs are analyzed to find calibration gaps
- Briefs with scores <50 that are approved by PMs are examined to understand when PMs override thin evidence with domain knowledge

This calibration loop runs monthly and feeds scoring weight adjustments.

---

## Score Immutability

Once a PM approves a brief, the confidence score at time of approval is permanently recorded in the decision audit trail. The score cannot be retroactively changed. If the PM subsequently discovers the evidence was incomplete, this is recorded as a decision quality note — not a score edit.

---

## Edge Cases

| Edge Case | Behavior |
|-----------|---------|
| Zero signals | Score blocked at 0; brief generation blocked; PM sees "Insufficient evidence" |
| All signals from same author | Flag in representativeness component; penalty applied |
| Signals are >180 days old | Recency score capped at 5; evidence marked "Historical — may not reflect current state" |
| CRM unavailable | Business impact coverage score defaults to 5; note: "CRM unavailable — business impact coverage is lower bound" |
| Signal volume is high but source diversity is 1 | Score reflects this asymmetry; recommendation to seek cross-source validation is shown |
