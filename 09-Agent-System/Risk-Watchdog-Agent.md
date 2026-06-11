# Risk Watchdog Agent

## Purpose

The Risk Watchdog Agent is a continuous monitoring agent that operates in the background, scanning signal volume trends, correlating anomalies with recent product changes, assessing customer impact, and generating proactive risk briefs before a PM would have noticed the pattern manually. It is the primary early-warning system in ProductPilot OS — designed to detect problems the PM doesn't know to look for.

---

## Core Behaviors

1. **Trend monitoring:** Runs every 4 hours. Compares current 7-day signal volume per category against the 30-day rolling baseline. Flags any category that shows >30% increase.

2. **Release correlation:** When a volume spike is detected, queries the knowledge graph for product releases or configuration changes in the prior 14-day window. If a plausible correlation exists, includes it in the risk brief.

3. **Customer impact assessment:** Queries CRM for ARR and SLA data for accounts named in high-volume signals. Calculates total ARR at risk. Prioritizes accounts with upcoming renewal dates.

4. **Escalation readiness:** Generates a risk brief with three mitigation options, each with estimated cost, benefit, and risk tradeoff. Prepares a Jira ticket draft for engineering escalation — staged for PM confirmation only.

---

## Inputs

| Input | Source | Format | Required |
|-------|--------|--------|---------|
| Signal volume time series | Signal Intelligence Hub | Daily/weekly volume per category per tenant | Yes |
| 30-day baseline per category | Signal Intelligence Hub | Rolling average, standard deviation per category | Yes |
| Recent releases / changes | Knowledge graph + Jira | Release timestamps, impacted feature areas | If available |
| Customer account data | CRM integration | ARR, tier, renewal date, SLA provisions | If available |
| Prior risk briefs | Knowledge graph | History of risk events, resolutions, recurrence | Yes |
| On-call schedule | PM configuration (optional) | Engineering escalation contacts | Optional |

---

## Outputs

| Output | Format | Recipient |
|--------|--------|----------|
| In-app risk alert | Priority badge + brief preview | PM notification center |
| Risk brief | Structured risk brief with 3 mitigation options | PM decision view |
| Affected accounts table | Account × ARR × tier × renewal × SLA | Included in risk brief |
| Release correlation note | If correlated: release name, date, impacted area, correlation confidence | Included in risk brief |
| Staged Jira ticket | Draft Jira ticket for engineering escalation | Jira integration layer (requires PM confirmation) |

---

## Alert Priority Classification

| Priority | Condition | Response Time Target |
|----------|-----------|---------------------|
| P1 | >100% spike + ARR >$500K at risk OR SLA breach possible | Alert within 15 minutes of detection |
| P2 | >50% spike + ARR >$100K at risk OR 5+ enterprise accounts affected | Alert within 30 minutes of detection |
| P3 | >30% spike, below P2 thresholds | Alert in daily digest (8am PM time) |
| Informational | 15–30% trend movement | Weekly signal health summary |

---

## Reasoning Pattern

**Pattern: ReAct (Background monitoring loop)**

```
Every 4 hours:

Step 1 [ACT]: Load current 7-day signal volume per category
Step 2 [REASON]: Compare to 30-day baseline — which categories are trending?
Step 3 [REASON]: For each trending category, is this above the 30% alert threshold?
Step 4 [ACT]: Query knowledge graph for recent releases/changes in the correlated product area
Step 5 [REASON]: Is there a plausible release correlation? Confidence level?
Step 6 [ACT]: Query CRM for ARR and SLA data on named accounts in high-volume signals
Step 7 [REASON]: What is the total ARR at risk? Are any SLA-protected accounts affected?
Step 8 [REASON]: What priority level does this warrant? (P1/P2/P3/Informational)
Step 9 [ACT]: If P1 or P2: generate risk brief immediately and send alert
Step 10 [ACT]: If P3: add to daily digest queue
Step 11 [ACT]: Query knowledge graph for similar prior risk events and resolutions
Step 12 [REASON]: What are 3 mitigation options with tradeoffs?
Step 13 [ACT]: Assemble risk brief and stage Jira ticket draft (not created until PM confirmation)
Step 14 [OUTPUT]: Deliver risk brief to PM notification center
```

---

## Memory

- **Persistent monitoring state:** Category baseline volumes, prior alert history, PM-resolved risk records — stored in knowledge graph
- **Episodic memory:** Prior risk events, their resolutions, and recurrence patterns — used to identify repeat issues and escalate if an issue was previously dismissed but re-emerges

---

## Guardrails

1. **False positive management:** Alert thresholds are configurable per PM and product area. The default 30% threshold is conservative; PMs can raise it for volatile signal categories (e.g., "feature requests" often spike after conferences and don't require risk alerts).

2. **SLA-sensitive accounts first:** When assembling the affected accounts table, SLA-protected accounts are always listed first, regardless of ARR rank. A $100K SLA account at breach risk is elevated above a $500K account without SLA exposure.

3. **No external communication without PM approval:** The Risk Watchdog never sends customer communications, creates Jira tickets, or escalates to engineering without explicit PM confirmation. It stages these actions and requires human approval.

4. **Repeat issue tracking:** If a risk event in the same category has occurred in the past 90 days, the brief includes a "Prior occurrence" section with the previous event date, resolution, and whether it recurred. Repeated, unresolved risks are escalated in priority.

---

## Failure Modes

| Failure | Detection | Response |
|---------|-----------|---------|
| Monitoring run fails (system error) | Health check after each 4-hour run | Retry once; if second failure, alert engineering on-call and pause monitoring with PM notification |
| CRM unavailable during risk brief generation | HTTP error detection | Generate brief without ARR data; display "ARR data unavailable — CRM connection failed" in affected accounts section |
| No release data available for correlation check | Knowledge graph query returns empty | Generate brief without release correlation; display "Release correlation: insufficient data" |
| PM has not acted on a P1 risk brief after 2 hours | Brief age monitoring | Send follow-up notification: "Risk brief [ID] has been open for 2 hours without action. Would you like to escalate?" |

---

## Human Approval Boundary

The Risk Watchdog Agent monitors, detects, and recommends. It never acts.

Every consequential action — Jira ticket creation, engineering escalation, customer communication — requires explicit PM confirmation. The PM sees what will happen before it happens and must actively confirm.

---

## Quality Metrics

| Metric | Target |
|--------|--------|
| True positive rate (alerts that resulted in PM action) | >70% of P1/P2 alerts |
| False positive rate (PM dismisses alert as irrelevant) | <20% of P1/P2 alerts |
| Alert-to-brief latency (p95) | <15 minutes for P1, <30 minutes for P2 |
| ARR at risk captured in brief | >90% of accounts with >$50K ARR identified |
| Prior occurrence detection rate | >85% of repeat issues flagged as repeat |
