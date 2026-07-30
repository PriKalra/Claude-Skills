---
name: pbpk-renal-impairment-assessment
description: Build, interrogate and defend PBPK/model-based predictions of drug exposure in renal impairment - CKD stages, acute kidney injury, dialysis (HD/CVVH/peritoneal) and renal dysfunction in children. Tool-agnostic. Use when deciding whether a dedicated renal impairment study is needed, when predicting exposure in unstudied renal strata, when reconciling a model with observed severity-stratified data, or when auditing someone else's renal impairment model for hidden assumptions.
---

# Renal impairment assessment

## When to use this skill

Invoke this whenever a question of the form "what happens to exposure when kidney
function falls?" arises: dedicated-study waiver arguments, label language for
mild/moderate/severe/ESRD, dose selection in dialysis, AKI in critically ill or
oncology patients, renal dysfunction in neonates, or review of a submitted model. The
output should always be a mechanistically attributed exposure change - not a single
number - plus an explicit statement of which assumption the number is most sensitive to.

## First principle: renal impairment is a systemic perturbation, not a clearance multiplier

The single most consequential error in this domain is treating declining kidney function
as a scalar on total clearance derived from creatinine clearance. Reduced kidney function
changes at least seven things simultaneously, and they do not move together:

1. Glomerular filtration - falls roughly with measured GFR, and this is the only axis
   that the conventional scalar captures.
2. Active tubular secretion - falls, but not necessarily in proportion to GFR.
   Transporter-mediated secretion (OAT1/OAT3, OCT2, MATE1/2-K, P-gp, MRP2/4) has its own
   relationship to declining eGFR, and secretion can decline faster than filtration.
3. Tubular reabsorption - can change independently, altering net renal clearance in the
   opposite direction to secretion.
4. Non-renal clearance - hepatic and intestinal enzyme and transporter activity is
   modified in uraemia (suppression is the more common direction, but induction has been
   reported). This is why regulators now expect renal impairment to be considered even
   for drugs eliminated non-renally.
5. Plasma protein binding - hypoalbuminaemia plus displacement by accumulated uraemic
   solutes raises fu, often substantially for acidic, highly bound drugs.
6. Distribution and haemodynamics - fluid overload, altered haematocrit and
   blood-to-plasma ratio, altered organ blood flows and Vss (partly a consequence of the
   fu change).
7. Absorption and the gut - altered gastric pH, delayed emptying, oedema of the gut wall,
   and near-universal co-medication (phosphate binders, PPIs, cation-containing salts)
   that can chelate or raise pH.

Operational rule: before running anything, write down which of these seven axes your
model represents explicitly, which it represents implicitly, and which it ignores. Any
axis in the "ignores" column is a stated limitation of the prediction, not an invisible
detail.

## The decision: dedicated study, PopPK covariate, PBPK, or a combination

Organ-impairment strategy follows the same logic in kidney and liver, and it is worth
using the more explicitly articulated hepatic framework as the template (Barter and Wood,
Certara, 2026, on minimising risk in hepatic impairment studies):

- A dedicated study is expected when the impaired pathway carries a material fraction of
  elimination (the hepatic analogue uses a threshold of more than 20 percent of absorbed
  dose; for renal, reason from fe and from the fraction of non-renal clearance that is
  plausibly uraemia-sensitive), when the drug will be used in this population, and when
  the therapeutic index is narrow.
- A dedicated study is usually unnecessary when elimination bypasses the kidney almost
  entirely, the exposure change is predicted to be small, and the therapeutic index is
  wide - but the absence of a study buys contraindication or "not recommended" language
  plus possible post-marketing commitments. Treat "no study" as a labelling decision, not
  a neutral default.
- PopPK with a pre-specified renal function covariate analysis using patients enrolled in
  phase 2/3 is an accepted route and is the cheapest way to cover mild-to-moderate
  impairment.
- PBPK covers the strata that were not enrolled, extrapolates single-dose to
  multiple-dose, handles dialysis and combined impairment, and can substitute for a
  clinical study in favourable circumstances.
- The most efficient strategy is usually a combination: PopPK for the mild/moderate range
  that trials naturally capture, PBPK to reach severe/ESRD and to bridge to dialysis.

What raises the probability that a model is accepted in lieu of a study. Four conditions
recur, and they are worth treating as a pre-flight checklist because they generalise
across organ impairment, DDI and paediatrics:

- a well-characterised ADME profile anchored by human mass balance and enzyme/transporter
  phenotyping (fe, fm per pathway and the biliary contribution are measured, not assumed);
- non-linearities and the single-dose to multiple-dose transition are understood;
- independent clinical DDI data exist that pin down fm for the major pathways - the DDI
  dataset is what turns a guessed fm into a constrained one;
- the predicted exposure shift is modest or clinically manageable.

If any of these four is missing, expect to be asked for the clinical study, and say so
early rather than discovering it at review.

## Build order that generalises across drugs

1. Verify the healthy-adult model on IV and oral data first. A renal model is a
   perturbation of a healthy model; error in the base model is inherited and amplified.
2. Decompose clearance before perturbing anything. Split total CL into filtration,
   secretion, reabsorption, each non-renal enzymatic pathway with its own fm, biliary
   excretion, and any extracorporeal route. The decomposition, not the total, is what
   renal impairment acts on.
3. Check the decomposition against the observed renal clearance using the filtration
   ratio test (below). Do not proceed on a decomposition that only reproduces CLR by an
   implausible parameter.
4. Apply population-level physiology changes by severity stage, not drug-level fudges.
   The parameters that should move with severity are systems parameters: GFR, kidney and
   liver volume, organ blood flows, haematocrit, albumin and alpha-1-acid glycoprotein
   concentrations, enzyme and transporter abundances, gastric residence time. If you are
   changing a drug parameter to fit a population effect, you have moved the error
   somewhere it will not extrapolate.
5. Verify against severity-stratified observed data where it exists, and against
   endogenous biomarkers where it does not.
6. Only then extrapolate to dialysis, combined organ impairment, paediatric renal
   dysfunction, or AKI.

Performance expectations: **ask the modeller what acceptance criteria apply, tied to the
intended purpose, before computing metrics.** The usual default is predicted-to-observed AUC
and Cmax ratios within twofold across all severity categories with average fold error
comfortably below 1.5 - a convention, not a constant. A dedicated-study waiver in severe
impairment warrants a tighter bar than an internal go/no-go. Record who set the criteria and
when; criteria agreed after the metrics are seen are a rationalisation. If the modeller
declines, apply the twofold convention and label it as defaulted.

Evaluate **per stratum, never pooled.** Pooling hides the pattern that matters, which is
whether error grows with severity - and a trend of signed error against eGFR is a missing
mechanism rather than noise. For the full metric set, the lens options, and the output schema,
use the Phase A / Phase B apparatus in `pbpk-model-build-workflow`.

## Parameter relationships that must hold internally

These are the arithmetic constraints that let you falsify a renal model without any new
data. Run them every time.

The filtration ratio. Compare observed renal clearance to fu x GFR. A ratio near 1 means
filtration alone explains renal handling; markedly above 1 means net secretion; markedly
below 1 means net reabsorption (or that fu is wrong). If your model reproduces observed
CLR only by assuming filtration of a drug whose ratio is 4, the mechanism is misassigned
and the model will fail the moment secretion becomes rate-limiting.

The fe ceiling. As renal clearance goes to zero, the maximum achievable AUC increase is
1/(1-fe). A drug with fe of 0.5 cannot exceed a twofold AUC increase from loss of renal
function alone. If a model or an observed dataset shows more, then something else is
happening - reduced non-renal clearance, an fu change, altered absorption, or the fe
estimate itself is wrong. This one check catches more mislabelled mechanisms than any
other single test.

Monotonicity across severity. Exposure should increase monotonically from normal through
mild, moderate, severe, to ESRD. Non-monotonic model output is almost always a sign of two
competing perturbations being applied inconsistently across population templates (commonly
an fu increase raising clearance of a low-extraction drug while GFR reduction lowers it).
Non-monotonic observed data is usually a study-design or matching artefact and should be
interrogated, not fitted.

The non-renal floor. Total clearance cannot approach zero in anuria if a meaningful
fraction of elimination is hepatic or biliary. Predicted half-life in ESRD that goes to
absurd values indicates the non-renal pathway has been inadvertently scaled down along
with kidney function.

The unbound-exposure distinction. For a low-extraction-ratio drug, clearance based on
total concentration scales with fu, so an fu increase in severe CKD lowers total AUC while
leaving unbound AUC essentially unchanged. Total-concentration exposure can therefore look
reassuring while pharmacology is unchanged, or look alarming when only binding shifted.
Always report the unbound metric alongside the total metric when fu is expected to move,
and never base a dose adjustment on total concentrations for a highly bound drug in severe
CKD without checking fu.

Volume and half-life consistency. An fu increase raises Vss for a drug with significant
tissue binding. Check that the predicted half-life change is consistent with the predicted
changes in both CL and V, rather than tracking clearance alone.

The eGFR-clearance relationship shape. Plot model-predicted CL against eGFR alongside
observed. Ask whether the relationship is proportional through the origin or linear with a
positive intercept - the intercept is the non-renal clearance, and getting the intercept
right matters far more at the severe end than getting the slope right.

Metabolite behaviour. If a metabolite is renally cleared and is active, toxic, or an
inhibitor of the parent's own elimination, the parent AUC ratio understates the clinical
consequence of renal impairment. Model the metabolite explicitly whenever it carries
pharmacology or inhibitory potential.

## Endogenous biomarkers: the most underused lever, and a trap

Regulatory thinking has converged on endogenous biomarkers as a legitimate way to
interrogate transporter function in vivo, and the harmonised DDI guidance now names a
usable panel (Gil Berglund, Rioux and Rowland Yeo, Certara, on ICH M12): plasma
coproporphyrin I for hepatic OATP1B1/1B3; plasma and urinary N-methylnicotinamide and
N-methyladenosine for renal OCT2/MATE1/MATE2-K; plasma pyridoxic acid for renal OAT1/OAT3;
and 4-beta-hydroxycholesterol/cholesterol and urinary 6-beta-hydroxycortisol/cortisol for
CYP3A.

Two uses in renal work:

- Calibration. When severity-stratified PK data are absent, biomarker data can constrain
  how much renal transporter function has actually been lost, separating a secretion
  deficit from a filtration deficit. Coproporphyrin I is well behaved enough that baseline
  intersubject variability is low, so a single pre-dose sample plus a post-dose time
  course is informative - a design worth copying for the renal biomarkers.
- Deconvolution. Because these probes are selective, a null biomarker response rules a
  mechanism out. The published logic for distinguishing OATP1B from BCRP effects on
  rosuvastatin using an OATP1B-only probe is the general pattern: pick a biomarker that is
  a substrate of exactly one of the two candidate mechanisms.

The trap, and it is important. Serum creatinine is itself a substrate of renal cation
transporters. A drug that inhibits OCT2/MATE raises serum creatinine and lowers apparent
eGFR without changing true glomerular filtration. If you then feed that apparent eGFR into
a renal impairment model, you will predict a filtration deficit that does not exist, and
clinically you may see a "renal safety signal" that is a transporter artefact. Whenever
serum creatinine rises without a corresponding change in an independent filtration marker
(cystatin C, iohexol, inulin) or without other markers of tubular injury, suspect
transporter inhibition before concluding nephrotoxicity.

A related confounding applies to phenotyping: urinary metabolic ratios used as in vivo
indices of enzyme activity are themselves renal-function-dependent, and the dependence
differs between markers, so different probes can give discordant answers in the same CKD
patients; ratios based on complete urine collection are the more robust form
(Rostami-Hodjegan, Kroemer and Tucker, 1999, on CYP2D6 indices in renal impairment). That
work also supports the substantive point that apparent CYP2D6 activity declines in
parallel with declining renal function - the non-renal axis is real and measurable, not a
theoretical concern.

## Non-renal clearance in uraemia: how to handle it honestly

This is where models most often become quietly unfalsifiable, because the effect is real,
compound-specific and poorly parameterised. Three defensible approaches, in descending
order of preference:

1. Use pathway-specific abundance changes from proteomic or expression data where they
   exist, applied as systems parameters by severity stage, and verify against a probe
   substrate of that pathway.
2. Borrow from top-down data. Where therapeutic drug monitoring or PopPK data exist in CKD
   patients, combine them with bottom-up systems parameters to back out the apparent
   change in non-renal clearance, then carry that forward mechanistically - the approach
   taken for tacrolimus as a worked example (Certara poster combining TDM data with
   bottom-up systems data to interrogate the effect of renal impairment on non-renal
   clearance).
3. Bound the problem. If neither is available, run the prediction at "no change in
   non-renal clearance" and at a plausible reduction, and present the range as the
   prediction. A range you can defend beats a point estimate you cannot.

The systems-level review of this area is worth internalising as the conceptual frame:
renal failure can suppress or induce hepatic metabolism and simultaneously act through
protein binding, hepatic blood flow and metabolite accumulation, which is precisely why
guidance moved to asking about non-renally eliminated drugs at all (Rowland Yeo, Aarabi,
Jamei and Rostami-Hodjegan, 2011, on modelling PK in renal impairment).

## Dialysis

Dialysis is not "severe CKD plus a bit". It is an intermittent, high-clearance
extracorporeal pathway superimposed on a near-anuric baseline, and it must appear in the
model as its own explicit, time-varying clearance term.

- Determinants of dialysability: unbound fraction, molecular size, volume of distribution
  (large Vss drugs are poorly removed regardless of everything else), blood and dialysate
  flow rates, membrane characteristics and surface area, session duration, and modality.
  Intermittent haemodialysis, continuous renal replacement and automated peritoneal
  dialysis are three different clearance models with different time profiles - not
  interchangeable.
- Post-session rebound matters for drugs with slow tissue redistribution; a concentration
  measured at the end of a session overestimates removal.
- Dose timing relative to the session is often the actual clinical recommendation being
  sought, so simulate it explicitly rather than reporting an average clearance.
- Study design to emulate. The cleanest dialysis-effect designs compare the same subjects
  on dialysis and non-dialysis days and evaluate the exposure ratio against conventional
  equivalence bounds; a ratio whose confidence interval sits inside 80-125 percent supports
  "no clinically meaningful removal", and a paired multiple-dose arm answers accumulation
  separately. The defibrotide programme is a compact template: within-subject
  dialysis-versus-non-dialysis comparison showing no meaningful effect of dialysis, no
  accumulation on repeated dosing, and modest exposure elevation in severe/ESRD relative
  to matched controls (Certara publication on defibrotide PK in renal impairment).
- Peritoneal dialysis in ESRD has been characterised for renally eliminated antivirals and
  their active metabolites, and is a reminder to evaluate metabolite removal separately
  from parent removal.

## Acute kidney injury is a different problem

AKI is time-varying, frequently non-steady-state, and often accompanied by haemodynamic
instability, fluid shifts and multi-organ dysfunction. Consequences:

- Do not apply a CKD population template to AKI. CKD templates encode chronic adaptations
  (anaemia, altered protein levels, stable transporter changes) that have not had time to
  develop.
- Use a severity staging system designed for acute change (RIFLE/KDIGO) and model renal
  function as a function of time, not a fixed stage.
- Expect the fu and Vss perturbations from fluid overload to dominate early, and the
  clearance perturbation to dominate later.
- In critically ill patients, remember the opposite tail exists: augmented renal clearance
  produces sub-therapeutic exposure, and a model built only to handle impairment will miss
  it.

## Combined and special populations

- Renal plus hepatic impairment is common and is not additive by default. Model both sets
  of systems changes and check that the resulting non-renal floor and fu changes are not
  applied twice.
- Paediatric renal dysfunction requires age-appropriate renal function definitions. Adult
  eGFR thresholds are meaningless in neonates, where filtration is maturing steeply with
  gestational and postnatal age; a preterm neonate with "adult severe CKD" eGFR may be
  physiologically normal. Renal impairment in the very young must be layered on top of
  maturation, using acute staging criteria referenced to age-appropriate normals, and
  validated against a drug with known renal handling in that age band. This combination
  has been demonstrated for methylxanthine dosing in preterm neonates with renal
  impairment and is the clearest example of why the two effects must be parameterised
  separately rather than lumped.
- Body composition extremes. Creatinine-based eGFR degrades in sarcopenia, obesity,
  amputation and cachexia. If your covariate is creatinine-derived and the population is
  not body-composition-typical, the input to your model is biased before the model runs.
- Transplant recipients carry graft-dependent, time-varying function plus immunosuppressant
  DDIs, and should be treated as a distinct population.

## The assumption that most often silently breaks a renal impairment model

A single scalar applied to total clearance, derived from creatinine clearance, carrying
three unstated assumptions: that renal handling is filtration-only, that unbound fraction
is unchanged, and that non-renal clearance is unchanged.

This one construct is defensible for a filtration-cleared, low-bound, non-renally-inert
drug and is wrong for almost everything else. When it is wrong it fails in a specific,
diagnosable way: predictions are acceptable in mild impairment and degrade progressively
into severe and ESRD, because all three neglected effects grow with severity. Error that
grows monotonically with severity is the fingerprint of this assumption. Test for it
explicitly by plotting prediction error against severity stage.

## Other assumptions that systematically bias output

- Assuming secretion falls proportionally with GFR. Secretion and filtration have different
  relationships to declining function; for a secreted drug this biases the severe strata.
- Using the healthy-subject unbound fraction throughout. For highly bound acidic drugs in
  severe CKD this can be badly wrong, and it biases both clearance and Vss in the same
  direction, so half-life looks internally consistent while both components are wrong.
- Treating ESRD-on-dialysis as the endpoint of the CKD severity trend. It is a different
  regimen with an added intermittent clearance path.
- Ignoring co-medication and formulation effects. Phosphate binders, PPIs and
  cation-containing products are near-universal in advanced CKD and can produce a bigger
  exposure change than the renal function loss itself.
- Fitting the population effect with a drug parameter. It will reproduce the training data
  and fail on the next stratum.
- Taking apparent enzyme phenotype from urinary ratios in CKD at face value. Confounded by
  renal function itself.
- Assuming the impairment affects only elimination. Emerging work in hepatic impairment
  shows absorption changes too (gastric residence, bile salts, shunting); the renal
  analogue - gastric pH, gut oedema, motility - is at least as plausible and much less
  studied.
- Validating on pooled data. Always stratify.

## Verification checklist

Run every item; record the answer even when it is "not applicable".

1. Healthy-adult model verified on IV and oral data independently.
2. Clearance decomposed into filtration, secretion, reabsorption, each metabolic pathway,
   biliary and extracorporeal routes, with fe and fm sourced from mass balance and
   phenotyping rather than fitted.
3. Filtration ratio test performed; assigned mechanism consistent with it.
4. fe ceiling test: is the predicted maximum increase below 1/(1-fe), and if not, is the
   excess mechanism named?
5. Monotonicity across severity strata confirmed.
6. Non-renal floor sensible; ESRD half-life physiologically plausible.
7. fu change considered explicitly; unbound exposure reported alongside total.
8. Vss and half-life changes mutually consistent with CL and V changes.
9. Prediction error plotted against severity stage; no monotonic drift.
10. Metabolites modelled where active, toxic or inhibitory - including their own renal
    clearance and dialysability.
11. Dialysis, if relevant, represented as an explicit time-varying clearance with
    modality-specific parameters, and dose timing relative to sessions simulated.
12. Endogenous biomarker data used where available; creatinine-based eGFR checked for the
    transporter-inhibition artefact.
13. Severity classification appropriate to the setting (chronic staging for CKD, acute
    staging for AKI, age-appropriate normals in children).
14. Acceptance criteria agreed with the modeller against intended purpose and recorded before
    metrics were computed, or flagged as defaulted; predicted-to-observed AUC and Cmax judged
    against that criterion per stratum, with AFE and AAFE both reported per stratum and signed
    error plotted against eGFR.
15. Sensitivity analysis over the three usual suspects - fe, fu in severe impairment, and
    the assumed change in non-renal clearance - with the resulting exposure range reported,
    not just the point estimate.
16. Limitations section names every one of the seven axes the model does not represent.

## How to report so that the reader gains agency

State the exposure change, then immediately state the mechanism split (how much came from
filtration, secretion, non-renal, binding), then the single parameter the answer is most
sensitive to, then the falsifiable prediction that would distinguish your model from the
leading alternative explanation. A reader who has those four things can act, disagree, or
design the confirmatory study. A reader who has only an AUC ratio can do none of them.

