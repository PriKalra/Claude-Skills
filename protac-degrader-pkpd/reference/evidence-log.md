# Evidence log - targeted protein degrader PK/PD

Provenance for the non-obvious claims in `SKILL.md`.

**Primary source** (present in the source folder as a PDF):

- **Watt GF, Scott-Stevens P, Gaohua L.** Targeted protein degradation in vivo with
  Proteolysis Targeting Chimeras: Current status and future considerations. *Drug Discovery
  Today: Technologies* 2019. doi:10.1016/j.ddtec.2019.02.005. Authors at GlaxoSmithKline.
  Cited below as **Watt 2019**.

Where a claim originates in a paper cited *by* Watt 2019, the downstream reference is named
so the primary source can be consulted.

---

## Scope note: what is Watt 2019 and what is not

Watt 2019 is a 2019 review of *in vivo* degrader studies and a set of recommendations for
building PK/PD understanding. It is strong on empirical observation and on study design, and
it deliberately stops short of specifying model equations - because, as it states, **no
mathematical PK/PD model describing PROTAC activity in vivo had been reported at the time of
writing**, and neither allometric approaches nor mechanistic PBPK had been applied to the
modality.

Three consequences for reading this skill:

1. The **empirical constraints** - tissue-selective degradation, plasma/tumour divergence,
   dose-versus-potency gap, degradation time course, single-timepoint inadequacy - are all
   Watt 2019 and are the load-bearing content.
2. The **model structure** in `SKILL.md` (the turnover ODE, the steady-state algebra, the
   Dmax ceiling argument, the recovery-versus-protein-half-life diagnostics) is a synthesis
   step. Watt 2019 names the indirect-response-with-stimulated-dissipation structure and its
   source; the explicit equations and the arithmetic falsification tests derived from them
   are written here for the first time and are attributable to no paper.
3. The **hook effect and ternary complex cooperativity** treatment is domain knowledge from
   the wider degrader literature, **not from Watt 2019**, which does not discuss the hook
   effect. See the dedicated section below. It is included because a degrader PD model
   without it will extrapolate wrongly at high dose, but it should not be cited to Watt.

**This field has moved substantially since 2019.** Degrader PK/PD modelling, ternary complex
kinetic models and clinical degrader data have all advanced. Treat this skill's translation
section in particular as a floor rather than as current state, and check the recent
literature before asserting that something "has not been reported".

---

## Modality and mechanism

- PROTACs as bifunctional molecules simultaneously binding a target protein and an E3
  ubiquitin ligase, causing ubiquitination and proteasomal degradation: **Watt 2019**,
  citing Lai & Crews, *Nat Rev Drug Discov* 2017; 16:101-114; Raina & Crews, *Curr Opin Chem
  Biol* 2017; 39:46-53; Neklesa, Winkler & Crews, *Pharmacol Ther* 2017; 174:138-144.
- The three routinely employed E3 ligases - VHL, CRBN (CUL4^CRBN) and IAP - plus MDM2 as a
  more recent addition: **Watt 2019**, citing Tinworth et al. 2016 for the ligase biology and
  Hines et al., *Cancer Res* 2018 for the MDM2-recruiting BRD4 degrader.
- **"Event-driven rather than occupancy-driven" pharmacology** is Watt 2019's own framing,
  citing Lai & Crews 2017. This phrase is the origin of the first principle in `SKILL.md`.
- Precedent monovalent degraders - fulvestrant (Faslodex) as a SERD accelerating ERα
  turnover, and the IMiD class (lenalidomide, pomalidomide) altering CRBN substrate
  specificity to degrade neosubstrates: **Watt 2019**, citing Bartlett et al. 2004, Ito &
  Handa 2016, Patel & Bihani 2018.
- That degraders require only a binder rather than a functional inhibitor, opening access to
  otherwise undruggable targets, and that the resulting biological profile can be
  differentiated from pharmacological inhibition: **Watt 2019**, with the PCAF/GCN5 and FAK
  examples (Bassi et al. 2018; Cromm et al. 2018) where degradation recapitulated a genetic
  phenotype that inhibition did not. This is the mechanistic basis for the warning in
  `SKILL.md` that kinase biology extends beyond catalytic function (Kung & Jura 2016; Knight
  & Shokat 2007).

## Protein resynthesis as the rate-limiting step

- **"Protein re-synthesis rate is a key factor impacting PK/PD relationships"** is stated
  directly in **Watt 2019**. The elevation of this to the first principle of the skill - that
  duration of pharmacology is set by protein turnover rather than drug half-life - is a
  synthesis step, but a short one.
- That protein levels have been shown to *partially* recover in vivo while **protein
  half-life has not been determined**, and that analysing compounds with differing PK
  profiles or characterising recovery after elimination would be valuable for estimating it:
  **Watt 2019**, citing Qin et al. (QCA570), *J Med Chem* 2018; 61:6685-6704 and Zhou et al.
  (BET degrader 23 / BETd-260), *J Med Chem* 2018; 61:462-481 for the partial recovery
  observations.
- That whether protein half-life is consistent across tissues, and whether variation in E3
  ligase concentration and proteasome activity influences degradation, are **explicitly open
  questions**: **Watt 2019**. The `SKILL.md` requirement to allow k_deg to vary by tissue
  follows from this being open rather than from it being answered.
- **The steady-state algebra (P₀ = k_syn/k_deg; fractional remaining =
  k_deg/(k_deg + k_deg,induced); therefore 100% degradation is impossible while synthesis
  continues) is a synthesis step and appears in no source.** It follows from the ODE
  structure and is included because reported "complete degradation" is common and is always
  either a detection limit or an error.

## The degradation time course

- Rapid degradation within 1 h, sustained to the 6 h timepoint, with some recovery of protein
  levels by 24 h post-dose, and a dose-dependent depth such that lower doses produced less
  profound degradation: **Watt 2019**, from QCA570 (Qin et al. 2018) and BET degrader 23
  (Zhou et al. 2018).
- That the subsequent efficacious three-times-per-week regimen suggests **complete
  degradation may not be required for efficacy**, with the caveat that further multiple-dose
  time-course data would be needed to confirm it: **Watt 2019**. Both the inference and the
  caveat are theirs.
- MDM2 degrader MD-224 showing time-dependent reduction with 80% loss at 24 h, accompanied by
  time-dependent p53 and p21 accumulation and PARP cleavage: **Watt 2019**, citing Li et al.,
  *J Med Chem* 2018. This is the cleanest published example of protein loss tracked to a
  downstream functional consequence.
- That PD assessment of protein levels is **often determined at only a single timepoint**, and
  that routine assessment of exposure in both plasma and relevant effect compartments is
  limited: **Watt 2019**. This is their criticism of the field, reproduced in `SKILL.md` as a
  study-design requirement.

## Tissue-selective degradation - the load-bearing empirical result

- **BTK degradation observed in rat spleen but not in lung tissue despite comparable tissue
  compound concentrations (2000-3000 ng/mL)** is **Watt 2019**, reporting Zorba et al., *Proc
  Natl Acad Sci USA* 2018; 115:E7285-E7292. Watt 2019 state that the reason is unknown and
  that the original authors speculate on explanations related to the E3 ligase axis and the
  ubiquitination process. Watt 2019 explicitly call this out as highlighting "the importance
  of understanding the factors which control protein degradation in vivo".
- The dose-dependence in the same study - 33% and 71% spleen BTK degradation following one or
  two 175 mg/kg doses respectively, with only a modest increase in plasma concentration on the
  second dose, suggesting later timepoints might yield more degradation - is **Watt 2019** /
  Zorba et al. Note the n of two animals per timepoint, which Watt 2019 flag.
- The inference in `SKILL.md` that this makes degradation capacity a *system* parameter
  requiring explicit representation, and that a model varying only drug concentration by
  tissue will confidently predict knockdown where none occurs, is a synthesis step. It is the
  most consequential such step in this skill.
- Tissue-restricted pharmacology as a stated ambition for the modality: **Watt 2019**
  (introduction and summary) - "understanding the drug- and system-dependent parameters for
  PROTACs will be critical for achieving tissue/cell specific pharmacology". Their own
  drug-versus-system framing aligns with the parameter discipline in the build-workflow skill.

## Plasma is not the site of action

- **QCA570 detected at appreciable levels in the tumour but not in the blood compartment at
  6 h post-administration**, which Watt 2019 present as highlighting "the importance of
  determining compound concentration-time profiles at the site of action": **Watt 2019**,
  from Qin et al. 2018. This is the diagnostic fingerprint quoted in `SKILL.md`.
- That blood/plasma concentrations are "often used as a surrogate of tissue (effect
  compartment) concentrations" while it is important to determine exposure in the relevant
  tissue compartment: **Watt 2019**.
- That distribution and metabolism of degraders within tissue are expected to be limited by
  **permeability across cell membranes**, by virtue of high molecular weight and complex
  structure, so that the time course of intracellular concentration driving
  binding-ubiquitination-degradation "may be significantly different from concentrations in
  the circulatory blood": **Watt 2019**.
- That degrader tissue disposition **may not follow classic pH-partition theory** if membrane
  transfer is mediated - enhanced or inhibited - by active transporters that are "yet to be
  identified": **Watt 2019**. The caution in `SKILL.md` about applying tissue-composition Kp
  equations outside their validated chemical space is a synthesis extension of this point,
  drawing on the distribution-model discussion in the build-workflow skill.
- Uncharacterised degrader ADME - non-specific protein binding in plasma and tissue,
  permeation into blood cells and hepatocytes, catabolism by CYPs and non-CYPs, renal
  excretion: **Watt 2019** ("Future considerations"), listed there as remaining to be
  investigated.
- That the linker region can be optimised to improve cell permeability, tissue distribution,
  metabolism/elimination and potency: **Watt 2019**, citing Neklesa et al. 2017, Bondeson et
  al. 2015 and Churcher, *J Med Chem* 2018; 61:444-452.
- **Target-mediated drug disposition** as a plausible but unproven elimination mechanism for
  degraders given their high molecular weight, by analogy with large molecules at low
  concentration: **Watt 2019**, citing Mager, *Biochem Pharmacol* 2006; 72:1-10. Watt 2019 are
  explicit that "this mechanism may exist for PROTACs, but at present has not been proven" -
  which is why `SKILL.md` frames it as a hypothesis to test rather than as either default or
  excluded.
- That PBPK modelling would be "a viable and valuable tool to incorporate all these ADME
  mechanisms together when exploring the local exposure in the target tissues
  mechanistically": **Watt 2019**. This sentence is the charter for this skill existing.

## PD model structure

- **The indirect response model with stimulation of dissipation** as the mathematical
  description of the degradation component is named in **Watt 2019**, citing Mager, Wyska &
  Jusko, *Drug Metab Dispos* 2003; 31:510-518 (a diversity-of-mechanism-based-PD-models
  review). The explicit ODE written in `SKILL.md` is the standard form of that model
  structure; Watt 2019 name it without writing it.
- That a mechanistic PD model needs **both** a target protein turnover component **and** a
  binding component, both linked to local degrader concentration defined by the PK or PBPK
  model, and that the impact of direct inhibition "may be minimal due to the lower PROTAC
  concentrations required to elicit protein degradation": **Watt 2019**. The instruction in
  `SKILL.md` to demonstrate rather than inherit that negligibility is a synthesis step.
- That an appropriate PD model should be linked to the PK, ideally PBPK, model to analyse in
  vivo protein knockdown across dose regimens: **Watt 2019**.
- That mechanistic PK/PD models incorporating protein turnover enable estimation of **in vivo
  DC50 and Dmax** values, and that knowledge gaps should be identified and addressed with
  further experiments in a learn-confirm-predict cycle: **Watt 2019**, Figure 1.

## Hook effect and ternary complex cooperativity - NOT from Watt 2019

**Watt 2019 does not discuss the hook effect.** The treatment in `SKILL.md` is domain
knowledge from the wider targeted-degradation literature and should not be cited to Watt.

The mechanism is standard for bifunctional recruiters: at high degrader concentration the two
unproductive binary complexes (degrader-target, degrader-ligase) outcompete the productive
ternary complex, producing a bell-shaped concentration-response curve. Its consequence for
modelling - that a monotonic Emax fitted over a low concentration range extrapolates in the
wrong direction at high dose - is the reason it is included here despite the absence of a
source in this folder.

The nearest anchor available in Watt 2019 is **cooperativity**: Watt 2019 cite Zorba et al.
2018, whose title is "Delineating the role of cooperativity in the design of potent PROTACs
for BTK", and note that PROTAC potency "is context dependent with reduced potency on THP-1
cells" for that molecule. Cooperativity and the hook effect are two faces of the same ternary
complex equilibrium, so the citation is adjacent rather than direct.

**Anyone extending this section should replace it with citations to the ternary complex
kinetic modelling literature**, which post-dates Watt 2019 and now provides explicit
mathematical treatments of both cooperativity and the hook effect. This is the largest
citation gap in this skill.

## Dose, potency and the gap between them

- **That the majority of degraders evaluated in vivo are of low nanomolar potency while the
  efficacious dose range is generally above 10 mg/kg, which "appears to be higher than might
  be expected for event-driven pharmacology" and "may reflect the fact that these molecules
  are unlikely to be fully optimised"**: **Watt 2019**. Both the observation and the
  explanation are theirs, and the honesty of the explanation is worth preserving.
- That in vivo efficacy is likely dependent on sufficient exposure of the degrader in the
  relevant tissue compartment, with in vitro activity determined in corresponding cellular
  assays (degradation, growth inhibition, apoptosis): **Watt 2019**.
- Reported exposures exceeding in vitro DC50 - PROTAC_ERRα in the µM range against a mouse
  serum DC50 of 0.9 µM; dBET1 Cmax 12,394 ng/mL; ARV-771 maintaining >8 nM over 24 h against
  a serum IC50 exceeded for 8-12 h of the interval; FLT3 degrader exceeding DC50 for 22 h:
  all **Watt 2019** Tables 1 and 2, citing Bondeson et al., *Nat Chem Biol* 2015;
  11:611-617; Winter et al., *Science* 2015; 348:1376-1381; Raina et al., *PNAS* 2016;
  Burslem et al., *JACS* 2018.
- Note the **serum-dependence of measured potency** visible in these numbers - DC50 of 100 nM
  in MCF-7 cells versus 0.9 µM in mouse serum for the same molecule. This is why `SKILL.md`
  requires the assay matrix and serum content to be stated with any DC50.

## PK driver and study design

- The three key components of a preclinical PK/PD study - full characterisation of
  concentration and PD modulation over time to expose temporal delays; choice of PD marker
  and whether it links to target protein and/or disease; and the impact of repeated dosing on
  both PK and PD - are **Watt 2019** summarising Tuntland et al., *Front Pharmacol* 2014;
  5:174. Biomarker classification: Danhof et al., *Pharm Res* 2005; 22:1432-1437.
- **The "three pillars of survival"** - exposure at the site of action, target binding, and
  expression of pharmacological activity - is **Watt 2019**, citing Morgan et al., *Drug
  Discov Today* 2012; 17:419-424. The degrader-specific amendment in `SKILL.md` (that pillar
  two becomes degradation, a time-shifted downstream consequence of binding) is a synthesis
  step.
- That the PK driver for efficacy (Cmax, Cmin, Cave) is determined through **dose
  fractionation** studies: **Watt 2019**, citing Tuntland et al. 2014. The precedent that
  Cave was the driver for tofacitinib in both preclinical collagen-induced arthritis and
  rheumatoid arthritis patients is **Watt 2019** citing Dowty et al., *J Pharmacol Exp Ther*
  2014; 348:165-173. In vivo EC50 generation to support human dose prediction: Gabrielsson,
  Peletier & Hjorth, *Eur J Pharmacol* 2018; 835:154-161.
- That close consideration of dosing schedule matters to avoid large peak-to-trough
  differences and consequent toxicity risk: **Watt 2019**, citing Chen, *Drug Discovery Today*
  2018; 24:371-376.
- The PK/PD-versus-efficacy model distinction, and the split of efficacy models into
  mechanistic/short-term (up to 1 week) and disease (>1 week), including explicitly what each
  model type **does not** give you - target validation, and for PK/PD models the relevant
  effect compartment: **Watt 2019** Table 3.
- That PD readouts may require ex vivo stimulation of blood or tissue, and that for degraders
  protein level is the simplest PD readout and an indication of target engagement:
  **Watt 2019**.
- **That understanding the relationship between targeted protein degradation and biological
  function is what determines the dosing regimen required for efficacy** - i.e. that protein
  loss is a biomarker and function is the claim - is **Watt 2019**, and is emphasised in their
  abstract as the paper's central recommendation.
- The inadequacy of n=2 per timepoint is flagged by **Watt 2019** in reporting Zorba et al.;
  the generalisation to a design requirement is a synthesis step.

## E3 ligase-intrinsic and neosubstrate pharmacology

- MDM2-recruiting BRD4 degraders producing synergy from combined pharmacology of target
  protein degradation **and** p53 stabilisation via the E3 ligase: **Watt 2019**, citing Hines
  et al., *Cancer Res* 2018.
- CRBN-based BTK degrader DD-03-171 exploiting combined degradation of BTK **and** the CRBN
  neosubstrates IKZF1/3, with the explicit observation that **BTK degradation was consistent
  while IKZF1 changes were "more variable" between animals and across PDX models**:
  **Watt 2019**, citing Dobrovolsky et al., *Blood* 2018; 133:952-961. This is the source of
  the `SKILL.md` warning that secondary neosubstrate degradation is more variable than primary
  target degradation.
- The counter-intuitive result that BTK *inhibition* upregulated CRBN neosubstrates in
  engrafted mice - contrary to the in vitro data - and that degrader treatment reduced this
  upregulation and improved survival relative to ibrutinib and lenalidomide: **Watt 2019** /
  Dobrovolsky et al. A useful reminder that in vitro-to-in vivo direction of effect is not
  guaranteed.
- Differentiated efficacy relative to an inactive degrader control (ARV-766) and to an
  inhibitor comparator (OTX015): **Watt 2019**, citing Raina et al. 2016 and Saenz et al.,
  *Leukemia* 2017. The inactive-degrader control is the right comparator design and worth
  copying.
- That efficacy magnitude varied across PDX models and that lack of efficacy occurred in
  models where the comparators were also ineffective: **Watt 2019** / Dobrovolsky et al.

## Limits of the published evidence base

- **That there are few examples of in vivo efficacy outside oncology, and that the evidence
  rests on xenograft models with profound tumour-volume effects**: **Watt 2019**, summary of
  the in vivo efficacy section. The `SKILL.md` warning against extrapolating from xenograft
  oncology follows directly.
- That efficacy-model targets are "generally endogenous... but in some cases (e.g. oncology
  models) this may be exogenous target (e.g. implantation cells in the animal)": **Watt 2019**
  Table 3 footnote. This is the basis for requiring endogenous-versus-implanted target as a
  reporting stratum - PROTAC_ERRα is the worked case, where tumour ERα and endogenous heart
  and kidney ERα were both measured (39% and 44% down-regulation respectively; Bondeson et
  al. 2015).
- **That neither allometric approaches for degrader distribution and elimination nor
  mechanistic PBPK defining human PK/PD from preclinical PK/PD had been reported for this
  modality**, and that a possible strategy is to establish (PB)PK/PD translation across
  preclinical species first and then predict the human regimen, remaining "full of
  uncertainties until the first PK/PD profile readout from the first-time-in-human study":
  **Watt 2019**, and it is a direct paraphrase. This is the whole of the translation section
  in `SKILL.md`.
- The predict-learn-confirm framing for building confidence in a unified (PB)PK/PD model
  across multiple preclinical species: **Watt 2019**, Figure 1 and summary, citing Morgan et
  al. 2012 and Kola & Landis, *Nat Rev Drug Discov* 2004; 3:711-715 on attrition.

## Caveats carried from the parent bundle

- All content is synthesised in original wording. Attributions point to where an idea came
  from; consult the primary publication for detail and for anything you intend to cite.
- Watt 2019 is a 2019 review of a fast-moving modality and its "has not been reported"
  statements were accurate then and are unlikely to be accurate now. Verify currency before
  repeating any claim of absence.
