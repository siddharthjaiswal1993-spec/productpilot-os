# Audit Logs

## Purpose

The audit log is the accountability layer of ProductPilot OS. It records every agent action, every HITL gate event, every PM approval and rejection, every external write, and every admin configuration change — in an immutable, tamper-proof store.

The audit log answers the questions that matter most in enterprise product management:
- "Who approved this roadmap decision?"
- "What evidence was available when this priority was set?"
- "Did anyone review this communication before it was sent?"
- "Why did we change this Jira ticket priority?"

---

## Immutability Requirements

Audit log entries are append-only. No role — including admin, system administrator, and the engineering team — can modify or delete an audit log entry through any available interface.

**Implementation:** Write-once S3 storage with S3 Object Lock (Compliance mode, 7-year retention) for production. Each entry is hash-chained: each entry includes the hash of the previous entry, so tampering with any historical entry would invalidate all subsequent entries.

---

## Event Categories

### Category 1: Agent Invocation Events

| Event | Logged Fields |
|-------|--------------|
| Agent started | agent_name, input_hash, pm_id (if triggered by PM), timestamp |
| Agent completed | agent_name, output_hash, duration_ms, status (success/failure) |
| Agent failed | agent_name, error_type, error_message, failed_step, timestamp |
| Tool call made | agent_name, tool_name, input_summary, output_summary, latency_ms |

### Category 2: HITL Gate Events

| Event | Logged Fields |
|-------|--------------|
| Draft delivered to PM | item_id, item_type, pm_id, confidence_score, delivery_timestamp |
| PM approved | item_id, item_type, pm_id, approval_timestamp, edit_count, edit_distance_score |
| PM rejected | item_id, item_type, pm_id, rejection_timestamp, rejection_reason_category, rejection_reason_text |
| PM deferred | item_id, item_type, pm_id, deferral_timestamp |
| PM requested regeneration | item_id, item_type, pm_id, prior_rejection_count, additional_context_provided |

### Category 3: External Write Events

| Event | Logged Fields |
|-------|--------------|
| External write staged | item_id, write_type (jira_update/email/slack), target_system, payload_summary, staged_at, pm_id |
| External write confirmed | item_id, write_type, confirmed_by_pm_id, confirmed_at, actual_write_timestamp |
| External write cancelled | item_id, write_type, cancelled_by_pm_id, cancelled_at, cancellation_reason |
| External write failed | item_id, write_type, error_type, error_message, retry_count |

### Category 4: Decision Record Events

| Event | Logged Fields |
|-------|--------------|
| Decision created | decision_id, brief_id, decision_text_hash, pm_id, confidence_score, rice_score, timestamp |
| Decision with evidence snapshot | decision_id, evidence_snapshot_hash (immutable evidence list at decision time) |
| Override recorded | decision_id, override_type, original_value, new_value, override_reason, pm_id, timestamp |
| Decision outcome recorded | decision_id, outcome_type, outcome_value, pm_id, timestamp |

### Category 5: Admin Configuration Events

| Event | Logged Fields |
|-------|--------------|
| Integration connected | integration_type, connected_by, timestamp, scopes_granted |
| Integration disconnected | integration_type, disconnected_by, timestamp, reason |
| User permission change | affected_user_id, old_role, new_role, changed_by, timestamp |
| Signal channel enabled | source_type, channel_id, enabled_by, timestamp |
| Tenant configuration changed | config_key, old_value_hash, new_value_hash, changed_by, timestamp |

---

## Audit Log Access

| Role | Access Level |
|------|-------------|
| PM (Full) | Own actions only: their approvals, rejections, decisions |
| Admin | Full tenant audit log read access |
| Compliance officer (enterprise) | Full tenant audit log export capability |
| Engineering (ProductPilot team) | System-level logs (not customer data content); operational events |
| External auditor | Via enterprise audit export package; customer authorizes access |

**No role can delete or modify audit log entries.** Read access is separate from write access, and write access is only available to the append-write system process.

---

## Audit Log Retention

| Log Category | Default Retention | Enterprise Configurable |
|-------------|------------------|------------------------|
| HITL gate events | 7 years | 10 years max |
| Decision records | 7 years | 10 years max |
| Agent invocation logs | 1 year | 3 years max |
| Admin config events | 7 years | 10 years max |
| System error logs | 90 days | Not configurable |

---

## Decision Evidence Snapshot

For every PM-approved decision, the audit log stores an immutable evidence snapshot:

```json
{
  "event": "decision_evidence_snapshot",
  "decision_id": "dec_sso_scim_20250315",
  "snapshot_timestamp": "2025-03-15T14:32:11Z",
  "evidence_count": 8,
  "evidence_chunks": [
    {
      "chunk_id": "chunk_zendesk_12847_01",
      "source_type": "Zendesk",
      "source_id": "ticket_12847",
      "author": "Aisha Johnson",
      "date": "2025-03-01",
      "excerpt_hash": "sha256_of_excerpt",
      "relevance_score": 0.94
    }
    // ... 7 more chunks
  ],
  "confidence_score": 87,
  "rice_score": 11963,
  "snapshot_hash": "sha256_of_full_snapshot"
}
```

This snapshot preserves exactly what evidence was available when the PM made the decision. Even if source signals are later updated or deleted, the evidence-at-decision-time is permanently recorded.

---

## Audit Log Export

Enterprise customers can export their full audit log:
- **Format:** JSON Lines (one event per line), or CSV for tabular event categories
- **Authentication:** Admin-authorized export; generates time-limited signed download URL
- **Scope:** Per date range, per event category, or full export
- **Integration:** Audit logs can be streamed to customer's SIEM system (Splunk, Datadog, Elastic) via webhook
