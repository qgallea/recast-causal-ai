# Final Review Report

**Paper:** The Effect of Corporate Taxes on Investment and Entrepreneurship
**Original authors:** Djankov, S., Ganser, T., McLiesh, C., Ramalho, R. and Shleifer, A. (*American Economic Journal: Macroeconomics*, 2010)
**Rounds completed:** 1 of 3
**Final verdict:** Minor manual revision needed

---

## Overall Assessment

This RECAST of Djankov et al. (2010) replicates the full 12-specification matrix from the original paper's Table 5D (4 outcomes x 3 tax measures) and extends each specification with a DoubleML Partial Linear Regression using four ML learners. The replication is partially successful: all 12 OLS coefficients have the correct negative sign, and one of the four benchmarkable specifications (Entry rate, Panel D) replicates within the 20% threshold (3.5% deviation). The other three benchmarkable specifications deviate by 27.9% to 177.9%, driven by a sample-size gap (N = 42-53 in our data vs. 50-61 in the reference) that appears to reflect non-random missingness in institutional control variables.

The DML extension delivers genuine value for three of the four outcome panels. For FDI (Panel B), business density (Panel C), and entry rate (Panel D), all four ML learners agree on the negative sign, and multiple learners produce statistically significant estimates where OLS does not -- the clearest gain being in Panel D, where Gradient Boosting yields -0.228 (p = 0.002) compared to OLS -0.128 (p = 0.34). For Investment/GDP (Panel A), however, the DML extension is uninformative: two learners flip sign, nuisance R-squared values are deeply negative, and the automated preferred-learner selection (LassoCV) produces a near-zero, sign-reversed coefficient of +0.010 (p = 0.94). The paper's revised text correctly flags this.

Would this be publishable as a short note? In its current form, close. The core finding -- that DML recovers significant negative tax effects in three of four panels where OLS is insignificant under kitchen-sink controls -- is a genuine contribution that extends Baiardi & Naghi (2024). The identification discussion, after revision, is honest about the limits of both OLS and DML in this cross-country setting. The remaining issues are minor and manageable with a careful manual pass.

---

## What Was Solved During Revision

| Issue | Raised (round) | Resolved (round) | How |
|-------|----------------|-------------------|-----|
| Estimand undefined; causal vs. associational language inconsistent | 1 | 1 | Added formal estimand definition (Eq. 1) and consistent language policy |
| Selection-on-observables not defended; DML does not solve OVB | 1 | 1 | Added "Threats to identification" paragraph and DML caveat in Section 3.1 |
| Panel A Lasso/ElasticNet flagged as unreliable | 1 | 1 | Rewrote Panel A bullet; italicised warning on Lasso/ElasticNet estimates |
| Panel C replication deviation (177.9%) underexplained | 1 | 1 | Added dedicated paragraph on influential observations and right-skewed outcome |
| Sample-size discrepancy underexplained | 1 | 1 | Identified specific controls causing missingness; noted non-random attrition |
| "DML strengthens" language too strong given negative nuisance R-squared | 1 | 1 | Replaced with "broadly consistent with" and "supportive, though not dispositive" |
| Best-learner selection by p-value introduces selection bias | 1 | 1 | Added paragraph and footnote documenting the criterion and its limitation |
| GATE grouping variable unjustified | 1 | 1 | Added justification and exploratory-analysis caveat |
| N_rep = 3 limitation | 1 | 1 | Noted as limitation; recommended N_rep = 5-10 |
| K = 5 borderline for Panel D | 1 | 1 | Noted in diagnostics; suggested K = 3 robustness check |
| CATE summary statistics ambiguous | 1 | 1 | Clarified as smoothed conditional effects, not unit-level heterogeneity |
| HC1 vs. DML SEs not distinguished | 1 | 1 | Added explanation of Neyman-orthogonal score derivation |
| Missing reference values for 8/12 specs | 1 | 1 | Prominently flagged in replication assessment |
| Forest plot omits published estimates | 1 | 1 | Added published values in figure caption |
| Missing robustness checks | 1 | 1 | Acknowledged as limitation; listed desirable checks |
| Panel D sample drop needs identification comment | 1 | 1 | Addressed jointly with sample-size discrepancy |

All 7 major and 9 minor issues from round 1 were resolved through prose edits. No notebook re-runs were required.

---

## Remaining Issues

| # | Issue | Severity | Action needed |
|---|-------|----------|---------------|
| 1 | Panel C replication deviation (177.9%) has no formal diagnostic. The paper discusses influential-observation risk but no Cook's distance or DFBETAS analysis was run. | Major | Run leverage diagnostics on Panel C (Business density ~ effective_5yr) manually. Identify which of the 7 missing countries are high-leverage and report whether the -0.195 estimate is driven by one or two observations. |
| 2 | Preferred-learner designation in `dml_results.json` still reports LassoCV with `preferred_coef: 0.010` for the primary specification (Panel A, effective_5yr). The paper text overrides this narratively but the metadata is misleading. | Minor | Update `preferred_learner` and `preferred_coef` in `dml_results.json` to reflect Random Forest (-0.118) for the primary specification, or add a `narrative_preferred` field. |
| 3 | No reduced-control specification was tested. With N = 42-53 and 12 controls, a specification with fewer controls (e.g., dropping the 3-4 variables with the most missingness) would recover a larger sample and test robustness. | Minor | Run a supplementary OLS and DML with 8 controls (dropping emplo_i2004, legalform, avinformal3, seign2004) as a robustness check. |
| 4 | Causal forest extension was configured in config.yaml (`enabled: true`) but `causal_forest_results.json` does not exist. The causal forest stage was never executed. | Minor | Either run `/recast-cf` to produce the causal forest analysis, or set `enabled: false` in config.yaml to avoid confusion. |

*Severity: **Blocking** = must fix before sharing. **Major** = should fix. **Minor** = optional.*

---

## Key Results

### Primary specification (Panel A: Investment ~ five-year effective rate)

| Specification | Estimate | SE | 95% CI | N |
|---------------|----------|----|--------|---|
| Published OLS (Djankov et al. 2010) | -0.189 | 0.118 | [-0.420, 0.042] | 61 |
| Published DML Lasso (Baiardi & Naghi 2024) | -0.178 | 0.096 | [-0.366, 0.010] | 61 |
| Our replication (OLS, HC1) | -0.089 | 0.145 | [-0.372, 0.195] | 53 |
| DML LassoCV (preferred by pipeline) | +0.010 | 0.135 | [-0.256, 0.275] | 53 |
| DML Random Forest (substantively preferred) | -0.118 | 0.084 | [-0.282, 0.047] | 53 |
| DML Gradient Boosting | -0.132 | 0.107 | [-0.321, 0.078] | 53 |

**Replication check:** FAIL -- 53.0% relative deviation vs. published OLS for Panel A (worst deviation: 177.9% for Panel C). Only 1/4 benchmarkable specs within 20% threshold (Panel D: 3.5%).

**DML shift (Panel A):** The tree-based DML estimates (RF: -0.118, GBM: -0.132) are smaller in magnitude than the published OLS (-0.189) but preserve the negative sign. The linear DML estimates (Lasso: +0.010, ElasticNet: +0.027) flip sign and are unreliable due to deeply negative nuisance R-squared. The DML extension is uninformative for Panel A.

### Strongest DML results (Panels B-D)

| Panel | Outcome | Treatment | OLS (ours) | Best DML | Learner | p-value | N |
|-------|---------|-----------|------------|----------|---------|---------|---|
| B | FDI/GDP | Effective 1yr | -0.131 (p=0.05) | -0.187 | ElasticNet | 0.010 | 53 |
| B | FDI/GDP | Effective 5yr | -0.121 (p=0.19) | -0.148 | ElasticNet | 0.050 | 53 |
| C | Business density | Effective 1yr | -0.199 (p=0.07) | -0.265 | LassoCV | 0.004 | 53 |
| C | Business density | Effective 5yr | -0.195 (p=0.07) | -0.247 | LassoCV | 0.005 | 53 |
| D | Entry rate | Statutory | -0.139 (p=0.12) | -0.184 | RandomForest | 0.001 | 42 |
| D | Entry rate | Effective 5yr | -0.128 (p=0.34) | -0.228 | GradientBoosting | 0.002 | 42 |

**DML shift (Panels B-D):** The DML estimates are consistently larger in magnitude than OLS by 15-78% and reach statistical significance in many specifications where OLS does not. This pattern is consistent across learners and panels: all four DML learners agree on the negative sign for all 9 specifications in Panels B-D. The strongest gains are in Panel D (Entry rate), where DML estimates are roughly 30-60% larger than OLS and highly significant.

---

## Notes for the Reader

- **Identification is inherited, not improved.** DML relaxes functional-form assumptions on the nuisance functions but requires the same conditional-independence (selection-on-observables) assumption as OLS. The original paper acknowledges omitted-variable concerns (GDP per capita, government expenditure, tax competition). The DML extension does not resolve these; it only addresses the problem that 12 controls in a sample of ~50 countries may introduce functional-form misspecification under OLS.

- **Nuisance model quality is poor throughout.** Average out-of-sample R-squared for both the outcome and treatment nuisance models is negative across all learners. This means the ML models predict worse than the sample mean. Under these conditions, DML standard errors may not satisfy the regularity conditions for asymptotic validity, and the reported p-values should be treated as indicative rather than exact. The consistent sign pattern across learners in Panels B-D is more informative than any individual p-value.

- **The 177.9% replication deviation in Panel C is unresolved at a formal level.** The paper discusses influential-observation risk qualitatively but no leverage diagnostics have been run. Before citing the Panel C results, a manual check (Cook's distance or leave-one-out) is needed to determine whether the large replicated coefficient (-0.195 vs. published -0.070) reflects genuine signal or a handful of influential countries in the reduced sample.

- **Sample-size loss is non-trivial.** Our data yields N = 42-53 vs. 50-61 in the reference, a 13-18% reduction. The missingness is concentrated in institutional control variables (employment rigidity, legal formalism, average informality) that vary with development level, so the dropped countries are likely systematically different from those retained. This non-random attrition could bias results in either direction.

- **The pipeline cannot verify data provenance or variable construction.** The replication uses `data_taxes.csv` as provided. Whether this exactly matches the original Djankov et al. (2010) dataset, or whether variable definitions (e.g., "five-year effective rate") are computed identically to the original paper, cannot be confirmed without access to the original authors' code and data files.
