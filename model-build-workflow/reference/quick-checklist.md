# PBPK model build - quick checklist

The live working checklist. Run it during the build, and again before reporting.
The Phase B section is the one most often skipped under time pressure and the one a
reviewer reads first.

## Before building

- [ ] Every number sorted into system / drug / drug-biological.
- [ ] Physicochemistry complete: MW (effective MW if halogenated), logP, pKa(s), solubility.
- [ ] Binding complete: fu in plasma, fu in the in vitro incubation, B:P ratio.
- [ ] Clearance apportioned from mass balance and excretion data, per pathway, before any fit.
- [ ] Observed datasets listed with dose, route, matrix, sampling site, n - and flagged
      `used_in_build` yes/no. Decide this now, not after the metrics.
- [ ] Intended purpose of the model written in one sentence.

## Structural choices - state each as an assumption

- [ ] Distribution model named (Poulin / Rodgers-Rowland / Berezhkovskiy / Willmann /
      Schmitt) with the reason.
- [ ] Implied Vss = Σ(V_T × Kp_T) + V_P computed and compared against observed Vss.
- [ ] Perfusion vs permeability limitation decided per tissue.
- [ ] Any tissue with an active transport claim is permeability-limited (a perfusion-limited
      organ has no membrane for a transporter to sit on).
- [ ] Large molecule? Two-pore extravasation, lymph return, endosomal catabolism, FcRn -
      or say the model does not apply.

## Build order

- [ ] IV model established first.
- [ ] Oral step varied absorption parameters only - or the simultaneous-fit exception
      declared with its reason.
- [ ] Biorelevant (FaSSIF/FeSSIF) solubility used for poorly soluble lipophilic compounds,
      not aqueous.
- [ ] In vitro permeability calibrated against reference compounds with human jejunal Peff,
      not used raw.
- [ ] Animal simulation run and recovered before the human prediction, with the compound
      file unchanged.
- [ ] Fitted parameters counted. More than ~5 per compound is a warning, not a detail.
- [ ] Each fitted parameter named, with its method and the dataset it was fitted to.
- [ ] Measured and finally-used lipophilicity both recorded; difference visible.

## Arithmetic falsification tests

- [ ] Kp-implied Vss consistent with observed Vss.
- [ ] t½ consistent with Vss/CL - if AUC fits but the terminal slope does not, suspect
      distribution, not clearance.
- [ ] Predicted F ≤ predicted fa; hepatic term bounded by hepatic blood flow.
- [ ] Renal CL compared against fu × GFR; secretion or reabsorption claimed accordingly.
- [ ] Saturable kinetics only where dose non-linearity was demonstrated; otherwise reduced
      to Vmax/Km.
- [ ] Dose-linear compound not carrying unidentifiable Michaelis-Menten parameters.

## Phase A - evidence tier

- [ ] Every comparison dataset assigned a tier 0-5.
- [ ] At least one tier ≥ 1 comparison exists before any predictive claim is made.
- [ ] Tier reported next to every metric.

## Phase B - goodness of fit

- [ ] **Acceptance criteria asked of the modeller, tied to intended purpose, and recorded
      before metrics were computed.** If defaulted to AAFE < 2, that is stated in prose.
- [ ] Fold error per observation computed and reported **as a distribution across cohorts**,
      not only as an aggregate.
- [ ] AFE (signed bias) and AAFE (magnitude) both reported - never one alone.
- [ ] GMFE, if reported, accompanied by the formula used. The name is used inconsistently in
      the literature.
- [ ] RMSE on log10 scale where profiles exist; raw and normalised (%) where relevant; the
      scale behind any conclusion named.
- [ ] BLQ / zero-observed pairs excluded, with the exclusion count and reason recorded.
- [ ] n stated for every metric.
- [ ] VPC dataset exported per `gof-output-schema.md`, with its absolute path stated.
- [ ] No prediction-interval series emitted from a mean-individual simulation.
- [ ] Verdict per endpoint: pass / marginal / fail, each with its numeric basis.
- [ ] Cohort-level failures named even where the aggregate passes, and the driving cohort
      identified.
- [ ] `marginal` verdicts carry a stated reason, and digitisation error checked against the
      margin.
- [ ] Additional lenses: which were run, which were not, and why not.
  - [ ] aggregate vs stratified
  - [ ] bias vs precision separated
  - [ ] endpoint-wise vs profile-wise
  - [ ] proportion within twofold
  - [ ] worst-case fold error and its identity
  - [ ] signed error against a covariate (dose, age, weight, eGFR, time)
  - [ ] population coverage, if a virtual population was simulated
  - [ ] sensitivity-weighted verdict robustness

## Before reporting

- [ ] Sensitivity analysis over uncertain parameters; structural uncertainty simulated as
      model alternatives rather than absorbed into an interval.
- [ ] Verdict robustness reported - does pass become fail inside the parameter's own
      confidence range?
- [ ] Cmax, tmax, AUC, t½ each compared individually; observed sampling site identified.
- [ ] PD driven by a named tissue compartment at the site of action, not plasma-by-default.
- [ ] Predicted-vs-fitted parameter split stated explicitly.
- [ ] Falsifying observation named - which tissue measurement, dose, species or population
      would break this model.
- [ ] Limitations list every process not represented and every value that came from a fit.
- [ ] Run directory not overwritten; previous qualification runs retained.
