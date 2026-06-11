# Evaluation Stack

## Why Evaluation Is a First-Class Concern

Most AI products treat evaluation as an afterthought. ProductPilot OS treats it as infrastructure.

The reason is simple: the only way to know if the product is improving is to measure it against a consistent baseline. The only way to know if a model or prompt change is safe to deploy is to run it against a golden dataset. The only way to earn PM trust through continuous improvement is to have a feedback loop that is:
1. Always running (online evals on real production traffic)
2. Rigorous when it needs to be (human evals for major changes)
3. Scalable at low cost (LLM-as-judge for high-volume metrics)
4. Component-level (unit-testable, not just end-to-end testable)

The eval stack is the difference between a product that improves predictably and one that drifts.

---

## Eval Stack Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        Eval Stack                                │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │  1. Online Evals (Production monitoring, always running)  │   │
│  │     • PM acceptance rate by agent, model version         │   │
│  │     • Hallucination rate (automated claim checking)      │   │
│  │     • Citation coverage rate                             │   │
│  │     • Latency p50/p95/p99 by agent                      │   │
│  │     • Error rate by agent and step                       │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │  2. LLM-as-Judge Evals (Automated quality scoring)       │   │
│  │     • Coherence scoring (output is well-reasoned)        │   │
│  │     • Faithfulness scoring (claims match evidence)       │   │
│  │     • Relevance scoring (evidence matches query)         │   │
│  │     • Helpfulness scoring (output is actionable)         │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │  3. Component Evals (Unit tests for AI components)       │   │
│  │     • Retrieval recall/precision on golden queries       │   │
│  │     • Classification accuracy on golden signal set       │   │
│  │     • Confidence calibration on golden brief set         │   │
│  │     • Entity extraction precision on golden documents    │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │  4. Human Evals (Ground truth, monthly cadence)          │   │
│  │     • PM panel review of sampled briefs                  │   │
│  │     • Expert review of hallucination candidates          │   │
│  │     • Golden dataset labeling and refresh                │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │  5. Golden Dataset (Ground truth library)                │   │
│  │     • Curated query → expected output pairs              │   │
│  │     • PM-labeled high-quality briefs                     │   │
│  │     • Adversarial examples (edge cases that should fail) │   │
│  └──────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
```

---

## Eval Cadences

| Eval Type | Cadence | Trigger |
|-----------|---------|---------|
| Online evals | Continuous (real-time) | Every agent execution |
| LLM-as-judge | Continuous (sampled) | 10% sample of all agent outputs |
| Component evals (retrieval) | Daily | Automated CI/CD pipeline |
| Component evals (classification) | Daily | Automated CI/CD pipeline |
| Human evals | Monthly | Scheduled + on major model/prompt changes |
| Golden dataset refresh | Quarterly | Scheduled + after major product changes |

---

## Eval → Deployment Gate

Every model or prompt change must pass the following before production deployment:

1. **Component eval regression check:** All component eval scores must be within 5% of baseline
2. **LLM-as-judge regression check:** Coherence, faithfulness, relevance scores must be within 5% of baseline
3. **Hallucination rate check:** New version must not increase hallucination rate above current production baseline
4. **PM acceptance rate prediction:** Shadow deployment on 5% of traffic for 48 hours; acceptance rate on shadow traffic must be ≥ baseline

Any regression triggers a blocking review before the change proceeds to full deployment.

---

## Eval Infrastructure

**Eval database:** Separate from production database; receives copies of agent inputs, outputs, PM actions, and eval scores.

**Eval dashboard:** Internal engineering dashboard showing all eval metrics with time-series charts, regression detection alerts, and drill-down into individual eval failures.

**PM feedback integration:** Every PM approval, rejection, and override is automatically ingested as an eval signal. No manual labeling required for the online eval layer.
