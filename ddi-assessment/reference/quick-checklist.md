# DDI quick checklist

Use live, during model building or review. One line per item; if you cannot answer it,
that is the finding.

## Before simulating
1. Mechanism class named (reversible / TDI / induction / transporter / interplay /
   DGI / auto- or metabolite-mediated) and evidence stated.
2. fe and fm per pathway from mass balance and phenotyping - written down, not implied.
3. In vitro potency corrected for incubational binding. Blood-to-plasma ratio available.
4. Major circulating metabolites screened as objects and as perpetrators.
5. Object model verified standalone: IV, oral, single dose, multiple dose, dose range.
6. Perpetrator model verified against an index object with known fm.

## While simulating
7. No re-fitting at the coupling step.
8. Unbound concentration at the correct site used (hepatic inlet for oral perpetrators,
   enterocyte for gut wall).
9. Gut wall represented for CYP3A and P-gp objects.
10. Time course simulated, not just steady-state ratio.

## Arithmetic falsification tests
11. Predicted AUC ratio below 1/(1-fm)? If not, name the extra mechanism.
12. Back-calculated fm from any strong-inhibitor data consistent with the model fm?
13. Cmax ratio pattern consistent with the assigned mechanism?
14. Onset/offset timescale consistent with the assigned mechanism?
15. Saturation behaviour sensible across the perpetrator dose range?

## Before reporting
15a. **Acceptance criteria agreed with the modeller, tied to intended purpose, and recorded
    BEFORE metrics were computed** - or explicitly flagged as defaulted to the twofold
    convention.
16. Independent validation pairs reported; per-pair and aggregate GMFE, **with the GMFE
    formula printed next to the value** (the term is used inconsistently in the literature).
17. Predicted/observed AUC and Cmax reported **per pair as a fold-error distribution**, not
    only as an aggregate, and judged against the agreed criterion. AFE (signed) and AAFE
    (unsigned) both reported - AFE alone cancels opposing errors.
17a. Full metric set, additional GoF lenses and the VPC output schema taken from
    `pbpk-model-build-workflow` (`reference/gof-output-schema.md`), not improvised.
18. Sensitivity analysis on potency parameter, fm, and unbound fraction assumptions.
19. Population extrapolations (genotype, age, impairment, ethnicity) flagged as
    predictions with their own uncertainty.
20. Limitations list every mechanism not represented.
21. Report states: ratio, mechanism split, top sensitivity, falsifying observation.

