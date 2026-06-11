# Insight Synthesizer Agent

## Purpose

The Insight Synthesizer Agent is the primary intelligence engine of ProductPilot OS. It aggregates signals from all connected data sources, identifies emerging patterns and themes, clusters signals by topic and urgency, assesses business impact, and generates structured opportunity briefs. It is the first agent in most PM intelligence workflows and the primary driver of evidence-backed opportunity creation.

---

## Inputs

| Input | Source | Format | Required |
|-------|--------|--------|---------|
| Signal cluster | Signal Intelligence Hub | List of signal IDs with metadata | Yes |
| Retrieved evidence | RAG retrieval pipeline | Top 5–8 ranked evidence chunks with source metadata | Yes |
| Knowledge graph context | Product Knowledge Graph | Related entities (features, customers, prior decisions) | Yes |
| CRM data | Salesforce / HubSpot integration | Account ARR, tier, renewal date, open opportunities | If available |
| Current OKRs | PM configuration | OKR list with weight | Yes |
| PM preferences | PM profile | Brief format preferences, product area | Optional |

---

## Outputs

| Output | Format | Recipient |
|--------|--------|----------|
| Opportunity Brief | Structured JSON → rendered UI | PM inbox |
| Evidence table | Table with source, author, date, excerpt, strength | Included in brief |
| Business impact estimate | Dollar range with confidence | Included in brief |
| Confidence score | 0–100 composite with components | Included in brief |
| Preliminary RICE score | RICE breakdown with evidence citations | Included in brief |
| Knowledge graph updates | Entity and relationship insertions | Knowledge Graph |

---

## Tools Used

- **Vector search:** Retrieve top 5–8 semantically relevant evidence chunks from the signal corpus
- **BM25 keyword search:** Supplement semantic search with exact-match keyword retrieval for product-specific terms
- **CRM lookup:** Query account ARR and pipeline data for identified customer accounts
- **Knowledge graph query:** Retrieve related features, prior decisions, and outcome history
- **Confidence calculator:** Compute composite confidence score from evidence quality components

---

## Memory

- **Working memory:** Current brief context (signal cluster, retrieved evidence, synthesis in progress) — cleared after brief generation
- **Episodic memory:** History of briefs generated in current session, including PM feedback
- **Semantic memory (Knowledge Graph):** Prior decisions, customer entities, feature entities, outcome records — persistent across sessions

---

## Reasoning Pattern

**Pattern: ReAct (Reason + Act)**

```
Step 1 [REASON]: Analyze the signal cluster — what is the common theme? What are the strongest signals?
Step 2 [ACT]: Retrieve top evidence from vector index (Retrieve: "bulk export performance issues")
Step 3 [REASON]: Evaluate retrieved evidence — does it corroborate the pattern? What's missing?
Step 4 [ACT]: Query CRM for ARR on identified accounts
Step 5 [REASON]: Assess business impact — what is the revenue exposure?
Step 6 [ACT]: Query knowledge graph for related prior decisions and outcomes
Step 7 [REASON]: Synthesize — what is the opportunity, what evidence supports it, how confident am I?
Step 8 [ACT]: Generate structured brief output with citations
Step 9 [REASON]: Self-critique — are all claims citable? Is the confidence score calibrated?
Step 10 [OUTPUT]: Deliver brief to PM inbox
```

---

## Autonomy Level

**Read-only for all data access.** Write access limited to:
- Adding new entities and relationships to the knowledge graph (low-risk)
- Creating a draft brief in the PM's inbox (clearly labeled "Draft — Pending PM Review")

**No autonomy over:**
- Jira priority changes
- Customer communications
- Roadmap decisions
- PRD publishing

---

## Guardrails

1. **Citation required:** Every factual claim in the output must cite a specific source. If a claim cannot be cited, it must be omitted or labeled "[No source — PM review required]."

2. **Confidence threshold:** If confidence score is below 30/100, the brief is generated with a prominent red warning. Below 15/100, generation is blocked with a message: "Insufficient evidence — minimum 2 signals required."

3. **PII in output:** Customer names, emails, and contact information are never included in generated output without passing through the PII access control layer.

4. **Hallucination check:** All numeric claims (ARR values, user counts, percentage changes) must be directly sourced from retrieved evidence or CRM data — never inferred by the LLM from training data.

5. **Max evidence age:** Evidence older than 180 days is marked as "historical" and weighted lower in the confidence score. Evidence older than 365 days is excluded from primary evidence but noted as "historical context."

---

## Failure Modes

| Failure | Detection | Response |
|---------|-----------|---------|
| Retrieval returns low-relevance chunks | Relevance score threshold check | Retry with expanded query; if still low-quality, generate brief with thin evidence warning |
| CRM lookup fails (API unavailable) | HTTP error detection | Generate brief without CRM data; lower business impact confidence; note "CRM data unavailable" |
| Knowledge graph query timeout | 5-second timeout | Proceed without knowledge graph context; note "Historical context unavailable" |
| LLM generation timeout (>60 seconds) | Agent orchestration timeout | Return partial brief with completion status; PM can retry failed sections |
| Citation injection fails (source not found at referenced ID) | Citation validation post-generation | Remove uncitable claim; log for human review; PM sees "[Claim removed — source validation failed]" |

---

## Human Approval Boundary

**The Insight Synthesizer Agent generates drafts. It does not decide.**

- All opportunity briefs are delivered to the PM inbox as drafts
- No downstream action (PRD generation, stakeholder notification, Jira update, prioritization scoring) occurs without explicit PM approval of the brief
- PM edits to the brief are captured and used as model improvement signal
- PM rejection of the brief triggers a reason-capture flow that feeds back into evaluation

---

## Quality Metrics

| Metric | Target |
|--------|--------|
| PM acceptance rate (brief approved without major edit) | >75% at 30-day cohort |
| Evidence citation rate (claims with citations) | >95% |
| Hallucination rate (unverifiable claims) | <2% |
| Brief generation latency (p95) | <90 seconds |
| Confidence calibration error | <10% |
