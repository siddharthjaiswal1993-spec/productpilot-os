# Persona: Staff Product Manager

## Overview

**Name:** Priya Sharma
**Title:** Staff Product Manager, Platform
**Company:** 600-person B2B SaaS (Series D), data infrastructure platform
**Team:** 12 PMs across 5 squads; Priya works horizontally across all squads
**Experience:** 10 years in product, 3 years at current company

Priya owns the horizontal platform product — the APIs, integrations, and shared infrastructure that all five product squads depend on. She does not own any squad's roadmap directly, but her architectural decisions affect all five squads' execution. She is the primary PM interface for the engineering platform team and a key input to quarterly planning across the organization.

---

## Goals

- Provide architecture-level product guidance that prevents cross-squad technical debt and dependency conflicts
- Synthesize signals from 5 product teams and surface patterns that require platform-level solutions
- Support engineering in making platform investment decisions that are grounded in product strategy, not just technical preference
- Develop the organizational capability for cross-squad intelligence sharing
- Advance to Principal PM or Group PM by demonstrating organizational scale of impact

---

## Pain Points

**Pain 1: Cross-squad signal synthesis is manually impossible**
"I'm supposed to synthesize signals from 5 product teams, identify the patterns that need platform-level solutions, and surface them before they become technical debt. But each squad has its own Jira, its own Slack channels, its own customer call schedule. There's no system that connects any of it. I'm doing this synthesis by attending 5 squad meetings per week and taking notes."

**Pain 2: Technical feasibility without unified context**
"When I give a feasibility assessment on a cross-squad feature, I need context from 3 squad backlogs, the platform technical roadmap, and 2 months of customer escalations that touch the API. This takes a full day to assemble. I do it maybe twice a month — but I should be doing it for every major architectural decision."

**Pain 3: PRD quality for platform features**
"Platform PRDs are the most complex documents in the organization — they have to satisfy 5 squad PM stakeholders, an engineering platform team, and an executive sponsor. Writing them without systematically assembled evidence from all stakeholders makes them into negotiation documents rather than decision documents."

**Pain 4: Invisible dependency mapping**
"Features across 5 squads have technical dependencies that nobody has mapped systematically. I track these manually in a spreadsheet. It's always incomplete. When a dependency conflict causes a sprint slip, I find out in the post-mortem."

---

## Current Workflow

**Monday:** Cross-squad stand-up review (5 meetings × 20 min), Slack catch-up across 8 channels
**Tuesday/Wednesday:** Deep architecture and spec work; dependency mapping; technical risk assessment
**Thursday:** Stakeholder reviews with squad PMs; platform roadmap alignment
**Friday:** Executive summary prep; cross-squad dependency report update (manual)

---

## Jobs to Be Done

- **When** identifying platform investment opportunities: synthesize signals from all 5 squads simultaneously to identify recurring patterns that require platform solutions
- **When** providing feasibility guidance: retrieve technical constraints from the platform backlog and cross-reference with squad feature requirements
- **When** writing a platform PRD: assemble requirements from 5 squad stakeholders, customer signals from all squads, and technical constraints from the platform team
- **When** managing dependencies: maintain a live dependency map updated from Jira states across all squads

---

## How ProductPilot OS Helps

**Multi-squad signal synthesis:** The Insight Synthesizer Agent aggregates signals across all 5 squad inboxes simultaneously and surfaces cross-squad patterns to Priya. "Three squads are requesting the same new data export capability — it belongs in the platform, not in three independent feature implementations."

**Platform PRD generation:** PRD Agent generates cross-squad platform PRDs by synthesizing requirements from all squad opportunity briefs. Priya reviews a consolidated draft rather than manually integrating 5 separate requirement sets.

**Dependency intelligence:** The Risk Watchdog Agent monitors cross-squad Jira dependencies and surfaces conflicts before they impact sprint planning. Dependency map is maintained in the knowledge graph, not in Priya's spreadsheet.

**Architecture decision support:** Before major architectural decisions, the Decision Engine synthesizes technical constraints from engineering signals with business impact from squad customer signals — providing the evidence base for architecture-level product guidance.

---

## Success Criteria

- Cross-squad pattern identification: 3+ platform opportunities identified per quarter from signal synthesis (up from 1)
- Platform PRD quality score: >80/100 (covering all stakeholder requirements with citations)
- Dependency conflicts caught proactively: >70% caught before sprint planning impact
- Architecture decision speed: <1 day for evidence assembly (down from 3–5 days)

---

## Quote

*"My job is to see the patterns that individual squad PMs can't see because they're too close to their area. ProductPilot OS gives me the cross-squad synthesis capability that I've been trying to build manually for three years."*
