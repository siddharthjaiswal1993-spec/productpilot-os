# Chunking Strategy

## Why Chunking Matters

The goal of chunking is to produce retrievable units that are:
1. **Small enough** that the LLM can use them efficiently (not wasting context window budget)
2. **Large enough** that each chunk contains a complete, contextually meaningful unit of information
3. **Atomic enough** that a citation to a chunk is a citation to a specific, verifiable claim

Chunking strategy is one of the highest-leverage decisions in RAG system design. Poor chunking (too large, too small, or wrong boundary detection) directly degrades retrieval quality and citation accuracy.

---

## Chunking by Source Type

Different source types require different chunking strategies because their semantic units are structurally different.

### Slack Messages
**Strategy:** Message-level chunking  
**Unit:** Single Slack message  
**Max size:** 500 tokens  
**Why:** A Slack message is a natural semantic unit. Splitting within messages loses attribution (the whole message is from one author at one time). Merging messages into threads loses individual-author attribution.  
**Exception:** Slack thread replies are chunked as individual messages. The parent message is injected as metadata into each reply chunk to preserve context.

### Zendesk / Intercom Tickets
**Strategy:** Conversation turn chunking with sliding context window  
**Unit:** Customer's message turn (not agent responses, which are low-signal for product intelligence)  
**Max size:** 300 tokens  
**Context injection:** Previous message turn injected as metadata for context  
**Why:** Tickets contain long back-and-forth conversations. Only customer voice is high-signal for product intelligence. Agent responses are indexed separately and lower-weighted.

### Gong / Zoom Transcripts
**Strategy:** Topic-segmented chunking  
**Unit:** Conversation segment identified by topic change  
**Typical size:** 400–600 tokens  
**Method:** LLM-based topic segmentation runs offline during processing to identify natural segment boundaries  
**Why:** Transcripts are long (30–90+ minutes). Topic segments are semantically coherent units. Arbitrary fixed-window chunking would split a continuous discussion of a product need across two chunks, hurting retrieval.  
**Metadata:** Each chunk includes speaker attribution and timestamp range

### Confluence / Notion Documents
**Strategy:** Hierarchical chunking  
**Unit:** Section (defined by H2 headings) → sub-chunk if section >600 tokens  
**Max size:** 600 tokens  
**Parent context injection:** Page title + H1 heading injected as metadata on each chunk  
**Why:** Document structure (headings) reflects semantic organization. A requirements section and a decision log section in the same document are semantically independent.

### CRM Notes (Salesforce / HubSpot)
**Strategy:** Note-level chunking  
**Unit:** Individual CRM note / activity log entry  
**Max size:** 400 tokens  
**Metadata:** Account name, note author, note date, note type (call note, email, meeting note)  
**Why:** CRM notes are atomic units of customer context written by a specific rep at a specific time.

### Uploaded PRDs / Specs
**Strategy:** Section-level chunking with overlap  
**Unit:** Document section (H2 boundary)  
**Overlap:** 50-token overlap at section boundaries to prevent context loss at breaks  
**Metadata:** Document title, section name, author, creation date  
**Why:** PRD sections are the smallest semantically independent units in a specification document.

---

## Chunk Metadata Schema

Every chunk, regardless of source type, is stored with this metadata:

```json
{
  "chunk_id": "string (UUID)",
  "tenant_id": "string",
  "source_type": "slack|zendesk|gong|confluence|notion|jira|salesforce|hubspot|intercom|upload",
  "source_id": "string (original document/ticket/message ID in source system)",
  "author": "string (author name or ID)",
  "date": "ISO-8601",
  "product_area": "string (extracted entity)",
  "signal_type": "feature_request|bug_report|churn_risk|competitive|technical|general",
  "chunk_index": integer,
  "total_chunks_in_doc": integer,
  "parent_context": "string (parent document title or thread context, injected for retrieval)",
  "pii_redacted": boolean,
  "content_hash": "string (for deduplication)"
}
```

---

## Deduplication

Near-duplicate chunks are a significant problem in multi-source environments. The same customer complaint may appear in a Zendesk ticket, a Slack thread referencing the ticket, and a Gong call noting "we have a ticket for that." All three should be indexed but should not all be retrieved and presented to the LLM as independent evidence.

**Deduplication approach:**
1. **Exact hash matching:** Content hash deduplication for identical text
2. **Near-duplicate detection:** Cosine similarity >0.98 on embeddings → flagged as near-duplicate; one is designated "primary," others are linked as "related" and weighted lower in retrieval

---

## Chunk Quality Validation

After chunking, a quality validation step checks:
- Chunk is not below minimum size (30 tokens — avoids noise chunks)
- Chunk is not above maximum size (enforced by chunker)
- Required metadata fields are populated
- Content is not empty after PII redaction

Chunks failing quality validation are discarded and logged for review.
