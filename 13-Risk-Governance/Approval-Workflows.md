# Approval Workflows

## Purpose

Approval workflows are the structured processes by which PM actions become sanctioned outputs in ProductPilot OS. Every consequential action has a defined approval workflow: what the PM reviews, what they decide, and what is recorded.

This document specifies the approval workflow for each output type.

---

## Workflow 1: Opportunity Brief Approval

**Input:** AI-generated opportunity brief (draft)  
**Reviewer:** PM who triggered generation  
**Decision required:** Approve / Reject / Edit and Approve / Defer

**Review checklist (shown in UI):**
- [ ] Problem statement accurately describes the opportunity
- [ ] Evidence table contains relevant, recent, and cited sources
- [ ] Business impact estimate is reasonable given evidence
- [ ] Confidence score component breakdown makes sense
- [ ] Preliminary RICE is a reasonable starting point

**Approval record captured:**
- PM ID, PM name
- Approval timestamp
- Edit count and edit distance score
- Confidence score at approval
- Notes (optional free text)

**Downstream unlocked on approval:**
- "Score for Prioritization" button
- "Generate PRD" button
- Brief is visible to team members (if team sharing enabled)

---

## Workflow 2: Priority Decision Approval

**Input:** Prioritization brief (RICE score, backlog comparison, strategic alignment)  
**Reviewer:** PM responsible for this product area  
**Decision required:** Approve priority rank / Reject / Override individual dimensions

**Review checklist (shown in UI):**
- [ ] RICE dimensions have cited evidence
- [ ] Backlog placement makes sense in context of team capacity
- [ ] Strategic alignment score reflects OKR priorities
- [ ] Any overridden dimensions have recorded rationale

**Override capture:**
- If PM changes a RICE dimension: original value, new value, reason (required)
- If PM changes backlog rank: original rank, new rank, reason (required)

**Approval record captured:**
- PM ID, final RICE score, final backlog rank, override count, override reasons
- This record is the core "decision made" event in the audit trail

**Second confirmation required (if Jira write):**
After approval, if the priority decision changes a Jira item's priority:
- Separate "Confirm Jira Update" dialog shown
- Preview of the specific Jira change displayed
- PM must click "Confirm" separately — approval of the decision does not implicitly authorize the Jira write

---

## Workflow 3: PRD Approval

**Input:** AI-generated PRD draft  
**Reviewer:** PM authoring the PRD  
**Decision required:** Approve for sharing / Continue editing / Reject and regenerate

**Approval gates in the PRD:**
- PMs can approve section by section (marking sections as reviewed)
- Full PRD requires all sections to be marked before final approval
- Unapproved sections show a "Pending review" badge visible to engineering recipients

**What approval enables:**
- "Share with Engineering" option becomes available
- PRD appears in team's approved PRD library
- PRD creates Feature entity in knowledge graph

**What approval does NOT enable:**
- Sending directly to any external channel — PM still chooses where and when to share
- Automatic sprint commitment — PRD approval is product commitment, not engineering commitment

---

## Workflow 4: Stakeholder Communication Approval

**Input:** AI-generated audience-specific communication draft (×N audiences)  
**Reviewer:** PM who owns the stakeholder relationship  
**Decision required:** Per-audience approve / edit and approve / reject

**Per-audience review:**
Each audience draft is reviewed and approved separately. A PM approving the Engineering draft does not approve the Sales draft — these are distinct communications with different implications.

**PII confirmation gate:**
If the draft contains financial figures above the configured threshold, or customer names above a configured sensitivity level, a pre-approval confirmation dialog is shown:
> "This draft includes '$1.2M ARR at risk'. Confirm this is appropriate to share with [Sales audience]?"

**Approval record:**
- PM ID, audience, approval timestamp
- PII confirmation flag (if triggered)
- Edit distance score per audience draft

---

## Workflow 5: Risk Alert Acknowledgment and Escalation

**Input:** Risk Watchdog P1/P2 alert + risk brief  
**Reviewer:** PM assigned to the affected product area  
**Decision required:** Acknowledge / Select mitigation / Escalate to engineering / Dismiss

**Acknowledgment:** Required within 2 hours for P1 alerts; clears the active alert indicator

**Mitigation selection:** PM reviews three mitigation options, selects one, records rationale

**Engineering escalation:** Requires a second confirmation ("Confirm escalation — this will create a Jira ticket in [Engineering Queue]")

**Dismissal:** PM can dismiss a risk alert as "false positive" — requires a reason. Dismissed alerts are logged (cannot be silently dismissed).

**Escalation record captured:**
- PM ID, timestamp, selected mitigation, escalation confirmed (Y/N), Jira ticket ID if created, dismissal reason if dismissed

---

## Approval Queue Priority Order

When multiple outputs arrive for PM review simultaneously, the inbox displays them in this priority order:

1. P1 Risk Alerts (red badge)
2. P2 Risk Alerts (orange badge)
3. Manually triggered outputs (PM initiated these; they're waiting on PM)
4. Prioritization briefs with Jira write pending (time-sensitive: backlog planning may be blocked)
5. PRDs awaiting approval for sharing
6. Opportunity briefs
7. Stakeholder communication drafts
8. Scheduled outputs (executive summaries, roadmap health reports)

PM can reorder manually; the default prioritization is a starting point.
