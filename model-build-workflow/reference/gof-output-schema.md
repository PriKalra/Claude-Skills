# Goodness-of-fit output schema

Structured output format for Phase B of `pbpk-model-build-workflow`. The purpose of this
schema is separation of concerns: this skill computes and emits **numbers**, a downstream
reporting skill renders **figures and tables**. Keeping the boundary sharp means the same
qualification dataset can be re-plotted, re-tabulated or re-reviewed without re-running the
model, and means a reviewer can recompute any metric from the primitives.

## Contents

1. [File layout](#1-file-layout)
2. [`gof_metadata.json`](#2-gof_metadatajson)
3. [`gof_observations.csv`](#3-gof_observationscsv) - the primitive paired data
4. [`gof_metrics.json`](#4-gof_metricsjson) - computed metrics and verdicts
5. [`vpc_dataset.csv`](#5-vpc_datasetcsv) - the visual predictive check package
6. [Conventions and gotchas](#6-conventions-and-gotchas)
7. [Worked minimal example](#7-worked-minimal-example)

---

## 1. File layout

Write to a single directory per qualification run. Never overwrite a previous run -
qualification history is evidence.

    <model-name>-gof/
    |- run-<YYYY-MM-DD>-<short-label>/
    |  |- gof_metadata.json      # what was run, criteria, provenance
    |  |- gof_observations.csv   # paired predicted/observed primitives
    |  |- gof_metrics.json       # computed metrics + verdicts
    |  \- vpc_dataset.csv        # profile data for plotting
    \- run-<...>/

State the absolute path of the run directory in the response. A dataset the modeller
cannot find has not been delivered.

---

## 2. `gof_metadata.json`

Captures everything needed to interpret the metrics, including the parts that are
judgement rather than arithmetic.

```json
{
  "model_name": "compound-x-pbpk-v3",
  "run_label": "tier1-dose-extrapolation",
  "run_date": "2026-07-30",
  "software": "tool-agnostic; state platform and version if applicable",
  "intended_purpose": "First-in-human dose selection for a 10-200 mg range",
  "acceptance_criteria": {
    "set_by": "modeller",
    "source": "agreed_with_modeller",
    "agreed_before_metrics": true,
    "endpoints": [
      {"endpoint": "AUC",  "metric": "AAFE", "pass_below": 2.0, "marginal_below": 2.5},
      {"endpoint": "Cmax", "metric": "AAFE", "pass_below": 2.0, "marginal_below": 2.5},
      {"endpoint": "t_half", "metric": "AFE", "pass_range": [0.5, 2.0]}
    ]
  },
  "evidence_tier": 1,
  "evidence_tier_rationale": "Dose levels 10 and 200 mg were not used in model build",
  "fitted_parameters": [
    {"name": "OATP1B1 uptake CLint", "value": 282, "units": "uL/min/1e6 cells",
     "method": "top-down fit to Niemi 2004 mean profile"}
  ],
  "predicted_parameters_count": 11,
  "distribution_model": "Rodgers-Rowland",
  "lipophilicity_measured": 3.99,
  "lipophilicity_used": 3.99,
  "gof_lenses_run": ["aggregate", "stratified_by_dose", "bias_vs_precision",
                     "proportion_within_2fold", "signed_error_vs_dose"],
  "gof_lenses_not_run": ["population_coverage", "sensitivity_weighted"],
  "gof_lenses_not_run_reason": "No virtual population simulated for this run"
}
```

Required fields: `model_name`, `run_date`, `intended_purpose`, `acceptance_criteria`,
`evidence_tier`, `distribution_model`, `gof_lenses_run`, `gof_lenses_not_run`.

`acceptance_criteria.source` must be one of `agreed_with_modeller`, `defaulted_convention`,
or `regulatory_guideline`. If `defaulted_convention`, `agreed_before_metrics` is `false` and
the response must say so in prose - a defaulted criterion is a materially weaker claim than
an agreed one, and that difference should not be discoverable only by opening a JSON file.

---

## 3. `gof_observations.csv`

The primitive paired dataset. Every metric in `gof_metrics.json` must be recomputable from
this file alone. One row per predicted/observed pair.

| Column | Type | Required | Notes |
|--------|------|----------|-------|
| `obs_id` | string | yes | Unique within the run |
| `endpoint` | string | yes | `AUC`, `AUC_inf`, `Cmax`, `tmax`, `t_half`, `CL`, `Vss`, `conc`, `AUC_ratio`, ... |
| `predicted` | float | yes | Model value |
| `observed` | float | yes | Digitised or reported value |
| `units` | string | yes | Explicit. Never blank. |
| `study_id` | string | yes | Citation key or internal study number |
| `dose` | float | yes | |
| `dose_units` | string | yes | |
| `route` | string | yes | `iv`, `po`, `sc`, `dermal`, ... |
| `matrix` | string | yes | `plasma`, `blood`, `urine`, `<tissue name>` |
| `sampling_site` | string | yes | `peripheral_vein`, `central_venous`, `arterial`, `unknown` |
| `n_subjects` | int | no | |
| `observed_variability` | float | no | SD, SEM or CI half-width |
| `observed_variability_type` | string | no | `sd`, `sem`, `ci95` |
| `cohort` | string | no | Free-text stratum label used for stratified metrics |
| `species` | string | yes | |
| `population` | string | yes | `healthy_adult`, `neonate`, `ckd_stage_4`, ... |
| `used_in_build` | bool | yes | Drives the evidence tier. Be scrupulous here. |
| `fold_error` | float | derived | `predicted / observed` |
| `log10_fold_error` | float | derived | |

`sampling_site` is required and `unknown` is an acceptable value, because the honest
answer is frequently unknown for digitised literature data - but recording `unknown`
prevents a Cmax discrepancy from being silently attributed to the model when it may be a
compartment mismatch.

`used_in_build` is the field most likely to be filled in carelessly and the one that most
changes what the metrics mean. If it is uncertain for a dataset, mark it `true` - the
conservative direction is to claim less evidence, not more.

---

## 4. `gof_metrics.json`

Computed metrics, at both aggregate and stratified level, plus verdicts.

```json
{
  "aggregate": {
    "n_pairs": 14,
    "by_endpoint": {
      "AUC": {
        "n": 14, "AFE": 1.08, "AAFE": 1.31,
        "GMFE": 1.31,
        "GMFE_formula": "10^(mean(|log10(P/O)|))  [equals AAFE]",
        "RMSE_log10": 0.118, "RMSE_raw": 142.6, "RMSE_pct": 18.1,
        "max_abs_fold_error": 1.79, "max_abs_fold_error_obs_id": "obs-009",
        "proportion_within_2fold": 1.0,
        "verdict": "pass",
        "verdict_basis": "AAFE 1.31 < 2.0; all 14 pairs within twofold; tier 1"
      },
      "Cmax": {
        "n": 14, "AFE": 1.42, "AAFE": 2.14,
        "GMFE": 2.14,
        "GMFE_formula": "10^(mean(|log10(P/O)|))  [equals AAFE]",
        "RMSE_log10": 0.331,
        "max_abs_fold_error": 3.62, "max_abs_fold_error_obs_id": "obs-011",
        "proportion_within_2fold": 0.71,
        "verdict": "fail",
        "verdict_basis": "AAFE 2.14 > 2.0; driven by fed-state cohort (FE 3.62). Fasted-state subset AAFE 1.44."
      }
    }
  },
  "stratified": [
    {"stratum_type": "cohort", "stratum": "fed_state", "endpoint": "Cmax",
     "n": 4, "AFE": 3.11, "AAFE": 3.18, "verdict": "fail"},
    {"stratum_type": "cohort", "stratum": "fasted_state", "endpoint": "Cmax",
     "n": 10, "AFE": 1.03, "AAFE": 1.44, "verdict": "pass"}
  ],
  "covariate_trends": [
    {"covariate": "dose", "endpoint": "Cmax", "trend": "none",
     "note": "signed error flat across 10-200 mg"}
  ],
  "verdict_robustness": [
    {"endpoint": "AUC", "parameter": "hepatic CLint",
     "range_tested": [0.5, 2.0], "verdict_range": ["pass", "fail"],
     "robust": false,
     "note": "verdict flips to fail at 2x CLint, which is inside the IVIVE confidence range"}
  ],
  "overall": {
    "verdict": "fail",
    "summary": "AUC qualifies for dose extrapolation; Cmax does not, because food effect is not represented. Model is fit for AUC-driven dose selection only."
  }
}
```

Rules the schema is designed to enforce:

- `AFE` and `AAFE` always appear together. A consumer that finds one without the other
  should treat the file as incomplete.
- `GMFE_formula` is mandatory whenever `GMFE` is present. The name is used inconsistently in
  the literature - some authors define it as the bias-insensitive geometric mean of
  |fold error| (identical to AAFE), others as the geometric mean of the signed ratio
  (algebraically AFE). Two papers quoting the same GMFE value may not be quoting the same
  quantity. The formula string removes the ambiguity for one line of cost.
- `verdict_basis` is a required non-empty string for every verdict. A verdict without its
  arithmetic is an opinion.
- Any `stratified` entry with `verdict: fail` must be surfaced in prose even when
  `aggregate` passes. Aggregate metrics conceal single-cohort failure, which is the most
  common way a GoF table misleads.
- `overall.verdict` is not a vote count. A model can fail one endpoint and remain fit for a
  purpose that does not depend on it - say which purpose, as the example does.

---

## 5. `vpc_dataset.csv`

Numeric package for plotting. **This skill does not render final report figures.** It emits
this dataset for a reporting skill to consume. If no reporting skill exists in the
environment, write the file, state its path, and stop - do not substitute an improvised
report figure. A quick-look diagnostic plot for the modeller's own review is fine, and
should be labelled diagnostic.

Long format, one row per time point per series.

| Column | Type | Required | Notes |
|--------|------|----------|-------|
| `series_id` | string | yes | Groups rows into one curve |
| `series_type` | string | yes | `predicted_mean`, `predicted_pi_lower`, `predicted_pi_upper`, `observed_mean`, `observed_individual` |
| `study_id` | string | yes | |
| `dose` | float | yes | |
| `dose_units` | string | yes | |
| `route` | string | yes | |
| `matrix` | string | yes | |
| `time` | float | yes | |
| `time_units` | string | yes | |
| `concentration` | float | yes | |
| `conc_units` | string | yes | |
| `variability` | float | no | On observed series |
| `variability_type` | string | no | `sd`, `sem`, `ci95` |
| `pi_percentile` | float | no | e.g. `5` / `95` on PI series |
| `n_subjects` | int | no | |
| `sampling_site` | string | yes | |

Prediction-interval series are only meaningful if a virtual population was simulated. Do
not emit `predicted_pi_*` rows from a single mean-individual simulation - an interval that
represents nothing is worse than no interval, because a reader will interpret it as
population variability.

---

## 6. Conventions and gotchas

- **Log scale is the default for concentration.** Concentration data are multiplicatively
  distributed and span orders of magnitude. Raw-scale RMSE is dominated by the peak and
  effectively ignores the terminal phase. Report both when profiles exist and name the
  scale behind any conclusion.
- **Fold error is undefined when observed is zero or below LLOQ.** Exclude such pairs and
  record the exclusion count and reason. Silent exclusion of BLQ points systematically
  flatters the terminal phase.
- **Digitised literature data carry their own error.** Values read off a published figure
  have a digitisation uncertainty that is rarely smaller than a few percent and is
  sometimes much larger. Where a verdict is `marginal`, digitisation error may exceed the
  margin - say so rather than reporting a decision the data cannot support.
- **Units are never optional.** A units column that is blank in one row has already cost
  more than it would have to fill it in.
- **`n` accompanies every metric.** An AAFE from three pairs is a number, not an estimate.
- **Never overwrite a previous run directory.** Qualification history is evidence, including
  the runs that failed.

---

## 7. Worked minimal example

The smallest complete, valid run - two endpoints, one study, defaulted criteria.

`gof_metadata.json`:

```json
{
  "model_name": "cip-pbpk-v1",
  "run_date": "2026-07-30",
  "intended_purpose": "Confirm IV model before adding oral absorption",
  "acceptance_criteria": {
    "set_by": "not_asked",
    "source": "defaulted_convention",
    "agreed_before_metrics": false,
    "endpoints": [
      {"endpoint": "AUC",  "metric": "AAFE", "pass_below": 2.0},
      {"endpoint": "Cmax", "metric": "AAFE", "pass_below": 2.0}
    ]
  },
  "evidence_tier": 0,
  "evidence_tier_rationale": "Same 200 mg IV dataset used to estimate clearance",
  "distribution_model": "PK-Sim standard",
  "gof_lenses_run": ["aggregate"],
  "gof_lenses_not_run": ["stratified_by_dose", "population_coverage"],
  "gof_lenses_not_run_reason": "Single dose level available at this build step"
}
```

`gof_observations.csv`:

    obs_id,endpoint,predicted,observed,units,study_id,dose,dose_units,route,matrix,sampling_site,species,population,used_in_build,fold_error,log10_fold_error
    obs-001,AUC,12.4,11.8,mg*h/L,davis1994,200,mg,iv,plasma,unknown,human,healthy_adult,true,1.051,0.0216
    obs-002,Cmax,4.10,4.55,mg/L,davis1994,200,mg,iv,plasma,unknown,human,healthy_adult,true,0.901,-0.0453

Because `evidence_tier` is 0 and the criteria were defaulted, the correct reporting of this
run is not "the model passes". It is: *the IV model is internally consistent with the
dataset used to build it (AUC AAFE 1.05, Cmax AAFE 1.11, n=1 pair each, tier 0); no
predictive claim is supported until a dose or dataset outside the build is tested, and no
acceptance criteria were agreed with the modeller.* The schema is built to make that
sentence the natural one to write.
