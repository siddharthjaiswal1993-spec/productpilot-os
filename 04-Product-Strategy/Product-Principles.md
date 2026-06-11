# Product Principles

Product principles are the standing decisions that govern how we build. When trade-offs arise — and they always do — these principles define which way we lean. They are not aspirations; they are design constraints.

---

## Principle 1: Evidence Before Recommendation

**The principle:** Every recommendation, score, or generated document must be grounded in retrieved organizational evidence before it is surfaced to the PM. We cite first and conclude second — never the reverse.

**Why:** An AI recommendation that is not grounded in organizational evidence is a guess. Guesses dressed as recommendations are more dangerous than honest uncertainty. A PM who acts on a confident-sounding ungrounded recommendation makes a worse decision than a PM who acknowledges they don't have enough evidence.

**Design implication:** No agent output is surfaced without at least one cited source. If evidence coverage is insufficient for a quality conclusion, the output should say so explicitly — with a confidence score below threshold — rather than generate a plausible-sounding unsupported recommendation.

---

## Principle 2: Human Approval for Every Consequential Action

**The principle:** No action that changes the state of external systems (Jira priority, published PRD, sent stakeholder communication, roadmap commitment) occurs without explicit PM review and approval.

**Why:** ProductPilot OS amplifies PM judgment — it does not replace it. The PM is the accountable party for product decisions. An AI that takes consequential actions without PM approval removes accountability from the human and places it in the system — which is inappropriate for consequential decisions and unacceptable to enterprise buyers.

**Design implication:** Every agent output is a draft until the PM approves. The approval interface is prominent, clear, and fast — but cannot be bypassed for consequential actions. The system may surface recommendations proactively; it does not act on them without authorization.

---

## Principle 3: Transparency Over Black-Box Automation

**The principle:** The PM always knows what the AI is doing, why it did it, and what evidence it used. There are no hidden steps, no opaque reasoning, and no conclusions that cannot be traced to a source.

**Why:** Trust in AI systems is built through transparency, not through confidence in outcomes. A PM who understands why the system scored a priority as high — and can see the evidence — will trust the score and act on it. A PM who gets a high score with no explanation will not. Trust built on transparency is durable; trust built on outcomes alone breaks at the first surprising result.

**Design implication:** Every AI output includes: the evidence sources used, the reasoning steps applied (visible in "explain this" mode), the confidence score with component breakdown, and the ability to drill into any citation. No output is final until the PM can see how it was produced.

---

## Principle 4: Compounding Value

**The principle:** Every interaction with ProductPilot OS should make the next interaction better. The system must be designed for compounding — each signal ingested, each decision recorded, each approval or rejection captured should improve future output quality.

**Why:** A product that is equally useful on Day 1 and Day 365 has no compounding value and no retention moat. A product that is dramatically better on Day 365 — because it has learned the team's priorities, style, and decision patterns — creates a switching cost that is structural, not contractual.

**Design implication:** The knowledge graph, PM feedback model, and eval dataset must be built to compound. Approvals and rejections must generate model improvement signal. The knowledge graph must grow with every signal ingested. The system must surface evidence that every new interaction makes the next one measurably better.

---

## Principle 5: Source Attribution Always Visible

**The principle:** Every piece of information displayed in ProductPilot OS is attributed to its original source — system, author, date, and excerpt — visible to the PM on demand without requiring them to dig.

**Why:** Attribution serves three functions: trust (PM can verify claims), accountability (PM knows who said what), and learning (PM can go deeper on any cited source with one click).

**Design implication:** Citations are displayed inline, not hidden in footnotes. The citation includes: source system icon, author or originator, date, and a 2–3 sentence excerpt. Clicking the citation opens the original source in the connected system. No claim in a generated document is unattributed.

---

## Principle 6: PM Judgment Amplified, Never Replaced

**The principle:** ProductPilot OS exists to amplify what PMs do best — synthesize, judge, and decide — not to automate it away. The product should handle the research, assembly, and first-draft generation. The PM handles the strategy, context, and final approval.

**Why:** PM judgment — the ability to weigh incomplete evidence, navigate organizational politics, and make calls under uncertainty — is irreplaceable by current AI systems. Attempting to automate PM judgment would produce worse outcomes and undermine the trust that makes human-AI collaboration effective.

**Design implication:** Agent outputs are always framed as "recommendations" or "drafts" — never as decisions. The PM's edit and override ability is prominent and fast. PM edits are captured and learned from. The product celebrates PM judgment, not AI automation.

---

## Principle 7: Fail Safely

**The principle:** When the system is uncertain, it should fail toward transparency and caution — not toward confident-sounding outputs. An output that says "I found limited evidence for this claim; confidence score 42/100" is better than a polished output that masks low confidence.

**Why:** The failure mode of high-confidence wrong outputs is worse than the failure mode of low-confidence uncertain outputs. A PM acting on a confident wrong recommendation makes a bad decision. A PM seeing a low confidence score knows to gather more evidence before deciding.

**Design implication:** Hard gates block outputs below confidence threshold from appearing as recommendations. Low-confidence outputs are flagged with clear visual indicators and explanations. The system never generates customer quotes or metrics it cannot cite.

---

## Principle 8: Privacy by Design

**The principle:** Customer data, PII, and sensitive organizational information are protected by architecture — not just by policy. PII is detected and handled at ingestion, before it reaches the vector index or generation pipeline.

**Why:** Enterprise customers have legal obligations around customer data. A breach of these obligations by ProductPilot OS — even as an intermediary — creates legal liability for the customer and destroys trust irreparably.

**Design implication:** PII scanning runs before chunking and indexing. Customer names and contact information are stored separately with access controls. Outputs are checked for PII before display. Audit logs record every data access event.

---

## Principle 9: Integration Depth Over Breadth

**The principle:** A deep, reliable integration with 5 critical data sources is better than a shallow integration with 15 sources. Every integration should be: real-time where needed, reliably maintained, deeply schema-mapped, and bidirectional where the use case requires it.

**Why:** Shallow integrations produce shallow evidence. A Slack integration that only reads the last 100 messages misses the signal that was posted 6 weeks ago. A Jira integration that doesn't capture custom fields misses critical team-specific metadata. Deep integration is the prerequisite for high-quality evidence assembly.

**Design implication:** Launch with Slack, Jira, Salesforce, Zendesk, and Gong as deeply integrated sources before expanding to the next tier. Integration quality trumps integration count.

---

## Principle 10: Speed Respects the PM Workflow

**The principle:** Intelligence that arrives after the decision has been made is not intelligence — it is commentary. The system must be fast enough to be in the PM's workflow, not behind it.

**Why:** If generating an opportunity brief takes 10 minutes, the PM will decide first and consult the brief afterward. The brief then becomes a post-hoc justification, not an evidence foundation. Speed is a prerequisite for the product delivering its core value.

**Design implication:** p95 latency for opportunity brief generation: <90 seconds. p95 latency for PRD draft (first pass): <3 minutes. p95 latency for confidence score: <10 seconds. These are not aspirational — they are design requirements. Architecture decisions must be made to meet them.

---

*These principles are not suggestions. They are the design constraints that govern every product decision. When a proposed feature conflicts with a principle, the principle wins unless there is an explicit strategic reason to make an exception — and that exception must be documented.*
