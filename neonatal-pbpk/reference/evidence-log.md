# Evidence log - neonatal PBPK

Provenance for the non-obvious claims in `SKILL.md`.

**Primary source** (present in the source folder as a PDF):

- **Zhang W, Zhang Q, Cao Z, Zheng L, Hu W.** Physiologically Based Pharmacokinetic Modeling in
  Neonates: Current Status and Future Perspectives. *Pharmaceutics* 2023; 15:2765.
  doi:10.3390/pharmaceutics15122765. Department of Clinical Pharmacology, Second Affiliated
  Hospital of Anhui Medical University. Cited below as **Zhang 2023**.

Zhang 2023 is a narrative review of neonatal physiology combined with a **PRISMA systematic
review** of neonatal PBPK modelling studies: PubMed, Web of Science and Google Scholar searched
to **7 September 2023**; 895 records; 188 duplicates removed; 707 titles and abstracts screened;
573 full texts read; **56 articles included**. Exclusions were reviews, comparative studies,
animal modelling, toxicology and environmental modelling, placental and lactational transfer
modelling, popPK modelling, DDI, and paediatric studies not including newborns. That exclusion
list matters when judging coverage - placental and lactational transfer, and neonatal DDI, are
outside this review and therefore outside this skill's evidence base.

Age definitions used throughout Zhang 2023 and adopted here: fetus 0 years; **neonates
0-1 month**; infants 1 month-2 years; children 2-12 years; adolescents 12-16 years; adults
>16 years.

Where a claim originates in a paper cited *by* Zhang 2023, that downstream reference is named
where Zhang give enough to identify it.

---

## Workflow and its stated assumptions

- The standard method - **modify a verified adult PBPK model** by applying age-dependent
  physiological changes to generate preterm and full-term neonatal models, then validate against
  neonatal clinical data - is **Zhang 2023** section 2.4 and their Figure 1, citing Maharaj &
  Edginton, *CPT Pharmacometrics Syst Pharmacol* 2014; 3:e150.
- **The two load-bearing assumptions are stated explicitly by Zhang 2023**: "This strategy is
  based on the assumption that clearance routes in adults and neonates (preterm and term) are
  comparable, and that the structure of the PBPK simulation is similar in adults and newborns."
  This sentence is the target of the named-failure-mode section in `SKILL.md`.
- **That neonatal PBPK modelling "should reflect observably growing physiology with neonates'
  postnatal days, particularly when simulating multiple dosing scenarios in this population",
  as implemented in some dedicated tools**: **Zhang 2023**. The inference in `SKILL.md` that a
  constant-physiology multiple-dose simulation biases toward over-exposure late in the course is
  a synthesis step.
- Both whole-body and simplified/lumped mechanistic PBPK models in use: **Zhang 2023**.
- Applications listed: dose selection, early clinical trial design, correlation with target organ
  toxicities, DDI, and effect of impaired organ function: **Zhang 2023**.
- Practical constraints on neonatal clinical research - **low consent rates from neonatal parents
  and limited blood volume availability** - are **Zhang 2023**, introduction. These are the reason
  small n is irreducible rather than a study-design failing.
- That **popPK simulation can construct pharmacokinetic datasets usable in PBPK model
  evaluation, "particularly suited for neonates who cannot accept intensive blood collection"**:
  **Zhang 2023**, future directions. The caution in `SKILL.md` that such a dataset is not
  independent observation and should be tiered accordingly is a synthesis step connecting to the
  Phase A hierarchy in the build-workflow skill.

## The three age clocks

- GA / PNA / PMA are used throughout **Zhang 2023**; the composite relationship PMA = GA + PNA is
  standard usage rather than a Zhang claim.
- **CYP2E1 expression "is highly correlated with increased PNA rather than GA"**: **Zhang 2023**,
  citing their ref 88.
- **"Similar to CYP450 enzymes, glucuronidation capacity maturation in neonates depends on PNA
  and postmenstrual age (PMA) rather than on GA"**: **Zhang 2023**, citing ref 96.
- **"Generally, the GFR is limited to newborns and appears to be dependent on weight, GA, and
  PNA"**: **Zhang 2023**, citing ref 148.
- **Epidermal development "is dependent on gestational age (GA) and is complete at the 34th
  gestational week"**: **Zhang 2023**, citing Afsar, *Clin Exp Dermatol* 2010; 35:346-350.
- **The elevation of these scattered observations into a first principle - that different pathways
  key on different clocks, that a single "age" covariate is therefore wrong for a multi-pathway
  drug, and the worked 28-week/8-week versus 36-week/0-week PMA-collision example - is a synthesis
  step.** Zhang 2023 report the individual correlations; they do not draw the general rule or
  construct the counterexample.

## The CYP3A7 / CYP3A4 crossover - the named failure mode

- **"The most abundant CYP450 enzyme in newborns is CYP3A7, which develops in embryonic hepatic
  tissue as early as a GA of 50-60 days, and its activity gradually decreases with age but is
  still present in many individuals until the first year of age"**: **Zhang 2023**, citing refs
  83-85.
- **"In contrast, CYP3A4, one of the fastest-changing enzyme activities in early life, displays a
  mirror image pattern: the activity and expression increase in the first week of age, reaching
  30% of adult levels at one month"**: **Zhang 2023**, citing refs 86-87. Their Table 3 adds:
  CYP3A4 low in fetal livers rising after birth to 30-40% of adult; expression in children 60% of
  adult levels; and CYP3A7 detectable at GA 50-60 days, significant perinatally, then decreasing
  after the first week until undetectable by 1 year.
- **"Owing to differences in substrate specificity and catalytic activity between CYP3A7 and
  CYP3A4, the individual metabolic capacity constantly changes during development and
  maturation"**: **Zhang 2023**. This sentence - that the enzymes differ in specificity, not just
  in amount - is what makes the CYP3A4-ontogeny-only approach a *mechanism* error rather than a
  parameter error.
- **The FMO1/FMO3 parallel**: "Similar to the age-related transition from CYP3A7 to CYP3A4 in the
  liver, the developmental expression of hepatic FMO1 and FMO3 showed the opposite pattern":
  **Zhang 2023**, citing ref 92. Their Table 3: FMO1 high in fetal liver, decreasing with age to
  undetectable in adults; FMO3 low in embryo, undetectable in fetus, detectable by 1-2 years,
  increasing to 18 years.
- **The three-part diagnostic fingerprint** (under-prediction worst in the youngest band; total
  clearance recoverable while metabolite ratios are wrong; an empirical scaling factor that
  applies only to the youngest band and has no physiological name) is a **synthesis step**. It
  follows from the crossover but is not stated by Zhang 2023, and it is the most consequential
  original inference in this skill.

## Pathway substitution - sulfation compensating for glucuronidation

- **"Limited glucuronidated enzymes in neonates can be partly compensated by sulfate
  conjugation"**: **Zhang 2023**, citing refs 97-98.
- **"The expression of SULTs shows significantly different developmental patterns. For example,
  the protein expression of SULT1A1 did not change significantly during various developmental
  periods, whereas that of SULT2A1 increased during the third trimester of gestation and
  continued to increase after birth"**: **Zhang 2023**, citing ref 99.
- **The inference that total clearance can therefore appear reasonable while the pathway split is
  wrong, invalidating any conclusion that depends on the split (DDI prediction, metabolite safety
  argument, genotype extrapolation), is a synthesis step.**

## Enzyme ontogeny quantities

All from **Zhang 2023** section 3.1.3 and Table 3.

- **UGT isoform-specific fold increases from neonate to adult, by LC-MS/MS proteomics: UGT1A1
  ~8-fold, UGT1A4 ~55-fold, UGT1A6 ~35-fold, UGT1A9 ~33-fold, UGT2B7 ~8-fold, UGT2B15 ~3-fold**:
  citing ref 95. The 3-to-55-fold spread is the basis for `SKILL.md` requiring the isoform to be
  named; the "order of magnitude" phrasing is a synthesis inference from that spread.
- **Hepatic UDP-glucuronyltransferase expression approximately 1% of adult levels during GW
  30-40, increasing significantly to adult values by the first few weeks of life**: citing ref 94.
- Different UGT isoforms show different expression and activity patterns with age, all present in
  liver early in gestation: citing ref 12 (Badee et al. on UGT ontogeny).
- **Liver microsomal protein content lower in newborns, gradually increasing, reaching maximum by
  approximately 30 years of age**: citing ref 47 (Allegaert, Mian & van den Anker).
- **"Previous studies found that antipyrine clearance was related to age even after correction for
  liver weight. Therefore, changes in drug clearance in neonates primarily depend on the
  maturation of transporters, intrinsic activity of liver enzymes, and regional blood flow, rather
  than solely on liver size"**: citing refs 77-80. This is the substantive justification for
  keeping this skill separate from the allometric scaling skill, and it is Zhang's own conclusion.
- Liver morphogenesis at 10 weeks GA; smooth endoplasmic reticulum development from the 10th GW;
  hepatocellular hyperplasia and hypertrophy continuing until early adulthood: citing ref 76.
- **CYP2E1** detectable as early as GA 93-186 days; expression in neonates lower than infants
  31-90 days; reaching 30-40% of adult by one year and approaching adult by 10 years; fetal
  (GW 16) about 10-30% of adult: citing refs 88, 114, 115.
- **Aldehyde dehydrogenase content of perinatal infants approximately 10-fold lower than adults**,
  by optimized enzyme immunoassay: citing ref 93.
- **Carboxylesterases - the conflicting evidence.** Pope et al. found no significant difference in
  hepatic CES expression between infants (2-24 months) and adults (20-36 years), with an activity
  ranking that is essentially non-monotonic across age. Yang et al. found mRNA and protein
  expression age-dependent, with adult activity approximately four-fold higher than children and
  ten-fold higher than fetuses. **Zhang 2023** present both without resolving them (refs 90, 91).
  The `SKILL.md` instruction to carry this as structural uncertainty and simulate alternatives is a
  synthesis step, drawing on the structural-uncertainty guidance in the build-workflow skill.
- Non-CYP phase I enzymes named: esterases, FMOs, alcohol and aldehyde dehydrogenases;
  carboxylesterases important for insecticide detoxication (ref 89).
- Phase II enzymes named: glucuronosyltransferases, sulfotransferases, glutathione
  S-transferases, arylamine N-acetyltransferases, methyltransferases.
- **That drug toxicity is more significant in newborns and infants than adults because of immature
  metabolism**: citing ref 82.

### High-extraction drugs

- **"The drugs which are extracted greater than 70% by the liver are defined as high clearance
  drugs. Their intrinsic clearances are greater than liver blood flow. For these drugs, liver
  blood flow rather than enzymatic activity possesses a determinant effect on drug disposition"**:
  **Zhang 2023**, citing ref 100.
- **"Compared to adults, the fraction of hepatic blood flow in cardiac output is higher in children
  (38% vs. 24%)"**: citing ref 101. Plus: neonatal hepatic function slightly improved after birth
  from increased hepatic blood flow related to postnatal circulatory alteration (ref 102).
- The `SKILL.md` instruction to classify by extraction ratio *before* investing in enzyme ontogeny
  is a synthesis step.

## Body composition and protein binding

All **Zhang 2023** section 3.1.2 and Table 2.

- **Total body water: ~90% of body weight in preterm, 80-85% term, 60-65% adult**: refs 61-63.
- **Body fat: ~3% in a premature neonate with deficient birth weight, ~12% in full-term babies**;
  low body fat percentage at birth 10-15%: refs 62-65.
- **First-week weight loss from extracellular water contraction: 5-7% term, 10-15% preterm with
  birth weight <1500 g**: ref 61.
- **"Neonates require larger doses of hydrophilic drugs to achieve similar efficacy because the
  ratio of water to lipids is higher in newborns"**, both factors more significant in preterm,
  "resulting in lower drug plasma concentrations in these different compartments using a body
  weight-based dosing mode": refs 62-65. This is Zhang's own conclusion and it is the source of
  the counter-intuitive dosing point in `SKILL.md`.
- **HSA binds acidic exogenous compounds; AAG has high affinity for basic lipophilic compounds**:
  ref 66 and surrounding. This is the hook to the ionization-and-binding skill, and the basis for
  `SKILL.md` insisting that the fu shift is ionization-class dependent rather than a single factor.
- **Albumin: 2.36 g/dL in preterm (GW 23-34) versus 3.43 g/dL term, sustained for at least the
  first three months; adult mean 4.0 g/dL**; HSA concentration increases until age 20 then
  declines: refs 67, 68, 74.
- **AAG: stays stable at a low level until 260 days of GA then rises significantly; term neonates
  about 50% of adult level**: refs 69, 75.
- **"Physiologically elevated endogenous enzymes, such as bilirubin or fatty acids, competitively
  bind to plasma proteins, resulting in drug displacement and increased unbound drug
  concentration"**: refs 70, 71. Zhang's phrasing calls them enzymes; the substances are bilirubin
  and free fatty acids. This is the neonatal-specific displacement mechanism in `SKILL.md`.
- That only unbound drug distributes and exerts effect: ref 66.
- Diseases such as cardiogenic shock or PDA affecting cardiac output, regional blood flow or tissue
  permeability, thereby increasing volume of distribution and requiring higher doses: refs 72, 73.
- The `SKILL.md` arithmetic check tying predicted neonatal fu to the albumin ratio (2.36/4.0 ≈ 0.59)
  is a synthesis step.

## Renal maturation and glomerulo-tubular imbalance

All **Zhang 2023** section 3.1.4.

- Kidneys smaller in neonates and continuing to grow; **growth mainly from increased tubular mass
  because the number of glomeruli is constant from nephrogenesis to maturation**: ref 139.
- **Preterm kidneys much smaller than term; mean total kidney volume doubles during PMA
  28-37 weeks**: ref 140.
- **Nephrogenesis and vasculogenesis occur in utero and complete at GW 36; tubule maturation and
  growth continue during the first year; nephrogenesis in preterm neonates lasts until 40 days
  after birth**: refs 141, 142, 41.
- Increased renal vascular resistance after birth from the balance of vasoconstrictive and
  vasodilatory forces: ref 144.
- **Renal blood flow low, reaching only 10% of cardiac output by the first week**: ref 64.
- **Renal plasma flow 20 mL/min/1.73 m² premature, 83 term, reaching adult 650 mL/min/1.73 m² by
  2 years**: ref 64.
- **GFR approximately 40% of adult in neonates (41 ± 15 mL/min/1.73 m²), rising to ~60%
  (66 ± 25 mL/min/1.73 m²) during PNA 2-8 weeks, reaching adult 100-125 mL/min/1.73 m² by
  8-12 months**: refs 4, 145, 146.
- **"The GFR in preterm infants is only half of that of newborns compared to full-term infants, as
  nephrogenesis is incomplete until GW 34 and initially rises slowly to reach an average level
  until eight years of age"**: refs 12, 147.
- **"The active tubular secretion is also immature in neonates and is approximately 20-30% of adult
  levels, and then approaches the adult capacity by 7-12 months of age"**: refs 4, 35.
- **"Hence, the GFR matures more rapidly than tubular secretion, leading to glomerulo-tubular
  imbalance in neonates"**: ref 148. **This is Zhang's own named phenomenon**, and the most useful
  discriminating fact in this skill.
- **Tubular reabsorption is the last renal function to develop, steepest rise during 1-3 years,
  continuing until adolescence**: refs 148, 149.
- Tubular secretion and reabsorption depend on renal blood flow and involve active transport, so
  maturational changes in transporters must be considered for extensively renally eliminated
  drugs: ref 35.
- **The inference in `SKILL.md` that a single renal scalar cannot reproduce both filtration-cleared
  and secretion-cleared drugs, and will be biased in opposite directions for the two, is a
  synthesis step** - the strongest arithmetic falsification test in this skill.
- **Biliary:** main elimination route for polar macromolecular compounds (>300 g/mol), oral drugs
  highly bound to hepatic transporters, and hydrophilic drugs extensively plasma-protein-bound
  given parenterally (ref 150). **Intestinal bile acid concentration ~60% of adult at birth,
  ~80% by one year** (ref 151); bile acid pool reduced in premature and full-term infants with
  remarkable expansion at end of pregnancy for term (refs 152, 153); **pool reaching adult levels
  by two years, but approaching adult by seven weeks if corrected for body surface area**
  (ref 154). The observation in `SKILL.md` that the normalisation choice moves the maturational
  conclusion is a synthesis remark on that discrepancy.

## Transporter ontogeny

All **Zhang 2023** section 3.1.5 and Table 4.

- **"Investigating transporter ontogeny is challenging because of the lack of specific transporters
  and the unclear correlation between measured mRNA levels and actual protein expression"**:
  Zhang's own caveat, and the reason `SKILL.md` requires mRNA-only evidence to be flagged.
- General direction - transporter expression gradually increases during organogenesis: refs 50, 72,
  157, 158.
- P-gp, OCT1 and OATP1B3 significantly lower at birth increasing with age; **hepatic MRP2 and
  OATP1B1 delayed and reduced versus adults until the first months of life**: refs 157, 159, 160.
  Note Zhang's Table 4 contains entries that appear internally inconsistent with this summary for
  OCT1 (see below) - the underlying literature is not settled.
- **Counter-examples that defeat a blanket immaturity assumption**, from Table 4: renal **OCT2**
  reported "high in newborns and reduces with age" (ref 157); intestinal **OATP2B1** with "higher
  expression in neonates and young infants than in adults" (refs 157, 50); several renal
  transporters (MATE1, and MRP1 protein abundance) with "no age-dependent changes"; intestinal
  MDR1 stable from neonates to adults (refs 49, 50). Table 4 also carries direct contradictions
  between methods for the same transporter - e.g. MRP2 "stable from neonates to adults" by one
  reference and "lower expression in preterm neonates than adults" by another. `SKILL.md`'s
  instruction to look up the specific transporter rather than apply a blanket rule follows from
  this.
- **Blood-brain barrier MDR1: "Low expression at birth (approximately 30% to 50% of adults),
  increases with postnatal maturation, reaching adult levels at around 3-6 months"** by
  immunohistochemistry: ref 170. Single entry in the BBB section of Table 4, and the quantified
  CNS-exposure point in `SKILL.md`.
- **Intestinal P-gp expression low, especially before the 28th GW** (ref 49), and **intestinal
  CYP3A4 protein expression limited after birth, increasing from neonates to adults, "which may
  result in an increase in bioavailability in newborns"** (refs 46, 50). **This bioavailability
  conclusion is Zhang's own** and is the source of the counter-intuitive point in `SKILL.md`.

## Absorption ontogeny by route

All **Zhang 2023** section 3.1.1 and Table 1.

- **Gastric pH trajectory: drops from approximately 7 to approximately 2 after birth, then rises
  to above 4, and declines back to approximately 2 in two years** (ref 42, Avery, Randolph & Weaver,
  *Pediatrics* 1966); adult approximately 1-2. **Preterm gastric pH relatively higher than term due
  to lower basal acid and gastric secretions** (ref 13).
- **"A relatively high gastric pH in the body may increase absorption of weakly alkaline drugs and
  reduce absorption of weakly acidic drugs"**: **Zhang 2023**. This is the direct link to the
  ionization skill and the reason `SKILL.md` says the direction of the neonatal absorption effect
  is ionization-class dependent.
- Gastric motility low in highly preterm newborns, approaching full-term after 32 weeks gestation;
  gastric emptying slower than older children, maturing rapidly after birth; adults biphasic
  emptying: refs 43, 51.
- **Intestinal transit time approximately four hours in term neonates by an in vitro model, and
  approximately four-fold longer in preterm newborns due to reduced motility and peristalsis**:
  ref 48. Adults approximately four hours.
- **Intestinal pH in neonates/infants: 6.6 ± 0.4 duodenal; 6.6 ± 0.4 jejunum; 6.8 ± 0.7 ileum**,
  with adults slightly lower: refs 52, 53.
- **Reduced intestinal surface-to-volume ratio compared with adults**: ref 54.
- **Intestinal permeability higher in preterm neonates (GW 26-36) than full-term**: ref 55.
- **Intestinal fluid composition: lower bile acid and salt concentration, no secondary bile salts,
  higher total protein and lipid concentrations compared to adults**: ref 57. The framing in
  `SKILL.md` that adult FaSSIF/FeSSIF is not a neonatal medium is a synthesis step linking to the
  build-workflow skill's biorelevant-media requirement.
- Anatomical gut differentiation reaching neonatal levels by GW 20, functional maturation (digestive
  enzyme secretion, tight junction closure) generally after GW 32-34; intestinal lengths increasing
  from fetal to maturation age: refs 44, 45, 46.
- Low bile acid and salt production at birth possibly affecting enterohepatic circulation, though
  passive reuptake and active transport are present: ref 47.
- Digestive enzyme quantities: **enterokinase secretion at GW 24 approximately 25% of older infant
  values** (ref 41); **lactase activity at GW 34 only 30% of term levels** (ref 58); **trypsin
  secretion approximately 90% of childhood levels at term** (ref 59); pepsin expression incomplete
  in neonates; **pancreatic triglyceride lipase lower than adults** (ref 60). Intestinal microbiome
  reduced in diversity with increased pathogenic colonisation in preterm versus term (ref 56).
- **"Specifically, the overall absorption is delayed and incomplete in newborns"**: **Zhang 2023**.
- GI absorption also affected by regional blood flow, "especially in critical ill status such as
  hypoxia"; pancreatic and biliary function immature after birth: **Zhang 2023**.
- Primary sites of oral absorption in newborns are stomach, small intestine and colon: ref 41.
  Oral is the preferred route, liquid dosage forms preferred: **Zhang 2023**.
- **Intramuscular: "the absorption rate of neonates is challenging to estimate because skeletal
  muscle blood flow and muscular contraction are reported to be reduced, and the body water content
  and capillary density in skeletal muscles are reported to be much higher in neonates"**: refs 34,
  35. Vitamin K formulations and vaccines named as the common IM drugs. The `SKILL.md` instruction
  not to assume a direction is Zhang's point restated.
- **Percutaneous: "The extent of transdermal formulation absorption is inversely correlated to the
  thickness of epidermis and directly related to the degree of skin hydration and skin surface area
  to body weight ratio"** (ref 35); **epidermal development GA-dependent, complete at the 34th GW**;
  **full-term neonates have intact skin barrier function while preterm infants lack the vernix
  caseosa and are more sensitive to percutaneous toxicity**; "hence, percutaneous administration of
  drugs must be conservative in the first few weeks of life": ref 36 (Afsar 2010). Handed to the
  dermal absorption skill rather than developed here.
- **Rectal:** good alternative when neonates are unconscious, uncooperative or vomiting; less
  invasive and well accepted; avoids first-pass metabolism; **but high variability of exposure
  limits its use, and "the absorption rate and extent of rectal acetaminophen are lower than oral
  administration in preterm and term neonates"**: ref 37 (Lin, Sussman & Benitz).
- **Inhaled:** most full-term newborns have entered the alveolar development stage after birth,
  highly preterm infants are at the bronchiole and alveolar epithelial development stage, and late
  preterm at the saccular phase, "so the inhalation routine requires caution in premature
  neonates": refs 38, 39.

## Non-maturational / critical-care overlay

All **Zhang 2023** section 3.2, which is the least-quoted and most practically important part of
the review.

- Framing: non-maturational pathophysiological factors such as asphyxia, sepsis and patent ductus
  arteriosus "also serve as clinically relevant variables of the drug disposition in newborns":
  refs 79, 172.
- **Asphyxia** - reduced or altered absorption, increased or unchanged volume of distribution,
  decreased clearance (refs 173, 174). **Ceftazidime: elimination half-life and serum trough
  concentrations significantly increased, clearance and GFR decreased** (ref 175).
  **Gentamicin: clearance significantly reduced versus non-asphyxiated neonates, with a prolonged
  dosing interval necessary to achieve target troughs** (ref 176, a popPK analysis).
- **Sepsis** - altered organ function, haemodynamics, capillary permeability, acid/base disorders,
  endothelial injury and enzyme activity including CYP3A; PK changes comprising increased,
  decreased or unchanged absorption, **increased distribution of hydrophilic drugs, unchanged
  distribution of lipophilic drugs**, and altered clearance in shock (refs 174, 177, 178).
  **Glomerular hyperfiltration of beta-lactams observed in adult sepsis, possibly from altered
  renal blood flow** (refs 179-181) - note the direction is opposite to maturational
  under-filtration. **In neonatal sepsis, amoxicillin distribution significantly increases,
  resulting in lower exposure and a longer half-life** (ref 182).
- **Patent ductus arteriosus** - gentamicin clearance decreased and volume of distribution increased
  in neonates below GW 36 versus those without clinically suspected PDA (ref 183).
- **Acute kidney injury** - "may cause changes in hepatic blood flow and decrease CYP3A activity,
  further affecting drug metabolism in the neonatal liver" (ref 184). The liver-kidney crosstalk
  point.
- **Therapeutic hypothermia** - may reduce absorption and alter distribution (increased, decreased
  or unchanged) and elimination (decreased or unchanged) (ref 174).
- **ECMO** - "may induce changes in drug absorption (decreased or unchanged), distribution
  (increased or unchanged), and elimination (increased, decreased, or unchanged)" (ref 174). The
  fully non-committal direction is Zhang's, quoted rather than paraphrased in `SKILL.md` because it
  accurately represents the state of knowledge.
- **The operational rule that "neonatal model" plus "critically ill patient" is two extrapolations
  and the second is usually undeclared, and that an unrepresented overlay means the prediction is
  for a well neonate, is a synthesis step.**

## Platforms and application evidence

- **Platform distribution across the 56 included articles: 34 Simcyp, 14 PK-Sim, 5 GastroPlus, 2
  MATLAB SimBiology, 1 PhysPK**: **Zhang 2023** section 3.4 and Figure 3. They note increasing
  PK-Sim use because Open Systems Pharmacology is a nonprofit, and explicitly decline to compare
  platforms (refs 2, 235-238 for existing comparisons).
- **20 of the 56 studies established PBPK models providing dose optimisation, dose regimen or dose
  selection for neonates**; PBPK modelling can also simulate virtual bioequivalence trials to aid
  generic evaluation: **Zhang 2023** section 3.5.
- Worked dose-optimisation examples in **Zhang 2023** section 3.5, useful as precedent and as a
  sense of achievable precision - zidovudine requiring GA-adjusted dosing in preterm infants;
  dolutegravir 5 mg every 48 h for three weeks then daily; **gentamicin extended-interval regimens
  (6 mg/kg q36h term, 6 mg/kg q48h preterm from one model; 5 mg/kg q36h in specified PMA/PNA bands
  from a PBPK-PD model, targeting trough <1 µg/mL)**; meropenem with renal transporter ontogeny and
  GFR maturation, 90% of virtual infants achieving target on label dosing but **without disease or
  co-medication effects considered**; ampicillin 50 mg/kg q8h; fluconazole 25 mg/kg loading for CSF
  target, and 30 mg/kg loading for neonates on ECMO; aminophylline 5 mg/kg loading with 2 mg/kg
  maintenance q8h in preterm, with **PNA significantly influencing clearance**; carbamazepine and
  valproic acid dose reductions; lisinopril requiring dose reduction because simulated neonatal and
  infant Cmax and AUC greatly exceeded adult values; propofol clearance sensitive to cardiac output
  with age-dependent effect, recommending 15% dose reduction in neonates/infants/children versus 25%
  in adults under 40-50% reduced CO; a proteomics-informed model supporting the FDA label dose of
  acetaminophen injection in neonates.
- The gentamicin therapeutic context - concentration-dependent bactericidal effect so high Cmax is
  desirable, while **high trough is associated with irreversible ototoxicity and reversible
  nephrotoxicity**, with about 79% renally eliminated by glomerular filtration with tubular
  reabsorption: **Zhang 2023**, refs 187, 242-246. This is why `SKILL.md` names trough as a required
  endpoint where a toxicity-driven trough target exists.
- Model limitations Zhang themselves flag: the nafamostat model's predictive performance limited by
  insufficient human PK data; the meropenem model not considering disease or co-medication; the
  moxifloxacin model not supporting dose finding below 3 months.

## Acknowledged gaps - Zhang 2023 future directions

Quoted here because they define what this skill cannot do.

- Need for more comprehensive understanding of organ ontogeny; **"the less-studied metabolic
  enzymes, transporters, and population-based discrepancies in ontogeny are worthy of attention"**.
- **"The neonatal ontogeny of drug targets also requires continued research, as it contributes to
  possible differences in the exposure-response relation between neonates and older people."**
  This is the basis for the final checklist item in `SKILL.md`: matched exposure does not imply
  matched response in a neonate.
- Need for standardised guidelines and tools for neonatal PBPK development and validation, and for
  international collaboration and consensus.
- Advocacy for **in silico and in vitro platforms such as organ chips** to predict neonatal drug
  disposition, explicitly "given the ethical and logistical challenges associated with conducting
  clinical research on neonates" and to "reduce the need for invasive and risky clinical studies".
  Worth noting as a non-animal-methods argument made on the review's own terms.
- Machine learning and advanced computational methods to enhance predictive capability; data mining
  and meta-analysis of existing neonatal PK data.

## Caveats

- All content synthesised in original wording. Attributions point to where an idea came from;
  consult the primary publication for detail and for anything you intend to cite. Reference numbers
  cited above are Zhang 2023's own numbering - resolve them in that paper's reference list before
  citing downstream.
- Zhang 2023's systematic search closed **7 September 2023**. Neonatal PBPK publication volume is
  growing; the platform distribution and the count of dose-optimisation studies will have moved.
- **Excluded from Zhang 2023 and therefore from this skill's evidence base:** placental and
  lactational transfer modelling, neonatal DDI, popPK modelling as a subject, and animal or
  environmental modelling. A question about maternal-fetal or breast-milk transfer is outside this
  skill.
- Several Table 3 and Table 4 entries in Zhang 2023 are internally inconsistent between measurement
  methods, and the tables are partly garbled by the PDF's two-column layout. Where a specific
  ontogeny value matters to a decision, go to the primary reference rather than trusting the table
  row.
