# Vector Database

## Technology Selection

**Selected:** Pinecone (managed) or Weaviate (self-hosted option for enterprise)

### Why Pinecone (default / hosted)
- Fully managed: no infrastructure to run for early-stage product
- Namespacing support: tenant isolation via namespaces without infrastructure per tenant
- ANN search performance: sub-100ms at high k values
- Metadata filtering at query time: critical for pre-filtering by product_area, date range, source_type
- Horizontal scale without operator overhead

### Why Weaviate (enterprise option)
- Self-hosted deployment option for customers with data residency requirements
- Hybrid search (dense + sparse) built in
- GraphQL query interface for complex filtered queries
- Strong GDPR/compliance story for EU enterprise customers

---

## Namespace Strategy

Tenant isolation is enforced at the namespace level in the vector database:

```
Namespace: tenant_{tenant_id}

Example:
tenant_acme_corp_prod
tenant_globaltech_prod
tenant_novartis_prod

Each namespace contains ONLY that tenant's signal chunks.
Cross-namespace queries are not possible via the application layer.
```

**Why namespace isolation (not metadata filtering for isolation):**
Metadata filtering can be bypassed by application bugs or misconfiguration. Namespace isolation is enforced at the database infrastructure layer — a query in namespace A will never return results from namespace B regardless of application-layer filter state.

---

## Index Configuration

**Dimensions:** 3072 (OpenAI text-embedding-3-large)  
**Distance metric:** Cosine similarity  
**Index type:** HNSW (Hierarchical Navigable Small World)  
**HNSW parameters:**
- `ef_construction`: 128 (quality of index construction; higher = better recall, slower build)
- `M`: 16 (max connections per node; higher = better recall at query time, more memory)
- `ef` (query): 64 (quality of query-time search; higher = better recall, higher latency)

**Target performance (p95):**
- Query latency: <100ms at 1M vectors per namespace
- Throughput: >100 queries/second per namespace

---

## Embedding Model Selection

| Aspect | Selection | Rationale |
|--------|-----------|-----------|
| Model | OpenAI text-embedding-3-large | Best performance on MTEB benchmark for text similarity and retrieval |
| Dimensions | 3072 | Maximum dimensions for this model; highest retrieval quality |
| Fallback | text-embedding-3-small (1536 dim) | Cost-optimized option for tenants with very large corpora |
| Batch size | 256 chunks per embedding API call | Throughput optimization for historical data loads |
| Rate limit handling | Exponential backoff; queue-based processing | Historical loads do not compete with real-time queries |

---

## Metadata Storage and Filtering

The vector database stores the following metadata fields per chunk for filtering:

| Field | Type | Filter Operations | Use Case |
|-------|------|------------------|----------|
| tenant_id | string | equality | Tenant isolation (always applied) |
| source_type | string | equality, in | Filter by source (e.g., "show only Gong evidence") |
| date | timestamp | range, gte, lte | Recency filtering |
| product_area | string | equality, in | Product area scoping |
| signal_type | string | equality, in | Signal type filtering for Risk Watchdog |
| author | string | equality | Author-specific queries |
| account_name | string | equality, in | Customer-specific queries |

**Important:** Metadata filters are applied at query time, before embedding similarity scoring. This pre-filtering reduces the search space and improves both latency and precision.

---

## Storage Capacity Planning

**Estimates per tenant (Team tier, 25 PMs, 12 integrations):**
- Average signals per week: ~2,000
- Average chunks per signal: 2.5
- Average chunks per week: 5,000
- Weeks of active use until 1M chunks: ~200 weeks (nearly 4 years)

**Practical limit:** The system supports up to 10M chunks per tenant namespace (NFR-034). Most teams will take 5+ years to reach this limit under normal usage patterns.

**Enterprise tier:** Custom namespace sizing for very large tenants (e.g., 10+ years of historical data loaded at onboarding).

---

## Operational Monitoring

| Metric | Alert Threshold | Action |
|--------|----------------|--------|
| Query latency p95 | >200ms | Investigate HNSW ef parameter or shard scaling |
| Index build lag | >60 minutes behind ingestion queue | Alert engineering; scale embedding compute |
| Namespace size | >8M chunks | Notify account team; discuss archival strategy |
| API error rate | >1% over 5 minutes | Alert engineering; check Pinecone status page |
| Embedding API error rate | >0.5% over 5 minutes | Alert engineering; activate fallback embedding model |
