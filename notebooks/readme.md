# Notebook: AB_Test_Fee_Transparency.ipynb

Quick walkthrough of what's in this notebook and why I did each part. The
notebook is already run, with all outputs and charts saved, so you can
read it directly on GitHub without running it again.

## 1. Business question and hypotheses

First I write what I am comparing, what the main metric is, what the
guardrail metrics are, and the significance level I am using. I do this
before loading any data, because the metric and the hypothesis need to be
decided in advance, not chosen later after seeing the results.

## 2. Load and inspect the data

Basic checks here: shape, data types, missing values. The only missing
values are in `satisfaction_score` and `repeated_within_30d`. This only
happens for transfers that were never completed, which makes sense,
because you cannot rate a transfer that did not happen.

## 3. Sanity checks before trusting the results

Before comparing the groups, I run two checks that need to pass first:

- **Sample Ratio Mismatch (SRM):** a chi square test that compares the
  real group sizes to the expected 50/50 split. If this test fails, it
  means the randomization is broken, and none of the results after this
  can be trusted.
- **Duplicate and range checks:** no duplicate transfer IDs, all dates
  inside the experiment period, and the amount tier split balanced
  between groups.

## 4. Overall completion rate (the topline number)

Here I calculate the overall conversion rate for each group and run a two
proportion z test. Result: the difference is small and not statistically
significant (p = 0.24). If you only look at this number, you would say
the treatment made no difference. But this is not the full picture.

## 5. Segmenting by transfer size

I split the same comparison by `amount_tier` and run the z test again for
each segment. This is the most important part of the analysis, because
the overall number was hiding two effects that go in different directions
(small transfers went down, large transfers went up), and both are
statistically significant. I decided to look at this segment before
seeing the results, not after, just to find an explanation for a null
result. This is important, because deciding the segment in advance is
what makes this a real analysis and not p hacking.

## 6. Guardrail metrics

I check the satisfaction score (t test) and the 30 day repeat rate (two
proportion z test) between groups, only for completed transfers. Both
metrics are better for the treatment group. I added this check because
looking only at completion rate could hide a problem, for example if the
treatment increased completions but made customers less satisfied. That
did not happen here, both guardrail metrics look fine.

## 7. Conclusion and recommendation

My recommendation is to launch the transparency treatment. But the drop
in small transfers is a real cost and should not be ignored. I would also
keep monitoring the results by segment after launch, instead of only
looking at the overall number, because that is what hid the effect in
the first place.

## 8. Limitations

Things I would add to make this analysis more complete: a correction for
multiple comparisons, a better way to model the guardrail metrics (right
now I only mention that they depend on completion, but I do not model
this), and testing the result again in a second time period before saying
it is a stable effect.

## Key libraries used

- `scipy.stats.chisquare` for the Sample Ratio Mismatch check
- `statsmodels.stats.proportion.proportions_ztest` for the binary metric
  comparisons (completion rate, repeat rate)
- `scipy.stats.ttest_ind` (Welch's t test, `equal_var=False`) for the
  satisfaction score comparison
- `matplotlib` for the charts. I kept them simple on purpose (bar charts
  and histograms), because the goal here is to communicate the result
  clearly, not to make it look impressive
