# A/B Testing Experiment Analysis: Fee & Exchange Rate Transparency

![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white)
![Statistics](https://img.shields.io/badge/Statistical%20Analysis-6c757d)
![Hypothesis Testing](https://img.shields.io/badge/Hypothesis%20Testing-6c757d)
![A/B Testing](https://img.shields.io/badge/A%2FB%20Testing-6c757d)
![Data Visualization](https://img.shields.io/badge/Data%20Visualization-6c757d)
 
## Overview

Statistical experimentation project evaluating a business decision through a
controlled A/B test: whether showing the full fee and exchange rate
breakdown on an international transfer changes user behavior, compared to
showing only the final amount received. The project covers experiment
validity checks, the topline result, a segment analysis that reverses the
topline conclusion (a Simpson's paradox pattern), and guardrail metrics
that inform the final recommendation.
 
All data used in this project is synthetic, generated to reflect a
realistic fintech remittance scenario without exposing any real company or
user data.
 
## Experiment design

- **Control (A):** only the final amount received is shown
- **Treatment (B):** full breakdown shown (amount sent, exchange rate
  applied, fee, final amount)
- **Primary metric:** transfer completion rate
- **Segment:** transfer amount (small, under $200 vs large, $200 and up)
- **Guardrail metrics:** post-transaction satisfaction score (0-10) and
  30-day repeat transfer rate
- **Randomization:** 50/50 split, ~5,000 transfers per group
- **Duration:** 4 weeks
- **Significance level:** 5%, two-sided, decided before looking at results.
  The transfer-size segment split was also decided in advance, based on
  the business reasoning that a fee matters more, relatively, on a small
  transfer than a large one

## Features

- Sample Ratio Mismatch (SRM) check before trusting the results
- Topline (aggregate) two-proportion z-test for the primary metric
- Pre-registered segment analysis by transfer size, showing the topline
  result was masking two real, opposite effects (a textbook Simpson's
  paradox pattern)
- Guardrail metric analysis (satisfaction, repeat usage) to check whether
  optimizing the primary metric alone would have been the wrong call
- Explicit discussion of the difference between statistical and practical
  significance, and of the risk of exploratory segment hunting vs
  pre-registered segment analysis

## Technologies

- Python (pandas, numpy)

- SciPy and statsmodels for hypothesis testing
- Matplotlib for visualization
- Jupyter Notebook

## Workflow

- Simulate a realistic dataset for the experiment
  (`data/fee_transparency_ab_test_data.csv`)
- Load and inspect the data, checking for data quality issues
- Check the sample split for Sample Ratio Mismatch
- Compute the topline (aggregate) completion rate and run the primary
  hypothesis test
- Break the result down by transfer size and re-run the test within each
  segment
- Analyze the guardrail metrics (satisfaction, 30-day repeat rate)
- Summarize the result into a business recommendation, including what it
  would take to capture the upside without the small-transfer cost

## Results

- **Topline:** 78.9% (control) vs 79.6% (treatment), not statistically
  significant (p = 0.24) — taken alone, this would say "no effect"
- **Segmented by transfer size:** small transfers drop by 3.9 pp with
  transparency (statistically significant), large transfers gain 5.3 pp
  (statistically significant) — the topline number was hiding two real,
  opposite effects
- **Guardrail metrics:** satisfaction and 30-day repeat rate both improve
  clearly with transparency, regardless of transfer size
- **Recommendation:** ship the transparency treatment, but treat the
  small-transfer drop-off as a real cost worth a follow-up experiment
  (e.g. a lighter breakdown for small transfers), and monitor by segment
  after launch rather than only on the aggregate number
 
## Project Purpose

The objective of this project was to demonstrate a level of A/B test
analysis that goes past reading a single p-value: checking experiment
validity first, understanding when and why to pre-register a segment
split instead of hunting for one after the fact, and using guardrail
metrics to catch cases where the primary metric alone would lead to the
wrong decision.
 
## Repository Contents
```
├── data
│   ├── fee_transparency_ab_test_data.csv
│   └── README.md
│
├── notebooks
│   ├── AB_Test_Fee_Transparency.ipynb
│   └── README.md
│
├── images
│   ├── segment_reversal.png
│   └── satisfaction_distribution.png
│
└── README.md
```
 
## Author

Jorge Lozano



