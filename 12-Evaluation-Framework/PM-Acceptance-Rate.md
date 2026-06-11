# PM Acceptance Rate

## Definition

**PM Acceptance Rate** is the percentage of agent-generated outputs that a PM approves without making major edits.

This is the single most important AI quality metric in ProductPilot OS. It measures whether the agents are producing work that a PM would have produced themselves — just faster. If the acceptance rate is high, the agents are genuinely amplifying PM capability. If it's low, the agents are producing drafts that require substantial PM work to salvage — and adoption will stall.

---

## Precise Calculation

```
PM Acceptance Rate =
  (Outputs approved with edit_distance_score ≤ 0.20)
  ─────────────────────────────────────────────────
  (Total outputs approved OR rejected)

Where:
  edit_distance_score = 1 - (shared_content / total_content)
  
  "Major edit" threshold: edit_distance_score > 0.20
  (PM changed more than 20% of the generated content)
  
  "Deferred" outputs: excluded from denominator
  (PM has not acted — neither approved nor rejected)
```

---

## Measurement Mechanics

**Edit distance scoring:**
When a PM edits a brief or PRD, the system compares the pre-edit and post-edit versions using normalized edit distance (Levenshtein distance over word count). A score of 0.00 = no changes made. A score of 1.00 = complete rewrite. Threshold 0.20 = "substantial PM contribution."

**Per-output tracking:**
PM acceptance rate is tracked per:
- Output type (brief, prioritization brief, PRD, stakeholder communication, risk brief)
- Agent (which agent generated the output)
- PM (which PM approved/rejected)
- Model version (to detect regression after model changes)
- Time window (7-day, 30-day, 90-day rolling)

---

## Target Performance

| Output Type | V1 Launch Target | V2 Target | Mature Target |
|-------------|-----------------|-----------|--------------|
| Opportunity Brief | >65% | >75% | >80% |
| Prioritization Brief | >60% | >70% | >75% |
| PRD (first draft) | >55% | >65% | >70% |
| Stakeholder Communication | >65% | >75% | >80% |
| Risk Brief | >70% | >80% | >85% |
| **Overall weighted average** | **>63%** | **>73%** | **>78%** |

Note: PRDs are expected to have a lower acceptance rate because PMs invest significant time in refining requirements documents regardless of how good the first draft is.

---

## PM Acceptance Rate Segments

The metric is most useful when segmented:

**By output type:** Which outputs need the most improvement? PRDs consistently lower than briefs is expected. Briefs lower than stakeholder communications is a signal that brief quality needs attention.

**By PM role:** Senior PMs and Staff PMs may accept at lower rates because their standards are higher — this is acceptable. The metric should not be interpreted as "higher acceptance = better PM." It measures agent quality, not PM quality.

**By product area:** Low acceptance rate in a specific product area (e.g., "payments") may indicate that the evidence corpus for that area is thin, or that the agents need better domain calibration.

**By model version:** Acceptance rate on the new model vs. current model is the primary signal for model change decisions.

---

## Acceptance Rate vs. Usage Rate

PM Acceptance Rate measures quality. Usage rate (daily active PMs, outputs generated per PM per week) measures adoption. Both matter, but differently.

A product with 90% acceptance rate but 20% PM usage means the system is high-quality but not embedded in workflow. A product with 40% acceptance rate but 80% PM usage means PMs are using it but doing significant rework — they're getting value from starting points, but the quality needs improvement.

The goal is high acceptance AND high usage.

---

## Acceptance Rate as a PM Feedback Mechanism

When the PM acceptance rate is below target for a specific output type:

**Step 1: Diagnose.** Is it low acceptance across all PMs, or concentrated in a few? Is it one specific section of the output that's always edited? Is there a common rejection reason?

**Step 2: Root cause.** Is it an evidence problem (thin corpus for this product area), a prompt problem (instructions not constraining the output correctly), or a model problem (model not following instructions)?

**Step 3: Improve.** Targeted prompt changes for identified patterns. Retrieval tuning for thin-evidence product areas. Model upgrade or fine-tuning if systemic.

**Step 4: Measure.** Re-deploy. Monitor acceptance rate for the next 7 days. Did it improve?

This diagnosis-improvement-measurement loop is the core mechanism by which the product improves over time.

---

## What Acceptance Rate Does Not Measure

- **Whether the decision was right:** A PM can accept a well-structured brief based on incomplete evidence and make a bad decision. Acceptance rate measures agent output quality, not decision quality.

- **PM satisfaction:** A PM who consistently accepts outputs at 60% because they've learned to correct a specific systematic flaw may be less satisfied than a PM who occasionally rejects outputs at 20%.

- **Output complexity appropriateness:** A PRD for a simple bug fix and a PRD for a major new feature are both counted equally. Complex features reasonably require more PM intervention.

These limitations are why PM Acceptance Rate is the primary metric but not the only metric in the eval stack.
