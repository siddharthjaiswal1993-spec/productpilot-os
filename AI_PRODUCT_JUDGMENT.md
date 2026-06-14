# AI Product Judgment — ProductPilot OS

---

## Decision 1: Citation as a hard product requirement, not a quality goal

**What:** Every agent output must include citations to specific source documents with author, date, and excerpt. Outputs without citations are blocked before reaching users.

**Why:** Product managers make decisions that affect roadmaps, resource allocation, and customer commitments. A recommendation from an AI system that cannot be verified is not usable in a PM context — not because PMs distrust AI, but because PM decisions require defensible evidence. If a PM takes an opportunity brief to their CPO, they need to be able to answer "where did this come from?"

The hard requirement — not a quality metric but a gate — forces the AI architecture to be retrieval-first. The model cannot reason from training data and present it as a citation. It must retrieve and cite.

**What this reflects:** Product requirements should define observable behaviour, not aspirational quality levels. "Citations must be present" is observable. "Citations should be high quality" is not.

---

## Decision 2: Knowledge graph over static context

**What:** The evidence layer is a structured knowledge graph (Features, Customers, Signals, Decisions, Outcomes) that grows with every user interaction, not a static document repository with periodic refresh.

**Why:** A static repository degrades in relevance as the product evolves. Last quarter's feature priorities, customer feedback from last month, decisions made six months ago — these matter for current decisions, and they are often more relevant than the most recent Slack thread.

A knowledge graph that connects entities (a feature to the customer requests that motivated it, to the outcome it produced, to the decision it informs) enables reasoning that a document retrieval system cannot. The AI can answer "what happened last time we shipped something in this area?" because the graph encodes the history.

**What this reflects:** AI product architecture decisions should reflect the reasoning required for the product's use cases, not the architecture that is easiest to build. The knowledge graph is harder to build and maintain than a document retrieval system. It is worth it because the reasoning it enables is qualitatively different.

---

## Decision 3: 8 specialised agents over a general product intelligence agent

**What:** Eight agents with defined domains (Sales Intelligence, CS Intelligence, PRD Generation, Risk Detection, Stakeholder Updates, Meeting Intelligence, Roadmap Intelligence, Executive Summary) rather than one general product management agent.

**Why:** Product management spans multiple knowledge domains (customer evidence, competitive analysis, technical feasibility, business impact, stakeholder dynamics) that have different evidence sources, different output types, and different quality criteria. A general agent produces adequately-good outputs across all domains; specialised agents produce excellent outputs in their domain.

More practically: evaluation is domain-specific. A PRD Agent can be evaluated on acceptance criteria completeness, citation coverage, and engineering utility. A Risk Watchdog can be evaluated on precision and recall against a golden dataset of historical risks. A general agent cannot be evaluated meaningfully on all of these simultaneously.

**What this reflects:** Agent scope is a product decision because it determines evaluation strategy. Define the evaluation criteria first; define agent scope to make those criteria measurable.

---

## Decision 4: Human approval gated on evidence, not time

**What:** Agent outputs that require human review are not released on a time-based schedule (daily digest, weekly report). They are released when the evidence quality exceeds a threshold — confidence score above threshold, citation completeness above threshold, freshness within window.

**Why:** Time-based releases create pressure to surface outputs that are not ready. A weekly digest that must go out Friday whether or not the evidence is strong produces noisy outputs that train users to discount the digest. Quality-gated releases — released when the output meets the bar — create a consistent expectation: if you receive an output, it met the quality criteria.

**What this reflects:** Release cadence is a product design decision, not just an operational one. The cadence shapes user expectations and determines whether users trust the output they receive.

---

## What These Decisions Have in Common

All four decisions reflect the same product principle: for AI to be useful in high-stakes professional contexts, the quality bar is not "usually right" — it is "trustworthy enough to act on without independent verification." That bar is much higher and requires different architectural decisions than a consumer AI product. The citation requirement, the knowledge graph, the specialised agents, and the quality-gated release model are all expressions of that higher bar.
