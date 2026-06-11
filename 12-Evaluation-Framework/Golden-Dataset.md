# Golden Dataset

## Purpose

The Golden Dataset is the ground truth library for evaluating ProductPilot OS AI quality. It is a curated set of (input, expected output) pairs that represents the full range of use cases, quality levels, and edge cases the system must handle correctly.

The golden dataset serves two functions:
1. **Regression testing:** Run before every model or prompt change to verify the change does not degrade quality on known-good cases.
2. **Baseline calibration:** Measure production quality against a stable reference rather than against itself (which would mask drift).

---

## Dataset Composition

### Tier 1: High-Quality Positive Examples (40% of dataset)
PM-approved outputs with low edit distance (edit_distance_score < 0.10). These represent what excellent agent output looks like.

**Sources:**
- Production PM approvals with no edits (manual selection by trust team)
- PM-curated "best brief" examples from beta testing
- Synthetic examples created by domain experts for underrepresented scenarios

**Use:** Test that the current system would produce similarly high-quality output for the same input. Regression here means quality degradation.

### Tier 2: Acceptable Positive Examples (25% of dataset)
PM-approved outputs with moderate edits (edit_distance_score 0.10–0.20). These represent the floor of acceptable agent quality.

**Use:** Test that the current system produces at least this quality level. Regression below this tier is a deployment blocker.

### Tier 3: Negative Examples — PM Rejections (20% of dataset)
PM-rejected outputs with documented rejection reasons. These represent known failure modes that the system must not repeat.

**Use:** Test that the current system does not produce output that would trigger the known rejection patterns. Any improvement in Tier 3 coverage is a quality win.

### Tier 4: Adversarial Examples (15% of dataset)
Hand-crafted inputs designed to trigger known failure modes: hallucination triggers, PII exposure scenarios, thin evidence cases, conflicting signal inputs, edge cases from the edge case specification.

**Use:** Test that safety guardrails hold against adversarial inputs. Any Tier 4 failure is treated as a critical safety regression.

---

## Dataset Structure

Each golden dataset entry:

```json
{
  "example_id": "golden_ex_001",
  "tier": 1,
  "scenario": "Opportunity brief — strong evidence, enterprise customer",
  "input": {
    "signal_cluster": [...],
    "evidence_set": [...],
    "crm_data": {...},
    "okrs": [...]
  },
  "expected_output": {
    "confidence_score_range": [75, 95],
    "required_claims": ["SSO", "enterprise accounts", "renewal risk"],
    "forbidden_claims": [],
    "citation_coverage_min": 0.95,
    "structure": "valid opportunity brief schema"
  },
  "human_quality_score": 4.5,
  "created_by": "trust_team",
  "created_at": "2025-03-01",
  "last_reviewed": "2025-03-01"
}
```

---

## Dataset Maintenance

**Growth cadence:** 20–30 new examples added monthly from:
- PM feedback (rejection reason database)
- New edge cases identified in production
- New scenarios added with product feature expansion

**Review cadence:** Full dataset reviewed quarterly:
- Are expected outputs still representative of what excellent looks like?
- Have product area definitions changed?
- Are adversarial examples still relevant?
- Are any examples from deprecated features?

**Size targets:**
- V1 launch: 200 examples
- V1 + 6 months: 400 examples
- V2 launch: 700 examples
- Mature: 1,000+ examples across all agent types and output types

---

## Dataset Split

| Split | Size | Purpose |
|-------|------|---------|
| Eval (primary) | 60% | Run in CI/CD pipeline for regression testing |
| Calibration | 25% | Calibrate scoring thresholds; not used for deployment gating |
| Held-out test | 15% | Reserved for major model change decisions; never used in development |

The held-out test set is strictly controlled. Using it for development decisions would inflate apparent performance.

---

## Quality Score Thresholds (for Deployment Gating)

| Tier | Pass Threshold | Fail = Deployment Blocker? |
|------|---------------|---------------------------|
| Tier 1 (high quality positives) | ≥90% pass | Yes — regression in Tier 1 blocks deployment |
| Tier 2 (acceptable positives) | ≥85% pass | Yes — regression in Tier 2 blocks deployment |
| Tier 3 (rejection negatives) | ≥80% correctly rejected | Yes — if the system now accepts previously-rejected patterns |
| Tier 4 (adversarial) | 100% pass | Yes, always — any adversarial failure blocks deployment |
