# Metalite.ae - Adverse Event Analysis Reference

This reference provides detailed code patterns for adverse event analysis using metalite.ae.

## Table of Contents
- [Build Metadata](#build-metadata)
- [AE Summary Analysis](#ae-summary-analysis)
  - [In HTML version](#in-html-version)
  - [In RTF version](#in-rtf-version)
- [AE Specific Analysis](#ae-specific-analysis)
  - [In HTML version](#in-html-version)
  - [In RTF version](#in-rtf-version)

## Build Metadata

Following is an example using `metalite` to build metadata. 
Refer to the code pattern at metalite.md for more details.

```r
library(metalite)

data(r2rtf_adsl, package = "r2rtf")
data(r2rtf_adae, package = "r2rtf")

meta <- meta_adam(
  population = r2rtf_adsl,
  observation = r2rtf_adae) |>
  # define plan
  define_plan(plan(
    analysis = "ae_specific",
    population = "apat",
    observation = "wk12",
    parameter = "rel")) |>
  define_population(
    name = "apat",
    group = "TRT01A",
    subset = ITTFL == "Y",
    label = "All Participants as Planned",
    var = c("USUBJID", "TRT01A", "ITTFL", "SEX", "RACE", "AGE", "AGEU")
  ) |>
  define_observation(
    name = "wk12",
    group = "TRTA",
    subset = SAFFL == "Y",
    label = "Weeks 0 to 12",
    var = c("USUBJID", "TRTA", "SAFFL", "AEREL", "AEDECOD", "AEBODSYS")
  ) |>
  define_parameter(
    name = "rel",
    subset = toupper(AEREL) %in% c("PROBABLE", "POSSIBLE"),
    label = "Drug-related AEs",
    var = "AEDECOD", soc = "AEBODSYS"
  )  |> meta_build()
```

## AE Summary Analysis

### In HTML version
```r
library(metalite.ae)

meta |> prepare_ae_summary(
  population = "apat",
  observation = "wk12",
  parameter = "any;rel;ser") |>
  format_ae_summary() |>
  gt_ae_summary(
    analysis = "ae_summary",
    source = "Source:  [CDISCpilot: adam-adsl; adae]"
  )
```
### In RTF version
```r
library(metalite.ae)

meta |> prepare_ae_summary(
    population = "apat", # Select population by keywords
    observation = "wk12", # Select observation by keywords
    parameter = "any;rel" # Select AE terms by keywords
  ) |>
  format_ae_summary() |>
  tlf_ae_summary(
    source = "Source:  [CDISCpilot: adam-adsl; adae]", 
    path_outtable = "ae0summary.rtf" 
  )
```

## AE Specific Table
The AE specific analysis provide the patients with a certain type of AE. 
For example, an AE specic analysis table can be:
- Patients with any AE
- Patients with drug-related AE
- Patients with serious AE
- Patients with grade 3-5 AE
- Patients with AE os special interest

The following code generate an AE specific analysis table of drug-related AE. 
Please note that there is no interactive HTML version of the AE specific analysi, we only provide the statistic RTF version.

Following is an example using `metalite` to build metadata. 
Refer to the code pattern at metalite.md for more details.
```r
library(metalite)

data(r2rtf_adsl, package = "r2rtf")
data(r2rtf_adae, package = "r2rtf")

meta <- meta_adam(
  population = r2rtf_adsl,
  observation = r2rtf_adae) |>
  # define plan
  define_plan(plan(
    analysis = "ae_specific",
    population = "apat",
    observation = "wk12",
    parameter = "rel")) |>
  define_population(
    name = "apat",
    group = "TRT01A",
    subset = ITTFL == "Y",
    label = "All Participants as Planned",
    var = c("USUBJID", "TRT01A", "ITTFL", "SEX", "RACE", "AGE", "AGEU")
  ) |>
  define_observation(
    name = "wk12",
    group = "TRTA",
    subset = SAFFL == "Y",
    label = "Weeks 0 to 12",
    var = c("USUBJID", "TRTA", "SAFFL", "AEREL", "AEDECOD", "AEBODSYS")
  ) |>
  define_parameter(
    name = "rel",
    subset = toupper(AEREL) %in% c("PROBABLE", "POSSIBLE"),
    label = "Drug-related AEs",
    var = "AEDECOD", soc = "AEBODSYS"
  )  |> meta_build()
```

### In RTF version
```r
meta |> 
  prepare_ae_specific(
    population = "apat",
    observation = "wk12",
    parameter = "rel"
  ) |>
  format_ae_specific() |>
  tlf_ae_specific(
    source = "Source:  [CDISCpilot: adam-adsl; adae]",
    meddra_version = "24.0",
    path_outdata = tempfile(fileext = ".Rdata"),
    path_outtable = tempfile(fileext = ".rtf")
  )
```