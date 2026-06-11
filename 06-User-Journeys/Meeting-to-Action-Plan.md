# Journey: Meeting to Action Plan

## Overview

**Trigger:** A 60-minute product strategy meeting concludes. Attendees: PM, EM, Designer, VP Product. Topics covered: Q3 roadmap review, 3 upcoming feature decisions, 2 risk discussions, and a customer escalation update.

**Who is involved:** Meeting Intelligence Agent, Insight Synthesizer Agent, PM (review and distribution)

**Outcome:** Structured meeting summary with action items (owners, due dates), decisions made, signals extracted for the knowledge graph, and personalized follow-up briefs for each attendee — available within 4 minutes of meeting end.

---

## Before ProductPilot OS

**During meeting:** PM takes notes in a Notion doc while also participating. Notes are fragmented: "Alex — look into webhook fix by Friday," "VP says we should reconsider the mobile priority," "Design mentioned the new flows are ready to share." Misses several action items while talking.

**1 hour after meeting:** PM writes the meeting summary in Notion. 25–30 minutes to reconstruct from memory and fragmented notes.

**2 hours after meeting:** PM sends a Slack message with action items. 4 out of 6 action items captured; 2 are forgotten until someone asks about them at the next meeting.

**Next week:** 30% of action items from this meeting are not completed on time because: owners didn't see them in Slack (buried in thread), due dates were vague ("by next week"), or ownership was unclear.

**Total PM overhead:** 45–60 minutes of post-meeting administration. 30–40% incomplete action item follow-through.

---

## With ProductPilot OS

**Pre-meeting:** Meeting Intelligence Agent is activated for the Zoom call. (Or: PM uploads recording after the call.)

**During meeting:** PM participates fully — no note-taking required.

**4 minutes after meeting end:** Meeting Intelligence output is ready.

---

## Agent Processing Steps

**Step 1 — Transcript processing**
Meeting Intelligence Agent processes the 60-minute Zoom transcript. Speaker diarization applied. Each speaker's contributions are tagged by name and timestamped.

**Step 2 — Action item extraction**
Agent identifies action items using pattern recognition: commitments, requests, assignments ("I'll", "can you", "let's have X do", "by [date]", "before next week"). Extracts 8 action items with owners and implied due dates.

**Step 3 — Decision extraction**
Agent identifies decisions made: statements of conclusion, consensus language, direction-setting statements. Extracts 4 decisions with attributed decision-maker.

**Step 4 — Signal extraction**
Agent identifies product signals mentioned: customer escalations, feature discussions, risk flags, competitive mentions. Extracts 6 signals. Routes 3 to the PM's Signal Intelligence Hub as new items for follow-up.

**Step 5 — Knowledge graph update**
3 decisions are added to the knowledge graph as decision nodes. 2 signals are linked to existing entities (a customer mentioned matches an existing account; a feature discussed links to an existing backlog item).

**Step 6 — Personalized follow-up generation**
Stakeholder Alignment Agent generates personalized follow-up summaries for each attendee: each person sees their action items prominently, the decisions that affect their work, and relevant context.

---

## The Generated Meeting Summary

**Meeting:** Q3 Roadmap Strategy | May 15 | Attendees: PM (Jordan), EM (Wei), Designer (Sam), VP Product (Sarah)

---

### Decisions Made

| Decision | Made By | Rationale (from transcript) | Record |
|----------|---------|------------------------------|--------|
| Multi-tenant workspace confirmed as P1 for Q3 | Sarah (VP Product) | "Given the sales signal and the ACV at risk, I'm comfortable making this the priority." [0:12:34] | Added to knowledge graph |
| Advanced reporting deferred to Q4 | Jordan (PM) + Sarah (VP Product) | "The evidence is strong but not as urgent as the pipeline blocker." [0:14:10] | Added to knowledge graph |
| Mobile push notifications descoped from Q3 | Wei (EM) | "We don't have capacity in the current sprint structure to include this without compromising quality." [0:28:42] | Added to knowledge graph |
| Customer communication for webhook issue approved | Jordan (PM) | "Yes, let's send the proactive communication today." [0:45:19] | Action triggered |

---

### Action Items

| # | Owner | Action | Due Date | Priority |
|---|-------|--------|----------|---------|
| 1 | Wei (EM) | Engineering feasibility estimate for multi-tenant workspace | May 17 (EOW) | High |
| 2 | Jordan (PM) | Send webhook customer communication (already drafted) | Today | High |
| 3 | Sam (Designer) | Share updated onboarding flow designs in Figma for async review | May 16 | Medium |
| 4 | Jordan (PM) | Update VP Sales on multi-workspace P1 decision | Today | High |
| 5 | Sarah (VP Product) | Confirm Q3 OKR alignment with CEO given priority change | May 17 | Medium |
| 6 | Wei (EM) | Review sprint capacity for Q3 given mobile descope | May 16 | Medium |
| 7 | Jordan (PM) | Follow up with CS team on affected reporting accounts | May 16 | Medium |
| 8 | Jordan (PM) | Schedule customer deep-dive calls for multi-workspace validation | May 20 | Low |

---

### Signals Extracted (Routed to Signal Hub)

1. "VP mentioned a board inquiry about our competitive positioning on enterprise features" — tagged as strategic signal, routed to VP Product's dashboard
2. "Wei mentioned API performance degradation under heavy load in staging" — tagged as technical risk signal, routed to PM Signal Hub
3. "Sam noted that three customers during design research asked about a dark mode option" — tagged as feature request signal, added to knowledge graph

---

## Personalized Follow-Up Summaries

Each attendee receives a personalized summary in their inbox:

**Jordan's summary:** Your 4 action items highlighted, decisions you made, signals you need to follow up on.

**Wei's summary:** Your 2 action items highlighted, technical decisions that affect your sprint planning, the API performance signal extracted.

**Sam's summary:** Your 1 action item, design-relevant decisions, customer insights from meeting.

**Sarah's summary:** Your 1 action item, all decisions requiring her sign-off, the board inquiry signal.

---

## Time Comparison

| Activity | Before | After | Improvement |
|----------|--------|-------|------------|
| Note-taking during meeting | 20 min (distracted participation) | 0 (full participation) | 100% |
| Meeting summary writing | 25–30 min | 0 (generated) | 100% |
| Action item distribution | 10 min | 0 (automated per-person) | 100% |
| Knowledge graph update | Not done | Automated | ∞ |
| Signal extraction and routing | Not done | Automated | ∞ |
| **Total PM post-meeting time** | **45–60 min** | **5 min (review + approve)** | **92%** |
| **Action item completion rate** | **~65%** | **~85% (owners assigned, due dates explicit)** | **+20 pts** |

---

*60 minutes of meeting becomes a structured intelligence artifact in 4 minutes — and the PM participated fully instead of taking notes the whole time.*
