# Integration Architecture

## Overview

The Integration Layer is the component that connects ProductPilot OS to the 14+ signal sources in a PM's tech stack. It must handle: OAuth authentication, real-time webhooks, polling fallbacks, schema normalization, historical bulk loads, and graceful degradation when sources are unavailable.

The integration layer is not glamorous. It is the work that makes everything else possible. Poor integration architecture creates gaps in the signal corpus that silently reduce brief quality without surfacing clearly to the PM.

---

## Integration Patterns

### Pattern 1: Webhook + REST (Preferred)
**Sources:** Slack, Zendesk, Jira, Linear, Gong (transcript complete event)

**Flow:**
1. Admin connects integration via OAuth 2.0
2. ProductPilot registers webhook endpoint at source (Slack Events API, Zendesk webhook, Jira webhook)
3. Source delivers events in near-real-time
4. Webhook receiver validates payload signature
5. Signal processing pipeline queued

**Latency:** <5 minutes end-to-end

**Fallback:** If webhook fails to deliver for >15 minutes, polling fallback activates automatically

### Pattern 2: Polling (Secondary)
**Sources:** HubSpot (limited webhooks), Confluence, Notion, older API versions

**Flow:**
1. Admin connects integration via OAuth 2.0
2. Polling scheduler runs on configured interval (15–30 minutes)
3. REST API call fetches new/updated items since last poll timestamp
4. Incremental delta processing (only new/updated items ingested)

**Latency:** <30 minutes end-to-end

### Pattern 3: Bulk Historical Load
**Sources:** All sources support historical load at initial setup

**Flow:**
1. Admin triggers historical load from Settings > Integrations
2. Historical load job queued as background task (does not affect real-time performance)
3. Progress displayed to admin: "Indexing: 12,847 of ~45,000 documents"
4. Rate-limited to 50% of API quota to preserve real-time capacity

**Latency:** 24–48 hours for large historical loads

---

## OAuth 2.0 Integration Flow

```
Admin clicks "Connect [Source]"
         │
         ▼
ProductPilot redirects to source OAuth authorization page
         │
         ▼
Admin grants permissions (minimum required scopes)
         │
         ▼
Source redirects to ProductPilot callback URL with authorization code
         │
         ▼
ProductPilot exchanges code for access + refresh token
         │
         ▼
Tokens encrypted and stored in AWS Secrets Manager
(linked to tenant_id + source_type — never stored in application DB)
         │
         ▼
ProductPilot registers webhook endpoint at source
         │
         ▼
Historical load triggered (background)
         │
         ▼
Integration status: Active
```

---

## Integration Health Monitoring

Each integration has a health status updated in real-time:

| Status | Condition | PM-Visible |
|--------|-----------|-----------|
| Active | Webhook delivering; last event <15 min ago | Green indicator |
| Polling | Webhook failed; polling active; last poll <30 min ago | Yellow "Polling" badge |
| Delayed | Polling; last poll >30 min ago | Orange "Delayed" badge |
| Error | Active error; ingestion paused | Red "Error" badge |
| Stale | No events in >24 hours (may be correct for low-volume sources) | Gray "Stale" badge |
| Disconnected | OAuth deauthorized or token expired | Gray "Disconnected" |

**Integration health panel:** Available in Settings > Integrations for admin view. PMs see a simplified "Source Health" indicator on any evidence chunk from a non-Active integration.

---

## Integration Error Handling

**Webhook signature validation failure:**
- Log the failure
- Do not process the payload
- Alert if failures from the same source exceed 3 in 5 minutes (possible webhook injection attack)

**OAuth token expiry:**
- Attempt silent re-authentication using refresh token
- If refresh fails: set integration status to "Disconnected"; notify admin; continue serving from existing indexed data

**API rate limit (HTTP 429):**
- Exponential backoff: 15s, 30s, 60s, 120s
- After 4 retries: pause ingestion for this source for 1 hour; alert admin if sustained

**API schema change (parsing failure):**
- Log the payload parsing failure
- Set integration status to "Error"
- Alert admin: "Schema change detected in [source] integration — reconnection may be required"
- Continue serving from existing indexed data; do not index malformed payloads

---

## Minimum Required OAuth Scopes

Minimum-privilege scoping for each integration:

| Source | Required Scopes |
|--------|----------------|
| Slack | channels:read, channels:history (selected channels only), users:read |
| Zendesk | tickets:read, comments:read |
| Jira | read:jira-work |
| Gong | api:calls:read, api:calls:transcript:read |
| Salesforce | read (Objects: Account, Opportunity, Note, Activity) |
| HubSpot | crm.objects.contacts.read, crm.objects.deals.read, crm.objects.notes.read |
| Confluence | read:confluence-content.all |
| Notion | pages:read, databases:read |

**Principle:** Request only what is needed. If a scope is not required for signal intelligence, do not request it. This is visible to the admin during OAuth authorization and builds trust.

---

## Integration Testing

Each integration has an automated health check suite:

1. **Connection test:** Verify OAuth token is valid and API is reachable
2. **Permission test:** Verify required scopes are granted
3. **Payload test:** Fetch 1 recent item; verify it can be parsed with current schema
4. **Webhook test:** (For webhook-based sources) Trigger a test event and verify delivery

Admins can run the test suite on-demand from the integrations panel. Failed tests surface specific error messages for debugging.
