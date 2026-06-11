# Roadmap Intelligence Agent

## Purpose

The Roadmap Intelligence Agent provides continuous intelligence about the health, coherence, and evidence-backing of the team's product roadmap. It synthesizes the roadmap against the current signal corpus, surfaces items that have strong or weak evidence backing, detects strategic drift, identifies roadmap items that conflict with emerging signals, and generates roadmap narratives for stakeholder reviews. It is the agent that ensures the roadmap remains a living, evidence-backed document — not a static plan that goes stale.

---

## Core Capabilities

### 1. Roadmap Health Scoring
For each roadmap item, the agent computes an evidence health score:
- **Evidence volume:** How many signals support this item?
- **Evidence recency:** When were the most recent supporting signals?
- **Customer coverage:** How many distinct customer accounts or user segments expressed this need?
- **Strategic alignment:** How well does this item align with current OKRs?

Items with low health scores are surfaced to the PM as "roadmap items that need evidence refresh."

### 2. Signal-to-Roadmap Gap Detection
The agent identifies signals in the corpus that have no corresponding roadmap item — either because the PM hasn't yet created one or because the signal is a new emerging theme. These are surfaced as "unaddressed signals" with a brief summary.

### 3. Roadmap-to-Signal Gap Detection
The agent identifies roadmap items that have few or no supporting signals — either because the need is engineering-driven, assumption-based, or the original signals are stale. These are surfaced as "unsupported roadmap items" for PM review.

### 4. Strategic Drift Detection
The agent compares the current roadmap composition against the team's OKRs. If a significant percentage of roadmap capacity is allocated to items with low OKR alignment, it surfaces this as a "strategic drift" alert with the calculation.

### 5. Roadmap Narrative Generation
On demand, the agent generates stakeholder-ready roadmap narratives (executive summary, sales overview, engineering sprint context, investor-facing roadmap) — each using the same roadmap data with audience-appropriate framing.

---

## Inputs

| Input | Source | Format | Required |
|-------|--------|--------|---------|
| Current roadmap | Jira / Linear / PM-uploaded roadmap | Roadmap items with status, timeline, owner | Yes |
| Signal corpus | Signal Intelligence Hub | All current classified signals | Yes |
| Team OKRs | PM configuration | OKR list with weights and timelines | Yes |
| Prior roadmap snapshots | Knowledge graph | Historical roadmap versions | If available |
| CRM data | Salesforce / HubSpot | Customer ARR, tier, open opportunities | If available |

---

## Outputs

| Output | Format | Recipient |
|--------|--------|----------|
| Roadmap health report | Table: roadmap item × evidence health score | PM roadmap view |
| Unsupported roadmap items | List with evidence score, suggestion to gather more signals | PM roadmap view |
| Unaddressed signal clusters | List of high-volume signals with no roadmap item | PM roadmap view |
| Strategic drift alert | Alert with OKR alignment calculation | PM notification |
| Roadmap narrative (×N audiences) | Audience-specific narrative draft | PM communication queue |
| Evidence-backing rate | % of roadmap items with >3 cited evidence sources | Company health dashboard |

---

## Reasoning Pattern

```
Weekly roadmap health run:

For each roadmap item:
  [ACT]: Query signal corpus for supporting evidence (semantic search on roadmap item description)
  [REASON]: How many signals? How recent? How many distinct customers/segments?
  [ACT]: Compute evidence health score (0–100)
  [ACT]: Compute OKR alignment score

For the full corpus of unaddressed signals:
  [ACT]: Identify signal clusters with volume >5 and no corresponding roadmap item
  [REASON]: Are these genuinely missing from the roadmap or deliberately deprioritized?
  [ACT]: Flag as "Unaddressed signal cluster" for PM review

Cross-check:
  [REASON]: What % of roadmap capacity is OKR-aligned?
  [REASON]: Is there strategic drift?
  [ACT]: If OKR alignment < 60%: generate strategic drift alert

Output:
  [ACT]: Compile roadmap health report
  [ACT]: Queue for PM review
```

---

## Guardrails

1. **No roadmap modifications:** The Roadmap Intelligence Agent never creates, deletes, or modifies roadmap items. It surfaces intelligence about the roadmap and waits for PM action.

2. **Deliberate deprioritization is not drift:** Items that are on the roadmap and have been explicitly deprioritized by a PM are not flagged as "evidence gaps" — they are flagged as "low evidence, confirmed low priority." The PM's explicit decision record is respected.

3. **Strategic drift threshold is configurable:** The default alert threshold (OKR alignment below 60%) is configurable per team. Some teams intentionally allocate roadmap capacity to technical debt, infrastructure, or experimental bets that don't map directly to OKRs.

---

## Quality Metrics

| Metric | Target |
|--------|--------|
| Evidence-backing rate (roadmap items with >3 cited signals) | >60% at V1 launch → >80% at V2 |
| Unaddressed signal detection accuracy | >80% of flagged signals are genuinely unaddressed |
| Roadmap health report latency | <3 minutes for weekly run |
| Strategic drift alert precision | >75% of alerts confirmed as real drift by PM |
| Narrative draft acceptance rate | >65% approved without major edit |
