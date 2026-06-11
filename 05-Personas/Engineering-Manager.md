# Persona: Engineering Manager

## Overview

**Name:** Wei Zhang
**Title:** Engineering Manager
**Company:** 300-person B2B SaaS (Series C), e-commerce platform
**Team:** 6 engineers, 1 PM partner, 1 designer
**Experience:** 8 years as engineer, 3 years as EM, 2 years at current company

Wei manages the integrations squad — responsible for 40+ third-party integrations and a public API platform. Works closely with the PM on the integrations product area. Frustrated by PRDs that arrive in sprint planning without edge cases, missing non-functional requirements, or insufficient customer context to make good architectural decisions.

---

## Goals

- Start every sprint with clear, complete requirements that the engineering team can build without ambiguity
- Reduce the number of "why are we building this?" conversations in sprint planning
- Get technical risk signals surfaced to the product team before they become sprint blockers
- Understand customer context behind priority decisions before committing architectural resources
- Build a culture where engineering and product are aligned before sprint planning, not during it

---

## Pain Points

**Pain 1: PRDs without edge cases and non-functional requirements**
"Every sprint, I get a PRD that describes the happy path in detail and ignores everything else. No error handling specification. No performance requirements. No scale expectations. No edge cases. My engineers discover these requirements in code review, which is the most expensive place to discover them."

**Pain 2: Missing customer context for architectural decisions**
"When a PM says 'we need real-time data sync,' I need to know: is that because 5 power users requested it or because the top 50 enterprise customers are blocking renewal on it? The architectural choices are completely different. I often don't have this context and have to ask, which adds a day to every sprint planning cycle."

**Pain 3: Technical risk without product context**
"My team identifies technical risks regularly — scalability limits in the current architecture, API rate limit risks, third-party deprecations. I have no way to route these to the product team with the customer impact context they need to prioritize them. I send Slack messages that get buried."

**Pain 4: Sprint reprioritization from late-discovered requirements**
"Late-discovered requirements — things that should have been in the PRD but weren't — cause sprint scope changes 40% of the time. Each scope change costs 2–3 days of team context switching. The aggregate overhead is 15–20% of engineering capacity per quarter."

---

## Jobs to Be Done

- **When** reviewing an incoming PRD: see evidence quality score and edge case coverage before the sprint planning meeting
- **When** making an architectural decision: retrieve customer context — how many customers, what tier, what their technical requirements are — without scheduling a meeting with the PM
- **When** surfacing a technical risk: route it to the product team with business impact context assembled automatically
- **When** a sprint scope change is being proposed: see the evidence and business impact supporting the change before agreeing or pushing back

---

## How ProductPilot OS Helps

**PRD quality signals for EMs:** Wei sees an evidence quality score and edge case coverage percentage on every incoming PRD before sprint planning. Low-quality PRDs are flagged before engineering commitment, not after.

**Customer context on demand:** Wei can query the knowledge graph: "How many enterprise customers use the Salesforce integration and what are their sync frequency requirements?" — and get a synthesized answer with citations without waiting for a PM response.

**Technical risk routing:** Wei submits technical risks to the Risk Watchdog interface, which automatically assesses business impact from customer signals and CRM data — and routes to the PM with a business context brief.

**Sprint scope change evidence:** When a scope change is proposed, the system surfaces the evidence (or lack thereof) supporting the change. Wei can accept or push back on evidence, not intuition.

---

## Success Criteria

- Late-discovered requirements per sprint: reduced from 3–4 to <1
- Sprint scope changes due to missing requirements: <10% (down from 40%)
- Technical risk routing time: <4 hours from identification to PM with business context
- Sprint planning preparation time: <1 hour (down from 2+ hours of PM-EM sync)

---

## Quote

*"I'm not asking for the PM to work harder. I'm asking for the requirements to be complete before they reach me. ProductPilot OS is the system that makes completeness systematic, not dependent on any individual PM's thoroughness."*
