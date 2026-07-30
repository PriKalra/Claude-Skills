# Evidence log - DDI

Maps framework elements in SKILL.md to sources. "OSP #n" refers to issues in the
Open-Systems-Pharmacology/OSP-based-publications-and-content repository (label: DDI),
each of which carries the publication abstract. Certara entries are publications,
posters and blog articles on certara.com. All descriptions are paraphrased.

## Network qualification methodology (the build order)

- OSP #305, #218, #191, #203 - PBPK DDI qualification-network papers. Source of the
  five-step build order: verify objects standalone, verify perpetrators against index
  objects, couple without re-fitting, validate on independent pairs, then extrapolate.
  Also the source of the twofold / GMFE acceptance convention and of reporting
  per-pair as well as aggregate performance.
- OSP #782, #781, #773, #750 - applied DDI model packages spanning multiple objects and
  perpetrators; support the claim that a qualified network transfers to new pairs while
  a single fitted pair does not.

## Mechanism diversity

- OSP #712, #711, #695, #742 - reversible inhibition and mixed metabolism/transport
  cases.
- OSP #469, #475, #318, #303 - mechanism-based inactivation and induction; source of the
  point that onset/offset timescales are governed by enzyme turnover rather than
  perpetrator half-life.
- OSP #614, #448, #83, #628 - less common mechanisms: autoinhibition, metabolite-mediated
  inhibition, transporter-metabolism interplay, and PD-linked interaction networks.
- Certara publication, A Mechanistic Framework for In Vitro-In Vivo Extrapolation of
  Liver Membrane Transporters: prediction of the rosuvastatin-cyclosporine interaction.
  Supports the interplay principle - hepatic uptake, efflux and metabolism must be
  represented together, and passive versus active permeation must be separated.
- Certara publication, Mechanism-based Inhibition of Cytochrome P450 Enzymes: evaluation
  of early in vitro decision-making approaches and DDI prediction.
- Certara publications comparing algorithms for predicting clinical DDIs from CYP3A4 in
  vitro data, for compounds as objects and as precipitants. Support the graded
  static-to-dynamic escalation and the conservatism of basic static methods.

## Parameter relationships

- The 1/(1-fm) ceiling and its reciprocal use to infer fm from strong-inhibitor data:
  derived from first principles and used consistently across the OSP qualification
  papers above.
- Cmax-versus-AUC diagnostic pattern and the gut wall contribution for CYP3A objects:
  recurring across OSP #712, #711, #695 and the Certara CYP3A4 algorithm comparisons.

## In vitro package integrity

- Certara blog, What are the most common in vitro drug-drug interaction study gaps?
  (Rioux, Heller, Colombo, 2025). Source of the two mandatory corrections: unbound
  fraction in the incubation, without which risk is underestimated; and blood-to-plasma
  ratio for the unbound hepatic inlet concentration used in OATP1B risk assessment. Also
  the source of the assay-quality gap list - solubility in assay media, cytotoxicity,
  recovery, stability, non-specific binding, adequate concentration range, positive
  control sensitivity - and of the metabolite significance thresholds.

## Regulatory framing

- Certara blog, ICH M12 guidelines and your drug-drug interaction package (Gil Berglund,
  Rioux, Rowland Yeo). See reference/regulatory-and-biomarker-addendum.md for the
  detailed change list, nomenclature, biomarker panel and induction criteria.
- Certara blog, Measuring metabolites in index clinical DDI studies (Yu,
  Ragueneau-Majlessi, Gardner, 2025). Source of the guidance on when metabolite
  measurement adds information.

## Population dependence of DDI magnitude

- Certara publication, Age Related Changes in Fractional Elimination Pathways for Drugs
  (Salem, Johnson, Barter, Leader, Rostami-Hodjegan, 2013). Quantitative demonstration
  that fm is age-dependent and therefore DDI magnitude is age-dependent.
- Certara blog, New FDA requirement to support labeling on DDIs in the pediatric
  population. Source of the caffeine example - CYP1A2-dominated in adults, renally
  eliminated in neonates - and of the point that absorption-level DDI potential differs
  in children because of gastric fluid volume relative to dose.
- OSP drug-gene interaction papers in the DDI set: genotype sets baseline fm, and
  inhibitors can phenoconvert.

