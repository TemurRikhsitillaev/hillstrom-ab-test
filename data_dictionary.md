# Data Dictionary: Hillstrom E-Mail Analytics

## Source

Public dataset by Kevin Hillstrom (MineThatData), released in March 2008 as a challenge for analysts:
[The MineThatData E-Mail Analytics And Data Mining Challenge](https://blog.minethatdata.com/2008/03/minethatdata-e-mail-analytics-and-data.html).

An online clothing retailer took 64,000 customers who had bought something in the last 12 months and randomly split them into three groups:

| Group (`segment`) | What they received | Customers |
|---|---|---|
| `Mens E-Mail` | email with men's products | 21,307 |
| `Womens E-Mail` | email with women's products | 21,387 |
| `No E-Mail` | nothing (control group) | 21,306 |

Customer behaviour was tracked for 2 weeks after the email.

- File: `data/hillstrom.csv` (not stored in the repository)
- Size: 64,000 rows, 12 columns
- **One row = one customer.** Each customer is in exactly one group.

## Columns

### Customer attributes (before the experiment)

The email could not affect these columns. They are used to check group balance and to build segments.

| Column | Type | Meaning | Values |
|---|---|---|---|
| `recency` | integer | Months since the last purchase | 1 to 12 |
| `history` | float | USD spent in the past 12 months | 29.99 to 3,345.93 |
| `history_segment` | text | `history` binned into 7 ranges | `1) $0 - $100` … `7) $1,000 +` |
| `mens` | 0 / 1 | 1 = bought men's products in the past year | 0, 1 |
| `womens` | 0 / 1 | 1 = bought women's products in the past year | 0, 1 |
| `zip_code` | text | Type of area where the customer lives | `Urban`, `Surburban`, `Rural` |
| `newbie` | 0 / 1 | 1 = new customer (first purchase in the past 12 months) | 0, 1 |
| `channel` | text | Where the customer bought in the past year | `Phone`, `Web`, `Multichannel` (both) |

### Experiment group

| Column | Type | Meaning | Values |
|---|---|---|---|
| `segment` | text | Which email the customer received | `Mens E-Mail`, `Womens E-Mail`, `No E-Mail` |

### Outcomes in the 2 weeks after the email (test metrics)

| Column | Type | Meaning | Values |
|---|---|---|---|
| `visit` | 0 / 1 | 1 = visited the website | 0, 1 |
| `conversion` | 0 / 1 | 1 = made a purchase | 0, 1 |
| `spend` | float | USD spent | 0 to 499; 0 for most customers |

## Metrics calculated from the columns

| Metric | Formula | Role |
|---|---|---|
| Conversion rate | customers with `conversion = 1` / customers in the group | primary |
| Visit rate | customers with `visit = 1` / customers in the group | secondary |
| Revenue per customer | sum of `spend` in the group / **all** customers in the group | secondary |
| Average order value (AOV) | sum of `spend` / buyers only | diagnostic |

Revenue per customer is divided by all customers in the group, including those who bought nothing.
Dividing by buyers only gives the average order value, which is a different metric.

## Columns added in the notebook (step 8)

| Column | Meaning | Values |
|---|---|---|
| `purchase_history` | which products the customer bought in the past year, from `mens` and `womens` | `mens only`, `womens only`, `both` |
| `history_level` | `history` below or at/above the median | `low`, `high` |
| `newbie_label` | `newbie` as text | `existing`, `new` |

## Data notes

- **Typo in `zip_code`:** the data says `Surburban` instead of `Suburban`. Filters must use `'Surburban'`.
- **`mens` and `womens` can both be 1** if the customer bought both kinds of products. They are never both 0.
- **`history` and `history_segment` hold the same information.** `history` is used for calculations.
- **No customer ID.** About 6,500 rows are exact copies of other rows. These are different customers with the same attributes and outcomes, so they are not removed.
- **Revenue is very skewed:** 99.1% of customers spent 0, so mean revenue depends on a small number of purchases.

## What the data does not contain

- Unsubscribes or spam complaints, so no guardrail metric can be calculated.
- Daily data: only the 2-week total, so long-term and novelty effects cannot be checked.
- Email sending cost and profit margin: they are set as assumptions in step 9.
