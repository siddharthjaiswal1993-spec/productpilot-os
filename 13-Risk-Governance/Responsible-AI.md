# Responsible AI Policy

## Principles

ProductPilot OS is built on the belief that AI systems in professional settings must be accountable, transparent, and safely constrained. The following principles govern all AI design decisions in the product.

---

## Principle 1: Human Agency Over All Consequential Decisions

AI in ProductPilot OS suggests. Humans decide.

No prioritization decision, PRD approval, customer communication, Jira update, or stakeholder notification occurs without explicit PM approval. The system is designed so that PMs always know what the AI has done and what it is about to do before it does it.

**What this means in practice:**
- All agent outputs are labeled "Draft — Pending PM Approval"
- All external writes (Jira, communications) require a second explicit confirmation
- PMs can edit any agent output before approving
- PMs can reject any agent output at any time
- The system cannot learn its way into bypassing these gates — they are architectural, not behavioral

---

## Principle 2: Transparency in Reasoning

PMs should always be able to understand why the system produced an output.

Every brief includes a confidence score with component breakdown, an evidence table with source citations, and a "How was this generated?" reasoning trace. The system is designed to be legible, not magical.

**What this means in practice:**
- No black-box scores — every score has a decomposed explanation
- Evidence is always cited at source level (system, author, date, excerpt)
- Agent reasoning traces are logged and accessible
- Confidence scores are calibrated, not just qualitative

---

## Principle 3: Evidence Before Recommendation

The system never makes a recommendation that isn't grounded in retrieved evidence from the organization's own data.

**What this means in practice:**
- LLMs are used as synthesizers of retrieved evidence, not as sources of knowledge
- Every factual claim is bound to a cited evidence chunk
- Claims that can't be cited are removed or flagged for PM review
- Hallucination detection is active on every output

---

## Principle 4: Proportional Oversight

Not all actions carry equal risk. Oversight requirements are proportional to consequence.

Reading a signal: no approval needed. Generating a draft: automatic. Recording a decision: explicit PM approval. Writing to Jira: second explicit confirmation. Sending a communication: approval per audience.

**What this means in practice:**
- HITL gate specifications are defined by consequence level, not by agent type
- Low-risk reads are fully automated; high-risk writes are fully gated
- The PM experience is not burdened by approvals for low-consequence actions

---

## Principle 5: Privacy by Design

Customer data, employee communications, and organizational information are treated as inherently sensitive.

**What this means in practice:**
- PII is scanned and redacted in all generated outputs by default
- Slack channel monitoring is opt-in, not opt-out
- Tenant isolation is enforced at the database layer
- Data minimization: only signals relevant to product intelligence are indexed
- Data retention policies are configurable per tenant

---

## Principle 6: Fail Safely

When the system is uncertain, it says so. When the system fails, it fails gracefully.

**What this means in practice:**
- Low-confidence outputs are flagged, not suppressed
- Partial outputs are labeled as partial, not delivered as complete
- System failures show clear error messages with retry options
- No state corruption when an agent fails mid-workflow — partial progress is saved

---

## Responsible AI Risk Register

| Risk | Category | Mitigation |
|------|----------|-----------|
| Hallucination in PM-facing outputs | Safety | Citation binding + post-generation validation + LLM-as-judge |
| Overreliance on AI outputs | Autonomy | HITL gates; confidence transparency; edit capability |
| PII exposure in stakeholder communications | Privacy | PII scanner on all outputs; audience-scoped access controls |
| Cross-tenant data leakage | Security | Namespace isolation at vector DB; tenant ID enforcement |
| AI-amplified prioritization bias | Fairness | Override logging; PM final authority; calibration monitoring |
| Unauthorized autonomous action | Safety | No external writes without PM confirmation; audit log |
| Stale evidence misleading PM decisions | Accuracy | Evidence age labels; recency component in confidence score |

---

## AI Incident Classification

| Severity | Condition | Response Time | Response |
|----------|-----------|--------------|---------|
| P0 | Cross-tenant data exposure, unauthorized external action | <1 hour | Immediate investigation; suspend affected feature; notify affected tenants |
| P1 | Critical hallucination in approved output used in decision | <4 hours | Engineering investigation; PM notification; trust review |
| P2 | PII exposure in generated output | <24 hours | Audit of affected outputs; PM notification; policy review |
| P3 | Repeated model quality regression (Trust Score drop >5 points) | <72 hours | Root cause analysis; prompt/model review |
| P4 | Individual false positive/negative in eval | Next sprint | Add to golden dataset; flag for improvement |

---

## Responsible AI Review Cadence

| Review | Cadence | Participants |
|--------|---------|-------------|
| Trust Score review | Monthly | Engineering, Product |
| AI incident retrospective | After any P0/P1 incident | Engineering, Product, Legal |
| Hallucination rate review | Monthly | Engineering |
| PII handling audit | Quarterly | Engineering, Legal, Security |
| Responsible AI policy review | Annually | All leadership |
