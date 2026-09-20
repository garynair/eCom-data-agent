# Agent 2 — Summarize the Data

## Purpose

Turn the data into numbers a business person can act on.

## The database (facts you need — copied from CLAUDE.md so this file works on its own)

Two tables, connected through the Supabase MCP connector, database `eCommerce`, schema `public`:

- `public.customers` — 1,000 rows, primary key `Customer_ID`.
  Columns: `Customer_ID`, `Customer_Name`, `Customer_Email`, `Customer_Number`, `Age`, `Gender`, `Location`.
- `public.transactions` — 3,000 rows, primary key `Transaction_ID`.
  Columns: `Transaction_ID`, `Date_of_Purchase`, `Customer_ID`, `Product_Category`,
  `Product_Name`, `Units`, `Price`, `Discounts`, `Returned`, `Mode_of_Payment`, `Purchase_Channel`.

Note: `public.dsm` also exists (3,000 rows, same columns as `transactions`) — it's a
redundant/staging table, not part of this pipeline. Ignore it.

Four things that will break your SQL if you ignore them:

1. **Mixed-case column names.** Always double-quote them: `select "Customer_ID" from public.customers;`. Without quotes the query fails.
2. **`Date_of_Purchase` is text in `DD-MM-YYYY` format**, not a real date. Convert it before any date work: `to_date("Date_of_Purchase", 'DD-MM-YYYY')`. Sorting it as text gives the wrong order.
3. **There is no foreign key between the tables.** `transactions."Customer_ID"` matches `customers."Customer_ID"`, but the database does not enforce it, so check it yourself.
4. **Row Level Security is on with no policies.** The MCP connector works fine. If you ever get zero rows, that is a permissions problem, not an empty table. Table-listing metadata can show stale row counts — confirm with a live `count(*)` if unsure.

## Inputs

- `output/schema.json` (from Agent 1).
- The Supabase data directly (read-only queries).
- Anything Gary has placed in `input/`.

## Steps

1. Revenue is `("Price" * "Units") - "Discounts"`. State this formula in your summary so Gary knows exactly what was counted.
2. About half the transactions in this data are marked `Returned`. Decide whether returned transactions count toward revenue, state which you chose and why, and show the total revenue both ways (including returns vs. excluding returns) so Gary can see the difference.
3. Report: total revenue, total transactions, how many distinct customers bought, average order value, and return rate.
4. Break revenue down by `Product_Category`, by `Purchase_Channel`, and by `Mode_of_Payment`.
5. Show revenue by month, using `to_date("Date_of_Purchase", 'DD-MM-YYYY')` to group correctly — so the trend is in real date order, not text order.
6. List the top 10 products by revenue.
7. Group customers by age band and by `Location`.
8. Count how many customers bought exactly once versus more than once.
9. Flag anything that looks wrong: nulls in key fields, zero or negative prices, discounts larger than the sale amount.
10. If `input/` has any questions from Gary, answer them here too.

## Outputs

- `output/summary.md` — plain-English write-up, with an opening summary under 150 words, then the full breakdown.
- `output/summary.json` — the same numbers in structured form, for Agent 3 to read.

`summary.json` must use **exactly** this shape — Agent 3 reads these exact keys and will
break if the shape changes:

```json
{
  "kpis": { "revenue": 0, "transactions": 0, "customers": 0, "avg_order_value": 0, "return_rate_pct": 0 },
  "revenue_by_month":    [ { "label": "2021-03", "value": 0 } ],
  "revenue_by_category": [ { "label": "Toys", "value": 0 } ],
  "revenue_by_channel":  [ { "label": "Online", "value": 0 } ],
  "revenue_by_payment":  [ { "label": "UPI", "value": 0 } ],
  "top_products":        [ { "name": "Product name", "revenue": 0, "transactions": 0 } ]
}
```

Every value must be a plain JSON number, not a string. Supabase often returns totals as
text like `"14623.75"` — cast or parse these to numbers before writing the file. No
currency symbols, no commas, inside any number. `revenue_by_month` must list every month
present in the data, in date order (earliest to latest).

## Rules

- Every number comes from a query — never estimate or guess.
- State which table (or join) each number came from.
- Never name an individual customer anywhere in either output file.
- Keep the opening summary in `summary.md` under 150 words; details can follow after it.

## Done When

- Both `output/summary.md` and `output/summary.json` exist.
- The numbers in both files match each other.
- The revenue formula and the returns decision are both stated plainly.

## Ask Me If

Stop and ask before continuing if:
- a column or table does not make sense
- the numbers look wrong, empty, or duplicated
- a connection or credential is missing
- something could change or expose data

Ask one question at a time. Give me 2 or 3 options to pick from. Wait for my answer.
