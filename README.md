# Nonparametric Statistical Analysis of NYC Hospital Discharge Data (SPARCS 2022)

A graduate-level applied statistics project applying **ten distribution-free hypothesis tests** to **368,495 hospital discharge records** from New York State's SPARCS database, comparing inpatient utilization between Kings County (Brooklyn) and Queens County.

> **Course:** AMAT 581 — Nonparametric Statistics, Spring 2026
> **Institution:** Department of Data Science, University at Albany, SUNY
> **Author:** Vinay Siddi

## Why this project matters

Hospital Length of Stay, charges, and costs are heavily right-skewed — a small number of catastrophically long or expensive admissions distort means and break the assumptions of classical t-tests and ANOVA. Qualls, Pallin, and Schuur (2010) found that 43% of published Emergency Department studies still use parametric methods on this kind of data, which causes false negatives in real group comparisons.

This project takes the opposite approach: apply ten **distribution-free** procedures simultaneously to a single large administrative dataset, and let the agreement (or disagreement) between rank-based, distribution-based, and resampling-based tests tell the story.

To my knowledge, this is the first comprehensive nonparametric comparison of inpatient utilization between Kings and Queens counties on SPARCS 2022 data at this scale.

## The data

- **Source:** New York State Statewide Planning and Research Cooperative System (SPARCS), Inpatient Discharges (De-identified) 2022. Publicly available at https://health.data.ny.gov/
- **Scope:** All inpatient hospital admissions in Kings County (Brooklyn) and Queens County, calendar year 2022
- **Size:** 368,495 records · 33 original SPARCS variables (+ 4 derived numeric columns) · ~160 MB CSV
- **Key outcomes:** Length of Stay (days), Total Charges (USD), Total Costs (USD), Birth Weight (grams)

## The ten tests

| # | Test | Purpose | Key Result |
|---|------|---------|-----------|
| 1 | **Runs Test** | Randomness of record ordering | Reject H₀ for all 7 testable variables |
| 2 | **Sign Test** | Median equality, paired (Kings vs Queens) | Reject H₀ for all 5 variables |
| 3 | **Wilcoxon Signed-Rank** | Paired distribution equality | Reject H₀ for all 5 variables |
| 4 | **Mann-Whitney U** | Two-sample distribution equality | Reject H₀ for all 6 comparisons |
| 5 | **Kolmogorov-Smirnov** | Full CDF equality | Reject H₀ for all 8 comparisons |
| 6 | **Kruskal-Wallis** | k-sample distribution equality | Reject H₀ for all 15 outcome × grouping tests |
| 7 | **Friedman** | Severity effect, blocked by facility | Reject H₀ for LOS, Charges, Costs |
| 8 | **Kendall's Tau** | Ordinal association | Reject H₀ for all 6 pairs |
| 9 | **Spearman's Rho** | Monotone rank association | Reject H₀ for all 6 pairs; ρ ≈ 0.86 for Charges–Costs |
| 10 | **Permutation Test** (B=5,000) | Label exchangeability | Median Charges differ (p=0.011); mean does not (p=0.389) |

## Key findings

- **Hospital utilization differs measurably between Kings and Queens.** Length of Stay, Charges, Costs, and Birth Weight all show significant distributional differences across multiple test families.
- **Severity of illness is the dominant driver** of variation in Length of Stay, Charges, and Costs — and this effect survives blocking by facility (Friedman test). Sicker patients cost more, predictably, across every hospital.
- **The mean is the wrong summary statistic** for hospital charge data. The permutation test on Charges *fails to reject* equal means between counties (p = 0.389) but *rejects* equal medians (p = 0.011) — a textbook demonstration of how a handful of multi-million-dollar admissions in Kings County inflate its mean without shifting the typical charge. This reproduces the simulation findings of Chazard et al. (2017).
- **Kendall's τ is consistently smaller in magnitude than Spearman's ρ** for the same variable pairs, exactly as predicted by Croux and Dehon (2010) under the bivariate normal dependence approximation τ ≈ (2/π) arcsin(ρ).

## What I learned

- **Pick the right statistic for the data shape.** On right-skewed data, mean-based tests can fail to detect real effects that median-based tests catch easily. The permutation test results on Charges are a clean object lesson.
- **Multiple tests should agree by design.** When ten different distribution-free procedures all reject H₀ on the same comparison, the finding is robust to methodological choices. When they disagree (as the permutation mean vs median did), the disagreement itself is informative.
- **N is not enough.** With N ≈ 370,000, p-values approach zero for any non-null effect — even effects too small to matter clinically. A useful future extension is bootstrap confidence intervals for effect sizes (Spearman ρ, dollar differences) to complement the p-value-based conclusions.
- **Custom implementations build understanding.** Writing the Runs Test and Permutation Test from scratch in base R, rather than calling a package, made me reason carefully about variance formulas, tie handling, and the exchangeability null. Implementing once is worth reading ten times.

## Repository contents

```
sparcs-nonparametric-analysis/
├── README.md                    # this file
├── Vinay_Final_Project.Rmd      # main analysis (R Markdown source)
├── Vinay_Final_Project.pdf      # full 44-page paper (LaTeX-rendered)
├── Knitted.pdf                  # R Markdown knitted output (code + results)
├── data/
│   └── README.md                # how to obtain the SPARCS dataset
└── .gitignore
```

## How to reproduce

1. Clone this repo:
   ```bash
   git clone https://github.com/VinaySiddi08/sparcs-nonparametric-analysis.git
   cd sparcs-nonparametric-analysis
   ```
2. Download the SPARCS 2022 Inpatient Discharges dataset from https://health.data.ny.gov/ and filter to Kings and Queens counties (or use the filter steps documented in `data/README.md`). Place the file at `SPARCS_2022_Kings_Queens.csv` in the project root.
3. Open `Vinay_Final_Project.Rmd` in RStudio and knit. All ten tests will run in base R; no external packages are required for the test statistics themselves.

## R packages used

All ten nonparametric test statistics are implemented in **base R**. The only external dependency is `knitr` for rendering tables in the report. Custom functions:

- `runs_test()` — Runs test with normal approximation
- `perm_two_sample()` — Monte Carlo permutation test (supports mean or median)

Standard base R functions used: `binom.test()`, `wilcox.test()`, `ks.test()`, `kruskal.test()`, `friedman.test()`, `cor.test()` (Kendall and Spearman methods).

## Key references

This project builds on a wide literature; full citations in the report. The most directly relevant works:

- **Qualls, Pallin & Schuur (2010)** — motivated the use of nonparametric methods on Emergency Department Length of Stay data.
- **Chazard et al. (2017)** — simulation comparison of 12 tests on Length-of-Stay data; the methodological foundation for choosing nonparametric procedures here.
- **Hollander, Wolfe & Chicken (2013)** and **Conover (1999)** — standard graduate textbooks for the ten test families.
- **Croux & Dehon (2010)** — the τ ≈ (2/π) arcsin(ρ) relationship reproduced in this project.
- **Ernst (2004)** and **Good (2005)** — permutation testing foundations.
- **Heslin et al. (2024)** and **Ning, Harkness & Gao (2017)** — prior nonparametric work on SPARCS data.

## Future extensions

- Generalize to all five NYC boroughs using SPARCS 2023, enabling a six-group Kruskal-Wallis
- Apply the **Jonckheere-Terpstra trend test** for ordered severity levels (a natural eleventh test)
- Fit a **semi-parametric time-to-discharge model** (Li et al., 2022) combining parametric hazard with nonparametric baseline
- Add **bootstrap confidence intervals** for effect sizes to complement p-value-based conclusions

---

**Contact:** Vinay Siddi · <vsiddi@albany.edu> · [LinkedIn](https://www.linkedin.com/in/vinay-siddi-378252252) · Albany, NY
