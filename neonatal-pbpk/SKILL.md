---
name: pbpk-neonatal
description: Build, interrogate and defend PBPK models for neonates - preterm and term, the first days to months of life. Covers the gestational versus postnatal versus postmenstrual age question, enzyme and transporter ontogeny including the CYP3A7-to-CYP3A4 switch, body composition and plasma protein maturation, bilirubin displacement, glomerulo-tubular imbalance, absorption ontogeny across oral, IM, rectal and inhaled routes, and the non-maturational critical-care overlays (asphyxia, sepsis, patent ductus arteriosus, AKI, therapeutic hypothermia, ECMO). Tool-agnostic. Use when selecting a neonatal or preterm dose, when a neonatal prediction is wrong, when choosing which age metric a maturation function should key on, when scaling an adult model to a newborn, or when auditing a neonatal model. Boundaries - allometric exponent discipline and dose-band translation across the whole paediatric range belong to the paediatric allometric scaling skill; percutaneous and dermal absorption at any age belongs to the dermal absorption skill; this skill owns the first weeks of life and the preterm-versus-term distinction.
---

# Neonatal PBPK

## When to use this skill

When the patient is a newborn - and especially when the newborn is preterm. Neonates are not
small children and they are not the youngest point on a paediatric curve; they are a
population undergoing several simultaneous, independent, non-linear maturational transitions
on *different clocks*, overlaid on the largest physiological discontinuity a human ever
experiences. A model that treats "age" as one covariate cannot represent them.

Boundaries with the neighbouring skills in this suite, stated so the three do not compete:

- **Allometric exponent discipline, dose-band translation, and scaling across the full 0-18
  range** → the paediatric allometric scaling skill.
- **Percutaneous and dermal absorption at any age**, including neonatal skin maturation → the
  dermal absorption skill. This skill notes only that the route is high-risk in preterm
  infants and hands over.
- **The first days to months of life, preterm versus term, and the maturational plus
  critical-care overlays** → here.

The output is never a mg/kg number. It is a dose, the age metric it is keyed to, the
maturational processes represented and the ones not, and whether a critical-care overlay
applies.

## First principle: there is no such thing as "neonatal age"

Three clocks run at once and they are not interchangeable:

- **GA** - gestational age. Time in utero at birth. Sets what had finished developing before
  birth.
- **PNA** - postnatal age. Time since birth. Drives processes triggered *by* birth - the
  circulatory transition, gastric acidification, the postnatal enzyme surge.
- **PMA** - postmenstrual age = GA + PNA. The composite, and the right clock for processes
  that continue on their in-utero trajectory regardless of when birth interrupted it.

Different pathways key on different clocks, and this is documented rather than theoretical:
CYP2E1 expression correlates with **PNA** rather than GA. Glucuronidation maturation depends on
**PNA and PMA** rather than GA. GFR depends on **weight, GA and PNA together**. Epidermal
barrier development completes at a **GA** milestone (about 34 weeks) irrespective of when
delivery occurs.

The operational consequences are immediate:

1. **A single maturation function keyed to a single "age" is wrong for a drug with more than
   one elimination pathway.** Each pathway needs its own clock, named.
2. **Two neonates with the same PNA can be physiologically unalike, and two with the same PMA
   can differ too.** A 28-week infant at 8 weeks PNA and a 36-week infant at 0 weeks PNA share
   a PMA of 36 weeks, but the first has had 8 weeks of postnatal enzyme induction, postnatal
   circulatory adaptation and extrauterine growth, and the second has had none.
3. **Report all three.** A neonatal PK dataset described only as "neonates" is close to
   uninterpretable, and a model validated against it inherits that ambiguity.

## The build order, and the assumption it rests on

The standard approach is to modify a verified adult model:

1. Build and verify an adult PBPK model against observed adult data. Refine until it recovers.
2. Apply age-dependent system parameters to generate **separate preterm and term neonatal
   models**. Not one paediatric model evaluated at a young age - separate models, because
   preterm and term physiology differ on parameters that are not simple functions of size.
3. Simulate and validate against neonatal clinical data.

This rests on two stated assumptions: that **clearance routes are comparable between adults
and neonates**, and that **model structure is similar**. Both are worth writing down, because
the first one is the named failure mode of this entire skill (below).

One further requirement that is easy to miss and that some tools do not handle: **neonates
grow measurably during the simulation.** A multiple-dosing scenario over days or weeks must
run against physiology that changes with postnatal day. A model that holds body weight,
GFR and enzyme abundance constant across a two-week regimen is simulating a patient who does
not exist, and the error compounds in the direction of over-exposure late in the course.

## The assumption that most often silently breaks a neonatal model

**That the adult model's elimination pathways are the neonate's elimination pathways.** The
scaling workflow assumes comparable clearance routes - and for several important pathways
that assumption is simply false, in a way that is invisible because *the missing pathway is
absent from the adult model by construction*.

The clearest case is CYP3A. **CYP3A7 is the most abundant CYP in the newborn liver.** It is
detectable in embryonic hepatic tissue from around 50-60 days GA, persists at significant
levels through the perinatal period, declines after the first week, and is undetectable in
adults. CYP3A4 runs the mirror image: low in fetal liver, rising in the first week, reaching
roughly 30% of adult levels at one month. The two have **different substrate specificity and
different catalytic activity**, so the neonate is not metabolising a CYP3A substrate more
slowly than an adult - it may be metabolising it by a different enzyme, to different
metabolites, with a different sensitivity to inhibition.

An adult model has no CYP3A7. Scaling it down by applying a CYP3A4 ontogeny function therefore
represents the neonate as an adult with less of the adult enzyme, which is the wrong mechanism
even where it happens to produce an approximately right total clearance. FMO1 and FMO3 show
the same crossover pattern, so this is a class of error rather than a single exception.

The diagnostic fingerprint has three parts:

- neonatal clearance of a CYP3A substrate is under-predicted, and the under-prediction is
  worst in the youngest band;
- total clearance can be recovered while **metabolite ratios are wrong** - which is why
  metabolite data, where they exist, are the highest-value neonatal validation dataset;
- the model needs an empirical scaling factor that applies *only* to the youngest age band and
  has no physiological name.

That last one is the tell. A correction that exists only where the missing pathway dominates
is not a scaling factor, it is the shape of the missing pathway.

**The same trap in a second form: pathway substitution.** Limited glucuronidation capacity in
neonates is partly compensated by **sulfation** - and the sulfotransferases have their own,
different developmental patterns, with SULT1A1 roughly stable across development while SULT2A1
rises through the third trimester and continues after birth. For a drug conjugated by both
routes, total clearance in a neonate can look almost reasonable while the pathway split is
completely different from the adult. Any conclusion that depends on the split - a DDI
prediction, a metabolite safety argument, an extrapolation to a UGT-deficient genotype - is
then wrong while the concentration-time profile looks fine.

## Ontogeny: what is established, what varies by isoform, what is not known

**Do not treat "ontogeny" as one curve.** The isoform-to-isoform variation is larger than the
average trend. From neonate to adult, protein abundance increases have been measured at
roughly 8-fold for UGT1A1, **55-fold for UGT1A4**, 35-fold for UGT1A6, 33-fold for UGT1A9,
8-fold for UGT2B7 and 3-fold for UGT2B15. A model using a generic "UGT maturation" factor is
therefore wrong by up to an order of magnitude depending on which isoform actually clears the
drug. Overall hepatic UDP-glucuronyltransferase expression is around **1% of adult levels at
GW 30-40**, rising steeply in the first weeks - so this is the pathway where getting the
isoform right matters most.

Other anchors worth carrying:

- **Liver microsomal protein content** is lower in newborns and increases with age, reaching a
  maximum around 30 years. This is a system parameter, separate from enzyme abundance, and both
  must be applied.
- **Liver size alone does not explain the change in clearance.** Antipyrine clearance remains
  age-related *after* correction for liver weight. Clearance maturation depends on transporter
  maturation, intrinsic enzyme activity and regional blood flow - which is precisely why
  allometric size scaling alone under-performs in this population, and the substantive reason
  this skill is separate from the allometry skill.
- **Aldehyde dehydrogenase** content in perinatal infants is roughly 10-fold lower than adult.
- **Carboxylesterases: the evidence conflicts.** One study found no significant difference
  between infants (2-24 months) and adults; another found clear age-dependence with
  adults > children > fetuses. Where two methods disagree this sharply, a model should carry
  both as a structural uncertainty and simulate the alternatives rather than pick one.
- **High-extraction drugs are a special case.** For drugs extracted more than about 70% by the
  liver, where intrinsic clearance exceeds liver blood flow, **hepatic blood flow rather than
  enzyme activity determines disposition**. Ontogeny of enzymes is then almost irrelevant and
  the blood flow trajectory is everything - and hepatic blood flow as a fraction of cardiac
  output is *higher* in children than adults (roughly 38% versus 24%), with a further postnatal
  increase in neonates driven by circulatory transition. Classify the drug by extraction ratio
  before spending effort on enzyme ontogeny.
- **Transporter ontogeny is genuinely poorly characterised**, and the review literature says so:
  specific probes are lacking and the correlation between measured mRNA and actual protein is
  unclear. The general direction is increasing expression with organogenesis, but the exceptions
  matter and are not rare - OCT1 has been reported high in newborns and decreasing with age;
  intestinal OATP2B1 higher in neonates and young infants than adults; several renal
  transporters showing no age dependence at all while their neighbours mature substantially.
  **Do not apply a blanket "transporters are immature" assumption.** Look up the specific
  transporter, and where the data are mRNA-only, say so.
- **Blood-brain barrier P-glycoprotein is roughly 30-50% of adult levels at birth**, reaching
  adult levels around 3-6 months. For a CNS-active P-gp substrate this is a large, quantified,
  frequently ignored exposure difference and a genuine safety consideration.

## Body composition and protein binding

The composition changes are large enough to invert dosing intuitions:

| Parameter | Preterm | Term | Adult |
|---|---|---|---|
| Total body water | ~90% BW | 80-85% | 60-65% |
| Body fat | ~3% (very low birth weight) | ~12% | - |
| First-week weight loss | 10-15% (BW <1500 g) | 5-7% | - |
| Serum albumin | 2.36 g/dL (GW 23-34) | 3.43 g/dL | 4.0 g/dL |
| AAG | low until ~260 days GA, then rises | ~50% of adult | - |

Consequences:

- **Hydrophilic drugs need a larger dose per kilogram in neonates**, because the water-to-lipid
  ratio is higher. A body-weight-based regimen carried down from adults produces *lower* plasma
  concentrations, and the effect is stronger in preterm infants. If a model predicts the same
  L/kg volume of distribution for a hydrophilic drug in a neonate as in an adult, the body
  composition was not changed.
- **The first-week weight loss is a modelling problem, not a footnote.** Extracellular water
  contraction produces 5-7% weight loss in term and 10-15% in low-birth-weight preterm infants
  within the first week. A model that holds body weight constant across the first week carries
  that error into the denominator of every per-kilogram quantity in the sickest patients.
- **Which binding protein matters depends on the drug's ionization class.** Albumin binds
  acidic compounds; AAG binds basic lipophilic compounds. So the neonatal increase in unbound
  fraction is *not* a single factor - an acidic drug tracks the albumin deficit, a basic
  lipophilic drug tracks the AAG deficit, and the two proteins have different trajectories
  (AAG stays low until around 260 days GA before rising significantly). Using the wrong protein
  gets the direction approximately right and the magnitude wrong. See the ionization and
  binding skill.
- **Bilirubin and free fatty acids competitively displace drugs from plasma protein**, raising
  unbound concentration beyond what the reduced protein concentration alone predicts. This is a
  neonatal-specific mechanism with no adult analogue at this magnitude, it is clinically
  consequential for highly bound drugs, and it is routinely absent from models.

## Renal maturation, and the imbalance that a single factor cannot represent

The three renal processes mature on different schedules, and that difference is the point.

- **Renal blood flow** is low - roughly 10% of cardiac output in the first week. Effective renal
  plasma flow is about 20 mL/min/1.73 m² in premature and 83 in term infants, reaching the adult
  650 mL/min/1.73 m² by around 2 years.
- **GFR** begins rising immediately after birth: roughly 40% of adult values in neonates
  (about 41 ± 15 mL/min/1.73 m²), about 60% (66 ± 25) by PNA 2-8 weeks, adult levels
  (100-125 mL/min/1.73 m²) by 8-12 months. **Preterm GFR is about half that of term infants**,
  because nephrogenesis is incomplete before GW 34, and rises slowly thereafter.
- **Active tubular secretion** is roughly **20-30% of adult** levels, approaching adult capacity
  by 7-12 months.
- **Tubular reabsorption** is the last function to develop, with its steepest rise at 1-3 years
  and continued increase into adolescence.

**GFR therefore matures faster than tubular secretion. This is glomerulo-tubular imbalance, and
it is the most useful discriminating fact in neonatal renal modelling.** A drug cleared purely
by filtration and a drug cleared substantially by secretion have *different* maturation
trajectories in the same infant. A model applying one "renal function" scalar - however
carefully fitted - cannot reproduce both, and will be biased in opposite directions for the two
drug types.

Anatomical points that constrain the model: **nephron number is fixed** from nephrogenesis, so
postnatal kidney growth is tubular mass rather than new glomeruli; nephrogenesis completes
around GW 36 in utero but **continues to about 40 days after birth in preterm infants**; total
kidney volume roughly doubles across PMA 28-37 weeks. Tubular secretion and reabsorption are
both active and both flow-dependent, so transporter ontogeny and renal blood flow maturation
compound.

Biliary elimination has its own trajectory: intestinal bile acid concentration is around 60% of
adult at birth, roughly 80% by one year, with the bile acid pool reaching adult levels by about
two years - or by about seven weeks when corrected for body surface area, a discrepancy worth
noting because it shows how much the normalisation choice can move a maturational conclusion.

## Absorption ontogeny by route

**Oral** - the preferred route, usually as a liquid. The overall picture is *delayed and
incomplete* absorption, from several independent changes:

- **Gastric pH follows a non-monotonic trajectory**: about 7 at birth, falling to about 2, rising
  above 4, and returning to about 2 by two years. Preterm pH is higher than term because of lower
  basal acid and gastric secretion. A high gastric pH **increases absorption of weak bases and
  reduces absorption of weak acids** - which makes this a direct interaction with the
  ionization skill, and means the direction of the neonatal absorption effect depends on the
  compound's ionization class rather than being a general "reduced absorption".
- **Gastric emptying** is slower than in older children; motility is low in highly preterm
  infants and approaches term behaviour after about 32 weeks gestation.
- **Intestinal transit time** is about 4 hours in term neonates - similar to adults - but roughly
  **four-fold longer in preterm** infants, from reduced motility and peristalsis.
- **Intestinal pH** is slightly higher than adult: around 6.6 in duodenum and jejunum and 6.8 in
  ileum.
- **Reduced intestinal surface-to-volume ratio** compared with adults; **intestinal permeability
  higher in preterm** infants (GW 26-36) than term.
- **Intestinal fluid composition differs**: lower bile acid and salt concentrations, **no
  secondary bile salts**, and higher total protein and lipid. For a poorly soluble lipophilic
  drug this is the neonatal analogue of the biorelevant-media problem in the build-workflow
  skill - adult FaSSIF/FeSSIF is not a neonatal medium, and solubilisation capacity is lower.
- **Both intestinal P-gp and intestinal CYP3A4 are low**, P-gp especially before 28 weeks
  gestation. **Net effect: oral bioavailability can be higher in newborns**, because first-pass
  extraction and efflux are both reduced. This runs opposite to the "everything is immature so
  exposure is lower" intuition and is a common source of over-dosing.
- Digestive enzyme immaturity is substantial and uneven - enterokinase at GW 24 around 25% of
  older infant values, lactase at GW 34 only about 30% of term levels, trypsin around 90% of
  childhood levels at term, pancreatic triglyceride lipase lower than adult. Relevant to
  lipid-based formulations and to prodrugs requiring hydrolysis.
- Regional GI blood flow is affected by critical illness, particularly hypoxia.

**Intramuscular** - genuinely hard to predict, because the determinants move in opposite
directions: skeletal muscle blood flow and muscular contraction are reduced, while body water
content and capillary density in skeletal muscle are higher. Do not assume a direction. Common
in practice for vitamin K and vaccines.

**Rectal** - avoids first pass and is well accepted, and useful when an infant is unconscious,
uncooperative or vomiting. But exposure variability is high enough to limit its use, and rectal
acetaminophen has shown both lower rate *and* lower extent of absorption than oral in preterm
and term neonates. Model it as high-variability, not as a first-pass-free oral route.

**Percutaneous** - absorption is inversely related to epidermal thickness and directly related
to skin hydration and to the skin-surface-area-to-body-weight ratio. Epidermal development is
GA-dependent and completes around the 34th gestational week. Term neonates have intact barrier
function; **preterm infants lack vernix caseosa and are more sensitive to percutaneous
toxicity**, so this route must be treated conservatively in the first weeks. For the mechanistic
treatment, go to the dermal absorption skill.

**Inhaled** - lung developmental stage gates this route. Most full-term newborns have entered
the alveolar stage; highly preterm infants are at the bronchiole and alveolar epithelial
development stage and late preterm at the saccular phase. Caution in premature infants is a
structural point about available surface, not a dosing adjustment.

## The non-maturational overlay: neonatal critical care

Maturation is not the only thing happening. Most neonates who receive modelled drugs are ill,
and the illness changes disposition independently of age. This is where a technically correct
maturational model still mispredicts, and it deserves separate treatment because the
directions are not all knowable in advance.

- **Asphyxia** - reduced or altered absorption, increased or unchanged volume of distribution,
  decreased clearance. Documented: ceftazidime half-life and trough concentrations significantly
  increased with clearance and GFR decreased; gentamicin clearance significantly reduced versus
  non-asphyxiated neonates, requiring a prolonged dosing interval to hit trough targets.
- **Sepsis** - altered organ function, haemodynamics, capillary permeability, acid/base status,
  endothelial injury and CYP3A activity. Absorption may increase, decrease or be unchanged;
  **distribution of hydrophilic drugs increases while lipophilic distribution is unchanged**;
  clearance is altered in shock. Glomerular **hyperfiltration** of beta-lactams has been observed
  in adult sepsis - note the direction, since the maturational assumption would be
  under-filtration. In neonatal sepsis, amoxicillin distribution increases significantly,
  producing lower exposure and a longer half-life.
- **Patent ductus arteriosus** - gentamicin clearance decreased and volume of distribution
  increased in infants below GW 36 versus those without clinically suspected PDA.
- **Acute kidney injury** - changes hepatic blood flow and decreases CYP3A activity, so renal
  injury alters *hepatic* metabolism. Liver-kidney crosstalk, not two independent organs. The
  renal impairment skill develops the general form of this.
- **Therapeutic hypothermia** - may reduce absorption, alter distribution in either direction,
  and decrease or leave unchanged elimination.
- **ECMO** - absorption decreased or unchanged; distribution increased or unchanged; elimination
  increased, decreased **or** unchanged. That is not an evasion, it is the literature: the
  direction is not predictable, and a model applied to an infant on ECMO without ECMO-specific
  data is making an assumption it cannot support. Say so.

Operational rule: **state which overlay applies and whether it is represented.** "Neonatal
model" plus "critically ill patient" is two extrapolations, not one, and the second is usually
undeclared. Where an overlay is present but unrepresented, the prediction is for a well neonate
and should be labelled that way.

## Parameter relationships that must hold internally

Arithmetic checks, no simulation required.

1. **All three ages present and consistent: PMA = GA + PNA.** If a dataset gives only "age",
   the model's maturation functions cannot be verified.
2. **Each maturation function keyed to a named clock**, with a reason. Multiple pathways →
   multiple clocks.
3. **Hydrophilic Vd per kg must be higher in neonates than adults.** Equal L/kg means body
   composition was not applied.
4. **Neonatal unbound fraction consistent with the binding-protein ratio**, and with the *right*
   protein for the compound's ionization class. Preterm albumin 2.36 versus adult 4.0 g/dL is a
   ratio of about 0.59; the fu increase for a highly albumin-bound acid should be broadly
   commensurate. A basic lipophilic drug should track AAG instead.
5. **Filtration-cleared and secretion-cleared drugs must show different maturation
   trajectories.** If one renal scalar drives both, glomerulo-tubular imbalance is not
   represented and the model is biased in opposite directions for the two classes.
6. **fu × GFR versus renal clearance**, using **age-appropriate** GFR - roughly 40% of adult in
   neonates, half of that again in preterm. The build-workflow and renal skills use this test;
   with an adult GFR it will silently mislead here.
7. **UGT-substrate clearance per kg must be dramatically lower than adult**, given roughly 1% of
   adult expression at GW 30-40. Near-adult predicted CL/kg means ontogeny was not applied - or
   sulfation is compensating, which is a different claim requiring metabolite evidence.
8. **The specific UGT isoform must be named.** The neonate-to-adult range across isoforms spans
   roughly 3-fold to 55-fold; a generic factor is an order-of-magnitude assumption.
9. **CYP3A substrates: CYP3A7 either represented or its absence justified.** An
   ontogeny-corrected CYP3A4-only model is a different mechanism, not a scaled one.
10. **Extraction ratio classified before enzyme ontogeny is invoked.** Above roughly 70%
    extraction, hepatic blood flow governs and enzyme maturation is largely irrelevant.
11. **Body weight allowed to change across the first week** - 5-7% loss term, 10-15% preterm.
12. **Oral bioavailability allowed to be higher, not only lower.** Reduced intestinal CYP3A4 and
    P-gp raise F; a model that only reduces absorption has represented half the physiology.
13. **Direction of the gastric-pH effect consistent with ionization class** - weak bases up,
    weak acids down.
14. **Preterm and term modelled separately**, not as two points on one function.
15. **Physiology time-varying across a multiple-dose simulation.**
16. **Critical-care overlay named, and represented or declared absent.**

## Model qualification

Use the Phase A evidence hierarchy and Phase B goodness-of-fit apparatus in
`pbpk-model-build-workflow`, including agreeing acceptance criteria with the modeller against
intended purpose before computing metrics. Formulas and the output schema live there.

Neonatal-specific points:

- **Strata that must be reported separately, never pooled:** preterm versus term; GA band; PNA
  band; PMA band; birth weight band; well versus critically ill, and by overlay type. Aggregate
  neonatal metrics conceal exactly the failures this skill is about - a model can pass overall
  and fail entirely below GW 30.
- **Endpoints:** the usual AUC and Cmax, plus trough concentration where the drug has a
  toxicity-driven trough target, plus **metabolite ratios wherever available** - the single most
  discriminating neonatal endpoint, because it separates "right total clearance" from "right
  pathways".
- **Signed error against GA, PNA and PMA separately.** A trend against one clock and not the
  others tells you which clock the maturation function should have used. This is the highest-value
  diagnostic plot in neonatal PBPK and it is rarely made.
- **n is usually small and should always be stated.** Neonatal datasets are constrained by low
  parental consent rates and by limited blood volume - both real and both irreducible. An AAFE
  from four infants is a number, not an estimate.
- **Population PK simulation can legitimately supply evaluation datasets** for a PBPK model where
  intensive sampling is impossible. Useful and worth doing - but a popPK-derived dataset is not
  independent observation, and its evidence tier should reflect that.

## Verification checklist

1. GA, PNA and PMA all reported for every subject and every simulation; PMA = GA + PNA checked.
2. Preterm and term treated as separate models with separate physiology.
3. Every maturation function keyed to a named age metric, with the reason.
4. Adult model verified against adult data before any scaling.
5. The "comparable clearance routes" assumption examined explicitly for this drug, not inherited.
6. CYP3A7 addressed for CYP3A substrates; FMO1/FMO3 crossover addressed where relevant.
7. Pathway substitution considered - sulfation compensating for immature glucuronidation - and
   tested against metabolite data where available.
8. UGT isoform named; isoform-specific ontogeny used rather than a generic factor.
9. Liver microsomal protein content applied as a system parameter, separately from enzyme
   abundance.
10. Clearance maturation not attributed to liver size alone.
11. Hepatic extraction ratio classified; blood-flow-limited drugs handled as such, with the
    higher hepatic-flow fraction of cardiac output applied.
12. Body composition applied: total body water, body fat, first-week weight loss.
13. Binding protein matched to ionization class - albumin for acids, AAG for basic lipophiles -
    with their different trajectories.
14. Bilirubin and free fatty acid displacement considered for highly bound drugs.
15. Renal model represents filtration, secretion and reabsorption **separately**;
    glomerulo-tubular imbalance reproducible.
16. Age-appropriate GFR used in the fu × GFR check.
17. Preterm renal function set at roughly half of term, with continuing postnatal nephrogenesis
    for preterm infants noted.
18. Absorption ontogeny applied per route; gastric pH trajectory non-monotonic and its effect
    directionally consistent with ionization class.
19. Reduced intestinal CYP3A4 and P-gp allowed to *increase* bioavailability.
20. Neonatal intestinal fluid composition considered for poorly soluble lipophilic drugs rather
    than adult biorelevant media.
21. Transporter assumptions sourced per transporter, not applied as a blanket immaturity; mRNA-only
    evidence flagged as such.
22. BBB P-gp immaturity applied for CNS-active P-gp substrates.
23. Physiology time-varying across multiple-dose simulations.
24. Critical-care overlay named; if present and unrepresented, prediction labelled as
    well-neonate.
25. Conflicting ontogeny evidence (e.g. carboxylesterases) carried as structural uncertainty and
    simulated as alternatives, not silently resolved.
26. Metrics stratified by preterm/term, GA, PNA, PMA, birth weight, and illness overlay; n stated.
27. Signed error plotted against each age clock separately.
28. popPK-derived evaluation datasets identified as such and tiered accordingly.
29. Limitations name every maturational process not represented, every transporter with no
    ontogeny data, and every drug target whose own neonatal ontogeny is unknown - target ontogeny
    can make the exposure-response relationship differ from adults even when exposure is matched.

## How to report so that the reader gains agency

Give the dose and the regimen; the GA, PNA and PMA range it applies to; which maturational
processes are represented and which are not; which age clock each one keys on; whether the
patient population is well or critically ill and whether that is in the model; the metabolite
evidence, or its absence; and the observation that would falsify it - a trough above target in
the youngest band, a metabolite ratio that does not match, a trend of signed error against GA
but not PNA. A neonatologist given those can decide whether to trust the number for the infant
in front of them. Given "5 mg/kg q36h" alone, they cannot.
