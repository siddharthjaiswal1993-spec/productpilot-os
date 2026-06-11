# Support Integration Specification

## Overview

Support platforms (Zendesk and Intercom) are the highest-volume source of direct customer voice in ProductPilot OS. Customer support tickets, feedback, and conversations represent real user experiences with real product problems — they are the most credible signal of user pain.

Support integrations are read-only. ProductPilot never writes back to support tickets.

---

## Zendesk Integration

### Authentication
**Method:** OAuth 2.0 with Zendesk  
**Scopes:** `read` (tickets, users, organizations — read-only)  
**Not requested:** `write`, `tickets:write`, `users:write`

### Objects Ingested

| Object | Fields Ingested | Signal Value |
|--------|----------------|-------------|
| Ticket | Subject, description, status, priority, tags, created_at | Core customer complaint or request |
| Ticket Comment | Body, author_type (agent vs customer), created_at | Full conversation context |
| Organization | Name (matched to CRM account) | Customer account attribution |
| Tag | All tags | Product area classification |

**What is NOT ingested:**
- Agent email addresses
- Customer email addresses or phone numbers
- Internal notes marked "private" — private comments are excluded by default (admin can enable for support analysis use case)
- Satisfaction survey scores (CSAT) — too aggregated for signal-level use; may be added as KPI feed in V2

### Ticket Scoping
By default, ProductPilot ingests all customer-facing ticket types. Admin can restrict to specific Zendesk ticket groups or forms (useful for limiting to product feedback tickets, excluding billing disputes).

**Recommended tag monitoring:** Admin configures a list of product-relevant tags (e.g., `feature-request`, `bug`, `performance`, `integration`, `api`). Tickets without monitored tags are still ingested but receive lower priority in clustering.

### Zendesk-Specific Chunking

**Strategy:** Conversation-turn chunking  
**Unit:** Individual comment in a ticket thread  
**Max size:** 600 tokens  
**Context linking:** Each comment chunk includes ticket subject and the first customer message as context metadata (not as separate chunks)

This means that a 10-comment ticket generates 10 chunks, each individually searchable but all linked via the `ticket_id` in metadata. The Retrieval Strategy uses metadata filters to prevent multiple comments from the same ticket appearing as separate evidence items in a single brief (deduplication by `source_ticket_id`).

---

## Intercom Integration

### Authentication
**Method:** OAuth 2.0 with Intercom  
**Scopes:** `conversations.read`, `contacts.read` (for company attribution only)

### Objects Ingested
- Conversations: customer-initiated messages and support replies
- Conversation tags: used for product area classification
- Company names (from contacts — used for entity resolution, not storing personal contact data)

**Intercom note:** Intercom conversations tend to be shorter and more real-time than Zendesk tickets. They often represent lighter-weight requests (questions, small bugs). Chunking uses individual message-level strategy with a 300-token max per chunk.

---

## Signal Enrichment from Support Data

Support tickets contribute to the confidence score through two components:

**Signal Volume (0–20):** Each unique customer ticket or conversation is counted as one signal. Duplicate submissions from the same customer about the same topic are deduplicated — only the most recent conversation is counted.

**Source Diversity (0–20):** Support signals count as a distinct source category. An opportunity brief citing Zendesk tickets + Slack feedback + Gong calls + Jira reports achieves full source diversity.

### Business Impact from Support
Support data provides a proxy for business impact when CRM is not available:
- Number of paying customers reporting the same issue
- Tier tags on Zendesk organizations (if present — Enterprise/Mid-market/SMB)
- Escalation flags on tickets

---

## Historical Load

**Default window:** 90 days of historical tickets  
**Extended historical load:** Up to 24 months available for enterprise customers at onboarding (requires admin approval and increases index build time)

**Incremental sync:** Webhook-based for new tickets and new comments; polling fallback (every 10 minutes via Zendesk API) if webhook fails.

---

## Privacy Considerations

**Email redaction:** Customer email addresses that appear in ticket bodies are detected and redacted before indexing. PII scan runs on all support content before embedding.

**Private tickets:** Tickets marked as sensitive or VIP in Zendesk are excluded by default. Admin can configure exception rules.

**Deleted ticket handling:** If a Zendesk ticket is deleted and ProductPilot detects the deletion via webhook, the corresponding chunks are removed from the vector index. If the ticket was already cited in an approved brief, the citation is marked "[Ticket removed by support team]" — the brief is not retroactively altered.

---

## Support Health Monitoring

**API version:** Zendesk API v2, Intercom REST API v2.10+  
**Rate limits:** Zendesk: 700 requests/minute; Intercom: 83 requests/10 seconds  
**Health check:** Test API connection every 15 minutes; alert admin on consecutive failures >30 minutes
