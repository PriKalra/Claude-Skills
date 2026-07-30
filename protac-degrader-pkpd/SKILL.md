---
name: protac-degrader-pkpd
description: Build, interrogate and defend PK/PD and PBPK/PD models for targeted protein degraders - PROTACs, bifunctional degraders, molecular glues and monovalent degraders using VHL, CRBN, IAP or MDM2 E3 ligases. Covers event-driven versus occupancy-driven pharmacology, DC50 and Dmax, protein resynthesis as the rate-limiting step, indirect-response turnover models, the hook effect, ternary complex cooperativity, tissue-selective degradation, intracellular versus plasma exposure, and preclinical-to-clinical translation. Tool-agnostic. Use when modelling a degrader, when a degrader shows tissue-selective knockdown, when effect outlasts exposure, when designing a PK/PD study for a degrader, when converting DC50 to a dose, or when auditing a degrader PK/PD model. This is the skill for degradation-modality pharmacology - conventional inhibitors belong to the standard PBPK build and DDI skills.
---

# Targeted protein degrader PK/PD

## When to use this skill

Any time the pharmacology runs through *removal of a protein* rather than *occupancy of a
protein*: a PROTAC or bifunctional degrader, a molecular glue, an IMiD-class neosubstrate
degrader, a SERD, or any modality where the measured endpoint is protein level rather than
target inhibition. Also use it when a molecule that was modelled as an inhibitor behaves
like a degrader - effect persisting after exposure has gone, or knockdown appearing in one
tissue and not another at matched drug concentration.

The output is never a concentration-time profile with an Emax curve attached. It is an
exposure profile at the site of action, a degradation depth and duration, the protein
turnover rate that sets the recovery, and an explicit statement of which of those four the
prediction is most sensitive to.

## First principle: the effect is an event, not an occupancy

A conventional inhibitor produces effect while it is bound. Effect tracks concentration,
and duration of effect is a property of the drug's pharmacokinetics. A degrader does
something categorically different: it catalyses a *transient* ternary complex - degrader,
target protein, E3 ligase - which tags the target for ubiquitination and proteasomal
destruction, and then the degrader is free to do it again. The protein is gone. The drug
may also be gone. The effect persists.

This inverts the central relationship of PK/PD. **The duration of pharmacology is set by
the resynthesis rate of the protein, not by the half-life of the drug.** Every downstream
consequence in this skill follows from that one sentence:

- A degrader can be efficacious at exposures and dose frequencies that would be
  indefensible for an inhibitor of the same potency, because coverage of the dosing
  interval is not the objective.
- Conversely, a degrader against a rapidly resynthesised protein may need continuous
  exposure despite being catalytic, because the pool refills as fast as it is cleared.
- The PK driver is an empirical question with a genuinely open answer. Cmax, Cave and Cmin
  are all plausible, and dose-fractionation studies are how you find out - not inference
  from the potency assay.
- Complete degradation is not necessarily the goal. Intermittent regimens have been
  efficacious in preclinical models, which means the therapeutically relevant question is
  the *depth and duration of knockdown required for the functional endpoint*, not maximal
  Dmax.

Operational rule: before modelling, write down whether you believe effect duration is
governed by drug elimination or by protein resynthesis, and what observation would
distinguish them. If you cannot answer, the study that answers it is more valuable than
the model.

## Potency vocabulary, and why it is not interchangeable with inhibitor vocabulary

- **DC50** - concentration producing 50% degradation of the target protein. The degrader
  analogue of IC50, and not the same quantity.
- **Dmax** - maximum degradation achievable, expressed as percent loss. This has no
  inhibitor equivalent and it matters enormously: a compound with excellent DC50 and Dmax
  of 60% has a hard ceiling on its pharmacology that no dose escalation removes.
- **Ternary complex cooperativity** - the degree to which target and E3 ligase binding
  reinforce each other. Cooperativity, not raw binary affinity, is what distinguishes a
  potent degrader from a molecule that binds both partners and degrades nothing.
- **Binary versus ternary occupancy** - the degrader forms two unproductive binary
  complexes as well as the productive ternary one. This is the origin of the hook effect
  below.

A model that carries an IC50 where it should carry a DC50, or that has no Dmax parameter
at all, cannot represent degrader pharmacology no matter how well it fits.

## Model structure: what a degrader PD model must contain

The correct minimal structure is an **indirect response model with stimulation of
dissipation** - the degrader does not inhibit protein production, it accelerates protein
loss:

    dP/dt = k_syn − k_deg · P − k_deg,induced(C) · P

where P is target protein, k_syn is the zero-order synthesis rate, k_deg is the baseline
first-order degradation rate constant, and k_deg,induced is the degrader-driven additional
degradation, typically an Emax function of the *intracellular* concentration C at that
site.

Three components must be present, and the third is the one usually missing:

1. **Protein turnover.** k_syn and k_deg. These are properties of the protein and the
   tissue, not of the drug. They are prior knowledge in exactly the way physiology is prior
   knowledge in a PBPK model, and they should be sourced or measured rather than fitted
   alongside everything else.
2. **Degradation stimulation driven by local concentration.** An Emax term in
   k_deg,induced, parameterised by DC50 and Dmax, driven by the concentration at the site -
   linked to a PK or ideally PBPK model rather than to a plasma value.
3. **Direct binding inhibition, evaluated and then justified if omitted.** A degrader also
   occupies its target, so there is a conventional inhibitory pharmacology sitting
   underneath the degradation. In practice its contribution is often minor because
   degradation occurs at lower concentrations than meaningful occupancy - but "often minor"
   is a finding to demonstrate for your molecule, not an assumption to inherit. If the
   binding term is omitted, say so and say why.

## Parameter relationships that must hold internally

These falsify a degrader model with arithmetic, before any simulation.

**Baseline protein level is a ratio, and it bounds Dmax.** At steady state without drug,
P₀ = k_syn / k_deg. With drug, P_ss = k_syn / (k_deg + k_deg,induced). Therefore the
fractional protein remaining is k_deg / (k_deg + k_deg,induced), which is strictly greater
than zero for any finite induced degradation rate. **100% degradation is arithmetically
impossible while synthesis continues.** A model or an assay reporting complete knockdown is
reporting either a limit of detection or an error. This is the cheapest sanity check in the
skill and it is failed regularly.

**Recovery is governed by the protein's half-life, not the drug's.** After the degrader has
been eliminated, protein returns toward baseline with time constant ln2/k_deg. Two
inferences follow, and both are diagnostic:

- If observed recovery is *slower* than the protein's known turnover, degrader is still
  present at the site - your PK model is under-predicting tissue persistence.
- If observed recovery is *faster* than the protein's known turnover, something is wrong:
  the protein pool may be compartmentalised, the assay may be reading a different pool than
  the pharmacologically relevant one, or k_deg was taken from the wrong tissue.

**If effect duration tracks drug exposure, you do not have event-driven pharmacology.**
Test it explicitly rather than assuming the mechanism from the chemistry. A bifunctional
molecule can fail to be catalytic in vivo, in which case it is an inhibitor with a very
large molecular weight and should be modelled and optimised as one.

**Tissue exposure above DC50 is necessary but demonstrably not sufficient.** This is the
single most important empirical result in degrader in vivo pharmacology, and it should be
treated as a standing constraint rather than an anomaly: BTK degradation has been observed
in spleen and *not* in lung at comparable tissue compound concentrations of the same
molecule in the same animals. The PK was adequate in both tissues. The degradation was
tissue-selective anyway.

The consequence for modelling is structural, not parametric. Degradation capacity is a
*system* property that varies by tissue - E3 ligase abundance, proteasome capacity,
ubiquitination machinery, ternary complex geometry in that cellular context. A model in
which the only tissue-varying quantity is drug concentration cannot reproduce this
observation, and will confidently predict knockdown in tissues that do not exhibit it. If
you need tissue-selective pharmacology - and tissue-restricted degradation is one of the
principal attractions of the modality - the E3 ligase axis has to be represented
explicitly.

**The hook effect makes concentration-response non-monotonic.** At high degrader
concentration, unproductive binary complexes (degrader-target and degrader-ligase) compete
with the productive ternary complex, and degradation *decreases* as concentration rises. A
monotonic Emax model fitted over a low concentration range will extrapolate to high doses
in exactly the wrong direction, predicting more knockdown where less occurs. If a
concentration-response curve turns over, that is mechanism, not noise - and the model needs
a ternary-complex formulation rather than a single Emax term.

## The assumption that most often silently breaks a degrader model

**Using plasma concentration as a surrogate for the intracellular concentration that
actually forms the ternary complex.** Degradation happens inside cells. Plasma is separated
from that compartment by three sequential barriers, and degraders are unusually poorly
served by all three:

1. **Plasma to tissue.** High molecular weight and complex bifunctional structure make
   distribution permeability-limited. A perfusion-limited tissue model will equilibrate far
   too fast and will overstate early tissue exposure.
2. **Tissue to intracellular.** Cell membrane permeation is widely expected to be the
   rate-limiting step for degrader tissue disposition. Worse, degrader transfer across
   membranes may not follow classic pH-partition theory at all if it is mediated by active
   transporters - and the relevant transporters are, for this modality, largely
   unidentified. So the standard in silico route from physicochemistry to permeability is
   on weaker ground here than for a conventional small molecule.
3. **Total to free.** Non-specific plasma and tissue protein binding for degraders is
   poorly characterised, and the free fraction is what forms complexes.

The diagnostic fingerprint is direct and has been observed: a degrader present at
appreciable concentration *in the tumour* while being undetectable *in blood* at the same
timepoint. Plasma and site-of-action profiles for these molecules can diverge not just in
magnitude but in shape and in rank order over time. Any dose projection built on a plasma
PK/PD relationship inherits that divergence silently.

## Other assumptions that systematically bias output

- **Single-timepoint PD assessment treated as a PD characterisation.** This is the dominant
  practice in the published in vivo degrader literature and it is close to uninformative.
  A single timepoint cannot distinguish "no degradation" from "degradation that recovered
  before sampling", and cannot estimate protein half-life at all. Degradation has been
  observed to be rapid within an hour, sustained to around six hours, and partially
  recovered by twenty-four - a sampling scheme that misses that shape will misread the
  pharmacology entirely. Time courses at multiple dose levels are the minimum useful design.
- **Assuming protein turnover is constant across tissues.** Whether protein half-life is
  consistent across tissues, and how E3 ligase concentration and proteasome activity vary,
  is an open question. Treating k_deg as one number per protein is an assumption that
  directly produces the false tissue-uniformity described above.
- **Assuming Dmax is a property of the compound alone.** It is a property of compound,
  protein, cell type and E3 ligase axis together. Potency has been shown to be
  context-dependent across cell lines for the same molecule.
- **Reading dose from potency.** Efficacious doses in preclinical degrader studies have
  generally been above 10 mg/kg while in vitro potency is low nanomolar - a gap far larger
  than event-driven pharmacology would predict, plausibly because these molecules are not
  yet optimised. A dose projected directly from DC50 without a PK/PD model will be badly
  wrong, and wrong in the optimistic direction.
- **Ignoring target-mediated drug disposition.** Given high molecular weight and specific
  high-affinity binding to two proteins, TMDD is mechanistically plausible as a
  concentration-dependent elimination route. It has not been demonstrated for this
  modality. Treat it as a hypothesis to test where non-linear PK appears at low dose, not as
  a default and not as excluded.
- **Assuming the E3 ligase is a passive participant.** Recruiting an E3 ligase can produce
  pharmacology of its own - MDM2 recruitment stabilises p53 while degrading the target, and
  IMiD-class chemistry degrades neosubstrates alongside the intended target. The observed
  phenotype is then the combined pharmacology of target loss *and* ligase engagement. A
  model attributing all effect to target knockdown will misattribute both efficacy and
  toxicity.
- **Assuming neosubstrate degradation is consistent.** Where a degrader also degrades
  ligase neosubstrates, that secondary degradation has been observed to be considerably
  more variable between animals and between models than the primary target degradation.
  Variability in a secondary readout is a signal about mechanism, not just noise.
- **Extrapolating from xenograft oncology to anything else.** Almost all published in vivo
  degrader efficacy sits in xenograft tumour models, frequently with an implanted rather
  than endogenous target. Endogenous-target degradation in normal tissue is a different
  measurement, and the two have diverged within single studies.
- **Applying pH-partition-based Kp prediction without qualification.** The tissue
  composition equations were developed and validated for conventional small molecules.
  Degraders sit outside that chemical space, so a predicted Kp set carries more uncertainty
  than the equations' published performance implies. See the build-workflow skill on the
  Vss consistency check, which becomes more important here rather than less.

## Translation to human: what is and is not established

State this honestly, because the temptation to over-claim is strong in a hot modality.
Allometric approaches for degrader distribution and elimination, and mechanistic PBPK
defining human PK/PD from preclinical PK/PD, have not been established for this class in
the way they have for conventional small molecules. There is no long track record to lean
on.

The defensible strategy is sequential and it is worth following in order:

1. Establish (PB)PK/PD translation *across preclinical species* first, with a unified model
   rather than a per-species fit. Multiple predict-learn-confirm cycles.
2. Only with demonstrated cross-species translation, project a human dose regimen from the
   preclinical PK/PD relationship.
3. Treat everything before the first-in-human PK/PD readout as carrying irreducible
   uncertainty, and say so in the projection rather than in a footnote.

The three-pillars framing remains the right skeleton - exposure at the site of action,
target engagement, and expression of pharmacological activity - with the degrader-specific
amendment that pillar two is *degradation*, a downstream and time-shifted consequence of
binding rather than binding itself.

## Study design that makes a model possible

Most published degrader datasets cannot support a mechanistic PK/PD model, and the gaps are
consistent. Design against them:

- Concentration-time profiles in plasma **and** in the relevant effect compartment, at
  matched timepoints. Not plasma alone, and not tissue at a single time.
- PD time courses, not single timepoints, at **multiple dose levels** - dose-dependence of
  the depth and of the recovery are separate pieces of information.
- Sampling extended far enough to observe recovery, because recovery is what identifies
  protein half-life.
- Dose fractionation to identify the PK driver empirically.
- Compounds with deliberately differing PK profiles, or a designed washout, to separate
  drug kinetics from protein kinetics. This is the cleanest available route to an in vivo
  protein half-life.
- Functional or pathway readouts alongside protein level, so the protein-to-function
  relationship is quantified rather than assumed. Loss of protein is a biomarker; the
  clinical claim is about function.
- Adequate n. Two animals per timepoint has been common in this literature and cannot
  support the variance structure any model will need.

## Model qualification

Use the Phase A evidence hierarchy and Phase B goodness-of-fit apparatus in the
`pbpk-model-build-workflow` skill - including the requirement to agree acceptance criteria
with the modeller, tied to intended purpose, *before* computing metrics. Do not restate the
metric definitions here; the formulas and the output schema live there.

Degrader-specific endpoints to carry into that framework:

- Protein level as fraction of baseline, at each timepoint - the primary PD endpoint.
- Degradation depth (observed Dmax) and time to maximal degradation.
- Recovery time course, and the implied protein half-life.
- Site-of-action drug concentration, reported separately from plasma and never in place of
  it.
- Functional or pathway endpoint, related to protein level.

Degrader-specific strata that must be reported separately rather than pooled: **by tissue**
(this is where the spleen-versus-lung failure mode surfaces, and pooling hides it), by dose
level, by dosing regimen, by species, by endogenous versus implanted target, and by primary
target versus ligase neosubstrate.

## Verification checklist

1. Mechanism stated as event-driven or occupancy-driven, with the evidence, and with the
   observation that would distinguish them.
2. PD model is an indirect-response structure with stimulation of dissipation, not an Emax
   attached to concentration.
3. k_syn and k_deg sourced or measured, with the tissue they came from named - not fitted
   simultaneously with the drug parameters.
4. Fractional protein remaining checked against k_deg/(k_deg + k_deg,induced); no claim of
   100% degradation.
5. Dmax present as an explicit parameter, and treated as context-dependent rather than as a
   compound constant.
6. DC50 used, not IC50; the assay system, cell line and matrix (including serum content)
   stated.
7. Direct binding inhibition either represented or explicitly justified as negligible for
   this molecule.
8. Hook effect tested for over a concentration range wide enough to see it; ternary-complex
   formulation used if the response turns over.
9. Drug concentration driving the PD is the intracellular or site-of-action concentration,
   with the plasma-to-site relationship shown rather than assumed.
10. Tissue distribution modelled as permeability-limited; perfusion limitation justified if
    used.
11. Degradation capacity allowed to vary by tissue where tissue-selective pharmacology is
    claimed or observed; E3 ligase axis represented explicitly.
12. Protein recovery compared against independent knowledge of protein turnover, with
    disagreement in either direction investigated.
13. Effect duration compared against drug exposure duration, and the comparison reported.
14. PK driver (Cmax / Cave / Cmin) determined by dose fractionation, not asserted.
15. Time-course PD data at multiple dose levels; no conclusion resting on a single
    timepoint.
16. E3 ligase-intrinsic pharmacology and neosubstrate degradation evaluated as contributors
    to the observed phenotype.
17. Protein-to-function relationship quantified, not assumed to be linear or complete.
18. Non-linear PK at low dose evaluated against TMDD as a hypothesis.
19. Human projection preceded by demonstrated cross-species translation of a unified model;
20. Uncertainty in the human projection stated in the projection itself, including the
    absence of an established allometric or PBPK precedent for this modality.
21. Limitations name the ADME processes not characterised for the molecule - non-specific
    binding, blood cell and hepatocyte permeation, CYP and non-CYP catabolism, renal
    excretion, transporter involvement.

## How to report so that the reader gains agency

Give the degradation depth and its duration; the site-of-action exposure that produced it,
alongside the plasma exposure it is not the same as; the protein turnover rate that governs
recovery and where that number came from; the tissues where degradation was and was not
observed, with drug concentration in each; the relationship between protein loss and
function; and the observation that would falsify the model - a tissue that should degrade
and does not, a recovery faster than protein turnover permits, a concentration-response
that turns over. A reader with those can design the next study. A reader given a tumour
volume curve and a Western blot at one timepoint can only take your word for it.
