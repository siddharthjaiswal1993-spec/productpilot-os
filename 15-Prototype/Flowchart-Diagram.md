# Flowchart Diagrams

## Primary PM Workflow Flowchart

```
START: PM logs into ProductPilot OS
         │
         ▼
    Signal Intelligence Hub
    [Review incoming signals]
         │
    PM spots a signal cluster?
    ─────────────────────────
    YES │                    │ NO
        ▼                    ▼
  Select cluster        Browse signal feed
  (2+ signals)          [filter/search]
        │                    │
        └─────────┬──────────┘
                  ▼
    Click "Generate Brief"
                  │
                  ▼
    Insight Synthesizer Agent running
    [RAG retrieval + synthesis]
    (p95: 90 seconds)
                  │
                  ▼
    Brief arrives in PM inbox
    [status: AI Draft]
                  │
    PM reviews brief?
    ────────────────────────────────
    APPROVE │    EDIT → APPROVE │   REJECT
            │                  │         │
            ▼                  ▼         ▼
    Brief approved         Brief edited    Rejection reason
    Decision recorded      + approved      captured → log
            │                  │
            └────────┬─────────┘
                     │
    What next?
    ───────────────────────────────────────
    PRIORITIZE │    GENERATE PRD │   DONE
               │                 │
               ▼                 ▼
    Prioritization Agent    PRD Agent runs
    running                 (p95: 3 min)
    (p95: 60s)                   │
               │                 ▼
               ▼          PRD Draft in inbox
    Priority Brief         PM reviews PRD
    in PM inbox                  │
    PM reviews RICE         ─────────────
               │           APPROVE │ EDIT+APPROVE
    ─────────────────
    APPROVE │ OVERRIDE     Both paths → PRD Approved
            │         │           │
            ▼         ▼           ▼
    Decision      Override    PRD shareable
    recorded      logged      with engineering
            │
    Jira update?
    ──────────────
    YES │        │ NO
        ▼        └→ Done
    Confirmation
    dialog shown
        │
    PM confirms?
    ──────────────
    YES │        │ NO
        ▼        ▼
    Jira updated  No Jira update
    (logged)      (decision still
                   recorded)
```

---

## Risk Watchdog Flow

```
START: Every 4 hours (scheduled)
            │
            ▼
    Load signal volume time series
    Compare to 30-day baseline
            │
    Threshold exceeded?
    ─────────────────────────
    YES (>30%) │           │ NO
               ▼           ▼
    Calculate spike severity  Update baseline
    P1: >100% + >$500K ARR    Continue monitoring
    P2: >50% + >$100K ARR
    P3: >30%, below P2
               │
               ▼
    Query knowledge graph:
    Recent releases? Correlated?
               │
               ▼
    Query CRM: ARR + SLA for
    named accounts in signals
               │
               ▼
    Generate risk brief
    (p95: 90s for P1)
               │
    P1 or P2?
    ─────────────────────
    YES │                │ NO (P3)
        ▼                ▼
    Alert PM immediately  Add to daily digest
    (in-app + Slack DM)
        │
        ▼
    PM acknowledges alert?
    ─────────────────────────────
    (within 2 hours for P1)
        │
    ACKNOWLEDGE │    DISMISS (requires reason)
                │
                ▼
    PM selects mitigation
    ─────────────────────────────────────────
    Escalate to Eng │  Monitor │  No action
                    │
                    ▼
           Jira ticket preview
                    │
           PM confirms?
           ──────────────────
           YES │          │ NO
               ▼          ▼
           Jira ticket   No Jira ticket
           created       (noted in audit)
           (logged)
```

---

## Stakeholder Alignment Flow

```
TRIGGER: PM approves priority decision
              │
              ▼
    PM clicks "Generate Stakeholder Updates"
              │
              ▼
    Stakeholder Alignment Agent
    (parallel generation for all audiences)
    ┌─────────────────────────────────────┐
    │ Engineering draft  │  Sales draft   │
    │ CS draft           │  VP/Exec draft │
    │ PM Team draft                        │
    └─────────────────────────────────────┘
    All 5 generated in parallel (p95: 90s total)
              │
              ▼
    PII screening (all drafts)
    Contradiction detection (across drafts)
              │
    Contradiction detected?
    ─────────────────────────────
    YES │                      │ NO
        ▼                      ▼
    PM sees contradiction   All drafts queued
    alert; must resolve     for PM review
    before approving        
              │
              ▼
    PM reviews per-audience:
    ────────────────────────────────────────────
    Each audience independently:
    
    APPROVE │ EDIT + APPROVE │ REJECT
            │                        │
            ▼                        ▼
    Draft approved              Draft rejected
    PM can now send             PM can regenerate
                                or write manually
            │
    PM sends (email, Slack, Confluence)?
    ──────────────────────────────────────────────
    Yes, PM sends externally
    Communication logged in audit trail
    (linked to decision record)
```
