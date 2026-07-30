# Evidence log - dermal absorption

Provenance for the non-obvious claims in `SKILL.md`.

**Primary source** (present in the source folder as a PDF):

- **Yun YE, Calderon-Nieva D, Hamadeh A, Edginton AN.** Development and Evaluation of an In
  Silico Dermal Absorption Model Relevant for Children. *Pharmaceutics* 2022; 14:172.
  doi:10.3390/pharmaceutics14010172. School of Pharmacy, University of Waterloo. Cited below as
  **Yun 2022**.

**The underlying permeation model is not theirs.** Yun 2022 adapt the mechanistic dermal
absorption model of **Dancik et al.** (their ref 2), which integrates a series of previously
described pharmacokinetic models of skin penetration pathways, was derived from in vitro
permeation data in animal models and adult human skin, and incorporates extensively described
adult human skin structure and physiology. Anyone implementing this should read Dancik et al.
for the full partial differential equation system rather than working from Yun 2022's shorthand.

**Implementation:** the Dancik model has been programmed into **MoBi (Open Systems Pharmacology
v8.21)** and is available on GitHub at `Open-Systems-Pharmacology/Skin-permeation-model`.
Yun 2022 used OSP v9.1 for parameter identification. Its predictive accuracy for volatile
vehicles was separately evaluated by Hamadeh et al. against the in vitro data of Hewitt et al.

Where a claim originates in a paper cited *by* Yun 2022, that downstream reference is named.

---

## Model structure and outputs

- Three stacked compartments - **stratum corneum, viable epidermis, dermis** - with thicknesses
  h_sc, h_ed, h_de; permeant applied via vehicle to the SC surface; diffusion by **Fick's law**;
  partitioning into lipid, protein and aqueous sub-compartments within each layer; aggregate
  layer-specific diffusion coefficients (D_sc, D_ed, D_de) and partition coefficients
  (K_sc/w, K_ed/w, K_de/w) decomposable to phase-specific coefficients and thence to
  **QSPRs** of the permeant's physicochemical properties: **Yun 2022** section 2.1 and their
  Figure 1, after Dancik et al.
- Outputs **y_J (flux, µg/cm²/h)** and **y_Q (cumulative accumulation, µm or µg/cm²)** derived
  from Fick's law at the receptor boundary (their equations 5 and 6); model can generate
  longitudinal flux and accumulation estimates in the various skin layers under both in vitro
  and in vivo conditions, and **under in vivo conditions can additionally estimate
  bioavailability**: **Yun 2022** sections 1 and 2.3.1.
- The generalisation to age-dependence - PDE with parameters split into an age-independent set
  **P** and an age-dependent set **P_A(Age)**, with skin layer thicknesses, permeant diffusivities
  and partitioning coefficients expressible as lump parameters that are functions of both sets:
  **Yun 2022** equations 1-4.
- **The observation in `SKILL.md` that the analogy to whole-body PBPK parameter discipline is
  exact - system parameters change with age, drug parameters do not - is a synthesis step**,
  though Yun 2022 themselves note they followed the established paediatric PBPK workflow
  deliberately (see below).

## Sensitivity - the reason the SC dominates

- **Local sensitivity analysis on the adult model (Age = 30 years) for all three compounds found
  "sensitivity was highest with respect to the stratum corneum parameters (D_sc, K_sc/w,
  h_sc)"**: **Yun 2022** section 3.3.1 and their Figure 3. This holds across buprenorphine
  (logP 4), diamorphine (logP 1.5) and phenobarbital (logP 1.47) - chemically diverse enough
  that the conclusion generalises reasonably.
- **The three uncertain QSPR quantities driving D_sc and K_sc/w**: log₁₀ k_trans (permeant
  transbilayer permeability, Wang et al. 2006); log₁₀ K_lip/w (SC lipid phase/water partition,
  Anderson & Raykar 1989); log₁₀ PC_pro/w (SC protein phase/water partition, Nitsche et al.
  2006). Nominal values and uncertainty ranges are given in **Yun 2022** Table 5, including
  **K_lip/w nominal = 0.81 log₁₀ K_o/w + log₁₀ 0.43 with uncertainty ±0.434**, and
  **PC_pro/w nominal = 0.27 log₁₀ K_o/w + log₁₀ 5.4 with uncertainty ±0.32**. These three were
  taken as the set **P** of uncertain age-independent parameters.
- **The post hoc age-differential sensitivity analysis**: adults set to 30 years, children to
  study-matched ages; a parameter was deemed age-sensitive if
  |sensitivity coefficient in children − sensitivity coefficient in adults| ≥ **15%**
  (their equation 11). **Result: "The flux prediction showed age-dependent sensitivity to the SC
  thickness and a permeability-related parameter (i.e., log₁₀ k_trans), such that the absolute
  sensitivity coefficients of these parameters were higher in neonates than those seen in
  adults."** **Yun 2022** sections 2.5 and 3.4.
- **The inference in `SKILL.md` that this means the newborn prediction is most sensitive to the
  least well known parameter in the population with least data is a synthesis step**, though it
  follows directly.

## Build order and maturation function fitting

- **The workflow steps S1-S3** - update with compound parameters at adult skin age; fit the
  uncertain age-independent parameters **P** to observed adult flux *and* receptor fluid
  accumulation by **Monte Carlo parameter identification in MoBi initiated from 10 randomly
  selected initial values**; then simulate infant skin using the *same* optimised **P** with
  **100 simulations per infant skin age**, each with a fresh sample from p(P_A | Age), comparing
  mean and 95% CI against observed flux: **Yun 2022** section 2.4.2.
- **That compound-specific parameters "were kept the same in both adult and children models"
  while age-dependent components were parameterised as functions of age**: **Yun 2022**
  discussion. They state explicitly that they followed the established paediatric PBPK workflow
  - adult model optimised on adult observations first, then age-dependent components scaled -
  citing their refs 85, 86.
- **The requirement in `SKILL.md` to fit against both flux and cumulative accumulation rather
  than flux alone, because flux alone leaves the diffusion/partition trade-off
  under-determined, is a synthesis step.** Yun 2022 fit both; they do not give this reason.
- **Candidate maturation forms - sigmoid, Hill, polynomial** (their equations 7-9), each
  expressed as a ratio P_Ai(Age)/P_adult, fitted with the **nlstools** R package (R 3.6.1,
  nlstools 1.0-2), with **leave-one-out cross-validation** used to evaluate test error and
  **"the functional form with the lowest LOOCV test error was selected as the final model"**:
  **Yun 2022** section 2.3.2. Final equations and coefficients in their Table 4.
- Where possible, P_Ai(Age) and P_adult were sourced from the **same study**; where the adult
  level was not reported in the same study, the mean of all collected adult values was used as
  reference. Geometric mean of mean SC/ED/DE thickness values used as the adult reference:
  **Yun 2022** section 2.3.2. This is a sensible and rarely stated methodological detail.
- **Adult in vitro SC thickness distribution**: fully hydrated nominal **43 µm** (Nitsche et al.
  2006), with the coefficient of variation (**0.43**) estimated from literature measurements of
  *partially hydrated* SC at various anatomical sites assumed log-normal, giving
  **Lognormal(µ = 3.68, σ² = 0.17)** for the fully hydrated adult h_sc: **Yun 2022** section
  3.3.1. Age-dependent samples were obtained by sampling this adult distribution and scaling by
  the maturation model.
- **That h_sc "varies with the degree of SC hydration"** and that in vitro diffusion experiments
  assume a fully hydrated SC: **Yun 2022** section 3.3.1. **The warning in `SKILL.md` against
  applying age-dependent thickness and hydration ratios as independent multipliers is a
  synthesis inference from this coupling.**

## Skin parameter maturation - the quantities

All from **Yun 2022** section 3.1, a comprehensive literature review searched on MEDLINE,
EMBASE and PubMed for quantifiable data in **healthy full-term infants and children up to 18
years**, restricted to English-language human studies. Search strategies and extracted data are
in their Supplementary Tables S1-S14.

### Stratum corneum thickness

- 43 relevant articles identified; 17 with child-versus-adult epidermis thickness data; **only
  eight specifically measured SC thickness in children versus adults**. Most common sites:
  forearms, upper arm, abdomen; also buttocks and thighs. Methods: ex vivo histology and in vivo
  confocal microscopy.
- **The measurement-modality discrepancy, which is load-bearing in `SKILL.md`: "Earlier studies
  measuring SC thickness using histological methods did not identify a significant distinct
  difference in the thickness of the SC between infants and adults. This was unlike in vivo SC
  measurements using confocal microscopy and confocal Raman spectroscopy, which revealed that
  infant SC is thinner than adult SC."** **Yun 2022**. Their introduction adds that the age
  differences "were not previously well-captured in light microscopy and chemically fixed skin
  samples in infants", i.e. they regard the in vivo methods as the more reliable - but the fact
  remains that the entire age effect on the dominant parameter rests on one class of technique.
- **Stamatas et al.**: 20 infants 3 months-2 years, lower thigh SC on average **30% thinner**
  than adults (**7.3 ± 1.1 µm vs 10.5 ± 2.1 µm**).
- **Liu et al.**: 52 infants and children of the same range, **34% thinner** lower thigh; and at
  the upper inner arm only **18% thinner** (**5.3 ± 1.4 vs 7.9 ± 1.8 µm**). **The site-dependence
  of the age effect within a single study is the cleanest evidence for the `SKILL.md` point that
  site must be named.**
- **Walters et al.**: SC thickness of upper inner arm and dorsal forearm increased from **8 µm at
  3 months to 14 µm at 4 years**, at which point similar to adult (25-40 years, average 32)
  values of **13-14 µm**. Dorsal forearm and inner arm similar to each other in that study.
- **"Overall, SC thickness approaches adult values at around 4 years of age"**: **Yun 2022**,
  their Figure 2A.
- **Only one retrieved study measured SC thickness in neonates and infants under 3 months**
  (**Miyauchi et al.**), pooling measurements at 4-7 days and comparing with 1, 3 and 6 months in
  the same infants at buttock, thigh and forearm. **"Unlike the previous studies, Miyauchi et al.
  found that the SC was thicker at 4-7 days of age compared to 3 months of age at all three
  measured sites."**
- **The documented exclusion:** SC Maturation Model 1 was built including Miyauchi data, but
  "these data report SC thickness values in neonates that exceed values reported in adults... in
  contradiction to previous literature findings". **SC Maturation Model 2, built on a dataset
  excluding Miyauchi, "was chosen as the final model"**: **Yun 2022** section 3.2 and Table 4.
  Their Table 4 also shows Holbrook 1982 **preterm** data were excluded from SC Maturation
  Model 2. Both exclusions are transparently reported, which is why `SKILL.md` treats them as
  documented limitations to carry rather than as errors.
- SC geometry is critical for steady-state permeation, lag time and intercellular flux:
  **Yun 2022**, their ref 34.

### Viable epidermis thickness

- Nine publications identified. Note the SC-versus-epidermis definitional hazard **Yun 2022**
  flag: the epidermis comprises the SC *plus* the viable epidermis, which contains nucleated
  keratinocytes, melanocytes, Langerhans cells and Merkel cells; some studies of "epidermis
  thickness" in children include the SC and label it supra-papillary epidermis.
- **Evans & Rutter (1986)**: abdominal viable epidermis thickness of post-mortem infant skin
  **increased linearly with postnatal age up to 16 weeks**; the **undulating nature of the
  epidermis develops after birth and becomes more distinct with age**.
- **de Viragh et al.**: scalp biopsies; **maximum** epidermis thickness (viable epidermis start
  to most prominent collagen fibre projection) **increased with age, while minimal epidermis
  thickness did not vary** - consistent with increasing undulation.
- **Stamatas et al.** and **Liu et al.**: pooled thigh epidermis thickness in infants 3-24 months
  **20%** and **8%** lower than adult respectively; Liu also measured **22% thinner inner arm**
  epidermis.
- **Miyauchi et al.**: epidermal thickness measured from 4 days to 6 months, two values recorded
  given undulation (top of dermal papillae, bottom of rete ridges); **thickness increased with
  age until one month, reaching 25 µm minimal and 58 µm maximal**, at which point maximal was
  **similar to adult maximal epidermis thickness (60 µm)**.
- **Mogensen et al.**: viable epidermis thinner in children than adults, but no data for the
  forearm.
- Maturation ratio plotted in **Yun 2022** Figure 2B; final Hill-form equation in their Table 4.

### Dermis thickness

- Four relevant articles, one grey literature (not extracted). Dermis is the **thickest layer**,
  contributes substantial in vitro experimental variability (their ref 46), and **"is important
  when predicting systemic drug delivery through the transdermal route"** (ref 47) - the basis
  for `SKILL.md` separating dermis-driven systemic error from SC-driven flux error.
- Structural complication: the dermis also undulates (dermal papillae) and comprises papillary
  and reticular layers, so **thickness was measured differently in each publication**.
- **de Viragh et al.** (scalp): maximal dermis **1125 µm at 2 weeks → 1500 µm at 21 years**;
  minimal dermis **850 µm at 2 weeks → 2200 µm at 21 years**. Average of min and max used.
- **Marcos et al.**: **1603.88 µm at birth → 3236.18 µm at 50 years**, from 5-month-old infants
  to 95 years.
- **Hughes et al.** (forearm, 1 week to 3 years): **highest at 1 week (1200 µm), decreasing to
  1100 µm at 4 weeks, then similar 1050 µm from 6 to 36 months** - i.e. *decreasing*, opposite in
  direction to the others. **Yun 2022** note the dermis bounds measured were not clear in this
  study. The conflict is real and unresolved.
- **"The dermis thickness in children does not change and remains at around 40% of adult
  thickness until around 2 years of age (730 days postnatal), where the dermis thickness
  increases rapidly into adulthood and continues to increase until 27 years of age"**:
  **Yun 2022**, Figure 2C. Final sigmoid-form equation in Table 4, valid to 9883 days.

### Skin hydration - the non-monotonic parameter

- 16 publications measured surface skin hydration indirectly via **skin capacitance**
  (corneometer), capacitance being related to dielectric properties and proportional to water
  content.
- **Mechanism: "An increase in water content results in an increase in skin permeability, since
  the stratum corneum can act as a reservoir to promote percutaneous absorption"**: **Yun 2022**,
  refs 8, 51. This is why the hydration trajectory matters for flux.
- **Newborns in the first 2 weeks have LOWER skin hydration than adults** (nine references).
  **Chittock et al.**: infants <72 h old **17.66 ± 4.55 RCU** versus adults **31.47 ± 6.9 RCU**.
  **Yosipovitch et al.**: hydration begins significantly increasing **in the first 24 hours** of
  life. **Bartels et al.**: largest increase 7 AU in the abdomen from 2-7 days.
- **Visscher et al.**: hydration increases until 2 weeks then plateaus; **several other studies
  found the rapid increase continues to approximately one month**.
- **"At the 1 month mark, the skin capacitance in the infant is higher than adults."**
  **Fluhr et al. (2012)**: hydration **remains above adult values until 6 months (41.5 AU)** and
  **decreases to adult values (30 AU) in the first 1-2 years of life**. By 6 months to 1 year,
  several investigators found no significant difference from adults.
- **Yun 2022** Figure 2D; final polynomial-form equation in Table 4, valid to 1182 days.
- **The framing in `SKILL.md` that the hydration contribution to flux therefore reverses sign
  across the first month, and that a monotonic maturation function is wrong in both directions
  at different ages, is a synthesis step** - and the reason it is worth stating is that the
  fitted polynomial in Table 4 does capture the non-monotonicity, so an implementer who
  substitutes a "simpler" sigmoid would silently break it.

### Held equal to adult

- **Corneocyte volume fraction.** The corneocyte phase enters partition coefficient and SC
  saturation concentration calculations. Four relevant articles. Changes in cell density,
  cluster formation, shape, thickness and adhesion exist **until 5 years of age, with the most
  drastic changes in the first two years**; corneocytes in infants **6-24 months are smaller
  than adult**, attributed to **higher proliferation rate**, becoming **larger and flatter with a
  greater surface area** as proliferation declines. **"Since the relationship between these data
  and the effect on the volume of corneocytes in the SC are unknown, the corneocyte phase volume
  fraction in children was kept the same as in adults in the model."** **Yun 2022** section
  3.1.5. Explicitly an unresolved gap, not a finding of no difference.
- **Lipid and protein content.** Of nine relevant articles, **only two conference abstracts from
  the same research group** measured whole lipid content in infant versus adult skin.
  **Stamatas et al.** measured volar forearm SC in infants 3-24 months and their respective
  mothers, finding **similar amounts of urea, lipids (cholesterol and ceramides) and keratin
  (protein)**. Consequently the SC protein phase volume fraction, protein-to-dry-weight mass and
  lipid-to-dry-weight mass were held equal between adults and children. **Yun 2022** section
  3.1.6. The thinness of this evidence base is noted in `SKILL.md` as a synthesis caution.
- **Skin albumin.** Albumin content affects protein binding in skin and therefore bound and
  unbound dermal concentrations. **Only one article quantified albumin in newborn skin.**
  **"Albumin concentration in premature infants is greater than adults and full-term
  newborns. However, the albumin content in adults and newborn skin were similar, both within
  2.5-5 ng/µg of protein."** The fraction of aqueous phase accessible to albumin was therefore
  held constant. **Yun 2022** section 3.1.7. **The `SKILL.md` point that this constancy is
  therefore *not* justified for preterm skin is a synthesis inference from Yun 2022's own
  sentence.**

## The missing mechanisms - the load-bearing gap

All three are reviewed by **Yun 2022** *for future model development* and are explicitly stated
to be absent from the Dancik model.

- **Skin surface pH.** "Although surface skin pH is not an input parameter in the Dancik et al.
  model, the pH of newborn skin is near neutral, unlike in adults." **Infant skin pH immediately
  after birth is higher and less acidic, around 6.5**, versus adult skin **pH 4-6**; it
  **decreases within 7-14 days and can normalize by 6 months**, appearing similar to adults in
  later infancy. Skin pH is "a crucial element of skin barrier function as it affects enzymatic
  activity in the skin and lipid processing". **Yun 2022** section 3.1.9. **The connection drawn
  in `SKILL.md` to Henderson-Hasselbalch and the un-ionised fraction of an ionisable permeant is
  a synthesis step** linking to the ionization and binding skill; Yun 2022 do not make it.
- **Transfollicular shunt route.** "Similarly, the transfollicular shunt route is another
  parameter not currently included in the Dancik model. Given the importance of the
  transfollicular shunt route of drug permeation through the skin..." **Marchini et al.** found
  the number of visible hair structures per mm² in infants **1-2 days of age was approximately
  ten times greater than in adults**; a grey literature source suggested a relationship between
  follicle dimensions and age. **Yun 2022** section 3.1.9. The inference that this makes the
  model structurally blind to the dominant pathway for follicle-permeating compounds *in the
  population of concern* is a synthesis step.
- **Skin blood flow.** "Skin blood flow is an important limiting parameter that helps to predict
  systemic drug clearance from the skin in vivo... **Skin blood flow is not included in the
  Dancik et al. model**; however, the maturation of skin blood flow data will be useful in the in
  vivo prediction of dermal absorption." Capillary clearance can be predicted from capillary
  surface area and blood-flow-limited clearance; dermal capillary clearance modelling was
  reviewed by **Kretsos & Kasting (2004)**, who also proposed a microscopic model based on
  physiologic capillary structure. **In infants the microvascular structure is disorganised after
  birth and matures over the first 4-5 weeks, when papillary loops are seen as in adult skin**;
  **Miyauchi et al.** observed capillary loop formation at 1-3 months; **Pöschl et al.** found
  skin blood flow changes in the first week of life, with full-term **blood flow oscillations
  reaching the lower range of adult values within 4-5 days**. Yun 2022 state the relationship
  between blood flow changes and microvessel structural change "are not known and need to be
  further studied". **Yun 2022** section 3.1.8.
- **The synthesis step in `SKILL.md` is the combination:** because two of the three missing
  mechanisms (follicular shunt, blood flow) sit on the flux-to-systemic path, a model validated
  on in vitro flux can be structurally incapable of a systemic claim while appearing validated.
  Yun 2022 report each gap separately; they do not draw this conclusion.

## Evaluation - and its limits

- **Three compounds: buprenorphine, diamorphine, phenobarbital**, "selected based on the
  availability of experimental in vitro skin penetration data in adults and infants **within the
  same study**" (Barret et al. 1994, Barret et al. 1993, Bonina et al. 1993). That selection
  criterion is methodologically right and is also why n is small.
- Compound properties in **Yun 2022** Table 1: buprenorphine MW 467.6, logP 4; diamorphine
  MW 369.4, logP 1.5; phenobarbital MW 232.2, logP 1.47. Adult experimental conditions in their
  Table 2 (0.1 M acetate buffer pH 4 for the first two, ethanol for phenobarbital; 72 h, 72 h and
  12 h durations).
- **fold error = predicted flux / observed flux** (their equation 10).
- **Results** (**Yun 2022** Table 7 and section 3.3.2):
  - **diamorphine fold errors 0.55-1.4**; **phenobarbital 0.96-1.26**;
  - **preterm (GA 35-36 weeks) fold errors 1.2 (diamorphine) and 0.93 (phenobarbital)** -
    described as "in good agreement";
  - **buprenorphine failed**: fold errors **12.8** at 38 weeks GA / 1 day PNA and **0.35** at
    40 weeks / 7 hours. The model "could not describe the inter-individual variability observed
    in full-term neonates".
- **The buprenorphine diagnosis is Yun 2022's own and is quoted rather than softened in
  `SKILL.md`:** "The 30-fold difference in flux values between these two skin samples could not
  be captured by the model and this discrepancy is thought to be due to an experimental error.
  The improbable values could have been due to the handling of the skin samples before the
  experiment, such as the freezing, thawing and treating the samples with water for rehydration,
  causing the neonatal skin to become damaged." The model *did* predict reasonably for the
  37-week neonate (1-1.37-fold).
- **The direction was reproduced**: "the pediatric dermal models described the general observed
  trends of higher dermal absorption (i.e., higher flux) in younger infants"; and **"Pre-term
  infants with a lower gestational age exhibited higher absorption rates compared to full-term
  neonates. These trends were captured by the developed dermal absorption model accounting only
  for SC thickness being the difference between pre-term and term neonates. This speaks to the
  importance of SC thickness in driving flux rate in the model."**
- **Preterm caveats, all Yun 2022's own:** preterm defined as GA <37 weeks, full-term ≥37 weeks;
  **"The observed data for full-term and late preterm neonates with a gestational age from 35 to
  40 weeks were included in this study. The review of skin anatomy and physiology did not focus
  on pre-term neonates, and this evaluation was for preliminary assessment only."** Combined with
  preterm-versus-term being captured through SC thickness alone, this is the basis for
  `SKILL.md` requiring preterm predictions below the validated range to be labelled provisional.
- **The authors' own summary of their evidence strength, quoted in `SKILL.md`:** "Clearly, these
  results provide only a small amount of evidence that the anatomy and physiology changes in the
  model are correct. This study as a whole is limited by the amount of in vitro skin penetration
  data available for this age group. However, the limited data that were found could generally be
  recapitulated by the model, although further experimentation would strengthen the basis to say
  that the model is predictive of age-related changes in dermal absorption."

## Regulatory and product framing

- **21 CFR 320.23** as quoted by **Yun 2022**: "For drug products that are not intended to be
  absorbed into the bloodstream, bioavailability may be assessed by measurements intended to
  reflect the rate and extent to which the active ingredient or moiety becomes available at the
  site of action."
- **IVPT** as "an important tool for evaluating the permeation amount and the rate of active
  compounds with the use of excised human skin", and **"required to characterize the rate and
  extent of drug delivery via transdermal or topical routes to demonstrate bioequivalence"**:
  **Yun 2022**, refs 94, 95.
- **Transdermal delivery in neonates:** "advantageous because it can replace an invasive
  procedure of an intravenous line or an oral administration"; "the smaller dose requirements and
  high permeability in neonates makes transdermal drug delivery more plausible"; already used in
  children, with **fentanyl, tulobuterol and lidocaine:prilocaine (EMLA)** named: **Yun 2022**
  discussion, refs 88, 89.
- Determinants of topical bioavailability listed by **Yun 2022**: physicochemical properties of
  drug and vehicle, temperature, skin anatomy and physiology, skin hydration, and **metabolism in
  dermis and epidermis** - the last of these also not represented in the layer-diffusion model.

## Non-animal-methods framing and risk assessment motivation

Quoted because it is the paper's own rationale, and because it is unusually explicit.

- **"Given the limited availability of human and animal skin samples for permeability experiments
  and the differences between human and animal skin, in silico models can be used in lieu of
  experimental studies to estimate dermal exposure to chemicals and drugs and to predict systemic
  exposure under various dosing conditions and exposure scenarios. This is especially valuable in
  pediatric patients where skin samples for in vitro studies are even more limited."**
  **Yun 2022**, introduction. This is the basis for the `SKILL.md` framing that the comparator is
  often no study rather than a better study - a synthesis step, but a short one.
- Risk assessment drivers: cases of chemical poisoning through skin exposure reported in patients
  under 17; **1,4-dioxane and formaldehyde in children's bath products, classified as carcinogens
  by the US EPA**; **the French agency ANSES reporting identification of 60 hazardous chemicals
  in infant disposable diapers**; and **higher skin surface area to body weight ratio plus
  prematurity of skin in neonates** as the reason exposure matters more in children:
  **Yun 2022**, introduction, refs 16-19.
- That a full-term infant's skin is **histologically similar to adult skin**, with a well-defined
  SC and other epidermal layers, yet **in vivo confocal studies over the last 20 years have shown
  anatomical and physiological differences do exist as a function of postnatal age** - and that
  therefore **"in silico predictions generated by dermal models tailored to adult skin may fail
  to correctly predict exposure in the pediatric population"**: **Yun 2022**, introduction. This
  is the paper's thesis and the reason this skill exists.

## Synthesis steps - claims in SKILL.md attributable to no source

Flagged so they can be challenged directly.

1. **The flux ∝ D·K·ΔC/h direction check**, and its use as a sign test on the maturation function.
   Standard Fick's-law algebra; Yun 2022 report the observed direction but do not offer it as a
   falsification test.
2. **The y_Q = ∫y_J consistency check.** Follows from their equations 5 and 6.
3. **"Site variation is larger than the age signal."** Computed here by comparing the ~6-21 µm
   adult site range in their Table 6 against the 18-34% age effect in their section 3.1.1.
   Yun 2022 report both but do not compare them.
4. **The SA:BW multiplier acting on top of the permeability change** to raise systemic dose per
   kg. Yun 2022 cite the higher SA:BW ratio as a risk-assessment motivation but do not build it
   into the flux-to-systemic conversion.
5. **The local-versus-systemic endpoint rule**, and the requirement to state that capillary
   clearance is unrepresented. Follows from their section 3.1.8.
6. **The differing acceptance bars for topical bioequivalence versus margin-of-safety
   calculations.** Editorial, connecting to the acceptance-criteria negotiation in
   `pbpk-model-build-workflow`.
7. **Cross-references** to the neonatal, paediatric, ionization and build-workflow skills.

## Caveats

- All content synthesised in original wording. Attributions point to where an idea came from;
  consult the primary publication for anything you intend to cite. Reference numbers above are
  Yun 2022's own numbering - resolve them in that paper's reference list before citing downstream.
- **The mechanistic model is Dancik et al.'s, not Yun 2022's.** Read Dancik for the PDE system,
  the phase decomposition and the QSPR derivations before implementing.
- The paediatric maturation functions in Yun 2022 Table 4 have explicit validity ranges in days
  (SC to 1604, epidermis via Hill form, dermis to 9883, hydration to 1182), beyond which the
  ratio is set to 1. Respect those bounds; do not extrapolate the fitted forms past them.
- **Yun 2022's literature review covered healthy full-term infants and children.** Preterm skin
  physiology was not systematically reviewed, and the preterm evaluation was preliminary. This is
  the largest gap in the skill's evidence base and it coincides with the population at highest
  risk of percutaneous toxicity.
- Regulatory text (21 CFR 320.23, IVPT bioequivalence expectations) is quoted from a 2022 paper.
  Verify against current text before relying on it.
