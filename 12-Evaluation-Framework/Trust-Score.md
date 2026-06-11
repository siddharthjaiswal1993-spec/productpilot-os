# Trust Score

## Definition

The Trust Score is a composite metric that captures the overall trustworthiness of ProductPilot OS AI outputs at a given point in time. It synthesizes multiple quality and safety dimensions into a single 0–100 score that can be tracked over time, compared across model versions, and presented in stakeholder reviews.

The Trust Score is not a metric visible to end users. It is an internal product quality metric used by the product and engineering team.

---

## Trust Score Components

The Trust Score is a weighted composite of six measurable dimensions:

| Component | Weight | What It Measures |
|-----------|--------|-----------------|
| PM Acceptance Rate | 35% | % outputs approved without major edit — primary PM quality signal |
| Faithfulness Score | 25% | LLM-as-judge faithfulness: claims grounded in evidence |
| Citation Coverage | 15% | % claims with valid citations |
| Hallucination Rate (inverse) | 15% | (1 - hallucination rate) — zero tolerance for critical hallucinations |
| Calibration Accuracy | 10% | Confidence score correlation with actual PM acceptance rate |

**Formula:**
```
Trust Score = 
  (PM_Acceptance_Rate × 0.35) +
  (Faithfulness_Score_normalized × 0.25) +
  (Citation_Coverage × 0.15) +
  ((1 - Hallucination_Rate) × 0.15) +
  (Calibration_Accuracy_normalized × 0.10)

All components normalized to 0–100 scale before weighting.
```

---

## Score Interpretation

| Trust Score | Status | Meaning |
|-------------|--------|---------|
| 90–100 | Excellent | System is highly reliable; appropriate for high-stakes decisions |
| 80–89 | Strong | System produces trustworthy outputs; minor quality gaps |
| 70–79 | Adequate | System is useful; some quality issues to address |
| 60–69 | Developing | Quality issues are affecting PM adoption; improvement sprint needed |
| <60 | Critical | Quality problems require immediate investigation; deployment should be reviewed |

---

## Trust Score Targets

| Milestone | Target Score |
|-----------|-------------|
| Design partner launch (Month 1) | >65 |
| V1 general availability | >72 |
| V2 team intelligence | >80 |
| Enterprise tier launch | >85 |
| Mature product | >88 |

---

## Trust Score Monitoring

**Measurement cadence:** Trust Score computed weekly from trailing 30-day data  
**Dashboard location:** Engineering quality dashboard (not PM-facing)  
**Alert threshold:** If Trust Score drops >5 points in a 7-day window → P2 alert to engineering and product leads

**Component breakdown in dashboard:**
The Trust Score dashboard shows:
- Composite score with 30-day trend line
- Component scores with individual trend lines
- Which component is most responsible for any recent change
- Historical version-over-version comparison

---

## Trust Score and Model Changes

When evaluating a model or prompt change, the Trust Score provides a single decision-making metric:

1. Run candidate model on golden dataset
2. Compute projected Trust Score
3. Compare to production Trust Score
4. If projected > production by ≥2 points: green light for shadow deployment
5. If projected ≈ production (within 2 points): neutral; proceed with close monitoring
6. If projected < production by >2 points: block deployment; investigate regressions

This prevents "improving one metric at the expense of another" — a model change that improves PM Acceptance Rate but increases hallucination rate would show as a net Trust Score decrease.

---

## Trust Score Communication

The Trust Score is used in:
- Monthly product quality review (internal)
- Enterprise customer business reviews (as "AI quality metric")
- Investor updates (as evidence of systematic quality management)

In external contexts, the Trust Score is presented with component breakdowns — not as a black box number, but as evidence of a rigorous, multi-dimensional approach to AI quality.
