# PII and Data Protection

## Scope

ProductPilot OS ingests signals from 14+ source systems that contain personal information: customer names in CRM notes, individual names in Gong call transcripts, email addresses in support tickets, and employee names in Slack messages. This document defines how PII is identified, handled, stored, and protected throughout the system.

---

## PII Categories in Context

| PII Type | Typical Source | Risk Level | Handling |
|----------|---------------|------------|---------|
| Customer/account names (companies) | All sources | Low (organizational, not personal) | Indexed normally; displayed in citations |
| Individual names (customers) | Gong, Zendesk, Intercom | Medium | Indexed; displayed in citations with source context |
| Individual names (employees, internal) | Slack, meeting transcripts | Low-Medium | Indexed; access scoped to PM's Slack permissions |
| Personal email addresses | Zendesk, Intercom, CRM | High | Detected, redacted in display; stored encrypted in PII store |
| Personal phone numbers | CRM notes, support tickets | High | Detected, redacted in display; stored encrypted in PII store |
| Individual financial information | CRM deal records | High | Deal values are company-level (low risk); individual compensation/financial data (high risk if present) |
| Health information | Support tickets (potential) | Critical | Detected and flagged for admin review; not indexed |

---

## PII Detection Pipeline

The PII scanner runs on every signal during ingestion and on every AI-generated output before delivery to PM.

**Detection methods:**
1. **Pattern matching:** Regex patterns for email addresses, phone numbers, credit card numbers, SSNs, URLs
2. **NER (Named Entity Recognition):** ML-based detection for personal names, locations, organizational entities
3. **Context-aware classification:** Determines if a detected entity is PII in context (a person's name in a customer call = PII; a product feature name = not PII)

**Configurable sensitivity levels:**

| Level | What Gets Redacted |
|-------|-------------------|
| Low | Email addresses, phone numbers, SSNs, financial account numbers only |
| Standard (default) | All of the above + personal names in non-business context |
| High | All of the above + individual company employee names + health-related terms |
| Custom | Admin-defined pattern list for organization-specific PII |

---

## PII Handling: Signals

When PII is detected in an ingested signal:

1. **Raw storage:** The full original signal is stored encrypted in the raw signal archive (separate from the indexed signal store)
2. **Indexed signal:** PII is replaced with a placeholder: "[email redacted]", "[phone redacted]", "[name redacted]"
3. **Vector index:** Only the redacted version is embedded and indexed — PII does not enter the vector index
4. **PII store:** The original PII is stored separately in an encrypted PII vault, linked to the signal ID

**Accessing original PII:**
- PMs with CRM data permissions can access the original PII for signals from CRM sources
- PMs with full signal access can access the original PII for their connected sources
- Access is logged in the audit trail
- PII vault has separate access controls from the main application

---

## PII Handling: Generated Outputs

Before any AI-generated output (brief, PRD, stakeholder communication) is delivered to the PM inbox, it passes through the PII scanner:

1. Scan for personal email addresses → redact
2. Scan for personal phone numbers → redact
3. Scan for financial figures above configurable threshold → flag for PM confirmation before sending in stakeholder communications
4. Scan for individual names in contexts where they shouldn't appear (e.g., a Slack message author's name appearing in a customer-facing communication draft) → redact

**Audience-specific PII rules:**
Each stakeholder audience configuration includes a PII access level. The Sales audience may see company names but not individual contact details. Engineering never sees customer financial details. Executive audience sees ARR ranges, not individual account values by default.

---

## GDPR Compliance

**Data subject rights supported:**

| Right | Implementation |
|-------|---------------|
| Right to access | Admin can export all data associated with a specific individual's name across all signals |
| Right to deletion | Admin can request deletion of all data associated with a specific individual; deletion propagates to vector index, PII store, and knowledge graph |
| Right to portability | Data export in machine-readable format (JSON) available for admin |
| Right to rectification | Admin can update incorrect PII records in the PII store |

**Lawful basis for processing:** Contract performance (processing organizational data to provide the service) for business data; legitimate interest for product intelligence signals; explicit consent not required for organizational data.

**Data Processing Agreement (DPA):** Available for all customers; required for enterprise customers; specifies controller (customer) vs. processor (ProductPilot OS) responsibilities.

---

## Data Minimization

ProductPilot OS indexes only data relevant to product intelligence. It does not:
- Index individual user behavior analytics data (clicks, page views, individual session data)
- Index HR systems (employee performance, compensation, employment records)
- Index personal calendars (meeting titles and attendees only, not personal calendar events)
- Index personal files or email inboxes

If an integration is configured with broader access than needed, a data minimization advisory is shown to the admin.

---

## Data Retention and Deletion

**Default retention periods (configurable):**

| Data Type | Default Retention | Minimum | Maximum |
|-----------|------------------|---------|---------|
| Signal content | 2 years | 90 days | 7 years |
| AI-generated outputs | 2 years | 90 days | 7 years |
| Decision records | 7 years | 3 years | 10 years |
| Audit logs | 7 years | 3 years | 10 years |
| PII vault | Follows source signal retention | Configurable | |

**On customer offboarding:**
1. 30-day notice period before data deletion begins
2. Customer can request full data export during notice period
3. After notice period: all signal content, generated outputs, and PII are deleted
4. Decision records with business impact may be retained for audit purposes per customer agreement
5. Deletion confirmed in writing to customer admin
