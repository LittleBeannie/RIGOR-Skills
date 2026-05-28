
# Metalite Framework Reference

This reference provides detailed code patterns for setting up the metalite framework for clinical trial analysis.

## Table of Contents
- [Readin Data](#readin-data)
- [Analysis Plans](#analysis-plans)
- [Population Definition](#population-definition)
- [Observation Definition](#observation-definition)
- [Parameter Definition](#parameter-definition)

## Readin Data

```r
library(metalite)

# Load example datasets
data(r2rtf_adsl, package = "r2rtf")
data(r2rtf_adae, package = "r2rtf")

# Create basic metalite object
meta <- meta_adam(
  population = r2rtf_adsl,
  observation = r2rtf_adae
)
```

## Analysis Plans

### Defining the First Analysis Plans
```r
plan <- plan(
  analysis = "ae_summary",
  population = "apat",
  observation = "wk12",
  parameter = "any;rel"
)
```

### Defining the Plans After the First Plans
```r
plan <- plan |>
  add_plan(
    analysis = "ae_specific", population = "apat",
    observation = "wk12",
    parameter = c("any", "rel")
  )
```

### Feed in the Plans to the Metadata
```r
meta <- meta |> define_plan(plan)
```

## Population Definition

We use the function `define_population` to define the keywords of `plan(population = ...)` or `add_plan(population = ...)`.
One important note is, the grouping variable, i.e., `define_population(group = ...)` should be a factorized before readin the dataset.
```r
meta <- meta |>
  define_population(
    name = "apat",
    group = "TRT01A",
    subset = ITTFL == "Y",
    label = "All Participants as Planned",
    var = c("USUBJID", "TRT01A", "ITTFL", "SEX", "RACE", "AGE", "AGEU")
  )
```

## Observation Definition

We use the function `define_population` to define the keywords of `plan(observation = ...)` or `add_plan(observation = ...)`.
One important note is, the grouping variable, i.e., `define_observation(group = ...)` should be a factorized before readin the dataset.
```r
meta <- meta |>
  define_observation(
    name = "wk12",
    group = "TRTA",
    subset = SAFFL == "Y",
    label = "Weeks 0 to 12",
    var = c("USUBJID", "TRTA", "SAFFL", "AEREL", "AEDECOD", "AEBODSYS")
  )

```

## Parameter Definition

We use the function `define_parameter()` to define all the keyword of `plan(paramter = ...)` or `add_plan(parameter = ...)`.
If there is only 1 value in `plan(paramter = ...)` or `add_plan(parameter = ...)`, then check if there are ";" in this value. 
Users are required to define all keywords seperated by ";" by `define_parameter()`.
If there are multiple values in `plan(paramter = ...)` or `add_plan(parameter = ...)`, define them one by on `define_parameter()`.
```r
meta <- meta |>
  define_parameter(
    name = "any",
    subset = NULL,
    label = "Any AEs",
    var = "AEDECOD", soc = "AEBODSYS"
  ) |>
  define_parameter(
    name = "rel",
    subset = toupper(AEREL) %in% c("PROBABLE", "POSSIBLE"),
    label = "Drug-related AEs",
    var = "AEDECOD", soc = "AEBODSYS"
  ) 
```

## Build the Entire Metadata
```r
meta <- meta |> meta_build()
```