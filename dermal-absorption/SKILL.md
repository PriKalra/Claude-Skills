---
name: pbpk-dermal-absorption
description: Build, interrogate and defend mechanistic dermal and percutaneous absorption models - stratum corneum, viable epidermis and dermis diffusion, QSPR-derived partition and diffusion coefficients, in vitro permeation testing (IVPT), flux and cumulative-amount endpoints, and age-dependent skin maturation from neonate to adult. Covers why stratum corneum thickness and hydration dominate flux, anatomical site variation, the non-monotonic hydration trajectory, follicular shunt and skin pH gaps, surface-area-to-body-weight scaling of systemic exposure, and topical bioequivalence framing. Tool-agnostic and non-animal-methods oriented. Use when modelling a topical or transdermal product, predicting dermal exposure for chemical risk assessment, scaling adult skin permeation to children or neonates, interpreting IVPT data, deciding whether a skin permeation model can support a systemic exposure claim, or auditing a dermal model. Boundaries - this skill owns the skin route at any age; neonatal systemic physiology belongs to the neonatal skill and allometric dose scaling to the paediatric skill.
---

# Dermal absorption

## When to use this skill

Whenever the skin is the port of entry: a transdermal or topical drug product, a topical
bioequivalence argument, an occupational or consumer chemical risk assessment, or the
extrapolation of adult skin permeation data to a child or newborn. Also use it whenever a
model is being asked to convert *flux across skin* into *systemic exposure*, because that
conversion is where most dermal models quietly exceed their own structure.

Boundaries with neighbouring skills:

- **The skin route at any age, including neonatal skin maturation** → here.
- **Neonatal systemic physiology** - clearance ontogeny, body composition, protein binding,
  critical-care overlays → the neonatal skill.
- **Allometric dose scaling and dose-band translation** → the paediatric allometric scaling
  skill.

This is also, deliberately, a **non-animal-methods** skill. Human and animal skin samples are
scarce, animal skin differs from human skin, and paediatric skin samples are scarcer still -
so an in silico permeation model is not a convenience substitute for an animal study here, it
is frequently the only ethically available study. That framing should be made explicit in any
report, because it changes how the model's uncertainty should be weighed: the alternative is
not a better experiment, it is no experiment.

The output is never a flux number alone. It is a flux, the body site and hydration state it
refers to, whether the endpoint is local or systemic, and which age-varying mechanisms the
model does *not* contain.

## First principle: the stratum corneum is the model

A mechanistic skin model represents the skin as stacked compartments - **stratum corneum
(SC)**, **viable epidermis (ED)**, **dermis (DE)** - with thicknesses h_sc, h_ed, h_de. The
permeant is applied in a vehicle to the SC surface and diffuses inward by Fick's law. Within
each layer it partitions between lipid, protein and aqueous sub-phases, and the aggregate
behaviour of each layer collapses into a diffusion coefficient (D_sc, D_ed, D_de) and a
partition coefficient (K_sc/w, K_ed/w, K_de/w). Those aggregates decompose into phase-specific
coefficients, which in turn decompose into **quantitative structure-property relationships
(QSPRs)** driven by the permeant's physicochemistry.

The two outputs are:

- **flux, y_J** (e.g. µg/cm²/h) - the rate at the receptor;
- **cumulative amount, y_Q** (e.g. µg/cm²) - the time integral of flux.

Under in vivo conditions a bioavailability estimate can also be produced.

**Sensitivity analysis puts the SC parameters on top, consistently and across chemically
diverse compounds.** For lipophilic and hydrophilic permeants alike, flux and accumulation are
most sensitive to D_sc, K_sc/w and h_sc. Everything else in the model is secondary. Two
practical consequences:

1. **Effort belongs on the SC.** Getting the dermis thickness slightly wrong costs little;
   getting SC thickness wrong changes the answer.
2. **The SC parameters are also where the age effect lives.** A post hoc analysis comparing
   sensitivity coefficients between children and adults - flagging any parameter whose
   sensitivity differs by 15% or more - identified **SC thickness** and the transbilayer
   permeability term **log₁₀ k_trans** as the age-sensitive parameters, with absolute
   sensitivity coefficients *higher in neonates than in adults*. So the newborn prediction is
   more sensitive to the parameter you know least well, in the population where you have the
   least data.

The three uncertain QSPR quantities that drive SC diffusivity and partitioning, and which
therefore carry the compound-specific uncertainty, are: **log₁₀ k_trans** (transbilayer
permeability), **log₁₀ K_lip/w** (SC lipid/water partition), and **log₁₀ PC_pro/w** (SC
protein/water partition). Each has a published nominal QSPR and an explicit uncertainty range
around it, and each range is wide enough to matter - the lipid/water term carries roughly
±0.43 log units. Carry them as distributions, not as point values.

## The build order for an age-dependent dermal model

This mirrors the PBPK build discipline in `pbpk-model-build-workflow`, and the analogy is
exact enough to be worth stating:

1. **Set the model to adult skin** (a reference adult age, e.g. 30 years) with the compound's
   physicochemical parameters.
2. **Fit the uncertain, age-independent compound parameters to adult data** - the three QSPR
   terms above, estimated against observed adult flux *and* receptor-fluid accumulation from
   IVPT. Fitting both endpoints rather than flux alone constrains the diffusion/partition
   trade-off that flux alone leaves under-determined. Use multiple random starts.
3. **Hold those compound parameters fixed.** Change only the age-dependent skin parameters.
   This is the same rule as "drug parameters stay constant, system parameters change" in the
   whole-body build workflow, and violating it here destroys the extrapolation claim in exactly
   the same way.
4. **Sample the age-dependent parameters from their age-conditional distributions** rather than
   using point estimates. Adult distributions scaled by the maturation function give
   p(P_A | Age); running ~100 simulations per age produces a mean and a 95% interval that can
   be compared honestly against observed data.
5. **Compare against observed paediatric flux** using fold error = predicted / observed.

**Maturation functions should be fitted and selected, not assumed.** Candidate forms - sigmoid,
Hill, polynomial - fitted to literature-sourced ratios of child-to-adult parameter values, with
selection by **leave-one-out cross-validation test error** rather than by in-sample fit. This
matters because these datasets are small; an unpenalised fit will happily interpolate a dozen
points and generalise badly.

## What matures, what does not, and what is missing entirely

This three-way split is the most useful thing in this skill, because the third category is
where a validated model still gives a wrong answer.

**Age-dependent, characterised, and in the model:**

| Parameter | Trajectory |
|---|---|
| SC thickness | Infant SC thinner than adult; approaches adult around **4 years** |
| Viable epidermis thickness | Infant thinner; rises steeply, maximum epidermis close to adult by ~1 month |
| Dermis thickness | **~40% of adult until ~2 years**, then rises rapidly, continuing to ~27 years |
| SC hydration | **Non-monotonic** - below adult, then above adult, then adult (below) |

**Investigated and deliberately held equal to adult, because the data did not support a
change:**

- **Corneocyte volume fraction.** Corneocyte density, cluster formation, shape, thickness and
  adhesion do change - through about 5 years, most drastically in the first two - and infant
  corneocytes (6-24 months) are smaller than adult ones, attributed to a higher proliferation
  rate, becoming larger and flatter as proliferation slows. But the quantitative link from
  those observations to the *volume fraction* the model consumes is unknown, so the parameter
  was held at the adult value. That is the honest treatment; note that it is an
  acknowledged gap wearing the appearance of a settled parameter.
- **Lipid and protein content of the SC.** Measured urea, lipid (cholesterol, ceramides) and
  keratin contents were similar between infants (3-24 months) and their mothers, so the protein
  phase volume fraction and the lipid- and protein-to-dry-weight ratios were kept the same.
  Evidence base: two conference abstracts from one group. Thin, and worth knowing it is thin.
- **Skin albumin.** Adult and full-term newborn skin albumin are similar (both roughly
  2.5-5 ng/µg protein), so the aqueous fraction accessible to albumin was held constant. Note
  the exception buried here: **albumin concentration in premature infant skin is greater than
  in adults and full-term newborns** - so for preterm skin this parameter is *not* justified as
  constant.

**Documented to vary with age, and absent from the model. This is the load-bearing gap.**

- **Skin surface pH.** Newborn skin is near neutral - around 6.5 immediately after birth -
  against an adult range of roughly 4-6. It falls within 7-14 days and can normalise by about
  6 months. Skin pH governs enzymatic activity and lipid processing in the barrier, and for an
  ionisable permeant it governs the fraction un-ionised at the point of entry. See the
  ionization and binding skill: a two-unit pH difference is a hundred-fold shift in the
  ionised/un-ionised ratio for a permeant with a pKa in that window.
- **The transfollicular shunt route.** Hair-structure density in infants aged 1-2 days has been
  measured at roughly **ten times** adult density. For a compound whose permeation is
  substantially follicular rather than intercellular, a model containing only the
  stacked-layer route is missing the dominant pathway in exactly the population of concern.
- **Skin blood flow.** Microvascular structure is disorganised after birth and matures over the
  first **4-5 weeks**, when papillary loops resemble adult skin; capillary loop formation has
  been observed at 1-3 months; full-term skin blood flow oscillations reach the lower adult
  range within 4-5 days of life. Blood flow sets capillary clearance from the dermis and
  therefore the conversion from *permeation* to *systemic exposure* - and it is not in the
  model at all.

## The assumption that most often silently breaks a dermal model

**That the model's parameter set spans the mechanisms that actually differ with age - and
therefore that in vitro flux agreement licenses an in vivo systemic exposure claim.**

The failure is structural, not parametric, and it is invisible because the validation is done
on the endpoint the model *can* produce. A model can reproduce IVPT flux in infant skin
creditably - fold errors near 1 - while being incapable in principle of predicting systemic
exposure in that infant, because the three mechanisms above are missing and two of them
(follicular shunt, blood flow) sit precisely on the flux-to-systemic path.

The diagnostic fingerprint:

- in vitro flux agreement is good, and the in vivo systemic prediction has never been tested
  against systemic data;
- the compound is ionisable with a pKa between roughly 4 and 7, so the newborn's near-neutral
  surface pH changes the un-ionised fraction available to partition;
- the compound is known or suspected to permeate follicularly;
- the claim being made is a *systemic* one - a plasma concentration, a dose, a margin of safety
  - rather than a local flux or a bioequivalence comparison.

Operational rule: **state whether the endpoint is local or systemic, and if systemic, state
that capillary clearance is not represented.** A flux model is a flux model. Reporting it as a
systemic exposure model is the single most consequential overreach available in this domain.

## Other assumptions that systematically bias output

- **Anatomical site treated as incidental.** Adult SC thickness measured across body sites
  spans roughly **6 to 21 µm** - a threefold-plus range. The *age* effect is on the order of
  18-34% thinner in infants, and it differs by site itself: one study measured 34% thinner in
  lower thigh and only 18% thinner in upper inner arm in the same children. **Site variation is
  larger than the age signal.** A prediction that does not name the body site has an
  uncertainty exceeding the effect it is trying to report.
- **SC thickness and hydration applied as independent multipliers.** SC thickness *varies with
  the degree of hydration* - a hydrated SC swells. In vitro diffusion experiments assume a
  fully hydrated SC (nominal thickness around 43 µm, versus single-digit-to-low-twenties µm
  for partially hydrated in vivo measurements). Applying an age-dependent thickness ratio and
  an age-dependent hydration ratio as if they were separate risks double-counting. Be explicit
  about which hydration state each thickness refers to.
- **A monotonic hydration maturation function.** Hydration is not monotonic and modelling it as
  such is wrong in both directions at different ages. Newborns in the first two weeks have
  **lower** skin hydration than adults - capacitance around 17.7 ± 4.6 RCU under 72 hours old
  versus roughly 31.5 ± 6.9 in adults. It then rises steeply, beginning within the first 24
  hours, and by about one month **exceeds** adult values, remaining above adult until around 6
  months (roughly 41.5 versus 30 arbitrary units) before declining to adult values over the
  first one to two years. Since higher water content increases permeability - the SC acting as
  a reservoir promoting percutaneous absorption - the direction of the hydration contribution
  to flux *reverses* across the first month of life.
- **Measurement modality treated as neutral.** Whether infant SC is thinner than adult SC
  depends on how you measure it: **histological methods found no significant difference, while
  in vivo confocal microscopy and confocal Raman spectroscopy found infant SC thinner.** The
  in vivo methods are the basis for the current maturation functions. This is not a minor
  discrepancy to average over - it means the entire age effect on the dominant model parameter
  rests on one class of measurement technique.
- **Treating the maturation dataset as if it were dense.** Only **one** retrieved study measured
  SC thickness in neonates and infants under 3 months - the age group where the model is most
  often needed. And that study's values *contradicted* the others, reporting thicker SC at 4-7
  days than at 3 months and exceeding adult values. It was **explicitly excluded** from the
  final maturation function, and the alternative model built without it was selected. That is a
  defensible and transparently documented choice, and it should be carried forward as a
  documented exclusion rather than forgotten: the current SC maturation function is
  under-informed below 3 months, by construction.
- **Dermis errors misattributed.** The dermis contributes substantial in vitro experimental
  variability and matters for systemic delivery, but flux is SC-dominated. A dermis-thickness
  error should not be expected to fix a flux misprediction, and its trajectory - flat at ~40%
  of adult to 2 years, then rising to 27 years - means it is the parameter that stays wrong
  longest into adolescence.
- **Forgetting the surface-area-to-body-weight multiplier.** Children have a higher skin
  surface area relative to body weight than adults. So even at *identical* flux per cm²,
  systemic dose per kilogram is higher in a child. This is a separate factor from the
  permeability change and multiplies with it. Omitting it under-predicts paediatric systemic
  exposure regardless of how well the flux model performs.
- **Preterm skin treated as covered.** In the published age-dependent work, the anatomy and
  physiology review **did not focus on preterm neonates**, the evaluation data spanned only
  GA 35-40 weeks - full-term and *late* preterm - and the preterm assessment was described by
  its authors as preliminary only. Moreover, preterm-versus-term difference was captured
  **through SC thickness alone**. For infants below about 35 weeks GA, and particularly for
  those lacking vernix caseosa, the model is extrapolating outside its evidence base into
  exactly the population most vulnerable to percutaneous toxicity. The neonatal skill records
  that epidermal development completes around 34 gestational weeks and that preterm infants are
  more sensitive to percutaneous toxicity; those two facts together should make preterm
  predictions explicitly provisional.
- **Assuming in vitro skin samples are intact.** Freezing, thawing and rehydration can damage
  neonatal skin. In the published evaluation, one compound showed a **30-fold difference in
  observed flux between two full-term neonatal skin samples**, producing fold errors of 12.8 and
  0.35 for the same compound at adjacent ages - and the authors attributed this to probable
  experimental error from sample handling rather than to biology. When a model fails to
  reproduce inter-individual variability of that magnitude, interrogate the data before
  adjusting the model.

## Parameter relationships that must hold internally

Arithmetic checks, no simulation required.

1. **Thinner SC must give higher flux.** For a diffusion-limited barrier, steady-state flux
   scales roughly as D·K·ΔC/h. If a model with a reduced infant h_sc does not predict higher
   infant flux, the maturation was not applied or was applied with the wrong sign. The
   published models reproduce the observed direction - higher flux in younger infants, and
   higher still in lower-GA preterm infants - and any model that does not is wrong before it
   is imprecise.
2. **y_Q must be the time integral of y_J.** Flux and cumulative amount are not independent
   outputs; inconsistency between them is an implementation error.
3. **Hydration contribution must change sign across the first month.** Below adult in the first
   two weeks, above adult by one month. A monotonic function cannot do this.
4. **The stated SC thickness must match the stated hydration state.** ~43 µm implies fully
   hydrated in vitro conditions; low-single-digit to low-twenties µm implies partially hydrated
   in vivo measurement. Mixing them is a several-fold error in the dominant parameter.
5. **Body site must be named, and the age effect must not be quoted more precisely than the
   site variation permits** (roughly 6-21 µm across adult sites).
6. **Compound QSPR parameters identical between adult and paediatric simulations.** If they
   differ, the model was re-fitted per age and the extrapolation claim is void.
7. **Systemic exposure per kg must exceed the flux ratio alone** for a child, once SA:BW is
   applied. If predicted systemic exposure scales only with flux, the surface-area term is
   missing.
8. **Fold error reported separately for term and preterm**, and separately per compound. A
   pooled fold error across GA bands conceals the preterm extrapolation.
9. **Age-dependent parameters carried as distributions, not point values**, with the resulting
   interval compared against observation - a point prediction against a variable observation
   cannot be assessed.
10. **Ionisable permeant: fraction un-ionised recomputed at newborn surface pH (~6.5), not
    adult (~4-6)** - or the omission declared. Henderson-Hasselbalch, per the ionization skill.

## Model qualification

Use the Phase A evidence hierarchy and Phase B goodness-of-fit apparatus in
`pbpk-model-build-workflow`, including agreeing acceptance criteria with the modeller against
intended purpose before computing metrics. Formulas and the output schema live there.

Dermal-specific points:

- **Endpoints:** steady-state flux (y_J); cumulative receptor accumulation (y_Q); lag time; and,
  only if the model structure supports it, systemic bioavailability. Report flux and cumulative
  amount together - fitting or judging on flux alone leaves the diffusion/partition trade-off
  under-determined.
- **Fold error = predicted / observed flux** is the convention in this field. Note it is signed
  and per-observation, so it belongs in the fold-error *distribution* the build-workflow skill
  requires, not compressed to a mean.
- **Strata that must not be pooled:** body site; age band; term versus preterm; GA week;
  hydration state; in vitro versus in vivo; per compound.
- **The evidence bar is low in absolute terms and should be stated as such.** The published
  paediatric evaluation rests on **three compounds** and roughly a dozen infant skin samples,
  selected because adult and infant permeation had been measured *within the same study* - the
  right selection criterion, and one that severely limits available data. Its authors wrote that
  the results "provide only a small amount of evidence that the anatomy and physiology changes
  in the model are correct". Any qualification claim should inherit that humility rather than
  the fold errors alone. For context, the fold errors achieved were roughly 0.55-1.4
  (diamorphine) and 0.96-1.26 (phenobarbital), with 0.93-1.2 for late preterm - genuinely good -
  alongside a 12.8-fold miss for buprenorphine attributed to probable sample damage.
- **Sensitivity must be reported by age, not only overall.** The age-differential sensitivity
  analysis - flagging parameters whose sensitivity coefficient differs by ≥15% between children
  and adults - is what identifies *which* parameters the paediatric prediction actually rests
  on. Absolute sensitivities being higher in neonates means the neonatal prediction is the
  least robust one.

## Regulatory and product framing

For products not intended to be absorbed into the bloodstream, bioavailability may be assessed
by measurements reflecting the rate and extent to which the active moiety becomes available at
the site of action - which is what makes flux and cumulative amount legitimate regulatory
endpoints rather than surrogates. **In vitro permeation testing (IVPT) on excised human skin is
the established tool** for characterising rate and extent for transdermal and topical routes,
including for bioequivalence. Verify current guidance text before relying on any specific
criterion.

Two product-side points worth carrying:

- **Transdermal delivery is genuinely attractive in neonates**, because it avoids an intravenous
  line or oral administration, and because smaller dose requirements combined with higher
  permeability make it feasible. It is already in paediatric use - fentanyl, tulobuterol,
  lidocaine:prilocaine.
- **The same permeability makes it a risk-assessment problem.** Higher skin surface area to body
  weight ratio plus barrier immaturity means unintended dermal exposure matters more in
  children. Documented concerns include carcinogen-classified chemicals in children's bath
  products and dozens of hazardous chemicals identified in infant disposable diapers. A dermal
  model serves both the therapeutic and the toxicological question, and the acceptance criteria
  for the two are not the same - a factor-of-two error is tolerable for a topical
  bioequivalence screen and may not be for a margin-of-safety calculation.

## Verification checklist

1. Layer structure stated - SC, viable epidermis, dermis - with thicknesses and their source.
2. **Body site named** for every thickness and every flux.
3. **Hydration state named** (fully hydrated in vitro versus partially hydrated in vivo), and
   consistent with the thickness value used.
4. SC thickness and hydration not applied as independent multipliers without addressing their
   coupling.
5. The three QSPR terms (log₁₀ k_trans, log₁₀ K_lip/w, log₁₀ PC_pro/w) carried with their
   uncertainty ranges, not as point values.
6. Compound QSPR parameters fitted on **adult** data against **both** flux and cumulative
   accumulation, then held fixed across ages.
7. Maturation functions **fitted and selected by cross-validated test error**, not assumed.
8. Age-dependent parameters sampled from age-conditional distributions; interval reported.
9. Hydration maturation function **non-monotonic**.
10. Parameters held equal to adult (corneocyte volume fraction, lipid/protein content, skin
    albumin) listed **as assumptions**, with the preterm albumin exception noted.
11. **Missing mechanisms declared explicitly**: skin surface pH, transfollicular shunt, skin
    blood flow.
12. Endpoint declared **local or systemic**; if systemic, absence of capillary clearance stated.
13. Ionisable permeant: fraction un-ionised recomputed at age-appropriate surface pH, or the
    omission declared.
14. Follicular permeation considered; if the compound is follicle-dependent, the model's
    inability to represent ~10-fold higher infant follicle density is stated.
15. **SA:BW multiplier applied** on top of the flux change for any systemic paediatric estimate.
16. Direction check: thinner infant SC → higher infant flux; lower GA → higher flux.
17. y_Q consistent with the integral of y_J.
18. Term and preterm reported separately; **preterm predictions labelled provisional** below the
    validated GA range, noting that preterm was captured through SC thickness alone.
19. Measurement-modality dependence of the SC age effect acknowledged (histology versus in vivo
    confocal).
20. The documented exclusion of the sub-3-month SC dataset carried forward, and predictions
    below 3 months flagged as resting on an under-informed maturation function.
21. Anomalous observed variability interrogated as possible sample damage before the model is
    adjusted.
22. Fold errors reported per compound, per age, per GA band - not pooled.
23. Age-differential sensitivity analysis performed and reported; neonatal predictions
    identified as the least robust.
24. Non-animal-methods framing stated where relevant - that the comparator is often no study
    rather than a better study.
25. Acceptance criteria matched to purpose - a topical bioequivalence screen and a
    margin-of-safety calculation do not warrant the same bar.
26. Limitations name the size of the paediatric validation base (three compounds, ~a dozen
    samples) rather than only its fold errors.

## How to report so that the reader gains agency

Give the flux with its body site, hydration state and age; the cumulative amount alongside it;
whether the claim is local or systemic and what is missing from the systemic path; the fold
errors by compound and age band rather than pooled; the mechanisms the model does not contain -
pH, follicles, blood flow; and the observation that would falsify it - a follicle-rich site
permeating faster than predicted, an ionisable compound behaving differently on newborn skin, a
systemic concentration that the flux model was never able to speak to. A risk assessor or
formulator given those can decide what the number is worth. Given a flux value alone, they will
assume it means more than it does.
