# Embedding Strategy

## Overview

Embeddings are the mathematical backbone of semantic search in ProductPilot OS. The embedding model converts text (signals, queries, documents) into high-dimensional vectors such that semantically similar text maps to nearby points in vector space. Retrieval then becomes a nearest-neighbor search problem: find the vectors in the corpus closest to the query vector.

The embedding strategy defines: which model to use, how to optimize embedding quality, how to handle domain-specific terminology, and how to manage the computational cost of embedding at scale.

---

## Model Selection

### Primary: OpenAI text-embedding-3-large

| Property | Value |
|----------|-------|
| Dimensions | 3072 |
| Context window | 8,191 tokens |
| Performance (MTEB) | State-of-art on retrieval benchmarks |
| Cost | ~$0.13 per 1M tokens |
| Latency (batch 256) | ~2–3 seconds |

**Why text-embedding-3-large over text-embedding-3-small:**
The 3072-dimension model outperforms the 1536-dimension model on retrieval benchmarks by 3–8% on MTEB benchmarks. For a system where retrieval quality directly determines PM trust and adoption, this improvement justifies the 3x cost difference.

### Secondary (cost-optimized): OpenAI text-embedding-3-small

Used for: tenants that have very large corpora (>5M documents) and elect the cost-optimized indexing option.

| Property | Value |
|----------|-------|
| Dimensions | 1536 |
| Cost | ~$0.02 per 1M tokens |
| Performance tradeoff | 3–8% lower recall vs. large model |

### Query Embedding

Query embedding always uses text-embedding-3-large regardless of tenant cost settings. The reasoning: retrieval latency is already dominated by the ANN search, not the embedding step. Using the best model for queries costs negligibly more and improves retrieval quality.

---

## Domain-Specific Embedding Considerations

General-purpose embedding models perform well on common language. Product-specific terminology is a known weakness.

**The problem:** A query for "SCIM provisioning" might not return documents that say "user lifecycle management" or "just-in-time provisioning" even though these are semantically equivalent in the product domain.

**Mitigations:**

1. **BM25 keyword search as complement:** Product-specific terminology is captured by BM25 even when semantic search misses it. The hybrid retrieval approach exists precisely to handle this.

2. **Query expansion (optional, configurable per PM team):** The Orchestration Layer can optionally run query expansion before retrieval — generating 2–3 semantically equivalent alternative queries and running parallel retrieval. The RRF fusion step merges the results. This improves recall for domain-specific queries at the cost of higher retrieval latency.

3. **Product term dictionary (planned for V2):** A PM-configured product terminology dictionary maps internal product terms to their plain-language equivalents. This dictionary is injected into the query formulation step to improve semantic overlap.

---

## Embedding Pipeline

```
Input: Text chunk (from signal processing pipeline)
  │
  ▼
Pre-processing:
  ├── Trim to embedding model max tokens (8,191)
  ├── If chunk > max tokens: truncate with tail priority
  │   (keep the end of the text, where key information
  │    tends to appear in support tickets and call transcripts)
  └── Inject metadata prefix: "[source_type: Zendesk] [product_area: authentication]"
      (adding source type to the embedded text improves retrieval precision
       for source-type-filtered queries)
  │
  ▼
Embedding API call (batched, 256 chunks per call)
  │
  ▼
Post-processing:
  ├── Normalize vector (L2 normalization for cosine similarity)
  └── Store: vector + chunk_id + metadata → vector namespace
```

---

## Batch Processing and Rate Limits

**Embedding API rate limits (OpenAI):**
- 1M tokens per minute (TPM limit)
- 3,000 requests per minute (RPM limit)
- Batch processing recommended: 256 chunks per call

**Historical load throttling:**
Background (historical) embedding jobs are rate-limited to 50% of the available token budget to ensure real-time signal ingestion is never delayed by a bulk load.

**Priority queue:**
Real-time signals (new webhooks) are always processed before background historical loads. The embedding pipeline uses a priority queue with two lanes: `realtime` (priority) and `background` (bulk loads).

---

## Embedding Quality Monitoring

| Metric | Target | Measurement |
|--------|--------|-------------|
| Embedding success rate | >99.9% | Failed embeddings / total attempted |
| Embedding latency (real-time lane, p95) | <10 seconds from chunk creation | Timing in pipeline |
| Embedding coverage | 100% of indexed chunks have embeddings | Chunk vs. embedding count in monitoring |
| Semantic drift detection | Zero-shot retrieval quality maintained over model updates | Monthly eval against golden query set |
