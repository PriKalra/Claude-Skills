# Evidence log - renal impairment

Maps each framework element in SKILL.md to its source. "OSP #n" refers to issues in
Open-Systems-Pharmacology/OSP-based-publications-and-content (label: Renal impairment),
each carrying the publication abstract; 13 were deep-read and all 34 titles scanned.
Certara entries are publications, posters and blogs on certara.com. Paraphrased throughout.

## Whole-body / systemic nature of CKD

- OSP #284 - whole-body PBPK framework for CKD (Malik and Edginton). Basis for the
  seven-axis formulation: filtration, secretion, reabsorption, non-renal enzyme and
  transporter activity, protein binding, distribution/haemodynamics, absorption.
- Certara publication, Modeling and Predicting Drug Pharmacokinetics in Patients with
  Renal Impairment (Rowland Yeo, Aarabi, Jamei, Rostami-Hodjegan, 2011). Establishes that
  renal failure influences hepatic metabolism (suppression or induction), protein binding,
  hepatic blood flow and metabolite accumulation - and that guidance moved to cover
  non-renally eliminated drugs for exactly this reason.
- OSP #781, #743, #708 - renal impairment PBPK applications; support the build order and
  the twofold AUC/Cmax accuracy expectation evaluated per stratum.

## Filtration versus secretion as separate axes

- OSP #361 - quantitative relationship between eGFR and OAT1/OAT3-mediated secretion
  (Dubinsky et al.). Source of the rule that secretion does not track filtration
  proportionally, and that secreted drugs are where a GFR-scalar model fails first.
- OSP #354, #655, #632 - transporter-mediated renal handling in impairment; support the
  filtration-ratio test as a mechanism-assignment tool.
- OSP #669, #656 - severity-stratified applications; support monotonicity checking and
  per-stratum error analysis.

## Endogenous biomarkers

- OSP #362 - endogenous biomarkers (creatinine, N-methylnicotinamide) used to calibrate
  renal transporter function in models.
- Certara blog, ICH M12 guidelines and your drug-drug interaction package (Gil Berglund,
  Rioux, Rowland Yeo). Names the recognised panel: coproporphyrin I (OATP1B1/1B3);
  N-methylnicotinamide and N-methyladenosine, plasma and urine (OCT2/MATE1/MATE2-K);
  pyridoxic acid (OAT1/OAT3); 4-beta-hydroxycholesterol/cholesterol and urinary
  6-beta-hydroxycortisol/cortisol (CYP3A).
- Certara blog, An endogenous biomarker for predicting OATP1B DDIs. Source of the design
  points reused here: low baseline intersubject variability permits a single pre-dose
  sample plus post-dose time course; a selective probe rules a mechanism out (the OATP1B
  versus BCRP deconvolution for rosuvastatin); simple static criteria are conservative, so
  a biomarker readout can rescue a false-positive in vitro signal; biomarker data can be
  used to estimate in vivo inhibition potency.
- Creatinine-as-transporter-substrate artefact (pseudo-AKI): synthesis of the above with
  the OCT2/MATE biomarker literature. Practical test - discordance between creatinine-based
  eGFR and an independent filtration marker such as cystatin C, iohexol or inulin.

## Confounded readouts in CKD

- Certara publication, In Vivo Indices of Enzyme Activity - The Effect of Renal Impairment
  on the Assessment of CYP2D6 Activity (Rostami-Hodjegan, Kroemer, Tucker, 1999). Urinary
  metabolite recovery indices depend on renal function to a degree that varies with the
  marker's own elimination routes, so different probes disagree in the same patients;
  drug-to-metabolite ratios from complete urine collection behave consistently. Also
  supports the substantive claim that apparent CYP2D6 activity declines in parallel with
  renal function.

## Non-renal clearance in uraemia

- Certara poster, combining clinical therapeutic drug monitoring data with bottom-up
  systems data to understand the effect of renal impairment on non-renal clearance, using
  tacrolimus as the worked example. Source of the middle option in the three-tier approach
  (proteomics-informed, TDM-informed, or bounded).
- OSP #426 - bexarotene (shared with the IVIVE set); illustrates that a scaling assumption
  validated in health does not automatically hold in altered physiology.

## Organ-impairment strategy and regulatory acceptance

- Certara blog, Minimizing risk in hepatic impairment studies (Barter, Wood, 2026). The
  hepatic framework is used as the template for the renal decision tree. Elements adopted:
  the more-than-20-percent-of-absorbed-dose logic for whether a dedicated study is
  expected; full versus reduced study designs with matched controls; single-dose adequacy
  when PK is dose-proportional and time-independent, multiple-dose when induction,
  inhibition or time-dependence exist; PopPK covariate analysis and PBPK as accepted
  alternatives, and their combination as the most efficient route; the consequence of no
  study being restrictive labelling plus post-marketing commitments; the systems parameters
  that must move with severity (organ volumes and blood flows, albumin and AAG, enzyme and
  transporter expression by severity category, GFR, gastric residence time); the
  performance benchmark of AUC ratios within twofold with average fold error generally
  below 1.5; and the four conditions that raise the likelihood of a model being accepted in
  lieu of a study (characterised ADME with mass balance and phenotyping, understood
  non-linearity and single-to-multiple-dose behaviour, DDI data to refine fm, and a modest
  predicted exposure shift). The same source flags that confidence outside CYP-mediated
  clearance is structurally lower (few validated probes for UGTs) and that organ impairment
  can affect absorption, not only elimination.

## Dialysis

- Certara publication, Pharmacokinetic Profile of Defibrotide in Patients with Renal
  Impairment. Within-subject dialysis versus non-dialysis comparison with exposure ratios
  and 90 percent confidence intervals inside 80-125 percent, indicating no meaningful
  removal; no accumulation across multiple doses; and roughly 35-37 percent higher Cmax and
  50-60 percent higher AUC in severe/ESRD versus matched controls. Used as the design
  template for dialysis-effect evaluation.
- Certara publication, Pharmacokinetics and Safety of Oseltamivir in Patients with
  End-stage Renal Disease Treated with Automated Peritoneal Dialysis. Basis for treating
  peritoneal dialysis as a distinct modality and for evaluating metabolite removal
  separately from parent.
- Certara publication, Population PK/PD analysis for a monoclonal antibody in end-stage
  kidney disease undergoing dialysis. Reminder that large molecules are not removed by
  conventional dialysis and follow different logic entirely.

## Acute kidney injury, paediatric renal dysfunction, combined impairment

- OSP renal set, aminophylline/methylxanthine dosing in preterm neonates with renal
  impairment (Tummala et al.). Demonstrates layering acute renal staging on top of
  maturation and using age-appropriate normals rather than adult thresholds.
- Certara poster, PBPK modelling of the effect of chronic kidney disease on the PK of
  drugs eliminated non-renally. Supports the non-renal axis as a modelled, not assumed,
  effect.
- Combined renal-plus-hepatic and transplant considerations: synthesis across the OSP
  renal set and the hepatic impairment framework above.

