# Analytics Integration Specification

## Overview

Analytics integrations (Amplitude, Mixpanel, Heap) connect ProductPilot OS to quantitative behavioral evidence. They answer the question: "Do users actually use the features we're discussing?" Analytics data provides the behavioral grounding that prevents PMs from over-indexing on vocal minority signals.

Analytics integrations are read-only. ProductPilot never writes back to analytics systems.

---

## Supported Platforms

| Platform | Integration Method | Authentication |
|----------|------------------|----------------|
| Amplitude | REST API (Query API) | API Key + Secret Key |
| Mixpanel | REST API (JQL, Data Export) | Service Account OAuth |
| Heap | REST API (Data Layer API) | API Key |

---

## Amplitude Integration (Primary)

### Authentication
**Method:** HTTP Basic Auth with Amplitude API Key and Secret Key  
**Access:** Amplitude Query API (aggregated event data)

### Data Ingested

**Feature adoption metrics:**
```
Event: feature_used
  - event_type (name of the feature/action)
  - user_count_7d (unique users past 7 days)
  - user_count_28d (unique users past 28 days)
  - company_count (enterprise customers with usage)
  - adoption_rate (user_count / total_active_users)
```

**Workflow completion:**
```
Funnel data:
  - funnel_name (e.g., "Integration Setup")
  - step_completion_rates (array of % per step)
  - overall_conversion
  - avg_completion_time_seconds
```

**Retention signals:**
```
Retention data (by feature cohort):
  - feature_name
  - week_1_retention
  - week_4_retention
  - day_30_retention
```

### What Is NOT Ingested
- Individual user event streams — only aggregated, company-level or product-level statistics
- PII: user IDs, email addresses, personal identifiers — all analytics queries use anonymous group-by
- Raw event logs

**Key design constraint:** ProductPilot does NOT have access to individual user behavioral data. This is both a privacy choice and a practical one — PM-level intelligence works at the product area level, not the individual user level.

---

## How Analytics Data Enriches Briefs

Analytics data provides quantitative evidence for the Insight Synthesizer's business case section:

**Before analytics integration (qualitative only):**
"Multiple customers have requested an SSO/SCIM feature. Enterprise customers on Gong calls express strong interest."

**After analytics integration (quantitative grounding):**
"Multiple customers have requested an SSO/SCIM feature. Enterprise customers on Gong calls express strong interest. Behavioral data: 78% of enterprise users (≥100 seat accounts) access the Settings > Team page within the first 7 days, suggesting identity management is part of the initial setup workflow. However, only 23% complete the manual user invitation flow — a 77% abandonment rate at this step suggesting friction that SSO/SCIM would resolve."

### RICE Impact from Analytics

The Prioritization Agent uses analytics data to enrich RICE estimates:

**Reach:** Active users of the affected feature area (from Amplitude feature adoption data)

**Impact:** Adoption rate change expected (informed by similar feature cohort comparisons in the KG)

**Confidence:** If behavioral evidence aligns with qualitative signal, confidence component is strengthened

---

## Mixpanel Integration

**Authentication:** OAuth 2.0 Service Account  
**Primary use:** JQL (JavaScript Query Language) for more flexible behavioral analytics queries  
**Ingested:** Same structure as Amplitude — aggregated event metrics, funnel data, and retention by feature  
**No individual user data**

---

## Heap Integration

**Authentication:** API Key  
**Primary use:** Auto-captured behavioral events (no-code instrumentation); particularly useful for retroactive funnel analysis  
**Limitation:** Heap's data model is less structured; ProductPilot maps Heap's auto-captured events to product areas using admin-configured event name patterns

---

## Analytics Data Freshness

Analytics data is less real-time than Slack or Zendesk — daily sync is sufficient:

| Metric Type | Sync Frequency | Rationale |
|-------------|---------------|-----------|
| Feature adoption (7d/28d metrics) | Daily | Aggregated metrics don't require real-time |
| Funnel completion rates | Daily | Stable, batch computation by analytics platform |
| Retention cohorts | Weekly | Retention metrics are inherently weekly/monthly |

**No webhooks required** — analytics platforms expose REST query APIs; ProductPilot runs scheduled daily syncs via AWS Lambda cron jobs.

---

## Analytics + Knowledge Graph

Analytics data informs the knowledge graph:

**Feature entities:** When analytics data links a feature to an adoption metric, ProductPilot creates or updates the Feature entity in the KG with:
- `adoption_rate_7d`
- `adoption_rate_28d`
- `enterprise_adoption_rate` (if available)
- `funnel_completion_rate` (if funnel data available)

**Decision quality:** When a PM approves a prioritization decision backed by analytics evidence, and the outcome is recorded later (Feature adopted / Not adopted), the Decision Quality Score uses actual adoption data against the predicted adoption — improving future RICE calibration.

---

## Privacy and Data Governance

**No PII ingested:** All analytics queries explicitly exclude user-level identifiers. ProductPilot queries only aggregate and cohort-level data.

**Tenant isolation:** Each customer's analytics data is stored in a dedicated Pinecone namespace and labeled with `tenant_id` metadata. Analytics evidence from one customer is never surfaced in briefs for another customer.

**Data contract:** ProductPilot's Terms of Service explicitly prohibits customers from configuring analytics integrations in ways that would result in personal data ingestion. This is enforced at the query layer (admin configuration of event-level vs. aggregate-level access).

---

## Analytics Integration Health

**Connection test:** Query one aggregated metric from each connected platform; verify response  
**Daily sync monitoring:** Lambda function execution success/failure tracked in CloudWatch  
**Alert threshold:** If daily sync fails 3 consecutive times → alert admin; mark analytics data as stale in UI (confidence score Business Impact component capped at 10/20 if analytics data is >72 hours stale)
