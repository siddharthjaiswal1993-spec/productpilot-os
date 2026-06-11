# Trust Layer

## Purpose

The Trust Layer is the set of mechanisms that ensure PM users can trust the outputs of ProductPilot OS. It encompasses hallucination prevention, citation validation, PII protection, output verification, and the feedback loops that make trust measurable and improvable over time.

Trust is earned incrementally. The Trust Layer is designed to earn it systematically.

---

## Trust Failure Modes (and Their Mitigations)

### Failure Mode 1: Hallucination
**What it looks like:** A brief says "12 enterprise accounts affected" but only 7 accounts appear in the evidence. A PRD says "customers reported X in Q3" but X does not appear in any retrieved signal.

**Why it matters:** A PM uses the brief to justify a priority decision to a VP. The VP asks for the source. The PM can't find it. The PM's credibility is damaged. The PM stops using ProductPilot.

**Mitigations:**
- **Claim-source binding:** Every numeric claim and every customer attribution is bound to a specific retrieved evidence chunk during generation. The LLM cannot introduce a numeric claim that was not in the retrieved evidence.
- **Post-generation hallucination check:** A separate validation step checks all numeric values and entity names in the generated output against the retrieved evidence list. Claims that don't match are flagged or removed.
- **"No source found" handling:** If a claim cannot be linked to a source, it is replaced with "[No source — PM review required]" rather than published without a citation.

### Failure Mode 2: Stale Evidence
**What it looks like:** A brief cites a customer need that was resolved in last quarter's release. The evidence is real, but it's no longer relevant.

**Why it matters:** The PM prioritizes based on a need that no longer exists. Engineering builds something customers no longer want. The PM looks uninformed.

**Mitigations:**
- **Evidence age labels:** All evidence is displayed with a clear date. Evidence older than 90 days is marked "Historical." Evidence older than 180 days receives a yellow stale warning badge.
- **Recency component in confidence score:** Stale evidence directly reduces the confidence score, making the staleness risk visible in the headline metric.
- **"Already addressed?" check:** If the knowledge graph contains a PRD or release note that appears to address the same signal, the brief includes a "Possibly addressed" flag for PM review.

### Failure Mode 3: Fabricated Customer Quotes
**What it looks like:** A PRD contains a customer quote like "We need this to close our enterprise deals" — but no customer said this. The LLM constructed a plausible quote.

**Why it matters:** Using fabricated customer quotes in stakeholder communications is misleading and can permanently damage PM credibility if discovered.

**Mitigations:**
- **Verbatim-only quotes:** All customer quotes in generated outputs are verbatim excerpts from retrieved signals, with the source system, author, and timestamp cited.
- **Quote-source validation:** Post-generation check verifies that every quoted string appears in the retrieved evidence list. Any string presented as a quote that does not match a retrieved chunk is removed.
- **Quote attribution display:** Every quote is displayed with the source on hover, so PMs and reviewers can immediately verify any claim.

### Failure Mode 4: Cross-Tenant Data Exposure
**What it looks like:** A brief for Company A contains a signal or account name that belongs to Company B's tenant.

**Why it matters:** This is not just a trust failure — it is a security incident and potentially a regulatory violation.

**Mitigations:**
- **Vector namespace isolation:** Each tenant's signals are stored in a separate, dedicated vector namespace. Retrieval queries are scoped to the tenant namespace at the infrastructure level, not just the application level.
- **Tenant ID enforcement at query time:** Every RAG query and knowledge graph query includes a mandatory tenant_id filter that is set server-side (not from user input).
- **Automated isolation checks:** Periodic automated tests verify that a query from Tenant A returns zero results from Tenant B's namespace.

### Failure Mode 5: PII in Outputs
**What it looks like:** A stakeholder communication generated for the Sales team includes a specific enterprise customer's name, contract value, or personal contact details.

**Why it matters:** Sharing PII without appropriate controls violates privacy policies and, in some contexts, regulations like GDPR.

**Mitigations:**
- **PII scanner on all outputs:** Before any generated text is delivered to the PM, it passes through a PII scanner that detects email addresses, phone numbers, financial figures (above configurable threshold), and personal names in contexts where they shouldn't appear.
- **Audience-scoped PII rules:** Each audience configuration includes a PII access level. The Sales audience may receive company names but not individual contact details. The Executive audience may receive ARR figures but not personal health information (if healthcare signals are ingested).
- **PM confirmation for flagged content:** Content flagged as potentially sensitive is shown to the PM with a warning before the PM can approve it for delivery.

---

## Trust Metrics

| Metric | Target | Measurement Method |
|--------|--------|--------------------|
| Hallucination rate | <2% | Post-generation claim verification; LLM-as-judge eval on golden dataset |
| Citation coverage | >95% | Count claims with citations / total factual claims |
| Stale evidence rate | <10% | Evidence older than 90 days / total evidence in briefs |
| PII in outputs rate | <0.1% | PII scanner detection rate on delivered outputs |
| PM trust score (self-reported) | >4.0/5.0 at 90 days | In-app PM trust survey |
| Fabricated quote rate | 0% | Quote-source validation; any detection is a critical incident |

---

## The Trust Flywheel

```
PM uses system → PM verifies citations → Citations are accurate →
PM trusts system more → PM approves more outputs → PM uses system for higher-stakes decisions →
PM recommends system to colleagues → New PM joins → Trust cycle repeats
```

The Trust Layer is the mechanism that makes this flywheel turn. Without it, the product produces good AI output but cannot convert that output into PM adoption.
