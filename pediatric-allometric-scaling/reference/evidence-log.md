# Evidence log - paediatric scaling and allometry

"OSP #n" refers to issues in Open-Systems-Pharmacology/OSP-based-publications-and-content
(labels: Pediatrics, plus title searches on allometric, ontogeny and maturation); 11 were
deep-read. Certara entries are publications, posters and blogs on certara.com. Paraphrased
throughout; consult the primary publications before relying on any numeric value.

## Size versus maturation, and exponent discipline

- OSP #109, #219, #583 - paediatric allometric scaling applications across age ranges;
  support the separation of size and maturation terms and the age-banded decision framework.
- OSP #131, #132 - allometric exponent behaviour in the very young, including midazolam in
  neonates (Mansoor et al.), where empirically estimated exponents exceeded 1. Basis for the
  rule that a supra-unity exponent in a neonatal dataset is a maturation signal rather than
  a size relationship.
- Theoretical pairing of exponent 0.75 for clearance and 1 for volume, implying half-life
  scaling with weight to the power 0.25: standard allometric theory, used here as an
  internal consistency check rather than as a prescription.

## Enzyme ontogeny

- Certara publication, A Re-evaluation and Validation of Ontogeny Functions for CYPs 1A2 and
  3A4 Based on In Vivo Data (Salem, Johnson, Abduljalil, Rostami-Hodjegan, Tucker, 2014).
  In vitro-derived ontogeny profiles have under-predicted in vivo clearance in neonates and
  infants for some substrates. Deconvoluting in vivo PK data (caffeine and theophylline for
  CYP1A2; midazolam for CYP3A4) gave functions in which relative CYP1A2 intrinsic clearance
  rises from birth to about 3 years then declines to adult values, and CYP3A4 rises
  continuously to adult values at about 1.3 years. Validation on ropivacaine and alfentanil
  showed improved mean squared prediction error versus the in vitro-based function. The
  paper also stresses that clinical condition confounds net in vivo clearance and must be
  accounted for - the basis for the "state the provenance of your ontogeny function" rule
  and for treating disease as a confounder.
- Certara publication, Prediction of Pediatric Pharmacokinetics for CYP3A4 Metabolized
  Drugs: comparison of two hepatic ontogeny functions within a PBPK model. Direct evidence
  that the choice of ontogeny function changes the answer.
- OSP #205 - ontogeny applications supporting pathway-specific maturation.

## Renal and transporter ontogeny

- OSP #545 - renal transporter ontogeny (Hunt et al.). Basis for treating renal transporter
  maturation as a separate, later and less certain track than glomerular filtration
  maturation.
- Glomerular filtration maturation with postmenstrual age: standard across the paediatric
  PBPK literature and the OSP paediatric set; used here as the best-characterised ontogeny.

## Phase 2 and biliary ontogeny

- Certara publication, The Development of UGT1A1 and 1A6 in the Pediatric Liver. Supports
  treating phase 2 ontogeny as its own track with disproportionate neonatal consequences.
- Certara publication, How Does the In Vivo Biliary Elimination of Drugs Change with Age?
  (Johnson, Jamei, Rowland Yeo, 2016). Using azithromycin, ceftriaxone, digoxin and
  buprenorphine, biliary elimination ontogeny appeared rapid: a small fraction of adult
  activity was needed in the very preterm (about 15 percent at 26 weeks gestational age for
  azithromycin, reaching full activity by around 7 months; about 10 percent at 30 weeks for
  digoxin), full activity appeared present at term for ceftriaxone, and for buprenorphine
  the age dependence was explained by CYP3A4 and UGT1A1 ontogeny with no separate biliary
  ontogeny required. Source of the caution to test identifiability before adding an extra
  ontogeny term.

## Absorption ontogeny

- Certara publication, Ontogeny of Oral Drug Absorption Processes in Children. Systematic
  review of age-related change in gastric pH, gastrointestinal motility, bile salts,
  pancreatic function, intestinal pH, and intestinal drug-metabolising enzymes and
  transporters, concluding that large information gaps exist across almost all processes.
  Basis for the rule that absorption uncertainty can exceed clearance uncertainty in the
  very young and must be stated rather than defaulted.

## Protein binding maturation

- OSP #134 - alpha-1-acid glycoprotein maturation in healthy and infected children
  (Maharaj et al.). Sigmoidal maturation of AAG with age; disease and infection shift the
  absolute concentration while the maturation shape is conserved. Basis for the rule that a
  disease-state model can scale the level without re-deriving the ontogeny, and for the
  warning about porting adult total-concentration targets into neonates.

## Age-dependent fm and paediatric DDI

- Certara publication, Age Related Changes in Fractional Elimination Pathways for Drugs:
  assessing the impact of variable ontogeny on metabolic drug-drug interactions (Salem,
  Johnson, Barter, Leader, Rostami-Hodjegan, 2013). Systematic analysis of the ontogeny of
  CYPs 1A2, 2B6, 2C8, 2C9, 2C18/19, 2D6, 2E1, 3A4 and renal function, with bootstrap
  methodology to define the age ranges over which pathway pairs differ statistically.
  Simulated example: a theoretical drug split equally between CYP2D6 and CYP3A4 at birth,
  given with a strong CYP3A4 inhibitor, produced AUC ratios of about 1.65 at birth, 2.4 at
  one year and 3.2 in adults; the authors note neonates can be more sensitive than adults
  in other scenarios. Conclusion adopted here: extrapolating adult DDI data across all
  paediatric age groups is not valid, and paediatric PBPK can identify periods of
  vulnerability where no clinical data exist.
- Certara blog, New FDA requirement to support labeling on DDIs in the pediatric population.
  Source of the caffeine example (CYP1A2-dominated in adults, renally eliminated in
  neonates), of the point that smaller gastric fluid volume relative to dose can change
  absorption-level interaction potential, and of the expectation that DDI evaluation is
  planned within the paediatric study plan with quantitative approaches used where
  differences are expected.

## Population representativeness and ethnicity

- Certara blog, Developing pediatric PBPK models for differing ethnic populations. Japanese
  paediatric population model developed with population-specific demographics and equations;
  verification used 14 drugs including 5 CYP3A4 substrates and 4 renally eliminated drugs,
  with adult performance confirmed before paediatric use and 44 of 62 PK parameter
  predictions reasonably close to observed. Comparing Japanese and North European paediatric
  templates against Japanese clinical data showed that representative population data
  mattered to accuracy. Also the source of the point that qualifying specific pathways,
  rather than only specific drugs, improves reusability and regulatory acceptance.

## Allometry versus mechanistic scaling

- Certara publication, Prediction of Metabolic Drug Clearance in Humans: In Vitro-In Vivo
  Extrapolation vs Allometric Scaling (Shiran et al., 2006). Across 15 CYP-cleared drugs,
  IVIVE was accurate in 14 of 15 cases while five allometric variants were accurate in 13,
  11, 10, 10 and 14 cases, with some allometric errors exceeding fivefold. Used here to
  support "mechanistic scaling is the more reliable default; empirical scaling fails without
  bound".
- OSP tyrosine kinase inhibitor paediatric comparison (Centanni et al.). Case in which the
  mechanistic model under-predicted a metabolite while allometry performed better on trough
  - the basis for treating allometry-versus-PBPK disagreement as a diagnostic signal rather
  than an inconvenience.
- Certara publication, Towards a Better Prediction of Peak Concentration, Volume of
  Distribution and Half-life After Oral Administration in Man, Using Allometry. Supports
  treating volume and clearance scaling separately.

## Extrapolation framework and clinical translation

- Certara blog, Reflections on the recent ICH E11A pediatric extrapolation guideline.
  Source of the two preconditions - disease similarity and response similarity - and of the
  clarification that disease similarity is not a sameness standard but a question of whether
  differences preclude extrapolation, including that a subgroup may be similar when the
  overall populations are not. Also the extrapolation-concept-then-extrapolation-plan
  structure, the role of biomarkers and Bayesian methods, and exposure matching.
- Certara blog, Pediatric drug development: optimizing extrapolation. Source of the framing
  that disease, target engagement/pharmacology and response to treatment jointly determine
  the extrapolation argument, with the belimumab Bayesian-prior case as a precedent for
  novel designs under recruitment constraints.
- Certara blog, Pediatric drug development: from wild west to model-informed confidence.
  Source of the framing that children are not small adults, that weight-based dosing alone
  rarely reflects the biology, that regulators now expect quantitative justification early
  in stepwise PIPs and iPSPs, and of model-informed precision dosing with Bayesian updating
  as a practical clinical route (illustrated by maturation-informed dosing in infants).
- Certara blog, Pediatric drug dosing: tackling big problems for little patients. Optimal
  sampling strategy with maximum a posteriori Bayesian estimation to recover AUC from three
  to four samples - the practical answer to sparse-sampling ethics in children, and the
  reason trough-only validation is a weak test.

