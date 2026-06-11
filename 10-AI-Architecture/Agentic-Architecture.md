# Agentic Architecture

## What Makes This Architecture "Agentic"

An agentic system uses AI models to plan and execute sequences of actions, using tools, to accomplish goals that require more than a single model inference. ProductPilot OS qualifies as agentic because:

1. **Agents use tools:** Each agent queries vector indexes, calls APIs, reads from the knowledge graph, and writes structured outputs — they don't just generate text.

2. **Agents reason over multiple steps:** The Insight Synthesizer doesn't just retrieve and regurgitate. It retrieves, evaluates, decides what's missing, retrieves again, synthesizes, and self-critiques before delivering output.

3. **Agents coordinate:** The output of one agent (an approved brief) becomes the input of another (the Prioritization Agent or PRD Agent). The orchestration layer manages this coordination.

4. **Agents learn from feedback:** PM approval/rejection signals flow back through the evaluation layer to improve future agent outputs.

---

## Agent Design Principles

### 1. Narrow Scope
Each agent is designed to do one job well. The Insight Synthesizer synthesizes. The Prioritization Agent scores. The Risk Watchdog monitors. Cross-cutting concerns (evidence retrieval, citation injection, HITL gating, audit logging) are handled by shared infrastructure, not by individual agents.

**Why:** Narrow agents are easier to evaluate, easier to improve, and fail more gracefully than general-purpose agents.

### 2. Defined Input/Output Contracts
Every agent has an explicit JSON input contract and an explicit output schema. This makes agents composable — the output of one agent satisfies the input contract of another.

**Why:** Defined contracts enable reliable chaining, clear error attribution, and deterministic testing.

### 3. Tools as the Primary Action Mechanism
Agents act by calling tools (vector search, CRM lookup, knowledge graph query, RICE calculator) rather than generating freeform text about what they would do. Tool calls are logged, auditable, and reversible in ways that freeform outputs are not.

**Why:** Tool-centric agents are more reliable and observable than agents that reason entirely in free text.

### 4. Explicit Reasoning Traces
Both ReAct and Planner-Executor reasoning patterns make agent reasoning visible in the audit log. Each step is logged: what the agent reasoned, what action it took, what result it received.

**Why:** Visible reasoning enables debugging, user trust, and evaluation. A PM who can see why an agent produced a recommendation is more likely to trust it — and more likely to identify when it's wrong.

---

## Reasoning Pattern Usage

### ReAct (Reason + Act) Pattern
**Used by:** Insight Synthesizer, Risk Watchdog, Meeting Intelligence, Stakeholder Alignment

**When to use:** When the agent needs to respond to intermediate results and adapt its retrieval strategy. The ReAct pattern works well for synthesis tasks where the right evidence retrieval strategy depends on what earlier evidence reveals.

**Structure:**
```
[REASON]: Analyze current state
[ACT]: Call a specific tool
[OBSERVE]: Process the result
[REASON]: Update reasoning based on result
[ACT]: Call next tool (potentially different from initial plan)
... repeat until output is ready
[OUTPUT]: Generate final structured output
```

### Planner-Executor Pattern
**Used by:** PRD Agent, Prioritization Agent

**When to use:** When the task has a predictable structure that can be planned upfront, and execution can be parallelized across the planned sections. Works well for document generation tasks where sections are independent.

**Structure:**
```
PLAN: Analyze inputs → Generate execution plan (sections to generate, retrieval queries needed per section)
EXECUTE: Run all sections in parallel (with independent retrieval per section)
ASSEMBLE: Collect outputs → Inject citations → Validate → Deliver
```

---

## Agent Context Management

Each agent manages three types of context:

| Context Type | Scope | Storage | Used For |
|-------------|-------|---------|---------|
| Working memory | Single agent execution | In-memory, cleared after run | Current task state, intermediate results, citations pending |
| Session context | PM session | Redis cache | Prior agent outputs in current session, PM preferences, approval state |
| Persistent memory | All sessions | Knowledge graph + vector index | Historical decisions, prior briefs, entity relationships, customer history |

### Context Window Management
For long document processing (e.g., PRD generation), the agent works section by section rather than loading the entire document into a single context window. This avoids "lost in the middle" degradation where LLMs perform poorly on information from the middle of long prompts.

---

## Tool Library

| Tool | Description | Used By | Rate Limit |
|------|-------------|---------|-----------|
| `vector_search(query, top_k, filters)` | Semantic search in tenant's signal corpus | All synthesis agents | 10 req/s per tenant |
| `bm25_search(query, top_k)` | Keyword search for product-specific terms | Insight Synthesizer, PRD Agent | 10 req/s per tenant |
| `knowledge_graph_query(entity_type, filters)` | Query entities and relationships in knowledge graph | All agents | 20 req/s per tenant |
| `crm_lookup(account_name)` | Retrieve ARR, tier, renewal date for account | Insight Synthesizer, Prioritization, Risk Watchdog | 5 req/s per tenant |
| `jira_query(project, filters)` | Retrieve backlog items and current priorities | Prioritization, Risk Watchdog | 5 req/s per tenant |
| `confidence_calculator(evidence_list)` | Compute composite confidence score | Insight Synthesizer | No limit (internal) |
| `rice_calculator(reach, impact, confidence, effort)` | Compute RICE score | Prioritization Agent | No limit (internal) |
| `citation_injector(text, evidence_list)` | Inject inline citations into generated text | All synthesis agents | No limit (internal) |
| `pii_screener(text)` | Detect and redact PII from generated output | All agents with external-facing output | No limit (internal) |
| `audit_logger(event_type, payload)` | Write to immutable audit log | All agents | No limit (internal) |

---

## Agent Health Monitoring

Each agent exposes health metrics tracked by the orchestration layer:

| Metric | Description | Alert Threshold |
|--------|-------------|----------------|
| p95 latency | 95th percentile execution time | >120% of SLA |
| Error rate | % of executions that fail | >5% over 1 hour |
| PM acceptance rate | % of outputs approved without major edit | <60% over 7 days |
| Hallucination rate | % of outputs with unverifiable claims detected | >3% over 7 days |
| Tool call failure rate | % of tool calls that return errors | >10% over 1 hour |

Alerts trigger engineering notification and are visible in the admin dashboard.
