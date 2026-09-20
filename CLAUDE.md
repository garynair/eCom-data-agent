# eCommerce Reporting Pipeline — Project Rules

This file applies to every agent in this project. Read it before doing anything else.

## What this project is

A four-agent pipeline that explores, summarizes, and visualizes eCommerce data from
Supabase (database: `eCommerce`, schema: `public`), then optionally deploys the result
to Vercel. Agents run one at a time, only when Gary says "Activate Agent N."

## The database (facts every agent needs)

Two tables, connected through the Supabase MCP connector:

- `public.customers` — 1,000 rows, primary key `Customer_ID`.
  Columns: `Customer_ID`, `Customer_Name`, `Customer_Email`, `Customer_Number`, `Age`, `Gender`, `Location`.
- `public.transactions` — 3,000 rows, primary key `Transaction_ID`.
  Columns: `Transaction_ID`, `Date_of_Purchase`, `Customer_ID`, `Product_Category`,
  `Product_Name`, `Units`, `Price`, `Discounts`, `Returned`, `Mode_of_Payment`, `Purchase_Channel`.

Note: there is also a `public.dsm` table (3,000 rows, same columns as `transactions`) —
it's a redundant/staging table, not part of this pipeline. Ignore it unless told otherwise.

Four things that will break queries if ignored:

1. **Mixed-case column names.** Always double-quote them: `select "Customer_ID" from public.customers;`. Unquoted queries fail or silently hit the wrong (lowercased) column.
2. **`Date_of_Purchase` is text, in `DD-MM-YYYY` format**, not a real date. Convert before any date math or sorting: `to_date("Date_of_Purchase", 'DD-MM-YYYY')`. Sorting it as text gives the wrong order.
3. **No foreign key between the tables.** `transactions."Customer_ID"` matches `customers."Customer_ID"` in practice, but the database does not enforce it — check the link yourself, don't assume every transaction has a matching customer.
4. **Row Level Security is on with no policies.** The MCP connector works fine through this project. If a query ever returns zero rows unexpectedly, that's a permissions problem, not an empty table — say so, don't report "no data." (Table-listing metadata can also show stale row counts — confirm with a live `count(*)` if in doubt.)

## Rules for every agent

- **Read only.** Never `INSERT`, `UPDATE`, `DELETE`, `DROP`, or `ALTER`, on any table, ever.
- Always quote column names. Always convert `Date_of_Purchase` with `to_date(...)` before using it.
- Agents hand off work through files in `output/` — not through chat, and not by talking to
  each other directly. Each agent reads only the files it needs and writes its own outputs.
- `Customer_Name`, `Customer_Email`, and `Customer_Number` are personal data. Never write the
  actual values into `output/`, the dashboard, or anything that goes online. Counts and group
  sizes only (e.g. "312 customers in the 25-34 age band" is fine; a customer's name is not).
- Explain findings in plain English. No jargon, no unexplained acronyms.
- If something is unclear — a number looks wrong, a table doesn't match expectations, a
  connection is missing — stop and ask Gary before continuing. Don't guess and move on.

## Folders

- `input/` — Gary puts questions here for the agents to answer. Agents only read this folder, never write to it.
- `output/` — every agent's results go here (reports for Gary, structured files for the next agent).
- `archive/` — old runs get moved here (not written to automatically by any agent unless asked).
- `dashboard/` — the final single-file HTML dashboard.
- `agents/` — the four agent specs (`agent1.md` through `agent4.md`).

## The four agents

1. **Agent 1 — Explore the database.** Profiles both tables, checks the `Customer_ID` link, counts nulls, reports distinct values. Writes `output/schema_report.md` and `output/schema.json`.
2. **Agent 2 — Summarize the data.** Turns the data into KPIs, trends, segments, and flags. Writes `output/summary.md` and `output/summary.json`.
3. **Agent 3 — Build the dashboard.** Turns `output/summary.json` into a single offline HTML file with hand-drawn charts (no CDN, no fetch). Writes `dashboard/index.html` and `output/dashboard_notes.md`.
4. **Agent 4 — Put it online.** Checks the dashboard is clean of PII and credentials, asks Gary for deployment details, then deploys through the Vercel connector after Gary confirms. Writes `output/deploy_report.md`.

See each `agents/agentN.md` file for exact steps, inputs, outputs, and stop conditions.
