# CRM Integration Specification

## Overview

CRM integrations (Salesforce and HubSpot) provide the business impact layer for ProductPilot OS. They answer the question: "Which customer accounts are associated with this signal, and what is the revenue implication?"

CRM data is some of the most sensitive data in the organization. Access is permission-gated and access-logged.

---

## Salesforce Integration

### Authentication
**Method:** OAuth 2.0 with Salesforce Connected App  
**Scopes:** `api` (read access to standard objects), `refresh_token`  
**Objects accessed:** Account, Opportunity, Note, ActivityHistory, Task

### Data Points Ingested

| Object | Fields Ingested | Purpose |
|--------|----------------|---------|
| Account | Name, ID, AnnualRevenue, Type, Industry, CustomerTier | Customer ARR and tier for confidence scoring |
| Opportunity | AccountId, Amount, StageName, CloseDate, IsWon, Description | Pipeline value for business impact calculation |
| Note | AccountId, Body, Title, CreatedDate, CreatedById | Deal notes with customer feedback for brief evidence |
| ActivityHistory | AccountId, Subject, Description, ActivityDate | Call notes, email threads for additional context |

### What Is NOT Ingested
- Contact personal information (email, phone, personal addresses) — only company-level data
- SFDC financial configurations
- Internal sales compensation data
- Lead scoring models

### CRM Data Access Permission
CRM data is gated behind the `crm_data` permission attribute. PMs without this permission see `[ARR: Permission required]` instead of actual account values.

**Admin configuration:** In Settings > Permissions > CRM Data Access, admin assigns the `crm_data` permission to specific PM roles or individuals.

### Entity Matching
Salesforce account names are the anchor for entity resolution across all signal sources. When a Zendesk ticket or Gong call mentions "Acme Corp," the entity is resolved against the Salesforce account name to attach the ARR and business context.

**Resolution algorithm:**
1. Exact match (case-insensitive)
2. Fuzzy match (≥85% Levenshtein similarity)
3. If no match: tag as "[Account: Unresolved]" for PM manual resolution

---

## HubSpot Integration

**Authentication:** OAuth 2.0 with HubSpot  
**Scopes:** `crm.objects.contacts.read`, `crm.objects.deals.read`, `crm.objects.notes.read`

**Data points ingested:**
- Company records (name, ARR/revenue field if mapped)
- Deal records (amount, stage, close date)
- Deal notes (free text notes associated with deals)

**HubSpot vs. Salesforce:** HubSpot customers typically have less structured CRM data (fewer required fields, more variability in deal notes format). The entity extraction pipeline handles HubSpot's less structured data format gracefully — defaulting to company name matching rather than formal account hierarchy traversal.

---

## CRM Data in Brief Generation

When the Insight Synthesizer Agent generates a brief, CRM data is incorporated:

**Business Impact section:**
"$2.1M ARR at risk — 5 enterprise accounts have identified SSO/SCIM as a deal blocker or expansion prerequisite:
- Acme Corp: $580K (renewal in 45 days)
- GlobalTech: $420K (expansion blocked)
- NovaSystems: $380K (new deal pending)
- PinePoint: $340K
- ClearPath: $380K"

This calculation comes from:
1. Entity resolution: 6 signals → 5 distinct accounts identified
2. CRM lookup: per-account ARR retrieved from Salesforce
3. Risk context: renewal date retrieved; SLA status checked

**Confidence impact:**
If CRM is unavailable or unconnected, the Business Impact Coverage component of the confidence score is capped at 5/20. The brief still generates but is labeled "Business impact data unavailable — CRM not connected or insufficient permissions."

---

## CRM Health Monitoring

**Polling interval:** 15 minutes (Salesforce), 15 minutes (HubSpot)  
**Token refresh:** Automatic; re-auth prompt if refresh token expires  
**Rate limits:** Salesforce: 100K API calls/day (standard); HubSpot: 100 requests/10 seconds

**Alert conditions:**
- OAuth token expiry without successful re-auth → PM notification, CRM data temporarily unavailable
- API quota at 80% → alert engineering; review query efficiency
- ARR data quality: if >20% of entities have null ARR → alert admin for CRM field mapping review
