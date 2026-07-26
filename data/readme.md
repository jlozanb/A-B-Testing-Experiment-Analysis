# Data: fee & exchange rate transparency A/B test

## Context

This dataset simulates 10,000 international transfers sent through a
fintech remittance app over a 4 week period, split 50/50 between two
checkout experiences:

- **Control:** the checkout screen only shows the final amount the
  recipient will receive
- **Treatment:** the checkout screen shows the full breakdown, amount
  sent, exchange rate applied, fee charged, and final amount received

The data is fully synthetic. I generated it with numpy, with a fixed seed
so it can be reproduced. I built it to follow a specific pattern that
shows up in real experimentation work: transparency has almost no effect
overall, but it has a real, opposite effect depending on the transfer
size, and this only becomes visible once you segment the data. This
pattern is called Simpson's paradox. The point of the dataset is to
practice noticing this kind of pattern instead of stopping at the overall
number.

## File: fee_transparency_ab_test_data.csv

One row per transfer.

| Column | Type | Description |
|---|---|---|
| `transfer_id` | integer | Unique identifier for the transfer |
| `group` | text | `control` or `treatment` |
| `amount_tier` | text | `small` (transfer under $200) or `large` ($200 and up). This segment was decided in advance, not created after seeing the results |
| `amount_sent_usd` | float | Amount sent, in USD |
| `sent_at` | datetime | When the transfer was started |
| `destination_country` | text | Destination country of the transfer |
| `transfer_completed` | integer (0/1) | Primary metric, whether the user completed the transfer (1) or left before finishing (0) |
| `satisfaction_score` | float (0 to 10) | Guardrail metric, satisfaction survey after the transfer. Only filled in when `transfer_completed = 1` |
| `repeated_within_30d` | integer (0/1) | Guardrail metric, whether the user sent another transfer within 30 days. Only filled in when `transfer_completed = 1` |

## Why some columns have missing values

`satisfaction_score` and `repeated_within_30d` are empty whenever
`transfer_completed = 0`. This is intentional and reflects a real system,
if a user never finishes the transfer, there is no survey to show them
and no transfer to repeat. Any analysis of these two guardrail columns
should only use completed transfers (the notebook does this).

## How the data was generated

I set the completion rates for each group and each segment on purpose, so
that the overall difference between groups is small and not statistically
significant, while the differences inside each segment are both real and
statistically significant, and go in opposite directions:

| Group | Small transfers | Large transfers |
|---|---|---|
| Control | 82% | 74% |
| Treatment | 78% | 80% |

I also set the guardrail metrics (satisfaction, repeat rate) to be better
for the treatment group across both segments. This creates a situation
where looking only at the primary metric would undervalue the treatment.
