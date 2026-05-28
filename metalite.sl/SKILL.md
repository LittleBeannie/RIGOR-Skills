# Metalite.sl - Subject-Level Analysis Reference

This reference provides comprehensive code patterns for subject-level analysis using metalite.sl.

## Table of Contents
- [Baseline Characteristics](#baseline-characteristics)
  - [In HTML version](#in-html-version)
  - [In RTF version](#in-rtf-version)
- [Disposition Analysis](#disposition-analysis)
  - [In HTML version](#in-html-version)
  - [In RTF version](#in-rtf-version)
- [Exposure Duration](#exposure-duration)
  - [In HTML version](#in-html-version)
  - [In RTF version](#in-rtf-version)
- [Treatment Compliance](#treatment-compliance)
  - [In RTF version](#in-rtf-version)

## Baseline Characteristics

### Basic Baseline Table
```r
library(metalite.sl)

# Load example data
data(metalite_sl_adsl, package = "metalite.sl")
data(metalite_sl_adae, package = "metalite.sl")

meta_sl <- meta_adam(
    population = metalite_sl_adsl,
    observation = metalite_sl_adsl
  ) |>
    define_plan(plan = plan(
    analysis = "base_char", population = "apat",
    observation = "apat", parameter = "age;gender;race"
  )) |>
  define_population(
    name = "apat",
    group = "TRTA",
    subset = SAFFL == "Y",
    var = c("USUBJID", "TRTA", "SAFFL", "AGEGR1", "SEX", "RACE")
  ) |>
  define_observation(
    name = "apat",
    group = "TRTA",
    subset = SAFFL == "Y",
    var = c("USUBJID", "TRTA", "SAFFL", "AGEGR1", "SEX", "RACE")
  ) |>
  define_parameter(
    name = "age",
    var = "AGE",
    label = "Age (years)",
    vargroup = "AGEGR1"
  ) |>
  define_parameter(
    name = "gender",
    var = "SEX",
    label = "Gender"
  ) |>
  define_parameter(
    name = "race",
    var = "RACE",
    label = "Race"
  ) |>
  define_analysis(
    name = "base_char",
    title = "Participant Baseline Characteristics",
    label = "base_char"
  ) |>
  meta_build()

  meta_ae <- meta_adam(
    population = metalite_sl_adsl,
    observation = metalite_sl_adae
    ) |>
    define_plan(plan = plan(
      analysis = "ae_specific", population = "apat",
      observation = "wk12",
      parameter = c("any", "ser")
    )) |>
    define_population(
      name = "apat",
      group = "TRTA",
      subset = SAFFL == "Y"
    ) |>
    define_observation(
      name = "wk12",
      group = "TRTA",
      subset = SAFFL == "Y",
      label = "Weeks 0 to 12"
    ) |>
    define_parameter(
      name = "any",
      subset = NULL,
      var = "AEDECOD",
      soc = "AEBODSYS",
      label = "Any Adverse Events"
    ) |>
    define_parameter(
      name = "ser",
      subset = AESER == "Y",
      var = "AEDECOD",
      soc = "AEBODSYS",
      label = "Serious Adverse Events"
    ) |>
    define_analysis(
      name = "ae_specific",
      title = "Summary of Adverse Events"
    ) |>
    meta_build()
```
### In HTML version

The following code generates an interactive baseline characteristics table with drill down to AE specific tables for serious AE.
```r
react_base_char(
  metadata_sl = meta_sl,
  metadata_ae = meta_ae,
  ae_subgroup = c("age", "race", "gender"),
  ae_specific = "ser",
  width = 1200
)
```

### In RTF version
```r
meta_sl |> 
  prepare_base_char(parameter = "age;gender;race") |>
  format_base_char(
    display_col = c("n", "prop", "total"),
    digits_prop = 1,
    display_stat = c("mean", "sd", "se", "median", "q1 to q3", "range")) |>
  rtf_base_char(
    source = "Source: [CDISCpilot: adam-adsl]",
    path_outdata = tempfile(fileext = ".Rdata"),
    path_outtable = "outtable/base0char.rtf"
  )

```
## Disposition Analysis

### Build metadata
```r
meta_disposition <- meta_adam(
  population = metalite_sl_adsl,
  observation = metalite_sl_adsl
) |>
  define_plan(plan = plan(
    analysis = "disposition", population = "apat",
    observation = "apat", 
    parameter = "disposition;medical-disposition"
)) |>
  define_population(
    name = "apat",
    group = "TRTA",
    subset = SAFFL == "Y"
  ) |>
  define_parameter(
    name = "disposition",
    var = "EOSSTT",
    label = "Trial Disposition",
    var_lower = "DCSREAS"
  ) |>
  define_parameter(
    name = "medical-disposition",
    var = "EOTSTT",
    label = "Participant Study Medication Disposition",
    var_lower = "DCTREAS"
  ) |>
  define_analysis(
    name = "disposition",
    title = "Disposition of Participant",
    label = "disposition table"
  ) |>
  meta_build()
```
### In HTML version

In the HTML version, if there are a reason called "adverse event" in the trial disposition or medication disposition, the disposition table will have a drill down to show the AE details for those participants with "adverse event" as the reason.

To enable the above drill down, we build a metadata for AE.

```r
meta_ae <- meta_adam(
    population = metalite_sl_adsl,
    observation = metalite_sl_adae
    ) |>
    define_plan(plan = plan(
      analysis = "ae_specific", population = "apat",
      observation = "wk12",
      parameter = c("any", "ser")
    )) |>
    define_population(
      name = "apat",
      group = "TRTA",
      subset = SAFFL == "Y"
    ) |>
    define_observation(
      name = "wk12",
      group = "TRTA",
      subset = SAFFL == "Y",
      label = "Weeks 0 to 12"
    ) |>
    define_parameter(
      name = "any",
      subset = NULL,
      var = "AEDECOD",
      soc = "AEBODSYS",
      label = "Any Adverse Events"
    ) |>
    define_parameter(
      name = "ser",
      subset = AESER == "Y",
      var = "AEDECOD",
      soc = "AEBODSYS",
      label = "Serious Adverse Events"
    ) |>
    define_analysis(
      name = "ae_specific",
      title = "Summary of Adverse Events"
    ) |>
    meta_build()
```

```r
react_disposition(
  metadata_sl = meta_disposition,
  metadata_ae = metadata_ae,
  analysis = "disposition",
  trtvar = metalite::collect_adam_mapping(meta_disposition, population)$group,
  population = meta_disposition$plan$population[meta_disposition$plan$analysis == analysis],
  sl_parameter = paste(meta_disposition$plan$parameter[meta_disposition$plan$analysis == analysis],
    collapse = ";"),
  sl_col_selected = c("siteid", "subjid", "sex", "age", "weightbl"),
  sl_col_names = c("Site", "Subject ID", "Sex", "Age (Year)", "Weight (kg)"),
  ae_observation = "wk12",
  ae_population = population,
  ae_parameter = "any",
  ae_col_selected = c("AESOC", "ASTDT", "AENDT", "AETERM", "duration", "AESEV", "AESER",
    "related", "AEACN", "AEOUT"),
  ae_col_names = c("SOC", "Onset Date", "End Date", "AE", "Duraion", "Intensity",
    "Serious", "Related", "Action Taken", "Outcome"),
  display_total = TRUE,
  width = 1200
)
```


### In RTF version

```r
meta_disposition |> 
  prepare_disposition(
    analysis = "disposition",
    population = meta_disposition$plan[meta_disposition$plan$analysis == analysis, ]$population,
    parameter = paste(meta_disposition$plan[meta_disposition$plan$analysis == analysis, ]$parameter, collapse =
    ";")
  ) |>
  format_disposition(
    display_col = c("n", "prop", "total"),
    digits_prop = 1,
    display_stat = c("mean", "sd", "se", "median", "q1 to q3", "range")) |>
  rtf_disposition(
    source = "Source: [CDISCpilot: adam-adsl]",
    path_outdata = tempfile(fileext = ".Rdata"),
    path_outtable = "outtable/disposition.rtf"
  ) |>
  rtf_disposition(
    "Source: [CDISCpilot: adam-adsl]",
    path_outdata = tempfile(fileext = ".Rdata"),
    path_outtable = "outtable/disposition.rtf"
  )
```
## Exposure Duration

### Build metadata
```r
meta_exposure <- meta_adam(
  population = adexsum,
  observation = adexsum
) |>
  define_plan(plan(
    analysis = "exp_dur", population = "apat",
    observation = "apat", parameter = "expdur"
  )) |>
  define_population(
    name = "apat",
    group = "TRTA",
    subset = APERIOD == 1 & AVAL > 0
  ) |>
  define_parameter(
    name = "expdur",
    var = "AVAL",
    label = "Exposure Duration (Days)",
    vargroup = "EXDURGR"
  ) |>
  define_analysis(
    name = "exp_dur",
    title = "Summary of Exposure Duration",
    label = "exposure duration table"
  ) |>
  meta_build()
```
### In HTML version

```r
meta_exposure |> 
  prepare_exp_duration(
  analysis = "exp_dur",
  population = meta_exposure$plan[meta_exposure$plan$analysis == analysis, ]$population,
  parameter = paste(meta_exposure$plan[meta_exposure$plan$analysis == analysis, ]$parameter, collapse =
    ";")
) |>
extend_exp_duration() |>
plotly_exp_duration(
  display = c("n", "prop"),
  display_total = TRUE,
  display_standard_histogram = TRUE,
  standard_histogram_label =
    "Comparison of Exposure Duration (> = x days) by Treatment Groups",
  display_stacked_histogram = TRUE,
  stacked_histogram_label =
    "Comparison of Exposure Duration (> = x days and < y days) by Treatment Groups",
  display_horizontal_histogram = TRUE,
  horizontal_histogram_label = "Comparison by Exposure Duration (> = x days)",
  plot_group_label = "Treatment group",
  plot_category_label = "Exposure duration",
  hover_summary_var = c("n", "median", "sd", "se", "median", "min", "max", "q1 to q3",
    "range"),
  width = 1000,
  height = 400
)
```
### In RTF version
```r
meta_exposure |> 
  prepare_exp_duration() |>
  format_exp_duration(
    display_col = c("n", "prop", "total")) |>
  rtf_exp_duration(
    source = "Source: [CDISCpilot: adexsum]",
    path_outdata = tempfile(fileext = ".Rdata"),
    path_outtable = "outtable/exposure_duration.rtf"
  )
```
## Treatment Compliance

### Build metadata
```r
meta <- meta_adam(
  population = adsl,
  observation = adsl
) |>
  define_plan(plan = plan) |>
  define_population(
    name = "apat",
    group = "TRTA",
    subset = quote(SAFFL == "Y"),
    var = c("USUBJID", "TRTA", "SAFFL", "CMPLPCT", "CMPLRNG")
  ) |>
  metalite::define_parameter(
    name = "CMPLPCT",
    var = "CMPLPCT",
    label = "Treatment Compliance Percent",
  ) |>
  metalite::define_parameter(
    name = "CMPLRNG",
    var = "CMPLRNG",
    label = "Treatment Compliance Range",
  ) |>
  define_analysis(
    name = "trt_compliance",
    title = "Summary of Treatment Compliance",
    label = "treatment compliance table"
  ) |>
  meta_build()
```

### In RTF version
```r
meta |>
  prepare_trt_compliance() |>
  format_trt_compliance() |>
  rtf_trt_compliance(
    source = "Source: [CDISCpilot: adsl]",
    path_outdata = tempfile(fileext = ".Rdata"),
    path_outtable = "outtable/treatment_compliance.rtf"
  )
```
