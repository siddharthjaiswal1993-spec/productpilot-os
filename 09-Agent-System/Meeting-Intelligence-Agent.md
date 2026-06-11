# Meeting Intelligence Agent

## Purpose

The Meeting Intelligence Agent processes meeting transcripts and recordings to extract structured intelligence: decisions made, action items with owners, signals that should enter the product knowledge base, and follow-up communication drafts for each attendee. It converts what is currently the most unstructured information source in a PM's week into structured, actionable, and indexed product knowledge.

---

## The Problem It Solves

The average PM attends 15–20 hours of meetings per week. These meetings contain:
- Product decisions that are never formally recorded
- Customer feedback that never makes it into a prioritization brief
- Engineering constraints that PMs forget by the next sprint planning
- Commitments made to stakeholders that become invisible until someone asks about them

The Meeting Intelligence Agent captures all of this automatically from the transcript.

---

## Inputs

| Input | Source | Format | Required |
|-------|--------|--------|---------|
| Meeting transcript | Gong, Zoom, Fireflies, or uploaded .txt/.srt | Plain text with speaker labels and timestamps | Yes |
| Meeting metadata | Calendar integration or PM input | Meeting title, attendees, date, meeting type | Yes |
| Attendee profiles | PM configuration + knowledge graph | Role, team, prior communications, outstanding commitments | If available |
| Relevant signals (pre-meeting context) | RAG retrieval (run at meeting time) | Prior signals related to meeting topic | If available |

---

## Outputs

| Output | Format | Recipient |
|--------|--------|----------|
| Decision record | List of decisions with owner, context, timestamp | PM decision log |
| Action items | Table: task, owner, due date, confidence | PM task view |
| Extracted signals | Classified signals from meeting content | Signal Intelligence Hub |
| Follow-up draft (×N attendees) | Personalized follow-up email/Slack per attendee | PM communication queue |
| Meeting summary | Brief prose summary + structured sections | PM workspace |

---

## Extraction Types

### Decisions
The agent identifies explicit and implicit decisions in the transcript. An "explicit" decision is a clear statement: "We're going to go with Option B." An "implicit" decision is a pattern like: two parties agreeing on a course of action without using the word "decide."

Each decision record includes:
- Decision text (paraphrase + verbatim excerpt)
- Decision owner (who was accountable)
- Timestamp in transcript
- Confidence: High (explicit) / Medium (implicit) / Low (inferred)

### Action Items
Each action item includes:
- Task description
- Owner (identified from transcript by name or role)
- Due date (if mentioned) or "[Date not specified — PM review required]"
- Source quote from transcript

### Product Signals
The agent classifies statements from the meeting as product signals when they represent:
- Feature requests or customer needs described by attendees
- Pain points or friction expressed about the current product
- Technical constraints raised by engineering
- Competitive mentions

These are automatically submitted to the Signal Intelligence Hub as new signals, tagged with source "Meeting transcript — [Meeting Name] — [Date]."

### Follow-Up Communications
For each attendee with open action items or decisions that affect them, the agent generates a personalized follow-up draft. Framing logic mirrors the Stakeholder Alignment Agent's audience rules.

---

## Reasoning Pattern

```
Phase 1: Structural parsing
- Identify speaker turns
- Segment transcript into meeting phases (opening, discussion, decision, close)
- Build speaker map: speaker label → attendee role/name from metadata

Phase 2: Decision extraction (ReAct loop per segment)
- For each high-decision-signal segment (phrases: "we'll go with", "agreed", "the decision is", "let's do"):
  [REASON]: Is this a real decision or a proposal still under discussion?
  [ACT]: Extract candidate decision text
  [REASON]: Who owns this decision? Is there an explicit owner?
  [ACT]: Assign owner; confidence based on explicitness

Phase 3: Action item extraction (ReAct loop)
- For each action-signal phrase ("I'll", "you'll", "we need to", "by [date]", "can you"):
  [REASON]: Is this a committed action item or a hypothetical?
  [ACT]: Extract task, owner, date (if present)
  [REASON]: Confidence level?

Phase 4: Signal extraction
- For each statement representing customer feedback, feature need, or technical constraint:
  [ACT]: Classify signal type
  [ACT]: Submit to Signal Intelligence Hub

Phase 5: Follow-up generation
- For each attendee with open action items:
  [ACT]: Generate personalized follow-up draft with their specific items
  [REASON]: Is framing appropriate for their role and relationship?
```

---

## Autonomy Level

**Read-only for transcript processing.** Write access limited to:
- Adding classified signals to the Signal Intelligence Hub
- Creating a meeting record in the knowledge graph
- Creating draft follow-up communications in the PM's queue

**Requires PM confirmation:**
- Sending any follow-up communication
- Linking a meeting decision to a formal decision record in the knowledge graph

---

## Guardrails

1. **No action items assigned without PM review:** The agent extracts action items with owners, but the PM must confirm each item before it appears in anyone else's task management system (Jira, Asana, etc.).

2. **Low-confidence decisions flagged:** Decisions extracted at medium or low confidence are marked with a yellow badge and include the verbatim transcript excerpt so the PM can verify.

3. **Speaker attribution accuracy:** If the agent cannot confidently attribute a statement to a named attendee (speaker labels are numbers, not names), it flags the attribution: "Speaker [Label] — Please verify attendee identity."

4. **Meeting content is confidential by default:** Extracted signals from customer meetings are tagged with the originating meeting's confidentiality level. If a meeting was with a customer, the extracted signal is tagged "Customer meeting — confidential" and access is scoped to the PM team.

---

## Quality Metrics

| Metric | Target |
|--------|--------|
| Decision extraction precision (PM confirms decision is real) | >85% |
| Action item extraction precision | >90% |
| Signal extraction acceptance rate (PM adds to Signal Hub) | >60% of extracted signals |
| Follow-up draft acceptance rate | >65% |
| End-to-end processing latency for 60-minute transcript | <5 minutes |
