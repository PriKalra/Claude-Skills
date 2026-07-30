# Addendum - regulatory framing, biomarkers, and in vitro package integrity

Sourced from Certara resources (blogs and publications) and folded into the DDI skill.
Paraphrased throughout. Verify against the current guideline text before relying on any
numeric criterion.

## Nomenclature

Harmonised DDI guidance uses "object" (the substrate of the enzyme or transporter whose
exposure changes) and "precipitant" (the drug that inhibits or induces). The older
victim/perpetrator language is being retired. Use object/precipitant in anything
regulatory-facing.

## Where harmonised guidance shifted the goalposts

- Time-dependent inhibition: less stringent basic static risk criteria than several
  legacy regional guidances; dilution and non-dilution assay formats both acceptable;
  turnover rate constants for the major CYPs are now specified, which removes a degree
  of freedom modellers used to exploit.
- Induction: more stringent. The basic mRNA fold-change method uses a concentration of
  50x unbound Cmax; unbound concentration in the assay (EC50,u) should generally be used;
  positive controls should produce at least a sixfold mRNA increase for CYP1A2, CYP2B6
  and CYP3A; incubations shorter than 48 hours need justification; the relative induction
  score correlation method can be run with a single qualified hepatocyte batch.
- Cross-enzyme extrapolation is explicitly not permitted: a negative clinical CYP3A4
  induction study does not exonerate CYP2C8, CYP2C9 or CYP2C19, and those should be
  evaluated when CYP3A4 induction is shown clinically.
- Protein binding: a route now exists to use measured unbound plasma fraction below 0.01
  in a basic-method assessment, provided the methodology is suitable. Previously such
  drugs were effectively forced to a conservative default.
- Phase 2: a defined UGT panel (1A1, 1A4, 1A9, 2B7, 2B15) applies when the drug is mainly
  eliminated by direct glucuronidation or will be co-administered with such a drug.
  N-acetyltransferases and glutathione S-transferases are named among other phase 2
  enzymes.
- Timing: reaction phenotyping generally before phase 1 in patients; precipitant effects
  on CYPs and transporters generally before larger patient studies; metabolite work can
  come later.

## Endogenous biomarker panel

Recognised probes, by target:

- OATP1B1 / OATP1B3 - plasma coproporphyrin I
- OCT2 / MATE1 / MATE2-K - N-methylnicotinamide and N-methyladenosine, plasma and urine
- OAT1 / OAT3 - plasma pyridoxic acid
- CYP3A - 4-beta-hydroxycholesterol to cholesterol ratio; urinary 6-beta-hydroxycortisol
  to cortisol ratio

Practical design notes for coproporphyrin I, which generalise to the others: it is
metabolically stable, predominantly biliary, an established OATP1B1/1B3 substrate in
vitro, and its plasma level associates with SLCO1B1 genotype. Baseline intersubject
variability is low enough that a single pre-dose sample suffices, with a post-dose time
course recommended. Collecting it in an early phase 1 study tells you whether an in vitro
OATP1B signal translates before you commit to a dedicated clinical DDI study - the basic
static criteria are conservative, so this frequently de-risks a false positive.

Deconvolution use: choose a biomarker that is a substrate of exactly one of two candidate
mechanisms. For a dual OATP1B/BCRP inhibitor tested against rosuvastatin, an unchanged
coproporphyrin I signal attributes the rosuvastatin change to intestinal BCRP rather than
hepatic uptake - which changes the label and the co-medication advice.

Biomarker data can also be used quantitatively to estimate in vivo inhibition potency,
which is a more defensible input than a scaled in vitro Ki.

## When to measure metabolites in an index clinical DDI study

From an analysis of several thousand index-substrate DDI studies in a curated interaction
database, fewer than half measured any metabolite, and the value of doing so is
substrate-specific rather than universal:

- Worth measuring - caffeine (CYP1A2), bupropion (CYP2B6), omeprazole (CYP2C19),
  dextromethorphan (CYP2D6). These improve sensitivity for detecting an interaction or
  reduce intra-subject variability. Omeprazole metabolites additionally separate CYP3A4
  from CYP2C19 effects, which is mechanistic information you cannot get from parent alone.
- Not obviously worth measuring - midazolam (CYP3A). Metabolite data did not improve
  interpretation.

Design rule: measure the metabolite when it either raises assay sensitivity or resolves a
mechanistic ambiguity you actually have. Otherwise it is cost without inference.

## In vitro package gaps that trigger review findings

- Missing microsomal (or hepatocyte) binding data, so Ki/IC50 are uncorrected. This
  biases towards under-predicting risk.
- Missing blood-to-plasma ratio, so the unbound hepatic inlet concentration used for
  uptake transporter risk cannot be computed.
- Assay conditions not qualified: solubility in the assay medium unconfirmed,
  cytotoxicity not assessed in cell systems, recovery/stability/non-specific binding
  uncorrected, concentration range too narrow, positive controls insufficiently sensitive
  (especially in induction assays).
- Incomplete characterisation of major human circulating metabolites. A metabolite at
  roughly 10 percent or more of drug-related material and 25 percent or more of parent
  AUC may need evaluation as an object if it contributes to efficacy, and as a
  precipitant based on its exposure regardless of potency. A newly identified major
  metabolite from a human mass balance study can re-open phenotyping.
- Risk conclusions not re-derived against current cut-off values. A previously negative
  signal can become positive or inconclusive, and vice versa - which can also remove a
  planned clinical study.

Treat in vitro DDI characterisation as continuous across the programme lifecycle, not as
a box ticked once before phase 1.

