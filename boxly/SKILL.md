# Boxly - Interactive Box Plots Reference

This reference provides detailed code patterns for creating interactive box plots using boxly.

## Table of Contents
- [Build Metadata](#build-metadata)
- [Generate Interactive Box Plots](#generate-interactive-box-plots)


## Build Metadata

```r
library(metalite)
library(boxly)

data(boxly_adsl, package = "boxly")
data(boxly_adlb, package = "boxly")

meta <- meta_adam(
  population = boxly_adsl,
  observation = boxly_adlb
  ) |>
  # define plan with parameters of interest
  define_plan(
    plan(
      analysis = "boxly",
      label = "Interactive Box Plot",
      # The parameters are commonly a set of PARAMCD values in the observation dataset
      parameter = "ALP;ALT;AST;BILI;SODIUM",
      population = "apat",
      observation = "wk12")
  ) |>
  # define the population whose key words `population = "apat"` is mentioned in the `plan` above
  define_population(
    name = "apat",
    group = "TRT01A",
    subset = ITTFL == "Y",
    label = "All Participants as Planned",
    var = c("USUBJID", "TRT01A", "ITTFL", "SEX", "RACE", "AGE", "AGEU")
  ) |>
  # define the observation whose key words `observation = "wk12"` is mentioned in the `plan` above
  define_observation(
    name = "wk12",
    group = "TRTA",
    subset = SAFFL == "Y",
    label = "Weeks 0 to 12",
    var = c("USUBJID", "TRTA", "SAFFL", "PARAMCD", "AVAL", "CHG", "AVISITN")
  ) |>
  # define the analysis whose key words `analysis = "boxly"` is mentioned in the `plan` above
  # the variable used in `x` and `y` must be included in the `var` argument of the `define_observation()` or `define_population()` above
  define_analysis(
      name = "boxly",
      label = "Interactive Box Plot",
      x = "AVISITN",
      y = "CHG"
  ) |>
  # define all the parameters (seperated by ";") mentioned in the `plan(parameters = ...)`, the variable used in `subset` must be included in the `var` argument of the `define_observation()` or `define_population()` above
  define_parameter(
        name = "ALP",
        label = boxly_adlb |> dplyr::filter(PARAMCD == "ALP") |> dplyr::pull(PARAM) |> unique(),
        subset = PARAMCD == "ALP"
  ) |>
  define_parameter(
        name = "ALT",
        label = boxly_adlb |> dplyr::filter(PARAMCD == "ALT") |> dplyr::pull(PARAM) |> unique(),
        subset = PARAMCD == "ALT"
      ) |>
  define_parameter(
        name = "AST",
        label = boxly_adlb |> dplyr::filter(PARAMCD == "AST") |> dplyr::pull(PARAM) |> unique(),
        subset = PARAMCD == "AST"
      ) |>
  define_parameter(
        name = "BILI",
        label = boxly_adlb |> dplyr::filter(PARAMCD == "BILI") |> dplyr::pull(PARAM) |> unique(),
        subset = PARAMCD == "BILI") |>
  define_parameter(
        name = "SODIUM",
        label = boxly_adlb |> dplyr::filter(PARAMCD == "SODIUM") |> dplyr::pull(PARAM) |> unique(),
        subset = PARAMCD == "SODIUM") |>
  meta_build()
```

```r
meta |> 
  prepare_boxly(
    population = "apat",
    observation = "wk12",
    analysis = "boxly",
    filter_var = "PARAM",
    # A character vector of hover variables for outlier.
    # customization of hover label can be found at https://github.com/Merck/boxly/blob/main/vignettes/hover-label.Rmd
    hover_var_outlier = c("USUBJID", 
                          metalite::collect_adam_mapping(meta, analysis)$y
                          )
    ) |>
  boxly(x_label = "Visit",
        y_label = "Change",
        heading_select_list = "Lab parameter",
        heading_summary_table = "Number of Participants",
        hover_summary_var = c("n", "min", "q1", "median", "mean", "q3", "max"),
        hover_outlier_label = c("Participant ID", "AVAL")
  )
```