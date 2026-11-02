# Triple-Difference CGS Staggered Event Study: Effects of SNAP Work Requirements

This repository illustrates a triple-difference (DDD) staggered event-study design using the effects of Supplemental Nutrition Assistance Program (SNAP) work requirements as the empirical application. The analysis applies the CGS framework to policy adoption that occurs at different times across states.

## Research design

The design combines three sources of variation:

1. **Policy exposure:** states subject to SNAP work requirements (waiver states when the waiver ends) are compared with non-waiver or never-treated states.
2. **Event time:** outcomes before and after the work-requirement policy becomes effective are compared in a staggered-adoption event-study framework.
3. **Age eligibility:** individuals just below the age-50 threshold are compared with individuals just above it. In the code, the two groups are ages 48–49 and ages 51–52; age 50 itself is excluded.

The resulting estimand is a dynamic triple difference: the event-time treatment effect for the younger group minus the corresponding event-time treatment effect for the older comparison group. Estimates are produced separately for women and men.

## Estimation approach

The scripts use `did::att_gt()` to estimate group-time average treatment effects and `did::aggte(type = "dynamic")` to aggregate them into event-study coefficients. Never-treated states form the control group. The analysis is specified as a repeated cross-section (`panel = FALSE`) and controls for the unemployment rate (`UR`).

The event-study window covers four years before treatment through three years after treatment:

```text
Event time: -4, -3, -2, -1, 0, +1, +2, +3
```

For each event time, the DDD estimate is calculated as:

```text
DDD(e) = ATT_below_age_50(e) - ATT_above_age_50(e)
```

The outer bootstrap uses 101 resamples. Within each `att_gt()` estimation, the code requests 1,000 bootstrap iterations and simultaneous confidence bands.

## Outcomes

The analysis considers two labor-market outcomes:

- `self_emp`: self-employment
- `work_wage`: wage or salary employment

## Repository files

- `Effects on female`: estimates the DDD event-study effects for women (`sex == 0`).
- `Effects on male`: estimates the DDD event-study effects for men (`sex == 1`).

Each script estimates the dynamic effects for both outcomes and prints the corresponding bootstrap results.

## Required data

The scripts currently read `unity_CGS_1217.dta`. The dataset is not included in this repository. At minimum, it must contain the following variables:

- `self_emp`: self-employment outcome
- `work_wage`: wage-employment outcome
- `year`: calendar year
- `cpuma0010`: geographic or sampling-unit identifier used by the estimator
- `G`: first treatment year; missing values identify never-treated observations and are recoded to `Inf`
- `UR`: unemployment-rate control
- `age`: respondent age
- `sex`: sex indicator (`0` for women and `1` for men in the current code)

Before running the scripts, replace the hard-coded path in `read_dta()` with the location of the data on your computer.

## Required R packages

```r
install.packages(c(
  "tidyr", "dplyr", "haven", "fixest", "broom",
  "stringr", "here", "ggplot2", "did", "boot"
))
```

## Running the analysis

Run either script in R after updating the data path:

```r
source("Effects on female")
source("Effects on male")
```

The scripts set the random seed to `20240103` for reproducibility. Because the estimation uses nested bootstrap procedures, execution may take substantial time.

## Interpretation

Pre-treatment coefficients can be used to assess differential pre-trends around the age-50 threshold. Post-treatment coefficients measure how the effects of SNAP work requirements for the below-50 group differ from those for the above-50 comparison group, relative to non-waiver or never-treated states.

This repository is an empirical implementation of the DDD staggered event-study design; it does not include the underlying restricted or external data.
