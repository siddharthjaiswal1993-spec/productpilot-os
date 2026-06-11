# Knowledge Graph

## Purpose

The Product Knowledge Graph is the compounding moat of ProductPilot OS. It is a structured, persistent representation of product intelligence: entities (features, customers, signals, decisions, outcomes), relationships between them, and the temporal history of how those relationships evolved.

The knowledge graph answers questions that neither a vector index nor a relational database can answer well:
- "What evidence supported the decision to build feature X?"
- "Which customers have expressed needs that relate to our current roadmap item?"
- "What happened to the last five features that addressed this type of customer need?"
- "How have this customer's expressed needs changed over the past 12 months?"

---

## Entity Model

### Core Entity Types

**Feature**
```
Entities: Features, roadmap items, product capabilities
Key attributes: name, product_area, status (planned/in_development/shipped/deprecated), version_shipped, description
Relationships: REQUESTED_BY (Customer), SUPPORTED_BY (Signal), DECIDED_IN (Decision), HAS_OUTCOME (Outcome)
```

**Customer**
```
Entities: Customer accounts, market segments
Key attributes: account_name, account_id, crm_id, arr, tier, renewal_date, sla_protected, health_score
Relationships: ASSOCIATED_WITH (Signal), AFFECTS (Feature), ARR_EXPOSURE (Decision)
```

**Signal**
```
Entities: Individual product intelligence signals (tickets, calls, messages, docs)
Key attributes: signal_id, source_type, source_id, author, date, signal_type, classification_confidence, product_area
Relationships: EXPRESSED_BY (Customer), ABOUT (Feature), REFERENCES (Decision), CLUSTERS_WITH (Signal)
```

**Decision**
```
Entities: Formal PM decisions recorded in the system
Key attributes: decision_id, decision_type, brief_id, decision_text, pm_id, approved_at, dqs, confidence_score_at_approval
Relationships: REFERENCES (Signal), DECIDES_ON (Feature), MADE_BY (PM), HAS_OUTCOME (Outcome)
```

**Outcome**
```
Entities: Results of decisions, measured against expectations
Key attributes: outcome_id, decision_id, feature_id, expected_outcome, actual_outcome, measurement_date, outcome_met (bool)
Relationships: RESULTS_FROM (Decision), AFFECTS (Customer), UPDATES (Feature status)
```

**PM**
```
Entities: Product manager users
Key attributes: pm_id, name, email, role, product_areas
Relationships: MADE (Decision), MANAGES (Feature), BELONGS_TO (Team)
```

---

## Relationship Model

The most important relationships for PM intelligence use cases:

| Relationship | From → To | Meaning | Created By |
|-------------|-----------|---------|-----------|
| EXPRESSES_NEED_FOR | Customer → Feature | Customer has signaled a need for this feature | Signal processing pipeline |
| ASSOCIATED_WITH | Customer → Signal | This signal originated from or was attributed to this customer | Entity extraction |
| SUPPORTED_BY | Feature → Signal | This signal is evidence for building this feature | Brief generation |
| DECIDED_IN | Feature → Decision | This feature's priority was decided in this decision record | HITL approval |
| REFERENCES | Decision → Signal | This decision cited this signal as evidence | Brief generation |
| HAS_OUTCOME | Decision → Outcome | This outcome was the result of this decision | PM outcome recording |
| CLUSTERS_WITH | Signal → Signal | These signals are about the same topic | Clustering pipeline |
| PRIOR_TO | Decision → Decision | This decision preceded and informed another decision | Temporal ordering |

---

## Graph Storage

**Technology:** Neo4j (graph database) or equivalent property graph database

**Access patterns:**
- **Write-heavy:** Signal ingestion, entity extraction, relationship creation (continuous)
- **Read-heavy:** Agent queries for context enrichment, PM "why did we build that?" queries (per-brief)
- **Traversal-heavy:** Decision lineage queries ("what signals led to this decision?"), customer history queries ("what has this customer asked for over time?")

**Tenant isolation:** Each tenant has a dedicated graph namespace. Cross-namespace queries are blocked at the database layer.

---

## Knowledge Graph Growth

The knowledge graph grows through three mechanisms:

**1. Automated enrichment (continuous):**
Every signal ingested goes through entity extraction: product area detected → Feature entity created or matched; customer name detected → Customer entity created or matched; relationship ASSOCIATED_WITH created.

**2. Agent-driven enrichment:**
When the Insight Synthesizer generates a brief, it creates Feature entities for any new product area identified, links signals to features via SUPPORTED_BY, and links customers to features via EXPRESSES_NEED_FOR.

**3. PM-driven enrichment:**
When a PM approves a decision, the Decision entity is created and linked to the relevant Feature, Signal, and PM entities. When a PM records an outcome, the Outcome entity is created and linked to the Decision.

---

## Knowledge Graph Query Examples

**"Why did we build feature X?"**
```
MATCH (f:Feature {name: "Enterprise SSO"})<-[:DECIDED_IN]-(d:Decision)
MATCH (d)-[:REFERENCES]->(s:Signal)
MATCH (s)-[:ASSOCIATED_WITH]-(c:Customer)
RETURN d.decision_text, d.confidence_score_at_approval, 
       collect(s.excerpt) as evidence, collect(c.account_name) as customers
```

**"Which customers have asked for things we haven't built?"**
```
MATCH (c:Customer)-[:EXPRESSES_NEED_FOR]->(f:Feature)
WHERE f.status = "not_planned" OR NOT EXISTS(f.status)
RETURN c.account_name, c.arr, collect(f.name) as unaddressed_needs
ORDER BY c.arr DESC
```

**"What happened to the last 5 decisions in the authentication product area?"**
```
MATCH (d:Decision)-[:DECIDES_ON]->(f:Feature {product_area: "authentication"})
OPTIONAL MATCH (d)-[:HAS_OUTCOME]->(o:Outcome)
RETURN d.decision_text, d.approved_at, d.confidence_score_at_approval, 
       o.actual_outcome, o.outcome_met
ORDER BY d.approved_at DESC
LIMIT 5
```

---

## The Compounding Moat

The knowledge graph is a structural moat for ProductPilot OS because:

1. **It compounds with time:** Every week of use adds more entities, more relationships, more decision history. After 12 months, the graph contains the organization's complete product decision history — a resource that cannot be replicated by a competitor arriving later.

2. **It creates switching cost:** Migrating away from ProductPilot OS means losing not just the workflow tool, but the organization's institutional memory for why product decisions were made. This is the kind of switching cost that makes enterprise churn very low.

3. **It improves agent quality:** Agents that have access to the knowledge graph produce better outputs — more contextually aware briefs, more relevant edge case suggestions, better outcome predictions. The longer the system is used, the better the agents become for that specific organization.

4. **It's not replicable by incumbents:** Productboard and Jira could build a similar signal ingestion layer. They cannot easily build the decision-evidence-outcome graph that takes months of PM usage to populate. The moat grows with usage, not with headcount or engineering investment.
