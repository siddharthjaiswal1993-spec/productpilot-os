# API Design

## API Philosophy

ProductPilot OS's API is designed for the internal frontend first, and for external integrators second (V2+). Every endpoint reflects the PM workflow: triggering agent actions, approving outputs, querying intelligence, and managing integrations.

---

## Authentication

All API requests require a valid JWT bearer token:
```
Authorization: Bearer <jwt_token>
```

JWTs are issued by the auth service on login and contain:
- `sub`: PM user ID
- `tenant_id`: Tenant identifier
- `role`: PM role (admin/pm_full/viewer/analyst)
- `permissions`: Granted attribute-based permissions (crm_data, etc.)
- `exp`: Expiry timestamp (1-hour TTL)

Refresh tokens (30-day TTL) are stored in httpOnly cookies for browser clients.

---

## Core API Endpoints

### Signal Intelligence

```
GET    /v1/signals                      # List signals with filters
GET    /v1/signals/:signal_id           # Get signal detail + metadata
GET    /v1/signals/clusters             # Get detected clusters
POST   /v1/signals                      # Submit a signal manually
GET    /v1/signals/trends               # Get volume trend data

Query params for /v1/signals:
  source_type: slack|zendesk|gong|...
  signal_type: feature_request|bug_report|...
  date_from: ISO-8601
  date_to: ISO-8601
  product_area: string
  page: integer
  limit: integer (max 100)
```

### Opportunity Briefs

```
POST   /v1/briefs                       # Trigger brief generation
GET    /v1/briefs                       # List briefs (filtered by status, date, PM)
GET    /v1/briefs/:brief_id             # Get brief detail
PATCH  /v1/briefs/:brief_id             # Update brief fields (edit)
POST   /v1/briefs/:brief_id/approve     # Approve brief (HITL gate)
POST   /v1/briefs/:brief_id/reject      # Reject brief (requires reason)
GET    /v1/briefs/:brief_id/citations   # Get all citations for a brief
```

### Prioritization

```
POST   /v1/prioritization               # Generate prioritization brief from approved brief
GET    /v1/prioritization/:pri_id       # Get prioritization brief detail
PATCH  /v1/prioritization/:pri_id       # Override a RICE dimension (requires reason)
POST   /v1/prioritization/:pri_id/approve   # Approve priority decision
POST   /v1/prioritization/:pri_id/jira-stage    # Stage Jira priority update
POST   /v1/prioritization/:pri_id/jira-confirm  # Confirm Jira write (second gate)
```

### PRD

```
POST   /v1/prd                          # Generate PRD from approved brief
GET    /v1/prd/:prd_id                  # Get PRD detail
PATCH  /v1/prd/:prd_id                  # Edit PRD section
POST   /v1/prd/:prd_id/sections/:section/review   # Mark section as reviewed
POST   /v1/prd/:prd_id/approve          # Approve PRD for sharing
GET    /v1/prd/:prd_id/share-link       # Generate shareable link (post-approval only)
```

### Risk

```
GET    /v1/risk/alerts                  # List active risk alerts
GET    /v1/risk/alerts/:alert_id        # Get risk brief detail
POST   /v1/risk/alerts/:alert_id/acknowledge   # Acknowledge alert
POST   /v1/risk/alerts/:alert_id/dismiss       # Dismiss alert (requires reason)
POST   /v1/risk/alerts/:alert_id/escalate      # Escalate (stages Jira ticket)
POST   /v1/risk/alerts/:alert_id/escalate/confirm  # Confirm escalation (second gate)
```

### Stakeholder Communications

```
POST   /v1/communications               # Generate stakeholder drafts
GET    /v1/communications/:comm_id      # List drafts for a decision
GET    /v1/communications/:comm_id/:audience_id   # Get specific audience draft
PATCH  /v1/communications/:comm_id/:audience_id   # Edit draft
POST   /v1/communications/:comm_id/:audience_id/approve   # Approve this audience's draft
```

### Knowledge Graph

```
GET    /v1/knowledge/entities           # List entities (with type filter)
GET    /v1/knowledge/entities/:entity_id    # Get entity detail with relationships
GET    /v1/knowledge/decisions          # List decision history
GET    /v1/knowledge/decisions/:decision_id # Get decision detail with evidence snapshot
POST   /v1/knowledge/decisions/:decision_id/outcome  # Record decision outcome
GET    /v1/knowledge/search             # Text search across knowledge graph
```

### Integrations

```
GET    /v1/integrations                 # List connected integrations with health status
POST   /v1/integrations/:source/connect # Initiate OAuth flow
DELETE /v1/integrations/:source         # Disconnect integration
POST   /v1/integrations/:source/test    # Run integration health check
GET    /v1/integrations/:source/sync-status   # Get historical load progress
```

---

## Response Format

All responses follow a consistent envelope format:

```json
{
  "data": { ... },
  "meta": {
    "request_id": "req_abc123",
    "timestamp": "2025-03-15T14:32:11Z",
    "tenant_id": "tenant_abc"
  },
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 247,
    "has_more": true
  }
}
```

Error responses:
```json
{
  "error": {
    "code": "INSUFFICIENT_PERMISSIONS",
    "message": "This action requires CRM data access permission",
    "request_id": "req_abc123"
  }
}
```

---

## WebSocket Events (Real-Time)

The frontend subscribes to a WebSocket connection for real-time notifications:

```
Connection: wss://api.productpilot.ai/v1/ws?token=<jwt>

Events (server → client):
  brief.generation.started   { brief_id, progress: 0 }
  brief.generation.progress  { brief_id, step, progress: 0-100 }
  brief.ready                { brief_id, confidence_score }
  risk.alert.new             { alert_id, priority, category }
  approval.reminder          { item_id, item_type, age_hours }
  integration.status_changed { source_type, new_status }
```

---

## Rate Limits

| Endpoint Group | Rate Limit | Window |
|---------------|-----------|--------|
| Agent triggers (brief, PRD, prioritization) | 20 requests | Per PM per hour |
| Signal queries | 100 requests | Per PM per minute |
| Knowledge graph queries | 200 requests | Per PM per minute |
| Integration management | 10 requests | Per admin per minute |
| WebSocket connections | 5 concurrent | Per PM |

Rate limit headers are included in all responses:
```
X-RateLimit-Limit: 20
X-RateLimit-Remaining: 15
X-RateLimit-Reset: 1710509531
```
