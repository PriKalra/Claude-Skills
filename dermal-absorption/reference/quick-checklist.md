# Dermal absorption - quick checklist

Run during model building and again before reporting. The "missing mechanisms" and
"local vs systemic" items are the two that decide whether the model can support the claim
being made of it.

## Declare the question first

- [ ] **Endpoint declared local or systemic.** A flux model is a flux model.
- [ ] If systemic: **absence of capillary clearance stated explicitly** — skin blood flow is not
      in the layer-diffusion model.
- [ ] Purpose named: topical bioequivalence / therapeutic transdermal dose / chemical risk
      assessment. The acceptance bar differs — a 2-fold error may be fine for a BE screen and
      not for a margin of safety.
- [ ] Non-animal-methods framing stated where relevant: the comparator is often **no study**,
      not a better study. That changes how the uncertainty should be weighed.

## Structural inputs

- [ ] Layers stated — SC, viable epidermis, dermis — with h_sc, h_ed, h_de and their sources.
- [ ] **Body site named** for every thickness and every flux value.
- [ ] **Hydration state named** and consistent with the thickness used:
  - [ ] fully hydrated in vitro → nominal ~43 µm
  - [ ] partially hydrated in vivo → single digits to low twenties µm
  - [ ] mixing them is a several-fold error in the *dominant* parameter
- [ ] SC thickness and hydration **not** applied as independent multipliers — hydrated SC swells,
      so h_sc already varies with hydration. Address the coupling or double-counting occurs.
- [ ] Definitional check: "epidermis thickness" in the source literature sometimes **includes**
      the SC (supra-papillary epidermis). Confirm which.
- [ ] Undulation handled: maximum vs minimal epidermis/dermis thickness are different quantities
      and mature differently (maximum increases with age; minimal may not).

## Compound parameters

- [ ] Three QSPR terms carried **with uncertainty ranges**, not as point values:
  - [ ] log₁₀ k_trans (transbilayer permeability)
  - [ ] log₁₀ K_lip/w (SC lipid/water partition) — uncertainty ~±0.43 log units
  - [ ] log₁₀ PC_pro/w (SC protein/water partition) — uncertainty ~±0.32
- [ ] Fitted on **adult** data, against **both** flux and cumulative receptor accumulation —
      flux alone leaves the diffusion/partition trade-off under-determined.
- [ ] Multiple random starts used for parameter identification.
- [ ] **Held FIXED across ages.** If compound parameters differ between adult and paediatric
      simulations, the extrapolation claim is void. Same rule as the whole-body build workflow.

## Maturation functions

- [ ] **Fitted and selected by cross-validated test error** (LOOCV), not assumed. Datasets are
      small; in-sample fit will mislead.
- [ ] Child and adult reference values sourced from the **same study** where possible.
- [ ] **Validity ranges respected** — the published forms have day bounds (SC ~1604 d, dermis
      ~9883 d, hydration ~1182 d) beyond which the ratio is 1. Do not extrapolate past them.
- [ ] **Hydration function NON-monotonic.** Below adult in the first 2 weeks (~17.7 RCU under 72 h
      vs ~31.5 adult), rising within the first 24 h, **above adult by ~1 month**, staying above
      until ~6 months (~41.5 vs ~30 AU), reaching adult over 1–2 years. Substituting a "simpler"
      sigmoid silently breaks this.
- [ ] Trajectories applied:
  - [ ] SC thickness → approaches adult ~**4 years** (8 µm at 3 mo → 14 µm at 4 y)
  - [ ] Epidermis → maximum close to adult by ~**1 month**
  - [ ] Dermis → **~40% of adult until ~2 years**, then rising to ~**27 years**
- [ ] Age-dependent parameters **sampled from age-conditional distributions**, ~100 runs per age;
      interval reported. A point prediction cannot be assessed against variable observation.

## Held-equal-to-adult assumptions — list them as assumptions

- [ ] Corneocyte volume fraction — held constant because the link to volume fraction is
      **unknown**, not because there is no difference. (Corneocytes do change to ~5 years, most in
      the first 2; infant corneocytes 6–24 mo are smaller.)
- [ ] SC lipid and protein content — held constant on the basis of **two conference abstracts from
      one group**. Thin evidence.
- [ ] Skin albumin — held constant because adult and full-term newborn are similar (~2.5–5 ng/µg
      protein). **NOT valid for preterm** — preterm skin albumin is greater than both adult and
      full-term.

## Missing mechanisms — declare all three

- [ ] **Skin surface pH** absent from the model. Newborn ~**6.5** vs adult **4–6**; falls within
      7–14 days, normalises by ~6 months.
  - [ ] Ionisable permeant (pKa roughly 4–7)? **Recompute fraction un-ionised at newborn surface
        pH** or declare the omission. Two pH units = 100-fold ionisation shift.
- [ ] **Transfollicular shunt** absent. Infant follicle density at 1–2 days is ~**10× adult**.
      If the compound permeates follicularly, the model is missing the dominant pathway in the
      population of concern — say so.
- [ ] **Skin blood flow** absent. Microvasculature disorganised at birth, matures over **4–5
      weeks**; capillary loops at 1–3 months; full-term oscillations reach lower adult range in
      4–5 days. This sits directly on the flux→systemic path.
- [ ] Also absent: **metabolism in dermis and epidermis**.

## Arithmetic and direction checks

- [ ] **Thinner infant SC → HIGHER infant flux.** J ∝ D·K·ΔC/h. Wrong direction = maturation not
      applied, or applied with wrong sign.
- [ ] Lower GA → higher flux than full term.
- [ ] **y_Q consistent with the time integral of y_J.**
- [ ] Site variation checked against the age effect: adult SC spans **~6–21 µm** across sites,
      while the age effect is **18–34%** thinner — and is itself site-dependent (34% thigh vs 18%
      inner arm in the same children). **Do not quote the age effect more precisely than site
      variation permits.**
- [ ] **SA:BW multiplier applied** for any systemic paediatric estimate — higher surface area per
      kg raises systemic dose per kg even at identical flux per cm². Separate from, and
      multiplicative with, the permeability change.
- [ ] Dermis error not expected to fix a flux misprediction — flux is SC-dominated; dermis matters
      for systemic delivery and stays wrong longest into adolescence.

## Evidence-base honesty

- [ ] **Measurement-modality dependence acknowledged**: histology found **no** significant infant
      vs adult SC difference; in vivo confocal microscopy and confocal Raman found infant SC
      thinner. The entire age effect on the dominant parameter rests on one class of technique.
- [ ] **Sub-3-month caveat carried**: only **one** retrieved study measured SC thickness under
      3 months, it contradicted the others (thicker at 4–7 days than 3 months, exceeding adult
      values), and it was **explicitly excluded** from the final maturation model — along with a
      preterm dataset. Predictions below 3 months rest on an under-informed function **by
      construction**.
- [ ] **Dermis conflict carried**: one study reports forearm dermis *decreasing* over the first
      weeks while others report increase. Unresolved.
- [ ] **Preterm predictions labelled provisional** below the validated range. The published skin
      anatomy review **did not focus on preterm**; evaluation covered GA **35–40 weeks** only
      (full-term and *late* preterm); the preterm assessment was stated to be preliminary; and
      preterm-vs-term was captured through **SC thickness alone**.
- [ ] Validation base size stated, not just the fold errors: **three compounds**, ~a dozen infant
      skin samples, chosen because adult and infant data existed within the same study.

## Qualification

Metrics and output schema live in `pbpk-model-build-workflow`
(`reference/gof-output-schema.md`). Acceptance criteria agreed with the modeller, tied to
intended purpose, before metrics are computed.

- [ ] Endpoints: steady-state flux; cumulative receptor accumulation; lag time; systemic
      bioavailability **only if the structure supports it**.
- [ ] Fold error = predicted/observed flux, reported as a **distribution**, not a mean.
- [ ] **Strata not pooled**: body site; age band; term vs preterm; GA week; hydration state;
      in vitro vs in vivo; per compound.
- [ ] **Age-differential sensitivity analysis run** (flag parameters whose sensitivity coefficient
      differs ≥15% between children and adults). Expect **SC thickness** and **log₁₀ k_trans**;
      expect absolute sensitivities **higher in neonates** — i.e. the neonatal prediction is the
      least robust one, and should be reported as such.
- [ ] Benchmark context: published fold errors ~0.55–1.4 (diamorphine), 0.96–1.26 (phenobarbital),
      0.93–1.2 (late preterm) — alongside a **12.8-fold miss** for buprenorphine.

## Before reporting

- [ ] **Anomalous observed variability interrogated as possible sample damage before adjusting the
      model.** A 30-fold flux difference between two neonatal skin samples was attributed to
      freezing/thawing/rehydration damage, not biology. Do not fit to broken data.
- [ ] Falsifying observation named — a follicle-rich site permeating faster than predicted, an
      ionisable compound behaving differently on newborn skin, a systemic concentration the flux
      model was never able to speak to.
- [ ] Scope respected: neonatal **systemic** physiology → neonatal skill; allometric dose bands →
      paediatric skill.
- [ ] Regulatory text (21 CFR 320.23, IVPT bioequivalence expectations) verified against current
      guidance rather than quoted from secondary summary.
