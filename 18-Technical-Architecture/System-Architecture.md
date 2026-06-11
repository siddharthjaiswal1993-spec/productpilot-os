# System Architecture

## Architecture Overview

ProductPilot OS is a multi-tenant, cloud-native SaaS application built on a microservices architecture. The system is designed to handle continuous signal ingestion, asynchronous agent execution, real-time PM interactions, and background intelligence generation — all at tenant-isolated scale.

---

## High-Level Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────┐
│                          External Layer                                  │
│  Slack │ Jira │ Zendesk │ Gong │ Salesforce │ HubSpot │ Notion │ Others  │
└──────────────────────────────┬──────────────────────────────────────────┘
                               │ Webhooks / REST APIs
┌──────────────────────────────▼──────────────────────────────────────────┐
│                       Integration Gateway                                │
│  • Webhook receiver (per source type)                                    │
│  • OAuth 2.0 token management                                            │
│  • Payload validation + schema normalization                             │
│  • Polling scheduler (fallback for non-webhook sources)                  │
│  • Rate limit management + exponential backoff                           │
└──────────────────────────────┬──────────────────────────────────────────┘
                               │
┌──────────────────────────────▼──────────────────────────────────────────┐
│                       Signal Processing Pipeline                         │
│  • Classification service (ML model + rule-based)                       │
│  • Entity extraction service (NER + product ontology)                   │
│  • PII detection + redaction service                                     │
│  • Deduplication service (content hash + near-duplicate detection)       │
│  • Chunking service (per-source-type chunking strategy)                  │
│  • Embedding service (OpenAI API + batch queue)                          │
└─────────────────────────────────┬──────────────────────────────────────┘
                                  │
                    ┌─────────────┴──────────────┐
                    │                            │
┌───────────────────▼───────┐    ┌───────────────▼───────────────┐
│   Vector Index             │    │   Relational Database          │
│   (Pinecone)               │    │   (PostgreSQL + RDS)           │
│   • Tenant namespaces      │    │   • Signal metadata            │
│   • 3072-dim embeddings    │    │   • Brief/decision records     │
│   • Metadata filtering     │    │   • PM user/session data       │
│   • ANN search             │    │   • Audit log                  │
└───────────────────────────┘    └───────────────────────────────┘
                    │                            │
┌───────────────────▼────────────────────────────▼────────────────────────┐
│                         Knowledge Graph                                   │
│                         (Neo4j or equivalent)                             │
│   • Entity model: Feature, Customer, Signal, Decision, Outcome, PM       │
│   • Relationship model: 15+ relationship types                            │
│   • Tenant subgraph isolation                                             │
└────────────────────────────────────┬───────────────────────────────────┘
                                     │
┌────────────────────────────────────▼───────────────────────────────────┐
│                           Agent Orchestration Layer                      │
│   • Agent router (workflow → agent assignment)                           │
│   • State manager (workflow checkpoint persistence)                      │
│   • HITL gate enforcement                                                 │
│   • Context injector (relevant history + knowledge graph context)        │
│   • Output validator (schema validation + citation validation)           │
│   • Audit logger (immutable write-once log)                              │
└──────────────────────────────────┬─────────────────────────────────────┘
                                   │
┌──────────────────────────────────▼─────────────────────────────────────┐
│                            Agent Pool                                    │
│  ┌──────────────────────┐  ┌───────────────┐  ┌─────────────────────┐  │
│  │ Insight Synthesizer  │  │ Prioritization│  │  PRD Agent          │  │
│  └──────────────────────┘  └───────────────┘  └─────────────────────┘  │
│  ┌──────────────────────┐  ┌───────────────┐  ┌─────────────────────┐  │
│  │ Risk Watchdog        │  │ Stakeholder   │  │  Meeting Intel      │  │
│  └──────────────────────┘  └───────────────┘  └─────────────────────┘  │
└──────────────────────────────────┬─────────────────────────────────────┘
                                   │
┌──────────────────────────────────▼─────────────────────────────────────┐
│                            RAG Pipeline                                   │
│   • Query formulation                                                     │
│   • Dense retrieval (vector similarity)                                   │
│   • Sparse retrieval (BM25)                                               │
│   • Reciprocal Rank Fusion                                                │
│   • Cross-encoder re-ranking                                              │
│   • Source diversity enforcement                                          │
└──────────────────────────────────┬─────────────────────────────────────┘
                                   │
┌──────────────────────────────────▼─────────────────────────────────────┐
│                        LLM API Layer                                      │
│   • Primary: Anthropic Claude (via API)                                   │
│   • Secondary: OpenAI GPT-4o (fallback)                                   │
│   • Embedding: OpenAI text-embedding-3-large                              │
│   • Rate limit management + queue                                          │
│   • Timeout handling + retry logic                                         │
└──────────────────────────────────┬─────────────────────────────────────┘
                                   │
┌──────────────────────────────────▼─────────────────────────────────────┐
│                            API Layer                                       │
│   • REST API (FastAPI / Node.js)                                           │
│   • WebSocket (real-time PM notifications)                                 │
│   • Auth: JWT + OAuth 2.0                                                  │
│   • Rate limiting + tenant scoping                                         │
└──────────────────────────────────┬─────────────────────────────────────┘
                                   │
┌──────────────────────────────────▼─────────────────────────────────────┐
│                          Frontend                                          │
│   • React 18 + TypeScript                                                  │
│   • Tailwind CSS                                                           │
│   • Real-time updates via WebSocket                                        │
│   • PWA (progressive web app) for mobile read-only                         │
└────────────────────────────────────────────────────────────────────────┘
```

---

## Key Technology Choices

| Component | Technology | Rationale |
|-----------|-----------|-----------|
| Backend API | Python (FastAPI) + Node.js | FastAPI for AI/ML heavy services; Node.js for real-time event handling |
| Database | PostgreSQL (AWS RDS) | Battle-tested, ACID transactions, strong consistency for decision records |
| Vector DB | Pinecone (managed) | Production-grade, namespace isolation, sub-100ms at scale |
| Knowledge Graph | Neo4j (AWS-managed or self-hosted) | Native graph queries; APOC for advanced relationship traversal |
| Search (BM25) | Elasticsearch or OpenSearch | Mature BM25 implementation; integrates with metadata filtering |
| Cache | Redis (ElastiCache) | Session cache, rate limit counters, webhook dedup state |
| Queue | AWS SQS + Lambda | Async signal processing; fan-out for embedding pipeline |
| Object Storage | AWS S3 | Raw signal archive; audit log archive; PII vault |
| Secrets | AWS Secrets Manager | Integration tokens, API keys |
| LLM | Anthropic Claude (primary) | Best-in-class instruction following; safety-focused |
| Embedding | OpenAI text-embedding-3-large | Best MTEB performance for retrieval use cases |

---

## Infrastructure: AWS (Primary Region)

- **Primary region:** us-east-1
- **DR region:** us-west-2 (cross-region replication for database and vector index snapshots)
- **Deployment:** ECS Fargate (containerized services, auto-scaling)
- **CI/CD:** GitHub Actions → ECR → ECS
- **Monitoring:** CloudWatch + Datadog
- **Alerting:** PagerDuty for P0/P1 incidents

---

## Performance Targets (V1)

| Operation | p50 | p95 | p99 |
|-----------|-----|-----|-----|
| Signal ingestion to indexed | <30s | <5min | <15min |
| Brief generation | <45s | <90s | <3min |
| Prioritization brief | <25s | <60s | <2min |
| PRD generation | <90s | <3min | <5min |
| Risk Watchdog run | <30s | <90s | <3min |
| Knowledge graph query | <200ms | <500ms | <1s |
| Vector search (retrieval) | <200ms | <500ms | <1s |

---

## Scalability Design

**Horizontal scaling:** All services are stateless (state in database/cache). ECS Fargate auto-scaling based on CPU and queue depth.

**Multi-tenant scaling:** Each tenant's signal processing load is isolated via separate SQS queues. High-volume tenants don't affect latency for other tenants.

**LLM API throttling:** Separate token bucket per tenant per agent type. Background jobs (Risk Watchdog, Executive Summary) use the background budget; PM-triggered agents use the interactive budget.

**Capacity planning:** V1 designed for 500 concurrent PM sessions, 10M chunks per tenant namespace, 1,000 signals/minute sustained throughput across all tenants.
