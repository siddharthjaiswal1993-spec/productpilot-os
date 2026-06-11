# MVP Scope: ProductPilot OS V1

## V1 Theme: PM Copilot

V1 of ProductPilot OS delivers the PM Copilot — giving individual product managers dramatically more leverage through evidence-backed briefs, cited PRDs, and automated stakeholder communication. V1 does not attempt to automate product decisions; it amplifies PM judgment with synthesized evidence.

**V1 success criterion:** A PM who uses ProductPilot OS for 30 days achieves >75% PM acceptance rate and recovers >5 hours/week from signal assembly, documentation, and stakeholder communication.

---

## In Scope for V1

### Module 1: Signal Intelligence Hub

The real-time dashboard for ingested, classified, and prioritized product signals.

- Signal ingestion from Slack, Jira, Zendesk, Salesforce/HubSpot, Gong, and one analytics source (Amplitude or Mixpanel)
- Automated signal classification: feature request, bug report, churn risk, competitive signal, technical risk
- Signal deduplication: detect and merge signals about the same issue from different sources
- Signal priority scoring: composite score based on source strength, recency, and business impact proxy (account tier from CRM)
- PM signal inbox: daily digest of top signals with preview, source, and action options
- Manual signal submission: PM can add signals from sources not yet integrated

### Module 2: Opportunity Brief Generator

Evidence-assembled, PM-approved opportunity briefs with full citation.

- Automated brief generation from a PM-selected signal cluster or manual trigger
- Evidence assembly: retrieve top 5–8 relevant evidence segments from vector index
- Evidence table: structured table with source, author, date, excerpt, and strength rating
- Business impact estimate: auto-populated from CRM integration (ARR, deal pipeline)
- RICE scoring: automated initial scoring with evidence-backed dimension values
- Confidence score: 0–100 composite score with component breakdown
- PM review interface: edit any field, add/remove evidence, adjust scores
- PM approval gate: brief does not advance to downstream steps without explicit PM approval
- Knowledge graph update: approved brief adds entities and decisions to the graph

### Module 3: Prioritization Engine

Evidence-backed prioritization scoring against the active backlog.

- Prioritization brief generation from approved opportunity brief
- RICE + strategic alignment composite scoring
- Backlog comparison view: new item scored against top-10 backlog items
- PM drag-reorder with evidence toggle: see evidence for any item on demand
- Priority decision record: every priority change logged with timestamp and PM
- Jira sync (read + write with PM approval): display Jira backlog items; write priority changes only with explicit PM approval

### Module 4: PRD Copilot

AI-generated PRD drafts grounded in organizational evidence, with inline citations.

- PRD generation from approved opportunity brief
- Template support: PM team can configure PRD template; agent generates to template structure
- Section generation with evidence retrieval: each PRD section retrieves relevant evidence from signal corpus
- Inline citation injection: every factual claim linked to cited source
- User story generation with customer quote citations
- Acceptance criteria generation
- Edge case suggestions (from historical similar features in knowledge graph)
- PM edit interface: full inline editing with version history
- PM approval gate: PRD is a draft until PM approves for distribution

### Module 5: Risk Watchdog

Continuous monitoring and alert generation for product risks.

- Volume trend monitoring: alert when ticket/signal volume spikes >30% in 7 days
- Release regression detection: correlate new signal spikes with recent release dates
- SLA exposure detection: identify affected accounts with SLA provisions when risks are detected
- Risk brief generation: structured brief with severity, affected accounts, ARR at risk, mitigation options
- PM review interface: PM approves risk classification and mitigation path
- Engineering escalation (with PM approval): create Jira ticket with risk brief attached

### Module 6: Stakeholder Alignment Center

Personalized stakeholder communication generated from shared evidence.

- Audience configuration: define stakeholder groups (engineering, sales, CS, executive) with communication preferences
- Personalized update generation: from a PM-approved decision, generate tailored drafts for each audience
- Role-based framing: engineering gets technical context; sales gets customer/commercial context; executive gets business impact context
- PM review and approval: every communication is a draft until PM approves
- Delivery options: copy to clipboard, send via email (with email integration), post to Slack (with Slack integration)
- Delivery tracking: log which communications were sent to which audiences

### Supporting Infrastructure

- Citation architecture: chunk-level source tracking, inline citation display, source panel
- Confidence scoring engine: composite confidence calculation on every AI output
- Knowledge graph: entity store for features, customers, decisions, signals, outcomes — grows with every interaction
- Human approval architecture: approval gates built into every agent workflow
- Audit log: every agent invocation, PM approval/rejection, data access, and output generation logged
- Basic permissions: Admin, PM, View-only roles
- Integrations: Slack (events API), Jira (webhooks + API), Salesforce/HubSpot (OAuth + polling), Zendesk (webhooks), Gong (API)

---

## Out of Scope for V1

The following capabilities are explicitly deferred to V2 or V3. Including them in V1 would compromise quality on the core copilot experience.

| Capability | Reason for Deferral | Target Version |
|-----------|--------------------|----|
| Autonomous roadmap changes | Requires deeper trust and governance maturity | V3 |
| Auto customer commitments | Requires legal/compliance review and HITL maturity | V3+ |
| Auto sprint reprioritization | Requires deep Jira integration and EM trust | V2 |
| Autonomous production decisions | Out of scope permanently | Never |
| Team-level intelligence dashboard (Group PM/CPO view) | V2 feature — requires multi-PM usage data | V2 |
| Cross-PM signal deduplication | Requires team usage | V2 |
| Meeting Intelligence Agent | Deprioritized to focus on signal synthesis; useful but not MVP | V2 |
| Roadmap Intelligence Agent | Portfolio-level analysis requires V2 data scale | V2 |
| API platform (third-party developers) | V4 capability | V4 |
| Vertical specialization (healthcare, fintech) | V4 capability | V4 |
| SAML SSO / RBAC | Enterprise gates — available in Team/Enterprise tier; MVP focuses on PM individuals | V2 |
| Mobile app | Desktop-first in V1 | V2+ |

---

## MVP Success Criteria

| Criterion | Target | Measurement |
|-----------|--------|------------|
| PM acceptance rate (30-day cohort) | >75% | % of briefs/PRDs approved without major edit |
| Time recovered per PM per week | >5 hours | PM self-report + platform usage analysis |
| Evidence-backing rate (90 days) | >60% | % of roadmap decisions with approved brief |
| p95 brief generation latency | <90 seconds | Infrastructure monitoring |
| Hallucination rate | <2% | Weekly LLM-as-judge review |
| Design partner satisfaction | >4/5 | Monthly NPS survey |

---

## Technical Prerequisites

Before V1 can be launched to design partners:

1. **Data pipeline operational:** Slack, Jira, Salesforce, Zendesk ingestion running reliably with <15-minute latency
2. **Vector index seeded:** Minimum 1,000 documents indexed per design partner before activation
3. **RAG pipeline tested:** Retrieval quality >80% relevance in human eval (50 queries)
4. **HITL approval flow complete:** Every agent output gated on PM approval before downstream action
5. **Audit log complete:** Every agent invocation and PM action logged
6. **PII scanning operational:** PII detection and redaction running before every output
7. **Citation architecture complete:** Every factual claim cites source with ID and excerpt

---

## Design Partner Rollout Plan

**Phase 1 (Month 1–2):** 3 design partners. White-glove onboarding. Weekly check-ins. Product team directly supports every integration setup.

**Phase 2 (Month 3–4):** 5 additional design partners. Onboarding guide published. First version of self-serve integration setup.

**Phase 3 (Month 5–6):** Commercial launch. First paying customers. Pricing tiers activated.

---

*V1 is about doing a few things exceptionally well, not many things adequately. The PM who trusts the first brief is the PM who becomes the champion who drives team-wide adoption.*
