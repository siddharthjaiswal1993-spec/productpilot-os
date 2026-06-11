# Prioritization Brief Template

**Purpose:** The Prioritization Brief scores a candidate priority item against the full backlog, with evidence-based input for each scoring dimension. It is the structured input to the Prioritization Engine and the audit record for every prioritization decision.

---

## Brief Header

| Field | Value |
|-------|-------|
| **Brief ID** | PB-[YYYY-MM-DD]-[###] |
| **Opportunity Title** | [From linked Opportunity Brief] |
| **Linked Opportunity Brief** | OB-[###] |
| **Prioritization Date** | [Date] |
| **PM Owner** | [Name] |
| **Status** | Pending / Scored / Approved / Deferred |

---

## RICE Score Breakdown

Each RICE dimension must include: the value, the evidence basis, and the confidence level.

### Reach

**Value:** [Estimated number of users/accounts affected per quarter]
**Evidence basis:** [How this number was determined — analytics data, customer count from CRM, estimated segment size]
**Source:** [Amplitude / Salesforce / CRM / PM estimate]
**Confidence:** High (verified data) / Medium (extrapolation) / Low (estimate)

---

### Impact

**Value:** [1 = minimal, 2 = low, 3 = medium, 4 = high, 5 = critical]
**Evidence basis:** [Customer quotes and business impact signals that justify this score]
**Supporting evidence:**
- "[Customer quote or signal]" — [Source, Date]
- "[Customer quote or signal]" — [Source, Date]
**Confidence:** High / Medium / Low

---

### Confidence (evidence confidence)

**Value:** [% — ties to the opportunity brief confidence score]
**Basis:** [Same as opportunity brief confidence score rationale]
**Note:** This dimension reflects evidence quality, not PM judgment confidence.

---

### Effort

**Value:** [Estimated engineering effort in person-weeks or sprints]
**Evidence basis:** [Engineering pre-assessment, historical similar features, rough estimate]
**Source:** [Engineering EM estimate / analogous feature estimate / PM estimate]
**Confidence:** High (formal estimate) / Medium (rough estimate) / Low (guess)

---

### RICE Score

**Formula:** (Reach × Impact × Confidence%) / Effort
**Calculated score:** [Value]
**Interpretation:**
- >10,000: Very high priority — strong candidate for next sprint
- 5,000–10,000: High priority — strong backlog candidate
- 2,000–5,000: Medium priority — in consideration
- <2,000: Lower priority — consider deferring

---

## Strategic Alignment Score

**Score:** [1–10]

| OKR | Alignment | Rationale |
|-----|-----------|-----------|
| [OKR 1 — e.g., "Win 5 enterprise deals >$200K ACV"] | High / Medium / Low / None | [Why] |
| [OKR 2] | | |
| [OKR 3] | | |

**Overall strategic alignment:** [Average or weighted score]

---

## Evidence Confidence Score

**Score:** [0–100 from linked Opportunity Brief]
**Quick summary:** [One sentence on what drives this score up/down]

---

## Business Impact Score

**Score:** [1–10]

| Impact Type | Value | Confidence | Notes |
|------------|-------|------------|-------|
| ARR at risk (churn prevention) | $[X] | H/M/L | |
| ARR blocked (expansion) | $[X] | H/M/L | |
| Deal pipeline affected | $[X] ACV | H/M/L | |
| NPS / satisfaction impact | [estimate] | H/M/L | |
| **Total estimated impact** | $[X] | | |

---

## Composite Priority Score

**Formula:** (RICE × 0.5) + (Strategic Alignment × 0.25 × 1000) + (Business Impact Score × 0.25 × 500)
**Composite Score:** [Value]

*Note: Composite score is a guide for sorting. Final priority is PM's decision with evidence brief as context.*

---

## Backlog Comparison

**How does this compare to the current top-5 backlog items?**

| Rank | Item | RICE Score | Evidence Confidence | Business Impact | Composite |
|------|------|-----------|---------------------|----------------|-----------|
| P1 Current | [Item name] | [Score] | [Score] | $[X] | [Score] |
| P2 Current | | | | | |
| P3 Current | | | | | |
| P4 Current | | | | | |
| P5 Current | | | | | |
| **This item** | **[Name]** | **[Score]** | **[Score]** | **$[X]** | **[Score]** |

**Suggested placement:** P[X] (displacing / adjacent to [item name])

---

## PM Recommendation

**Recommendation:** [ ] Prioritize at P[X] [ ] Defer [ ] Decline [ ] Investigate Further

**PM rationale:** [2–3 sentences explaining the PM's decision, acknowledging the evidence and any context not in the brief]

**Overrides or adjustments:** [Note any dimensions the PM has adjusted from the automated score and why]

---

## Approval Record

| Action | Name | Date | Notes |
|--------|------|------|-------|
| Brief generated | ProductPilot OS | | |
| PM review | [Name] | | Edit summary: |
| PM approval | [Name] | | Priority assigned: P[X] |
| Stakeholder notification | Auto | | Audiences: |

---

*This prioritization brief is the audit record for this decision. It is stored in the Product Knowledge Graph and retrievable for future reference, post-mortems, and decision pattern analysis.*
