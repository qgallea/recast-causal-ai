## Referee 3 Report: Robustness and Replication
**Round:** 1
**Overall verdict:** Major concerns

### Blocking issues
- None

### Major issues

1. **Replication fidelity for Panel C (Business density) is very poor: 177.9% relative deviation.** The replicated coefficient is -0.195 vs. the published -0.070 (from Baiardi & Naghi 2024). This is not a small discrepancy attributable to rounding or standard-error differences --- the replicated estimate is nearly three times the published value in absolute terms. The paper attributes this entirely to sample-size differences (N=53 vs. N=60), but a drop of only 7 observations should not flip a coefficient from -0.070 to -0.195. The authors must investigate whether the sample composition differs (e.g., which countries are dropped) and whether the coefficient is driven by influential observations in the smaller sample. A leverage/influence diagnostic (Cook's distance or DFBETAS) for the Panel C specification is needed.

2. **Panel A (Investment ~ five-year effective rate) shows sign instability across DML learners.** Two of four learners (LassoCV: +0.010, ElasticNet: +0.027) produce positive coefficients, while the other two (RandomForest: -0.118, GradientBoosting: -0.132) produce negative coefficients. The OLS estimate is -0.089. The sign instability is correctly flagged in the diagnostics (`dml_direction: FAIL`) and discussed in the paper text (Section 3.2), but the paper does not adequately explain *why* the linear learners flip sign while tree-based learners do not. The nuisance R-squared values for LassoCV on this specification are deeply negative (R2_outcome = -0.236, R2_treatment = -0.347), which means the Lasso nuisance models are worse than a constant predictor. The paper should explicitly state that the DML estimates from Lasso and ElasticNet for Panel A are unreliable and should not be interpreted, rather than simply noting the sign change.

3. **Sample-size discrepancy is underexplained.** The paper states the sample drops from 50-61 (reference) to 42-53 (ours) "due to missing values in replication data for some controls." This is a potentially consequential 13-18% sample loss. The paper should report: (a) which specific control variables cause the most missingness, (b) whether the dropped observations are geographically or economically non-random, and (c) a comparison of summary statistics between the full and reduced samples. Without this information, it is impossible to assess whether the replication deviations are benign or reflect systematic sample selection.

### Minor issues

1. **No original-paper reference values are reported for the statutory or first-year effective rate specifications.** The replication_results.json file shows `"match": null` for 8 of 12 specifications, meaning no published benchmarks are available for comparison. The paper correctly notes that reference values come only from Baiardi & Naghi (2024) for the five-year effective rate, but this limitation should be more prominently flagged. Without benchmarks, the reader cannot assess replication fidelity for two-thirds of the specification matrix.

2. **The forest plot does not include the published reference estimate as a separate row.** The plot shows "OLS (replicated)" but not the Baiardi & Naghi (2024) or original Djankov et al. (2010) published point estimates. Adding a "Published" row for each panel (where available) would allow the reader to visually assess the replication gap directly.

3. **Panel A DML table reports LassoCV as "preferred learner" in the JSON (`preferred_coef: 0.009672`), but the paper text correctly focuses on tree-based learners for interpretation.** There is a disconnect between the automated pipeline's preferred-learner selection (which chose LassoCV based on some criterion) and the paper's narrative. The JSON metadata should be updated to reflect that LassoCV is inappropriate for Panel A, or the paper should explicitly note the override.

4. **HC1 standard errors are correctly used throughout, consistent with the `se_type` field in replication_results.json.** No issues here.

5. **Sample sizes in the LaTeX tables are consistent with the JSON files.** Panel A-C show N=53 and Panel D shows N=42, matching `replication_results.json` and `dml_results.json`. No inconsistencies detected.

6. **Missing robustness checks.** Given the small N, the following would strengthen the analysis:
   - Leave-one-out or jackknife estimates for the primary specification, to assess sensitivity to individual countries.
   - A reduced-control specification (e.g., dropping the 3-4 controls with the most missing values) to recover a larger sample and check whether results are robust to the sample-size/control tradeoff.
   - A brief note on whether results change materially if the treatment variable is winsorized, given that tax-rate outliers may be influential in a sample of ~50 countries.

### Comments to the authors

The replication exercise is comprehensive in scope, covering all 12 specifications from the original Table 5D. The finding that all 12 OLS coefficients have the correct negative sign is reassuring and suggests the data and code pipeline are fundamentally sound. The close replication of Panel D (Entry rate ~ five-year effective rate: -0.128 vs. -0.133, 3.5% deviation) is a strong anchor point. However, the Panel C deviation of 177.9% is troubling and cannot be dismissed as a simple sample-size artifact without further investigation. A coefficient of -0.195 that is nearly three times the published value of -0.070 raises the question of whether specific influential observations in the reduced sample are driving the result.

The DML extension delivers clear value for Panels B, C, and D, where multiple learners produce significant negative coefficients while OLS is insignificant. This is exactly the pattern expected when DML handles high-dimensional controls more efficiently than kitchen-sink OLS. The strongest results are in Panel D (Entry rate), where GradientBoosting yields -0.228 (p=0.002) compared to OLS -0.128 (p=0.34) --- a meaningful gain in both magnitude and precision. For Panel B (FDI), ElasticNet yields -0.148 (p=0.050), crossing the significance threshold that OLS does not reach. These results are robust across learners: in Panel B, all four DML learners agree on sign and approximate magnitude (-0.120 to -0.163); in Panel D, three of four are significant.

Panel A (Investment/GDP) is the weak point. The sign instability across DML learners, combined with deeply negative nuisance R-squared values (average R2_outcome = -0.096, with LassoCV at -0.236), means the DML estimates for this panel are not informative. The paper discusses this honestly but should go further: the Lasso and ElasticNet estimates for Panel A should be explicitly flagged as unreliable rather than included in the results table without qualification. It would also be useful to note that the Baiardi & Naghi (2024) reference DML estimate for Panel A (-0.178, SE 0.096) is itself only marginally significant, suggesting that the investment outcome has inherently low signal in this cross-country setting.

The GATE analysis finding of no significant heterogeneity is not surprising given the sample size and is appropriately caveated with a low-power disclaimer. The wide GATE confidence intervals (e.g., Q1: [-0.821, 0.498]) confirm that the data simply cannot support heterogeneity analysis with N approximately equal to 50.
