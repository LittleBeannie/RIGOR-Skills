# Forestly - Interactive Forest Plots Reference

This reference provides detailed code patterns for creating interactive forest plots using forestly.

## Table of Contents
- [Build Metadata](#build-metadata)
- [AE Forest Plot](#ae-forest-plot)
  - [In HTML version](#in-html-version)
    - [Customize AE listing columns](#customize-ae-listing-columns)
    - [Customize decimal precision](#customize-decimal-precision)
    - [Customize colors](#customize-colors)
    - [Customize AE-specific columns](#customize-ae-specific-columns)
    - [Customize risk difference label](#customize-risk-difference-label)
    - [Customize toggle buttons](#customize-toggle-buttons)
    - [Customize column widths](#customize-column-widths)
    - [Display only SOC](#display-only-soc)
    - [Customize plot limits](#customize-plot-limits)
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
    analysis = "ae_forest",
    population = "apat",
    observation = "wk12",
    parameter = "any;rel")) |>
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
    name = "any",
    subset = NULL,
    label = "All AEs",
    var = "AEDECOD", soc = "AEBODSYS"
  ) |>
  define_parameter(
    name = "rel",
    subset = toupper(AEREL) %in% c("PROBABLE", "POSSIBLE"),
    label = "Drug-related AEs",
    var = "AEDECOD", soc = "AEBODSYS"
  )  |> meta_build()
```

## AE Forest Plot

### In HTML version

In the interactive AE forest plot, it can display multiple AE specific tables. 
The following example displayed two AE specific tables, i.e., Patients with any AE, and Patients with drug-related AE.
```r
library(forestly)

meta |> 
  prepare_ae_forestly() |>
  format_ae_forestly() |>
  ae_forestly()
```

#### Customize AE listing columns

Reference vignette: [Selecting Columns to Display in AE Listings](https://merck.github.io/forestly/articles/customize-listing-columns.html)

Use `ae_listing_display` in `prepare_ae_forestly()` to choose the subject-level columns displayed in the AE drill-down listing.

```r
meta |>
  prepare_ae_forestly(
    ae_listing_display = c("USUBJID", "SITEID", "SEX", "RACE", "AGE")
  ) |>
  format_ae_forestly() |>
  ae_forestly()
```

#### Customize decimal precision

Reference vignette: [Adjusting Decimal Precision in Displayed Data](https://merck.github.io/forestly/articles/customize-digits.html)

Use `digits` in `format_ae_forestly()` to control the number of decimal places shown in the AE-specific tables.

```r
meta |>
  prepare_ae_forestly() |>
  format_ae_forestly(digits = 2) |>
  ae_forestly()
```

#### Customize colors

Reference vignette: [Personalized Color Choices in Data Visualization](https://merck.github.io/forestly/articles/customize-color.html)

Use `color` in `format_ae_forestly()` to set the plotting colors for treatment groups. Provide enough colors for all displayed groups.

```r
meta |>
  prepare_ae_forestly() |>
  format_ae_forestly(color = c("black", "grey60", "grey40")) |>
  ae_forestly()
```

#### Customize AE-specific columns

Reference vignette: [Add/Hide Columns in the AE-Specific Tables](https://merck.github.io/forestly/articles/customize-ae-specific-columns.html)

Use `display` in `format_ae_forestly()` to choose which columns appear in the AE-specific tables.

Available values include `"n"`, `"prop"`, `"diff"`, `"fig_prop"`, `"fig_diff"`, and `"total"`.

```r
meta |>
  prepare_ae_forestly() |>
  format_ae_forestly(
    display = c("n", "prop", "fig_prop", "fig_diff", "total")
  ) |>
  ae_forestly()
```

#### Customize risk difference label

Reference vignette: [Legend Customization in AE Proportion Difference Visualizations](https://merck.github.io/forestly/articles/customize-diff-label.html)

Use `diff_label` in `format_ae_forestly()` to customize the label shown below the risk difference visualization.

```r
meta |>
  prepare_ae_forestly() |>
  format_ae_forestly(diff_label = "New Drug <- Favor -> SoC") |>
  ae_forestly()
```

#### Customize toggle buttons

Reference vignette: [Toggle Risk Difference Columns in the AE-Specific Tables](https://merck.github.io/forestly/articles/customize-toggle-buttons.html)

Use `display_diff_toggle` in `ae_forestly()` to add a button that shows or hides risk difference columns.

```r
meta |>
  prepare_ae_forestly() |>
  format_ae_forestly(
    display = c("n", "prop", "fig_prop", "fig_diff", "diff")
  ) |>
  ae_forestly(
    display_diff_toggle = TRUE,
    display_soc_toggle = TRUE
  )
```

#### Customize column widths

Reference vignette: [Adjusting Column Widths for Optimal Layout](https://merck.github.io/forestly/articles/customize-width.html)

Use width arguments in `format_ae_forestly()` to adjust the AE term, numeric summary, figure, and footer spacing in the AE-specific tables.

```r
meta |>
  prepare_ae_forestly() |>
  format_ae_forestly(
    width_term = 180,
    width_n = 50,
    width_prop = 70,
    width_fig = 360,
    footer_space = 100
  ) |>
  ae_forestly()
```

#### Display only SOC

Reference vignette: [Display Only SOC in the AE-Specific Tables](https://merck.github.io/forestly/articles/customize-display-only-soc.html)

Use `components = "soc"` in `prepare_ae_forestly()` to display only system organ class terms in the AE-specific tables.

```r
meta |>
  prepare_ae_forestly(components = "soc") |>
  format_ae_forestly() |>
  ae_forestly()
```

#### Customize plot limits

Reference vignette: [Customizing Plot Limits for AE Proportions and Differences](https://merck.github.io/forestly/articles/customize-xlimit.html)

Use `prop_range` and `diff_range` in `format_ae_forestly()` to control the plotting limits for AE proportions and risk differences.

```r
meta |>
  prepare_ae_forestly() |>
  format_ae_forestly(
    prop_range = c(-0.5, 30),
    diff_range = c(-10, 35)
  ) |>
  ae_forestly()
```

### In RTF version

The static version of AE forest plot can only display one AE specific table.
The following example displayed Patient with any AE.
```r
meta <- meta |>
  prepare_ae_forestly() |>
  format_ae_forestly()

meta_any <- meta$tbl |> dplyr::filter(parameter == "any")

meta_any |>
  dplyr::select(name, prop_1, prop_2) |>
  plot_dot(
    y_var = "name",
    label = c("Treatment", "Placebo")
  )
```