# Neonatal PBPK - quick checklist

Run during model building and again before reporting. The age-clock items and the critical-care
overlay are the two that most often go unexamined.

## Age metrics - do this first

- [ ] **GA, PNA and PMA all recorded** for every subject and every simulation. PMA = GA + PNA
      checked.
- [ ] Dataset described as more than "neonates". A dataset without all three ages cannot verify a
      maturation function.
- [ ] **Each maturation function keyed to a named clock, with a reason.** Anchors:
  - [ ] CYP2E1 → **PNA**, not GA
  - [ ] glucuronidation → **PNA and PMA**, not GA
  - [ ] GFR → **weight, GA and PNA** together
  - [ ] epidermal barrier → **GA** (~34 GW), regardless of delivery timing
- [ ] Multi-pathway drug → multiple clocks. A single "age" covariate is wrong.
- [ ] **Preterm and term modelled separately**, not as two points on one curve.

## Build order

- [ ] Adult model verified against adult data **before** scaling.
- [ ] **The "comparable clearance routes" assumption examined for this drug**, not inherited from
      the workflow.
- [ ] Physiology **time-varying** across multiple-dose simulations. Neonates grow during the
      regimen; constant physiology biases toward over-exposure late in the course.
- [ ] Body weight allowed to fall in the first week: **5-7% term, 10-15% preterm <1500 g**.

## Metabolism

- [ ] **CYP3A substrate? Is CYP3A7 represented?** It is the most abundant newborn CYP and is absent
      from every adult model by construction. A CYP3A4-ontogeny-only model is a different mechanism,
      not a scaled one.
- [ ] FMO1/FMO3 crossover addressed if relevant (same pattern as CYP3A7/3A4).
- [ ] **Pathway substitution checked**: sulfation compensating for immature glucuronidation. Total
      clearance can look right while the split is wrong — which invalidates any DDI, metabolite
      safety, or genotype conclusion.
- [ ] **UGT isoform named.** Neonate→adult fold increases span ~3x (UGT2B15) to ~55x (UGT1A4). A
      generic UGT factor is an order-of-magnitude assumption.
- [ ] UGT-substrate CL/kg dramatically lower than adult (~1% of adult expression at GW 30-40). Near-
      adult CL/kg means ontogeny wasn't applied.
- [ ] Liver microsomal protein content applied as a **separate** system parameter from enzyme
      abundance.
- [ ] Clearance maturation **not** attributed to liver size alone — antipyrine clearance stays
      age-related after liver-weight correction.
- [ ] **Extraction ratio classified before investing in enzyme ontogeny.** Above ~70% extraction,
      hepatic blood flow governs; apply the higher hepatic-flow fraction of cardiac output
      (~38% children vs ~24% adults) plus the postnatal circulatory increase.
- [ ] Conflicting ontogeny evidence (carboxylesterases) carried as structural uncertainty and
      simulated as alternatives, not silently resolved.

## Distribution and binding

- [ ] Total body water applied: **~90% preterm, 80-85% term, 60-65% adult**.
- [ ] Body fat applied: **~3% very-low-birth-weight preterm, ~12% term**.
- [ ] **Hydrophilic Vd per kg HIGHER in neonates than adults.** Equal L/kg = composition not
      applied. Body-weight-based adult dosing under-doses hydrophilic drugs here.
- [ ] **Binding protein matched to ionization class**: albumin → acids; AAG → basic lipophiles.
- [ ] Albumin values used: **2.36 g/dL preterm (GW 23-34), 3.43 term, 4.0 adult** — ratio ~0.59
      preterm:adult, and predicted fu increase broadly commensurate.
- [ ] AAG trajectory used where relevant: low until ~260 days GA, then rising; term ~50% of adult.
      Different trajectory from albumin — using the wrong protein gets magnitude wrong.
- [ ] **Bilirubin / free fatty acid competitive displacement** considered for highly bound drugs.
      Raises unbound beyond what reduced protein alone predicts. Routinely omitted.

## Renal

- [ ] **Filtration, secretion and reabsorption represented separately.**
- [ ] **Glomerulo-tubular imbalance reproducible**: GFR matures faster than secretion. One renal
      scalar cannot fit both a filtered drug and a secreted drug, and will be biased in opposite
      directions.
- [ ] GFR values: **~40% adult in neonates (41 ± 15 mL/min/1.73 m²) → ~60% (66 ± 25) at PNA
      2-8 weeks → adult 100-125 by 8-12 months**.
- [ ] **Preterm GFR ~half of term**; nephrogenesis incomplete before GW 34.
- [ ] Tubular secretion **20-30% of adult**, adult capacity by 7-12 months.
- [ ] Tubular reabsorption last to develop — steepest 1-3 years, continuing into adolescence.
- [ ] Renal blood flow ~10% of cardiac output in the first week; RPF 20 (preterm) / 83 (term) /
      650 (adult, by 2 y) mL/min/1.73 m².
- [ ] **fu × GFR check run with AGE-APPROPRIATE GFR.** With adult GFR this test silently misleads.
- [ ] Continuing postnatal nephrogenesis in preterm infants (to ~40 days) noted.
- [ ] Biliary route: bile acid ~60% adult at birth, ~80% by 1 year; pool adult by ~2 years — or
      ~7 weeks if BSA-corrected. State which normalisation.

## Absorption by route

- [ ] **Gastric pH trajectory non-monotonic**: ~7 at birth → ~2 → >4 → ~2 by 2 years. Preterm higher
      than term.
- [ ] **Direction of gastric-pH effect consistent with ionization class**: weak bases absorbed more,
      weak acids less. Not a general "reduced absorption".
- [ ] Intestinal transit: ~4 h term (adult-like), **~4-fold longer preterm**.
- [ ] Intestinal pH ~6.6 duodenum/jejunum, ~6.8 ileum — slightly above adult.
- [ ] Reduced intestinal surface-to-volume ratio; **higher permeability in preterm (GW 26-36)**.
- [ ] **Neonatal intestinal fluid** used for poorly soluble lipophiles — lower bile acids, **no
      secondary bile salts**, higher protein and lipid. Adult FaSSIF/FeSSIF is not a neonatal medium.
- [ ] **Reduced intestinal CYP3A4 and P-gp allowed to INCREASE bioavailability.** A model that only
      reduces absorption has represented half the physiology — a common over-dosing source.
- [ ] Digestive enzyme immaturity considered for prodrugs and lipid formulations (enterokinase ~25%
      at GW 24; lactase ~30% at GW 34; trypsin ~90% at term).
- [ ] IM: **direction not assumed** — reduced muscle blood flow and contraction versus higher body
      water and capillary density.
- [ ] Rectal: modelled as **high-variability**, not as a first-pass-free oral route. Rectal
      acetaminophen showed lower rate *and* extent than oral.
- [ ] Percutaneous: treated conservatively in preterm (no vernix caseosa, barrier incomplete before
      ~34 GW) → hand to the **dermal absorption skill**.
- [ ] Inhaled: lung developmental stage checked (alveolar / bronchiole-epithelial / saccular).

## Transporters

- [ ] **No blanket "transporters are immature" assumption.** Look up the specific transporter.
- [ ] Counter-examples remembered: OCT2 high in newborns decreasing with age; intestinal OATP2B1
      higher in neonates than adults; several renal transporters age-independent.
- [ ] **BBB P-gp ~30-50% of adult at birth**, adult by 3-6 months — applied for CNS-active P-gp
      substrates.
- [ ] mRNA-only evidence flagged as such; mRNA-to-protein correlation is unclear for transporters.
- [ ] Where two methods disagree for the same transporter, primary reference consulted rather than
      trusting a review table row.

## Critical-care overlay — two extrapolations, not one

- [ ] **Overlay named**, and represented or explicitly declared absent.
- [ ] If present but unrepresented, prediction **labelled as being for a well neonate**.
  - [ ] **Asphyxia** — CL ↓, Vd ↑ or unchanged, absorption altered. (Ceftazidime t½ and trough ↑,
        CL and GFR ↓; gentamicin CL significantly ↓, needing prolonged interval.)
  - [ ] **Sepsis** — hydrophilic distribution ↑, lipophilic unchanged; CYP3A activity altered;
        beta-lactam glomerular **hyper**filtration possible (opposite direction to maturation).
  - [ ] **PDA** — CL ↓, Vd ↑ below GW 36.
  - [ ] **AKI** — alters **hepatic** blood flow and decreases CYP3A. Liver-kidney crosstalk.
  - [ ] **Therapeutic hypothermia** — absorption ↓, distribution either way, elimination ↓ or
        unchanged.
  - [ ] **ECMO** — every direction reported for every process. A model without ECMO-specific data
        is making an unsupportable assumption. Say so.

## Qualification

Metrics and output schema live in `pbpk-model-build-workflow`
(`reference/gof-output-schema.md`). Acceptance criteria agreed with the modeller, tied to intended
purpose, before metrics are computed.

- [ ] **Strata never pooled**: preterm vs term; GA band; PNA band; PMA band; birth weight band;
      well vs critically ill and by overlay. A model can pass overall and fail entirely below GW 30.
- [ ] Endpoints include **trough** where toxicity is trough-driven, and **metabolite ratios**
      wherever available — the endpoint that separates "right total clearance" from "right pathways".
- [ ] **Signed error plotted against GA, PNA and PMA separately.** A trend against one clock and not
      the others identifies which clock the maturation function should have used. Highest-value
      neonatal diagnostic and rarely made.
- [ ] n stated for every metric. Small n here is irreducible (parental consent, blood volume), not a
      design failing — but it bounds what can be claimed.
- [ ] popPK-derived evaluation datasets identified as such and tiered below independent observation.

## Before reporting

- [ ] Limitations name every maturational process not represented, every transporter lacking ontogeny
      data, and **the unknown neonatal ontogeny of the drug target itself** — matched exposure does
      not imply matched response in a neonate.
- [ ] Falsifying observation named — a trough above target in the youngest band, a mismatched
      metabolite ratio, signed error trending against GA but not PNA.
- [ ] Scope boundaries respected: allometry/dose-bands → paediatric skill; dermal → dermal skill;
      placental or breast-milk transfer → **outside this skill's evidence base entirely**.
