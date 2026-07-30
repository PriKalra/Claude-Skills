---
name: pbpk-model-build-workflow
description: Build a whole-body PBPK or PBPK/PD model from scratch in a defensible order, then qualify it with quantitative goodness-of-fit analysis. Covers system-versus-drug parameter discipline, distribution-model (Kp) selection, the IV-then-oral build sequence, learn-confirm-refine across species, bottom-up versus top-down parameter identification, PD coupling at the site of action, sensitivity analysis, and the full GoF metric set - fold error, AFE, AAFE, GMFE, RMSE and visual predictive check. Tool-agnostic. Use when starting a new PBPK model, when deciding what to fit versus what to predict, when a plasma profile fits but tissue or Cmax does not, when choosing a tissue-partition method, when coupling PD to PBPK, when computing or interpreting fold error or AAFE, when setting acceptance criteria for model qualification, or when auditing the build order of someone else's model. For applications of a finished model, see the DDI, IVIVE, renal impairment, paediatric, neonatal and dermal skills.
---

# PBPK model build workflow

## When to use this skill

Any time the question is "how do I build this model, in what order, and how do I know
whether it is good enough?" - a first whole-body model for a new compound, a rebuild
after a model failed to recover observed data, a decision about which parameter is
legitimately adjustable, PD coupling onto a PBPK model, computation of qualification
metrics, or review of whether someone else's model was built in an order that makes its
predictions mean anything. The output is never a concentration-time profile alone. It is
a profile, plus the list of parameters that were predicted rather than fitted, plus the
structural assumption the profile is most sensitive to, plus a numeric verdict against
criteria that were agreed before the metrics were computed.

## First principle: a PBPK model is two independent knowledge bases multiplied together

Every parameter in a PBPK model belongs to exactly one of three categories, and the
entire credibility of the model rests on not confusing them:

1. **System parameters** - organ volumes, tissue composition, blood flows, surface
   areas, enzyme and transporter abundance, GI transit times and pH. Properties of the
   species, population or disease state. Prior knowledge. Not yours to adjust.
2. **Drug parameters** - molecular weight, lipophilicity, pKa, solubility, intrinsic
   clearance, permeability, Michaelis-Menten constants, binding affinities. Properties
   of the compound, and should be independent of the organism.
3. **Drug-biological parameters** - fraction unbound, blood-to-plasma ratio,
   tissue-plasma partition coefficients. The product of the first two, and therefore
   *derived*, not independently assumed. Treating a Kp as a free parameter silently
   overwrites the physiology that was supposed to be constraining the model.

Two operational rules follow, and they carry most of the discipline in this skill:

- **Default physiology changes only with a mechanistic rationale.** If you alter a
  system parameter to improve a fit, you have converted a mechanistic model into an
  empirical one with a physiological vocabulary. Write down the mechanism, or revert.
- **Drug parameters stay constant across species, doses and routes.** The whole claim of
  PBPK is that the same compound file, dropped into a different physiology, predicts. A
  compound parameter that must be re-tuned per species is either wrong or is standing in
  for a process the model does not contain.

The extrapolation power comes from this separation. Extrapolating to a new population is
*only* a change of system parameters; to a new dose or regimen, *only* a change of the
administration protocol. If a new scenario requires touching drug parameters, that is not
extrapolation, it is a new fit.

## The build order that generalises

This sequence exists because each step isolates a different set of unknowns. Skipping or
reordering steps does not merely lose rigour - it makes specific parameters
unidentifiable, because two unknowns become confounded in the same dataset.

1. **Compile and classify.** Gather physicochemistry (MW, logP, pKa, solubility),
   binding (fu, B:P), in vitro ADME (CLint per pathway and system, permeability,
   transporter kinetics), mass balance and excretion fractions, and the observed PK you
   intend to compare against. Sort every number into system, drug, or drug-biological.
   Apply the effective-MW correction for halogenated compounds, whose atomic weight
   overstates their molecular volume.
2. **Choose the tissue distribution model, and record it as an assumption.** The Poulin,
   Rodgers-Rowland, Berezhkovskiy, Willmann and Schmitt methods all decompose tissue
   into water, neutral lipid, phospholipid and protein, but weight ionised and
   electrostatic interactions differently. They frequently produce nearly identical
   *plasma* profiles and substantially different *tissue* profiles. This is the most
   consequential choice in the build and the one most often left undocumented. If the
   model's purpose is target-site exposure, the distribution model is a primary result,
   not a setting.
3. **Decide perfusion-limited versus permeability-limited per tissue.** Perfusion
   limitation is the default for small lipophilic molecules, where blood flow is
   rate-limiting and only Kp and flow set the time to equilibrium. Switch a tissue to
   permeability limitation when the molecule is large or polar, when binding is very
   high, or - importantly - when active transport at that organ's membrane is part of the
   mechanism. Hepatic uptake transport cannot be represented in a perfusion-limited liver
   at all, because there is no membrane for the transporter to sit on.
4. **Establish the IV model first.** Intravenous data inform distribution and clearance
   with absorption removed from the problem. Estimate clearance per pathway, using mass
   balance to apportion between routes and IVIVE to set starting values. Judge the fit on
   both absolute concentration and the *shape* of the profile.
5. **Then establish the oral model, varying only absorption parameters.** Intestinal
   permeability, formulation release and dissolution, biorelevant solubility, meal
   events, and if needed enterohepatic recycling. Nothing that governs distribution or
   clearance may move at this step. The reason is not tidiness: absorption parameters and
   clearance are jointly identifiable from oral data alone, so if you leave clearance
   free you will find a fit that is arithmetically valid and mechanistically meaningless.
6. **The documented exception.** When the IV model already contains a process that also
   governs oral absorption - an uptake transporter, gut-wall metabolism - strict
   sequencing breaks down, and IV and oral data should be fitted simultaneously. Say
   which case you are in.
7. **Learn, confirm, refine across species.** Simulate animal PK using animal physiology
   and animal in vitro data. A failure here is diagnostic and cheap: it means a
   biological mechanism exists that the ADME screening assays did not see, and it tells
   you this *before* you have committed to a human prediction. Only when the animal
   simulation recovers observed data should the same assumption set be carried into human
   physiology with human in vitro data.
8. **Identify the one missing parameter, deliberately.** When a mechanistically complete
   model still cannot recover clinical data, name the single suspect parameter - call it
   Parameter X - and fit it top-down against clinical data while every other parameter
   remains bottom-up. Then verify the completed model against an independent dataset not
   used in the fit. One fitted parameter with independent validation is a model; several
   fitted parameters without it is a curve.
9. **Couple the PD model to the concentration that actually drives the effect.** The
   reason to build a PBPK model rather than a compartmental one is access to tissue
   concentrations, so a PBPK/PD model that drives its PD from plasma has discarded its own
   advantage. Use the interstitial, intracellular or organ concentration at the site of
   action, and name which one.
10. **Sensitivity analysis and best/worst-case scenarios.** Rank parameters by influence
    on the output you actually care about, and re-run the extremes of the experimentally
    plausible range for the uncertain ones. Where the uncertainty is structural rather
    than numerical, simulate the model alternatives rather than widening an interval.
11. **Qualify quantitatively** - Phase A and Phase B below.
12. **Only then extrapolate** - to new doses, regimens, formulations, populations or
    species, by changing system and administration parameters alone.

## Parameter relationships that must hold internally

These let you falsify a build with arithmetic, before and independently of any
simulation.

**The Kp set implies a volume of distribution.** Vss is the volume-weighted sum of the
tissue partition coefficients plus plasma volume:

    Vss = Σ(V_T × Kp_T) + V_P

So a distribution model is not a free choice that only affects tissues. It makes a
falsifiable prediction about a number you can measure from IV data. If the chosen Kp
method implies a Vss that disagrees with observed IV Vss, that method is wrong for this
compound, and no amount of clearance adjustment will repair it - it will only hide it.

**Clearance and volume jointly determine half-life.** Because t½ scales as Vss/CL, a
model that reproduces AUC (and therefore CL) but misses the terminal slope has a
distribution error, not a clearance error. This one inference reassigns a great many
misdiagnosed "clearance problems".

**Michaelis-Menten parameters are unidentifiable from a single dose level.** When
substrate concentration is well below Km, the kinetics collapse to first order with rate
constant Vmax/Km, and only that ratio is estimable. If a model reports separate Vmax and
Km values but was built on dose-linear data, those two numbers are a reparameterisation
of one, and their apparent precision is an artefact. Saturable kinetics require
demonstrated dose non-linearity to identify.

**Bioavailability is a product of three bounded fractions.** F = fa × (1 − Eg) × (1 − Eh).
Predicted F can never exceed predicted fa, and the hepatic term is bounded by hepatic
blood flow. A model predicting F above its own fraction absorbed has an arithmetic error,
not a parameter problem.

**Renal clearance versus filtration discriminates mechanism without any new data.**
Compare observed renal CL with fu × GFR. Above it, active tubular secretion must be
operating and a filtration-only kidney will under-predict. Below it, reabsorption is
occurring. The cheapest mechanism test in the whole build. The renal impairment skill
develops it further.

**Count your fitted parameters.** For a genuinely mechanistic build, the number of
independent compound-specific parameters that must be estimated is typically fewer than
about five, because physiology supplies the rest. If you are estimating substantially
more, the prior knowledge is not doing its job and the model's extrapolation claim is not
supported.

## The assumption that most often silently breaks a PBPK model

**The tissue distribution model - because it is validated against the wrong observable.**
The distribution model is almost always selected by which one best reproduces the plasma
profile, and plasma is exactly the compartment least able to discriminate between them.
The failure is therefore invisible at the point it is made: the model looks validated, and
every tissue concentration it subsequently reports - including the target-site
concentration driving a coupled PD model - carries an unquantified error.

The diagnostic fingerprint has three parts:

- plasma agreement is good while predicted tissue concentrations differ substantially
  between distribution methods;
- the Kp set implies a Vss at the edge of, or outside, the observed value;
- the adjustment to lipophilicity needed to fit plasma exceeds the measurement
  uncertainty of logP.

That last point deserves saying plainly, because build workflows tend to soften it:
lipophilicity in a PBPK model is a *surrogate* parameter, tuned so a distribution equation
reproduces observed partitioning, and it is normal practice to adjust it slightly. There
is no established numerical limit on how much adjustment is acceptable. So the honest
treatment is to report the starting measured value, the final used value, and the
difference - and to read a large difference as evidence that the distribution model is
wrong rather than as a successful calibration. If tissue exposure matters to the decision,
in vivo tissue data are the only thing that resolves this, and the model should say so
rather than imply a precision it cannot have.

## Other assumptions that systematically bias output

- **Sampling site.** Clinical samples usually come from a peripheral vein; PBPK output is
  usually central venous or arterial. These differ, and differ most during the absorption
  and distribution phase - precisely where Cmax and tmax live. A model judged to have
  "over-predicted Cmax" may have been compared against the wrong compartment.
- **Aqueous solubility for poorly soluble lipophilic compounds.** Bile salts and lipids
  solubilise in vivo, so aqueous values under-predict dissolution. Biorelevant media
  (FaSSIF/FeSSIF) are not a refinement here, they are the correct input; aqueous data is a
  known route to under-predicted oral exposure and spurious food effects.
- **Raw in vitro permeability used as effective permeability.** Caco-2 or PAMPA apparent
  permeability must be calibrated against reference compounds with measured human jejunal
  Peff. Skipping the calibration transfers an assay-specific scale factor straight into fa.
- **Efflux transporter kinetics treated as predictive.** There is no reliable in vitro-in
  vivo correlation for P-glycoprotein kinetics. Gut efflux in a PBPK model is in practice
  a fitted description, not a prediction, and should be labelled as such - particularly
  before it is used to support a formulation or DDI argument.
- **Uptake transporter scaling treated as bottom-up.** Successful hepatic uptake
  predictions have generally required empirical scaling factors. A transporter-mediated
  model that reproduces observation *because* of a fitted scaling factor is
  compound-specific and must not be carried into another population without
  re-verification. The IVIVE skill covers the scaling chain in detail.
- **Gene expression as a proxy for protein abundance.** Relative tissue expression is a
  legitimate way to distribute a clearance process across organs, but every
  post-transcriptional effect is absorbed into the lumped catalytic term. That term is then
  organ-independent by construction - an assumption, not a finding.
- **Perfusion limitation applied by default to everything.** For highly bound or highly
  hydrophilic compounds, and for all large molecules, the vascular wall is not freely
  permeable, and a perfusion-limited model will reach equilibrium too quickly.
- **Large molecules built on the small-molecule template.** Therapeutic proteins need
  convective extravasation (two-pore formalism), lymphatic return, endosomal catabolism and
  FcRn recycling. A whole-body model without those is not a protein PBPK model regardless
  of how many organs it has.
- **Visual inspection treated as validation.** Eyeballing overlaid profiles is a necessary
  first check and an insufficient last one - which is what Phase B is for.

---

# Model qualification

Qualification has two phases, and they must run in this order. Phase A asks *what kind of
evidence exists*; Phase B asks *how close is it, numerically*. Running B without A produces
a precise number about a meaningless comparison - an AAFE of 1.2 against the very data the
model was fitted to is not evidence of anything.

## Phase A - Evidence hierarchy

Establish where each dataset sits before computing a single metric. Report the tier
alongside every metric, because the same AAFE means different things at different tiers.

| Tier | Comparison | What it demonstrates |
|------|-----------|---------------------|
| 0 | Recovery of data used to build the model | Internal consistency only. Necessary; near-zero evidential value alone. |
| 1 | Dose levels not used in the build | Structural adequacy. Deviation across dose points to structure, not parameters. |
| 2 | Other species, compound file unchanged | That drug parameters are genuinely drug parameters. |
| 3 | Special population after changing physiology only | That the system/drug separation holds. |
| 4 | Cross-compound consistency of shared system parameters | That system parameters are not compound-specific fudge. Two compounds binding the same receptor should not need different receptor concentrations. |
| 5 | Fully independent external data, used at no build step | Predictive validity. |

## Phase B - Goodness-of-fit analysis

### Step 1 - Agree the acceptance criteria before computing anything

**Ask the modeller what acceptance criteria they want, and anchor the question to the
stated intended purpose of the model.** Do not silently apply the twofold convention. The
order matters: criteria chosen after seeing the metrics are not criteria, they are a
rationalisation, and a reviewer will read them that way.

Ask it directly - something like: *"Before I compute the fit metrics: what acceptance
criteria should this model be held to, given what you intend to use it for? The common
default is AAFE < 2 on AUC and Cmax, but a target-site exposure claim or a label-enabling
extrapolation may warrant tighter, and an early discovery ranking exercise may warrant
looser."*

Useful framing to offer, without imposing:

- **What is the intended purpose?** Compound ranking, first-in-human dose, study design,
  a waiver argument, a label claim. The consequence of being wrong sets the bar, not
  convention.
- **Which endpoints carry the decision?** AUC and Cmax are the usual pair, but tmax
  matters for an absorption claim, t½ for a regimen claim, and a tissue concentration for
  a target-engagement claim.
- **Per-observation or aggregate?** Aggregate metrics hide systematic failure in one
  cohort. Require both.
- **What happens at the boundary?** Define `marginal` explicitly rather than discovering
  it later.

Record the criteria, who set them, and when - before Step 2. If the modeller declines to
set them, apply the AAFE < 2 convention on AUC and Cmax, and state in the output that the
criteria were defaulted rather than chosen.

### Step 2 - Compute the metric set

Let *P* be predicted and *O* observed for each paired observation *i*, over *n* pairs.

**Fold error, per observation.** The primitive everything else is built from:

    FE_i = P_i / O_i

Report the **distribution across cohorts** - per study, per dose, per age band, per
severity stratum, per species - not just a summary. A mean fold error near 1 composed of
one cohort at 0.4 and another at 2.5 is a failing model wearing a passing number. This is
the single most common way a GoF table misleads.

**Average Fold Error (AFE) - signed, direction of systematic bias:**

    AFE = 10 ^ ( (1/n) Σ log10(P_i / O_i) )

AFE > 1 means systematic over-prediction, < 1 under-prediction. Because over- and
under-predictions cancel, AFE alone can look excellent for a model that is badly wrong in
both directions. Never report AFE without AAFE.

**Absolute Average Fold Error (AAFE) - unsigned, magnitude of error:**

    AAFE = 10 ^ ( (1/n) Σ |log10(P_i / O_i)| )

AAFE ≥ 1 by construction, with 1 being perfect. This is the standard PBPK qualification
metric, and the conventional acceptance threshold is **AAFE < 2**, the "twofold criterion".
Treat that threshold as a convention with regulatory currency, not as a scientific
constant - it is the default to be argued away from, in either direction, on the basis of
intended purpose.

**Geometric Mean Fold Error (GMFE).** Report this, and *state the formula you used*,
because the literature uses the name inconsistently: some authors define GMFE identically
to AAFE (geometric mean of |fold error|, bias-insensitive), others define it as the
geometric mean of the signed ratio, which is algebraically AFE. Two papers quoting "GMFE
= 1.4" may not be quoting the same quantity. Writing the equation next to the number costs
one line and removes the ambiguity entirely.

**RMSE, on log-transformed and raw scales.** Where full concentration-time profiles are
available:

    RMSE_log = sqrt( (1/n) Σ (log10 P_i − log10 O_i)² )
    RMSE_raw = sqrt( (1/n) Σ (P_i − O_i)² )
    RMSE%    = 100 × RMSE_raw / mean(O_i)

Log-scale RMSE is the appropriate default for concentration data, which is
multiplicatively distributed and spans orders of magnitude; raw RMSE is dominated by the
peak and will effectively ignore the terminal phase. Report both when profiles exist, and
say which one any conclusion rests on. `RMSE%` normalises for cross-compound or
cross-study comparison.

**Visual predictive check data package.** Assemble, do not render: predicted mean profile;
predicted prediction interval (typically 5th–95th percentile) where a virtual population
was simulated; observed mean with SD or CI; and the observation metadata (study, dose,
route, matrix, sampling site, n). Export as a structured numeric dataset in the format
given in `reference/gof-output-schema.md`.

This skill does **not** produce final report figures. It produces the numeric VPC dataset
for a downstream reporting skill to plot. If no reporting skill is available in the
current environment, write the dataset to disk at the path the schema specifies and say
where it is - do not hand-roll a report figure in its place. A quick-look diagnostic plot
for the modeller's own eyes is fine and encouraged; label it as diagnostic, not as a
report figure.

### Step 3 - Assign a verdict per parameter, with the numeric basis visible

For every endpoint in the criteria, assign exactly one of `pass`, `marginal`, `fail`, and
show the number that produced it. A verdict without its arithmetic is an opinion.

    | Endpoint | Metric | Value | Criterion | Verdict | Basis |
    |----------|--------|-------|-----------|---------|-------|
    | AUC      | AAFE   | 1.31  | < 2       | pass    | n=14 pairs, 4 studies, tier 1 |
    | Cmax     | AAFE   | 2.14  | < 2       | fail    | driven by fed-state cohort, FE 3.6 |
    | t½       | AFE    | 0.71  | 0.5–2     | marginal| systematic under-prediction |

Rules that keep the table honest:

- A `fail` in any cohort is reported even when the aggregate passes, and the cohort is
  named.
- `marginal` requires a stated reason, not just proximity to a boundary.
- Where a cohort drives an aggregate result, say which cohort and by how much.
- Where n is small, give n. An AAFE from three pairs is a number, not an estimate.

### Step 4 - Offer the modeller more than one lens on the fit

A single metric answers a single question, and modellers reach for AAFE by habit even when
their actual question is something else. Offer these explicitly, and let the modeller
choose - several are usually worth running together, and they can disagree in ways that
are themselves informative:

- **Aggregate versus stratified.** The same dataset summarised whole, then split by study,
  dose, route, formulation, age band, severity stratum, genotype, species. Stratification
  is where mechanism failures surface.
- **Bias versus precision, separated.** AFE for direction, AAFE for magnitude. A model can
  be unbiased and imprecise, or precise and consistently wrong; those need different
  fixes, and one number cannot tell them apart.
- **Endpoint-wise versus profile-wise.** Derived-parameter agreement (AUC, Cmax, tmax, t½)
  versus whole-curve agreement (RMSE on the profile). A model can match every derived
  parameter with a visibly wrong shape, and vice versa.
- **Proportion within an interval.** The fraction of predictions falling inside twofold, or
  inside the observed CI - a robust, easily communicated complement to a mean-based metric,
  and less sensitive to one bad point.
- **Worst-case rather than average-case.** Maximum absolute fold error and its identity.
  For a safety-facing question the worst prediction is the relevant one, and averaging it
  away is a methodological choice that should be made deliberately.
- **Signed error against a covariate.** Fold error plotted against dose, age, body weight,
  eGFR, time after dose, or severity. A trend is a missing mechanism; scatter is noise.
  This distinguishes "imprecise" from "structurally incomplete" better than any summary
  statistic.
- **Population coverage.** For a virtual-population simulation, the fraction of observed
  individuals inside the predicted interval, and whether the predicted variability is too
  wide as well as whether it is too narrow. An interval wide enough to contain everything
  has not predicted anything.
- **Sensitivity-weighted.** How much the verdict moves across the plausible range of the
  most influential uncertain parameter. If `pass` becomes `fail` within the parameter's own
  confidence interval, the verdict is not robust and should be reported as such.

State which lenses were run and which were not. Silence about a lens reads as though it
was run and passed.

## Regulatory framing, stated honestly

PBPK is established in regulatory submissions, is explicitly advocated in DDI guidance, is
recommended in hepatic impairment guidance, and paediatric plan timelines have made it
near-routine for paediatric dose setting. Verify current guideline text before relying on
any specific criterion; these positions move, including the numeric ones.

One circularity is worth naming because it recurs in every waiver discussion: a model is
proposed to replace a clinical study, and the evidence that the model is reliable would
have come from that class of clinical study. There is no way to argue out of this from
inside the model. The defensible position is that clinical interaction and
special-population data *qualify* models rather than compete with them, and that the
strongest use of a qualified model is the scenario that cannot ethically or practically be
studied - extreme-risk individuals, untestable combinations, populations closed to
enrolment. That argument is available on the merits. "The model agrees with the data we
fitted it to" is not.

## Verification checklist

1. Every parameter classified as system, drug, or drug-biological, and every deviation
   from default physiology carries a written mechanistic rationale.
2. Distribution model named, with the reason for the choice, and implied Vss checked
   against observed Vss.
3. Perfusion versus permeability limitation stated per tissue, consistent with the
   transport processes the model claims to represent.
4. IV model established before oral; absorption parameters the only things varied at the
   oral step, or the simultaneous-fit exception explicitly declared.
5. Measured and finally-used lipophilicity both reported, with the difference visible.
6. Clearance apportioned across pathways using mass balance and excretion data, not
   fitted as a lump.
7. Renal CL compared against fu × GFR and the resulting mechanism claim stated.
8. Saturable kinetics justified by demonstrated dose non-linearity, or reduced to a
   first-order Vmax/Km ratio.
9. Predicted F internally consistent with predicted fa, Eg and Eh.
10. Count of independently fitted compound parameters reported.
11. Every comparison dataset assigned an evidence tier (Phase A).
12. Acceptance criteria agreed with the modeller, tied to intended purpose, and recorded
    *before* metrics were computed - or explicitly flagged as defaulted.
13. Fold error reported as a distribution across cohorts, not only as an aggregate.
14. AFE and AAFE both reported; GMFE accompanied by the formula used.
15. RMSE reported on the log scale where profiles exist, with raw and normalised values
    where relevant, and the scale behind any conclusion named.
16. VPC dataset exported in `reference/gof-output-schema.md` format, with its location
    stated.
17. Per-endpoint verdicts assigned as pass/marginal/fail with the numeric basis and n
    shown; cohort-level failures named even when aggregates pass.
18. Which additional GoF lenses were run, and which were not.
19. PD driven by a named tissue compartment at the site of action, not by plasma unless
    plasma *is* the site.
20. Sensitivity analysis over uncertain parameters, plus simulation of structural
    alternatives; verdict robustness across the most influential parameter reported.
21. Cmax, tmax, AUC and t½ each compared, with the sampling site of the observed data
    identified.
22. Limitations name every process the model does not represent, and every parameter whose
    value came from a fit rather than from measurement or physiology.

## How to report so that the reader gains agency

State the prediction; then the distribution model and tissue-limitation assumptions that
produced it; then the explicit split between parameters that were predicted and parameters
that were fitted; then the evidence tier and the agreed criteria; then the verdict table
with its arithmetic; then the sensitivity ranking; then the observation that would falsify
the model - a tissue measurement, a dose level, a species, a population. A reader who has
those things can disagree with you productively, or design the experiment that settles it.
A reader given only an overlaid profile can only believe you.
