# Human Evaluations

## Purpose

Human evaluations are the ground truth in the evaluation stack. LLM-as-judge and automated checks are scalable proxies — human evaluation is what they are calibrated against.

Human evals serve three functions in ProductPilot OS:
1. **Calibration source:** Human scores validate that automated metrics are measuring the right thing
2. **Golden dataset creation:** Human evaluators label new golden dataset examples
3. **Qualitative insight:** Human evaluators can articulate why something is good or bad in ways that automated metrics cannot

---

## Human Eval Panel

**Panel composition:**
- 3–5 current beta PM users who volunteer for monthly eval sessions
- 1–2 product team members with PM background
- 1 trust/safety specialist for adversarial and PII eval cases

**Compensation:** Beta PMs participating in eval sessions receive extended feature access and direct product input.

**Conflict of interest controls:** PMs do not evaluate their own team's outputs. PM evaluators are anonymized to each other.

---

## Monthly Human Eval Session

**Duration:** 90 minutes  
**Format:** Structured scoring task + qualitative debrief

**Scoring task (60 minutes):**
- Each evaluator reviews 15–20 sampled outputs
- Outputs are anonymized (no PM name, no company name visible)
- Evaluators score each output on 4 dimensions (1–5 scale):
  - **Faithfulness:** Do the claims match the evidence?
  - **Coherence:** Is the output well-organized and logical?
  - **Helpfulness:** Would this help a PM make a better decision?
  - **Accuracy:** Are specific facts correct? (For outputs where ground truth is known)

**Qualitative debrief (30 minutes):**
- "What made the best outputs good?"
- "What was consistently wrong about the poor outputs?"
- "Were there any outputs you found misleading?"
- "Is there a type of output we're not generating that you'd find useful?"

---

## Structured Scoring Form

For each output, evaluators complete:

```
Output ID: [anonymized ID]
Output Type: [brief / prioritization / PRD / stakeholder / risk]

1. Faithfulness (1-5)
   1 = Contains clear fabrications   5 = All claims directly supported
   Your score: ___ 
   Notes (optional): ___

2. Coherence (1-5)
   1 = Disorganized / illogical      5 = Clear, well-structured
   Your score: ___
   Notes (optional): ___

3. Helpfulness (1-5)
   1 = Would not help PM decide      5 = Clearly actionable
   Your score: ___
   Notes (optional): ___

4. Would you approve this output? (yes / yes with minor edits / yes with major edits / no)
   Your answer: ___

5. Most important thing to improve: [free text]
```

---

## Inter-Rater Reliability

For each session, inter-rater reliability (IRR) is computed:
- Cohen's Kappa for the "Would you approve?" question (categorical)
- Pearson correlation for the numeric 1–5 scores

**Target IRR:** Kappa > 0.6 for approval/rejection; Pearson r > 0.7 for dimension scores

**If IRR is low:** Calibration session added before scoring continues. Evaluators discuss specific outputs where they disagreed to identify scoring ambiguity.

---

## Connecting Human Evals to System Improvement

Human eval outputs feed directly into:

1. **Golden dataset:** High-scoring examples added to Tier 1/2; low-scoring examples added to Tier 3; adversarial examples added to Tier 4.

2. **LLM judge calibration:** Human scores are used to validate and recalibrate the LLM-as-judge scoring model. If the judge systematically disagrees with humans on faithfulness, the judge prompt is revised.

3. **Prompt engineering:** Patterns identified in the qualitative debrief ("the outputs never explain why the confidence score is low") become prompt engineering tasks in the next sprint.

4. **Product roadmap:** Systematic gaps identified in human evals that can't be fixed with prompting (e.g., "there's never enough evidence about technical complexity") are surfaced as product requirements (e.g., "add engineering notes integration as a signal source").

---

## Human Eval Cadence for Model Changes

In addition to monthly sessions, human evals are triggered before major model or prompt changes:

1. Run automated eval stack against golden dataset: must pass
2. Run LLM-as-judge on 100 sampled outputs from candidate model: must meet threshold
3. Run human eval on 30 sampled outputs from candidate model vs. 30 from current model: evaluators blind to which is which
4. If >60% of evaluators prefer candidate model: proceed
5. If split or current preferred: block deployment; investigate

This ensures model upgrades are genuinely improvements, not regressions in disguise.
