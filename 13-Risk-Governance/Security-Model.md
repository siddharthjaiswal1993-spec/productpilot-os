# Security Model

## Overview

ProductPilot OS handles sensitive organizational data: customer names, deal values, internal communications, and strategic product decisions. The security model is designed around the principle that security failures in a product intelligence tool can be catastrophic for customer trust — far more so than in a generic productivity tool.

---

## Authentication

**Protocol:** OAuth 2.0 with PKCE for all user-facing authentication  
**Token TTL:** Access tokens expire after 1 hour; refresh tokens expire after 30 days  
**Re-authentication:** Required after 30-day refresh token expiry; silent re-auth attempted first  
**MFA:** Required for all admin roles; optional (strongly recommended) for all users  
**SSO support:** SAML 2.0 and OIDC for enterprise customers (V2 target)

---

## Authorization Model

**Role-based access control (RBAC) with three core roles:**

| Role | Capabilities |
|------|-------------|
| Admin | Full configuration access; integration management; user management; audit log access |
| PM (Full) | All PM features; brief generation; approvals; integrations already configured |
| Viewer | Read-only access to briefs and reports; cannot generate, approve, or modify |

**Attribute-based access control (ABAC) extensions:**

- **CRM data access:** PM must have CRM data permission to see ARR figures in briefs
- **Slack channel access:** PM sees signals only from channels they have been granted access to
- **Cross-PM brief access:** By default, PMs see only their own briefs; team sharing is opt-in

---

## Data Encryption

| State | Encryption |
|-------|-----------|
| Data in transit | TLS 1.3 for all API communication; no HTTP fallback |
| Data at rest (database) | AES-256 encryption for all stored data |
| Data at rest (vector index) | Encrypted at-rest storage in Pinecone (or equivalent) |
| PII fields | Double encryption: encrypted at rest + separately encrypted in PII store |
| Audit logs | Immutable + encrypted at rest |

---

## Tenant Isolation

Tenant isolation is the most critical security property for a multi-tenant SaaS handling sensitive organizational data.

**Isolation at each layer:**

| Layer | Isolation Mechanism |
|-------|-------------------|
| Vector database | Dedicated namespace per tenant; query-time `tenant_id` filter enforced at DB layer |
| Knowledge graph | Dedicated subgraph per tenant; cross-tenant entity queries blocked at DB layer |
| Application layer | All API routes validate `tenant_id` from JWT; server-side enforcement |
| Storage | Separate S3 prefixes per tenant; IAM role boundaries |
| LLM calls | Tenant context is never included in shared prompt cache; each tenant's context is isolated |

**Automated isolation testing:**  
Monthly automated tests verify that a query from Tenant A returns zero results from Tenant B's namespace across all storage layers. Any failure is treated as a P0 security incident.

---

## Integration Security

**Credential storage:** All integration tokens (OAuth tokens, API keys) are stored in a secrets manager (AWS Secrets Manager or Vault). They are never stored in the application database.

**Minimum-privilege scopes:** Each integration requests only the minimum required OAuth scopes. Slack integration requests only `channels:read`, `messages:read` for configured channels — not `admin` or `files:read`.

**Token rotation:** Integration tokens are rotated on configurable schedules. Expired tokens trigger re-authentication prompts, not failures.

**Webhook signature validation:** All inbound webhooks are validated against source system signatures (Slack signing secret, Jira webhook secret, etc.) before processing. Unsigned webhooks are rejected.

---

## Audit and Incident Response

**Audit log:** All HITL gate events, agent invocations, external writes, and admin actions are logged in an immutable, append-only audit store. Logs cannot be modified or deleted by any role including admin.

**Intrusion detection:** Unusual access patterns (high query volumes from new IPs, access from non-whitelisted locations for enterprise customers) trigger security alerts.

**Incident response:**
| Severity | First Response | Notification |
|----------|---------------|-------------|
| P0 (data breach) | <1 hour | Affected customers within 24 hours; regulatory notifications as required |
| P1 (security vulnerability) | <4 hours | Internal; customer if exploitation is possible |
| P2 (access control gap) | <24 hours | Internal |

---

## Compliance Readiness

| Standard | Status |
|----------|--------|
| SOC 2 Type II | In progress for V2 launch; design-partner customers see audit evidence package |
| GDPR | Privacy controls and data subject request workflows built from Day 1 |
| HIPAA | Architecture supports HIPAA requirements (BAA available for healthcare customers on enterprise tier) |
| CCPA | Data subject rights (deletion, portability) supported |
