# Retrieval Strategy

## Overview

The retrieval strategy defines how the RAG system selects the best evidence from the corpus for each agent query. The goal is to return evidence that is simultaneously:

- **Relevant:** Semantically related to the query topic
- **Precise:** About the specific feature area, customer segment, or product domain being queried
- **Recent:** Not stale relative to current product state
- **Diverse:** From multiple independent sources, not redundant copies of the same signal

---

## Query Formulation

Before retrieval, the Orchestration Layer formulates a retrieval query from the agent's current context. Query formulation is not a single query — it is a set of targeted queries per information need.

### Single-Query Retrieval
For simple evidence gathering (Risk Watchdog looking for signals about a specific category):
```
Query: "webhook failures integration errors customers cannot connect"
Filters: date_range=last_90_days, source_type=all, tenant_id=tenant_abc
```

### Multi-Query Retrieval (Insight Synthesizer)
For brief generation, the Insight Synthesizer runs multiple queries in parallel to maximize evidence coverage:
```
Query 1 (primary): [signal cluster summary]
Query 2 (customer voice): "customers asking about [feature area]"
Query 3 (business impact): "[feature area] deal blocker sales pipeline"
Query 4 (technical): "[feature area] technical implementation constraints"
Query 5 (historical): "prior decisions related to [feature area]"
```

This multi-query approach increases recall at the cost of more retrieval calls — worth the tradeoff for high-stakes brief generation.

### Per-Section Retrieval (PRD Agent)
For PRD generation, each section has its own targeted retrieval query:
```
Section: Problem Statement → "customer pain around [feature area]"
Section: User Stories → "[feature area] user needs use cases"
Section: Edge Cases → "edge cases error handling [feature area] prior features"
Section: Success Metrics → "[feature area] metrics KPIs measurement"
```

---

## Retrieval Pipeline

```
Query formulation
     │
     ▼
┌────────────────────────────────────────────────────────────────┐
│ Parallel Retrieval                                              │
│                                                                  │
│  ┌─────────────────────────────┐  ┌────────────────────────┐   │
│  │  Dense Retrieval             │  │  Sparse Retrieval       │   │
│  │  (Vector similarity)         │  │  (BM25 keyword)         │   │
│  │                              │  │                          │   │
│  │  1. Embed query              │  │  1. Tokenize query      │   │
│  │  2. ANN search in namespace  │  │  2. BM25 score against  │   │
│  │  3. Apply metadata filters   │  │     inverted index      │   │
│  │  4. Return top 20 candidates │  │  3. Return top 10 cands │   │
│  └──────────────┬──────────────┘  └──────────┬─────────────┘   │
│                 │                             │                  │
│                 └──────────────┬──────────────┘                 │
│                                │                                 │
│              ┌─────────────────▼─────────────────┐             │
│              │  Reciprocal Rank Fusion (RRF)       │             │
│              │  Combine dense + sparse rankings    │             │
│              │  Score = 1/(k+rank_dense) +         │             │
│              │          1/(k+rank_sparse)  (k=60)  │             │
│              └─────────────────┬─────────────────┘             │
└────────────────────────────────┼────────────────────────────────┘
                                  │
                    ┌─────────────▼─────────────┐
                    │  Cross-Encoder Re-Ranking   │
                    │  Score top 30 candidates    │
                    │  using cross-encoder model  │
                    │  Select top 5–8 for LLM    │
                    └─────────────┬─────────────┘
                                  │
                    ┌─────────────▼─────────────┐
                    │  Evidence Diversity Check   │
                    │  Ensure top-8 includes      │
                    │  ≥2 source types if possible │
                    └─────────────┬─────────────┘
                                  │
                    Final evidence set → LLM context
```

---

## Metadata Filters

Metadata filters reduce the search space before embedding similarity is computed. This improves both latency and precision.

**Always-applied filters:**
- `tenant_id = [current tenant]` — enforced at infrastructure layer

**Agent-applied filters (vary by workflow):**
```python
# Insight Synthesizer — brief generation
filters = {
    "tenant_id": tenant_id,
    "date": {"$gte": ninety_days_ago},  # Recency filter
    "product_area": {"$in": detected_product_areas},  # Scope filter
}

# Risk Watchdog — spike detection
filters = {
    "tenant_id": tenant_id,
    "signal_type": {"$in": ["bug_report", "churn_risk", "support_escalation"]},
    "date": {"$gte": thirty_days_ago},
}

# PRD Agent — section retrieval
filters = {
    "tenant_id": tenant_id,
    "product_area": brief.product_area,
    # No date filter — historical context is valuable for PRD generation
}
```

---

## Evidence Diversity Enforcement

After re-ranking, the system checks source diversity in the top-8 selection. If all 8 candidates come from the same source type (e.g., all Slack messages), the system:
1. Keeps the top 5 from the dominant source type
2. Forces inclusion of the top-ranked candidate from each available alternative source type
3. Total is capped at 8 chunks

This prevents a single noisy channel (a busy Slack thread) from dominating the evidence and inflating confidence in a one-source brief.

---

## Retrieval Quality Evaluation

The retrieval system is evaluated separately from the end-to-end generation quality:

| Metric | Target | Method |
|--------|--------|--------|
| Top-8 relevance rate | >75% of chunks relevant | Human eval on golden query set (monthly) |
| Recall@20 (relevant chunks in top 20) | >85% | Human eval on golden query set |
| Source diversity in top-8 | ≥2 source types in 70% of queries | Production monitoring |
| Cross-encoder re-ranking lift | >15% precision improvement over RRF alone | A/B evaluation on golden dataset |
| Retrieval latency p95 | <10 seconds | Production monitoring |
