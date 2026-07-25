# A/B Testing Experiment Analysis
![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white)
![Statistics](https://img.shields.io/badge/Statistical%20Analysis-6c757d)
![A/B Testing](https://img.shields.io/badge/A%2FB%20Testing-6c757d)

## Overview
Statistical experimentation project evaluating a business decision through a
controlled A/B test: whether a simplified identity verification (KYC) flow
increases the share of users who complete verification, compared to the
current flow. The project covers the full workflow from experiment design
sanity checks to hypothesis testing, confidence intervals and a business
recommendation.

All data used in this project is synthetic, generated to reflect a
realistic fintech onboarding scenario without exposing any real company or
user data.

## Experiment design
- **Control (A):** current KYC verification flow
- **Treatment (B):** simplified KYC verification flow (fewer steps, clearer
  instructions)
- **Primary metric:** verification completion rate (binary)
- **Secondary metric:** time to complete verification, for users who
  completed it
- **Randomization:** 50/50 split at signup, ~4,000 users per group
- **Duration:** 3 full weeks, to avoid weekday/weekend bias
- **Significance level:** 5%, two-sided test, decided before looking at
  the results

## Features
- Sample Ratio Mismatch (SRM) check before trusting the results
- Descriptive comparison of conversion rates by group, with 95% confidence
  intervals
- Two-proportion z-test for the primary metric, plus confidence interval
  for the difference in conversion rates
- Effect size reporting: absolute and relative lift, not just the p-value
- Secondary metric analysis (completion time) with an independent t-test
- Segment-level check (by device) to confirm the effect is not driven by
  a single subgroup
- Explicit discussion of statistical significance vs practical
  significance, and of the limitations of the analysis

## Technologies
- Python (pandas, numpy)
- SciPy and statsmodels for hypothesis testing
- Matplotlib for visualization
- Jupyter Notebook

## Workflow
- Simulate a realistic dataset for the experiment (`data/kyc_ab_test_data.csv`)
- Load and inspect the data, checking for data quality issues
- Check the sample split for Sample Ratio Mismatch before trusting any
  result
- Compare conversion rates between groups and run a two-proportion z-test
- Calculate the confidence interval and the effect size for the primary
  metric
- Analyze the secondary metric (completion time) and a device-level
  segment breakdown
- Summarize the result into a business recommendation

## Results
- Completion rate: **59.1%** (control) vs **64.7%** (treatment), a
  **5.6 percentage point** lift (p-value < 0.001, 95% CI excludes zero)
- Treatment users also completed verification faster on average
- The effect holds across both device types (Android and iOS)
- **Recommendation:** roll out the simplified KYC flow to all users

## Project Purpose
The objective of this project was to demonstrate a correct, end-to-end A/B
test analysis: checking the experiment's validity before trusting the
result, choosing the right statistical test for a binary metric, reporting
effect size and confidence intervals alongside the p-value, and turning a
statistical result into a clear business recommendation with its
limitations stated explicitly.

## Repository Contents
```
├── data
│   └── kyc_ab_test_data.csv
│
├── notebooks
│   └── AB_Test_Analysis.ipynb
│
└── README.md
```

## Author

Jorge Lozano



