---
name: pbpk-ionization-and-binding-inputs
description: Get the ionization and binding inputs of a PBPK or static DDI model right - pKa, ionization state, logP versus logD, fraction unbound in plasma versus blood (fu,p vs fu,b), blood-to-plasma ratio (Rb / B:P), and the unbound maximum hepatic inlet concentration (Iin,max,u / [I]u,inlet,max / Iinlet,max,u). Covers Henderson-Hasselbalch arithmetic, pH-partition theory and its exceptions, how acid/base/zwitterion class drives solubility, clearance route, tissue and lysosomal distribution and erythrocyte partitioning, when Rb interconversion is invalid, and the FDA/EMA/PMDA divergence in matrix, nomenclature and cutoff. Tool-agnostic. Use when computing an unbound hepatic inlet concentration, converting fu,p to fu,b, reconciling a DDI cutoff between agencies, choosing or tuning a pKa or logD input, explaining why a basic compound accumulates in tissue, sanity-checking a blood-to-plasma ratio, or auditing the physicochemical input block of any model. Also triggers on "ionisation", "unbound fraction", "fup vs fub", "hepatic inlet", "B:P ratio", "pKa optimisation".
---

# Ionization and binding inputs

## When to use this skill

Any time a number entering a model depends on what fraction of the molecule is charged, or
on what fraction is free, or on which matrix a concentration was measured in. This is the
input layer beneath almost every other skill in this suite: the DDI skill needs an unbound
hepatic inlet concentration, the IVIVE skill needs an incubational unbound fraction, the
build-workflow skill needs a pKa and a logD before it can predict a single Kp, and the
renal skill needs fu × GFR. Errors here are unusually damaging because they are quiet -
they do not break a simulation, they shift it.

The output is never a bare unbound concentration. It is a value, the matrix it refers to,
the assumptions embedded in the conversion, and whether those assumptions are valid for
this compound.

## First principle: ionization is not one input, it is a multiplier on almost all of them

A pKa change does not perturb one parameter. It propagates:

    pKa  →  fraction ionised at each local pH
             →  logD (and therefore effective lipophilicity)
                →  membrane permeability
                →  tissue partition coefficients (Kp), via ionised-species terms
             →  pH-dependent solubility along the GI tract
             →  plasma protein binding (fu,p)
             →  erythrocyte partitioning (Rb), and hence fu,b
                →  unbound hepatic inlet concentration, and every DDI cutoff built on it
             →  lysosomal and acidic-organelle trapping, and hence tissue Vss

Two consequences that matter operationally:

**One-parameter-at-a-time sensitivity analysis is misleading for pKa.** Perturbing pKa while
holding logD, solubility, fu and Kp fixed simulates a compound that cannot exist. If pKa is
uncertain, the honest analysis re-derives the dependent parameters at each pKa value, or
states plainly that the sensitivity shown is a lower bound on the true uncertainty.

**Basicity and lipophilicity are hard to separate, and the literature mostly has not
separated them.** Reducing amine basicity almost always reduces logD at the same time, so
published trends attributed to "basicity" are frequently confounded with lipophilicity.
Treat class-level trends as orientation, not as parameter values, and never as a substitute
for measurement.

## The arithmetic, written out

**Henderson-Hasselbalch.** For a weak acid:

    pH − pKa = log( [ionised] / [un-ionised] )

For a weak base, the ratio inverts:

    pH − pKa = log( [un-ionised] / [ionised] )

At pH = pKa the species is 50% ionised, and each pH unit away shifts the ratio tenfold.
Useful anchors to carry in your head: a base with pKa 6.6 is roughly 86% un-ionised at
pH 7.4; a base with pKa 9.0 is roughly 97.5% ionised; the same base is essentially fully
ionised at gastric pH 1.5. That last one is why basic compounds dissolve in the stomach and
then risk precipitating as they transit into the small intestine at pH ~6.5 - a solubility
trajectory that aqueous solubility at a single pH cannot represent.

**logP versus logD.** logP is the partition of the *un-ionised* species alone. logD at a
stated pH includes the ionised species, and because octanol carries a little water, logD
formally sums all ionised species in *both* phases. They are not interchangeable, and a
model input labelled only "lipophilicity" is ambiguous. State which, and at what pH.

**Unbound fraction conversion.** The relationship that governs everything downstream:

    C_B = C_P × Rb          (concentration converts by multiplying)
    fu,b = fu,p / Rb        (unbound fraction converts by dividing)

where Rb (also written B:P) is the blood-to-plasma concentration ratio, C_B/C_P. The
opposite directions of these two conversions is not a curiosity - it is the mechanism behind
the central result of this skill, below.

## The unbound maximum hepatic inlet concentration

This is the in vivo concentration used to assess inhibition of hepatic uptake transporters
(OATP1B1, OATP1B3, and OCT1 for the EMA) in the basic static models, and inhibition of
hepatic P450s in the mechanistic static model. It is also the single most error-prone
calculation in regulatory DDI assessment, and the errors are in the published guidance and
literature as well as in submissions.

**The underlying model.** The hepatic inlet concentration is the sum of two concentrations -
drug arriving from the systemic circulation, and drug arriving from the gut via the portal
vein. In blood:

    Iin,max,B = Imax,B + (Dose × Fa × Fg × ka) / QH

**Watch the Fa redefinition.** The original formulation defined Fa as the fraction of oral
dose reaching the hepatic portal vein. All three agencies replaced that single term with the
product Fa × Fg, redefining Fa as the fraction absorbed from the gut *lumen* and Fg as the
fraction passing through the gut *wall* into portal blood. So the original Fa is identical to
the agencies' Fa × Fg. The split is a genuine improvement because it carries mechanism -
Fa·Fg = 0.5 could be complete absorption with 50% gut-wall extraction, or 50% absorption
with no gut-wall loss, and those imply different things - but the two conventions coexist in
the literature and mixing them double-counts or drops a term.

**Default values, all of which are conventions rather than measurements:**

- ka = 0.1 min⁻¹ unless determined experimentally.
- Fa × Fg = 1 unless determined experimentally (complete absorption, no gut-wall loss).
- QH = 97 L/h = 1.62 L/min for a 70 kg person, used by all three agencies.
- fu,p has a **lower limit of 0.01** for the basic static models, by all three agencies. If
  your measured fu,p is below 0.01, you use 0.01. This makes the assessment deliberately
  conservative, and "correcting" it to the measured value is not a refinement, it is a
  departure from the guidance.

Note one unexplained choice worth knowing you are inheriting: the portal term uses *total*
hepatic blood flow rather than portal blood flow, which is roughly 80% of total. There is no
stated mechanistic reason for this.

**The FDA form (plasma).** Because concentration converts by multiplying by Rb, dividing the
blood equation through by Rb puts the systemic term in plasma but leaves Rb in the
denominator of the portal term:

    Plasma Iin,max = Plasma Imax + (Dose × Fa × Fg × ka) / (QH × Rb)

    Plasma Iin,max,u = fu,p × Plasma Iin,max

**The EMA / PMDA form (blood).**

    Blood Iin,max,u = fu,b × ( Imax,B + (Dose × Fa × Fg × ka) / QH )   ,  fu,b = fu,p / Rb

**And now the result that makes this tractable.** *The unbound maximum hepatic inlet
concentration is numerically identical in blood and in plasma, for a given Rb.* The reason is
exactly the opposing conversions above: to go from plasma to blood you multiply concentration
by Rb, and to go from plasma to blood you divide unbound fraction by Rb. The two cancel.

This is worth internalising because it removes an entire category of anxiety. You do not need
to worry about whether a submission wants blood or plasma *for the unbound hepatic inlet
concentration* - compute it either way and you get the same number. Use it as a check: if
your two routes disagree, you have made an arithmetic error, not discovered a subtlety.

**What does still differ:**

- **Total (bound + unbound) Iin,max differs between blood and plasma** unless Rb = 1. Only the
  *unbound* value is matrix-invariant. Quoting a total hepatic inlet concentration without
  its matrix is meaningless.
- **The cutoffs differ, and this is where a negative can become a positive.** FDA and PMDA use
  1 + Iin,max,u/(Ki or IC50) ≥ 1.1. The EMA uses 25 × [I]u,inlet,max/(Ki or IC50) ≥ 1, which
  rearranges to the same (1 + ratio) form with a cutoff of 1.04. **The EMA is therefore more
  conservative than the other two agencies**, and a compound can be negative for hepatic
  uptake transporter inhibition under FDA/PMDA criteria and positive under EMA criteria - not
  because of matrix, and not because of arithmetic, but because of the threshold. If you have
  concluded "no interaction" you must say under whose criterion.

**Nomenclature, since all three agencies use different symbols for the same quantity:**

| Agency | Total | Unbound | Matrix |
|--------|-------|---------|--------|
| FDA | Iin,max | Iin,max,u | Plasma |
| EMA | [I]inlet,max | [I]u,inlet,max | Blood |
| PMDA | Iinlet,max | Iinlet,max,u | Blood |

The EMA and PMDA equations are identical; only the symbols differ. All three intend the same
physical quantity, which the guidance documents also call [I]h.

**Known defects in the source documents - check rather than copy:**

- A PMDA draft revision defined Cmax as a *plasma* concentration while the portal term was in
  blood, making the resulting hepatic inlet concentration neither one nor the other. The final
  version corrected this by redefining Cmax as a blood concentration. If you are working from
  an older document, verify which definition is in force.
- The FDA's equation for [I]h retained the original single Fa while its equation for Iin,max,u
  uses Fa × Fg - even though [I]h and Iin,max,u are the same quantity. The Fa × Fg form is the
  one to use for both.
- A widely cited multi-stakeholder publication on static DDI models printed the unbound hepatic
  inlet equation with a **missing plus sign**. An equation from a respectable source is not
  self-validating; dimensional-check it.
- The EMA's blood equation is correct only if the portal term is divided by QH *before* being
  added to Imax,B. The guidance omits the brackets that would make this unambiguous.

The general lesson, and it is the reason this section is long: this is a small piece of
arithmetic that three agencies, one consensus publication and at least one guidance revision
have all managed to render ambiguously. Derive it, do not copy it.

## Blood-to-plasma ratio: rules of thumb, and when they stop being safe

Expected behaviour by ionization class:

- **Non-ionised and basic drugs** tend to distribute evenly between plasma and erythrocytes,
  with **Rb near 1**.
- **Zwitterionic and acidic drugs** tend to be excluded from erythrocytes, with **Rb around
  0.55**, which is simply 1 − haematocrit - i.e. the drug occupies the plasma fraction of
  blood and nothing more.

Basic drugs partition into red cells partly by electrostatic attraction to the negatively
charged phosphatidylserine of the erythrocyte membrane, with lipophilicity contributing. So Rb
is not an arbitrary measured constant; it is predictable in direction from ionization class,
which makes it checkable.

**Outliers are large and they matter.** Some drugs have Rb far above 1 - values averaging
around 15 and ranging from 4 to over 100 for one immunosuppressant, and 30 to 240 for a group
of diuretics that bind with high affinity to erythrocyte carbonic anhydrase.

## The assumption that most often silently breaks a model

**Using Rb to interconvert blood and plasma quantities without establishing that erythrocyte
partitioning is reversible.** The conversions C_B = C_P × Rb and fu,b = fu,p / Rb assume the
drug moves freely between plasma and red cells. When erythrocyte binding is high-affinity and
*restrictive*, the drug in red cells is not available to the liver, to a transporter, or to an
enzyme - and the concentration that governs interaction potential is the **plasma**
concentration, not a blood concentration derived from it.

The two behaviours are distinguishable and the distinction is empirical, not assumable:

- **Reversible high partitioning.** Extensive erythrocyte binding that does not restrict
  hepatic clearance. The diagnostic is that plasma clearance equals blood clearance × Rb,
  which holds. Plasma clearance may then exceed hepatic plasma flow, and even exceed hepatic
  blood flow, without anything being wrong - because the plasma clearance of a drug that lives
  mostly in red cells is a partly fictitious quantity.
- **Restrictive erythrocyte binding.** High Rb arising from tight binding to an intracellular
  erythrocyte protein, where the drug does not move out freely. Here Rb-based interconversion
  of concentration, of protein binding, and of clearance is **all invalid**.

Operational rule: **whenever Rb is well above 1, establish that partitioning is readily
reversible before using Rb for any conversion.** If you cannot, report the plasma-based
assessment and say why the blood-based one was not used. This is a case where the more
conservative choice is also the more mechanistically defensible one, which does not happen
often.

Related and worth carrying: any equation containing an organ **blood flow** term requires
**blood** concentrations. This is the same error that produces misuse of the well-stirred
hepatic clearance model, and it is the reason the hepatic inlet equations are formulated in
blood before being converted.

## What ionization class predicts about the rest of the model

These are orientation trends from large public datasets, not parameter values. Their use is to
tell you when a measured or predicted input is surprising and should be re-checked.

**Solubility.** All ionised classes - acids, bases and zwitterions - are generally more soluble
than neutral compounds, with lipophilicity also contributing strongly. Datasets disagree on
whether acids or bases have the edge, which is itself informative: the acid-versus-base
distinction is weaker than the ionised-versus-neutral one. Do not carry a class-level
solubility expectation into a model in place of a biorelevant measurement.

**Hepatic clearance.** Acids generally show *lower* hepatic clearance than other ionization
classes, consistent with their characteristically high plasma protein binding. Higher hepatic
clearance among bases and neutrals is associated with higher lipophilicity - again, the
class effect and the lipophilicity effect are entangled.

**Renal clearance.** The more polar ionised species - acids, bases and zwitterions - generally
show *higher* renal clearance than neutrals, and renally cleared compounds tend to be of low
lipophilicity overall. If a model assigns substantial renal clearance to a lipophilic neutral
compound, that is worth a second look. Cross-check with the fu × GFR test in the renal
impairment skill.

**Tissue distribution - the mechanistically important one.** Acidic drugs tend to be highly
bound to plasma protein and to reside predominantly in tissue *extracellular water*, with
distribution to adjacent tissue depending on acid strength. Lipophilic basic drugs behave
completely differently: they are stored in tissues rich in acidic phospholipids and in acidic
cellular organelles - liver, lung, kidney - where they become protonated and therefore
sequestered. This is **lysosomal trapping / lysosomotropism**, and it extends to mitochondria,
endosomes and Golgi.

This is exactly the mechanism that the ionised-species terms in the Rodgers-Rowland and Schmitt
distribution models exist to represent, and it is why distribution-model choice matters most
for lipophilic bases. A perfusion-limited, neutral-molecule treatment of a lipophilic base will
under-predict tissue Vss and will mislocate the drug. See the build-workflow skill on the
Vss consistency check.

**Erythrocyte partitioning.** As above - a direct consequence of ionization class, and the
input to Rb.

**Promiscuity and off-target risk.** Basic compounds tend toward lower selectivity, with
lipophilicity contributing. Relevant to a model insofar as unexpected off-target pharmacology
is more likely to exist and to need representing.

**Transporter interaction propensity, which feeds the DDI skill directly.** OATP inhibitors tend
to be strong acids, particularly carboxylates, with lipophilicity and hydrogen bond acceptor
count contributing. OCT inhibition is driven by lipophilicity and positive charge. BSEP
substrates are monovalent anions, and positively charged compounds are negatively correlated
with BSEP inhibition. So ionization class is a prior on *which* transporter interactions to
characterise - a strongly acidic lipophilic carboxylate without OATP inhibition data has a gap
in its in vitro package.

**Metabolic liabilities by class.** Carboxylic acids can form O-acyl glucuronides that covalently
modify protein by transacylation or acyl migration, and can be bioactivated to acyl-CoA
thioesters. Cyclic amines - piperidines, piperazines - can be oxidised to reactive iminium and
nitrenium species. These are safety observations rather than PK parameters, but they determine
whether metabolite characterisation is needed, which the DDI skill requires for metabolites
above its significance thresholds.

## Exceptions to pH-partition theory that are load-bearing

pH-partition theory - that the un-ionised species preferentially crosses membranes by passive
diffusion - describes the large majority of cases and is the foundation of every pH-dependent
absorption model. It is not absolute, and each exception has modelling consequences:

- **Ionised species do permeate**, in small amounts. A model that permits zero permeability for
  the charged form will under-predict absorption for a compound that is almost entirely ionised
  at intestinal pH.
- **Zwitterions permeate**, and by mechanisms outside the theory. Zwitterionic fluoroquinolones
  are thought to cross membranes in antiparallel stacked arrangements that cancel electrostatic
  potential, effectively presenting as neutral species. A permeability prediction based on
  fraction un-ionised will be badly wrong for such compounds.
- **Carrier-mediated uptake may dominate.** There is evidence that many small molecules enter
  cells substantially by carrier-mediated transport. Where that is true, ionization influences
  permeability through *carrier selectivity* rather than through passive diffusion of the
  neutral form - a completely different dependence, and one that a physicochemistry-driven
  permeability estimate cannot capture.
- **Gastric absorption is theoretically possible but rare in practice.** Acids are largely
  un-ionised at gastric pH, but clear examples of gastric-only absorption are unusual. Do not
  build a stomach absorption compartment as the primary route on ionization grounds alone.
- **Most non-neutral cellular compartments are acidic**, which is what makes basic trapping
  common and acidic trapping rare.

## pKa as a design variable, not just an input

Worth knowing because it explains why compound series behave the way they do, and because it is
where modelling can influence chemistry rather than only describe it. pKa is most often modulated
*retrospectively to fix a problem* and much less often explored prospectively as part of initial
SAR - which is itself an opportunity.

The dominant move in practice is **attenuating basicity**, and the documented consequences are
consistent enough to be predictive: lowering an amine pKa reduces Vd, reduces tissue and organ
sequestration, improves CNS penetration ratios, reduces hERG affinity and phospholipidosis
risk, and can improve selectivity. Reported examples include a ~4-fold Vd reduction and loss of
liver and bone marrow toxicity from adding a single fluorine; a 13-fold reduction in
spleen-to-plasma ratio by moving a piperidine pKa into the 6-8 range; and substantial
brain-to-plasma improvements from ~1 unit of pKa reduction.

The honest caveat: every one of these also lowered logD. Whether the effect is "basicity" or
"lipophilicity" is generally not resolvable from the reported data, and treating them as
separable is a modelling error, not just a semantic one.

For the model, the actionable point is that a pKa change in a series is *not* a
single-parameter change, and a PBPK model asked to explain a series should re-derive the whole
dependent parameter block rather than perturbing one input.

## Formulation consequences that reach the absorption model

- Basic compounds are ionised and soluble at gastric pH ~1.5, then lose solubility rapidly as
  pH rises toward ~6.5 in the small intestine. Solubility in simulated intestinal fluid
  (pH ~6.8) is the more informative measurement for oral absorption, and this is the
  physicochemical basis for the build-workflow skill's insistence on biorelevant media.
- Salts and cocrystals raise dissolution rate and bioavailability; the distinction between them
  is whether proton transfer occurred. The "rule of 2" - a stable salt generally requires more
  than 2 pKa units between drug and counterion - is an oversimplification but captures the real
  requirement that the conjugate partner be sufficiently strong. More salt forms exist for basic
  drugs than acidic ones.
- IV formulations are preferably pH 4-8 to limit pain and tissue damage, which can conflict with
  the extreme pH an ionisable poorly soluble drug needs for adequate solubility.

## Parameter relationships that must hold internally

Arithmetic checks, no simulation required.

1. **fu,b = fu,p / Rb must be ≤ 1.** If it is not, fu,p or Rb is wrong. A compound with
   fu,p = 0.8 and Rb = 0.5 implies fu,b = 1.6, which is impossible. This catches transcription
   and unit errors immediately.
2. **Rb should not fall below about 1 − haematocrit (~0.55)** for a drug merely excluded from
   erythrocytes, since that is the plasma fraction of blood. A materially lower value implies a
   measurement problem or a non-standard haematocrit.
3. **Unbound hepatic inlet concentration computed via plasma and via blood must agree.** They
   are algebraically identical. Disagreement is an error.
4. **Total hepatic inlet concentration in blood and plasma must differ unless Rb = 1.** If they
   agree at Rb ≠ 1, a conversion was skipped.
5. **Iin,max,u must exceed Imax,u.** The hepatic inlet adds a portal term to systemic exposure;
   it cannot be lower. A model where it is has a sign or bracket error - which is precisely the
   published missing-plus-sign failure.
6. **Ionization class must be consistent with Rb.** An acid with Rb of 1.5, or a lipophilic base
   with Rb of 0.55, contradicts the mechanism and should be investigated before use.
7. **fu,p below 0.01 must be floored at 0.01** for basic static model assessments.
8. **Fraction ionised must be consistent with pKa and local pH** by Henderson-Hasselbalch, at
   *every* pH the model uses - gastric, intestinal segments, plasma, lysosomal. A single
   "fraction ionised" input is a red flag.
9. **CL_P = CL_B × Rb.** Where blood clearance exceeds hepatic blood flow, hepatic elimination
   alone cannot account for it. Where plasma clearance exceeds hepatic plasma flow, check
   whether Rb is high and partitioning reversible before concluding anything.
10. **logD ≤ logP for an ionisable compound at a pH where it ionises.** Ionisation reduces
    apparent partitioning; a logD above logP indicates mislabelled inputs.

## Model qualification

Use the Phase A / Phase B framework in `pbpk-model-build-workflow`, including agreeing
acceptance criteria with the modeller before computing metrics. Metric formulas and the output
schema live there.

Endpoints specific to this layer: predicted versus measured fu,p; predicted versus measured Rb;
predicted versus measured pH-solubility profile across the GI pH range, not at one pH; predicted
versus measured logD; and where a static DDI assessment is the deliverable, the ratio
Iin,max,u/(Ki or IC50) reported **against each agency's cutoff separately** rather than as a
single pass/fail.

Strata worth separating: by ionization class, by pKa band, and by whether an input was measured
or predicted.

## Verification checklist

1. pKa values reported with their acid/base assignment, all of them, and their source
   (experimental or calculated).
2. Lipophilicity labelled as logP or logD, with the pH for logD.
3. Fraction ionised computed at every pH the model uses, not once.
4. fu,p source stated - measured or predicted - and the matrix and species named.
5. Rb reported, with its source, and checked against the ionization-class expectation.
6. fu,b derived as fu,p/Rb and checked to be ≤ 1.
7. **Where Rb is well above 1: evidence that erythrocyte partitioning is readily reversible,
   before any Rb-based interconversion.** If absent, plasma-based assessment used and the reason
   stated.
8. Any equation containing an organ blood flow term uses blood concentrations.
9. Hepatic inlet equation derived, not copied; brackets explicit; dimensionally checked.
10. Fa convention stated - agencies' Fa × Fg, or the original combined Fa - and not mixed.
11. ka, Fa × Fg and QH values stated as measured or as the default convention.
12. fu,p floored at 0.01 for basic static model assessments.
13. Iin,max,u computed both ways and confirmed identical.
14. Iin,max,u confirmed greater than Imax,u.
15. Total versus unbound hepatic inlet concentrations never quoted without their matrix.
16. DDI conclusion stated **per agency criterion**, with the cutoff quoted - EMA's effective
    1.04 is more conservative than FDA/PMDA's 1.1, and a negative under one may be positive
    under another.
17. OATP time-dependent inhibition addressed - current FDA and PMDA positions ask for
    evaluation with and without preincubation of the test drug with the test system.
18. Static model type named precisely; note that "static mechanistic model", "mechanistic
    static model" and "MSPK model" are the same thing, and that induction assessment is not
    combined with reversible and irreversible inhibition assessment.
19. Distribution model chosen with ionised-species terms if the compound is a lipophilic base;
    lysosomal trapping considered for tissue Vss.
20. Transporter in vitro package checked against ionization-class priors - strong acidic
    carboxylates without OATP data, cations without OCT data, anions without BSEP data.
21. Class-level trends used only as plausibility checks, never as substitutes for measured
    inputs, with the basicity/lipophilicity confound acknowledged.
22. Where pKa is uncertain, dependent parameters re-derived per pKa value rather than perturbed
    one at a time - or the sensitivity explicitly labelled as a lower bound.
23. Guidance-derived numeric criteria verified against current guideline text, not quoted from a
    secondary summary.

## How to report so that the reader gains agency

Give the value, the matrix, the conversion used and its validity condition, and the agency
criterion it was judged against. For a DDI conclusion, give the ratio and the cutoff, both
agencies' cutoffs where they differ, and the parameter the ratio is most sensitive to -
usually fu,p or Rb, both of which are frequently predicted rather than measured. A reader who
can see that the conclusion rests on a predicted Rb of 1.0 can ask for the measurement. A
reader given only "no clinically relevant interaction predicted" cannot.
