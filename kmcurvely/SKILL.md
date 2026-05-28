# Kmcurvely - Interactive Kaplan-Meier Curves Reference

This reference provides detailed code patterns for creating interactive Kaplan-Meier survival curves using kmcurvely.

## Table of Contents
- [Build Metadata](#build-metadata)
- [Generate KM curves](#generate-km-curves)
  - [In HTML version](#in-html-version)
  - [In RTF version](#in-rtf-version)

## Build Metadata
```r
library(dplyr)
library(metalite)
library(kmcurvely)

# Load kmcurvely example ADaM data.
data(kmcurvely_adsl, package = "kmcurvely")
data(kmcurvely_adtte, package = "kmcurvely")


meta <- meta_adam(
	population = kmcurvely_adsl,
	observation = kmcurvely_adtte
) |>
	define_plan(plan = plan(
		analysis = "interactive_km_curve",
		population = "itt_population",
		observation = "itt_observation",
		parameter = "pfs;male;female"
	)) |>
	define_analysis(
		name = "interactive_km_curve",
		title = "KM curves",
		label = "km curve"
	) |>
	define_population(
		name = "itt_population",
		group = "TRT01P",
		subset = EFFFL == "Y",
		var = c("USUBJID", "TRT01P", "SEX"),
    label = "Intention-to-treat population"
	) |>
	define_observation(
		name = "itt_observation",
		group = "TRTP",
		subset = SAFFL == "Y",
		var = c("USUBJID", "TRTP", "SEX", "PARAMCD", "AVAL", "CNSR"),
		label = "Efficacy population"
	) |>
	define_parameter(
		name = "pfs",
		subset = PARAMCD == "TTDE",
		label = "Time to First Dermatologic Event"
	) |>
	define_parameter(
		name = "male",
		subset = SEX == "M",
		label = "Male"
	) |>
	define_parameter(
		name = "female",
		subset = SEX == "F",
		label = "Female"
	) |>
	meta_build()
```

## Generate KM curves

### In HTML version

```r 
meta |> 
  kmcurvely(population = "itt_population",
            observation = "itt_observation",
            endpoint = "pfs",
            subgroup = "male;female",
            x_label = "Time",
            y_label = "Survival",
            color = c("blue", "red"),
            time_unit = "days")
```

### In RTF version

TODO: Add code for generating static KM curve for RTF version.