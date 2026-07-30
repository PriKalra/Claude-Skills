# Evidence log - PBPK model build workflow

Provenance for the non-obvious claims in `SKILL.md`. Every entry points to where the idea
came from so you can go to the primary source, disagree with the synthesis, or update it as
the literature moves.

**Primary sources for this skill** (both present in the source folder as PDFs):

- **Jones HM, Rowland-Yeo K.** Basic Concepts in Physiologically Based Pharmacokinetic
  Modeling in Drug Discovery and Development. *CPT Pharmacometrics Syst Pharmacol* 2013;
  2:e63. doi:10.1038/psp.2013.41. Cited below as **Jones 2013**.
- **Kuepfer L, Niederalt C, Wendl T, Schlender J-F, Willmann S, Lippert J, Block M,
  Eissing T, Teutonico D.** Applied Concepts in PBPK Modeling: How to Build a PBPK/PD
  Model. *CPT Pharmacometrics Syst Pharmacol* 2016; 5:516-531. doi:10.1002/psp4.12134.
  Cited below as **Kuepfer 2016**.

Where a claim originates in a paper cited *by* one of these two rather than in the tutorial
itself, the downstream reference is named so the primary source can be consulted directly.

---

## Scope and honesty note on the goodness-of-fit section

**Phase B is not derived from Jones 2013 or Kuepfer 2016.** Those tutorials treat model
evaluation qualitatively - Kuepfer 2016 names visual comparison, RMSD, AUC error and the
concordance correlation coefficient, and Jones 2013 reports predicted-vs-observed tables
and visual inspection. Neither defines AFE, AAFE or GMFE.

The AFE / AAFE / GMFE / twofold-criterion apparatus in Phase B comes from general PBPK
qualification practice and from the requirements specified by the modeller who commissioned
this skill. It is documented here as **convention, not as a citation to the two source
papers**. Two consequences worth carrying:

1. The AAFE < 2 threshold is a widely used convention with regulatory currency. It is not a
   scientific constant and it is not traceable to a single authoritative derivation. Treat
   it as the default to be argued away from on the basis of intended purpose - which is why
   the skill asks the modeller for criteria rather than asserting them.
2. **GMFE is genuinely ambiguous in the literature.** Some authors define it as the
   geometric mean of absolute fold error, which is algebraically identical to AAFE; others
   as the geometric mean of the signed ratio, which is algebraically AFE. This is why
   `SKILL.md` and the output schema both require the formula to be printed next to the
   value. Before citing any published GMFE, check which formula that paper used.

Anyone extending Phase B should replace this section with citations to the specific
qualification-practice literature they intend to rely on.

---

## Model structure and the system/drug parameter separation

- The distinction between organism parameters, drug parameters, and drug-biological
  parameters that are the product of the two, is set out explicitly in **Kuepfer 2016**
  ("Building blocks in PBPK modeling"; their Figures 1b and 2). The framing of Kp and fu as
  *derived* rather than freely assignable follows from that hierarchy.
- The claim that extrapolation to a new population is only a change of system parameters,
  and that translation to a new therapeutic design usually needs only limited parameter
  changes, is stated in **Kuepfer 2016** ("Applications of PBPK modeling").
- "Default physiological parameters should only be changed if there is a mechanistic
  rationale" is close to verbatim from **Kuepfer 2016** ("Compilation of available data and
  information").
- The requirement that drug-dependent parameters and the calculation methods for
  distribution and cellular permeability be the same across species, with any exception
  given a plausible physiological explanation, is from **Kuepfer 2016** ("Consistency of
  PBPK models for different doses, across species, special populations, and compounds").
- Perfusion-rate-limited vs permeability-rate-limited tissue models, including that
  perfusion limitation suits small lipophilic molecules and permeability limitation suits
  larger polar molecules, is **Jones 2013** (their Figure 2), drawing on Nestorov 2007 and
  Blakey 1997.
- That active transport requires the tissue to be modelled as permeability-limited - with
  hepatic OATP uptake as the worked case, uptake at the sinusoidal membrane and biliary
  efflux at the canalicular membrane - is **Jones 2013**.
- Effective molecular weight correction for halogen atoms, which contribute less to
  molecular volume than their atomic weight implies, is **Kuepfer 2016**, citing Willmann
  et al., *J Med Chem* 2004; 47:4022-4031. The ciprofloxacin case study applies it
  (MW 331 → effective MW 314 g/mol for a single fluorine).
- Lumping of tissues with similar perfusion properties to reduce model complexity:
  **Jones 2013**, citing Nestorov et al. 1998.
- Peripheral venous vs central venous vs arterial sampling as a real source of
  model-data mismatch, and the addition of a peripheral venous compartment to address it:
  **Kuepfer 2016**, citing Levitt, *BMC Clin Pharmacol* 2004; 4:2, and Musther et al.,
  "Are physiologically based pharmacokinetic models reporting the right Cmax? Central
  venous versus peripheral sampling site", *AAPS J* 2015; 17:1268-1279. The Musther title
  is worth reading as a standalone warning.

## Distribution models and the Vss constraint

- The five distribution-model families and what distinguishes them are catalogued in
  **Kuepfer 2016** ("Distribution models"): Poulin & Theil (*J Pharm Sci* 2002;
  91:129-156) on neutral lipid / phospholipid / water composition; Rodgers & Rowland
  (*Pharm Res* 2007; 24:918-933) extending to electrostatic interactions with acidic
  phospholipids and ionisation at physiological pH; Berezhkovskiy (*J Pharm Sci* 2004;
  93:364-374) correcting for peripheral elimination; Willmann et al. using membrane
  affinity (logMA) as the lipophilicity measure; Schmitt (*Toxicol In Vitro* 2008;
  22:457-467) compartmentalising tissue into water, neutral lipids, neutral and acidic
  phospholipids and proteins. **Jones 2013** gives the same landscape and adds the unified
  algorithms of Schmitt 2008 and Peyret et al. 2010.
- **The load-bearing claim of this skill** - that different distribution models can
  reproduce very similar plasma profiles while generating substantially different tissue
  concentrations, and that the choice of distribution model is therefore "a fundamental
  assumption during model development" whose inherent uncertainty must be carried into the
  predictions - is stated directly in **Kuepfer 2016** and illustrated in their Figure 1c
  using theophylline. Kuepfer are also careful to say Figure 1c should not be used to rank
  the models generally, since the result is compound-specific. That caution is preserved in
  `SKILL.md`.
- Vss = Σ(V_T × Kp_T) + V_P is **Jones 2013** Eq. 6. The inference drawn in `SKILL.md` -
  that the Kp set therefore makes a falsifiable prediction about an IV-measurable quantity,
  so a distribution model can be rejected on arithmetic before any curve is fitted - is a
  synthesis step, not a claim made in either paper. It follows directly from the equation.
- That models built on in silico Kp values without measured tissue data may oversimplify
  tissue kinetics, and that in vivo tissue data may be required where target-tissue
  concentration matters, is **Jones 2013**, citing Graham et al. 2012 and Poulin & Theil
  2009 on the predictive performance of Kp methods.

## Lipophilicity as a tuned surrogate

- **Kuepfer 2016** states that surrogate compound parameters such as lipophilicity "are
  usually slightly adapted at this stage to obtain a good agreement with experimental i.v.
  data", and - explicitly - that "there are no established rules regarding the extent of
  change that is reasonable". They also note that measured octanol-water partitioning does
  not match in vivo conditions, so a physicochemical input can legitimately differ from its
  measured value.
- The reporting discipline in `SKILL.md` (report measured value, used value, and the
  difference; read a large difference as evidence against the distribution model rather
  than as successful calibration) is a synthesis response to that acknowledged gap, not a
  rule stated by Kuepfer.

## Build order: IV then oral

- The stepwise establishment of an IV model before an oral model, so that distribution and
  clearance are informed independently of absorption, and the instruction that at the oral
  step "none of the parameters that influence distribution or metabolism/excretion should be
  further modified and only those parameters that influence the oral absorption should be
  varied", is **Kuepfer 2016** ("Establishment of the PBPK model for oral administration").
- The named exception - that strict sequencing may be wrong when the IV model already
  contains processes relevant to oral absorption, e.g. uptake transporters, in which case
  IV and PO data should be fitted simultaneously - is also **Kuepfer 2016**, in the same
  section. It is easy to miss and worth preserving.
- That model uncertainty is considerably higher without IV data, depending on BCS class and
  on whether transporters or gut-wall metabolism are involved, and that a human oral model
  may then be scaled from an animal model established with both IV and PO data:
  **Kuepfer 2016**.
- Typical parameters to estimate at the oral step - intestinal permeability, meal events,
  formulation release, solubility, then enterohepatic recycling if relevant:
  **Kuepfer 2016**.
- That the number of independent model parameters for a new compound is usually small, "in
  most cases fewer than five per compound", because of the volume of prior physiological
  information incorporated, is **Kuepfer 2016**. The use of this as a diagnostic threshold in
  `SKILL.md` is a synthesis step.

## Learn, confirm, refine; and top-down/bottom-up

- The iterative animal-then-human strategy, including the diagnostic value of an animal
  simulation failure as evidence that a biological mechanism exists which the ADME screening
  assays did not capture, is **Jones 2013** (their Figure 3), citing the strategy validated
  in Jones et al., *Clin Pharmacokinet* 2006; 45:511-542 and 2011; 50:331-347, and
  independently by De Buck et al. 2007.
- The "Parameter X" pattern - identify the single missing component or erroneous parameter,
  fit it top-down against clinical data while all other parameters remain bottom-up, then
  validate against an independent clinical dataset not used in development - is **Jones
  2013**, citing Vieira et al., *Clin Pharmacol Ther* 2012; 91:700-708 and the best-practice
  discussion in Zhao, Rowland & Huang, *Clin Pharmacol Ther* 2012; 92:17-20.
- The worked repaglinide example is **Jones 2013**: an OATP1B1 uptake CLint of
  282 µL/min/10⁶ cells obtained by top-down fitting because no in vitro uptake data existed,
  followed by confirmation against three mechanistically distinct inhibitors (trimethoprim /
  CYP2C8, clarithromycin / CYP3A4, cyclosporine / OATP1B1). Their conclusion that "even when
  investigating a single DDI pair, a matrix of interactions should be investigated to ensure
  that all components of the PBPK models are robust" is the origin of the network-
  qualification logic that the DDI skill develops at length.
- The observation that combining all three inhibitors gave a 5.6-fold mean AUC increase
  ranging from 2.3- to 18-fold across the virtual population - with no clinical data to
  confirm the combination - is **Jones 2013**, and is the cleanest available illustration of
  why population variability, not the mean, is the object of interest in risk assessment.
- Bayesian methods as well suited to PBPK because of the volume of prior information they
  can incorporate: **Jones 2013**, citing Gelman & Rubin 1996; also **Kuepfer 2016**, citing
  Krauss et al., *In Silico Pharmacol* 2013; 1:6.

## Clearance, IVIVE and active processes

- Hepatic CLint scaling from microsomes (MPPGL) and hepatocytes (HPGL), with division by
  fu,inc, is **Jones 2013** Eqs. 3 and 4. The IVIVE skill in this suite treats the scaling
  chain in full; it is referenced rather than repeated here.
- Allometric scaling CL = a·BW^b for routes without an IVIVE path, e.g. renal and biliary
  excretion, is **Jones 2013** Eq. 5, citing Mahmood & Sahajwalla 2002 and Paine et al. 2011.
- Michaelis-Menten kinetics collapsing to first order with rate constant Vmax/Km when
  [S] << Km is **Kuepfer 2016** Eq. 1 and the sentence following it. The explicit
  requirement that "data for different doses have to be available (showing
  dose-nonlinearity) in order to be able to identify Michaelis-Menten parameters" is also
  **Kuepfer 2016**. The framing in `SKILL.md` as an identifiability failure - that two
  reported numbers are a reparameterisation of one, and their apparent precision is an
  artefact - is a synthesis step.
- Gene expression as a surrogate for tissue-specific protein abundance, with the
  reformulation Vmax_j = kcat* · e_rel,j collapsing per-organ catalytic efficiencies into a
  single estimated parameter, is **Kuepfer 2016** Eq. 2, citing Meyer et al., *Drug Metab
  Dispos* 2012; 40:892-901. Kuepfer are explicit that gene-versus-protein correlation "is a
  matter of debate" and that post-transcriptional effects are absorbed into kcat*, which is
  the basis for the caveat in `SKILL.md`.
- That renal excretion of ciprofloxacin cannot be explained by passive glomerular filtration
  alone and requires active tubular secretion is **Kuepfer 2016**. The generalised test used
  in `SKILL.md` - compare renal CL against fu × GFR, infer secretion above and reabsorption
  below - is the standard reading of that comparison and is developed further in the renal
  impairment skill.
- **No reliable in vitro-in vivo correlation exists for P-glycoprotein kinetic parameters,
  so gut efflux modelling relies "mainly on model fitting rather than model simulations".**
  This is stated in **Jones 2013**, citing Watson, Davis & Jones, *Drug Metab Dispos* 2011;
  39:1203-1213. It is one of the most important and least-quoted sentences in the tutorial.
- That successful OATP-substrate PBPK predictions "in most cases" required empirical scaling
  factors is **Jones 2013**, citing Jones et al., *Drug Metab Dispos* 2012; 40:1007-1017,
  Poirier et al. 2009 and Watanabe et al. 2009. Also **Jones 2013** on the need for a
  combined top-down/bottom-up fitting approach where uptake transport is involved, citing
  Varma et al. 2012 and Rowland-Yeo et al. 2012.
- The reminder that enzyme/transporter interplay and compensatory up-regulation of
  alternative transporters when one is suppressed remain unresolved limitations:
  **Jones 2013** ("Future perspectives").

## Absorption and formulation

- Gut segmentation into lumen and enterocyte subcompartments with segment-specific volume,
  transit time and pH, and the use of pKa with pH-partition theory to drive dissolution and
  precipitation: **Jones 2013**. **Kuepfer 2016** adds the mucosal/non-mucosal split of the
  gut wall, which is what allows gut-wall metabolism to be localised correctly, citing Thelen
  & Dressman, *J Pharm Pharmacol* 2009; 61:541-558.
- ADAM, ACAT and PK-Sim absorption models: **Jones 2013**, citing Agoram et al. 2001,
  Willmann et al. 2004 and Jamei et al. 2009.
- That in vitro permeability must be calibrated against reference drugs with measured human
  in vivo jejunal permeability before use as effective permeability is **Jones 2013**, citing
  Lennernäs, *J Pharm Sci* 1998; 87:403-410. **Kuepfer 2016** catalogues the available
  permeability systems (artificial membranes, Caco-2, MDCK, Ussing chambers, single-pass
  intestinal perfusion).
- That aqueous solubility under-predicts in vivo dissolution for poorly soluble lipophilic
  compounds because bile salts and lipids enhance solubilisation, making FaSSIF/FeSSIF
  biorelevant media necessary for reliable simulation in both animals and humans, is
  **Jones 2013**, citing Jantratid et al. 2008, Parrott et al. 2009 and Jones et al. 2006.
  The last of these is specifically on food-effect prediction, which is why `SKILL.md` links
  aqueous solubility inputs to spurious food effects.
- Paracellular absorption contributing significantly for a small number of compounds
  (acyclovir, cimetidine, ranitidine) but marginally in general, owing to the small
  paracellular surface fraction: **Kuepfer 2016**, citing Thelen et al. 2011 and
  Lennernäs 2007.
- Charged species having lower permeability than the neutral form, so that intestinal pH
  affects both solubility (Henderson-Hasselbalch) and permeability: **Kuepfer 2016**. The
  ionisation-and-binding skill in this suite develops the pKa consequences.
- IVIVC from biorelevant dissolution profiles, and the alternative of embedding dissolution
  functions directly in the GI compartments: **Kuepfer 2016**, citing Dressman et al. 1998,
  Cardot et al. 2007, Costa & Sousa Lobo 2001 and Thelen et al. 2012.

## Large molecules

- Two-pore formalism for convective and diffusive extravasation, lymphatic return from
  interstitium to systemic circulation, endosomal catabolism, and FcRn-mediated recycling as
  the processes that distinguish a protein PBPK model from a small-molecule one:
  **Kuepfer 2016** ("Passive and active processes for large molecules"), citing the
  two-pore literature and the FcRn-containing PBPK models that followed. **Jones 2013**
  flags routine large-molecule PBPK as a coming development, citing Shah & Betts,
  *J Pharmacokinet Pharmacodyn* 2012; 39:67-86.
- Target-mediated disposition and immunogenicity as additional processes to implement when
  relevant: **Kuepfer 2016**.
- Receptor abundance quantified by the same relative-expression approach used for enzymes and
  transporters, including for antibody-drug conjugates: **Kuepfer 2016**, citing Block,
  *Expert Opin Drug Metab Toxicol* 2015; 11:743-756.

## PD coupling

- That PBPK/PD is a multiscale construct in which simulated tissue concentrations feed a
  downstream PD model, and that explicit organ representation is what makes on- and
  off-target tissue exposure directly quantifiable, is **Kuepfer 2016** ("Pharmacodynamic
  modeling").
- The ciprofloxacin PBPK/PD case study drives an adaptive Emax bacterial kill model from the
  **lung interstitial** concentration - the site of infection - not from plasma:
  **Kuepfer 2016**, coupling to the PK/PD model of Schuck et al., *Infection* 2005;
  33(suppl 2):22-28. This is the concrete precedent for the rule in `SKILL.md` that PD must
  be driven by a named site-of-action compartment.
- The parallel observation in **Jones 2013** that a repaglinide model's value includes
  predicting exposure in the pancreas, the site of action, which can then drive a PD model,
  makes the same point from the other tutorial.
- That the pancreas/lung examples generalise to QSP-scale disease models - glucose-insulin
  regulation, endometriosis, acetaminophen intoxication, drug-induced liver injury,
  antithrombotic therapy design - is **Kuepfer 2016** ("Applications"), and is the bridge
  from this skill into systems-pharmacology work.

## Model evaluation (Phase A material)

- The evaluation criteria in `SKILL.md`'s Phase A hierarchy are assembled from
  **Kuepfer 2016** ("Overall model evaluation"): visual comparison of simulated vs
  experimental profiles on both absolute concentration and dynamic shape; error functions
  (RMSD, AUC error, concordance correlation coefficient); comparison of Cmax, tmax, AUC and
  t½; consistency across doses not used in development as a test of *structure*; consistency
  across species with unchanged drug parameters; evaluation in special populations after
  changing physiology only; cross-compound consistency of shared system parameters, with the
  shared-receptor-concentration example; and external validation data.
- The specific tiering into levels 0-5, and the claim that tier-0 agreement carries near-zero
  evidential weight, is a synthesis step. Kuepfer 2016 make the components explicit but do
  not rank them.
- **Kuepfer 2016** also note that experimental data carry their own uncertainty and may
  themselves warrant critical re-evaluation - the basis for the digitisation-error caution in
  the output schema.
- Sensitivity analysis on uncertain parameters, best- and worst-case scenario simulation
  within experimental and physiological uncertainty, and simulation of *model alternatives*
  where the uncertainty is structural rather than numerical: **Kuepfer 2016** ("Sensitivity
  analysis or best- and worst-case scenarios"). Their framing - that the purpose is to
  assess whether the conclusions of the modelling work are robust, not merely to rank
  parameters - is the reason `SKILL.md` requires verdict robustness rather than a bare
  sensitivity ranking.
- Best-practice development efforts by regulators, industry and academia are referenced
  collectively in **Kuepfer 2016**; **Jones 2013** points specifically to Zhao, Rowland &
  Huang 2012.

## Regulatory framing

- 33 PBPK submissions to FDA between June 2008 and December 2012, PBPK advocacy in the
  then-current FDA and EMA drug-interaction guidance, the hepatic impairment guidance
  recommending PBPK development, and paediatric investigation plan timelines driving
  paediatric PBPK use, are all **Jones 2013**, citing Huang et al. 2013, Zhao et al. 2012,
  Huang & Rowland 2012 and Rowland, Peck & Tucker 2011.
- **The circularity argument is Jones 2013's own, and is quoted rather than softened
  because it is unusually candid for a tutorial:** the aspiration to have clinical DDI
  studies waived on the strength of a validated model "begs the question of how reliable is
  the model?... this then becomes a circular argument in that the robustness of the model is
  brought into question as there are no clinical DDI data to support it." Their resolution -
  that conducting clinical DDI studies "does not negate the impact of PBPK models... it only
  serves to enhance them", and that the strongest application is prediction in individuals at
  extreme risk or in subjects who cannot ethically be investigated in formal trials - is
  reproduced in `SKILL.md` because it is both honest and strategically usable.
- **Caveat carried from the parent bundle:** numeric criteria quoted from regulatory guidance
  anywhere in this suite are paraphrased summaries of secondary commentary and change over
  time. Both source tutorials predate the current guidance generation - Jones 2013 discusses
  the 2012 FDA and EMA interaction guidance. Verify against current guideline text before
  relying on any threshold, cut-off or study-design requirement.

## Resourcing and practice

- That PBPK users come from drug metabolism, pharmacology, medicine, pharmacy, biophysics,
  engineering, mathematics, programming and statistics, that dedicated users should be
  assigned to retain the skill set, and that continuous education is required as the models
  evolve, is **Jones 2013** ("Future perspectives" and "Conclusions"). It is not a scientific
  claim, but it is the reason a skill like this one is written down rather than held
  informally.
- Gaps flagged by **Jones 2013** that remain worth tracking: missing system parameters for
  enzyme and transporter abundances; scarce physiology for different ethnic populations and
  disease groups; difficulty measuring depletion for low-clearance compounds, citing Di et
  al. 2012 on the relay method; and the growing prevalence of transporter-mediated PK as
  compounds are optimised for metabolic stability.
