# Non-Functional Requirements: ProductPilot OS V1

**Format:** NFR-[ID] | Category | Requirement | Target | Measurement

---

## Performance

| ID | Requirement | Target | Measurement |
|----|------------|--------|-------------|
| NFR-001 | Signal ingestion latency (Slack, Jira, Zendesk via webhook) | p50: <2 min, p95: <15 min | Infrastructure monitoring; webhook to indexed timestamp |
| NFR-002 | Signal classification latency (from ingestion to classified) | p50: <30 sec, p95: <2 min | Classification pipeline monitoring |
| NFR-003 | Opportunity brief generation latency | p50: <30 sec, p95: <90 sec, p99: <3 min | Measured over 1,000 real brief generations |
| NFR-004 | PRD first draft generation latency | p50: <90 sec, p95: <3 min, p99: <5 min | Measured over 200 real PRD generations |
| NFR-005 | Risk brief generation latency | p50: <45 sec, p95: <90 sec | Measured over 100 real risk alert triggers |
| NFR-006 | Stakeholder communication draft generation | p50: <20 sec per draft, p95: <60 sec | All audiences generated in parallel |
| NFR-007 | Knowledge graph query latency (natural language) | p50: <5 sec, p95: <15 sec | Measured over 500 queries |
| NFR-008 | UI page load time (dashboard) | p95: <2 sec on standard broadband | Browser performance monitoring |
| NFR-009 | RAG retrieval latency | p95: <500ms | Vector database query monitoring |

---

## Reliability

| ID | Requirement | Target | Measurement |
|----|------------|--------|-------------|
| NFR-010 | Application uptime (design partner phase) | 99.5% monthly | Infrastructure monitoring; scheduled maintenance excluded |
| NFR-011 | Application uptime (commercial launch) | 99.9% monthly | Infrastructure monitoring |
| NFR-012 | Agent error rate (invocations that fail without output) | <1% of invocations | Agent orchestration error monitoring |
| NFR-013 | Integration connection uptime (per integration) | >99% per month | Integration health dashboard |
| NFR-014 | Data ingestion success rate | >99.5% of webhook events processed | Dead-letter queue monitoring |
| NFR-015 | Knowledge graph write consistency | 0 data loss on approved brief, PRD, or decision records | Integrity checks on write operations |
| NFR-016 | Recovery time objective (RTO) | <1 hour for any service component | Tested in quarterly disaster recovery drill |
| NFR-017 | Recovery point objective (RPO) | <15 minutes data loss in worst-case scenario | Backup frequency and restore testing |

---

## Security

| ID | Requirement | Target | Measurement |
|----|------------|--------|-------------|
| NFR-018 | Data encryption at rest | AES-256 for all customer data | Security audit; encryption configuration review |
| NFR-019 | Data encryption in transit | TLS 1.3 for all API communications | SSL Labs A+ rating; automated TLS config testing |
| NFR-020 | Authentication | OAuth 2.0 + MFA enforcement for all users | Auth configuration audit |
| NFR-021 | API authentication for integrations | OAuth 2.0 with scoped tokens; token rotation every 90 days | Integration token management audit |
| NFR-022 | Secrets management | All secrets in dedicated secrets manager (AWS Secrets Manager or equivalent); no secrets in environment variables or code | Secrets scanning in CI/CD pipeline |
| NFR-023 | Access control | Role-based access: Admin, PM, View-only | Access control matrix tested against all endpoints |
| NFR-024 | Input validation | All user inputs and integration payloads validated against schema | Security testing; fuzz testing on input endpoints |
| NFR-025 | Penetration testing | Annual penetration test by qualified third party | Pentest report; critical findings remediated within 30 days |
| NFR-026 | Dependency vulnerability scanning | No critical vulnerabilities in production dependencies | Automated dependency scanning in CI/CD |

---

## Privacy and Data Protection

| ID | Requirement | Target | Measurement |
|----|------------|--------|-------------|
| NFR-027 | Tenant data isolation | Zero cross-tenant data leakage | Penetration test + automated isolation testing |
| NFR-028 | PII detection before indexing | 100% of signals scanned for PII before indexing | PII scanner coverage audit; 0 PII in raw vector index |
| NFR-029 | PII handling in outputs | Customer PII (name, email, contact info) not exposed in AI outputs without explicit PM access | Automated output scanning; human eval sampling |
| NFR-030 | Data retention | Configurable per customer; default 2 years; signal data deletable on request | Retention configuration available; deletion tested |
| NFR-031 | Right to deletion | Customer data deletion request processed within 30 days; signals, entities, and graph nodes removed | Deletion workflow tested; confirmation audit trail |
| NFR-032 | GDPR compliance readiness | Data Processing Agreement (DPA) available; data residency option (EU) available | DPA template available; EU region deployment available |

---

## Scalability

| ID | Requirement | Target | Measurement |
|----|------------|--------|-------------|
| NFR-033 | Concurrent active PM sessions | Support 500 concurrent active sessions without performance degradation | Load test to 500 concurrent sessions |
| NFR-034 | Signal ingestion throughput | Support 10,000 signals/hour per tenant at peak | Load test; auto-scaling validation |
| NFR-035 | Vector index scale | Support up to 10 million document chunks per tenant | Vector DB capacity planning; index performance at scale test |
| NFR-036 | Knowledge graph scale | Support up to 100,000 entities per tenant without query degradation | Graph DB performance test at 100K entities |
| NFR-037 | Horizontal scaling | Application and agent infrastructure auto-scales with load | Auto-scaling policy tested with synthetic load |

---

## Compliance

| ID | Requirement | Target | Measurement |
|----|------------|--------|-------------|
| NFR-038 | Audit log retention | All audit log events retained for 2 years minimum | Log retention policy configured; restoration test |
| NFR-039 | Audit log immutability | Audit logs are append-only; no events can be deleted or modified | Immutability tested; tamper-evident log storage |
| NFR-040 | SOC 2 Type II readiness | Target certification within 12 months of commercial launch | Compliance gap assessment at Month 6 |
| NFR-041 | HIPAA readiness (if healthcare customers targeted) | BAA available; PHI handling controls in place | HIPAA compliance assessment before healthcare customer acquisition |

---

## AI Quality

| ID | Requirement | Target | Measurement |
|----|------------|--------|-------------|
| NFR-042 | Hallucination rate | <2% of generated claims unverifiable against cited sources | Weekly LLM-as-judge review of 50 sampled outputs |
| NFR-043 | Citation coverage rate | >95% of factual claims in generated outputs have citations | Automated citation audit on every output |
| NFR-044 | Confidence score calibration | Calibration error (ECE) <10% | Monthly calibration analysis comparing stated vs. actual accuracy |
| NFR-045 | PII in AI outputs | 0 instances of customer PII in generated outputs | Automated PII scanning on every AI output before display |
| NFR-046 | PM acceptance rate (30-day cohort) | >75% of outputs approved without major edit | Platform tracking of approval vs. rejection with edit distance |

---

*NFRs are non-negotiable constraints on system design. Any feature that cannot meet these constraints should be scoped accordingly rather than compromising the constraint.*
