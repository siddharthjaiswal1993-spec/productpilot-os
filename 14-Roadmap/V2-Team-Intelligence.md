# V2: Team Intelligence Layer

## Timeline: Months 7–14

## Strategic Intent

V1 proves value for individual PMs. V2 makes the team smarter. V2 shifts the unit of value from "this PM makes better decisions" to "this team has a shared intelligence layer that no individual PM could build alone."

V2 is the upgrade from "personal AI assistant" to "team product intelligence platform." It's the version where VP Product starts paying attention.

---

## V2 Scope

### 1. Roadmap Intelligence Agent
Cross-PM visibility into roadmap health. Evidence-backing rate by roadmap item. Unaddressed signal detection.

**What ships:**
- Weekly roadmap health report (evidence score per roadmap item)
- Unaddressed signal cluster detection
- Strategic drift alert (% roadmap capacity vs. OKR alignment)
- Audience-specific roadmap narratives (executive, sales, engineering)
- Evidence-Backing Rate as a visible team metric

**Target users:** VP Product, Group PM, CPO

**Success metric:** Evidence-Backing Rate increases from V1 baseline to >70% by Month 12

### 2. Meeting Intelligence Agent
Transcript → decisions → action items → signals → personalized follow-ups. Every meeting captured, structured, and indexed.

**What ships:**
- Gong and Zoom transcript processing
- Decision extraction with confidence levels
- Action item extraction with owners and due dates
- Signal extraction (new signals auto-submitted to Signal Hub)
- Per-attendee follow-up drafts (HITL-gated)
- Meeting history accessible from decision records

**Success metric:** >60% of extracted action items confirmed by PM; >60% of extracted signals added to Signal Hub

### 3. Executive Summary Agent
Weekly and quarterly product intelligence synthesis for CPO and VP Product.

**What ships:**
- Weekly executive summary (signal volume, top opportunities, active risks, evidence-backing rate)
- Quarterly product narrative for board/investor reviews
- Decision quality report (DQS trends, acceptance rate trends)
- Configurable audience profiles (CPO vs. VP Engineering vs. Board)

**Success metric:** >80% of generated summaries used in leadership reviews without major revision

### 4. Team Intelligence Dashboard
Aggregate product intelligence metrics for Group PMs and VP Product.

**What ships:**
- Cross-PM evidence-backing rate
- Signal volume trends by product area and PM
- Decision quality metrics by PM and product area
- Risk alert resolution times
- PM acceptance rate trends
- Knowledge graph growth metrics

**Target users:** VP Product, Group PM, CPO

### 5. Knowledge Graph: Version 2
Enhanced entity model with outcome tracking. The V2 graph starts answering: "Did that decision work?"

**What ships:**
- Outcome recording: PM can link a feature's actual outcome to its decision record
- Outcome vs. expectation comparison
- "Decision this area" query: "Show me all decisions in the payments product area and their outcomes"
- Cross-PM entity resolution: when two PMs index signals about the same customer, the graph merges them
- Knowledge graph export API (beta)

**Success metric:** >50% of shipped features have outcome records at 3 months post-ship

### 6. Enterprise Integrations Expansion
Additional integrations required for enterprise customers.

**What ships:**
- Intercom, Freshdesk, HubSpot (if not V1)
- Notion enhanced integration (full workspace sync)
- Google Workspace (Docs, Meet)
- Amplitude, Mixpanel (product analytics aggregate metrics)
- Linear integration

### 7. SSO / SAML Integration
Enterprise procurement requirement. Blocks deals at >200-person companies.

**What ships:**
- SAML 2.0 SSO
- OIDC
- Okta, Azure AD, Google Workspace SSO
- Just-in-time user provisioning

**Target:** Available by Month 10 (before major enterprise sales cycle closings)

---

## V2 Expansion Model

V2 introduces Group PM and VP Product as buyer personas. The unit of expansion shifts from "per PM" to "per team" and then "per org."

**Expansion motion:**
1. IC PM is champion (V1 adoption)
2. VP Product sees Decision Quality Report → wants team-wide metrics
3. Group PM needs cross-PM coordination → adopts Team Intelligence Dashboard
4. VP Product upgrades from Team to Enterprise → organization-wide rollout

**Target NRR:** >120% (expansion within existing accounts)

---

## V2 Pricing Change

| Tier | V1 Price | V2 Price | What Changed |
|------|----------|----------|--------------|
| Starter | $49/PM/mo | $49/PM/mo | No change |
| Team | $99/PM/mo | $99/PM/mo | Adds Meeting Intelligence, Roadmap Intelligence |
| Enterprise | Custom | Custom | Adds Team Dashboard, Executive Summary, Knowledge Graph export |

---

## V2 Success Criteria (Month 14)

| Metric | Target |
|--------|--------|
| ARR | $800K |
| NRR | >115% |
| Evidence-Backing Rate | >70% |
| PM Acceptance Rate | >73% |
| Team tier accounts | >30 |
| Enterprise design partners | ≥3 |
| CPO/VP use of Executive Summary | >5 VP-level users across design partners |
