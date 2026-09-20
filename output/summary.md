# Data Summary — Agent 2

**Revenue for every number below excludes returned transactions.** Revenue formula used:
`("Price" * "Units") - "Discounts"`. Exactly half the transactions in this data are marked
`Returned` (1,508 of 3,000, or 50.27%), so the choice of whether to count them matters a
lot — including them nearly doubles the revenue figure. I excluded returns because a
returned sale isn't money the business actually kept; that's the standard way revenue is
reported. Both totals are below so you can see the difference and override this if you'd
rather count gross sales instead.

- **Revenue including returns:** $2,117,111.78
- **Revenue excluding returns (used everywhere below):** $1,069,322.03

## Headline numbers

- **Revenue:** $1,069,322.03
- **Transactions:** 3,000 total (1,492 non-returned, 1,508 returned)
- **Customers who bought:** 954 of 1,000
- **Average order value:** $716.70 (revenue ÷ non-returned transactions)
- **Return rate:** 50.27%

That return rate is unusually high for a real store — worth treating as a flag, not just
a number. See "Things that look off" below.

## Revenue by Product_Category

| Category | Revenue |
|---|---|
| Toys | $197,766.01 |
| Automotive | $193,726.79 |
| Home | $177,954.22 |
| Electronics | $177,625.68 |
| Fashion | $172,063.11 |
| Books | $150,186.22 |

Fairly even — no category dominates, Toys leads by about 5% over the next closest.

## Revenue by Purchase_Channel

| Channel | Revenue |
|---|---|
| In-store | $554,560.89 |
| Online | $514,761.14 |

In-store edges out online by about 8%, close to a 50/50 split.

## Revenue by Mode_of_Payment

| Method | Revenue |
|---|---|
| Credit Card | $234,914.10 |
| Cash | $215,670.06 |
| Debit Card | $207,392.15 |
| UPI | $206,639.06 |
| Net Banking | $204,706.66 |

Credit Card leads, but all five methods are within about 15% of each other.

## Revenue by month

(Excludes returns; computed with `to_date("Date_of_Purchase", 'DD-MM-YYYY')`, March 2021 through March 2023.)

| Month | Revenue |
|---|---|
| 2021-03 | $5,635.18 |
| 2021-04 | $40,727.92 |
| 2021-05 | $57,464.93 |
| 2021-06 | $55,527.59 |
| 2021-07 | $51,486.57 |
| 2021-08 | $28,600.30 |
| 2021-09 | $47,246.42 |
| 2021-10 | $43,126.45 |
| 2021-11 | $46,903.82 |
| 2021-12 | $51,523.03 |
| 2022-01 | $33,141.26 |
| 2022-02 | $49,763.36 |
| 2022-03 | $40,372.85 |
| 2022-04 | $31,351.91 |
| 2022-05 | $39,727.22 |
| 2022-06 | $43,523.53 |
| 2022-07 | $45,429.34 |
| 2022-08 | $53,880.72 |
| 2022-09 | $39,532.29 |
| 2022-10 | $45,431.86 |
| 2022-11 | $56,920.45 |
| 2022-12 | $42,362.34 |
| 2023-01 | $46,453.02 |
| 2023-02 | $33,556.71 |
| 2023-03 | $39,632.96 |

2021-03 is a partial month (data starts 2021-03-25), which explains why it's far lower
than every other month — not a real dip. No clear seasonal or growth trend otherwise;
revenue bounces between roughly $30K and $57K a month.

## Top 10 products by revenue

| Product | Revenue | Transactions |
|---|---|---|
| Motor Oil | $48,471.71 | 125 |
| Laptop | $48,393.47 | 121 |
| Doll | $46,683.58 | 103 |
| Car Charger | $46,525.38 | 92 |
| Board Game | $45,566.90 | 110 |
| Jeans | $45,242.25 | 113 |
| Headphones | $40,913.23 | 103 |
| Dress | $39,972.74 | 105 |
| Bed Sheets | $38,543.19 | 111 |
| Air Freshener | $38,267.07 | 96 |

## Customers by age band (counts and revenue only — no names)

| Age band | Customers | Revenue |
|---|---|---|
| 25-34 | 207 | $238,253.46 |
| 35-44 | 193 | $232,274.72 |
| 45-54 | 223 | $217,252.95 |
| 55+ | 238 | $242,245.71 |
| Under 25 | 139 | $139,295.19 |

## Customers by Location (counts and revenue only)

| Location | Customers | Revenue |
|---|---|---|
| Pune | 116 | $124,909.91 |
| Mumbai | 106 | $119,240.72 |
| Hyderabad | 115 | $117,131.91 |
| Surat | 99 | $113,054.06 |
| Bangalore | 106 | $111,906.94 |
| Jaipur | 96 | $105,687.58 |
| Chennai | 87 | $103,443.38 |
| Delhi | 102 | $96,984.81 |
| Kolkata | 88 | $90,517.96 |
| Ahmedabad | 85 | $86,444.76 |

## Repeat vs. one-time buyers

- Bought once: **146 customers**
- Bought more than once: **808 customers**

Most active customers are repeat buyers — 85% of the 954 who bought at all came back for
a second purchase or more.

## Things that look off

- **Return rate of 50.27% is very high.** Agent 1 found zero nulls and zero orphan
  `Customer_ID`s, so this isn't a data-quality artifact from joining — it's just what the
  `Returned` column says. Worth confirming with whoever owns this data whether that's
  real or a labeling issue, since it changes the revenue story by roughly 2x.
- **No zero/negative prices, no zero/negative units, no discounts exceeding the sale
  amount, no negative discounts.** Checked all four and found none — the numeric fields
  are clean.
- **46 customers have never bought anything** (flagged in Agent 1's report too) — expected, not an error.

## Questions from input/

Nothing was in `input/` at the time this agent ran — no additional questions to answer.
