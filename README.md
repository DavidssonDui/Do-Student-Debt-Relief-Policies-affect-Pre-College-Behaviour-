# Do Student Debt Relief Policies Affect Pre-College Behaviour?
### Evidence from New York's "Get On Your Feet" Program

![Language](https://img.shields.io/badge/language-Python-blue)
![Method](https://img.shields.io/badge/method-Difference--in--Differences-orange)
![Topic](https://img.shields.io/badge/topic-Education%20Policy-green)
![Status](https://img.shields.io/badge/status-Complete-brightgreen)

A causal-inference study of whether New York State's 2015 student debt relief program shifted the post-graduation plans of NYS high school seniors. Uses a two-way fixed-effects difference-in-differences design on the NYSED Report Card panel covering high schools across New York State (907 schools, 658,678 graduating students, 2014–2017).

---

## Research Question

In 2015, New York launched **"Get On Your Feet"**, a debt relief program that covered up to two years of federal loan payments for graduates of NY high schools who went on to NY colleges. Most prior work on debt relief has focused on borrowing decisions *after* graduation. This project asks a different question:

> Did high school seniors respond to the program *before* graduating — by changing whether and where they enrolled in college?

## Headline Findings

- Using a pre-policy trend-based assignment to the treatment group, schools with high predicted in-state college share saw a **~3 percentage point increase** in 4-year college enrollment and a **~2.5 percentage point decrease** in 2-year college enrollment after the policy.
- Results are consistent with a **substitution effect**: students who would otherwise have enrolled in cheaper 2-year programs appear to shift toward 4-year programs once the financial risk is partly insured by the relief program.
- An alternative interpretation aligned with the *Bennett Hypothesis* (that financial aid raises tuition) is also discussed.
- **Caveat:** identification rests on a proxy for treatment exposure, and the parallel trends assumption is more credible for 4-year enrollment than for 2-year. The paper is transparent about these limitations.

## Methodology

| Stage | Approach |
|---|---|
| Treatment proxy | In-state college share is unobserved during the policy years. Constructed two proxies from pre-2014 NYSED data: (1) a regression of in-state share on school SES characteristics, (2) a pre-policy average above/below the 50% threshold. |
| Identification | Two-way fixed effects difference-in-differences comparing high vs. low predicted in-state-share schools, before vs. after 2015. |
| Estimation | Weighted least squares (weighted by graduating cohort size), with school and year fixed effects. |
| Inference | Robust standard errors clustered at the school level. |
| Robustness | Both intensity (continuous treatment) and dummy (above/below 50%) specifications reported; alternative proxy method used as a check. |

## Data

All data comes from the **New York State Education Department (NYSED) Report Card database**, publicly available at [data.nysed.gov/downloads.php](https://data.nysed.gov/downloads.php). Sections used:

- *High school post-graduation plans of completers* — outcome variables (4-year, 2-year college shares; in-state vs. out-of-state split available pre-2014).
- *Demographic Factors* — SES covariates (free/reduced lunch, LEP, racial composition, economic disadvantage).
- *Staff* and *High School Completers* — school-quality controls and graduate counts used as regression weights.

The analysis covers high schools across New York State, with 2014–2017 as the policy window and 2009–2013 as the pre-policy estimation window.

## Repository Contents

```
.
├── NYSDEBTSTUDENTRELIEF.pdf # Full paper with motivation, methods, results, and references
├── STUDENTSDEBTRELEIFNY2025.ipynb # Python notebook: data cleaning, proxy construction, DiD estimation, figures
└── README.md
```

## Running the Analysis

The notebook is written in Python and uses:

- `pandas`, `numpy`, `scipy` — data wrangling and numerics
- `statsmodels` (both `api` and `formula.api`) — OLS, WLS, fixed-effects regression with clustered standard errors
- `matplotlib`, `seaborn` — trend plots

The notebook is organized as a clean linear pipeline with markdown section headers:

1. Loading the four NYSED datasets (`High_School_Post-Graduation_Plans_of_Completers{year}.csv`, `Demographic_Factors{year}.csv`, `High_School_Completers{year}.csv`, `Staff{year}.csv`) across years and merging into a single school–year panel.
2. Summary statistics by in-state vs. out-of-state college-going intensity.
3. Estimation method 1 — SES regression to predict in-state share (`EligShare`).
4. Estimation method 2 — pre-policy average above/below 50% threshold.
5. Parallel-trends diagnostics for both proxies.
6. WLS difference-in-differences regressions with school and year fixed effects, clustered SEs.

To reproduce, follow these two steps.

**1. Download and convert the NYSED files.** NYSED publishes the Report Card data as Microsoft Access (`.accdb`) databases, one per school year. Download the relevant files for 2009–2017 from the [NYSED downloads page](https://data.nysed.gov/downloads.php), then convert each database into per-table CSVs using [`mdbtools`](https://github.com/mdbtools/mdbtools):

```bash
mdb-tables -1 "SRC2013.accdb" | while IFS= read -r t; do
 echo "Exporting table: $t"
 mdb-export "SRC2013.accdb" "$t" > "${t// /_}.csv"
done
```

This produces one CSV per table inside the database (e.g., `Demographic_Factors.csv`, `High_School_Completers.csv`). Rename each selected CSV to append the school year — for example, `Demographic_Factors.csv` → `Demographic_Factors2013.csv` — so the notebook can load them across years in a loop.

**2. Run the notebook.** Place the renamed CSVs in the same directory as the notebook. The loading cells expect filenames of the form `Demographic_Factors2013.csv`, `High_School_Post-Graduation_Plans_of_Completers2013.csv`, etc.

## Skills Demonstrated

- **Causal inference**: difference-in-differences, two-way fixed effects, proxy construction for unobserved treatment exposure.
- **Applied econometrics**: weighted least squares, clustered inference, parallel-trends diagnostics, robustness checks.
- **Data engineering**: assembling a school–year panel from heterogeneous yearly administrative releases.
- **Honest reporting**: explicit discussion of identification limitations and alternative explanations for the findings.

## Context

This was an independent research project completed for UBC ECON 526 (MA Quantatative Economics with Data Science Applications). All data sourcing, proxy construction, model specification, and analysis are my own work.

## Author

**Dui Davidsson** — [GitHub](https://github.com/DavidssonDui)

---

*Full citations and references are in the [paper](NYSDEBTSTUDENTRELIEF.pdf).*
