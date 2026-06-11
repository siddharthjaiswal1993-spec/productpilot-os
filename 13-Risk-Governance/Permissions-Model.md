# Permissions Model

## Overview

The ProductPilot OS permissions model controls who can see what data, who can approve what actions, and who can configure what integrations. It is designed to satisfy enterprise security requirements while being simple enough for small PM teams to configure without dedicated IT support.

---

## Role Hierarchy

```
Organization Owner (Billing)
  └── Workspace Admin (Full configuration)
       ├── PM Full (All PM capabilities)
       │    └── PM Viewer (Read-only)
       └── Analyst (Reports and dashboards only)
```

---

## Role Definitions

### Organization Owner
**Who:** The person who created the account and holds the billing relationship  
**Unique capabilities:** Transfer ownership, cancel subscription, access billing  
**Inherits:** All Admin capabilities

### Workspace Admin
**Who:** IT admin, Head of Product, or PM lead  
**Capabilities:**
- Connect and configure integrations
- Manage team members (invite, remove, change roles)
- Configure signal channel access permissions
- Configure PII sensitivity level
- Configure audience profiles for Stakeholder Alignment
- Configure team OKRs
- Access audit logs (full tenant)
- Configure data retention periods

### PM Full
**Who:** Product managers who use ProductPilot OS day-to-day  
**Capabilities:**
- Access Signal Intelligence Hub (for signals in their configured product areas)
- Generate opportunity briefs
- Generate prioritization briefs
- Generate and edit PRDs
- Approve or reject all agent outputs
- Confirm Jira updates and stakeholder communications
- View and add to knowledge graph (their product area)
- View other PMs' approved decisions (if team sharing is enabled)

**Cannot do:**
- Connect or disconnect integrations
- Access other PMs' drafts without sharing enabled
- Access CRM ARR data without CRM permission grant

### PM Viewer
**Who:** Stakeholders who need to see product decisions but not create them (CS leads, Sales managers, Engineering leads)  
**Capabilities:**
- View approved briefs and decisions
- View roadmap health dashboard
- View risk alerts (view only — cannot acknowledge or escalate)
- View approved PRDs

**Cannot do:**
- Generate any agent output
- Approve or reject any draft
- See unapproved drafts
- Access signal feed

### Analyst
**Who:** Data or business analysts supporting the PM team  
**Capabilities:**
- View reports and dashboards
- Export approved data to CSV
- View decision quality metrics

**Cannot do:**
- Access signal feed
- Generate outputs
- Access raw signal content

---

## Attribute-Based Access Extensions

In addition to roles, access to specific data is governed by attributes:

### Signal Source Permissions
PMs are granted access to specific signal sources by an admin. A PM on the "Payments" team may have access to Slack channels in `#payments-feedback` but not `#enterprise-success`.

| Source | Default Access | Admin-Configurable |
|--------|---------------|-------------------|
| Slack channels | Opt-in per channel | Yes |
| Zendesk tickets | By product area tag | Yes |
| Gong calls | By account assignment | Yes |
| CRM data | Denied by default | Grant explicit permission |
| Confluence/Notion | All connected spaces | Can restrict by space |

### CRM Data Permission
ARR figures, deal values, and account health scores are sensitive commercial data. Access requires explicit admin grant.

- **Without CRM permission:** PM sees signal content but "[ARR: Permission required]" in citations
- **With CRM permission:** Full ARR figures visible in briefs and citations

### Cross-PM Brief Sharing
By default, a PM's drafted and approved briefs are private to them.

**Team sharing modes (admin-configurable):**
- `private`: Default — only the brief owner can see it
- `team`: All PM Full users in the workspace can see all approved briefs
- `area`: PMs can see briefs within their shared product area assignments

---

## Permission Check Architecture

**Server-side enforcement:** All permission checks happen server-side. The client never receives data it's not authorized to see — it's not a matter of hiding UI elements.

**JWT claims:** User role and permission grants are encoded in the JWT token (server-issued, immutable by client). Every API request validates the JWT claims before returning data.

**Attribute filter injection:** When a PM queries the signal feed, their permitted sources and product areas are injected as database-level filters — the query physically cannot return signals they're not permitted to see.

---

## Permission Audit Trail

Every permission grant and revocation is logged in the audit trail:
```json
{
  "event": "permission_granted",
  "target_user_id": "pm_alex_chen",
  "permission_type": "crm_data_access",
  "granted_by": "admin_sarah_kim",
  "granted_at": "2025-03-15T09:00:00Z",
  "scope": "all_accounts"
}
```

Permission history is queryable by admin for compliance audits.
