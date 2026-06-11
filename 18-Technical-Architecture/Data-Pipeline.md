# Data Pipeline

## Pipeline Overview

The data pipeline transforms raw signals from external sources into indexed, searchable evidence in the ProductPilot OS intelligence layer. It runs continuously and asynchronously, processing signals from multiple concurrent sources at varying volumes without affecting real-time PM interactions.

---

## Pipeline Architecture

```
External Source Event
        │
        ▼
┌───────────────────────────────┐
│  Integration Gateway           │
│  • Webhook receiver             │
│  • Payload validation           │
│  • Schema normalization         │
│  • Tenant routing               │
└───────────────┬───────────────┘
                │
                ▼
        SQS: signal-ingestion-queue
        (per-tenant queue for isolation)
                │
                ▼
┌───────────────────────────────┐
│  Signal Processing Service     │
│                                 │
│  Step 1: Content extraction    │
│  • Extract text from payload   │
│  • Apply source-specific parser│
│                                 │
│  Step 2: Classification         │
│  • ML classifier + rules        │
│  • signal_type, confidence      │
│                                 │
│  Step 3: Entity extraction      │
│  • NER for customer names       │
│  • Product area detection       │
│  • Feature name matching        │
│                                 │
│  Step 4: PII detection          │
│  • Pattern matching             │
│  • ML-based NER for PII         │
│  • Redact in indexed content    │
│  • Store original in PII vault  │
│                                 │
│  Step 5: Deduplication          │
│  • Content hash lookup          │
│  • Near-duplicate check (>0.98) │
│  • Skip if exact duplicate      │
│                                 │
│  Step 6: Chunking               │
│  • Source-specific strategy     │
│  • Generate chunk metadata      │
│  • Quality validation           │
└───────────────┬───────────────┘
                │
                ▼
        SQS: embedding-queue
                │
                ▼
┌───────────────────────────────┐
│  Embedding Service             │
│  • Batch embedding (256 chunks)│
│  • OpenAI API with retry       │
│  • Dimension: 3072             │
│  • L2 normalization            │
└───────────────┬───────────────┘
                │
                ├──────────────────────────────────┐
                │                                  │
                ▼                                  ▼
┌───────────────────────────┐  ┌─────────────────────────────────┐
│  Vector Index Write        │  │  Knowledge Graph Write           │
│  (Pinecone)                │  │  (Neo4j)                         │
│  • Write to tenant         │  │  • Upsert Signal entity          │
│    namespace               │  │  • Link to Customer entity        │
│  • Include metadata        │  │  • Link to Feature entity         │
│    for filtering           │  │  • Create ASSOCIATED_WITH         │
│                            │  │    relationship                   │
└───────────────────────────┘  └─────────────────────────────────┘
                │                                  │
                └──────────────┬───────────────────┘
                               │
                               ▼
                   Relational DB: signal metadata record
                   (signal_id, status=indexed, chunk_count,
                    entities, classification, ingestion_timestamp)
                               │
                               ▼
                   Signal available in Signal Intelligence Hub
```

---

## Pipeline Performance Characteristics

**Real-time lane (webhook-triggered signals):**
- Priority: High
- Processing time (p95): <5 minutes from webhook to indexed
- Parallelism: Up to 20 concurrent signals per tenant

**Background lane (historical loads, low-priority bulk):**
- Priority: Low (throttled to not starve real-time lane)
- Processing time: Best-effort; 24–48 hours for large loads
- Rate limit: 50% of available API quota (leaves buffer for real-time)

---

## Pipeline Monitoring

| Metric | Alert Threshold |
|--------|----------------|
| Real-time queue depth | >50 messages → scale up processing |
| Background queue depth | >10,000 messages → notify admin (historical load in progress) |
| Classification error rate | >5% errors in 1 hour |
| PII detection false positive rate | >10% (reviewed monthly via human eval) |
| Embedding API error rate | >1% → alert engineering; activate fallback model |
| Vector write failure rate | >0.1% → alert engineering immediately |
| Knowledge graph write lag | >60 seconds behind embedding pipeline |

---

## Pipeline Idempotency

Every processing step is idempotent. If a step fails and retries, it produces the same result without creating duplicates.

**Key mechanisms:**
- Signal deduplication by content hash (Step 5 of processing)
- Chunk IDs are deterministic: SHA-256 hash of (signal_id + chunk_index)
- Vector writes are upsert-based (overwrite if chunk_id already exists)
- Knowledge graph writes use MERGE (create if not exists; update if exists)

---

## BM25 Index Pipeline

In parallel with the embedding pipeline, a BM25 (Elasticsearch) index is maintained for keyword search:

```
Chunked text
     │
     ▼
Tokenization + stopword removal + stemming
     │
     ▼
Elasticsearch upsert (per-tenant index alias)
     │
     ▼
BM25 search available for keyword-based retrieval
```

The BM25 index is updated in the same pipeline run as the vector index, ensuring keyword and semantic search are always consistent.
