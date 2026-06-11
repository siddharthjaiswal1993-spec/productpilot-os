# RAG Architecture Overview

## What RAG Does in ProductPilot OS

Retrieval-Augmented Generation (RAG) is the mechanism that makes ProductPilot OS citation-first. Instead of asking an LLM to generate insights from its training data, RAG retrieves specific, relevant evidence from the organization's actual data — then asks the LLM to synthesize and structure that retrieved evidence.

This architectural choice is not merely technical. It is what makes PM adoption possible. PMs will adopt AI assistants they can trust. They will trust AI assistants whose claims they can verify. RAG makes every claim verifiable by construction.

---

## RAG Architecture: Three-Layer Design

```
Layer 1: Offline Processing (runs continuously)
  ├── Signal ingestion from integrations
  ├── Chunking (per document type)
  ├── Embedding generation (OpenAI text-embedding-3-large)
  ├── BM25 index construction (keyword-based)
  └── Knowledge graph entity extraction and relationship building

Layer 2: Online Retrieval (runs per query)
  ├── Query formulation (per agent, per workflow step)
  ├── Dense retrieval (vector similarity search)
  ├── Sparse retrieval (BM25 keyword search)
  ├── Score fusion (Reciprocal Rank Fusion)
  └── Cross-encoder re-ranking (top-k candidates → final selection)

Layer 3: Generation (runs per agent invocation)
  ├── Evidence injection (structured format with chunk IDs)
  ├── LLM synthesis (guided by system prompt with citation requirements)
  ├── Citation binding (every claim → evidence chunk ID)
  └── Post-generation validation (hallucination check, PII scan, schema validate)
```

---

## Why Hybrid RAG (Dense + Sparse)

Dense semantic search (vector similarity) excels at finding conceptually related content even when exact terms don't match. A query for "authentication problems" will retrieve a Slack message saying "users can't log in to their accounts" — even though the words "authentication" and "problems" don't appear.

BM25 keyword search excels at exact-match retrieval for product-specific terms (feature names, API endpoints, error codes, customer names). "Enterprise SSO" is better matched by BM25 than by dense search, which might return conceptually related but irrelevant documents about "login" or "security."

Hybrid RAG combines both: dense search finds conceptually relevant evidence; BM25 ensures that product-specific terminology and exact customer names are always captured.

**Score fusion:** Reciprocal Rank Fusion (RRF) combines the dense and sparse rankings into a single ranked list by averaging reciprocal ranks. This is simple, effective, and avoids needing to tune a weighting parameter between the two retrievers.

---

## Retrieval Quality Design

The goal of the retrieval layer is precision and recall over a domain corpus, not general-purpose web search. Design decisions that reflect this:

**Tenant-scoped corpus:** Each tenant's signals are stored in a separate vector namespace. Retrieval is scoped to the tenant's corpus at the infrastructure layer — not just filtered at the application layer.

**Product area filtering:** Retrieval queries can be filtered by product area, signal source type, date range, and signal type. These metadata filters run at the vector database layer (pre-retrieval), not as post-retrieval filtering. This improves latency and prevents irrelevant evidence from consuming the re-ranker's capacity.

**Top-k tuning:** Default top-k for initial retrieval is 20 (dense) + 10 (BM25). After re-ranking, the top 5–8 evidence chunks are selected for LLM injection. This is tuned based on LLM context window economics: enough evidence for comprehensive coverage without exceeding the context window budget.

---

## RAG Quality Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| Retrieval recall (relevant chunks retrieved in top-20) | >85% | Human evaluation on golden query set |
| Re-ranking precision (relevant chunks in top-8 selection) | >75% | Human evaluation on golden query set |
| Evidence citation rate in generated output | >95% | Automated claim-citation validation |
| Retrieval latency (p95) | <10 seconds | Production monitoring |
| BM25 contribution rate (% of final selections that came from BM25) | 15–30% | Production monitoring |

---

## RAG as a Trust Architecture

The key insight about RAG in ProductPilot OS is not technical — it's behavioral.

When a PM opens an opportunity brief and sees:
> "12 enterprise accounts have requested SSO/SCIM in the past 60 days [Zendesk-12847] [Gong-call-8847] [Salesforce-note-441]..."

...they can click on each citation and read the original source. They are not trusting the AI's memory. They are trusting their own organization's data.

This behavioral shift — from "trust the AI" to "verify the source" — is what converts skeptical PMs into regular users. The RAG architecture makes this possible by design.
