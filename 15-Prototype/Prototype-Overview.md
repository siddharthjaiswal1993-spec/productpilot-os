# Prototype Overview

## Purpose

The ProductPilot OS prototype is a functional demonstration of the V1 product experience, built to validate the core PM workflow with design partners before full engineering investment. The prototype demonstrates the intelligence layer in action: signals flowing in, a brief being generated with citations, prioritization scoring, and stakeholder alignment drafts — all within a PM's workflow.

---

## Prototype Goals

**Goal 1: Validate the core value exchange**  
A PM who uses the prototype should leave with a clear sense that "this would save me 3 hours this week." If they don't feel that, the prototype reveals a messaging or design problem to fix before engineering builds it.

**Goal 2: Test the HITL gate design**  
Does the approval workflow feel appropriate (not burdensome, not confusingly placed)? PMs should feel in control, not like they're rubber-stamping.

**Goal 3: Validate citation design**  
When PMs see citations, do they click on them? Do they trust them? This tells us whether the citation architecture is landing as intended.

**Goal 4: Identify friction in the onboarding experience**  
How long does it take a new PM to get to their first brief? What blocks them?

---

## Prototype Scope

The prototype covers the primary PM workflow (Sections 06 user journeys):

1. **Signal Intelligence Hub:** Browse and filter incoming signals
2. **Opportunity Brief Generation:** Select a cluster → generate brief → view evidence → approve
3. **Prioritization Scoring:** Score from brief → RICE breakdown → backlog comparison → approve
4. **PRD Generation:** Generate from approved brief → review draft → approve
5. **Stakeholder Alignment:** Generate drafts for 3 audiences → review per-audience → approve

---

## What the Prototype Is NOT

- **Not connected to real data:** The prototype uses a realistic fictional dataset (see the sample data in `/assets/`)
- **Not a full production build:** Some UI states (loading, error, edge cases) are mocked
- **Not a specification for production UI:** The prototype tests concepts; production design may iterate significantly based on feedback
- **Not mobile-optimized:** Desktop-first prototype

---

## Build Tool

The prototype is built with **Lovable.dev** (for the primary UI prototype) and **Bolt.new** (for technical component exploration). See:
- `/15-Prototype/Lovable-Prompt.md` — the complete Lovable build prompt
- `/15-Prototype/Bolt-Prompt.md` — the Bolt component specification

---

## Design Partner Demo Flow

**Recommended 30-minute demo sequence:**

1. **Context (3 min):** "This week, a PM team gets 50 new signals from Slack, Zendesk, Gong, and Salesforce. Here's what that looks like."

2. **Signal Hub (5 min):** Show the signal feed; filter by source type; point to a trending category; highlight a cluster of 6 signals about the same topic.

3. **Brief Generation (8 min):** Click "Generate Brief" on the cluster; watch the brief generate; walk through the evidence table; expand 2 citations to show the source; review the confidence score breakdown.

4. **Prioritization (5 min):** Show the RICE breakdown with evidence citations; compare to backlog; approve the priority decision.

5. **Stakeholder Drafts (5 min):** Show the 3 audience-specific drafts; highlight how framing differs by audience; approve the Engineering and Sales drafts.

6. **Discussion (4 min):** "Which of these steps would have taken you the most time without this? What would you trust / not trust?"

---

## Success Criteria for Prototype Validation

| Signal | What It Indicates |
|--------|------------------|
| PM clicks citations spontaneously | Trust architecture is working |
| PM completes the brief → prioritization → PRD flow without guidance | UX is intuitive |
| PM says "this would save me [specific amount of time]" | Value prop is landing |
| PM identifies a specific thing they'd want to customize | Product depth is visible |
| PM asks "how do I connect this to my Slack/Jira?" | Intent to adopt |
| PM worries about "the AI making stuff up" | Trust architecture needs better explanation |
| PM finds the approval gates confusing | HITL design needs rethinking |
