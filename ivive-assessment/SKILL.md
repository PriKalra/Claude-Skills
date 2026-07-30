---
name: pbpk-ivive-assessment
description: Scale in vitro measurements to in vivo clearance, permeability, transport and tissue exposure, and diagnose why an IVIVE prediction is off. Covers hepatic and intestinal metabolism, non-CYP and non-hepatic routes, transporter IVIVE, permeability, protein binding, and the scaling-factor chain. Tool-agnostic. Use when predicting human PK before clinical data, when a bottom-up prediction disagrees with observation, or when auditing scaling assumptions in someone else's model.
---

# IVIVE assessment

## When to use this skill

First-in-human clearance prediction, choice of dose and regimen before clinical data,
deciding whether a bottom-up model is credible enough to replace a study, attributing
the source of a bottom-up mis-prediction, or reviewing a model whose clearance was
"scaled from in vitro" without further detail.

## First principle: IVIVE is a chain, and the chain has a weakest link

An IVIVE prediction is a product of terms, each with its own error:

  measured in vitro rate
  x correction for unbound fraction in the incubation
  x correction for the fraction of drug actually available (solubility, non-specific
    binding, stability, evaporation)
  x biological scaling factor (protein per gram tissue, cells per gram tissue)
  x organ mass
  x extrapolation to the whole-organ model (well-stirred, parallel-tube, dispersion,
    or a permeability-limited model)
  x in vivo binding term (plasma unbound fraction, blood-to-plasma ratio)

Because the terms multiply, a twofold error anywhere is a twofold error in the answer,
and two conservative choices in the same direction give a fourfold error while each
looks defensible in isolation. The discipline is therefore not "get the number right"
but "know which term dominates the uncertainty for this compound".

Operational rule: before quoting a predicted clearance, rank the chain terms by their
contribution to the uncertainty. The top term is what you report as the limitation, and
it is what the next experiment should address.

## Under-prediction is the default failure mode, and it is diagnosable

Bottom-up hepatic clearance from microsomes or hepatocytes tends to under-predict, and
the pattern of under-prediction identifies the cause:

- Uniform under-prediction across many compounds in the same system suggests a
  systematic scaling factor problem - protein recovery, cell viability, or an
  inappropriate empirical scalar.
- Under-prediction concentrated in low-clearance compounds suggests assay sensitivity:
  the depletion signal is inside the noise, and the measured rate is a floor, not a value.
- Under-prediction concentrated in highly bound or highly lipophilic compounds suggests
  the incubational binding correction is missing or wrong.
- Under-prediction concentrated in acids and anions suggests active hepatic uptake is
  rate-determining and a metabolism-only model is structurally wrong.
- Under-prediction concentrated in one chemotype suggests an unrepresented pathway -
  a non-CYP enzyme (aldehyde oxidase, carboxylesterase, UGT, sulfotransferase, FMO),
  extrahepatic metabolism (intestine, kidney, lung, plasma), or biliary excretion.

Applying a blanket empirical scaling factor to "fix" under-prediction destroys this
diagnostic information and transfers the error into every future extrapolation. If an
empirical factor is unavoidable, record it as compound-and-system-specific, state that
it is not a system parameter, and re-verify before reusing it.

## Choose the in vitro system to match the mechanism, not the convenience

- Microsomes: CYP and UGT phenomenology, cheap and reproducible, but no cytosolic
  enzymes, no uptake transporters, no cofactor complexity, and binding correction is
  essential.
- Hepatocytes: intact cofactors, cytosolic and phase 2 enzymes, uptake transport, but
  higher variability, donor dependence, and viability-sensitive.
- Sandwich-cultured hepatocytes: needed when biliary excretion matters.
- Recombinant enzymes: pathway attribution and relative activity factors, but the
  translation to tissue abundance is the whole difficulty.
- Cell lines with transfected transporters and polarised monolayers: transport kinetics
  and permeability, with the caveat that expression levels and passive permeability
  differ from tissue and require their own scaling.
- Plasma, blood, tissue homogenates: for hydrolases and extrahepatic routes that
  microsomes miss entirely.

A recurring, avoidable failure is using a system that cannot express the operative
mechanism and then treating the resulting low rate as evidence of low clearance.

## Transporter IVIVE needs its own scaling logic

Transporter-mediated clearance does not scale like metabolic clearance. Requirements:

- Separate passive from active permeation explicitly; the fitted active component is
  only as good as the passive estimate subtracted from it.
- Use an expression-based scaling approach (relative expression or relative activity)
  rather than a single empirical factor, and be explicit about which reference system
  and which protein quantification the factor came from.
- Recognise that inter-laboratory variability in transporter and marker protein
  abundance in the same nominal cell system is substantial, so a scaling factor derived
  in one laboratory does not transfer without qualification.
- Model the organ as permeability-limited when uptake is rate-determining. A well-stirred
  metabolic model applied to an uptake-limited drug produces a plausible plasma profile
  with a wrong intracellular concentration - and intracellular concentration is what
  drives efficacy, toxicity and interaction.

## Non-hepatic and non-CYP routes are where confidence quietly collapses

The framework is mature for CYP-mediated hepatic clearance and thin elsewhere. Practical
consequences:

- Validated probe substrates are scarce for several UGTs and for most non-CYP enzymes,
  so system-level parameters cannot be verified the way CYP3A4 can. State this rather
  than implying equivalent confidence.
- Intestinal metabolism and efflux need enterocyte-level parameters, not liver
  parameters scaled down.
- Renal, pulmonary and plasma hydrolysis routes require their own in vitro systems and
  their own scalars.
- For anything scaled through an unverified route, present a range from bounding
  assumptions rather than a point estimate.

## What bottom-up buys you that top-down does not, and vice versa

A direct comparison across CYP-cleared drugs found bottom-up IVIVE more reliable than
interspecies allometric scaling for human metabolic clearance - IVIVE was accurate in
14 of 15 cases, whereas allometric methods, depending on the variant, were accurate in
10 to 14 of 15 and produced errors exceeding fivefold in some cases (Shiran, Proctor,
Howgate, Rowland Yeo, Tucker, Rostami-Hodjegan, 2006). The generalisable points:

- Where the mechanism is known and the in vitro system expresses it, mechanistic scaling
  beats empirical scaling.
- Empirical scaling fails unpredictably rather than gracefully; its errors are not
  bounded by anything.
- The two are most valuable used together: agreement raises confidence; disagreement is
  a diagnostic signal about an unrepresented mechanism, and should be investigated
  rather than averaged away.

## How good does the prediction have to be

**Ask the modeller what acceptance criteria apply, anchored to the intended purpose, before
computing any metric.** IVIVE has no single conventional bar the way DDI ratios do, which makes
this question more important here rather than less - and it makes an unstated criterion easier
to slide past a reviewer. Candidate to offer, without imposing: AAFE below 2 on predicted
clearance is the common working expectation, but a first-in-human dose projection, a low-clearance
compound near the limit of assay resolution, and a compound-ranking exercise in discovery
plausibly justify three different bars.

Points specific to scaling:

- **Systematic under-prediction is the default failure mode of IVIVE**, so report **AFE
  (signed) alongside AAFE (unsigned)**, never AAFE alone. AAFE alone conceals direction, and
  direction is the diagnostic here - a bias is a missing scaling factor, while scatter without
  bias is assay variability.
- Report the **fold error distribution across compounds and pathways**, not just an aggregate.
  Aggregate performance across a compound set will pass while one pathway class - a non-CYP
  route, a transporter-mediated step - fails entirely.
- Where an empirical scaling factor was applied, report the prediction **with and without it**.
  A model that only meets criteria after an unexplained multiplicative correction has not
  demonstrated bottom-up predictive ability, and the correction must not be carried to another
  compound or population without re-verification.
- Where GMFE is reported, print the formula used - the term is defined inconsistently in the
  literature, sometimes as the bias-insensitive geometric mean of absolute fold error and
  sometimes as the signed ratio.

Record who set the criteria and when. Criteria chosen after the metrics are seen are a
rationalisation. For the full metric set, the additional GoF lenses, and the output schema, use
the Phase A / Phase B apparatus in `pbpk-model-build-workflow` rather than restating it here.

## Verification checklist

1. Every term in the scaling chain written out explicitly with its source and units.
2. Incubational binding measured or predicted, and applied.
3. Assay integrity confirmed: solubility in medium, stability, non-specific binding,
   recovery, cytotoxicity where relevant, adequate concentration range, linearity with
   time and protein.
4. In vitro system capable of expressing the operative mechanism; justify the choice.
5. Non-CYP, extrahepatic and biliary routes considered and either excluded with evidence
   or represented.
6. Passive and active permeation separated where transport matters.
7. Organ model structure (well-stirred vs permeability-limited) matched to the
   rate-determining step.
8. Blood-to-plasma ratio and plasma unbound fraction measured, not assumed, for
   highly bound compounds.
9. Prediction compared with any independent estimate (allometry, top-down, prior
   compound in the series); disagreement investigated, not averaged.
10. Uncertainty propagated through the chain; the dominant term identified and reported.
11. Any empirical scaling factor flagged as compound-and-system-specific and excluded
    from onward extrapolation without re-verification.
12. Predicted clearance sanity-checked against extraction ratio limits - predicted
    hepatic clearance cannot exceed hepatic blood flow, and a predicted extraction ratio
    near 1 has strong implications for first-pass and for interaction potential that
    should be consistent with the rest of the model.

## How to report so that the reader gains agency

Report the predicted value, the chain term that dominates its uncertainty, the range
implied by bounding that term, and the single experiment that would collapse the range.
That turns a number into a decision.

