# PRD Agent

## Purpose

The PRD Agent generates structured, cited product requirements documents from PM-approved opportunity briefs and organizational evidence. It uses a Planner-Executor pattern: first planning the PRD structure based on the brief and team template, then executing each section with targeted evidence retrieval, before assembling the full document with citation injection. The PRD Agent is the primary time-saving agent for PM documentation workflows.

---

## Inputs

| Input | Source | Format | Required |
|-------|--------|--------|---------|
| Approved opportunity brief | Brief approval record | Structured JSON with evidence table | Yes |
| PM team PRD template | Team configuration | Markdown template with section definitions | Yes (uses default if not configured) |
| Customer signals (relevant to feature area) | Vector search | Top 8–12 evidence chunks | Yes |
| Technical context | Knowledge graph + Jira | Prior related features, technical constraints, dependency notes | If available |
| Existing PRDs in similar area | Knowledge graph | Prior PRD structures for consistency | If available |
| Edge case history | Knowledge graph | Edge cases from similar prior features | If available |

---

## Outputs

| Output | Format | Recipient |
|--------|--------|----------|
| PRD first draft | Structured markdown → rendered editor | PM editing interface |
| User stories with citations | Structured user story format with citation panel | Included in PRD |
| Acceptance criteria | Given/When/Then format per user story | Included in PRD |
| Edge case suggestions | Table with scenario, expected behavior, source | Included in PRD |
| Out-of-scope items | Explicit exclusion list with rationale | Included in PRD |
| Success metrics | Draft metrics with measurement method | Included in PRD |

---

## Planner-Executor Pattern

```
PLAN PHASE:
1. Read approved opportunity brief — understand the scope, evidence base, and customer need
2. Load PM team's PRD template — understand required sections and ordering
3. Identify evidence coverage gaps — which sections have strong evidence, which are thin
4. Plan retrieval queries — section-by-section retrieval strategy
5. Estimate PRD complexity — how many user stories, what edge case surface area

EXECUTE PHASE (parallel section generation):
Section: Problem Statement → Retrieve: evidence from brief + knowledge graph context
Section: User Stories → Retrieve: per-story evidence from signal corpus (1 retrieval per story)
Section: Acceptance Criteria → Generate from user stories (no separate retrieval needed)
Section: Edge Cases → Retrieve: similar feature edge cases from knowledge graph
Section: Success Metrics → Retrieve: team metric conventions from prior PRDs + brief metrics
Section: Out of Scope → Infer from brief scope + knowledge graph exclusion history

ASSEMBLE PHASE:
1. Inject citations into all factual claims
2. Cross-check: all user stories have at least 1 citation
3. Cross-check: acceptance criteria cover main paths + key edge cases
4. Confidence check: flag sections with thin evidence
5. Deliver assembled PRD to PM editing interface
```

---

## Tools Used

- **Vector search (per section):** Targeted evidence retrieval for each PRD section
- **Knowledge graph query:** Retrieve related prior PRDs, features, edge cases, and technical constraints
- **Template engine:** Render PRD sections according to team template
- **Citation injector:** Link every factual claim to retrieved source with inline citation format
- **Edge case generator:** Query knowledge graph for edge cases from similar prior feature implementations

---

## Memory

- **Working memory:** Current PRD generation state — section completion status, evidence used, citations pending
- **Semantic memory (Knowledge Graph):** Prior PRDs, feature entities, edge case history, technical constraints

---

## Autonomy Level

**Draft only.** The PRD Agent generates a draft that is clearly labeled "AI Draft — Pending PM Review" throughout the editing interface. No section is considered final until the PM has reviewed and approved the full PRD.

Write access: add PRD draft to PM's workspace; add new feature entities to knowledge graph from PRD scope.

---

## Guardrails

1. **No fabricated customer quotes:** Every customer quote must be a direct verbatim excerpt from a cited source. If no direct quote is available for a user story, the story cites the general signal without a quote.

2. **Template compliance required:** If a required section is missing from the generated draft, the section is created with a "[Required section — insufficient evidence to generate. PM must complete.]" placeholder. The PRD is never delivered as "complete" with required sections empty.

3. **Scope containment:** Out-of-scope items must be explicitly listed. The agent is designed to over-specify out-of-scope rather than under-specify — it is better to clarify something is out of scope than to leave it ambiguous.

4. **Confidence-annotated sections:** Each PRD section includes a small confidence indicator. Sections generated with thin evidence are marked with a yellow badge; PM is advised to verify these sections specifically.

---

## Failure Modes

| Failure | Detection | Response |
|---------|-----------|---------|
| Template not configured | Configuration check at start | Use default template; notify PM: "No team template configured. Using default ProductPilot OS PRD template." |
| Evidence insufficient for a required section | Evidence relevance score below threshold after 2 retrieval attempts | Generate section header with: "[Section] — Evidence thin. PM: please review and complete this section." |
| PRD generation exceeds 3-minute p95 | Agent timeout monitor | Deliver partial PRD with incomplete sections flagged; PM can regenerate specific sections |
| Citation injection finds no source for a claim | Post-generation citation validation | Remove claim or mark with "[Source not found — PM verify]"; log for model improvement |

---

## Human Approval Boundary

The PRD Agent's output is a first draft. It is always labeled as such. The PM must:
1. Review the full PRD
2. Verify key citations
3. Complete any flagged low-evidence sections
4. Approve the PRD explicitly before it can be shared with engineering

The PRD is never sent to engineering, shared in Confluence, or used as a sprint planning input without explicit PM approval.

---

## Quality Metrics

| Metric | Target |
|--------|--------|
| PM acceptance rate (PRD approved without major revision) | >70% at 30-day cohort |
| Sections with citations | 100% (no uncited factual sections) |
| PM edit time on generated PRD | <45 minutes average |
| Edge case suggestions accepted by PM | >50% acceptance rate |
| PRD generation latency (p95) | <3 minutes |
