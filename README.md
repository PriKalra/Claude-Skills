# Claude Skills — PBPK, mechanistic modelling and MIDD regulatory assessment

Ten tool-agnostic agent skills for conducting and auditing physiologically-based pharmacokinetic
(PBPK) and mechanistic modelling work, plus one for assessing that work against the ICH M15
regulatory framework. Each is written to be actionable rather than descriptive: every skill states
first principles, a build order, arithmetic tests that can falsify a model without new data, the
single assumption that most often silently breaks that class of model, and a verification checklist.

Nothing here depends on a particular simulator.

## Installation

Each directory is a self-contained skill with YAML frontmatter in its `SKILL.md`. To use with
Claude Code, copy the ones you want into your skills directory:

```bash
git clone https://github.com/PriKalra/Claude-Skills.git
cp -r Claude-Skills/model-build-workflow ~/.claude/skills/
```

Or copy the whole set:

```bash
for d in Claude-Skills/*/; do cp -r "$d" ~/.claude/skills/; done
```

Skills load on trigger — the `description` in each frontmatter defines when. They can equally be
read as standalone documents; the reasoning is the point, not the packaging.

## Structure

    Claude-Skills/
    |- README.md
    |
    |  # Core workflow ------------------------------------------------------
    |- model-build-workflow/          <- start here for a new model
    |  |- SKILL.md
    |  \- reference/
    |     |- evidence-log.md
    |     |- quick-checklist.md
    |     \- gof-output-schema.md     <- qualification output format for the whole suite
    |
    |  # Input layer --------------------------------------------------------
    |- ionization-and-binding-inputs/
    |  |- SKILL.md
    |  \- reference/
    |     |- evidence-log.md
    |     \- quick-checklist.md
    |- ivive-assessment/
    |  |- SKILL.md
    |  \- reference/
    |     |- evidence-log.md
    |     |- quick-checklist.md
    |     \- scaling-integrity-addendum.md
    |
    |  # Applications -------------------------------------------------------
    |- ddi-assessment/
    |  |- SKILL.md
    |  \- reference/
    |     |- evidence-log.md
    |     |- quick-checklist.md
    |     \- regulatory-and-biomarker-addendum.md
    |- renal-impairment-assessment/
    |  |- SKILL.md
    |  \- reference/
    |     |- evidence-log.md
    |     \- quick-checklist.md
    |
    |  # Populations --------------------------------------------------------
    |- pediatric-allometric-scaling/
    |  |- SKILL.md
    |  \- reference/
    |     |- evidence-log.md
    |     \- quick-checklist.md
    |- neonatal-pbpk/
    |  |- SKILL.md
    |  \- reference/
    |     |- evidence-log.md
    |     \- quick-checklist.md
    |
    |  # Routes and modalities ----------------------------------------------
    |- dermal-absorption/
    |  |- SKILL.md
    |  \- reference/
    |     |- evidence-log.md
    |     \- quick-checklist.md
    |- protac-degrader-pkpd/
    |  |- SKILL.md
    |  \- reference/
    |     |- evidence-log.md
    |     \- quick-checklist.md
    |
    |  # Regulatory assessment -----------------------------------------------
    \- ich-m15-regulatory-assessment/
       \- SKILL.md

## What each skill owns

| Skill | Owns | Go elsewhere for |
|---|---|---|
| **model-build-workflow** | Build order, system-vs-drug parameter discipline, distribution-model (Kp) choice, IV-then-oral sequencing, PD coupling, sensitivity analysis, **and the qualification apparatus (Phase A evidence tiers, Phase B goodness-of-fit metrics, output schema) used by every other skill** | Any specific application |
| **ionization-and-binding-inputs** | pKa, logP vs logD, fu,p vs fu,b, blood-to-plasma ratio, unbound hepatic inlet concentration, FDA/EMA/PMDA divergence | Scaling in vitro clearance → IVIVE |
| **ivive-assessment** | The scaling chain from in vitro measurement to in vivo clearance, permeability and transport; under-prediction diagnosis | Physicochemical inputs → ionization skill |
| **ddi-assessment** | Interaction magnitude, mechanism classification, network qualification, study-waiver arguments | The hepatic inlet arithmetic itself → ionization skill |
| **renal-impairment-assessment** | CKD, AKI, dialysis, renal dysfunction as a systemic perturbation | Neonatal renal immaturity → neonatal skill |
| **pediatric-allometric-scaling** | Allometric exponent discipline, dose-band translation, scaling across the full 0–18 range | The first weeks of life → neonatal skill |
| **neonatal-pbpk** | Preterm vs term, GA/PNA/PMA clocks, ontogeny, glomerulo-tubular imbalance, critical-care overlays | Allometry → paediatric skill; skin → dermal skill |
| **dermal-absorption** | The skin route **at any age** — SC/epidermis/dermis diffusion, IVPT, flux, skin maturation | Neonatal systemic physiology → neonatal skill |
| **protac-degrader-pkpd** | Targeted protein degraders — event-driven pharmacology, DC50/Dmax, protein turnover, hook effect, tissue-selective degradation | Conventional inhibitors → build-workflow and DDI skills |
| **ich-m15-regulatory-assessment** | Whether a *finished* analysis carries enough evidence for the regulatory decision it is being asked to support — question of interest, context of use, model influence × consequence-of-wrong-decision risk grading, MAP/MAR documentation | Building or qualifying the model itself → the nine skills above |

Three skills deliberately overlap on paediatrics (**pediatric-allometric-scaling**,
**neonatal-pbpk**, **dermal-absorption**) and their descriptions carry explicit mutual-exclusion
clauses so they do not compete for triggering. If you are unsure which applies: allometry and dose
bands → paediatric; the first weeks of life → neonatal; skin at any age → dermal.

The M15 skill sits at a right angle to the other nine: they build and qualify a model, it assesses
whether the result is sufficient for a stated regulatory purpose. It does not build models, and it
never asserts that a regulator will accept an analysis.

## How the file types are meant to be used

- **SKILL.md** — the reasoning framework. Read it when starting a piece of work or when a
  prediction and an observation disagree. Each one carries YAML frontmatter (name, description) so
  it can be loaded as a skill.
- **reference/quick-checklist.md** — the live working checklist. Run it during model building and
  again before reporting.
- **reference/evidence-log.md** — provenance. Every non-obvious claim in the SKILL.md is traced to
  the paper or resource it came from, so you can go to the primary source, disagree with the
  synthesis, or update it as the literature moves. **Each log also flags its own synthesis steps** —
  claims that follow from the sources but are asserted by no paper — so those can be challenged
  directly rather than mistaken for citations.
- **reference/*-addendum.md** — topic-specific material sitting behind a skill (regulatory criteria,
  biomarker panels, in vitro package integrity, scaling-chain integrity).
- **reference/gof-output-schema.md** — the qualification output format. Lives once, in
  model-build-workflow, and is referenced by the rest rather than duplicated.

## Design principles behind these skills

1. **Tool-agnostic.** Nothing here depends on a particular simulator. Where a source used a
   specific platform, the transferable principle was extracted and the platform dropped.
2. **Mechanism before number.** Every skill forces classification of the mechanism before any value
   is quoted, because misclassification is the one error that fitting cannot repair.
3. **Arithmetic falsification.** Each skill contains internal-consistency tests — the fm ceiling,
   the fe ceiling, the filtration ratio, half-life versus CL and V, the Kp-implied Vss, fu,b ≤ 1,
   the protein-turnover Dmax ceiling, flux versus SC thickness — that let a human falsify a model
   with a calculator.
4. **Named assumptions.** Each skill identifies the single assumption that most often silently
   breaks that class of model, and the diagnostic fingerprint of its failure.
5. **Criteria before metrics.** Acceptance criteria are **asked of the modeller and tied to intended
   purpose before any metric is computed**, and their provenance is recorded. The twofold /
   AAFE < 2 convention is treated as a default to be argued away from, not as a scientific
   constant. Criteria chosen after the metrics are seen are a rationalisation, and a reviewer will
   read them that way.
6. **Agency.** Each skill ends with a reporting pattern — value, mechanism split, dominant
   sensitivity, falsifying observation — designed so the human reading the output can act, disagree,
   or design the next experiment.
7. **NAMs where NAMs apply.** Where a non-animal method is the scientifically appropriate route —
   most explicitly in the dermal skill, where human and paediatric skin samples are scarce and the
   comparator is often *no study* rather than a better one — that framing is stated rather than left
   implicit.

## Provenance and caveats

**The original four skills** (DDI, IVIVE, renal impairment, paediatric scaling) were built from publication sourcebodies

**The five added skills** were each built from named primary literature, cited in full in their
evidence logs:

| Skill | Primary sources |
|---|---|
| model-build-workflow | Jones & Rowland-Yeo 2013 (*CPT:PSP* 2:e63); Kuepfer et al. 2016 (*CPT:PSP* 5:516–531) |
| ionization-and-binding-inputs | Parkinson 2019 (*DMD* 47:779–784); Charifson & Walters 2014 (*J Med Chem*) |
| neonatal-pbpk | Zhang et al. 2023 (*Pharmaceutics* 15:2765) |
| dermal-absorption | Yun, Calderon-Nieva, Hamadeh & Edginton 2022 (*Pharmaceutics* 14:172), adapting the Dancik et al. permeation model |
| protac-degrader-pkpd | Watt, Scott-Stevens & Gaohua 2019 (*Drug Discov Today: Technol*) |

**The M15 skill** is built on the ICH M15 guideline *General Principles for Model-Informed Drug
Development*, Step 4, adopted 29 January 2026, and on ASME V&V 40 as applied to drug models. It is
a reading of a public guideline, not a substitute for it — verify any criterion against the
current guideline text.

All content is synthesised in original wording. Numeric criteria quoted from regulatory guidance are
paraphrased summaries of secondary commentary and change over time — verify against the current
guideline text before relying on any threshold, cut-off or study-design requirement. Attributions in
the evidence logs point to where an idea came from; consult the primary publication for detail and
for anything you intend to cite.

**Known limits, stated rather than buried:**

- **The Phase B goodness-of-fit metric set is convention, not citation.** Neither PBPK build
  tutorial defines AFE, AAFE or GMFE; those come from general qualification practice and from the
  requirements of the modeller who commissioned the workflow skill. AAFE < 2 has regulatory currency
  but no single authoritative derivation. **GMFE is genuinely ambiguous in the literature** — defined
  by some authors as the bias-insensitive geometric mean of absolute fold error (identical to AAFE)
  and by others as the geometric mean of the signed ratio (identical to AFE) — which is why the
  suite requires the formula to be printed next to any GMFE value.
- **The PROTAC skill's source is from 2019**, when no mathematical PK/PD model of in vivo degrader
  activity had been reported. That field has moved substantially since. Its hook-effect and
  ternary-complex material is domain knowledge rather than sourced from that paper, and is flagged in
  the evidence log as the skill's largest citation gap.
- **The dermal skill's paediatric evidence base is three compounds and about a dozen infant skin
  samples**, and its source authors' own assessment is that the results "provide only a small amount
  of evidence" that the maturation changes are correct. Preterm skin physiology was not
  systematically reviewed, and the sub-3-month stratum corneum dataset was explicitly excluded from
  the final maturation function.
- **The neonatal skill excludes placental and lactational transfer, and neonatal DDI**, because its
  source systematic review excluded them. Its search closed 7 September 2023.
- **Coverage of the original four:** IVIVE was read exhaustively within its label; DDI, renal
  impairment and paediatric coverage combined a full scan of titles with deep reading of a
  mechanistically diverse subset. The frameworks were selected for recurrence across independent
  drugs and populations rather than for novelty in any single paper.
- **The M15 skill has no evidence log**, unlike the nine PBPK skills. Its single source is the
  guideline text itself.
- **The M15 skill's routing table references sibling skills that are not published here**
  (`midd-product-manager`, `qsp-thales-project-orchestrator`, `systems-pharmacology-scientist`).
  Those rows tell you when M15 assessment is the *wrong* frame; they do not need the named skill to
  be installed to be useful.

## Note on the paediatric retrofit

The four original skills were authored with the twofold acceptance criterion asserted directly.
They have been amended so that acceptance criteria are negotiated with the modeller against intended
purpose, and so that they defer to `model-build-workflow` for metric definitions and the output
schema rather than restating them. The scientific content of those four skills is otherwise
unchanged from the original authoring.
