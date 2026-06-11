# Agent Orchestration

## Overview

ProductPilot OS runs 8 specialized agents coordinated by a central Orchestration Layer. The orchestration layer manages: which agents run when, what inputs they receive, how their outputs chain together, how conflicts between concurrent agents are resolved, and how HITL approval checkpoints are enforced. No agent runs autonomously without either PM trigger or system-defined schedule — and no agent writes to external systems without traversing a PM approval gate.

---

## Orchestration Architecture

```
┌─────────────────────────────────────────────────────────┐
│                   PM Interaction Layer                   │
│    (Approvals | Overrides | Manual Triggers | Inbox)     │
└──────────────────────┬──────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────┐
│                Orchestration Layer                        │
│  ┌────────────────────────────────────────────────────┐  │
│  │  Agent Router    │  State Manager  │  HITL Gates   │  │
│  └────────────────────────────────────────────────────┘  │
│  ┌────────────────────────────────────────────────────┐  │
│  │  Context Injector │  Output Validator │  Audit Log │  │
│  └────────────────────────────────────────────────────┘  │
└──────────────────────┬──────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────┐
│                    Agent Pool                             │
│  ┌─────────────┐  ┌──────────────┐  ┌────────────────┐  │
│  │   Insight   │  │ Prioritization│  │  PRD Copilot  │  │
│  │ Synthesizer │  │    Agent     │  │    Agent      │  │
│  └─────────────┘  └──────────────┘  └────────────────┘  │
│  ┌─────────────┐  ┌──────────────┐  ┌────────────────┐  │
│  │    Risk     │  │ Stakeholder  │  │   Meeting     │  │
│  │  Watchdog   │  │  Alignment   │  │ Intelligence  │  │
│  └─────────────┘  └──────────────┘  └────────────────┘  │
│  ┌─────────────┐  ┌──────────────┐                       │
│  │   Roadmap   │  │  Executive   │                       │
│  │ Intelligence│  │   Summary    │                       │
│  └─────────────┘  └──────────────┘                       │
└──────────────────────┬──────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────┐
│               Shared Infrastructure Layer                 │
│  RAG Pipeline  │  Knowledge Graph  │  Integration APIs   │
└─────────────────────────────────────────────────────────┘
```

---

## Agent Trigger Types

| Trigger Type | Description | Agents Using This |
|-------------|-------------|------------------|
| PM manual trigger | PM clicks a button in the UI | All agents |
| Automatic on approval | Fires after a PM approves a prior step | PRD Agent (after brief approval), Prioritization Agent (option to auto-queue) |
| Scheduled background | Runs on a schedule regardless of PM action | Risk Watchdog (every 4 hours), Roadmap Intelligence (weekly), Executive Summary (weekly) |
| Signal threshold | Fires when a signal volume threshold is crossed | Risk Watchdog (P1/P2 alerts), Roadmap Intelligence (unaddressed signals) |
| Event-driven | Fires on a specific event (meeting ends, transcript available) | Meeting Intelligence Agent |

---

## Agent Workflow: Signal → Brief → Prioritization → PRD → Stakeholder Alignment

This is the primary end-to-end workflow, triggered by a signal cluster.

```
1. PM reviews Signal Intelligence Hub → selects signal cluster → triggers "Generate Brief"
   ↓
2. Insight Synthesizer Agent runs
   - Input: signal cluster, retrieved evidence, CRM data, knowledge graph context
   - Output: Opportunity brief (draft)
   ↓
3. [HITL GATE #1] PM reviews and approves opportunity brief
   - PM can edit before approving
   - PM must click "Approve" to proceed
   - If rejected: agent logs rejection reason; cycle ends or PM requests regeneration
   ↓
4. PM selects "Score for Prioritization" (optional, or triggered on approval)
   ↓
5. Prioritization Agent runs
   - Input: approved brief, current backlog, OKRs
   - Output: Prioritization brief with RICE score and backlog comparison
   ↓
6. [HITL GATE #2] PM reviews and approves priority decision
   - PM can adjust RICE dimensions (override logged)
   - PM confirms backlog placement
   - If Jira update needed: staged for separate confirmation
   ↓
7. [HITL GATE #2b — Optional] PM confirms Jira priority update
   ↓
8. PM selects "Generate PRD" (optional)
   ↓
9. PRD Agent runs
   - Input: approved brief, PM template, supplementary evidence
   - Output: PRD draft with citations, user stories, acceptance criteria
   ↓
10. [HITL GATE #3] PM reviews and approves PRD
    - PM edits as needed
    - PM approves before PRD can be shared with engineering
    ↓
11. PM selects "Generate Stakeholder Updates"
    ↓
12. Stakeholder Alignment Agent runs (parallel for all audiences)
    - Input: approved priority decision, audience configs
    - Output: N audience-specific drafts
    ↓
13. [HITL GATE #4] PM reviews each stakeholder draft
    - PM approves each draft separately (or in batch with individual confirmation)
    - PM sends approved drafts
```

---

## Concurrent Execution

Multiple agents can run concurrently. The orchestration layer manages:

- **State isolation:** Each agent's working context is isolated. One agent's partial output cannot contaminate another's.
- **Shared resource contention:** If two agents simultaneously query the vector index, the query queue manages prioritization (PM-triggered queries take priority over background scheduled jobs).
- **Approval queue management:** If multiple agent outputs arrive simultaneously for PM review, they are queued in the PM inbox with priority order: Risk Watchdog alerts first, then manually-triggered agents, then scheduled agents.

---

## HITL Gate Specifications

| Gate | Position | What PM Must Do | What Happens if PM Declines |
|------|----------|-----------------|----------------------------|
| HITL-1 | After brief generation | Review and approve brief | Rejection logged; downstream agents blocked; PM can request regeneration |
| HITL-2 | After prioritization | Review and approve priority decision | Decision not recorded; Jira not updated; PRD not available |
| HITL-2b | After priority decision (if Jira write needed) | Confirm Jira ticket update | Jira not updated; decision is recorded in ProductPilot without Jira sync |
| HITL-3 | After PRD generation | Review and approve PRD | PRD not shareable; PM can edit and re-approve |
| HITL-4 | After stakeholder draft generation | Review and approve each communication | Communication not sent; PM can edit and re-approve per audience |
| HITL-5 | After risk brief (for escalation) | Confirm engineering escalation | Jira ticket not created; alert remains open |

---

## Error Recovery

| Error Scenario | Behavior |
|---------------|---------|
| Agent times out mid-workflow | Save partial progress; PM notified; PM can retry from last checkpoint |
| Integration unavailable (Jira, Slack) | Agent continues with reduced data; confidence score adjusted; PM notified of gap |
| LLM service unavailable | Queue agent run for retry; PM shown "Generation queued — typically completes in <2 minutes" |
| PM session expires during workflow | Workflow state saved; PM resumes from the approval queue on next login |
| Concurrent conflicting approvals | Surface conflict to both PMs; require explicit resolution |

---

## Audit Logging

Every orchestration event is logged with:
- `event_type`: agent_started, agent_completed, hitl_gate_triggered, pm_approved, pm_rejected, jira_write_staged, jira_write_confirmed, external_write_executed
- `agent_name`: Which agent
- `pm_id`: Which PM (for HITL events)
- `input_hash`: Hash of agent inputs
- `output_hash`: Hash of agent output
- `timestamp`: UTC millisecond precision
- `duration_ms`: Execution time

Audit logs are immutable. They cannot be edited or deleted by any user, including admins.
