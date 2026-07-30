---
name: pbpk-pediatric-allometric-scaling
description: Scale adult pharmacokinetics to children - neonates, infants, children and adolescents - using allometry, maturation functions and full physiological models, and diagnose why a paediatric prediction is wrong. Covers exponent discipline, enzyme and transporter ontogeny, protein binding maturation, absorption ontogeny, age-dependent DDI, ethnicity, and dose-band translation. Tool-agnostic. Use when selecting a first paediatric dose, defending a paediatric extrapolation, or auditing a paediatric model.
---

# Paediatric scaling and allometry

## When to use this skill

First-in-child dose selection, paediatric investigation and study plans, extrapolation
across age bands, bridging to a new ethnic paediatric population, reconciling a model with
sparse paediatric data, defending an extrapolation argument, or reviewing someone else's
paediatric model. The deliverable is a dose with an explicit account of which biology
carried the extrapolation and where the account is weakest.

## First principle: separate size from maturation, always

Allometry is a statement about size. Ontogeny is a statement about biology. Children
differ from adults in both, and the two effects have completely different shapes:

- Size effects are smooth, monotonic and predictable from body weight across the whole
  age range.
- Maturation effects are steep, pathway-specific, non-monotonic for some enzymes, and
  largely complete within the first years of life.

Conflating them produces a parameter that fits the data you have and fails on the data you
do not. The canonical failure: estimating an allometric exponent in a dataset that spans
neonates, obtaining an exponent well above 1, and treating it as a size relationship. It is
not - it is a maturation function wearing an exponent's clothing, and it will not
extrapolate in either direction.

Operational rule: write the clearance model as size x maturation x organ function x
disease, with each term separately identifiable and separately justified. If you cannot
say which term is doing the work, the model is not extrapolable.

## The decision framework: how to choose the scaling approach

Choose on three axes - the elimination mechanism, the age band, and the data you have.

By age band:
- Adolescents and older children (roughly above 5-6 years, and often above 2 years):
  maturation of the major hepatic and renal pathways is largely complete, so size scaling
  with a fixed exponent is frequently adequate, and the residual risk is body composition
  and puberty rather than ontogeny.
- Toddlers and young children (roughly 2 years down to a few months): maturation is mostly
  complete for some pathways and not others. A maturation function is required, and which
  pathway dominates decides the answer.
- Infants and neonates, term and especially preterm: maturation dominates everything.
  Size scaling alone is not defensible. Gestational age and postnatal age are both
  required covariates, and a physiological model with pathway-specific ontogeny is the
  appropriate tool.

By mechanism:
- Renally filtered drugs: use a glomerular filtration maturation function; this is the
  best-characterised ontogeny and the most reliable paediatric extrapolation available.
- Hepatically metabolised drugs: use pathway-specific enzyme ontogeny; the answer depends
  on which enzyme, and the enzymes do not mature together.
- Actively secreted or transported drugs: renal and hepatic transporter ontogeny is a
  separate track from enzyme ontogeny, matures on its own schedule, and is much less well
  characterised. Flag the reduced confidence.
- Biliary-excreted drugs: biliary elimination appears to mature rapidly, reaching adult
  levels at or within months of term birth, with reduced activity in the preterm.
- Monoclonal antibodies and other protein therapeutics: catabolic and FcRn-mediated
  disposition scales with size reasonably well; do not import small-molecule ontogeny
  logic, and do watch neonatal FcRn and endogenous IgG competition.

By data availability: with rich adult data plus a mechanistic understanding, prefer
mechanistic scaling; with sparse paediatric data already in hand, use it to verify the
mechanistic model rather than to fit an empirical exponent.

## Exponent discipline

- Do not accept a fixed 0.75 without testing it, and do not estimate it freely in a
  maturation-dominated dataset. Those are the two symmetrical errors.
- The theoretical pairing is exponent 0.75 for clearance and 1 for volume, which implies
  half-life scaling with weight to the power 0.25 - longer half-life in larger bodies.
  Check that your predicted half-life ordering across age bands follows this, and if it
  does not, find out which term broke it.
- Because clearance and volume scale differently, the same dose per kilogram gives
  different exposure and different Cmax-to-AUC balance across ages. Weight-based dosing
  is itself a modelling assumption, not a neutral default.
- Empirically estimated exponents above 1 in neonatal datasets are a maturation signal.
  Re-specify the model with an explicit maturation term rather than accepting the exponent.
- Report the exponent with its confidence interval and with the age range over which it was
  identified. An exponent without an age range is uninterpretable.

## Ontogeny is plural: run the tracks separately

Enzyme ontogeny. Pathways mature on different schedules and some are non-monotonic.
Ontogeny functions derived from in vitro abundance data have been shown to under-predict
in vivo clearance in neonates and infants for some substrates; functions deconvoluted from
in vivo PK data performed better. In one such re-evaluation, relative CYP1A2 intrinsic
clearance rose from birth to around 3 years and then declined towards the adult value,
while CYP3A4 rose continuously and reached adult values at around 1.3 years; using these
in vivo-derived functions improved prediction of clearance for a CYP1A2 substrate and a
CYP3A4 substrate versus the in vitro-derived function (Salem, Johnson, Abduljalil,
Rostami-Hodjegan, Tucker, 2014). Two lessons generalise: the provenance of the ontogeny
function materially changes the answer and must be stated; and a non-monotonic ontogeny
means "younger equals slower" is not a safe heuristic.

Renal function maturation. Filtration maturation with postmenstrual age is the
best-established curve. Renal transporter ontogeny is separate, later, and less certain -
do not let the filtration curve carry a secreted drug.

Phase 2 and non-CYP ontogeny. UGT isoforms mature on their own schedules and matter
disproportionately in neonates, where glucuronidation immaturity is a classic source of
toxicity.

Biliary excretion ontogeny. Systems analysis of intravenous drugs with meaningful biliary
components indicated that biliary elimination matures rapidly - present at term birth for
some drugs, requiring only a fraction of adult activity in the very preterm and reaching
adult activity within the first months (Johnson, Jamei, Rowland Yeo, 2016). In one case
the apparent age dependence was fully explained by the ontogeny of the metabolic pathways,
with no additional biliary ontogeny needed - a reminder to test whether an extra ontogeny
term is actually identifiable before adding it.

Absorption ontogeny. Gastric pH, gastric emptying and intestinal motility, bile salt
concentrations, pancreatic function, intestinal pH, and intestinal enzyme and transporter
expression all change with age, and the literature has substantial gaps for almost all of
them. For an orally administered drug in the very young, absorption uncertainty can exceed
clearance uncertainty. Say so rather than defaulting to adult absorption parameters
silently.

Protein binding ontogeny. Albumin and alpha-1-acid glycoprotein concentrations are low at
birth and rise towards adult values over the first year, so unbound fraction is higher in
neonates for bound drugs. Maturation of AAG is well described by a sigmoidal function of
age, and - importantly - disease and infection change the absolute AAG concentration while
the maturation shape is conserved, which means a disease-state model can scale the level
without re-deriving the ontogeny. Consequences: total-concentration targets ported from
adults are wrong for highly bound drugs in neonates; and weight-normalised clearance
comparisons across ages are contaminated by binding differences unless corrected.

## fm is age-dependent, therefore DDI magnitude is age-dependent

This is one of the highest-value transferable insights in paediatric modelling, and it is
quantitative. Because enzymes mature on different schedules, the fractional contribution of
each pathway to a drug's elimination changes with age, which changes the magnitude of any
interaction that inhibits one pathway. For a theoretical drug metabolised equally by CYP2D6
and CYP3A4 at birth, simulated co-administration of a strong CYP3A4 inhibitor produced an
AUC ratio of about 1.65 at birth, about 2.4 at one year, and about 3.2 in adults - and the
authors note that the reverse can occur, with neonates more sensitive than adults in other
pathway combinations (Salem, Johnson, Barter, Leader, Rostami-Hodjegan, 2013).

The clinical counterpart is caffeine: predominantly CYP1A2-metabolised in adults but
largely renally eliminated in neonates, so the interaction profile is not merely attenuated
but mechanistically different.

Rules that follow:
- Never port an adult DDI ratio into a paediatric label without re-deriving fm at that age.
- Interaction risk should be evaluated as a function of age, and the age of maximum
  vulnerability identified - it is not always the youngest.
- Paediatric absorption-level interactions can also differ, because gastric fluid volume
  relative to dose is smaller in children, which affects solubility-limited absorption and
  pH-dependent interactions.
- Plan paediatric DDI evaluation in the paediatric study plan rather than treating it as a
  post-hoc question; this is now an explicit regulatory expectation.

## Population representativeness and ethnicity

Most paediatric physiological databases were built on North European and North American
children. When the target population differs, inter-ethnic physiological differences should
be represented rather than assumed away. The disciplined build order is: verify the adult
model in the matched ethnic adult population first, then apply paediatric scaling within
that ethnicity. In the development of a Japanese paediatric population model, adult
performance was confirmed across the verification compounds before paediatric use, and
using population-representative demographic and physiological data rather than a
North European template materially improved accuracy against Japanese clinical data;
across 14 verification drugs spanning CYP3A4 and renally cleared compounds, most PK
parameter predictions were reasonably close to observed. A second lesson from that work:
qualifying specific elimination pathways, rather than only specific drugs, is what makes
the model reusable and improves the odds of regulatory acceptance for similar drugs later.

## Validation that actually tests the model

- Validate on the full concentration-time profile, not the trough alone. Trough agreement
  with a wrong absorption or distribution model is coincidence, and it will fail on a
  regimen change.
- Stratify performance by age band. Pooled statistics hide the neonatal failure that is the
  entire point of the exercise.
- **Agree the acceptance criteria with the modeller before computing metrics, anchored to
  intended purpose.** The twofold criterion is the default convention, not a scientific
  constant - a first-in-child dose for a narrow-therapeutic-index drug warrants tighter, a
  formulation-bridging exercise may warrant looser. Criteria set after the metrics are seen are
  a rationalisation. If the modeller declines, apply twofold and say it was defaulted.
- Report AFE **and** AAFE per age band alongside the twofold criterion - AFE alone can look
  excellent for a model that is badly wrong in both directions, because over- and
  under-predictions cancel. Where GMFE is reported, print the formula used; the term is defined
  inconsistently in the literature. For the full metric set and the output schema, use the
  Phase A / Phase B apparatus in `pbpk-model-build-workflow`.
- Plot signed error against age. A trend is a missing maturation mechanism; scatter is noise.
  The neonatal skill develops this further, including why the trend should be checked against
  gestational, postnatal and postmenstrual age separately.
- Predict before you look. A prospectively stated prediction that survives new data is
  worth far more, to reviewers and to your own judgement, than a retrospective fit.
- Cross-validate allometry against the mechanistic model, and treat disagreement as
  information. When a mechanistic paediatric model and a simple allometric projection
  disagree, one of them is missing a mechanism - commonly an unmodelled metabolite,
  transporter or formulation effect. Investigating that gap is more valuable than averaging
  the two answers. Note the broader evidence that mechanistic scaling is generally the more
  reliable of the two for metabolically cleared drugs and that empirical scaling fails
  without bound, while allometry can nonetheless outperform on specific endpoints where the
  mechanistic model is missing a component.
- Sanity checks on direction and shape: weight-normalised clearance is typically higher in
  young children than adults for many hepatically cleared drugs once maturation is complete
  (the size effect), and should converge to adult values through adolescence. A model that
  predicts monotonic increase in weight-normalised clearance right through adolescence, or
  that fails to converge, has a structural problem.

## From exposure to dose: the last mile

- Be explicit about the target. Exposure matching to adults is the default when disease and
  response similarity can be argued; otherwise a PK/PD target is required. Under the
  paediatric extrapolation framework, the two preconditions to be argued are disease
  similarity and response similarity, and the standard for disease similarity is explicitly
  not sameness but whether differences are large enough to preclude extrapolation -
  including the possibility that a disease subgroup is similar even when the overall
  populations are not.
- Choose the exposure metric that matches the pharmacology - AUC, Cmax, time above
  threshold, or trough - before optimising the dose. Matching the wrong metric produces a
  defensible-looking dose with the wrong regimen.
- Translate to practice: weight bands or body-surface-area bands, available strengths and
  formulations, palatability and excipient limits in the young, and rounding. A dose that
  cannot be administered accurately is not a dose. Simulate the exposure consequences of
  the banding and rounding you actually intend to recommend, including at band boundaries.
- Where variability is high and the therapeutic index narrow, plan for model-informed
  precision dosing with Bayesian updating rather than a fixed table - this is now a
  practical clinical route, not a research idea, and it is particularly suited to
  maturation-dominated settings where a child moves through the covariate space during
  treatment.

## The assumption that most often silently breaks a paediatric model

One universal size exponent applied to total clearance, with maturation either omitted or
absorbed into the exponent.

It fits older children, degrades through infancy, and fails in neonates - and because most
paediatric datasets are dominated by older children, the fit statistics look fine while the
extrapolation you actually need is wrong. Error that grows as age decreases is the
fingerprint. Test for it by plotting prediction error against postmenstrual age, not
against weight.

## Other assumptions that systematically bias output

- Assuming all pathways mature together. They do not, and the mixture determines both
  clearance and interaction magnitude.
- Assuming ontogeny is monotonic. At least one major CYP overshoots adult activity in early
  childhood before declining.
- Using in vitro-derived ontogeny without acknowledging that it has under-predicted in vivo
  neonatal clearance for some substrates, and without stating which function was used.
- Ignoring transporter and metabolite ontogeny while modelling enzyme ontogeny carefully.
- Porting adult protein binding, and therefore adult total-concentration targets, into
  neonates.
- Porting adult DDI ratios into paediatric labelling.
- Assuming adult absorption parameters in infants, where the underlying physiology is both
  different and poorly characterised.
- Assuming protein therapeutics follow small-molecule scaling.
- Using a North European physiology template for a different ethnic population without
  verifying the adult model in that population first.
- Treating disease as absent. Ontogeny functions are derived largely in relatively healthy
  children; disease alters the physiological context and can change absolute levels of
  binding proteins, organ function and blood flows. State the confounding.
- Validating on trough concentrations only, or on pooled age groups.

## Verification checklist

1. Clearance model written as size x maturation x organ function x disease, with each term
   separately justified.
2. Exponent either fixed with justification or estimated with a stated age range and
   confidence interval; no free exponent estimated across a maturation-dominated range.
3. Half-life ordering across ages consistent with the differing scaling of CL and V.
4. Pathway-specific ontogeny applied, with the provenance of each ontogeny function stated
   (in vitro-derived versus in vivo-deconvoluted).
5. Renal filtration maturation and renal transporter ontogeny handled separately.
6. Phase 2, biliary and absorption ontogeny considered; extra ontogeny terms added only
   when identifiable.
7. Protein binding maturation represented; unbound targets used for highly bound drugs.
8. fm re-derived at each modelled age; DDI magnitude reported as a function of age, with
   the age of maximum vulnerability identified.
9. Population physiology matched to the target ethnicity, with the adult model verified in
   that population first.
10. Preterm neonates handled with both gestational and postnatal age, not weight alone.
11. Disease-state effects on physiology stated as a confounder of ontogeny functions.
12. Validation on full concentration-time profiles, stratified by age band, with AFE/AAFE
    per band and the twofold criterion applied per band.
13. Prediction error plotted against postmenstrual age; no growth in error as age falls.
14. Allometric and mechanistic predictions compared; disagreement investigated as a
    mechanism signal.
15. Exposure metric matched to pharmacology; extrapolation argument (disease similarity,
    response similarity) stated explicitly if exposure matching is the target.
16. Dose bands, available strengths, rounding and formulation constraints simulated,
    including at band boundaries.
17. Sensitivity analysis over the ontogeny function, the exponent, absorption parameters and
    protein binding.
18. Limitations name every ontogeny track not represented.

## How to report so that the reader gains agency

Give the recommended dose, the age-resolved exposure it produces, the biology that carried
the extrapolation, the age at which confidence is lowest and why, and the observation from
the first paediatric cohort that would confirm or refute the model. That is a dose a
clinician can use and a modeller can improve.

