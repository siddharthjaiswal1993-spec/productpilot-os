# AI System Diagram

## Overview

This document describes the full AI system architecture of ProductPilot OS in diagram form (text representations) and narrative. It covers: how signals flow from data sources into the AI layer, how agents process and transform signals into PM-consumable outputs, how the knowledge graph compounds intelligence over time, and how PM feedback flows back into system improvement.

---

## System Architecture Overview

```
External Data Sources
──────────────────────────────────────────────────────────────────────
  Slack     Jira     Zendesk    Gong     Salesforce    Notion
  Linear    Intercom  Confluence  Zoom    HubSpot      Amplitude
     │         │          │        │          │            │
     └─────────┴──────────┴────────┴──────────┴────────────┘
                              │
                    ┌─────────▼──────────┐
                    │  Integration Layer  │
                    │  (Webhooks / APIs / │
                    │   Polling Fallback) │
                    └─────────┬──────────┘
                              │
                    ┌─────────▼──────────┐
                    │  Signal Processing  │
                    │  Pipeline          │
                    │  • Classification  │
                    │  • Entity extract  │
                    │  • Deduplication   │
                    │  • PII scanning    │
                    └─────────┬──────────┘
                              │
            ┌─────────────────┼─────────────────┐
            │                 │                 │
   ┌────────▼───────┐ ┌──────▼──────┐ ┌───────▼────────┐
   │ Vector Index   │ │  BM25 Index │ │ Knowledge Graph │
   │ (Dense Embed.) │ │ (Keyword)   │ │ (Entity-Rel.)   │
   └────────┬───────┘ └──────┬──────┘ └───────┬────────┘
            │                │                │
            └────────────────┼────────────────┘
                             │
                    ┌────────▼───────────┐
                    │   RAG Pipeline     │
                    │  • Retrieval       │
                    │  • Re-ranking      │
                    │  • Context inject  │
                    └────────┬───────────┘
                             │
                    ┌────────▼───────────┐
                    │  Agent Pool        │
                    │  ┌──────────────┐  │
                    │  │   Insight    │  │
                    │  │  Synthesizer │  │
                    │  ├──────────────┤  │
                    │  │Prioritization│  │
                    │  │   Agent     │  │
                    │  ├──────────────┤  │
                    │  │  PRD Agent  │  │
                    │  ├──────────────┤  │
                    │  │    Risk     │  │
                    │  │  Watchdog   │  │
                    │  ├──────────────┤  │
                    │  │ Stakeholder │  │
                    │  │  Alignment  │  │
                    │  ├──────────────┤  │
                    │  │  Meeting    │  │
                    │  │Intelligence │  │
                    │  ├──────────────┤  │
                    │  │  Roadmap   │  │
                    │  │Intelligence │  │
                    │  ├──────────────┤  │
                    │  │ Executive   │  │
                    │  │  Summary   │  │
                    │  └──────────────┘  │
                    └────────┬───────────┘
                             │
                    ┌────────▼───────────┐
                    │ Orchestration Layer│
                    │ • Agent routing   │
                    │ • HITL gates      │
                    │ • Context inject  │
                    │ • Audit logging   │
                    │ • Output validate │
                    └────────┬───────────┘
                             │
                    ┌────────▼───────────┐
                    │  PM Interface      │
                    │  • Brief inbox     │
                    │  • Approval queue  │
                    │  • PRD workspace   │
                    │  • Roadmap view    │
                    │  • Risk alerts     │
                    └────────┬───────────┘
                             │ (PM Feedback)
                    ┌────────▼───────────┐
                    │  Evaluation Layer  │
                    │  • Acceptance rate │
                    │  • Hallucination   │
                    │  • Calibration     │
                    │  • Golden dataset  │
                    └────────────────────┘
```

---

## Data Flow: Signal to Brief

```
1. Slack message arrives → Webhook receiver
   └─▶ Signal Processing Pipeline
       ├─▶ Classification: "feature_request" (0.91 confidence)
       ├─▶ Entity extraction: ["SSO", "enterprise", "Acme Corp"]
       ├─▶ Deduplication: no match found in cluster → new signal
       ├─▶ PII scan: no PII detected
       └─▶ Embedding: chunk → vector → write to tenant vector namespace

2. PM clicks "Generate Brief" on signal cluster [SSO, 6 signals]
   └─▶ Orchestration Layer receives request
       └─▶ RAG Pipeline runs
           ├─▶ Vector search: "enterprise SSO authentication" → top 8 chunks
           ├─▶ BM25 search: "SSO SCIM" → 3 additional keyword matches
           └─▶ Re-ranker: select top 8 of 11 candidates

3. Insight Synthesizer Agent receives context
   ├─▶ CRM lookup: Acme Corp → $580K ARR, renewal in 45 days
   ├─▶ Knowledge graph query: "SSO" entity → prior decisions = none
   └─▶ ReAct reasoning loop → generate opportunity brief
       └─▶ Citation injector: bind claims to evidence IDs
       └─▶ Confidence Engine: score = 87/100
       └─▶ Schema validator: PASS
       └─▶ HITL Gate: brief → PM inbox (Draft)

4. PM approves brief → Decision Engine records decision
   └─▶ Knowledge graph: entity "Enterprise SSO" created/updated
   └─▶ Downstream: Prioritization Agent and PRD Agent unlock
```

---

## Knowledge Graph: Entity Model

```
Entities:
  [Feature]     Enterprise SSO / SCIM
  [Customer]    Acme Corp, GlobalTech Inc, NovaSystems
  [Signal]      Zendesk-12847, Gong-call-8847, Slack-msg-991
  [Decision]    dec_sso_scim_20250315 (P1 approved by Alex Chen)
  [Outcome]     [pending — to be recorded post-launch]

Relationships:
  [Signal] → EXPRESSES_NEED_FOR → [Feature]
  [Customer] → ASSOCIATED_WITH → [Signal]
  [Decision] → REFERENCES → [Signal] (×8)
  [Decision] → DECIDES_ON → [Feature]
  [Feature] → HAS_OUTCOME → [Outcome]
  [Customer] → ARR_VALUE → $580K
```

---

## Trust Layer Integration

Every output from the Agent Pool passes through the Trust Layer before reaching the PM:

```
Agent Output
     │
     ▼
┌─────────────────────────────────────────┐
│ Trust Layer                              │
│ 1. Claim-source validation              │
│    → Every claim bound to evidence ID   │
│ 2. Hallucination check                  │
│    → Numerics verified against corpus   │
│ 3. PII scan                             │
│    → Customer PII filtered per audience │
│ 4. Quote validation                     │
│    → Verbatim quotes verified in corpus │
│ 5. Schema validation                    │
│    → Output matches required structure  │
└─────────────────────────────────────────┘
     │
     ▼
HITL Gate → PM Inbox (Draft)
```

---

## Feedback Loop: PM Action → System Improvement

```
PM Action                  Signal Type              Improvement Target
─────────                  ───────────              ──────────────────
Approve (no edit)     →    Positive label      →    Reinforce current approach
Approve (minor edit)  →    Positive + delta    →    Improve specific field
Approve (major edit)  →    Partial positive    →    Regenerate strategy for this output type
Reject + reason       →    Negative label      →    Update eval dataset + prompt tuning
RICE override         →    Calibration signal  →    RICE scoring model adjustment
Edge case accepted    →    Entity addition     →    Knowledge graph enrichment
Edge case rejected    →    Negative entity     →    Edge case filtering improvement
```

This feedback loop runs continuously. The system improves from every PM interaction — approval, rejection, and override.
