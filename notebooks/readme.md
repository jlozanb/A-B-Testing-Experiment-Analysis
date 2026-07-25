
# Notebooks: AB_Test_Fee_Transparency.ipynb

Walkthrough of what each section of the notebook does, why it's there, and
what it concludes. The notebook is already executed with its outputs and
charts saved, so it can be read directly on GitHub without needing to run
it.

## 1. Business question and hypotheses
States the experiment upfront: what's being compared, what the primary
metric is, what the guardrail metrics are, and the significance level.
This is written before any data is loaded, on purpose: the hypothesis and
the metric are decided in advance, not chosen after seeing what "worked".

## 2. Load and inspect the data
Basic checks: shape, dtypes, missing values. Confirms that the only
missing values are `satisfaction_score` and `repeated_within_30d` for
transfers that were never completed, which is expected.

## 3. Sanity checks before trusting the results
Two checks that have to pass before any comparison between groups means
anything:
- **Sample Ratio Mismatch (SRM):** a chi-square test comparing the actual
  group sizes to the expected 50/50 split. If this fails, the
  randomization itself is broken and no downstream result can be trusted.
- **Duplicate and range checks:** no duplicate transfer IDs, dates fall
  inside the expected experiment window, and the amount tier split is
  balanced across groups.

## 4. Overall completion rate (the topline number)
Computes the aggregate conversion rate for each group and runs a
two-proportion z-test. Result: the difference is small and not
statistically significant (p = 0.24). Taken alone, this section would
lead to "the treatment made no difference."

## 5. Segmenting by transfer size
Splits the same comparison by `amount_tier` and reruns the z-test inside
each segment. This is the core of the analysis: it shows the topline
number was hiding two real, statistically significant effects in opposite
directions (small transfers down, large transfers up). The notebook is
explicit that this segment was decided before looking at the results, not
picked afterward to explain a null finding, since that distinction is what
separates a valid segment analysis from p-hacking.

## 6. Guardrail metrics
Checks satisfaction score (t-test) and 30-day repeat rate (two-proportion
z-test) between groups, restricted to completed transfers. Both favor the
treatment clearly. This section exists to catch a case where optimizing
the primary metric alone would have led to a worse decision for the
business.

## 7. Conclusion and recommendation
Turns the statistical results into one business recommendation: ship the
transparency treatment, but treat the small-transfer drop-off as a real,
worth-addressing cost rather than ignoring it, and keep monitoring by
segment after launch instead of only watching the aggregate number.

## 8. Limitations
Explicit list of what a more rigorous version of this analysis would add:
correction for multiple comparisons, modeling the guardrail metrics'
conditional-on-completion structure instead of just noting it, and
replicating the result in a second time period before treating it as a
stable effect.

## Key libraries used and why
- `scipy.stats.chisquare` for the Sample Ratio Mismatch check
- `statsmodels.stats.proportion.proportions_ztest` for all the binary
  metric comparisons (completion rate, repeat rate)
- `scipy.stats.ttest_ind` (Welch's t-test, `equal_var=False`) for the
  continuous satisfaction score comparison
- `matplotlib` for all charts, kept deliberately simple (bar charts and
  histograms), since the goal here is clear communication of the result,
  not visual complexity
