# AI Strategy

## Strategic Position

ProductPilot OS is an AI-native product. AI is not a feature layered on top of a workflow tool — it is the core infrastructure that makes the product work. The AI strategy defines how intelligence is structured, which problems AI solves vs. which problems require human judgment, how the system improves over time, and how trust is earned with PM users.

---

## Core AI Bets

### Bet 1: Retrieval-Augmented Generation as the Reliability Foundation
The fundamental reliability problem with LLMs is hallucination — generating plausible but unverifiable claims. For a product where PMs stake their credibility on the output, a single hallucination undermines trust catastrophically.

RAG solves this by grounding every claim in retrieved evidence from the organization's actual data. The LLM's role shifts from "generate from training data" to "synthesize and structure retrieved evidence." This architecture makes citation-first AI economically viable.

**The bet:** Citation-first AI via RAG is not just technically correct — it is the trust architecture that makes PM adoption possible. PMs will adopt AI assistants when they can verify every claim. They will reject AI assistants when they cannot.

### Bet 2: Specialized Agents over General-Purpose Assistants
A single general-purpose AI assistant ("[chat with your data]") is not the right architecture for PM workflows. PM work is not one task — it is a set of structurally distinct tasks (synthesis, prioritization, documentation, risk monitoring, communication) each requiring a different reasoning pattern, different tools, and different output format.

**The bet:** Specialized agents with explicit input contracts, defined tools, and constrained output formats produce better results than a general assistant, are easier to evaluate, and are faster to improve.

### Bet 3: Human-in-the-Loop as a Feature, not a Limitation
Most AI products treat human approval requirements as a weakness to be engineered around. ProductPilot OS treats human approval as a first-class design principle — a feature that enables PM adoption by ensuring agents never do anything the PM didn't sanction.

**The bet:** PMs who trust the system adopt it deeply. PMs who feel AI is acting autonomously on their behalf resist it. HITL is the mechanism for trust, and trust is the mechanism for adoption.

### Bet 4: The Knowledge Graph as the Compounding Moat
Every agent interaction — every brief generated, every decision recorded, every outcome tracked — enriches the Product Knowledge Graph. Over time, this graph becomes the organization's institutional memory for product decisions: why we built what we built, what evidence supported each decision, what actually happened.

**The bet:** The knowledge graph is a moat because it compounds. After 6 months, ProductPilot knows your products, customers, and decisions better than any new tool could. The switching cost grows every day.

### Bet 5: PM Acceptance Rate as the North Star for AI Quality
Most AI products optimize for usage (sessions, queries). ProductPilot OS optimizes for quality: what percentage of agent outputs does the PM approve without major modification? This metric, called PM Acceptance Rate, directly measures whether the AI is producing work the PM would have produced themselves — just faster.

**The bet:** A system with 80% PM acceptance rate at low volume is more valuable than a system with 60% acceptance rate at high volume. Quality drives trust; trust drives deep adoption; deep adoption drives word-of-mouth and expansion.

---

## Model Selection Strategy

### LLM Layer

| Use Case | Requirements | Model Selection Approach |
|----------|-------------|--------------------------|
| Brief generation | Long context, instruction-following, structured output | Frontier model (e.g., Claude 3.5 Sonnet or GPT-4o) |
| PRD generation | Long output, template compliance, citation injection | Frontier model with long context window |
| Risk brief generation | Speed + structure | Fast frontier model with system prompt optimization |
| Executive summary | Concise synthesis | Frontier model with strong summarization |
| Classification | Speed, cost efficiency | Smaller specialized model or frontier model with few-shot examples |

**Principle:** Use frontier models for output quality where PMs will evaluate the result. Use smaller/faster models for background classification and metadata extraction where quality is measurable by precision/recall metrics.

### Embedding Layer
- **Primary:** OpenAI text-embedding-3-large (3072 dimensions) for dense semantic search
- **Backup/fallback:** OpenAI text-embedding-3-small (1536 dimensions) for cost-constrained scenarios
- **BM25 keyword index:** For exact-match retrieval of product-specific terms

### Re-ranking Layer
- **Cross-encoder re-ranker:** Applied after initial retrieval to re-score top-k candidates before LLM synthesis
- **Purpose:** Improves precision of evidence surfaced to LLM; reduces noise in generated outputs

---

## AI Trust Architecture

The system earns PM trust through four mechanisms:

1. **Citation-first outputs:** Every factual claim is linked to a specific source. PMs can verify any claim by clicking the citation.

2. **Confidence transparency:** Every output includes a confidence score with component breakdown. PMs understand not just "how confident" but "why this level of confidence."

3. **Explicit hallucination prevention:** The system actively validates that numeric claims come from retrieved evidence, not LLM training data. Claims that cannot be verified are flagged or removed.

4. **HITL approval gates:** The PM is always in control. The system generates; the PM decides.

---

## AI Improvement Loop

The system improves continuously through a structured feedback flywheel:

```
PM approves brief → Edit distance calculated → Acceptance rate updated
PM rejects brief → Rejection reason captured → Eval dataset updated
PM overrides RICE dimension → Override value + reason logged → Calibration improved
PM edits PRD section → Edit delta calculated → Section quality score updated
PM accepts edge case → Edge case added to knowledge graph → Future PRDs improve
```

This feedback loop makes the system measurably better over time without requiring new feature development. The same agents generate better outputs because the evaluation and calibration infrastructure is in place from Day 1.

---

## AI Risk Management

| Risk | Mitigation |
|------|-----------|
| Hallucination in agent outputs | Citation validation; numeric claim source checking; confidence score components |
| Overreliance on AI outputs | HITL gates; confidence transparency; PM edit capability |
| Model performance degradation | Continuous eval against golden dataset; regression detection |
| Data leakage between tenants | Tenant-isolated vector namespaces; query-time tenant ID enforcement |
| Bias in prioritization scoring | Override logging; calibration review; PM final decision authority |
| Model API unavailability | Graceful degradation; queue-based retry; PM notification |
