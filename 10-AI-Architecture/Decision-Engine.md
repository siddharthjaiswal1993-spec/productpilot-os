# Decision Engine

## Purpose

The Decision Engine is the component of ProductPilot OS that converts raw AI analysis into actionable, evidence-backed product decisions. It is not a single model — it is a structured process: evidence assembly → confidence scoring → recommendation generation → HITL approval → decision recording → outcome tracking.

The Decision Engine makes decisions auditable, comparable, and improvable over time.

---

## What a "Decision" Means in ProductPilot OS

A product decision in ProductPilot OS is a formal record, not just a priority vote:

```json
{
  "decision_id": "dec_sso_scim_20250315",
  "decision_type": "prioritization",
  "brief_id": "brief_abc123",
  "decision": "Prioritize Enterprise SSO/SCIM as P1 for Q2",
  "evidence_summary": "8 signals across 4 sources; 87/100 confidence; $2.1M ARR at risk",
  "rice_score": 11963,
  "strategic_alignment": 9.2,
  "pm_id": "pm_alex_chen",
  "pm_name": "Alex Chen",
  "approved_at": "2025-03-15T14:32:11Z",
  "edit_count": 2,
  "edit_distance_score": 0.12,
  "outcome_tracking": {
    "status": "in_progress",
    "expected_outcome": "Unblock $2.1M ARR expansion by Q3",
    "actual_outcome": null
  }
}
```

This record answers: What was decided? What evidence supported it? How confident were we? Who approved it? What did we expect to happen? What actually happened?

---

## Decision Quality Score

Every approved decision receives a Decision Quality Score (DQS) at the time of approval. This is a retrospective metric — it doesn't change the decision, but it benchmarks the quality of the evidence base.

**DQS Components:**

| Component | Weight | Measurement |
|-----------|--------|-------------|
| Confidence score at approval | 40% | Confidence Engine score (0–100) |
| Evidence citation rate | 20% | % of brief claims with citations |
| RICE evidence completeness | 20% | % of RICE dimensions with cited evidence |
| PM edit distance | 20% | Low edit = high alignment between agent and PM; high edit = agent needed significant correction |

**DQS Interpretation:**

| DQS Range | Meaning |
|-----------|---------|
| 80–100 | High-quality decision — strong evidence, well-structured brief, minimal PM correction needed |
| 60–79 | Good decision — evidence present but some gaps; PM added meaningful corrections |
| 40–59 | Adequate decision — thin evidence; PM did significant correction work |
| <40 | Low-quality decision — insufficient evidence base; this decision type needs investigation |

---

## Decision Audit Trail

Every decision is permanently recorded in the immutable audit trail with:

1. **Decision creation:** When the opportunity brief was generated, by which agent, with which inputs
2. **Evidence at decision time:** The full evidence table, including sources, dates, excerpts, and strength ratings — immutable snapshot of what the PM saw when they decided
3. **PM approval action:** PM ID, timestamp, edit count, edit distance score
4. **HITL override records:** Any RICE dimension overrides, business impact edits, or strategic alignment adjustments made by the PM
5. **Downstream actions:** Jira updates, stakeholder communications, engineering escalations — all linked back to this decision record

**Why the evidence snapshot is immutable:** If the decision is questioned later, the organization needs to know exactly what evidence the PM had at the time. Signal corpus data changes as new signals arrive — the snapshot preserves the evidence state at the moment of decision.

---

## Decision History and Pattern Analysis

Over time, the Decision Engine builds a corpus of approved decisions. This corpus enables:

**Decision recall:** "Why did we build the bulk export feature?" → Query knowledge graph → Return the decision record with evidence table, RICE score, and PM approval record.

**Pattern analysis:** "What types of decisions do we make with thin evidence?" → Query decisions with DQS <50 → Identify systemic evidence gaps in specific product areas.

**Outcome correlation:** "Do our high-confidence decisions produce better outcomes than our low-confidence decisions?" → Match decision records to outcome records → Validate or recalibrate the confidence model.

**PM calibration:** "How often does PM Alex override the system's RICE scores?" → Query override records → Identify if Alex systematically over-weights one RICE dimension.

---

## Decision vs. Non-Decision Classification

Not every PM action in ProductPilot OS is a "decision" in the formal sense. The Decision Engine classifies:

**Formal decisions (recorded in decision log):**
- Opportunity brief approved and moving to prioritization
- Priority rank approved and recorded
- PRD approved for engineering
- Risk brief acknowledged and mitigation selected
- Roadmap item status changed (added, deferred, removed)

**Non-formal actions (tracked in event log, not decision log):**
- Signal reviewed but not actioned
- Brief drafted but not approved
- Draft communication generated but not sent

This distinction matters for the Evidence-Backing Rate metric — we measure the percentage of formal decisions that are backed by cited evidence, not the percentage of all PM interactions.

---

## North Star Connection

The Decision Engine directly drives the North Star Metric:

**North Star:** % of roadmap decisions backed by cited customer, technical, and business evidence

The Decision Engine is the system that:
1. Creates the decision records that are counted in the numerator/denominator
2. Stores the evidence citations that determine whether a decision is "backed"
3. Provides the audit trail that makes this metric externally auditable

A PM team using ProductPilot OS for 6 months will have a complete, queryable record of every product decision they made, the evidence behind each one, and whether those decisions produced the expected outcomes.
