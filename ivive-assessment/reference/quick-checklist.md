# IVIVE quick checklist

## The chain
1. Write the full scaling chain with units and sources for every term.
2. Incubational binding measured or predicted and applied.
3. Biological scaling factors stated with their provenance (protein per gram, cells per
   gram, organ mass) - and matched to the system actually used.
4. Organ model structure matched to the rate-determining step.
5. Blood-to-plasma ratio and plasma unbound fraction measured for highly bound compounds.

## Assay integrity
6. Solubility in medium confirmed; concentration range adequate and within solubility.
7. Stability, recovery and non-specific binding assessed and corrected.
8. Linearity with incubation time and protein concentration confirmed.
9. Cytotoxicity assessed in cell-based systems.
10. For low-clearance compounds: is the measured rate above the assay noise floor, or is
    it a floor being reported as a value?

## Mechanism coverage
11. Can the chosen system express the operative mechanism? Justify.
12. Non-CYP enzymes considered (UGT, SULT, AO, carboxylesterase, FMO).
13. Extrahepatic routes considered (intestine, kidney, lung, plasma).
14. Biliary excretion considered; sandwich-cultured system used if it matters.
15. Passive and active permeation separated; expression-based transporter scaling used.

## Falsification
16. Predicted hepatic clearance below hepatic blood flow; implied extraction ratio
    consistent with observed or expected first-pass.
17. Under-prediction pattern analysed by chemotype, clearance range and binding, rather
    than corrected with a blanket factor.
18. Independent estimate (allometry, top-down, series precedent) compared; disagreement
    investigated as a mechanism signal.

## Reporting
18a. **Acceptance criteria agreed with the modeller, tied to intended purpose, recorded BEFORE
    metrics were computed.** IVIVE has no single conventional bar, which makes an unstated
    criterion easier to slide past a reviewer - not harder.
18b. **AFE (signed) reported alongside AAFE (unsigned).** Systematic under-prediction is the
    default IVIVE failure mode, so direction is the diagnostic: bias means a missing scaling
    factor, scatter without bias means assay variability. AAFE alone conceals this.
18c. Fold error reported as a distribution **across compounds and pathways**, not aggregated -
    aggregate performance passes while one pathway class fails entirely.
18d. Where an empirical scaling factor was applied, prediction reported **with and without it**.
    Metric definitions and output schema from `pbpk-model-build-workflow`
    (`reference/gof-output-schema.md`).
19. Dominant uncertainty term named; range from bounding it reported.
20. Any empirical factor flagged as compound-and-system-specific, non-transferable.
21. Lower structural confidence stated explicitly for non-CYP and extrahepatic routes.
22. The single experiment that would most reduce the uncertainty is named.

