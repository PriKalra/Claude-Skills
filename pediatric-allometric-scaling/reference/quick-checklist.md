# Paediatric scaling quick checklist

Use live. If an item cannot be answered, that is the finding.

## Frame
1. Age bands actually required, including preterm - stated up front.
2. Clearance model written as size x maturation x organ function x disease, each term
   separately justified.
3. Dominant elimination mechanism identified per age band (filtration, secretion, specific
   CYP, phase 2, biliary, catabolic for proteins).
4. Scaling approach chosen per band and justified: size-only, size plus maturation, or full
   physiological model.

## Size
5. Exponent fixed with justification, or estimated with a stated age range and confidence
   interval. No free exponent across a maturation-dominated range.
6. Supra-unity exponent in young data treated as a maturation signal, not a size law.
7. Volume scaled separately from clearance; half-life ordering across ages checked for
   consistency.
8. Weight-based dosing recognised as an assumption and its exposure consequences simulated.

## Maturation tracks (run each explicitly)
9. Enzyme ontogeny, pathway by pathway. Provenance of each function stated (in vitro-derived
   versus in vivo-deconvoluted).
10. Non-monotonic ontogeny considered - "younger equals slower" is not safe.
11. Glomerular filtration maturation applied by postmenstrual age.
12. Renal and hepatic transporter ontogeny handled as a separate, lower-confidence track.
13. Phase 2 (UGT) ontogeny considered for neonates.
14. Biliary ontogeny considered - and tested for identifiability before being added.
15. Absorption ontogeny considered for oral drugs (gastric pH and emptying, motility, bile
    salts, intestinal enzymes and transporters); uncertainty stated.
16. Protein binding maturation (albumin, AAG) represented; unbound targets used for highly
    bound drugs; disease-driven level changes applied without re-deriving the shape.

## Interactions and covariates
17. fm re-derived at each modelled age; DDI magnitude reported as a function of age.
18. Age of maximum interaction vulnerability identified - it is not always the youngest.
19. Absorption-level interaction potential considered (gastric fluid volume relative to
    dose).
20. Target ethnicity physiology used; adult model verified in that population first.
21. Preterm handled with gestational and postnatal age, not weight alone.
22. Disease state named as a confounder of ontogeny functions.

## Validation
22a. **Acceptance criteria agreed with the modeller, tied to intended purpose, recorded BEFORE
    metrics were computed** - or flagged as defaulted to the twofold convention.
23. Full concentration-time profile used, not trough alone.
24. Performance stratified by age band; **AFE and AAFE both** per band against the agreed
    criterion. AFE alone cancels opposing errors; AAFE alone conceals direction. Where GMFE is
    reported, print the formula. Metric definitions and output schema from
    `pbpk-model-build-workflow` (`reference/gof-output-schema.md`).
25. Prediction error plotted against postmenstrual age - does error grow as age falls?
26. Allometric and mechanistic predictions compared; disagreement investigated.
27. Weight-normalised clearance trend sensible and converging to adult values through
    adolescence.
28. Prospective prediction stated before new data are seen, where possible.

## Dose translation
29. Exposure metric matched to pharmacology (AUC, Cmax, time above threshold, trough).
30. Extrapolation argument stated: disease similarity and response similarity, or an
    explicit PK/PD target.
31. Weight or BSA bands, available strengths, rounding, formulation and palatability
    constraints simulated - including at band boundaries.
32. Sparse-sampling design optimised if paediatric PK will be collected.
33. Model-informed precision dosing considered where variability is high and the index
    narrow.

## Report
34. Dose, age-resolved exposure, the biology that carried the extrapolation, the age of
    lowest confidence, and the first-cohort observation that would confirm or refute it.
35. Limitations name every ontogeny track not represented.
