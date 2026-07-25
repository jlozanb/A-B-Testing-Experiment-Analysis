# Data: fee & exchange rate transparency A/B test

## Context

This dataset simulates 10,000 international transfers sent through a
fintech remittance app over a 4-week period, split 50/50 between two
checkout experiences:

- **Control:** the checkout screen shows only the final amount the
  recipient will receive
- **Treatment:** the checkout screen shows the full breakdown: amount
  sent, exchange rate applied, fee charged, and final amount received

The data is entirely synthetic (generated with numpy, seed fixed for
reproducibility). It is engineered to reproduce a specific, realistic
pattern: transparency has close to zero effect in aggregate, but a real,
opposite effect depending on transfer size, which only shows up once you
segment the data. This is the same kind of pattern ("Simpson's paradox")
that shows up in real experimentation work, and the point of the dataset
is to practice noticing it instead of stopping at the topline number.

## File: fee_transparency_ab_test_data.csv

One row per transfer.

| Column | Type | Description |
|---|---|---|
| `transfer_id` | integer | Unique identifier for the transfer |
| `group` | text | `control` or `treatment` |
| `amount_tier` | text | `small` (transfer under $200) or `large` ($200 and up). Pre-registered segment, not derived after the fact |
| `amount_sent_usd` | float | Amount sent, in USD |
| `sent_at` | datetime | When the transfer was initiated |
| `destination_country` | text | Destination country of the transfer |
| `transfer_completed` | integer (0/1) | Primary metric: whether the user completed the transfer (1) or abandoned it before finishing (0) |
| `satisfaction_score` | float (0-10) | Guardrail metric: post-transaction satisfaction survey. Only populated when `transfer_completed = 1` |
| `repeated_within_30d` | integer (0/1) | Guardrail metric: whether the user sent another transfer within 30 days. Only populated when `transfer_completed = 1` |

## Why some columns have missing values

`satisfaction_score` and `repeated_within_30d` are blank whenever
`transfer_completed = 0`. This is intentional and mirrors a real system: if
a user never finishes the transfer, there is no post-transaction survey to
show them and no transfer to repeat. Any analysis of these two guardrail
columns should filter to completed transfers only (the notebook does this).

## How the data was generated

Completion rates were set explicitly per group and per segment so that the
aggregate difference between groups is small and not statistically
significant, while the segment-level differences are both real and
statistically significant, in opposite directions:

| Group | Small transfers | Large transfers |
|---|---|---|
| Control | 82% | 74% |
| Treatment | 78% | 80% |

Guardrail metrics (satisfaction, repeat rate) were set to favor the
treatment group uniformly, regardless of segment, to create the scenario
where the primary metric alone would understate the value of the
treatment.
