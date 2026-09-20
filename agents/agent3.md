# Agent 3 — Build the Dashboard

## Purpose

Show the summary as charts in a single HTML file Gary can open on his laptop — no
internet, no build step, nothing else required.

## The database (facts you need — copied from CLAUDE.md so this file works on its own)

Two tables, connected through the Supabase MCP connector, database `eCommerce`, schema `public`:

- `public.customers` — 1,000 rows, primary key `Customer_ID`.
  Columns: `Customer_ID`, `Customer_Name`, `Customer_Email`, `Customer_Number`, `Age`, `Gender`, `Location`.
- `public.transactions` — 3,000 rows, primary key `Transaction_ID`.
  Columns: `Transaction_ID`, `Date_of_Purchase`, `Customer_ID`, `Product_Category`,
  `Product_Name`, `Units`, `Price`, `Discounts`, `Returned`, `Mode_of_Payment`, `Purchase_Channel`.

Note: `public.dsm` also exists (3,000 rows, same columns as `transactions`) — it's a
redundant/staging table, not part of this pipeline. Ignore it.

Four things that will break SQL if ignored (this agent shouldn't need to query directly,
but keep these in mind if you do spot-check anything):

1. **Mixed-case column names** — always double-quote them.
2. **`Date_of_Purchase` is text in `DD-MM-YYYY` format** — convert with `to_date("Date_of_Purchase", 'DD-MM-YYYY')` before any date work.
3. **No foreign key between the tables** — the `Customer_ID` link is real but unenforced.
4. **Row Level Security is on with no policies** — zero rows means a permissions problem, not an empty table. Table-listing metadata can also show stale row counts.

## Inputs

- `output/summary.json` (from Agent 2).
- `output/schema.json` (from Agent 1) — for context only, not required for the charts.

## Steps

1. Build one file: `dashboard/index.html`. Everything goes inside it — HTML, CSS,
   JavaScript, and the numbers. No build step, no server, no npm.
2. Do not use any charting library and do not load anything from a CDN or the internet.
   If a script fails to load, every chart goes blank. Draw the charts yourself with plain
   HTML, CSS, and SVG:
   - **Bar charts:** one row per item, a `div` bar whose width is a percentage of the
     largest value in that set, with the label and the number shown beside it.
   - **Monthly trend:** an SVG polyline, with the first and last month labelled underneath it.
3. Do not use `fetch()` and do not load `summary.json` from disk. Browsers block that when
   a file is opened by double-clicking, and the page comes out blank. Instead, copy the
   full contents of `output/summary.json` directly into the file as:
   `const DATA = { ... };`
   Write out every value. No placeholders, no `...`, no comments like "add the remaining
   months here."
4. Read only these keys from `DATA`: `kpis`, `revenue_by_month`, `revenue_by_category`,
   `revenue_by_channel`, `revenue_by_payment`, `top_products`.
5. Build these six visuals:
   - KPI cards: revenue, transactions, customers, average order value, return rate.
   - Revenue by month as a line (SVG polyline).
   - Revenue by `Product_Category` as bars.
   - Revenue by `Purchase_Channel` as bars.
   - Revenue by `Mode_of_Payment` as bars.
   - Top products as a table.
6. Wrap the code for each visual in its own `try/catch`. If a visual fails or its data is
   empty, write a red message inside that card saying what's missing. A card must never be
   silently blank, and one broken chart must never stop the others from rendering.
7. Before saying you're done, read `dashboard/index.html` back and check all of these:
   - there is no `<script src=` tag and no `fetch(` anywhere in the file
   - `DATA` contains all six keys, and every list in it has at least one item
   - `revenue_by_month` has the same number of months as `output/summary.json`
   - every key the drawing code reads exists in `DATA` with the same spelling
   Fix anything that fails, then check again. If you can open the file in a browser, do
   that too and confirm every card shows data.

## Outputs

- `dashboard/index.html` — the single-file dashboard.
- `output/dashboard_notes.md` — plain-English notes on what each visual shows.

## Rules

- One file only. No external scripts, no CDN, no `fetch`.
- No Supabase keys, connection strings, or credentials anywhere in the file.
- No customer names, emails, or phone numbers anywhere in the file.
- Must work by double-clicking the file, fully offline.

## Done When

- All checks in Step 7 pass.
- Double-clicking `dashboard/index.html` opens a page with every card filled in.

## Ask Me If

Stop and ask before continuing if:
- a column or table does not make sense
- the numbers look wrong, empty, or duplicated
- a connection or credential is missing
- something could change or expose data

Ask one question at a time. Give me 2 or 3 options to pick from. Wait for my answer.
