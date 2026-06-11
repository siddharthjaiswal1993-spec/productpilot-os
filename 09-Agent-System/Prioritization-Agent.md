# Prioritization Agent

## Purpose

The Prioritization Agent converts approved opportunity briefs into rigorous, evidence-backed prioritization scores. It assembles RICE scoring with cited evidence per dimension, computes strategic alignment against team OKRs, positions the opportunity within the current backlog, and delivers a structured prioritization brief for PM decision. The agent's goal is to replace opinion-based prioritization with an evidence-backed, auditable recommendation — while keeping the PM as the final decision-maker.

---

## Inputs

| Input | Source | Format | Required |
|-------|--------|--------|---------|
| Approved opportunity brief | Brief approval record | Structured JSON with evidence table | Yes |
| Current backlog | Jira / Linear integration | Backlog items with current scores and priorities | Yes |
| Team OKRs | PM configuration | OKR list with weights and current progress | Yes |
| Historical RICE calibration | Knowledge graph | Prior RICE scores and actual outcomes | If available |
| Customer ARR and tier data | CRM integration | Account tier, ARR, renewal date | If available |
| PM override history | Knowledge graph | Prior manual overrides for calibration context | If available |

---

## Outputs

| Output | Format | Recipient |
|--------|--------|----------|
| Prioritization brief | Structured brief → rendered UI | PM decision view |
| RICE breakdown | Table with evidence citations per dimension | Included in brief |
| Strategic alignment matrix | Table: OKR × opportunity score | Included in brief |
| Backlog comparison table | Table: opportunity vs. top 10 backlog items | Included in brief |
| Suggested priority rank | Position in current backlog with rationale | Included in brief |
| Jira update staging request | Staged Jira change for PM confirmation | Jira integration layer |

---

## Reasoning Pattern

**Pattern: Planner-Executor with RICE Evidence Assembly**

```
PLAN PHASE:
1. Load approved brief and evidence table
2. Identify which RICE dimensions have strong vs. weak evidence in the brief
3. Plan supplementary evidence retrieval per dimension if gaps exist

EXECUTE PHASE:

[Reach Scoring]
- Query: "How many users could benefit from this feature?"
- Sources: Signal corpus (user mentions), product analytics (if connected), CRM (account coverage)
- Evidence: Top 3 cited items; estimate range if exact count unavailable
- Output: Reach score (users/quarter) with confidence band and citations

[Impact Scoring]
- Query: "What is the improvement in user outcome if this feature exists?"
- Sources: Customer signal severity, comparable feature adoption data from knowledge graph
- Evidence: Cited signals with explicit severity indicators
- Output: Impact score (1–3 scale) with cited rationale

[Confidence Scoring]
- Input: Evidence volume, source diversity, signal recency from brief
- Calculate: Confidence score from brief's confidence components
- Output: Confidence percentage (0–100%) with citation

[Effort Scoring]
- Query: "What is the estimated engineering effort?"
- Sources: PM configuration, engineering notes in Jira, knowledge graph (similar features' actual effort)
- Note: If effort estimate is unavailable, default to "Medium (4 weeks)" with "[Engineering input required]" flag
- Output: Effort score (weeks) with citation or placeholder

[RICE Score Calculation]
- Formula: (Reach × Impact × Confidence) / Effort
- Calibration: Normalize against prior RICE scores in knowledge graph
- Output: RICE score with breakdown

[Strategic Alignment]
- Map opportunity to each active OKR
- Score alignment per OKR: 0 (none), 0.5 (partial), 1.0 (direct)
- Weight by OKR importance
- Output: Weighted alignment score (0–10) with OKR-by-OKR breakdown

[Backlog Comparison]
- Load top 10 backlog items with current RICE and strategic alignment scores
- Insert opportunity into ranked comparison table
- Identify suggested placement with rationale
- Output: Ranked comparison table
```

---

## Tools Used

- **Vector search:** Supplementary evidence retrieval for thin RICE dimensions
- **CRM lookup:** ARR and customer tier data for Reach and Impact scoring
- **Jira query:** Current backlog items with priorities and scores
- **Knowledge graph query:** Historical RICE scores, actual outcomes, PM override history
- **RICE calculator:** Formula execution with evidence aggregation
- **Strategic alignment scorer:** OKR matching and weighting

---

## Autonomy Level

**Read-only for all data access.** Generates a prioritization brief for PM review. Can stage Jira priority updates but requires explicit PM confirmation before execution.

The Prioritization Agent recommends. It does not decide.

---

## Guardrails

1. **Evidence-per-dimension requirement:** Each RICE dimension must have at least one cited evidence source. If a dimension has no evidence, it is flagged "[Evidence missing — PM estimate required]" and marked with an orange badge. The overall score is calculated but the brief is marked "Incomplete evidence for [dimension]."

2. **Effort estimate validation:** If effort estimate is not available from a connected engineering system or PM configuration, the system uses a default value clearly labeled as "Default estimate (unconfirmed by engineering)." The PM is warned to confirm with engineering before finalizing the priority.

3. **Override logging:** When a PM manually adjusts any RICE dimension score, the override is logged with original value, new value, PM name, timestamp, and reason (PM inputs free text or selects from a reason menu). This creates a calibration signal for future scoring improvements.

4. **Conflict detection:** If two PMs simultaneously approve conflicting priority decisions for the same backlog item, the system surfaces a conflict alert and requires explicit resolution rather than silently picking one.

---

## Human Approval Boundary

The Prioritization Agent generates a recommendation. The PM must:
1. Review the RICE breakdown and evidence per dimension
2. Review the strategic alignment matrix
3. Review the backlog comparison
4. Click "Approve Priority Decision" to record the decision
5. Separately confirm any Jira write (a second explicit confirmation)

The decision and both confirmations are logged in the immutable audit trail.

---

## Quality Metrics

| Metric | Target |
|--------|--------|
| RICE dimensions with cited evidence | >90% of dimensions |
| PM override rate (PM changes a dimension score) | <30% of briefs |
| PM acceptance rate (priority accepted without major override) | >70% |
| Effort estimate availability (from connected system or config) | >80% of briefs |
| Prioritization brief latency (p95) | <60 seconds |
