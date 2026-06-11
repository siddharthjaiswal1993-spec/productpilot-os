# Data Sources

## Overview

ProductPilot OS ingests signals from 14 primary data source categories. These sources collectively represent the full surface area of product intelligence available to a modern PM team. The integration depth varies by source category: some provide real-time webhooks, some require polling, some support historical bulk loads.

---

## Source Category Definitions

### Category 1: Customer Success Signals
**Sources:** Zendesk, Intercom, Freshdesk, Help Scout  
**Signal types:** Feature requests, bug reports, churn signals, product friction, NPS follow-up  
**Integration method:** Webhook (new tickets/conversations) + REST API (bulk historical load)  
**Ingestion latency:** <5 minutes for new tickets  
**Key entities:** customer_name, ticket_id, priority, status, product_area, assigned_agent

### Category 2: Sales Intelligence
**Sources:** Salesforce, HubSpot  
**Signal types:** Deal notes with feature asks, deal blocker flags, win/loss reason codes, renewal risk notes  
**Integration method:** OAuth2 → REST API polling (every 15 minutes) + Salesforce Platform Events webhook  
**Ingestion latency:** <15 minutes  
**Key entities:** account_name, opportunity_value, close_date, deal_stage, owner, competitor_mentions

### Category 3: Conversation Intelligence
**Sources:** Gong, Chorus, Zoom Recordings, Fireflies  
**Signal types:** Customer call transcripts, discovery call notes, QBR recordings, product demo feedback  
**Integration method:** Webhook (transcript complete) + REST API (historical)  
**Ingestion latency:** <30 minutes after call ends (transcript processing time)  
**Key entities:** participants, call_date, call_type, account_name, key_moments

### Category 4: Team Communication
**Sources:** Slack  
**Signal types:** Product channel discussions, customer escalations, feature request threads, roadmap debates  
**Integration method:** Slack Events API (real-time) + Slack Export API (historical)  
**Ingestion latency:** <15 minutes for new messages  
**Key entities:** channel_name, author, thread_ts, mentions, reactions
**Channel filtering:** Only channels where the Signal Intelligence integration is explicitly enabled — no default monitoring

### Category 5: Project and Issue Tracking
**Sources:** Jira, Linear, GitHub Issues  
**Signal types:** Bug reports, feature requests in backlog, engineering notes, sprint outcomes  
**Integration method:** Webhook (issue events) + REST API (backlog bulk load)  
**Ingestion latency:** <5 minutes for new/updated issues  
**Key entities:** issue_id, issue_type, priority, assignee, sprint, labels, resolution_status

### Category 6: Documentation
**Sources:** Confluence, Notion, Google Docs (read-only)  
**Signal types:** Product specifications, design docs, decision memos, retrospective documents  
**Integration method:** REST API polling (daily for Confluence, Notion webhook for new/updated pages)  
**Ingestion latency:** <24 hours for new documents, daily refresh  
**Key entities:** page_id, author, space_name, last_modified, parent_page

### Category 7: Product Analytics
**Sources:** Amplitude, Mixpanel, Heap, PostHog  
**Integration method:** REST API (aggregate metrics export) — individual user event data is not ingested  
**Signal types:** Feature adoption rates, funnel drop-off points, engagement trends, power user behavior  
**Key entities:** event_name, metric_type, date_range, cohort, trend_value
**Privacy note:** Only aggregate, anonymized metrics are ingested. No individual user event streams.

### Category 8: Customer Research
**Sources:** User interview transcripts (uploaded), survey exports (CSV), research repositories  
**Integration method:** Manual PM upload + file processing  
**Signal types:** Usability findings, JTBD research, customer discovery notes  
**Ingestion latency:** <10 minutes after upload

### Category 9: PM-Generated Documents
**Sources:** Uploaded PRDs, roadmap documents, spec sheets, strategy memos  
**Integration method:** Manual PM upload + file processing  
**Signal types:** Prior decisions, feature scope definitions, roadmap items  
**Ingestion latency:** <10 minutes after upload

---

## Source Quality Tiers

Not all sources are equally reliable. The confidence scoring system weights evidence by source quality tier:

| Tier | Sources | Evidence Reliability |
|------|---------|---------------------|
| Tier 1 (Highest) | Gong call transcripts, Zendesk tickets with replies, CRM opportunity notes | Direct customer voice, attributed, timestamped |
| Tier 2 (High) | Slack messages from CS/Sales channels, Jira feature requests with customer context, Intercom conversations | Attributed, timestamped, but second-hand customer voice |
| Tier 3 (Medium) | Confluence/Notion documentation, internal Slack discussions, GitHub issue descriptions | Organizational knowledge, may be outdated |
| Tier 4 (Contextual) | Amplitude/Mixpanel aggregate metrics, product usage reports | Behavioral data, aggregate only |

---

## Source Privacy and Access Control

### Slack Channel Scoping
Slack is the highest-risk source for privacy violations — it contains informal employee communications not intended for AI analysis. ProductPilot OS only ingests Slack data from channels where a Signal Intelligence integration is explicitly activated by an admin. Default is zero Slack monitoring.

### CRM Data Access
CRM data (ARR, pipeline, account health) is gated behind an additional permission layer. PM roles that don't have CRM access in the organization's settings will not see ARR figures in generated briefs — they see "[ARR: Permission required]" instead.

### Document Access Parity
The Signal Intelligence Hub only indexes documents that the connected integration account has read access to. If the Notion integration is connected with a service account that has access to 80% of workspaces, only those 80% are indexed. No elevation of privilege above the connected account.

---

## Source Health Monitoring

Each connected source has a health status visible in the admin dashboard:
- **Active:** Webhooks delivering, no errors in last 24 hours
- **Polling:** Webhook failed; system fell back to polling; latency degraded
- **Stale:** No new data in past 24 hours (may indicate integration issue)
- **Error:** Active error; data ingestion paused; admin action required
- **Disconnected:** Integration deauthorized; historical data preserved in index; no new data

PMs see a source health indicator on any evidence chunk sourced from a non-Active integration.
