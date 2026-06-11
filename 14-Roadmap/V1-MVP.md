# V1: Product Intelligence Foundation (MVP)

## Timeline: Months 1–6

## Strategic Intent

V1 is about proving the core value proposition: a PM team that uses ProductPilot OS makes faster, better-evidenced decisions than one that doesn't. Every feature in V1 is in service of this proof.

V1 does not try to do everything. It does six things extremely well.

---

## V1 Scope: What's In

### 1. Signal Intelligence Hub
The foundation of everything. If signals don't flow in reliably and get classified correctly, nothing else works.

**What ships:**
- Slack, Jira, Zendesk, Gong integrations (webhook + REST API)
- Salesforce and HubSpot integrations (CRM data for business impact)
- Signal classification (feature_request, bug_report, churn_risk, competitive, technical)
- Signal deduplication and clustering
- Volume trend detection with configurable thresholds
- PM signal feed with filtering by source, type, date, product area

**Success metric:** >90% of signals classified correctly; signal feed used by PM daily within 30 days

### 2. Insight Synthesizer Agent (Opportunity Briefs)
The primary value-delivery vehicle. PMs need to be able to turn a cluster of signals into a structured brief in under 90 seconds.

**What ships:**
- Brief generation from signal cluster
- Evidence table with source citations
- Confidence score with 5-component breakdown
- Business impact estimate with CRM integration
- Preliminary RICE scoring
- HITL approval gate (PM must approve before downstream actions)

**Success metric:** >65% PM acceptance rate within 30 days of launch; brief generation p95 <90s

### 3. Prioritization Agent
RICE scoring with evidence per dimension. Backlog comparison against Jira. Stakeholder-ready recommendation.

**What ships:**
- RICE dimension scoring with evidence citations
- Strategic alignment against team OKRs
- Backlog comparison table (Jira integration)
- Priority decision record with immutable audit trail
- Staged Jira priority update (requires PM confirmation)

**Success metric:** >60% PM acceptance rate on prioritization briefs; Jira update confirmation used

### 4. PRD Copilot Agent
First-draft PRD generation from approved opportunity briefs. Template-compatible. Every section cited.

**What ships:**
- PRD generation from approved brief
- User stories with per-story citations
- Acceptance criteria (Given/When/Then)
- Edge case suggestions from knowledge graph
- Out-of-scope list
- Success metrics section
- Team PRD template support

**Success metric:** >55% PM acceptance rate on PRD first drafts; PM edit time <45 minutes

### 5. Risk Watchdog Agent
Automated 4-hour monitoring cycle. P1/P2 alerts with affected account ARR. Proactive risk briefs.

**What ships:**
- 4-hour background monitoring
- Volume spike detection (configurable thresholds)
- Risk brief with affected accounts and ARR
- Release correlation detection
- P1/P2/P3 alert prioritization
- Engineering escalation workflow (Jira ticket creation, HITL-gated)

**Success metric:** >70% of P1/P2 alerts confirmed as real (not false positives) by PM

### 6. Stakeholder Alignment Agent
5 audience-specific communication drafts from one approved decision. HITL-gated per audience.

**What ships:**
- Engineering, Sales, CS, VP/Exec, PM Team audience profiles
- Audience-specific framing logic
- PII screening per audience access level
- Contradiction detection across drafts
- Per-audience approval gate

**Success metric:** >65% PM acceptance rate on stakeholder drafts; communication generation p95 <90s

---

## V1 Out of Scope (Explicitly)

| Feature | Why Deferred |
|---------|-------------|
| Roadmap Intelligence Agent | Requires more knowledge graph history to be useful; prioritize signals first |
| Executive Summary Agent | Needs a base of approved decisions to synthesize; V1 is building that base |
| Meeting Intelligence Agent | High complexity; separate session workflows; deprioritized in favor of depth in core 6 |
| Multi-tenant team analytics | Requires significant PM base; design for V2 when we have data |
| Fine-tuned models | V1 uses off-the-shelf frontier models with prompt engineering; fine-tuning after feedback data exists |
| Mobile interface | Web-first; PM workflows are desktop workflows |
| Public API | Focus on integration depth; API for external access is V2 |

---

## V1 Design Partner Program

**Timeline:** Months 1–3: 3–5 design partners  
**Selection criteria:** Mid-size B2B SaaS (50–500 employees), 2–8 PMs, uses Jira + Slack + at least one of Zendesk/Gong/Salesforce  
**Commitment:** 2 hours/week of feedback sessions; participation in monthly human eval sessions  
**What they get:** Unlimited access, direct product influence, public case study (opt-in)

**Design partner success criteria:**
- At least 2 PMs using the product daily within 30 days
- At least 1 priority decision made using ProductPilot evidence within 60 days
- PM Acceptance Rate above 55% at 60 days

---

## V1 Success Criteria (6-Month Milestone)

| Metric | Target |
|--------|--------|
| Design partner NPS | >40 |
| PM Acceptance Rate (overall) | >63% |
| Briefs generated per PM per week | >3 |
| Decisions with evidence backing | >60% of roadmap decisions at 6 months |
| Evidence-Backing Rate | >50% |
| Hallucination rate | <2% |
| Retention (DAU/MAU) | >50% |
| ARR | $150K at 6 months |
