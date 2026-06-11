# Re-Ranking Strategy

## Why Re-Ranking Matters

Initial retrieval (dense + sparse) optimizes for recall: getting the relevant chunks into the top-20 candidates. Re-ranking optimizes for precision: ensuring the final 5–8 chunks delivered to the LLM are the highest-quality, most relevant evidence.

This two-stage approach reflects a fundamental principle: it is computationally cheaper to retrieve broadly and re-rank narrowly than to optimize initial retrieval for precision. The re-ranker runs a more expensive, higher-quality relevance model — but only on the 30 candidates produced by initial retrieval, not on the full corpus.

---

## Re-Ranking Architecture

**Model:** Cross-encoder (e.g., ms-marco-MiniLM-L-6-v2 or equivalent)

A cross-encoder scores each (query, chunk) pair jointly — unlike the bi-encoder used for embedding, which scores query and chunks independently. The cross-encoder sees both query and candidate text together, enabling it to reason about relevance in a way that bi-encoders cannot.

**Input:** Top 30 candidates from RRF-fused dense + sparse retrieval  
**Output:** Re-scored ranking of all 30 candidates → select top 5–8

---

## Re-Ranking Process

```
Input: Query + Top 30 candidates from RRF

For each of the 30 candidates:
  cross_encoder_score = model.score(query, candidate.text)

Sort candidates by cross_encoder_score (descending)

Apply diversity filter:
  - Count source types in current top selection
  - If top-8 is dominated by 1 source type: replace lowest-scoring
    instance of dominant type with highest-scoring instance of 
    underrepresented type
  - Target: ≥2 source types in final selection

Output: Top 5–8 re-ranked candidates → LLM context injection
```

---

## Re-Ranking Quality Goals

| Goal | Mechanism |
|------|-----------|
| Precision over recall | Cross-encoder re-ranker significantly improves precision vs. bi-encoder retrieval alone |
| Diversity | Source diversity enforcement in final selection |
| Freshness awareness | Recency bonus applied in re-ranking score: `final_score = cross_score * (1 + 0.05 * recency_multiplier)` where `recency_multiplier` = 0 for >90 days old, up to 1 for <7 days old |
| Threshold enforcement | Chunks with cross-encoder score below 0.3 are excluded from selection even if they rank in top 8 (insufficient relevance) |

---

## Re-Ranking Latency

Cross-encoder re-ranking is the highest-latency step in the retrieval pipeline.

**Target:** <3 seconds for 30 candidates (p95)  
**Optimization:** Re-ranker runs as a hosted service, not in-process with the LLM call. This allows parallel execution of re-ranking and LLM prompt construction.

**Fallback:** If re-ranker service is unavailable, system falls back to RRF-fused ranking without re-ranking. PM-visible note: "Evidence selection quality may be reduced — re-ranking service unavailable." This fallback exists so that brief generation is never blocked by a re-ranker outage.

---

## Re-Ranking for Different Agent Types

| Agent | Top-k Initial Retrieval | Re-ranked Selection | Rationale |
|-------|------------------------|--------------------|-----------| 
| Insight Synthesizer | 30 (dense: 20, BM25: 10) | Top 8 | Maximum evidence quality for brief generation |
| Prioritization Agent (per RICE dim.) | 20 (dense: 15, BM25: 5) | Top 5 | Per-dimension evidence, smaller context needed |
| PRD Agent (per section) | 20 (dense: 15, BM25: 5) | Top 5 | Per-section evidence, parallel section generation |
| Risk Watchdog | 30 (dense: 20, BM25: 10) | Top 8 | Risk briefs need strong evidence quality |
| Meeting Intelligence | 10 (dense: 8, BM25: 2) | Top 5 | Context enrichment only, not primary evidence |

---

## Re-Ranking Evaluation

The re-ranker is evaluated monthly against a golden query set with human-labeled relevance:

| Metric | Target |
|--------|--------|
| nDCG@8 (normalized discounted cumulative gain) | >0.75 |
| Precision improvement over RRF baseline | >15% |
| Diversity achievement (≥2 source types in top-8) | >70% of queries |
| Low-relevance exclusion rate | >80% of sub-threshold chunks excluded correctly |
