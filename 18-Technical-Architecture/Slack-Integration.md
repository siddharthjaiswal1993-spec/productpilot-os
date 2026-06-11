# Slack Integration Specification

## Overview

Slack is the highest-signal and highest-risk data source in ProductPilot OS. It is the richest source of informal customer intelligence, PM coordination context, and emerging product signals — but it also contains the most sensitive employee communications. The Slack integration is designed with the most conservative default settings of any integration.

---

## Connection Model

**Authentication:** OAuth 2.0 with Slack API  
**Required scopes:** `channels:read`, `channels:history` (per-channel), `users:read`  
**Not requested:** `admin`, `files:read`, `im:read`, `mpim:read` (direct and group messages are never accessed)

**Key principle:** ProductPilot OS only accesses Slack channels where an admin has explicitly enabled the Signal Intelligence integration. No default channel monitoring.

---

## Channel Scoping

**Default:** Zero channels monitored  
**How to enable:** Admin selects specific channels from a picker in Settings > Integrations > Slack

**Recommended channels to monitor:**
- `#customer-feedback` or `#voc` (voice of customer)
- `#product-requests`
- `#support-escalations`
- `#sales-feedback` or `#sales-wins-losses`
- `#customer-success`
- `#engineering-incidents` (for risk signal)

**Explicitly excluded categories:**
- Direct messages (never accessible — not in OAuth scope)
- HR channels (by admin configuration — explicit exclusion)
- Executive channels (by admin configuration — explicit exclusion)
- Random/general channels (configurable)

---

## Event Types Ingested

| Event Type | Ingested? | Notes |
|------------|----------|-------|
| Regular channel messages | Yes | From monitored channels only |
| Thread replies | Yes | With parent message as context |
| Edited messages | Yes | Re-indexed; old version marked deprecated |
| Deleted messages | Deleted from index | If message is cited in an approved brief, citation marked "[Message deleted by author]" |
| File shares | No | File content not indexed (attachment-only signals flagged but not parsed) |
| Reactions (emoji) | No | Not signal-quality content |
| Bot messages | Configurable | Excluded by default; admin can enable specific bot messages |

---

## Slack-Specific Chunking

**Strategy:** Message-level chunking  
**Unit:** Single Slack message  
**Max size:** 500 tokens  
**Thread context:** Thread replies include the parent message text in the chunk metadata (not as a separate chunk — as context for the reply)

**Special case: Long messages**
If a single Slack message exceeds 500 tokens (rare, but possible for detailed Slack posts), it is split at paragraph boundaries. Chunks from the same message include `chunk_index` and `total_chunks` in metadata.

---

## Entity Resolution from Slack

Slack messages often reference customers by informal names or nicknames:
- "Acme" instead of "Acme Corp"
- "the big enterprise customer" without naming them
- "the fintech startup in our pipeline"

**Entity resolution approach:**
1. Run NER on message text
2. Check extracted entity against known account names from CRM
3. Fuzzy match: "Acme" → "Acme Corp" (≥85% similarity)
4. If no match: tag as "[Account: Unresolved]"; PM can manually link
5. Resolved entities are linked to the Signal via ASSOCIATED_WITH relationship in knowledge graph

---

## Slack Integration Health

**Webhook:** Slack Events API (Real-Time Messaging events)  
**Polling fallback:** Slack Conversations API (every 5 minutes per channel, if webhook fails)  
**Rate limit:** Slack tier 2 API methods: 20 requests/minute per channel  
**Historical load:** Slack Conversations History API, 90-day default historical window

**Health monitoring:**
- If no events delivered from Slack for >20 minutes: activate polling fallback
- Webhook reconnection attempted every 15 minutes
- Admin notified after 1 hour of continuous webhook failure

---

## Privacy Considerations

**What is indexed:** Message text, author (Slack user ID → resolved name), channel, timestamp  
**What is NOT indexed:** User email addresses (from `users.read` used for name resolution only), Slack DMs, reactions, file attachments

**PII handling:**
Email addresses that appear in message text are detected and redacted before indexing. Personal phone numbers and financial data are also scanned and redacted.

**Audit note:** Because Slack channel monitoring is strictly opt-in and admin-configured, employees in non-monitored channels are never indexed. This must be communicated clearly in the internal rollout communication to avoid concerns about surveillance.
