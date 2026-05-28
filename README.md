# RIGOR-Skills

AI skills for R Interactive Generation of Report (RIGOR).

This repository is a collection of AI skill references, not an R package. Each folder contains a `SKILL.md` file with reusable guidance and R code patterns that an AI coding assistant can use when helping generate clinical reporting workflows.

## Skill Catalog

| Skill | Focus |
| --- | --- |
| `metalite` | Core `metalite` metadata setup for clinical trial analysis. |
| `metalite.ae` | Adverse event summary and AE-specific analysis patterns. |
| `metalite.sl` | Subject-level analysis patterns, including baseline characteristics, disposition, exposure, and treatment compliance. |
| `forestly` | Interactive and static adverse event forest plot workflows. |
| `boxly` | Interactive box plot workflows. |
| `kmcurvely` | Interactive and static Kaplan-Meier curve workflows for time-to-event analysis. |

## Repository Structure

```text
RIGOR-Skills/
|-- README.md
|-- boxly/
|   `-- SKILL.md
|-- forestly/
|   `-- SKILL.md
|-- kmcurvely/
|   `-- SKILL.md
|-- metalite/
|   `-- SKILL.md
|-- metalite.ae/
|   `-- SKILL.md
`-- metalite.sl/
    `-- SKILL.md
```

## How To Use

Open or reference the relevant skill folder when asking an AI assistant to generate R reporting code. The assistant should use the corresponding `SKILL.md` file as the source of reusable patterns, examples, package calls, and expected workflow structure.

For example:

- Use `metalite/SKILL.md` when building metadata with `meta_adam()`, `define_plan()`, `define_population()`, `define_observation()`, and `define_parameter()`.
- Use `metalite.ae/SKILL.md` for AE summaries and AE-specific tables.
- Use `metalite.sl/SKILL.md` for subject-level reporting outputs.
- Use `forestly/SKILL.md`, `boxly/SKILL.md`, or `kmcurvely/SKILL.md` when the task requires the corresponding visualization workflow.

## Runtime Dependencies

The examples reference R packages used by the reporting workflows, including:

- `metalite`
- `metalite.ae`
- `metalite.sl`
- `forestly`
- `boxly`
- `kmcurvely`
- Supporting packages such as `dplyr`, `r2rtf`, `survival`, `ggplot2`, and `plotly` where required by the examples

Install the needed R packages in the target R environment before running the snippets from a skill file.

## Maintaining Skills

When updating a skill:

- Keep each `SKILL.md` focused on one package or workflow area.
- Prefer concise, runnable R examples over broad prose.
- Keep metadata-building examples aligned with the package vignettes and current function signatures.
- Update the table of contents when adding or renaming sections.
- Validate new R snippets when possible before committing changes.
