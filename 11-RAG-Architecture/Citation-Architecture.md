# Citation Architecture

## Why Citations Are Non-Negotiable

A product intelligence system that PMs cannot verify is a liability, not an asset. If a PM approves a priority decision based on an AI-generated brief, and the brief turns out to contain unverifiable claims, the PM's credibility is damaged. Once that happens with an individual PM, they stop using the tool. If it becomes known within the organization, adoption stalls.

Citation architecture is not a feature. It is the trust infrastructure that makes PM adoption possible.

Every factual claim in every ProductPilot OS output has a citation, or it is flagged for PM review. There are no undocumented claims.

---

## Citation Model

A citation is a structured link from a specific claim in generated text to a specific evidence chunk in the corpus.

**Citation structure:**
```json
{
  "citation_id": "cit_001",
  "claim_text": "12 enterprise accounts have requested SSO/SCIM",
  "source": {
    "chunk_id": "chunk_zendesk_12847_01",
    "source_type": "Zendesk",
    "source_id": "ticket_12847",
    "author": "Aisha Johnson",
    "date": "2025-03-01",
    "excerpt": "Our enterprise customers are asking about SSO. We have 4 open tickets this week alone.",
    "relevance_score": 0.94
  },
  "citation_type": "direct_evidence",
  "verifiable": true
}
```

---

## Citation Types

| Type | Description | Example |
|------|-------------|---------|
| `direct_evidence` | Claim is a direct restatement of retrieved evidence | "12 accounts affected" — from Zendesk ticket listing 12 accounts |
| `aggregated_evidence` | Claim aggregates multiple evidence chunks | "32 signals across 8 source types in 90 days" — computed from retrieved evidence set |
| `inferred_from_evidence` | Claim is a reasonable inference from evidence, not directly stated | "Pattern consistent with renewal risk" — inferred from signals, not explicitly stated |
| `crm_data` | Claim sourced directly from CRM (not from vector retrieval) | "$580K ARR" — sourced from Salesforce API response |
| `knowledge_graph` | Claim sourced from knowledge graph query | "This feature was previously scoped in Q2 2024" — from decision record |

---

## Citation Binding Process

Citation binding happens in two phases:

### Phase 1: In-Prompt Citation Instructions
The agent's system prompt includes explicit instructions for citation binding:

```
For every factual claim you make in the output:
1. Identify the evidence chunk(s) that support this claim
2. Include the citation ID in your output as: [CITE:chunk_id]
3. For aggregate claims (combining multiple chunks), include all supporting chunk IDs: [CITE:chunk_001,chunk_004,chunk_007]
4. If you cannot identify an evidence chunk that supports a claim, output: [UNCITED:your_claim]

NEVER make a claim you cannot cite. If you cannot find evidence for a claim, omit it.
```

### Phase 2: Post-Generation Citation Validation
After the LLM generates output, the Citation Validator runs:

1. **Parse:** Extract all `[CITE:chunk_id]` and `[UNCITED:claim]` markers from the output
2. **Validate:** For each `[CITE:chunk_id]`, verify that `chunk_id` exists in the retrieved evidence set
3. **Handle uncited:** Each `[UNCITED:claim]` is replaced with: "[PM review required — no source found for: {claim}]"
4. **Handle invalid citations:** Any `[CITE:chunk_id]` where `chunk_id` is not in the retrieved set is replaced with: "[Source not found — PM verify: {claim}]"
5. **Render:** Convert validated citation markers into the rendered UI format (superscript numbers + footnote table)

---

## Citation UI Design

Citations are displayed at three levels of interaction:

**Level 1: Inline superscripts**
Claims in the rendered brief show superscript citation numbers: "12 enterprise accounts have requested SSO/SCIM¹"

**Level 2: Citation hover tooltip**
Hovering over a superscript shows: Source type, author name, date, and a 2-line excerpt — all in a tooltip without leaving the brief.

**Level 3: Citation panel**
Clicking a superscript opens the full citation panel:
- Source type with logo
- Full source metadata (author, date, source system, source ID)
- Full excerpt text
- Relevance score
- Link to original source (if integration supports deep linking)
- "Mark as incorrect citation" button (PM can flag a citation they believe is wrong)

---

## Citation Completeness Tracking

| Metric | Target | Alert Threshold |
|--------|--------|----------------|
| Claims with valid citations | >95% per brief | <90% triggers brief warning |
| [PM review required] occurrences per brief | <2 per brief | >5 per brief triggers "evidence thin" warning |
| Citation validation failures (invalid chunk IDs) | 0% | Any occurrence triggers engineering alert |
| PM citation verification rate (PM expands citation) | >30% of briefs have at least 1 citation expanded | <10% suggests PMs aren't verifying (trust without verification) |

---

## Handling Confidence Claims About Numbers

A special case: numeric aggregations. When the Insight Synthesizer says "12 enterprise accounts have requested this," that number must come from:
- A specific Zendesk query result (12 accounts found in ticket metadata), OR
- A count of distinct Customer entities in the retrieved evidence set, OR
- An explicit figure stated in a single evidence chunk

It must NOT come from:
- The LLM "estimating" based on the content of retrieved chunks
- The LLM applying training data about typical enterprise request volumes
- Pattern-matching against vague phrases like "many customers"

**Implementation:** Before generating aggregate numeric claims, the agent is required to explicitly state the computation:

```
Internal computation (logged, not shown to PM):
COMPUTATION: Count distinct Customer entities in retrieved evidence set
RESULT: 8 distinct Customer entities found in chunks [chunk_001, chunk_003, chunk_007, chunk_012]
ACCOUNTS: Acme Corp, GlobalTech, NovaSystems, PinePoint, Horizon, Vertex, ClearPath, Meridian
CLAIM: "8 enterprise accounts have requested SSO in the past 90 days [CITE:chunk_001,chunk_003,chunk_007,chunk_012]"
```

The computation is logged in the reasoning trace. The claim and its citations are displayed to the PM.
