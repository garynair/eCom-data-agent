# Agent 4 — Put It Online

## Purpose

Deploy the dashboard to Vercel using the Vercel connector in Claude, only after Gary
confirms and only once everything is clean of personal data and credentials.

## The database (facts you need — copied from CLAUDE.md so this file works on its own)

Two tables, connected through the Supabase MCP connector, database `eCommerce`, schema `public`:

- `public.customers` — 1,000 rows, primary key `Customer_ID`.
  Columns: `Customer_ID`, `Customer_Name`, `Customer_Email`, `Customer_Number`, `Age`, `Gender`, `Location`.
- `public.transactions` — 3,000 rows, primary key `Transaction_ID`.
  Columns: `Transaction_ID`, `Date_of_Purchase`, `Customer_ID`, `Product_Category`,
  `Product_Name`, `Units`, `Price`, `Discounts`, `Returned`, `Mode_of_Payment`, `Purchase_Channel`.

Note: `public.dsm` also exists (3,000 rows, same columns as `transactions`) — it's a
redundant/staging table, not part of this pipeline.

Four things that matter for this project even though this agent doesn't query the database directly:

1. **Mixed-case column names** exist upstream — always double-quoted in any query that produced this data.
2. **`Date_of_Purchase` is text in `DD-MM-YYYY` format** upstream — already converted with `to_date(...)` by earlier agents.
3. **No foreign key between the tables** — the `Customer_ID` link is real but unenforced; Agent 1 already checked it.
4. **Row Level Security is on with no policies** — the connector works fine through this project.

## Inputs

- The `dashboard/` folder (specifically `dashboard/index.html`, built by Agent 3).
- The Vercel connector.

## Steps

1. Check that `dashboard/index.html` opens and every chart renders. Stop if it does not —
   send it back rather than deploying a broken dashboard.
2. Search `dashboard/` for any API keys, `.env` files, connection strings, or customer
   names, emails, and phone numbers. Stop and report immediately if you find any — do not
   deploy.
3. Ask Gary for the project name and whether the link should be public. Wait for his
   answer before doing anything else.
4. Deploy to Vercel through the Vercel connector only. Do not use the Vercel CLI or npm.
   If the connector isn't connected, stop and tell Gary what to connect — do not guess at
   credentials or try a workaround.
5. Open the live link and check it looks the same as the local file — same KPI cards,
   same charts, same top products table.

## Outputs

- `output/deploy_report.md` — the live link, deployment timestamp, and what was checked.

## Rules

- Never deploy before Gary explicitly confirms the project name and visibility.
- Never upload `.env` files or anything from `input/`.
- Never fabricate or guess at credentials — if something's missing, say what's missing and stop.

## Done When

- The live link works and matches what Gary saw locally.

## Ask Me If

Stop and ask before continuing if:
- a column or table does not make sense
- the numbers look wrong, empty, or duplicated
- a connection or credential is missing
- something could change or expose data

Ask one question at a time. Give me 2 or 3 options to pick from. Wait for my answer.
