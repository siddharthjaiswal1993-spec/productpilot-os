# Stakeholder Alignment Agent

## Purpose

The Stakeholder Alignment Agent converts a PM's approved priority decision into personalized, audience-specific stakeholder communications. It reads the evidence base of the decision, understands each audience's goals and framing needs, and generates distinct draft communications that all reference the same evidence but are framed appropriately for each audience. The agent eliminates the most time-consuming PM communication task: writing the same decision five different ways.

---

## The Alignment Problem It Solves

Without ProductPilot OS, a PM makes a priority decision and then:
- Writes a Slack message to engineering explaining the technical scope
- Writes a different Slack message to sales about the impact on the pipeline
- Prepares a slide for the weekly executive review
- Updates the roadmap for the CS team
- Drafts a response to the customer success manager who asked about the specific feature

Same decision. Five different audiences. Five different documents. 3–5 hours of communication work after the decision has already been made.

The Stakeholder Alignment Agent does this in 60–90 seconds.

---

## Inputs

| Input | Source | Format | Required |
|-------|--------|--------|---------|
| Approved priority decision | Prioritization approval record | Structured JSON with evidence citations | Yes |
| Audience configuration | Team settings | Audience list with role, communication preferences, key concerns | Yes |
| Relationship context | Knowledge graph | Prior communications to each audience, outstanding asks, commitments made | If available |
| Sales pipeline data | CRM integration | Open opportunities affected by this decision | If available |
| Stakeholder communication history | Knowledge graph | Prior update drafts, PM edits, approval patterns | If available |

---

## Outputs

| Output | Format | Recipient |
|--------|--------|----------|
| Audience-specific draft (×N) | Rich-text draft in PM editing interface | PM review queue |
| Evidence footnotes | Shared evidence table referenced by all drafts | Each draft's citation panel |
| Communication brief | Summary of what each audience received and key framing | PM audit record |
| Staged communications | Ready-to-send/share drafts pending PM approval | PM sends after review |

---

## Audience Framing Logic

For each configured audience, the agent applies audience-specific framing rules:

**Engineering Team:**
- Lead with: Scope, technical requirements, dependencies
- Include: Acceptance criteria summary, edge cases flagged, timeline expectations
- Tone: Technical, specific, no business jargon
- Evidence to surface: Technical signal sources (Jira issues, infra concerns)
- Omit: ARR figures, customer names without engineering need-to-know

**Sales Team:**
- Lead with: Commercial impact, pipeline unblocked, customer commitments addressed
- Include: Which accounts will benefit, expected timeline to general availability
- Tone: Commercial, confident, action-oriented
- Evidence to surface: CRM signals, Gong call excerpts about this need
- Omit: Engineering complexity, implementation details

**Customer Success:**
- Lead with: Which customer requests are addressed, expected availability
- Include: Known open tickets/requests this closes, what to tell customers now
- Tone: Customer-focused, empathetic, concrete
- Evidence to surface: Zendesk tickets, Intercom conversations
- Omit: ARR figures, competitive context

**VP / Executive:**
- Lead with: Business impact, strategic alignment, risk if not addressed
- Include: Evidence-backed business case summary, expected outcome metrics
- Tone: Brief, data-driven, outcome-oriented
- Evidence to surface: Business impact metrics, OKR alignment, ARR at risk
- Omit: Implementation details, technical specifics

**PM Team:**
- Lead with: Decision rationale, what evidence drove this, how it was scored
- Include: RICE summary, prior decisions this relates to, knowledge graph updates
- Tone: Transparent, analytical, honest about uncertainty
- Evidence to surface: Full evidence table, confidence score explanation
- Omit: Nothing — PM team deserves full context

---

## Reasoning Pattern

```
For each configured audience:

Step 1 [REASON]: What does this audience care about most regarding this decision?
Step 2 [REASON]: What framing will make this decision legible to them?
Step 3 [ACT]: Retrieve: audience-specific evidence from the approved brief's evidence table
Step 4 [ACT]: Retrieve: prior communications to this audience from knowledge graph
Step 5 [REASON]: Are there outstanding commitments or asks from this audience this decision addresses?
Step 6 [REASON]: What should I include vs. omit for this audience's access level?
Step 7 [ACT]: Generate draft communication with audience framing
Step 8 [ACT]: Inject citations (footnotes, not inline — stakeholder communications need to be readable)
Step 9 [REASON]: Would a stakeholder in this role find this communication clear and actionable?
Step 10 [OUTPUT]: Queue draft for PM review
```

---

## Autonomy Level

**No autonomous communication.** The Stakeholder Alignment Agent generates drafts. It does not send, post, or share anything.

Every communication is:
1. Generated as a clearly-labeled "Draft — Pending PM Approval"
2. Reviewed by the PM per-audience
3. Approved individually before it can be shared

The PM can approve all drafts in batch, or review each one individually. Even in batch mode, each approval is logged separately in the audit trail.

---

## Guardrails

1. **No disclosure without access control check:** Customer names, contract values, and ARR figures are only included in a communication if the PM's access control configuration for that audience permits it.

2. **No speculative timelines:** The agent never generates timeline commitments it cannot source from the PM's roadmap configuration. If timeline is unspecified, the draft says "[Timeline: PM to specify]" — never guesses.

3. **Consistent evidence base:** All audience-specific drafts must reference the same underlying evidence table. Different framing of the same evidence is appropriate; fabricating different evidence for different audiences is not.

4. **Contradiction detection:** If two audience-specific drafts would make conflicting claims (e.g., Sales draft says "Q3 availability" but Engineering draft says "timeline TBD"), the system flags the contradiction before delivering drafts to the PM.

---

## Quality Metrics

| Metric | Target |
|--------|--------|
| PM acceptance rate (draft approved without major edit) | >70% per audience type |
| PM edit time per draft | <8 minutes average |
| Communication generation latency (all audiences, p95) | <90 seconds |
| Contradiction detection rate | >95% of contradictions flagged before PM review |
| Audience framing accuracy (PM rates as "right framing") | >80% at 30-day cohort |
