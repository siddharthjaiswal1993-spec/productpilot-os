# Multi-Agent Product Team

## From Agents to a Team

ProductPilot OS V1 ships 8 specialized agents that operate in a coordinated but largely sequential manner: signals come in, Insight Synthesizer generates a brief, PM approves, Prioritization Agent runs, etc. Each agent has a defined scope and a defined handoff protocol.

This is the right architecture for V1 — it is auditable, predictable, and easy to explain to a PM who is evaluating whether to trust the system.

But the trajectory from here is more interesting: a multi-agent system where agents coordinate dynamically, spawn sub-tasks, and represent an emergent "product intelligence team" that operates in the background while the PM focuses on strategic and relational work.

---

## What Changes in a Multi-Agent Architecture

### From sequential to concurrent
In V1, most workflows are sequential: signal → brief → prioritization → PRD. Each step waits for PM approval before the next step begins.

In a multi-agent future (V3+), agents operate concurrently on parallel tracks:
- While PM is reviewing the SSO/SCIM brief, the PRD Agent is already pre-staging a PRD outline in the background
- While the Risk Watchdog is monitoring signal spikes, the Roadmap Intelligence Agent is checking whether the roadmap adequately addresses the trending signal cluster
- When a weekly executive summary is requested, the Meeting Intelligence Agent and Roadmap Intelligence Agent run concurrently to feed the Executive Summary Agent their respective evidence packages

The PM sees the result of this concurrent work — faster turnaround, richer outputs — without needing to manage the orchestration.

### From fixed agents to dynamic task delegation
In V1, each agent has a fixed scope. In a mature multi-agent architecture, agents can spawn sub-agents for specific tasks:

Example: The Insight Synthesizer is generating a brief about "enterprise reporting gaps." It detects that one cited evidence cluster involves a Gong call where a specific account mentioned "we built internal reports because your exports weren't good enough." The synthesizer spawns a targeted evidence retrieval sub-task: "Find all evidence about export quality and customer-built workarounds for [Account: GlobalTech]."

This targeted sub-task returns precise, account-specific evidence that strengthens the brief's business case section — without requiring the PM to ask for it.

### From individual agents to cross-agent learning
In V1, each agent's PM Acceptance Rate is tracked independently. In a multi-agent future, agents share a learning signal:

- If the Insight Synthesizer's briefs consistently receive PM edits that add engineering complexity caveats, and the PM's engineering context comes from a specific Confluence space, the PRD Agent can incorporate that Confluence space as a higher-weight evidence source
- If the Risk Watchdog's alerts for a specific product area consistently receive PM "dismiss" actions, the system learns that PM's risk threshold for that area and adjusts alert sensitivity

This cross-agent learning compounds over time. The system doesn't just get better at individual tasks — it gets better at modeling the PM's judgment across the full workflow.

---

## The Agent Specialization Hierarchy

V1 deploys 8 generalist agents within their domains. The multi-agent future introduces a hierarchy:

**Tier 1: Strategic Agents (existing V1 agents)**
- Insight Synthesizer, Prioritization Agent, PRD Agent, Risk Watchdog
- Handle PM-facing workflow outputs
- Require PM approval for consequential actions

**Tier 2: Research Agents (new in V3)**
- Evidence Deep-Dive Agent: Given a specific claim or hypothesis, find all evidence for and against it across all connected sources
- Competitive Intelligence Agent: Monitor product release notes, job postings, and public documentation from competitors; surface relevant changes
- Customer Health Agent: Cross-reference CRM renewal dates, support ticket volume, feature adoption, and NPS to generate customer health scores per account

**Tier 3: Coordination Agents (new in V3)**
- Brief Routing Agent: Determines which PM or product area a new signal cluster belongs to; assigns briefs accordingly
- Evidence Quality Agent: Continuously monitors evidence freshness; flags briefs with stale citations; triggers re-retrieval when source material has been updated

**Tier 4: Execution Agents (new V3 with supervised autonomy)**
- Notification Agent: Manages all PM-facing communication (what to surface when, to whom, at what priority); prevents notification overload
- Draft Staging Agent: Handles background preparation of draft outputs so that PM approval actions are never blocked waiting for generation

---

## The Orchestration Intelligence Problem

Multi-agent systems create a new class of problem: how does the orchestration layer decide which agents to run, in what order, with what context?

V1 uses a simple rule-based orchestration: PM trigger → defined workflow → defined agents in sequence.

V3+ requires dynamic orchestration:
- An incoming signal cluster should trigger different agents depending on whether there is an existing open brief on that topic, whether there is an active risk alert, and whether it is close to a quarterly planning cycle
- An approved brief for a V-next feature should trigger both PRD prep and stakeholder alignment prep — but only if there is no existing PRD draft and no existing stakeholder communication in flight for that feature

**Design principle for orchestration intelligence:** Orchestration decisions are made by a dedicated meta-agent (the Orchestrator) using the knowledge graph's current state as its context. The Orchestrator does not generate content — it decides what to generate, when, and with what dependencies.

The Orchestrator is the most powerful agent in the system, which is why its decisions are always logged and the PM always has a real-time view of what agent work is currently in flight.

---

## Coordination Protocols Between Agents

Agents in a multi-agent system need protocols for:

**Evidence sharing:** When Agent A retrieves evidence for a task, that retrieval result is cached and available to Agent B working on a related task in the same session. This prevents redundant retrieval and ensures consistent evidence across related outputs.

**Contradiction resolution:** When Agent A's output contains a claim that contradicts Agent B's output for the same brief, the Orchestrator flags the contradiction and routes both outputs to the PM with the specific conflict highlighted. The PM resolves it.

**Confidence propagation:** When Agent A's output has a low confidence score, any downstream Agent B output that depends on Agent A's output inherits the confidence constraint. A low-confidence brief cannot generate a high-confidence PRD — the confidence ceiling propagates.

---

## Why Build This Incrementally

The multi-agent architecture described here is the V3 and beyond target state. Building it V1 would be a mistake — for two reasons:

1. **Trust must be established before delegation can expand.** A multi-agent system where agents spawn sub-agents creates attribution complexity ("which agent produced this claim?"). Before that complexity is acceptable, PMs must trust that individual agent outputs are reliable. That trust comes from V1 performance data.

2. **Orchestration requires a rich knowledge graph.** Dynamic orchestration decisions depend on the knowledge graph knowing what is already in progress, what has already been decided, and what the PM's priorities are. This graph richness is built by V1 usage, not before it.

The roadmap is: get V1 right → compound the knowledge graph → expand to multi-agent coordination → graduate to supervised autonomy. Each phase funds the next.
