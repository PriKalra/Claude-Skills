# Ionization and binding inputs - quick checklist

Run before the model is built (these are inputs, so errors here propagate everywhere) and again
before any DDI conclusion is reported.

## Physicochemical inputs

- [ ] **All** pKa values listed, each labelled acid or base, each with source (measured /
      calculated / which tool).
- [ ] Multiprotic compounds: major microspecies at each relevant pH identified, not just a single
      "fraction ionised".
- [ ] Lipophilicity labelled **logP or logD**, and if logD, at what pH. "Lipophilicity = 2" is not
      an input.
- [ ] logD ≤ logP at any pH where the compound ionises.
- [ ] Fraction ionised computed by Henderson-Hasselbalch at **every** pH the model uses - gastric
      (~1.5), each intestinal segment, plasma (7.4), lysosomal (~5). A single value is a red flag.
- [ ] pH-solubility profile across the GI range, not solubility at one pH. Basic compounds
      dissolve at gastric pH and can precipitate by pH 6.5.
- [ ] Biorelevant (SIF / FaSSIF / FeSSIF) solubility for poorly soluble compounds.

## Binding and matrix

- [ ] fu,p reported with source (measured / predicted), species, and matrix.
- [ ] fu,inc (incubational unbound fraction) present where in vitro potency is being scaled -
      see the IVIVE skill.
- [ ] **Rb (B:P) reported with its source.**
- [ ] Rb checked against ionization-class expectation:
  - [ ] non-ionised / basic → Rb near 1
  - [ ] acidic / zwitterionic → Rb near 0.55 (= 1 − haematocrit)
  - [ ] contradiction (acid at Rb 1.5, lipophilic base at Rb 0.55) investigated before use
- [ ] Rb not below ~0.55 for a merely erythrocyte-excluded drug.
- [ ] fu,b derived as **fu,p / Rb**, and confirmed **≤ 1**. (fu,p 0.8 with Rb 0.5 → fu,b 1.6 is
      impossible and catches transcription errors instantly.)
- [ ] **If Rb is well above 1: evidence that erythrocyte partitioning is readily reversible,
      obtained BEFORE any Rb-based interconversion of concentration, protein binding, or
      clearance.**
  - [ ] Reversible (e.g. binding to a soluble intracellular protein, CL_P = CL_B × Rb holds) →
        interconversion valid; plasma CL may legitimately exceed hepatic plasma flow.
  - [ ] Restrictive (tight binding to an erythrocyte enzyme; drug does not exit freely) →
        interconversion **invalid**; use the plasma-based assessment and say why.
- [ ] Any equation containing an organ **blood flow** term uses **blood** concentrations.
- [ ] CL_P = CL_B × Rb checked. Blood CL above hepatic blood flow (97 L/h) cannot be hepatic
      elimination alone.

## Hepatic inlet concentration

- [ ] Equation **derived, not copied**. Brackets explicit. Dimensionally checked.
- [ ] Fa convention stated and not mixed: agencies' **Fa × Fg**, or the original combined Fa.
      They are the same quantity under different names.
- [ ] ka stated as measured or as the 0.1 min⁻¹ default.
- [ ] Fa × Fg stated as measured or as the default of 1.
- [ ] QH stated (97 L/h = 1.62 L/min, 70 kg). Note it is *total* hepatic flow, not portal.
- [ ] Units consistent: dose in µmol with QH in L/min gives µM, matching Ki / IC50.
- [ ] **fu,p floored at 0.01** for basic static model assessments. Do not "improve" this to the
      measured value.
- [ ] Iin,max,u computed **both ways** (FDA plasma route, EMA/PMDA blood route) and confirmed
      **identical** — they are algebraically the same. Disagreement = arithmetic error.
- [ ] Iin,max,u confirmed **greater than** Imax,u. (This is the check that catches the published
      missing-plus-sign error.)
- [ ] Total Iin,max confirmed **different** between blood and plasma unless Rb = 1. If they agree
      at Rb ≠ 1, a conversion was skipped.
- [ ] Total hepatic inlet concentration never quoted without its matrix. Only the **unbound** value
      is matrix-invariant.

## Regulatory conclusion

- [ ] **Conclusion stated per agency criterion, with the cutoff quoted.**
  - [ ] FDA / PMDA: 1 + Iin,max,u/(Ki or IC50) ≥ 1.1
  - [ ] EMA: 25 × [I]u,inlet,max/(Ki or IC50) ≥ 1, i.e. effectively 1.04 — **more conservative**
  - [ ] Note EMA includes OCT1 in scope; FDA/PMDA criterion as quoted covers OATP1B1/1B3
- [ ] A negative under FDA/PMDA is not automatically negative under EMA. Say whose criterion.
- [ ] Symbol used matches the agency: FDA Iin,max,u (plasma); EMA [I]u,inlet,max (blood);
      PMDA Iinlet,max,u (blood). All also called [I]h.
- [ ] OATP **time-dependent** inhibition addressed — evaluation with and without preincubation of
      test drug with test system.
- [ ] Static model type named precisely. "Static mechanistic", "mechanistic static" and "MSPK" are
      one model. Induction assessment **not** combined with reversible + irreversible inhibition.
- [ ] Guidance numbers verified against **current** guideline text, not a secondary summary.
      Cutoffs move; the matrix-invariance algebra does not.

## Distribution consequences

- [ ] Distribution model includes ionised-species / electrostatic terms if the compound is a
      **lipophilic base** (Rodgers-Rowland, Schmitt). A neutral-molecule treatment will
      under-predict tissue Vss and mislocate the drug.
- [ ] Lysosomal / acidic-organelle trapping considered for lipophilic bases — liver, lung, kidney;
      also mitochondria, endosomes, Golgi.
- [ ] Acidic drugs: predominantly plasma-protein-bound and in tissue extracellular water — check
      the Kp set reflects this rather than intracellular partitioning.
- [ ] Kp-implied Vss cross-checked against observed Vss (build-workflow skill).
- [ ] Renal clearance assignment sanity-checked: polar ionised species clear renally, neutrals and
      lipophiles generally do not. Cross-check with fu × GFR (renal impairment skill).

## In vitro package priors by ionization class

- [ ] Strong acid / carboxylate, lipophilic → **OATP inhibition data** present?
- [ ] Cation / lipophilic base → **OCT inhibition data** present?
- [ ] Monovalent anion → **BSEP** data present?
- [ ] Lipophilic base → hERG, phospholipidosis assessed?
- [ ] Carboxylic acid → acyl glucuronide / acyl-CoA bioactivation considered?
- [ ] Piperidine / piperazine → reactive iminium / nitrenium considered?
- [ ] Metabolites above the DDI skill's significance thresholds characterised in their own right?

## Discipline on trends and uncertainty

- [ ] Class-level trends (acids clear hepatically less, ionised species more soluble, bases less
      selective) used only as **plausibility checks** — never as parameter values.
- [ ] Basicity/lipophilicity confound acknowledged wherever a class trend is invoked. Almost every
      published pKa reduction also reduced logD.
- [ ] pH-partition theory exceptions considered where relevant: ionised species do permeate;
      zwitterions permeate by non-classical mechanisms; carrier-mediated uptake may dominate; a
      fraction-un-ionised permeability estimate can be badly wrong.
- [ ] **Where pKa is uncertain: dependent parameters re-derived at each pKa value**, not perturbed
      one at a time — or the sensitivity explicitly labelled as a lower bound on true uncertainty.
- [ ] Each input flagged measured vs predicted, and the conclusion's sensitivity to the predicted
      ones stated (usually fu,p and Rb).

## Qualification

Metrics and output schema live in `pbpk-model-build-workflow`
(`reference/gof-output-schema.md`). Acceptance criteria agreed with the modeller, tied to
intended purpose, before metrics are computed.

- [ ] Endpoints: predicted vs measured fu,p; Rb; logD; pH-solubility **profile**; and the
      Ki-ratio against **each** agency cutoff separately, not a single pass/fail.
- [ ] Strata: by ionization class, by pKa band, and by measured-vs-predicted input.
