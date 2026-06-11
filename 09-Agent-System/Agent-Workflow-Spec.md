# Agent Workflow Specification

## Purpose

This document specifies each major agent workflow in ProductPilot OS V1: trigger conditions, input contracts, output contracts, HITL gate positions, error handling, and success criteria. It is the authoritative specification for engineering implementation of agent-driven workflows.

---

## Workflow 1: Signal Ingestion to Opportunity Brief

**Trigger:** PM clicks "Generate Brief" from a signal cluster of ≥2 signals  
**SLA:** Opportunity brief delivered to PM inbox within 90 seconds (p95)

### Input Contract
```json
{
  "workflow": "signal_to_brief",
  "trigger": {
    "pm_id": "string",
    "cluster_id": "string",
    "signal_ids": ["string"],
    "trigger_type": "manual"
  },
  "context": {
    "product_area": "string (optional)",
    "okr_ids": ["string"],
    "crmEnabled": boolean
  }
}
```

### Processing Steps
| Step | Agent/Service | Input | Output | Max Duration |
|------|--------------|-------|--------|-------------|
| 1 | Signal Intelligence Hub | cluster_id | Signal metadata list | 2s |
| 2 | RAG Pipeline | signal_ids + product_area | Top 8 evidence chunks | 10s |
| 3 | CRM Integration | account_names from signals | ARR + account data | 5s |
| 4 | Knowledge Graph | product_area + entities | Related features, prior decisions | 5s |
| 5 | Insight Synthesizer Agent | All above | Opportunity brief draft | 60s |
| 6 | Citation Validator | Brief draft | Validated brief | 5s |
| 7 | HITL Gate | Validated brief | PM inbox delivery | <1s |

### Output Contract
```json
{
  "brief_id": "string",
  "status": "pending_pm_approval",
  "confidence_score": {
    "composite": 0-100,
    "components": {
      "signal_volume": 0-20,
      "source_diversity": 0-20,
      "recency": 0-20,
      "business_impact_coverage": 0-20,
      "representativeness": 0-20
    }
  },
  "evidence_count": integer,
  "all_claims_cited": boolean,
  "generated_at": "ISO-8601 timestamp"
}
```

### HITL Gate
- Brief is delivered to PM inbox with status "Draft — Pending Approval"
- PM must click "Approve" before any downstream workflow can trigger
- Rejection reason captured as free text (required)
- Approval/rejection recorded in audit log

---

## Workflow 2: Brief to Prioritization

**Trigger:** PM approves opportunity brief and clicks "Score for Prioritization"  
**SLA:** Prioritization brief delivered within 60 seconds (p95)

### Input Contract
```json
{
  "workflow": "brief_to_prioritization",
  "brief_id": "string (approved)",
  "pm_id": "string",
  "include_backlog_comparison": boolean
}
```

### Processing Steps
| Step | Agent/Service | Input | Output | Max Duration |
|------|--------------|-------|--------|-------------|
| 1 | Knowledge Graph | brief.product_area | Historical RICE data | 5s |
| 2 | Jira Integration | tenant_id | Current backlog items | 10s |
| 3 | RAG Pipeline (per RICE dimension) | Dimension-specific queries | Supporting evidence | 15s |
| 4 | CRM Integration | account entities | ARR per customer segment | 5s |
| 5 | Prioritization Agent | All above | Prioritization brief | 30s |
| 6 | HITL Gate | Brief | PM decision view | <1s |

### HITL Gate
- PM reviews RICE breakdown, strategic alignment matrix, backlog comparison
- PM can override individual RICE dimensions (override logged with reason required)
- PM clicks "Approve Priority Decision" to record
- If Jira priority update needed: additional confirmation dialog before write

---

## Workflow 3: Approved Brief to PRD

**Trigger:** PM approves opportunity brief and clicks "Generate PRD"  
**SLA:** PRD draft delivered within 3 minutes (p95)

### Input Contract
```json
{
  "workflow": "brief_to_prd",
  "brief_id": "string (approved)",
  "pm_id": "string",
  "template_id": "string (optional, uses default if null)"
}
```

### Processing Steps
| Step | Agent/Service | Input | Output | Max Duration |
|------|--------------|-------|--------|-------------|
| 1 | Template Engine | template_id | PRD template structure | 2s |
| 2 | RAG Pipeline (×N sections, parallel) | Section-specific queries | Evidence per section | 30s |
| 3 | Knowledge Graph | product_area | Prior PRDs, edge cases | 10s |
| 4 | PRD Agent (plan phase) | Template + evidence coverage | PRD generation plan | 5s |
| 5 | PRD Agent (execute phase, parallel sections) | Per-section evidence + plan | Section drafts | 90s |
| 6 | PRD Agent (assemble phase) | Section drafts | Full PRD with citations | 20s |
| 7 | Citation Validator | PRD draft | Validated PRD | 10s |
| 8 | HITL Gate | Validated PRD | PM editing interface | <1s |

### HITL Gate
- PRD delivered to PM workspace as "AI Draft — Pending PM Review"
- PM edits in-place; all edits version-tracked
- PM clicks "Approve PRD" to enable sharing
- Sharing with engineering requires approval; unapproved PRD cannot be shared

---

## Workflow 4: Priority Decision to Stakeholder Alignment

**Trigger:** PM approves a priority decision and clicks "Generate Stakeholder Updates"  
**SLA:** All audience drafts delivered within 90 seconds (p95)

### Input Contract
```json
{
  "workflow": "decision_to_stakeholder_alignment",
  "decision_id": "string (approved priority decision)",
  "pm_id": "string",
  "audience_ids": ["string"],
  "include_evidence_footnotes": boolean
}
```

### Processing Steps
| Step | Agent/Service | Input | Output | Max Duration |
|------|--------------|-------|--------|-------------|
| 1 | Knowledge Graph | audience_ids | Audience profiles + prior communications | 5s |
| 2 | CRM Integration | decision.account_entities | Pipeline data for sales audience | 5s |
| 3 | Stakeholder Alignment Agent (parallel ×N audiences) | Decision + per-audience context | Audience-specific drafts | 60s |
| 4 | PII Screener | All drafts | PII-safe drafts | 5s |
| 5 | Contradiction Detector | All drafts as a set | Contradiction flags | 5s |
| 6 | HITL Gate | Drafts (×N) | PM communication queue | <1s |

### HITL Gate
- All drafts queued for individual PM review per audience
- Each draft clearly labeled "Draft — Pending PM Approval for [Audience]"
- PM approves each draft individually (or in batch with per-draft confirmation)
- No communication sent without explicit PM approval per audience

---

## Workflow 5: Risk Watchdog Monitoring (Scheduled)

**Trigger:** System schedule (every 4 hours)  
**SLA:** Alert delivered within 15 minutes of threshold breach (P1), 30 minutes (P2)

### Processing Steps
| Step | Agent/Service | Input | Output | Max Duration |
|------|--------------|-------|--------|-------------|
| 1 | Signal Intelligence Hub | tenant_id | Current volume time series | 10s |
| 2 | Risk Watchdog Agent | Volume data + baselines | Trend analysis | 30s |
| 3 | Knowledge Graph | Trending categories | Release correlation check | 10s |
| 4 | CRM Integration | Named accounts in signals | ARR + SLA data | 10s |
| 5 | Risk Watchdog Agent | All above | Risk brief + alert priority | 30s |
| 6 | Alert Engine | Priority classification | In-app notification | <5s |
| 7 | HITL Gate (if P1/P2) | Risk brief | PM notification center | <1s |

### HITL Gate
- PM notified immediately for P1/P2 alerts
- PM reviews risk brief; must click "Acknowledge" to clear the active alert indicator
- Engineering escalation requires explicit PM confirmation
- Customer communication requires explicit PM approval

---

## Workflow 6: Meeting Intelligence Processing

**Trigger:** PM uploads transcript or Gong/Zoom integration delivers completed transcript  
**SLA:** Processing results delivered within 5 minutes for transcripts up to 90 minutes

### Input Contract
```json
{
  "workflow": "meeting_intelligence",
  "meeting_id": "string",
  "transcript_source": "gong|zoom|fireflies|upload",
  "transcript_text": "string (or URL for large transcripts)",
  "metadata": {
    "meeting_title": "string",
    "attendees": [{"name": "string", "role": "string", "email": "string (optional)"}],
    "date": "ISO-8601",
    "duration_minutes": integer
  }
}
```

### HITL Gate
- Decision records: PM reviews extracted decisions and confirms each one before adding to decision log
- Action items: PM reviews before any external task assignment
- Signals: PM reviews before adding to Signal Intelligence Hub (or PM configures auto-add with 24-hour review window)
- Follow-up drafts: PM approves before sending

---

## Shared Workflow Requirements

1. **All workflows must log to immutable audit trail** — start, complete, HITL gate, PM approval/rejection, external write

2. **All workflows must handle partial failure gracefully** — if any step fails, save progress and surface clear error to PM with retry option

3. **All workflows must respect tenant isolation** — no cross-tenant data access is possible at any step

4. **All workflows must enforce HITL gates at specification positions** — no engineering shortcut that bypasses an approval gate is acceptable

5. **All workflows must deliver outputs labeled as "Draft"** until PM approval — no output appears as finalized without explicit PM confirmation
