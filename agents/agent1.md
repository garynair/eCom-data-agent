# Agent 1 — Explore the Database

## Purpose

Work out what is actually in the database before anyone summarizes it. This is the
groundwork every later agent relies on — get it right and be honest about what's messy.

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
4. **Row Level Security is on with no policies.** The MCP connector works fine. If you ever get zero rows, that is a permissions problem, not an empty table — say that explicitly, don't report it as "no data." Table-listing metadata can show stale row counts — confirm with a live `count(*)` if unsure.

## Inputs

- The Supabase connection (MCP connector), database `eCommerce`, schema `public`.

## Steps

1. List both tables with their columns, data types, and row counts.
2. Check the `Customer_ID` link: count transactions whose `"Customer_ID"` has no matching row in `tbl_customers`. Report that number plainly (e.g. "47 transactions reference a Customer_ID that doesn't exist in tbl_customers").
3. Report how many customers actually have at least one transaction, and the average and highest number of transactions per customer.
4. For `Gender`, `Location`, `Product_Category`, `Mode_of_Payment`, and `Purchase_Channel`: list the distinct values and the count of rows for each.
5. Count nulls in every column, in both tables.
6. Report the earliest and latest purchase date, using `to_date("Date_of_Purchase", 'DD-MM-YYYY')` — never sort or compare the raw text.

## Outputs

- `output/schema_report.md` — plain-English write-up for Gary.
- `output/schema.json` — the same findings in structured form, for Agent 2 and Agent 3 to read. Include: table/column/type/row-count info, the orphan-transaction count, customers-with-transactions stats, distinct-value counts per listed column, null counts per column, and the earliest/latest purchase date.

## Rules

- Read only. Never `INSERT`, `UPDATE`, `DELETE`, `DROP`, or `ALTER`.
- Quote every column name in every query.
- For `Customer_Name`, `Customer_Email`, `Customer_Number`: report null counts and nothing else. Never report, print, or write an actual value from these columns anywhere.

## Done When

- Both `output/schema_report.md` and `output/schema.json` exist.
- The `Customer_ID` link is backed by an actual orphan count from a query, not an assumption.

## Ask Me If

Stop and ask before continuing if:
- a column or table does not make sense
- the numbers look wrong, empty, or duplicated
- a connection or credential is missing
- something could change or expose data

Ask one question at a time. Give me 2 or 3 options to pick from. Wait for my answer.
