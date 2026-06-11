# Reasoning Engine

## Overview

The Reasoning Engine is the component of the AI architecture that manages how agents think through complex, multi-step tasks. It defines reasoning patterns, prompt architecture, self-critique mechanisms, and the structured thinking scaffolding that makes agent outputs reliable, auditable, and improvable.

The Reasoning Engine is not a single model or a single function — it is the design of how agents reason.

---

## Core Reasoning Patterns

### ReAct Pattern (Reason + Act)

Used for: tasks where the right retrieval strategy depends on intermediate results.

The ReAct pattern interleaves reasoning steps ("what do I need to know next?") with action steps ("retrieve X", "query Y"). This makes the agent adaptive — it can decide to do a follow-up retrieval after evaluating the initial results, rather than executing a fixed query plan.

```
THOUGHT: [What do I need to understand to make progress on this task?]
ACTION: [What tool call will give me the information I need?]
OBSERVATION: [What did the tool return? What does it tell me?]
THOUGHT: [How does this change my understanding? What do I need next?]
ACTION: [Next tool call based on updated reasoning...]
...
THOUGHT: [I have sufficient information. What does the evidence say?]
OUTPUT: [Generate structured output]
```

**Implementation:** Reasoning traces are logged in full. The PM can expand a "How was this generated?" panel to see the agent's reasoning chain for any output. This makes the output verifiable and the reasoning understandable.

### Planner-Executor Pattern

Used for: tasks with predictable structure where sections can be generated in parallel.

The Planner phase produces an explicit execution plan: which sections to generate, what retrieval query to run for each, what tools to call. The Executor phase runs the plan — often in parallel across independent sections. The Assembler phase combines outputs, injects citations, and validates completeness.

**Why parallel execution matters:** A PRD with 8 sections generated sequentially would take ~8x longer than necessary. The Planner-Executor pattern enables parallel section generation, reducing PRD generation from ~10 minutes to ~3 minutes at p95.

---

## Prompt Architecture

### System Prompt Design
Each agent has a system prompt that defines:
- **Role:** Who the agent is and what it's responsible for
- **Constraints:** What it must never do (fabricate quotes, use training data for numeric claims, act without HITL approval)
- **Output format:** The exact JSON schema or markdown structure it must produce
- **Reasoning instructions:** How to structure its thinking (ReAct or Planner-Executor)
- **Citation requirements:** How to bind every factual claim to a retrieved evidence chunk

### Retrieved Evidence Injection
Evidence from the RAG pipeline is injected into the LLM context in a structured format that makes citation-binding tractable:

```
[EVIDENCE_CHUNK_001]
Source: Zendesk
Source ID: ticket_12847
Author: Aisha Johnson
Date: 2025-03-01
Excerpt: "Our enterprise customers are asking about SSO. We have 4 open tickets this week alone."
Relevance Score: 0.94

[EVIDENCE_CHUNK_002]
Source: Gong
Source ID: call_8847
Author: Roberto Diaz (Account Executive)
Date: 2025-03-05
Excerpt: "The Acme deal is stalled. They won't sign until SSO is on the roadmap."
Relevance Score: 0.91

...
```

This structured injection tells the LLM exactly what IDs to cite when it references specific evidence. The citation injector then validates that every citation ID in the output corresponds to a real, retrieved chunk.

### Self-Critique Prompt
Before outputting any generated content, agents run a self-critique pass:

```
Review your generated output and answer the following questions:
1. Does every factual claim cite a specific evidence chunk ID?
2. Does every customer attribution refer to evidence that contains that customer's name?
3. Does every numeric value appear in a retrieved evidence chunk?
4. Are there any sections where the evidence was thin and the PM should be warned?
5. Is the confidence score calibrated correctly given the evidence you retrieved?

If any answer is "no", correct the output before delivering it.
```

---

## Output Schema Enforcement

All agent outputs are validated against a JSON schema before delivery. Schema validation catches:
- Missing required fields (evidence table, confidence score, HITL gate fields)
- Malformed citations (citation IDs that don't match retrieved evidence IDs)
- Outputs that exceed or fall below length bounds
- Confidence scores that are out of range

Outputs that fail schema validation are not delivered to the PM. The agent is retried with an explicit error message about what failed.

---

## Reasoning Trace Storage

Every agent execution stores a reasoning trace in the audit log:
- Each ReAct step (thought, action, observation)
- Each Planner-Executor phase (plan, execute steps, assemble)
- Self-critique output
- Schema validation result
- Final output hash

**PM-visible reasoning:** A simplified version of the reasoning trace is available to PMs from the "How was this generated?" panel on any brief. It shows the key retrieval queries, what evidence was found, and what the agent concluded. This is not the full technical trace — it's a plain-language explanation.

**Engineering-visible reasoning:** The full technical trace (with exact tool calls, inputs, outputs, and timestamps) is available to engineering in the admin dashboard for debugging and quality analysis.

---

## Reasoning Quality Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| Reasoning trace completeness | 100% | All steps logged for all executions |
| Self-critique effectiveness | >80% | % of issues caught by self-critique vs. post-generation validation |
| Citation-binding success rate | >98% | % of claims in output that have valid citation IDs |
| Schema validation pass rate | >99% | % of outputs that pass schema validation on first attempt |
| Parallel execution speedup (PRD, 8 sections) | >3x vs. sequential | Wall-clock time: parallel vs. sequential generation |
