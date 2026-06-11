# Component Evaluations

## Overview

Component evaluations are unit tests for AI components. They verify that individual parts of the AI pipeline perform correctly in isolation, separate from end-to-end evaluation. Component evals run automatically in the CI/CD pipeline before every deployment.

---

## Why Component Evals Matter

End-to-end PM Acceptance Rate is the right primary metric for overall system quality. But it tells you when something is wrong, not where. Component evals tell you where.

If PM acceptance rate drops, component evals help isolate: Is it a retrieval problem (relevant evidence not being found)? A classification problem (signals misclassified)? A confidence calibration problem (scores not matching evidence quality)? An entity extraction problem (customers not being identified correctly)?

---

## Component 1: Signal Classification

**What it measures:** Does the classification model correctly categorize signals into signal types?

**Test methodology:**
- Golden classification dataset: 500 signals with human-labeled signal types
- Signal types: feature_request, bug_report, churn_risk, competitive_mention, technical_constraint, general_feedback
- Run classifier on all 500; compare predictions to human labels

**Metrics:**
| Metric | Target | Deployment Gate |
|--------|--------|----------------|
| Overall accuracy | >85% | <80% = deployment blocker |
| Precision (per class) | >80% for all classes | <70% on any class = deployment blocker |
| Recall (per class) | >80% for all classes | <70% on any class = deployment blocker |
| F1 score (macro) | >0.83 | <0.78 = deployment blocker |

**Dataset refresh:** 50 new labeled examples added quarterly.

---

## Component 2: Retrieval Quality

**What it measures:** Does the retrieval pipeline return relevant evidence in the top-8?

**Test methodology:**
- Golden retrieval dataset: 100 queries with human-labeled relevant document sets
- Queries cover: feature areas, customer needs, risk patterns, historical decisions
- Run retrieval pipeline on all 100 queries; compute precision and recall at k

**Metrics:**
| Metric | Target | Deployment Gate |
|--------|--------|----------------|
| Recall@20 (relevant docs in initial retrieval) | >85% | <80% = deployment blocker |
| Precision@8 (relevant docs in final selection) | >75% | <70% = deployment blocker |
| Re-ranking lift (vs. RRF-only baseline) | >15% precision improvement | <10% = investigate |
| BM25 contribution rate | 15–30% of final selections | <10% = sparse index quality issue |

---

## Component 3: Entity Extraction

**What it measures:** Does the entity extraction pipeline correctly identify product areas, customer names, and signal entities?

**Test methodology:**
- Golden entity dataset: 200 signals with human-labeled entity annotations
- Entity types: product_area, customer_name, feature_name, error_code, integration_name
- Run entity extraction; compare to labels

**Metrics:**
| Entity Type | Precision Target | Recall Target |
|-------------|-----------------|--------------|
| product_area | >85% | >80% |
| customer_name | >90% | >85% |
| feature_name | >80% | >75% |
| error_code | >95% | >90% |

---

## Component 4: Confidence Score Calibration

**What it measures:** Is the confidence score accurately predictive of PM acceptance probability?

**Test methodology:**
- From production data (last 90 days): 500 briefs with confidence scores and PM approval/rejection outcomes
- Plot: confidence score buckets vs. PM acceptance rate
- A well-calibrated score should show strong correlation

**Metrics:**
| Metric | Target |
|--------|--------|
| Pearson r (confidence score vs. PM acceptance rate) | >0.65 |
| Mean absolute error (confidence vs. observed acceptance rate) | <15% |
| High-confidence accuracy (>80 score → acceptance rate) | >75% acceptance observed |
| Low-confidence accuracy (<40 score → rejection rate) | >50% rejection observed |

---

## Component 5: Citation Binding Accuracy

**What it measures:** Does the citation injector correctly bind generated claims to their source evidence chunks?

**Test methodology:**
- Golden citation dataset: 100 generated outputs with human-verified correct citations
- Run citation validator; compare validated citations to human-verified set

**Metrics:**
| Metric | Target |
|--------|--------|
| Valid citation rate | >99% of citations are valid chunk IDs |
| Uncited claim detection rate | >95% of uncitable claims flagged |
| False citation rate (correct chunk ID but wrong claim) | <1% |

---

## Component 6: PII Detection

**What it measures:** Does the PII scanner correctly detect and redact PII in generated outputs?

**Test methodology:**
- Golden PII dataset: 100 outputs with known PII instances (emails, phone numbers, financial values above threshold, personal names in non-business context)
- Run PII scanner; compare detections to known instances

**Metrics:**
| Metric | Target |
|--------|--------|
| PII recall (detected PII / total known PII) | >98% |
| PII precision (true PII / flagged content) | >95% |
| False positive rate (non-PII flagged as PII) | <5% |

---

## CI/CD Integration

All component evals run automatically on every pull request and before every production deployment:

```yaml
# .github/workflows/ai-evals.yml
eval_jobs:
  - name: classification_eval
    trigger: on_pr + on_deploy
    dataset: golden_classification_v4.jsonl
    pass_threshold: f1 > 0.78

  - name: retrieval_eval  
    trigger: on_pr + on_deploy
    dataset: golden_retrieval_v3.jsonl
    pass_threshold: precision_at_8 > 0.70

  - name: entity_extraction_eval
    trigger: on_pr + on_deploy
    dataset: golden_entities_v2.jsonl
    pass_threshold: all entity precision > 0.80

  - name: confidence_calibration_eval
    trigger: weekly (production data)
    pass_threshold: pearson_r > 0.65

  - name: citation_binding_eval
    trigger: on_pr + on_deploy
    dataset: golden_citations_v2.jsonl
    pass_threshold: valid_citation_rate > 0.99

  - name: pii_detection_eval
    trigger: on_pr + on_deploy
    dataset: golden_pii_v1.jsonl
    pass_threshold: pii_recall > 0.98
```

Any eval failure blocks the PR or deployment until the issue is resolved.
