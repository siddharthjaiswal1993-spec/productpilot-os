# What I Built — ProductPilot OS

---

## Repository Structure (19 Numbered Sections)

| Section | Contents |
|---|---|
| `01-Vision/` | Vision, mission, product thesis, why-now analysis, category narrative |
| `02-Market-Research/` | Competitive landscape, market trends, category creation thesis, comparable companies |
| `03-Problem-Space/` | Signal collapse, prioritisation crisis, context-switching tax, decision debt |
| `04-Product-Strategy/` | Full strategy, north star, principles, product bets, metrics, positioning, business model |
| `05-Personas/` | 10 detailed persona documents with JTBD, pain points, and workflow maps |
| `06-User-Journeys/` | 7 end-to-end journey maps with agent reasoning steps and before/after time comparisons |
| `07-Opportunity-Briefs/` | Templates and worked examples for opportunity, evidence, risk, and prioritisation briefs |
| `08-Product-Requirements/` | Full PRD, MVP scope, functional/non-functional requirements, acceptance criteria |
| `09-Agent-System/` | Detailed spec for all 8 agents plus orchestration and workflow coordination |
| `10-AI-Architecture/` | AI strategy, agentic architecture, reasoning/decision/confidence/trust layer design |
| `11-RAG-Architecture/` | Full RAG pipeline: data sources, chunking, embedding, retrieval, re-ranking, knowledge graph |
| `12-Evaluation-Framework/` | Eval stack, online/human/LLM-as-judge evals, golden dataset design, trust score model |
| `13-Risk-Governance/` | Responsible AI, PII handling, security model, compliance framework, audit log design |
| `14-Roadmap/` | V1 MVP through V3 agentic product organisation and long-term vision |
| `15-Prototype/` | UX principles, page structure, screen specs, Lovable and Bolt prototype prompts |
| `16-GTM/` | ICP definition, positioning, messaging, pricing, launch strategy, sales motion, competitive battlecards |
| `17-Investor-Narrative/` | Why-now, market opportunity, category creation, product moat, investor memo |
| `18-Technical-Architecture/` | System architecture, integrations (Slack, Jira, CRM, Support, Analytics), API design |
| `19-Future-Vision/` | Product OS vision, autonomous PM, multi-agent product team, enterprise intelligence |

---

## Standard Portfolio Documents

| File | Contents |
|---|---|
| `PORTFOLIO_AUDIT.md` | Honest evaluation of completeness, strengths, and what's missing |
| `PRODUCT_THESIS.md` | The core bet, problem framing, and strategic rationale |
| `WHAT_I_BUILT.md` | This file |
| `OUTCOME_MODEL.md` | Business outcomes, success metrics, and how value is measured |
| `AI_PRODUCT_JUDGMENT.md` | AI-specific product decisions and the reasoning behind them |

---

## Prototype

Built with Lovable (primary) and Bolt (secondary). Demonstrates the Signal Intelligence Hub, Opportunity Brief generation, and stakeholder update workflows.

---

## Key Design Decisions Encoded in the Docs

**Evidence-first output model** — every agent output must cite the specific source document, author, date, and excerpt that supports it. No citations = no output to users. This is a hard product requirement documented in Section 12.

**8 specialised agents, not one** — Sales, CS, Support, Meeting Intelligence, PRD, Risk, Roadmap Intelligence, and Executive Summary agents each have a defined signal input set, a defined output type, and a dedicated evaluation rubric.

**RAG over fine-tuning as the architecture bet** — the knowledge graph (Features, Customers, Signals, Decisions, Outcomes) compounds with every interaction. Fine-tuning a model on historical data is a one-time improvement; a growing knowledge graph is a compounding advantage.

**Trust score per agent** — the evaluation framework defines a composite trust score for each agent based on human acceptance rate, citation quality, and output freshness. Autonomy decisions are gated on trust score thresholds.
