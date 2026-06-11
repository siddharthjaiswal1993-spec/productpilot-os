# Human-in-the-Loop (HITL) Architecture

## Why HITL is a Core Design Principle

Human-in-the-loop is not a constraint imposed on ProductPilot OS. It is a deliberate design choice that enables PM adoption.

The failure mode of agentic AI products in enterprise settings is not that they produce bad outputs — it's that PMs feel the system is acting on their behalf without their knowledge or approval. When a PM discovers that their name was attached to a stakeholder communication they didn't write, or that a Jira ticket was updated with a priority they didn't confirm, they stop using the tool. Trust, once broken by unexpected autonomous action, is very hard to rebuild.

ProductPilot OS is designed so that a PM always knows exactly what the system has done and exactly what it will do before it does it.

---

## The HITL Taxonomy

Not all actions carry equal risk. The HITL design applies proportional oversight based on action consequence:

| Consequence Level | Actions | HITL Requirement |
|------------------|---------|-----------------|
| **Low (read-only)** | Signal retrieval, evidence search, knowledge graph query, confidence scoring | No approval required — system reads freely |
| **Medium (internal write)** | Adding signals to Signal Hub, creating entities in knowledge graph, generating drafts in PM workspace | Automatic — PM sees the result but does not need to approve the write |
| **High (decision records)** | Recording a priority decision, updating a brief status, logging a PRD approval | Explicit PM click approval required |
| **Critical (external writes)** | Jira ticket creation, Jira priority update, stakeholder communication sent | Double confirmation — approval + confirmation of the specific external action |

---

## HITL Gate Design

### Gate Type A: Draft Delivery Gates
The system generates a draft and delivers it to the PM inbox. Nothing happens until the PM acts.

**Applies to:** Opportunity briefs, prioritization briefs, PRD drafts, stakeholder communication drafts, risk briefs

**UI:** Clear "Draft" label throughout the interface; all downstream actions disabled until approval.

**PM actions available:**
- Edit (any field — edits version-tracked)
- Approve (required to unlock downstream)
- Reject (requires reason; reason captured as feedback signal)
- Defer (moves to review queue; alert persists)
- Regenerate (retry with same inputs or with PM-provided context)

### Gate Type B: External Write Confirmation Gates
The PM has already approved a decision, but the system is about to write to an external system (Jira, email, Slack).

**Applies to:** Jira priority updates, Jira ticket creation, stakeholder message sending, engineering escalation

**UI:** A confirmation dialog showing exactly what will be written, to where, and by whom. "Cancel" and "Confirm" are the only options.

**Why separate from Gate Type A:** An opportunity brief approval is a product intelligence decision. A Jira write is an external commitment. These carry different risks and require separate, explicit consent.

### Gate Type C: Escalation Approval Gates
The system has detected something urgent (P1/P2 risk) and is proposing an escalation action.

**Applies to:** Engineering escalation for risk events, customer SLA breach alerts, security incident notifications

**UI:** High-urgency notification with risk context → PM opens risk brief → PM reviews mitigation options → PM selects "Escalate" → Gate B confirmation for the specific escalation action

---

## HITL and Speed

A common objection to HITL: "It slows everything down."

The response: HITL gates in ProductPilot OS are designed to be fast for PM review, not slow. The goal is that a PM can review an opportunity brief in 2–3 minutes, not because we're asking them to rubber-stamp it, but because the brief is well-structured and evidence is easily verifiable.

The speed improvement is not "AI acts instead of PM." The speed improvement is:
- PM spends 2 minutes reviewing a brief that would have taken 3 hours to create
- PM spends 5 minutes reviewing a PRD that would have taken 2 days to write
- PM spends 3 minutes reviewing 5 stakeholder drafts that would have taken 3 hours to write

The approval step is the smallest part of the time savings. The synthesis work — which previously took days — now takes seconds. HITL adds minutes, not hours.

---

## PM Approval Patterns

The system is designed to support different PM approval styles:

**Deep review mode:** PM opens every citation, reads every evidence chunk, adjusts any field before approving. Appropriate for high-stakes decisions (roadmap bets, enterprise customer risks, board-level narratives).

**Scan and approve mode:** PM scans the brief, reviews the summary, spot-checks 2–3 key citations, approves. Appropriate for routine prioritization decisions and standard stakeholder updates.

**Batch approval mode:** PM reviews multiple low-stakes outputs (daily signal digest, routine stakeholder updates) in sequence. Each approval is individual and logged; batch mode is a UI convenience, not a bypass.

---

## Override Design

When a PM disagrees with the system's recommendation:

1. **RICE dimension override:** PM can change any RICE dimension value. Override is logged with original value, new value, PM name, timestamp, and optional reason.

2. **Business impact override:** PM can change the business impact estimate. Override is logged and compared against actual outcomes when available.

3. **Priority rank override:** PM can place an item at a different backlog rank than suggested. Override is logged with reason.

4. **Agent output rejection:** PM can reject any agent output entirely. Rejection reason is required and feeds back into the eval dataset.

All overrides are visible in the decision audit trail. Leadership can see not just what decisions were made, but how often the PM overrode the system's recommendation and why.

---

## HITL in the Audit Trail

Every HITL gate event is recorded in the immutable audit log:

```json
{
  "event_type": "pm_approval",
  "gate_type": "A",
  "item_id": "brief_abc123",
  "item_type": "opportunity_brief",
  "pm_id": "pm_alex_chen",
  "pm_name": "Alex Chen",
  "action": "approved",
  "edit_count": 2,
  "edit_distance_score": 0.12,
  "major_edit": false,
  "timestamp": "2025-03-15T14:32:11Z"
}
```

This audit trail answers the question every compliance team, legal team, and enterprise customer eventually asks: "Who approved this decision and when?"

---

## HITL as a Learning Signal

Every PM approval/rejection is a labeled training example:

| PM Action | Training Signal |
|-----------|----------------|
| Approve without edit | High-quality output; format, content, and framing are correct |
| Approve with minor edits | Good output with refinements; analyze edit patterns |
| Approve with major edits | Structurally correct but content needs work; major improvement signal |
| Reject with reason | Output failed on specified dimension; high-value negative example |
| Override RICE dimension | Model's estimate was wrong on this dimension; calibration signal |

This feedback flywheel is the mechanism by which the system improves over time without retraining — it improves prompting, retrieval strategies, and output formatting based on real PM feedback.
