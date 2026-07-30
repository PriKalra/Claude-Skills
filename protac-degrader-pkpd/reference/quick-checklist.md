# Targeted protein degrader PK/PD - quick checklist

Run during model building and again before reporting. The tissue-stratification and
protein-turnover items are the ones that separate a degrader model from an inhibitor model
wearing degrader vocabulary.

## Before modelling - establish the mechanism

- [ ] Modality named: bifunctional degrader / molecular glue / monovalent degrader.
- [ ] E3 ligase named (VHL / CRBN / IAP / MDM2 / other).
- [ ] Event-driven vs occupancy-driven stated, **with the evidence**.
- [ ] The observation that would distinguish them identified - does effect outlast exposure?
- [ ] If effect duration tracks drug exposure: stop and model this as an inhibitor.

## Potency inputs

- [ ] **DC50**, not IC50. Assay system, cell line, and **matrix including serum content**
      stated - the same molecule can shift ~10x between buffer and mouse serum.
- [ ] **Dmax** captured as an explicit parameter. A 60% Dmax is a hard pharmacological
      ceiling that dose escalation does not remove.
- [ ] Dmax treated as context-dependent (compound × protein × cell type × ligase axis), not
      as a compound constant.
- [ ] Potency measured in the preclinical species of interest, not only human cells.
- [ ] Concentration range wide enough to detect a turnover in the response (hook effect).

## Protein turnover - the parameters that set duration

- [ ] k_syn and k_deg sourced or measured, **with the tissue they came from named**.
- [ ] Turnover parameters *not* fitted simultaneously with drug parameters - they are prior
      knowledge in the same sense physiology is.
- [ ] Baseline check: P₀ = k_syn / k_deg.
- [ ] Ceiling check: fractional protein remaining = k_deg / (k_deg + k_deg,induced).
      **No claim of 100% degradation** - it is arithmetically impossible while synthesis
      continues. Reported "complete degradation" is a detection limit or an error.
- [ ] Recovery time constant ln2/k_deg computed and compared against observed recovery.
  - [ ] Recovery slower than protein turnover → drug still present at site; PK model is
        under-predicting tissue persistence.
  - [ ] Recovery faster than protein turnover → compartmentalised pool, wrong assay pool, or
        k_deg from the wrong tissue.
- [ ] k_deg allowed to vary by tissue, or the uniformity assumption stated explicitly.

## PD model structure

- [ ] Indirect response model with **stimulation of dissipation** - not an Emax hung off
      concentration.
- [ ] Degradation stimulation driven by **intracellular / site-of-action** concentration.
- [ ] Direct binding inhibition either represented, or explicitly justified as negligible
      *for this molecule* rather than inherited as an assumption.
- [ ] If concentration-response turns over: ternary-complex formulation, not single Emax.
      A monotonic Emax extrapolates in the wrong direction at high dose.

## Exposure - the three barriers between plasma and the ternary complex

- [ ] Plasma → tissue: modelled as **permeability-limited**; perfusion limitation justified
      if used.
- [ ] Tissue → intracellular: membrane permeation represented; not assumed instantaneous.
- [ ] Total → free: non-specific plasma and tissue binding characterised, or its absence
      flagged as an uncertainty.
- [ ] Site-of-action concentration reported **alongside** plasma, never in place of it.
- [ ] Plasma-to-site relationship shown, not assumed. They can diverge in shape and rank
      order, not just magnitude (drug present in tumour, absent in blood, same timepoint).
- [ ] pH-partition / tissue-composition Kp prediction flagged as extrapolation outside its
      validated chemical space; Vss consistency check from the build-workflow skill run.
- [ ] Active transporter involvement considered - largely unidentified for this modality.

## Tissue-selective degradation

- [ ] Tissue exposure vs DC50 tabulated **per tissue**.
- [ ] Where exposure exceeds DC50 and degradation is absent: the missing factor is
      degradation machinery, not PK. Do not fix this by adjusting the PK model.
- [ ] E3 ligase axis represented explicitly if tissue-selective pharmacology is claimed or
      observed.
- [ ] Degradation capacity treated as a **system** property varying by tissue - E3 ligase
      abundance, proteasome capacity, ternary complex geometry.
- [ ] A model whose only tissue-varying quantity is drug concentration cannot reproduce
      selective degradation, and will predict knockdown in tissues that show none.

## E3 ligase and neosubstrate pharmacology

- [ ] Ligase-intrinsic pharmacology evaluated (e.g. MDM2 recruitment stabilising p53).
- [ ] Neosubstrate degradation evaluated where the chemistry implies it (IMiD/CRBN → IKZF1/3).
- [ ] Neosubstrate readouts reported separately from primary target - they are typically
      **more variable** between animals and models.
- [ ] Observed phenotype not attributed wholly to target knockdown where ligase engagement
      also contributes - this misattributes both efficacy and toxicity.

## Study design - the gaps that make modelling impossible

- [ ] Concentration-time profiles in plasma **and** effect compartment, at matched timepoints.
- [ ] PD **time courses**, not single timepoints. A single timepoint cannot distinguish "no
      degradation" from "degradation that recovered before sampling".
- [ ] Multiple dose levels - depth and recovery are separate pieces of information.
- [ ] Sampling extended far enough to observe recovery (the shape is roughly: rapid within
      1 h, sustained to ~6 h, partial recovery by 24 h - a scheme that misses this misreads
      the pharmacology).
- [ ] Dose fractionation run to identify the PK driver (Cmax / Cave / Cmin) empirically.
- [ ] Differing-PK compounds or a designed washout, to separate drug kinetics from protein
      kinetics - the cleanest route to an in vivo protein half-life.
- [ ] Functional / pathway readout alongside protein level. **Protein loss is a biomarker;
      function is the claim.**
- [ ] Adequate n. Two animals per timepoint is common in this literature and supports no
      variance structure.
- [ ] Inactive-degrader control included, not only an inhibitor comparator.
- [ ] Endogenous vs implanted target distinguished.

## Qualification

Metrics, formulas and output schema live in `pbpk-model-build-workflow`
(`reference/gof-output-schema.md`). Do not restate them.

- [ ] Acceptance criteria agreed with the modeller, tied to intended purpose, **before**
      metrics were computed.
- [ ] Evidence tier assigned per dataset (Phase A).
- [ ] Endpoints: protein fraction of baseline per timepoint; observed Dmax; time to maximal
      degradation; recovery course and implied protein half-life; site-of-action
      concentration; functional endpoint.
- [ ] Stratified **by tissue** - pooling hides the spleen-vs-lung failure mode.
- [ ] Also stratified by dose, regimen, species, endogenous vs implanted target, and primary
      target vs neosubstrate.

## Before reporting

- [ ] Effect duration vs exposure duration compared and reported.
- [ ] Non-linear PK at low dose evaluated against TMDD as a hypothesis - plausible for this
      modality, not demonstrated, not excluded.
- [ ] Protein-to-function relationship quantified, not assumed linear or complete.
- [ ] Human projection preceded by demonstrated **cross-species** translation of a unified
      model.
- [ ] Uncertainty stated in the projection itself, including that allometric and mechanistic
      PBPK precedent for this modality is thin.
- [ ] Claims of "not yet reported" checked against current literature - the 2019 source
      predates substantial movement in this field.
- [ ] Limitations name the uncharacterised ADME: non-specific binding, blood cell and
      hepatocyte permeation, CYP and non-CYP catabolism, renal excretion, transporters.
- [ ] Falsifying observation named - a tissue that should degrade and does not, a recovery
      faster than turnover permits, a concentration-response that turns over.
