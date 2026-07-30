---
name: pbpk-ddi-assessment
description: Build, interrogate and defend model-based drug-drug interaction predictions - reversible inhibition, time-dependent inactivation, induction, transporter DDI, drug-gene interaction and complex networks. Tool-agnostic. Use when predicting a DDI magnitude, designing or waiving a clinical DDI study, reconciling a model with observed AUC ratios, or auditing someone else's DDI model for hidden assumptions.
---

# DDI assessment

## When to use this skill

Any time the question is "what happens to exposure when these two things are given
together?" - risk assessment from in vitro data, study design and prioritisation,
label language, extrapolation to untested perpetrator or object doses, extrapolation
to populations that were never studied (paediatric, renal, hepatic, genotype
subgroups), or review of a submitted model. The output is never a bare AUC ratio. It
is a ratio, plus the mechanism that produced it, plus the parameter it is most
sensitive to.

## First principle: classify the mechanism before touching a number

Every DDI model is a statement about which of a small number of mechanisms is
operating. Misclassification is unrecoverable downstream - no amount of fitting fixes
it. Force the classification first:

1. Reversible inhibition (competitive, non-competitive, uncompetitive) - fast onset,
   fast offset, magnitude tracks perpetrator concentration in real time.
2. Time-dependent / mechanism-based inactivation - onset and offset governed by enzyme
   degradation rate, not by perpetrator half-life. Effects persist after the
   perpetrator has gone and accumulate on repeated dosing.
3. Induction - slow onset and offset on the timescale of protein turnover, nuclear
   receptor mediated, tissue-specific (gut vs liver differ), and reversible only over
   days to weeks.
4. Transporter inhibition - uptake vs efflux, and organ-specific (hepatic uptake,
   renal secretion, intestinal efflux, BBB). Uptake inhibition raises systemic
   exposure while lowering intracellular exposure; efflux inhibition can do the
   opposite. The direction of the intracellular effect is often the pharmacology.
5. Combined / interplay - metabolism plus transport in the same organ. The common
   real-world case, and the one where a single-mechanism model gives a confidently
   wrong answer.
6. Drug-gene interaction and phenoconversion - genotype sets the baseline fm, so the
   same perpetrator produces different ratios in different genotypes, and an inhibitor
   can convert an extensive metaboliser into a phenotypic poor metaboliser.
7. Autoinhibition, autoinduction, and metabolite-mediated inhibition - the perpetrator
   is the object, or the real perpetrator is a metabolite that was never characterised.

Operational rule: write down the mechanism class and the evidence for it before you
open a simulator. If the evidence is only "the AUC ratio was 3", you have not
classified anything.

## The qualification workflow that generalises

This build order is what makes a DDI model transferable rather than a bespoke fit. It
is the pattern behind the published DDI qualification networks, and it is worth
following even for a single interaction.

1. Build and verify each compound as a standalone object model - IV and oral,
   single and multiple dose, across the dose range - before any interaction is
   simulated. Elimination must be split into pathway-specific fm values sourced from
   mass balance and phenotyping, not fitted.
2. Verify each perpetrator as a perpetrator using an accepted index object with a
   known, well-characterised fm. An inhibitor model is only as good as the object
   used to calibrate it.
3. Couple them and predict the interaction without re-fitting either model. Any
   parameter you change at this step is a confession that one of the standalone models
   was wrong.
4. Validate against independent interactions not used in building - other objects for
   the same perpetrator, other perpetrators for the same object. This is what
   qualifies the network rather than the pair.
5. Only then extrapolate to untested doses, regimens, populations, or genotypes.

Acceptance: **ask the modeller what criteria this model should be held to, and anchor the
question to its intended purpose, before computing any metric.** The common default is
predicted/observed AUC ratio and Cmax ratio within twofold - a convention with real
regulatory currency, not a scientific constant. A study-waiver argument or a label claim may
warrant tighter; an early risk-ranking exercise may warrant looser. Criteria chosen after
seeing the metrics are a rationalisation, and a reviewer will read them that way. If the
modeller declines to set them, apply the twofold convention and state in the output that the
criteria were defaulted rather than agreed.

Then report geometric mean fold error for the whole network, **with the formula used printed
next to it** - GMFE is defined inconsistently in the literature, sometimes as the
bias-insensitive geometric mean of absolute fold error and sometimes as the signed ratio.
Report per-pair performance as well as aggregate; aggregate GMFE hides systematic failure in
one mechanism class. For the full metric set (fold error distribution, AFE versus AAFE, RMSE,
VPC package, verdict tables) and the output schema, use the Phase A / Phase B apparatus in
`pbpk-model-build-workflow` rather than restating it here.

## Parameter relationships that must hold internally

These constraints let you falsify a DDI model with arithmetic alone.

The fm ceiling. Complete inhibition of one pathway gives at most an AUC ratio of
1/(1-fm). An observed ratio above that ceiling means fm is underestimated, a second
pathway is also inhibited, or a non-metabolic mechanism (transport, absorption,
binding) is contributing. This single check resolves more mechanism arguments than any
simulation.

The reciprocal use of the ceiling. Run it backwards: a strong index inhibitor that
produces a fivefold increase implies fm of at least 0.8 for that pathway. Clinical DDI
data are therefore the cheapest and most reliable way to constrain fm, and a model
whose fitted fm contradicts its own DDI data is internally inconsistent.

The Cmax-versus-AUC pattern is mechanistically diagnostic. A large AUC increase with a
modest Cmax increase points to hepatic clearance inhibition. Both rising together
points to first-pass involvement - gut wall enzyme or efflux. Cmax rising more than
AUC points to absorption-rate or efflux effects rather than clearance. If the model
reproduces AUC but not Cmax, the mechanism is misassigned even though the headline
number looks right.

Time course is diagnostic. Reversible inhibition tracks perpetrator concentration;
inactivation and induction do not. If the observed effect on day 7 differs from day 1
in a way your reversible model cannot generate, you have the wrong mechanism class.

Dose-response saturation. Reversible inhibition saturates as unbound perpetrator
concentration rises well above Ki. A model that is still linear in perpetrator dose at
high multiples is probably using the wrong effective concentration.

Unbound concentration at the site of interaction. Not plasma total, not average
plasma. Hepatic inlet unbound concentration for oral perpetrators acting on hepatic
uptake or first-pass metabolism; enterocyte concentration for gut wall effects. Using
systemic plasma concentration for a gut-wall interaction is a standard way of
under-predicting by several fold.

## The assumption that most often silently breaks a DDI model

The scaling factor between the in vitro inhibition constant and the in vivo effective
constant. Ki, IC50 and KI/kinact are measured in a system whose binding, partitioning
and enzyme content differ from the tissue. Uncorrected values embed a systematic,
compound-specific bias. Two corrections are mandatory and are the most commonly
missing pieces of an in vitro DDI package:

- unbound fraction in the incubation (microsomal or hepatocyte binding), without which
  Ki is overestimated and DDI risk is underestimated;
- blood-to-plasma ratio, needed to compute the unbound hepatic inlet concentration for
  oral perpetrators, particularly for hepatic uptake transporter risk assessment.

If a model reproduces one interaction only after an unexplained multiplicative
adjustment to Ki, that adjustment is a compound-specific empirical correction, not a
system parameter, and it must not be carried into other interactions or other
populations without re-verification.

## Other assumptions that systematically bias output

- Assuming static average concentrations when the mechanism is dynamic. Static
  mechanistic models are screening tools; they are deliberately conservative and are
  not evidence of magnitude.
- Ignoring the gut wall. For CYP3A objects, intestinal contribution frequently drives
  the majority of the interaction.
- Treating the inhibitor as the only perpetrator. Metabolites can carry substantial
  or dominant inhibitory potency. A major circulating metabolite - roughly, at least
  10 percent of drug-related material and at least 25 percent of parent AUC - needs its
  own in vitro characterisation.
- Assuming fm is a constant of the drug. It is a property of the drug in a population.
  It changes with genotype, with age and ontogeny, with organ impairment, and with
  concomitant induction or inhibition of the competing pathway.
- Assuming inhibition and induction cannot coexist. Net effects can be
  time-dependent and can reverse sign between day 1 and steady state.
- Assuming a negative clinical result for one enzyme generalises to related enzymes.
  It does not - a clean CYP3A induction study does not exonerate CYP2C8/2C9/2C19.
- Ignoring the object's own PD. Where the object has a pharmacodynamic marker
  (INR, glucose, QT), the exposure ratio is not the endpoint; the response ratio is.
- Using total plasma concentration for highly bound perpetrators without a validated
  unbound measurement.

## Populations change the answer, not just the confidence interval

Because fm is population-dependent, the same interaction has a different magnitude in
different populations, and this is a prediction the model should be asked to make
explicitly:

- Genotype. Baseline fm for the affected pathway differs; poor metabolisers may show
  no interaction at all while extensive metabolisers show a large one.
- Age and ontogeny. Enzyme maturation changes the fractional contribution of pathways,
  so a paediatric DDI can be smaller or larger than the adult one. See the paediatric
  skill for the quantitative demonstration.
- Organ impairment. Reduced clearance of one pathway raises the fm of the others.
- Ethnicity. Frequency of variant alleles and expression differences shift the
  population distribution of ratios.

## Verification checklist

1. Mechanism class explicitly stated with the evidence that supports it.
2. Object model verified standalone, IV and oral, single and multiple dose.
3. fm per pathway sourced from mass balance plus phenotyping, and cross-checked against
   any available index-inhibitor DDI data.
4. Perpetrator model verified against an index object before use.
5. In vitro potency corrected for incubational binding; blood-to-plasma ratio applied
   where hepatic inlet concentration matters.
6. No parameters re-fitted at the coupling step.
7. Predicted AUC ratio below the 1/(1-fm) ceiling, or the excess mechanism named.
8. Cmax ratio and AUC ratio both reproduced; gut wall represented for relevant objects.
9. Time course of onset and offset reproduced, not just steady-state magnitude.
10. Metabolites evaluated as objects and as perpetrators against the significance
    thresholds.
11. Acceptance criteria agreed with the modeller against intended purpose and recorded before
    metrics were computed, or flagged as defaulted; independent validation interactions
    reported, with per-pair and aggregate GMFE, the GMFE formula printed, and AFE and AAFE
    both given.
12. Sensitivity analysis over Ki (or KI/kinact, or Emax/EC50), fm, and the unbound
    fraction assumptions.
13. Population extrapolations stated as predictions with their own uncertainty, not as
    outputs.
14. Limitations name every mechanism the model does not represent.

## How to report so that the reader gains agency

Give the ratio, the mechanism split, the sensitivity ranking, and the observation that
would falsify the model. That is what allows a human to disagree productively, design
the confirmatory study, or accept the waiver argument on the merits.

