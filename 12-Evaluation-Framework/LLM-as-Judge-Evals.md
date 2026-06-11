# LLM-as-Judge Evaluations

## Overview

LLM-as-judge evaluation uses a separate LLM (the "judge") to score the quality of outputs from ProductPilot OS agents. This approach scales human-quality evaluation across large volumes of production traffic — evaluating dimensions like coherence, faithfulness, relevance, and helpfulness that automated rule-based checks cannot assess.

The judge LLM is different from the agent LLM. It is prompted to act as a critical evaluator, not as a helpful assistant. And its scores are calibrated against human evaluator judgments.

---

## When LLM-as-Judge Is Used

| Eval Dimension | LLM-as-Judge? | Rule-Based? | Human? |
|---------------|--------------|-------------|--------|
| Faithfulness (claims match evidence) | Yes (primary) | Partial (numeric check) | Monthly calibration |
| Coherence (well-reasoned output) | Yes (primary) | No | Monthly calibration |
| Relevance (evidence matches query) | Yes (secondary) | Re-ranking score (primary) | Monthly calibration |
| Helpfulness (output is actionable) | Yes (primary) | No | Monthly calibration |
| Citation coverage | No | Yes (primary) | As needed |
| Schema compliance | No | Yes (primary) | No |
| PII in outputs | No | Yes (primary) | Incident review |
| Hallucination (critical) | Yes (secondary) | Yes (primary) | On flagged cases |

---

## Judge Prompt Design

Each evaluation dimension uses a separate judge prompt. Key design principles:

1. **Chain-of-thought reasoning:** The judge is asked to reason before scoring, not just output a score. This improves consistency and makes the reasoning auditable.

2. **Anchored scales:** Each score is accompanied by clear behavioral anchors so the same judge prompt produces consistent scores across different runs.

3. **Evidence-grounded:** For faithfulness, the judge sees the actual evidence chunks, not just the query and output. This prevents the judge from rewarding plausible-sounding outputs that aren't actually grounded.

---

## Faithfulness Judge Prompt

```
You are a rigorous evidence evaluator. Your job is to assess whether the claims in 
[OUTPUT] are faithfully supported by [EVIDENCE_CHUNKS].

Evidence chunks:
{evidence_chunks}

Output to evaluate:
{output}

For each factual claim in the output, classify it as:
A (1.0): Directly stated in the evidence — verbatim or clear paraphrase
B (0.7): Reasonable inference from the evidence — evidence implies this but doesn't state it
C (0.3): Loosely related to evidence — connection is a stretch
D (0.0): Not supported by evidence — contradicts or has no relationship to evidence

List each claim and its classification. Then compute:
faithfulness_score = average(claim scores)

If any claim is classified D, also flag: HALLUCINATION_CANDIDATE: [claim text]

Respond in JSON:
{
  "claim_assessments": [{"claim": "...", "score": float, "evidence_ref": "chunk_id or null"}],
  "faithfulness_score": float,
  "hallucination_candidates": ["claim text"] or []
}
```

---

## Coherence Judge Prompt

```
You are evaluating the quality of a product intelligence brief for a professional 
product manager audience.

Brief to evaluate:
{output}

Score the brief on coherence (1-5 scale):
5: The brief is well-organized, logically flows from problem to evidence to recommendation, 
   all sections connect clearly, and the PM can immediately understand what action to take.
4: Minor organizational issues or slight logic gaps but generally clear and actionable.
3: Some structural problems; PM would need to do some work to understand the recommendation.
2: Significant gaps in reasoning; evidence and recommendation don't connect clearly.
1: Output is disorganized or the recommendation is unclear.

Think through your reasoning step by step. Then output your score and a 1-sentence rationale.

JSON:
{
  "coherence_score": integer (1-5),
  "rationale": "string"
}
```

---

## Helpfulness Judge Prompt

```
You are evaluating whether a product intelligence brief would be actionable for 
a product manager who needs to make a prioritization decision.

Brief:
{output}

PM context (what decision this is for):
{pm_context}

Score helpfulness (1-5 scale):
5: PM has everything needed to make the decision: clear recommendation, strong evidence, 
   business impact quantified, next step specified.
4: PM has most of what's needed; minor gaps in one area.
3: Brief provides useful starting point but PM needs to gather additional information 
   before deciding.
2: Brief raises more questions than it answers; PM is worse positioned to decide.
1: Brief is unhelpful or misleading.

JSON:
{
  "helpfulness_score": integer (1-5),
  "what_is_missing": "string or null"
}
```

---

## Judge Calibration

LLM-as-judge scores are calibrated against human evaluator scores on the golden dataset monthly:

**Calibration process:**
1. Select 50 examples from the golden dataset
2. Have human evaluators score each example on faithfulness, coherence, and helpfulness
3. Run LLM-as-judge on the same 50 examples
4. Compute Pearson correlation between LLM judge scores and human scores
5. If correlation < 0.7 on any dimension: investigate judge prompt; adjust as needed

**Target calibration:** Pearson r > 0.75 for all judge dimensions

**Current calibration status:** Updated in the quarterly eval review document.

---

## LLM-as-Judge Production Pipeline

- **Sample rate:** 10% of all production outputs receive LLM-as-judge scoring
- **Asynchronous:** Judge runs after output is delivered to PM; does not add to generation latency
- **Storage:** All judge scores stored in eval database with input hash, output hash, agent version, model version
- **Alerts:** If any dimension's weekly average drops below threshold → engineering alert

| Dimension | Target Score (1-5) | Alert Threshold |
|-----------|-------------------|----------------|
| Faithfulness | >3.8 | <3.5 over 7-day window |
| Coherence | >4.0 | <3.7 over 7-day window |
| Helpfulness | >3.8 | <3.5 over 7-day window |
