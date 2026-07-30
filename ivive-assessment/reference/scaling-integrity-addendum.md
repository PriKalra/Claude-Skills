# Addendum - scaling-chain integrity

Practical material behind the IVIVE skill: the corrections that are most often missing, the
reproducibility problem in transporter scaling, and how to compare a bottom-up prediction
against an empirical one. Paraphrased from Certara publications and blog resources and from
the OSP IVIVE publication set.

## The two corrections that are missing most often

Unbound fraction in the incubation. Inhibition constants and intrinsic clearances measured
in microsomes or hepatocytes are nominal-concentration quantities. Drug bound to microsomal
lipid or to cellular material is not available to the enzyme, so the nominal value
overstates the concentration that acted. Uncorrected, this inflates apparent Ki and
deflates apparent intrinsic clearance - i.e. it biases towards under-predicting both
clearance and interaction risk. The same logic applies to hepatocyte systems, where knowing
the actual concentration in the medium matters for induction endpoints too. The correction
can be measured experimentally or predicted in silico; not doing either is a review finding,
not a modelling nuance.

Blood-to-plasma ratio. Needed whenever the quantity of interest is a blood-side
concentration - most obviously the unbound concentration at the liver inlet for an orally
administered compound, which is the driver for hepatic uptake transporter effects and for
first-pass processes. Omitting it silently converts a blood-side question into a plasma-side
answer.

## Assay conditions that decide whether the input is real

Before any scaling arithmetic, confirm: solubility in the assay medium across the
concentration range used; stability of the compound over the incubation; non-specific
binding to labware; recovery; cytotoxicity in cell-based systems; a concentration range
wide enough to define the kinetics rather than one point on a curve; positive controls with
demonstrated sensitivity (particularly in induction assays); and linearity of the measured
rate with time and with protein or cell concentration. A scaling chain applied to an
unqualified in vitro number produces a confident wrong answer.

For low-clearance compounds, ask specifically whether the depletion signal was above the
noise floor of the assay. A rate reported at the limit of detection is a ceiling on what the
assay could see, not a measurement of the compound.

## Transporter scaling and reproducibility

Transporter IVIVE depends on relating expression or activity in the in vitro system to
expression in the tissue. Two consequences follow:

- Separate passive from active permeation explicitly. The active component is obtained by
  difference, so it inherits all the error in the passive estimate.
- Scaling factors are laboratory-specific until proven otherwise. Proteomic quantification
  of the same nominal cell system across laboratories shows substantial variability, so a
  relative expression factor derived elsewhere is a starting assumption requiring
  qualification, not a system constant.

When uptake is rate-determining, the organ must be modelled as permeability-limited. A
well-stirred model can be tuned to reproduce the plasma profile while getting intracellular
concentration badly wrong - and intracellular concentration is what drives efficacy,
toxicity and inhibition. The mechanistic framework developed for hepatic membrane
transporters and applied to the rosuvastatin-cyclosporine interaction is the reference
pattern: system parameters (perfusion) and drug attributes (protein and lipid binding,
ionisation, the relative magnitude of passive and active permeation) jointly determine the
outcome, and metabolism and transport must be represented together rather than sequentially.

## Bottom-up versus empirical scaling: how to use both

Across 15 drugs cleared principally by single CYP pathways, mechanistic IVIVE predicted
human clearance accurately in 14 of 15 cases, whereas five variants of interspecies
allometric scaling were accurate in 13, 11, 10, 10 and 14 cases respectively, with some
allometric errors exceeding fivefold (Shiran et al., 2006). Practical use:

- Default to mechanistic scaling where the mechanism is known and the in vitro system can
  express it.
- Run the empirical estimate anyway, as an independent check. Agreement raises confidence
  cheaply.
- Treat disagreement as a mechanism signal. The usual causes are an unrepresented pathway,
  an unrepresented transporter, a metabolite, or a formulation/absorption effect - all of
  which are worth finding.
- Never average the two to split the difference. That destroys the diagnostic information
  and produces a number that neither method supports.

## Confidence is not uniform across pathways

The framework is mature for CYP-mediated hepatic clearance, particularly CYP3A4. It is
thinner for UGTs and other non-CYP enzymes, where system parameters are increasingly
characterised but validated clinical probe substrates remain scarce - in the hepatic
impairment context, only a very small number of clinical studies were available to validate
UGT1A1 predictions. It is thinner again for extrahepatic routes. When scaling through a
route with no validated probe, present a bounded range and state the structural uncertainty
rather than reporting a point estimate with the same apparent authority as a CYP3A4
prediction.

## A closing sanity check

Predicted hepatic clearance cannot exceed hepatic blood flow. Convert the prediction into an
implied extraction ratio and ask whether the resulting first-pass, oral bioavailability and
interaction sensitivity are consistent with everything else known about the compound. An
extraction ratio near 1 has strong, testable implications; if the rest of the data set does
not show them, the scaling chain is wrong somewhere.

