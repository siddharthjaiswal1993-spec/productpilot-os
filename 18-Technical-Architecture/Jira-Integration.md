# Jira Integration Specification

## Overview

Jira serves a dual role in ProductPilot OS: it is both a signal source (bug reports, feature requests, engineering notes) and a write target (priority updates, escalation tickets). This dual role requires particular care in the integration design — read permissions are broad, write permissions are narrow and always PM-confirmed.

---

## Authentication

**Method:** OAuth 2.0 with Atlassian Marketplace  
**Scopes (read):** `read:jira-work` (issues, comments, projects)  
**Scopes (write):** `write:jira-work` (issue update — priority field only)  
**Not requested:** `manage:jira-configuration`, `delete:jira-work`, `manage:jira-project`

---

## Signal Ingestion from Jira

**What is ingested:**
- Issue descriptions and comments (feature requests, bug reports)
- Engineering notes in issue descriptions
- Labels and components (used for product area tagging)
- Reporter name and team attribution
- Issue priority and status (metadata, used for context)

**What is NOT ingested:**
- Internal Jira administration notes
- Sprint velocity or engineering metrics (not product intelligence signals)
- Time tracking data
- Attachment content

**Ingestion method:**
- Webhook: Jira issue events (issue_created, issue_updated, comment_added)
- Polling fallback: Jira REST API (`/rest/api/3/search`) every 5 minutes if webhook fails

**Project scoping:**
Admin selects which Jira projects to monitor from a picker. Not all projects are included by default. Recommended: product backlog projects, customer-facing issue projects. Excluded by default: HR ticketing, legal, internal IT.

---

## Entity Resolution from Jira

**Product area detection:** Jira labels, components, and epic names are mapped to ProductPilot product areas. Admin configures the mapping in Settings > Integrations > Jira > Product Area Mapping.

**Customer attribution:**
Jira issues often reference customers in the description or custom fields. NER extracts customer names and resolves them against CRM account names.

**Feature matching:**
Issue titles and labels are fuzzy-matched against known Feature entities in the knowledge graph. If a match is found (≥80% similarity), the signal is linked to the Feature entity.

---

## Jira as a Write Target

ProductPilot OS can write to Jira in two scenarios:

### Scenario 1: Priority Updates
When the Prioritization Agent recommends a priority change for a Jira issue, ProductPilot stages the change for PM confirmation.

**Fields written:** `priority` (the Jira priority level — Highest/High/Medium/Low/Lowest)  
**Gate:** Two confirmations: approve priority decision → confirm Jira write specifically  
**Not written:** Status, assignee, sprint, components, labels, description, comments

### Scenario 2: Engineering Escalation Tickets
When the Risk Watchdog recommends escalating to engineering, ProductPilot can create a new Jira ticket.

**Fields written on new ticket creation:**
- Summary: "[ProductPilot Risk Alert] [Category] — [Priority]"
- Description: Risk brief summary + affected accounts table + mitigation recommendation
- Priority: Set to P1 or P2 based on alert priority
- Labels: `productpilot-escalation`
- Reporter: The PM who confirmed the escalation

**Gate:** PM reviews the exact Jira ticket content before creation; confirms creation; ticket is created only after explicit confirmation

**Not written:** Sprint assignment, component assignment (these require engineering PM to assess capacity and context)

---

## Jira Integration Health

**Webhook:** Jira system webhooks (configured by admin in Jira Project Settings)  
**Polling fallback:** JQL-based REST API polling  
**Rate limit:** Jira Cloud: 100 requests/minute for read; 10 requests/minute for write  
**Historical load:** JQL query with date range; 90-day default

**Write rate limiting:**
Write operations (priority updates, ticket creation) are rate-limited to 10 operations per hour per tenant to prevent accidental bulk writes.

---

## Jira Audit Integration

Every Jira write is recorded in ProductPilot's immutable audit log with:
- Jira issue ID written to
- Field name and value written
- The PM who confirmed the write
- The brief/decision ID that justified the write
- Timestamp

This creates a complete, auditable chain: customer signal → brief → priority decision → Jira update.
