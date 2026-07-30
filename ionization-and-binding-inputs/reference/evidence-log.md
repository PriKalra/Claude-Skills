# Evidence log - ionization and binding inputs

Provenance for the non-obvious claims in `SKILL.md`.

**Primary sources** (both present in the source folder as PDFs):

- **Parkinson A.** Regulatory Recommendations for Calculating the Unbound Maximum Hepatic
  Inlet Concentration: A Complicated Story with a Surprising and Happy Ending. *Drug Metab
  Dispos* 2019; 47:779-784. doi:10.1124/dmd.119.086496. XPD Consulting. Cited as
  **Parkinson 2019**.
- **Charifson PS, Walters WP.** Acidic and Basic Drugs in Medicinal Chemistry: A Perspective.
  *J Med Chem* 2014. doi:10.1021/jm501000a. Vertex Pharmaceuticals. Cited as
  **Charifson 2014**.

The two papers divide cleanly: Parkinson 2019 supplies the hepatic inlet arithmetic, the
blood/plasma conversion logic, and the Rb reversibility caveat. Charifson 2014 supplies the
ionization-to-property relationships, the class-level DMPK and distribution trends, and the pKa
optimisation material.

---

## The hepatic inlet concentration - all Parkinson 2019

- The three in vivo concentrations used in basic static models (Igut, Imax,u, Iin,max,u), each
  with a safety factor to minimise false negatives, and the assignment of each to a mechanism
  class - Igut for intestinal CYP3A and efflux transporters, Imax,u for hepatic P450s and
  hepatic efflux and renal transporters, Iin,max,u for hepatic uptake transporters:
  **Parkinson 2019**, opening summary.
- Igut calculated as dose/250 mL by all agencies: **Parkinson 2019**.
- Imax,u calculated as plasma Cmax,ss × fu,p with a **lower limit of 0.01 on fu,p** for the
  basic static models, by all three agencies: **Parkinson 2019**.
- The base equation Iin,max,B = Imax,B + (Dose × Fa × ka)/QH is **Parkinson 2019** eq. 1,
  reconstructed from **Ito K, Iwatsubo T, Kanamitsu S, Ueda K, Suzuki H, Sugiyama Y**,
  *Pharmacol Rev* 1998; 50:387-412 (their eq. 22, abbreviated). Parkinson is explicit that he
  writes Ito's version rather than the agencies' "because the corresponding equation described
  by all three agencies contains ambiguities (at best) or errors (at worst)", and that the B
  subscript was added to clarify these are **blood** concentrations - evident from Ito's own
  Figure 4.
- **The Fa redefinition** - agencies replaced Ito's Fa (fraction of oral dose reaching hepatic
  portal vein) with Fa × Fg, redefining Fa as fraction absorbed from the gut lumen and Fg as
  fraction passing the gut wall into portal blood, such that **Ito's Fa is identical to the
  agencies' Fa × Fg** - is **Parkinson 2019**. The Fa·Fg = 0.5 worked example (complete
  absorption with 50% gut-wall extraction versus 50% absorption with no gut-wall loss) is his,
  as is the observation that the split "captures additional mechanistic information".
- Default conventions: **ka = 0.1 min⁻¹** unless determined experimentally (0.1 of dose absorbed
  per minute); **Fa × Fg = 1** unless determined experimentally; **QH = 97 L/h = 1.62 L/min for
  a 70 kg person, used by all three agencies**: all **Parkinson 2019**. He also notes the unit
  discipline - dose in µmol with QH in L/min gives µM to match Ki or IC50.
- **The unexplained use of total hepatic blood flow rather than portal blood flow (~80% of
  total)** is **Parkinson 2019**'s own stated puzzlement: "I do not know why blood flow is based
  on total hepatic blood flow (97 l/h) and not on hepatic portal blood flow". Portal flow figure
  from Carlisle et al., *Gut* 1992; 33:92-97.
- Rb definition (CB/CP) and the two conversion directions (CP = CB/Rb, CB = CP × Rb):
  **Parkinson 2019** eq. 3 and surrounding text.
- The FDA plasma forms - total plasma Iin,max with the Rb in the denominator of the portal term
  only, and plasma Iin,max,u = fu,p × plasma Iin,max - are **Parkinson 2019** eqs. 4 and 5,
  derived by dividing the blood equation through by Rb.
- **Parkinson's expansion of the FDA's single-line equation (his eqs. 6→7→8) exists specifically
  to show that the systemic term is being divided by QH and Rb when only the portal term should
  be** - i.e. the compressed guidance form is ambiguous about operator precedence. This is the
  origin of the `SKILL.md` instruction to derive rather than copy.
- fu,b = fu,p / Rb, alongside CB = CP × Rb: **Parkinson 2019** eq. 11.
- The EMA blood equation, **correct only if the portal term is divided by QH before being added
  to Imax,B**, with Parkinson noting that "adding brackets around the term Fa·Fg·ka·Dose/QH
  would remove any ambiguity": **Parkinson 2019** eq. 10.

### The central result

- **That the unbound maximum hepatic inlet concentration is identical in blood and plasma for a
  given Rb, because concentration converts by multiplying by Rb while unbound fraction converts
  by dividing by Rb, and "the two cancel each other out"** - is **Parkinson 2019**, demonstrated
  numerically in his Table 1 and proved algebraically in his eqs. 12→13→14→15. This is the
  paper's title result and the "surprising and happy ending".
- The Table 1 simulation that demonstrates it: three drugs with Rb of 0.5, 1.0 and 2.0; oral dose
  325 µmol; total systemic plasma Cmax 4.0 µM; 80% plasma protein bound (fu,p = 0.2);
  Fa·Fg = 1; ka = 0.1 min⁻¹; QH = 1.62 L/min. Results: total plasma Iin,max of 44 / 24 / 14 µM
  versus total blood of 22 / 24 / 28 µM - **different** - while unbound Iin,max,u is
  8.8 / 4.8 / 2.8 µM in **both** matrices. **Parkinson 2019** Table 1. Note this also shows Rb
  does affect the unbound value across drugs; the invariance is between matrices for a fixed Rb,
  which Parkinson emphasises with the qualification "for a given value of Rb".
- The `SKILL.md` use of this as an arithmetic self-check (compute both ways; disagreement means
  error) is a small synthesis step.

### Cutoffs and nomenclature

- **FDA/PMDA criterion 1 + Iin,max,u/(Ki or IC50) ≥ 1.1 for OATP1B1/1B3; EMA criterion
  25 × [I]u,inlet,max/(Ki or IC50) ≥ 1 for OATP1B1/1B3/OCT1, equivalent to (1 + ratio) with a
  cutoff of 1.04; "in other words, the EMA is more conservative than the other two agencies"**:
  **Parkinson 2019** eqs. 15 and 16 and the sentence following. Note OCT1 is in the EMA's scope
  and not the others'.
- Parkinson's framing question - "Is it possible that a negative result according to one agency
  might be positive according to another agency?" - and his answer that identical unbound
  concentrations would give identical outcomes **only if** agencies used the same equations and
  cutoffs, "but they do not": **Parkinson 2019**. This is the basis for the `SKILL.md`
  requirement to state the conclusion per agency criterion.
- The nomenclature table (FDA Iin,max/Iin,max,u in plasma; EMA [I]inlet,max/[I]u,inlet,max in
  blood; PMDA Iinlet,max/Iinlet,max,u in blood; EMA and PMDA equations identical, only symbols
  differ): **Parkinson 2019** Table 2 and text.
- That all three agencies also call this quantity **[I]h**, with the EMA making the
  correspondence explicit, the PMDA confirming it only in a Question & Answer section (question
  15) rather than in the guideline body, and the PMDA otherwise defining [I]h merely as "the
  concentration of the investigational drug in the liver hepatocytes": **Parkinson 2019**.

### Documented defects in the source guidance and literature

Every one of these is Parkinson 2019 and is the reason `SKILL.md` says derive, do not copy.

- **PMDA draft (Sept 2017) versus final (Feb 2018):** identical-looking equations concealing a
  changed definition of Cmax from *plasma* to *blood*. In the draft, Iinlet,max was the sum of a
  plasma concentration and a portal blood concentration, so it "was neither plasma concentration
  nor blood concentration but a combination of both". The final version corrected this.
- **FDA internal inconsistency:** the equation for Iin,max,u uses Fa × Fg while the equation for
  [I]h retains Ito's original Fa, "even though they are the same thing, namely, the unbound
  maximum hepatic inlet concentration". Parkinson's recommendation is explicit: "Equation 5
  should be used to calculate plasma Iin,max,u and [I]h."
- **Missing plus sign in a consensus publication:** Parkinson 2019 Figure 1 reproduces eq. 3 of
  **Vieira ML et al.**, *Clin Pharmacol Ther* 2014; 95:189-198 - a paper authored by
  representatives of FDA, EMA, industry and academia - and identifies a missing plus sign. He
  also notes it uses Ito's Fa rather than Fa × Fg. His comment that this "supports my contention
  that this is a rather tricky area to navigate" is the mildest possible phrasing.
- Parkinson's positive note on the same figure - that it "reinforces the importance of using
  blood concentration in equations that include one or more terms for organ blood flow" - and the
  parallel with misuse of the well-stirred hepatic clearance model, citing **Yang J, Jamei M,
  Rowland Yeo K, Rostami-Hodjegan A, Tucker GT**, "Misuse of the well-stirred model of hepatic
  drug clearance", *Drug Metab Dispos* 2007; 35:501-502. This is the source of the `SKILL.md`
  rule that blood-flow-containing equations require blood concentrations.
- OATP evaluation **with and without a 30-minute preincubation** of test drug with test system, to
  assess time-dependent inhibition of OATPs - recommended by FDA (2017 update) and PMDA (2018
  update): **Parkinson 2019**.
- That "static mechanistic model", "mechanistic static model" and "mechanism-based static
  pharmacokinetic (MSPK) model" are "all one and the same"; that it assesses the net effect of
  reversible inhibition, irreversible inhibition and induction in liver and intestine; but that
  EMA and FDA both recommend **not** combining induction assessment with reversible and
  irreversible inhibition assessment, while reversible plus irreversible inhibition may be
  combined: **Parkinson 2019**.

### Blood-to-plasma ratio and the reversibility caveat

- **Class expectations: non-ionised and basic drugs distribute evenly with Rb near 1; zwitterionic
  and acidic drugs are excluded from erythrocytes with Rb roughly 0.55, "which is 1 −
  hematocrit"**: **Parkinson 2019**, citing Hallifax, Foster & Houston, *Pharm Res* 2010;
  27:2150-2161.
- Tacrolimus Rb averaging about 15, concentration-dependent and ranging 4 to 114:
  **Parkinson 2019**, citing Wallemacq & Verbeeck, *Clin Pharmacokinet* 2001; 40:283-295.
- **The tacrolimus case as an example of non-restrictive high partitioning:** hepatic clearance
  from blood 2.1-6.3 L/h (well below hepatic blood flow 97 L/h) while hepatic clearance from
  plasma is 42-378 L/h, greatly exceeding both hepatic blood flow and hepatic plasma flow
  (~53 L/h); the finding that CL_P = CL_B × Rb suggests hepatic clearance is **not** restricted
  by erythrocyte partitioning, owing to reversible binding to the intracellular immunophilin
  FKBP12: **Parkinson 2019**. This worked example is the basis for the `SKILL.md` statement that
  a plasma clearance exceeding hepatic plasma flow is not necessarily an error.
- **The counter-case:** diuretics chlorthalidone, dorzolamide and methazolamide with Rb of 30 to
  240, arising from high-affinity binding to erythrocyte **carbonic anhydrase**, which "do not
  move freely between erythrocytes and plasma; hence, for these drugs - drugs whose movement out
  of erythrocytes is restricted - it is their concentration in plasma that is relevant to their
  potential to cause drug interactions": **Parkinson 2019**, citing Hinderling, "Red blood cells:
  a neglected compartment in pharmacokinetics and pharmacodynamics", *Pharmacol Rev* 1997;
  49:279-295.
- **The operational rule** - "when a drug has a high value of Rb, it would be prudent to establish
  that partitioning into erythrocytes is readily reversible before using Rb to interconvert values
  of drug concentration in blood and plasma, protein binding in blood and plasma, and/or
  clearance from blood and plasma. Such interconversions are not appropriate when high values of
  Rb reflect restrictive binding of drugs to erythrocytes" - is **Parkinson 2019**, close to
  verbatim. It is the named silent-failure assumption in `SKILL.md` and is Parkinson's own
  emphasis, not a synthesis step.

---

## Ionization and physicochemistry - Charifson 2014

- Brønsted-Lowry as the most useful definition for ionic equilibria discussions, with the Lewis
  extension noted: **Charifson 2014**.
- Henderson-Hasselbalch in the acid and base forms given in `SKILL.md`: **Charifson 2014**.
- **pH-partition theory** - that the un-ionised form preferentially traverses gastrointestinal
  and other lipid membranes by passive diffusion - and the explicit caveat that "this is not an
  absolute; i.e., a small amount of ionized species can permeate membranes passively",
  **including zwitterions**: **Charifson 2014**, with multiple supporting references.
- **The zwitterionic fluoroquinolone mechanism** - that they are suggested to cross membranes "in
  antiparallel stacked arrangements that reduce overall electrostatic potential and polarity,
  thus presenting themselves to membrane bilayers as neutral species": **Charifson 2014**. This
  is the specific exception that defeats a fraction-un-ionised permeability estimate.
- **Carrier-mediated uptake** - "there is some evidence suggesting that at least some, if not
  most, small molecules may get into cells through carrier-mediated uptake", in which case
  "the acidity or basicity of a given compound might impact absorption/permeability more through
  carrier selectivity rather than the overall extent of ionization": **Charifson 2014**. The
  strength of that claim ("if not most") is theirs.
- That gastric absorption is theoretically possible for un-ionised acids but that "clear examples
  of gastric absorption only (in the absence of intestinal absorption as well) are quite rare",
  and that most cellular compartments deviating from neutral pH are **acidic**:
  **Charifson 2014**.
- logP versus logD definitions, that logD is usually evaluated at pH 7.4 in octanol, and that
  because octanol contains some water, ionisation can occur in the organic phase so **logD
  formally represents the sum of all ionised species in both phases**: **Charifson 2014**.
- **That modifying pKa to increase polarity has "the same net impact as lowering lipophilicity",
  making it "difficult to understand which property is more relevant or whether these properties
  can be viewed in isolation"**: **Charifson 2014**. This is the confound acknowledged throughout
  `SKILL.md` and it is the authors' own caution, stated more than once. They also note that many
  prior papers "do not, in fact, describe how they define acids or bases".
- Their methodological point that analyses were restricted to public sources (ChEMBL, DrugBank,
  PubChem) for reproducibility: **Charifson 2014** Methods.

## Class-level DMPK trends - Charifson 2014

All of these are population-level statistical trends from public datasets, which is why
`SKILL.md` restricts their use to plausibility checking.

- **Solubility:** all ionised species (acids, bases, zwitterions) generally more soluble than
  neutrals, with lipophilicity playing an important role; in Charifson's 37,100-compound PubChem
  subset of the NIH MLSMR, bases showed a slight but statistically significant advantage over
  acids - which **differs** from an earlier ~44,500-compound GSK analysis finding acids more
  soluble. **Charifson 2014** state the main lesson is the ionised-versus-neutral distinction
  rather than acid-versus-base, which is the framing used in `SKILL.md`.
- **Hepatic clearance:** acids generally lower than other ionization classes, consistent with
  protein binding; higher hepatic clearance among bases and neutrals associated with higher
  lipophilicity. From a combined analysis of two published datasets, 591 unique compounds.
  **Charifson 2014** Figure 6.
- **Renal clearance:** more polar ionised acids, bases and zwitterions generally show higher
  clearance than neutrals, and renally cleared compounds tend to be of low lipophilicity. 471
  unique compounds. **Charifson 2014**.
- **Tissue distribution:** "tissue distribution depends on competition of compound binding to
  blood versus tissues, as well as within individual tissues"; **acidic drugs tend to be highly
  bound to plasma proteins and are most often present in tissue extracellular water**, with
  distribution to adjacent tissues depending on acid strength; **lipophilic basic drugs tend to be
  stored in tissues rich in acidic phospholipids and in acidic cellular organelles such as
  lysosomes (e.g. liver, lung, kidney)**, where they become protonated and thus sequestered:
  **Charifson 2014**.
- **Lysosomotropism** and accumulation in other acidic organelles (mitochondria, endosomes,
  Golgi) as a contributor to safety issues, and the authors' remark that "drug distribution in
  cells is a fundamental, yet often overlooked aspect in drug efficacy": **Charifson 2014**.
- The connection drawn in `SKILL.md` between this mechanism and the ionised-species terms of the
  Rodgers-Rowland and Schmitt distribution models is a **synthesis step** linking Charifson 2014
  to the distribution-model material in the build-workflow skill. Charifson 2014 do not discuss
  PBPK distribution models.
- **Erythrocyte partitioning of bases** driven by electrostatic attraction to the negatively
  charged phosphatidylserine component of RBC membranes, with lipophilicity contributing:
  **Charifson 2014** Table 2. This is the mechanistic complement to Parkinson 2019's empirical Rb
  class expectations, and the two agree.
- **Selectivity:** basic compounds generally less selective than acidic or neutral compounds, from
  3,282 ChEMBL compounds tested in at least 20 assays at two activity cutoffs; literature
  consensus that basicity drives promiscuity and lipophilicity contributes. **Charifson 2014**
  Figure 7. They note the zwitterion count was too small for generalisation.
- **Cellular potency:** acids generally less active in cellular assays (zwitterionic > basic and
  neutral > acidic), from ~400,000 data points across 62,839 compounds - but with the explicit
  caveat that "there are still a significant number of acids with potent cellular activity":
  **Charifson 2014** Figure 9.
- Tumour pH gradient: intracellular pH roughly equal in tumour and normal cells (~7.2) while
  immediate extracellular pH is consistently ~0.4 units lower in tumour; some drug-sensitive
  tumours show defective lysosomal acidification raising cytoplasmic and nuclear concentrations of
  weakly basic drugs, while some resistant tumours sequester basic drugs in lysosomes:
  **Charifson 2014**. Not used in `SKILL.md` but relevant to any oncology tissue-exposure model.

## Transporter and safety priors by class - Charifson 2014 Table 2

- **OATPs:** inhibitors tend to be strong acids, especially carboxylate-containing molecules, with
  lipophilicity and hydrogen bond acceptor count contributing. Charifson 2014 extended the
  literature analysis with 224 ChEMBL compounds (their Figure 8), finding increased lipophilicity
  correlates with OATP inhibition more for basic and neutral molecules than for acidic ones.
- **OCTs:** most important features are lipophilicity and positive charge.
- **BSEP:** substrates are monovalent negatively charged acids; 30-40% of inhibitors are acidic but
  the majority of BSEP inhibitors are un-ionised; positively charged compounds are negatively
  correlated with BSEP inhibition.
- **hERG:** inhibition strongly driven by lipophilicity, and basicity attenuation is the standard
  mitigation.
- **Phospholipidosis:** lipophilic basic molecules, especially cationic amphiphiles, via
  distribution to membranes and lysosomes.
- **Mitochondrial toxicity:** both strongly acidic and basic drugs implicated - strong lipophilic
  acids uncouple oxidative phosphorylation, basic drugs accumulate in acidic mitochondrial spaces
  in liver and pancreas.
- **Acid metabolic liabilities:** O-acyl glucuronides covalently modifying protein via
  transacylation or acyl migration, implicated in ADRs and idiosyncratic toxicity; bioactivation to
  electrophilic acyl-CoA thioesters.
- **Base metabolic liabilities:** piperidines oxidised to reactive iminium species via oxidative
  dehydration; piperazines forming iminium and nitrenium species, and conjugated imine-amide
  intermediates.
- Anilines and anilides associated with mutagenicity, direct toxicity, methemoglobinemia and
  immunogenic allergenic toxicity: **Charifson 2014** text.

The use of these as a **prior on which in vitro transporter studies must exist** is a synthesis
step connecting Charifson 2014 to the in vitro package-integrity requirements in the DDI skill.

## pKa optimisation - Charifson 2014 Tables 3 and 4

- **That pKa modulation "is most often employed retrospectively to 'fix a problem' and less
  frequently employed prospectively as part of the initial SAR exploration"**: **Charifson 2014**.
- That the most common example is attenuating basicity to overcome hERG liability, and that this is
  closely tied to a net reduction in lipophilicity: **Charifson 2014**.
- **That "in almost all of the examples presented in Table 4, there was an effort to reduce the
  basicity of a lead compound"**: **Charifson 2014**. The `SKILL.md` observation that every cited
  example also lowered logD follows from reading the Table 4 entries, where both values are
  reported.
- Worked examples quoted in `SKILL.md`, all **Charifson 2014** Table 4:
  - Met inhibitor GEN-203 (pKa 7.45) → GEN-890 (pKa 5.93) by adding a second 3-position fluorine
    to an aminopiperidine: mouse Vd reduced ~4-fold from 3.6 to 0.99 L/kg, Met cell potency
    maintained, comparable xenograft efficacy, and no detectable liver or bone marrow toxicity up
    to 600 mg/kg for 14 days at comparable or higher exposures.
  - Cathepsin S inhibitors moved into a pKa 6-8 range via a heteroarylmethyl group, reducing
    spleen/plasma ratio **13-fold from 26 to 1.9** while maintaining cell activity - cathepsin S
    residing in acidic lysosomes makes this a direct lysosomal-trapping intervention.
  - Cathepsin C amidoacetonitrile series: basic piperidine → tetrahydropyran, pKa and logD from
    8.4/2.7 to 5.8/1.9, resolving hERG inhibition and phospholipidosis that had translated to in
    vivo liver histology findings.
  - Guanidine-based 5-HT5A antagonist: difluoroethyl side chain lowering pKa 9.9 → 8.9, with up to
    20-fold brain-to-plasma improvement achievable via combined pKa and lipophilicity modulation.
  - H1-antihistamine benzimidazoles: pKa 9.1 → 7.4 via morpholine/thiomorpholine, presumed
    responsible for increased CNS exposure.
  - Zwitterionic renin inhibitors from adding a carboxylic acid to 3-amido-4-aryl piperidines,
    improving CYP3A4 time-dependent inhibition and hERG profiles.
- Approaches for lowering amine pKa - fluorine substitution, oxetanes, β-position carbonyl -
  **Charifson 2014** Figure 12, modified from **Morgenthaler et al.** (their ref 68), whose review
  is the place to go for tuning amine basicity rationally.
- pKa prediction tools, with ChemAxon MarvinSketch's pKa plugin named as useful for predicted
  values and **major microspecies at a given pH**: **Charifson 2014**. Microspecies matter for
  multiprotic compounds and are why `SKILL.md` asks for all pKa values with acid/base assignment.

## Formulation - Charifson 2014 Table 3

- Basic compounds ionised and soluble at gastric pH 1.5, with solubility decreasing rapidly as pH
  rises toward 6.5 in the small intestine, thereby reducing absorption; **solubility in simulated
  intestinal fluid (pH 6.8) can provide insights into oral absorption**: **Charifson 2014**. This
  is the physicochemical mechanism behind the build-workflow skill's biorelevant-media
  requirement, which is sourced there to the PBPK tutorials - the two lines of evidence converge.
- Salt and cocrystal formation raising solubility and dissolution rate and hence bioavailability;
  the salt/cocrystal distinction being whether **proton transfer** occurred; the **"rule of 2"**
  (stable salt generally requires >2 pKa units between acid and base) described as "an
  oversimplification" that nonetheless highlights the real requirement that the conjugate base of
  a weak acid be moderately strong and vice versa; and that **more salts exist for basic drugs
  than acidic drugs**: **Charifson 2014**.
- IV formulations preferably pH 4-8 to minimise pain and tissue damage on injection, potentially
  conflicting with the extreme pH an ionisable poorly soluble drug requires: **Charifson 2014**.

---

## Synthesis steps - claims in SKILL.md attributable to no source

Flagged so they can be challenged directly rather than mistaken for citations.

1. **The propagation diagram** (pKa → fraction ionised → logD → permeability / Kp / solubility /
   fu / Rb → Iin,max,u), and the resulting rule that **one-parameter-at-a-time sensitivity
   analysis is invalid for pKa**. This follows from Charifson 2014's coupling observations
   combined with the PBPK parameter hierarchy in the build-workflow skill, but neither paper
   states it.
2. **The ten arithmetic falsification tests.** Individually derivable from the equations in
   Parkinson 2019 and Charifson 2014, but assembled here. Specifically:
   - fu,b = fu,p/Rb ≤ 1, and the worked counterexample (fu,p 0.8, Rb 0.5 → fu,b 1.6): synthesis.
   - Rb floor at ~1 − haematocrit: follows from Parkinson 2019's statement that 0.55 *is*
     1 − haematocrit for erythrocyte-excluded drugs; the use as a lower bound is synthesis.
   - Iin,max,u > Imax,u: follows from the equation structure; framed here as the check that would
     have caught the published missing-plus-sign error.
   - logD ≤ logP at a pH where the compound ionises: follows from the definitions.
   - Ionization class must be consistent with Rb: combines Parkinson 2019's class expectations
     with Charifson 2014's phosphatidylserine mechanism.
3. **The Henderson-Hasselbalch numerical anchors** (base pKa 6.6 → ~86% un-ionised at pH 7.4;
   pKa 9.0 → ~97.5% ionised) are computed here, not quoted. The pKa 6.6 value is chosen to match
   the worked compound in the build-workflow skill's source tutorial.
4. **The requirement to report DDI conclusions per agency criterion** operationalises
   Parkinson 2019's rhetorical question into a checklist item.
5. **Cross-references** to the DDI, IVIVE, renal impairment and build-workflow skills are
   editorial.

## Caveats carried from the parent bundle

- All content is synthesised in original wording. Attributions point to where an idea came from;
  consult the primary publication for anything you intend to cite.
- **Numeric regulatory criteria change.** Parkinson 2019 describes the FDA 2017 guidance, PMDA
  2018 guidelines and EMA 2012 guideline. Cutoffs, nomenclature and scope have moved since.
  Verify against current guideline text before relying on any threshold - and note that
  Parkinson's central algebraic result (matrix-invariance of the unbound value) is robust to
  guidance revision in a way that the cutoffs are not.
- Charifson 2014's trends are population statistics from public databases, with the
  basicity/lipophilicity confound acknowledged by the authors themselves. They orient; they do
  not parameterise.
