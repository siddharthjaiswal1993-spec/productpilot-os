# Journey: Support Escalation to Prioritization

## Overview

**Trigger:** Support ticket volume on a specific issue spikes 40% over 14 days. No PM has triggered a review — the system detects it automatically.

**Who is involved:** Risk Watchdog Agent, Insight Synthesizer Agent, Prioritization Agent, Support Lead (signal originator), PM (decision-maker)

**Outcome:** A severity-scored escalation brief with volume trend data, affected customer analysis, correlated NPS signals, and prioritization recommendation — surfaced to the PM before any individual human has connected the pattern.

---

## Before ProductPilot OS

Week 1: 12 tickets about "webhook delivery failures" in Zendesk. Support agents handle individually, don't see a pattern. Each agent treats it as a one-off customer issue.

Week 2: Volume increases to 18 tickets. A CSM mentions to a PM in Slack: "I've been seeing a few webhook issues lately." PM makes a mental note but doesn't investigate.

Week 3: Volume hits 28 tickets. The Support Lead notices and emails the PM team with a "heads up" message. PM is in sprint planning and doesn't read the email until Friday.

Week 3 (Friday): PM reads email, starts investigating. Searches Zendesk for webhook tickets, finds 58 tickets over 3 weeks. Searches Gong calls for any customer mentions. Checks NPS responses from the last 30 days. Discovers the issue correlates with a release 3 weeks ago that changed webhook retry logic.

Week 4: PM writes a risk brief and escalates to engineering. Engineering triages. Total delay from first signal to engineering escalation: 4 weeks.

**Customer impact:** 5 enterprise customers experienced data loss due to webhook delivery failures over the 4-week detection delay.

---

## With ProductPilot OS

**Day 8 (automated):** The Risk Watchdog Agent runs its daily volume trend analysis. Webhook delivery failure ticket count: Day 1–7: 8 tickets. Day 8–14: 13 tickets (62.5% increase). Threshold triggered: 40% volume increase in 7 days. Classification: P2 Risk (high severity + growing pattern).

**Day 8 (7:00am):** PM receives a proactive alert: "Volume Spike Detected — Webhook Delivery Failures: 62% increase in 14 days (8→13 tickets). 5 affected accounts identified. Potential correlation with May 1 release. View brief?"

**Day 8 (9:05am):** PM opens the alert (between meetings). Clicks "Generate Risk Brief." System processes:
- Insight Synthesizer: pulls all 21 webhook tickets (found 8 additional from earlier in the month below the spike threshold), correlated Gong call mentions (2 found), NPS verbatims mentioning "reliability" (4 found in same time window), Slack mentions in customer success channels (3 found)
- Correlation analysis: 80% of tickets timestamp within 72 hours of the May 1 deployment
- Customer impact: 5 enterprise accounts affected, combined ARR $780K
- Risk classification: Data integrity risk (missed webhook deliveries = missed customer data events)

**Day 8 (9:06:30am):** Risk brief ready.

---

## The Generated Brief

**Risk Title:** Webhook Delivery Failure Volume Spike — Potential Release Regression

**Severity:** P2 (High) — data integrity risk, enterprise customer exposure

**Volume Trend:**

| Week | Ticket Volume | Change |
|------|-------------|--------|
| April 17–23 | 4 | Baseline |
| April 24–30 | 5 | +25% |
| May 1–7 | 8 | +60% |
| May 8–14 | 13 | +62.5% ← Alert triggered |

**Affected Customers:** 5 enterprise accounts identified, $780K combined ARR. 2 of 5 are within 90 days of renewal.

**Correlation Evidence:**
- 80% of tickets timestamp within 72 hours of May 1 deployment
- May 1 release notes: "Updated webhook retry logic for improved performance"
- 2 customers explicitly mention in tickets: "webhooks stopped working after your update last week"

**Correlated Signals:**
- NPS verbatims (May 8–14): 3 responses mentioning "reliability issues" — first mention of reliability in NPS for this product area in 60 days
- Gong call (May 10, Apex Analytics): "We're seeing some data gaps in our pipeline that we think might be from missed webhooks. We're investigating."

**Confidence Score:** 87/100 — high corroboration, strong temporal correlation with release, multiple signal types

**Recommended Action:** Immediate engineering investigation to confirm regression in May 1 release. Communication to 5 affected accounts. P1 if regression confirmed.

---

## Human Approval Points

**PM review (3 minutes):** Reads brief, confirms the May 1 release correlation is credible (recalls the webhook retry change), adds a note: "Confirm with engineering before communicating to customers." Approves the brief as P2.

**PM action:** Clicks "Escalate to Engineering" and "Draft Customer Communication."

**Risk Watchdog** creates a Jira ticket (with PM approval) with full brief attached, assigned to the integrations EM. Alert level: P2.

**Stakeholder Alignment Agent** drafts:
- Engineering EM: Technical brief with release correlation analysis and request for immediate triage
- Affected customer accounts (for CSM delivery): "We've identified a potential issue affecting webhook delivery. Our team is investigating and will update you within 24 hours. No action required on your end."
- VP Product: Risk summary — "P2 webhook delivery regression suspected from May 1 release. Engineering investigating."

PM reviews all three drafts in 4 minutes. Sends.

---

## Resolution Path

Day 9: Engineering confirms regression in webhook retry logic. Hotfix deployed Day 10.
Day 10: Customer communication updated: "Issue resolved. We identified and fixed a regression in our May 1 deployment. Affected webhook events will be replayed within 4 hours."

**Time from first signal to engineering escalation:** 8 days (down from 4 weeks)
**Affected customer notification:** Day 8 (proactive) vs. Week 5 (post-mortem)
**Enterprise customer churn risk:** Low (proactive communication + fast resolution) vs. High (4-week delay, potential data loss, no communication)

---

## Time Comparison

| Activity | Before | After | Improvement |
|----------|--------|-------|------------|
| Pattern detection | 3 weeks (manual) | Day 8 (automated) | 13 days earlier |
| Evidence assembly | 4+ hours | 90 seconds | 99% |
| Engineering escalation | Week 4 | Day 8 | 13 days earlier |
| Customer communication | Week 5 (post-mortem) | Day 8 (proactive) | 20+ days earlier |
| PM active time | 6+ hours | 12 minutes | 97% |

---

*The data to detect this risk existed on Day 3. The intelligence to act on it arrived on Day 8 — three weeks before it would have through manual detection.*
