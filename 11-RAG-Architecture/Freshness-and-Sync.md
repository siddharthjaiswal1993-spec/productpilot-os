# Freshness and Sync Strategy

## The Freshness Problem

A product intelligence system is only as useful as its data is current. A brief generated from signals that are 6 months old may recommend priorities that are already addressed, or miss an urgent emerging need that appeared last week.

Freshness is a first-class design requirement in ProductPilot OS — not an afterthought.

---

## Sync Architecture by Source Type

### Real-Time (Target: <15 minutes)

**Webhook-based sources:** Slack, Zendesk, Intercom, Jira, Linear, Salesforce Platform Events, Gong (transcript complete event)

When a new message/ticket/issue/transcript is created:
1. Source system delivers webhook to ProductPilot's webhook receiver
2. Signal processing pipeline classifies, extracts entities, deduplicates, PII-scans
3. Embedding generated and written to vector index
4. Knowledge graph entities updated
5. Signal available in Signal Intelligence Hub

**Latency breakdown:**
- Webhook delivery: <1 second (source system dependent)
- Classification + entity extraction: 5–10 seconds
- Embedding generation: 2–5 seconds
- Index write: <1 second
- **Total end-to-end: <15 minutes (SLA)**

### Near-Real-Time (Target: <1 hour)

**Polling-based sources:** HubSpot, Confluence (no webhook), Notion (limited webhooks), Zoom recordings (processing delay)

Polling schedule: Every 15 minutes for HubSpot; every 30 minutes for Confluence/Notion.

### Daily (Target: <24 hours)

**Document-based sources:** Product analytics exports (Amplitude, Mixpanel), uploaded research documents, Confluence content updates

Daily batch job runs at 2am tenant-local time to process document updates.

---

## Freshness Metadata

Every chunk in the vector index includes:
- `ingestion_timestamp`: When ProductPilot received and indexed the signal
- `source_created_timestamp`: When the original document was created in the source system (may differ from ingestion time for bulk loads)
- `source_modified_timestamp`: When the document was last modified in the source system (for updating)

**Displayed freshness:**  
In the citation panel, PMs see "Source date: March 1, 2025" (the `source_created_timestamp`), not the ingestion timestamp. The source date is what matters for recency assessment.

---

## Update Handling

### Updated Documents
When a document (Confluence page, Notion page, Jira issue) is modified:
1. Source system delivers webhook or polling detects the modification
2. Existing chunks for this document are marked as `deprecated`
3. New chunks are generated from the updated document
4. New chunks are written to the vector index; old chunks remain but are deprioritized
5. In the citation panel, deprecated chunks are displayed with a warning: "This source was updated on [date] — this citation refers to the version before the update."

### Deleted Documents
When a document is deleted from the source system:
1. Corresponding chunks are marked as `deleted` in the vector index
2. Deleted chunks are excluded from retrieval
3. If a deleted chunk was cited in an approved brief, the citation remains in the brief's evidence snapshot (immutable record) but is marked "[Source deleted from [source_system]]"

---

## Webhook Failure Recovery

Webhooks fail. Source system maintenance windows, network errors, and rate limits all cause gaps in real-time data. The system handles this with a polling fallback:

```
Webhook health check: every 5 minutes
If webhook_last_delivered > 15 minutes ago:
  - Activate polling fallback for this source
  - Poll source API every 5 minutes for new content
  - PM notified: "Slack connection: polling mode (webhook delayed)"
  - Continue until webhook resumes
```

**Polling vs. webhook latency:**
- Webhook: <5 minutes end-to-end
- Polling fallback: <10 minutes end-to-end (worst case: just missed a polling cycle)

The SLA degrades during polling mode but the system remains functional.

---

## Historical Data Loads

For new customers, historical data is loaded in bulk:

**Recommended historical window:** 12 months (provides baseline volume history for Risk Watchdog)  
**Maximum supported:** 36 months (per tenant, performance-tested)

**Bulk load process:**
1. Admin connects integration with expanded API scopes
2. Historical load job queued (background, does not affect real-time performance)
3. Progress displayed to admin: "Indexing: 12,847 of ~45,000 documents complete"
4. First usable results available within 2 hours of large load start
5. Full indexing completes within 24–48 hours for 36-month loads
6. PM notified: "Historical indexing complete — Signal Intelligence Hub now includes full 12-month history"

---

## Freshness Indicators in UI

Signals and evidence chunks display freshness context to help PMs assess relevance:

| Age | Display |
|-----|---------|
| 0–7 days | Green "Recent" badge |
| 8–30 days | No badge (normal) |
| 31–90 days | No badge (normal) |
| 91–180 days | Yellow "Historical" badge with date |
| >180 days | Orange "Historical" badge with date; tooltip: "This evidence may not reflect current state" |

These badges are visible in the evidence table of every brief and in the citation panel.

---

## Freshness in Confidence Scoring

The Confidence Engine's Recency component directly reflects freshness:

- Most signals within 7 days: 20/20 points
- Most signals 8–30 days: 15/20 points  
- Most signals 31–90 days: 10/20 points
- Most signals >90 days: 5/20 points

A PM opening a brief with a low recency score immediately sees that the evidence base is dated and can judge whether to gather more recent signals before proceeding.
