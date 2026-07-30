# Renal impairment quick checklist

Use live. If an item cannot be answered, that is the finding.

## Frame the problem
1. Which of the seven axes does the model represent explicitly / implicitly / not at all?
   (filtration, secretion, reabsorption, non-renal CL, protein binding, distribution and
   haemodynamics, absorption and gut)
2. What is the actual decision - dedicated study, waiver, dose recommendation, label text?
3. Route chosen and justified: dedicated study, PopPK covariate, PBPK, or combination.
4. Four acceptance conditions checked: characterised ADME (mass balance + phenotyping),
   understood non-linearity and single-to-multiple dose, DDI data constraining fm, modest
   predicted shift.

## Build
5. Healthy-adult model verified on IV and oral before any perturbation.
6. Clearance decomposed: filtration, secretion, reabsorption, each fm, biliary,
   extracorporeal. fe and fm measured, not fitted.
7. Severity changes applied as systems parameters (GFR, organ volumes and flows,
   haematocrit, albumin, AAG, enzyme and transporter abundance, gastric residence) - not as
   drug-parameter adjustments.
8. Non-renal clearance handled by one of: proteomics-informed, TDM/top-down-informed, or
   explicitly bounded range.

## Arithmetic falsification tests
9. Filtration ratio CLR / (fu x GFR): approximately 1 = filtration, greater than 1 =
   secretion, less than 1 = reabsorption. Consistent with the assigned mechanism?
10. fe ceiling: predicted maximum AUC increase below 1/(1-fe)? If not, name the mechanism.
11. Monotonic exposure increase across normal to mild to moderate to severe to ESRD?
12. Non-renal floor: is ESRD half-life physiologically plausible?
13. Unbound versus total: is fu expected to move, and is unbound exposure reported?
14. Vss and half-life changes consistent with the CL and V changes?
15. CL versus eGFR plot: is the intercept (non-renal CL) right, not just the slope?
16. Prediction error plotted against severity - any monotonic drift? (fingerprint of the
    single-scalar assumption)

## Special settings
17. Dialysis modelled as an explicit time-varying clearance term, modality-specific
    (intermittent HD / CRRT / peritoneal), with rebound and dose timing simulated.
18. AKI modelled with acute staging and time-varying function - not a CKD template.
19. Augmented renal clearance considered in critically ill populations.
20. Paediatric renal dysfunction referenced to age-appropriate normals, layered on
    maturation.
21. Combined renal-plus-hepatic: systems changes applied once, not twice.
22. Body-composition-driven bias in creatinine-based eGFR considered.

## Biomarkers and confounders
23. Endogenous biomarker data used or its absence noted (pyridoxic acid for OAT1/3, NMN and
    N-methyladenosine for OCT2/MATE, coproporphyrin I for OATP1B).
24. Serum creatinine rise checked against an independent filtration marker before it is
    interpreted as lost GFR (pseudo-AKI / transporter inhibition artefact).
25. Urinary-ratio phenotyping in CKD treated as confounded.

## Report
25a. **Acceptance criteria agreed with the modeller, tied to intended purpose, recorded BEFORE
    metrics were computed** - or flagged as defaulted to the twofold / AFE<1.5 convention.
26. AUC and Cmax judged against the agreed criterion **per stratum, never pooled**; AFE and
    AAFE both per stratum. Signed error plotted against eGFR - a trend is a missing mechanism,
    scatter is noise.
26a. Metric definitions, additional GoF lenses and the output schema taken from
    `pbpk-model-build-workflow` (`reference/gof-output-schema.md`).
27. Sensitivity analysis over fe, fu in severe impairment, and non-renal CL change.
28. Report states: exposure change, mechanism split, top sensitivity, falsifying
    observation.
29. Limitations name every unrepresented axis.

