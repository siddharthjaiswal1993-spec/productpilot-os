# V3: Agentic Product Organization

## Timeline: Months 15–24

## Strategic Intent

V3 is the transition from "AI-assisted PM" to "AI-native product organization." By V3, teams using ProductPilot OS have 12–18 months of compounding knowledge graph data and high-calibration agents. The question shifts from "can AI help this PM?" to "how much of the routine PM intelligence work can operate autonomously, with human oversight at the right altitude?"

V3 is not about replacing PMs. It is about elevating them. PMs stop spending time finding information and synthesizing signals — they spend time on judgment, strategy, and customer relationships.

---

## V3 Scope

### 1. Autonomous Signal Monitoring and Brief Generation
In V1/V2, PMs trigger brief generation manually. In V3, the system can generate briefs autonomously when signal thresholds are crossed — and deliver them to PM inbox for review.

**What ships:**
- Configurable auto-brief triggers (signal volume thresholds, specific signal types, specific customer mentions)
- Autonomous brief generation with HITL gate
- PM inbox: briefs arrive without manual trigger
- PM can configure "brief me when..." rules per product area

**Safeguard:** Briefs are always drafts. PM approval still required before any downstream action. The autonomy is in generation, not in decision.

### 2. Predictive Prioritization
Based on outcome history in the knowledge graph, the system can begin predicting which prioritization decisions are likely to achieve their expected outcomes.

**What ships:**
- Outcome-correlation model: which types of evidence predict successful outcomes?
- Priority recommendations that surface outcome predictions: "Features with similar evidence profiles had 73% outcome achievement rate in your org"
- Confidence-adjusted RICE: confidence component calibrated to historical accuracy per PM and product area

**Foundation required:** 12+ months of outcome records in the knowledge graph

### 3. Cross-Product Intelligence
For CPOs managing multiple product lines, V3 provides a portfolio-level intelligence layer that synthesizes across PM teams.

**What ships:**
- Portfolio-level signal aggregation (cross-product area)
- Cross-product customer journey intelligence (same customer expressing needs across multiple products)
- Resource allocation intelligence: "Product Area A has 8 high-confidence opportunities; Product Area B has 2 — does this reflect the intended resource split?"
- Portfolio roadmap coherence analysis

**Target users:** CPO, VP Product (multiple products), Head of Product

### 4. Proactive Strategic Briefs
V3 agents don't just respond to PM queries — they proactively identify emerging strategic patterns that no single PM would see.

**What ships:**
- Market signal monitoring (from customer-facing signals, not web scraping)
- Competitive mention trend analysis (from Gong, CRM, support signals)
- Customer segment cohort intelligence: "Your enterprise customers are shifting to [need X] — your mid-market customers are still on [need Y]"
- Strategic drift alerts at portfolio level

### 5. Automated Draft PRDs for Routine Enhancements
For well-understood, routine product enhancements with strong evidence backing (confidence >80), V3 can auto-generate draft PRDs that arrive in PM inbox for review.

**What ships:**
- Auto-PRD generation for high-confidence briefs (PM configurable threshold)
- PM inbox notification: "PRD draft ready for [feature] (confidence: 87/100)"
- Same HITL gate as manual PRD — PM reviews and approves before sharing

**Safeguard:** This is only available for PM-configured product areas with high evidence quality. It reduces the gap between "we approved this priority" and "PRD is ready for engineering" from days to hours.

### 6. Public API: Product Intelligence Layer
V3 opens the Product Knowledge Graph and intelligence capabilities as an API for organizations that want to build custom applications on top of their product intelligence.

**What ships:**
- REST API for knowledge graph queries
- Webhooks for decision events
- Signal ingestion API (custom source connectors)
- Evidence retrieval API
- Developer portal and documentation

**Target:** Large enterprises and technical PM teams who want to integrate ProductPilot intelligence into their own tooling

---

## V3 Vision: The Autonomous Intelligence Layer

By V3, a product organization operating on ProductPilot OS operates at a qualitatively different pace:

**Signal loop:** New customer signal → classified and indexed in <15 min → brief auto-generated (if threshold) → in PM inbox by morning

**Decision loop:** PM reviews brief → 3-minute approval → PRD draft arrives → 45-minute PM review → shared with engineering by end of day

**Communication loop:** Decision approved → 5 audience-specific drafts generated → PM reviews in 15 minutes → all stakeholders updated same day

**Risk loop:** Volume anomaly detected → risk brief in inbox → PM acknowledges → engineering Jira ticket created → all within 2 hours of anomaly

---

## V3 Success Criteria (Month 24)

| Metric | Target |
|--------|--------|
| ARR | $2.5M |
| NRR | >130% |
| Enterprise customers | >20 |
| Evidence-Backing Rate | >80% |
| PM Acceptance Rate | >80% |
| Auto-generated briefs (accepted without PM query) | >30% of briefs |
| Outcome records in knowledge graph | >500 across customer base |
| API customers | ≥5 |
