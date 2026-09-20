# Dashboard Notes — Agent 3

`dashboard/index.html` is a single self-contained file — open it by double-clicking, no
internet connection needed. It reads `output/summary.json`'s numbers baked directly into
the page as `const DATA = {...}` (no `fetch`, no external scripts, no CDN).

## What each card shows

- **KPI cards** (top row): revenue, transactions, customers, average order value, return
  rate — the five headline numbers from Agent 2's summary. Revenue and AOV are net of
  returns (see `summary.md` for why).
- **Revenue by month**: a hand-drawn SVG line from March 2021 to March 2023, labelled with
  the first and last month underneath. No axis gridlines or numeric labels on the line
  itself — it's meant to show the shape of the trend, not exact values (the KPI cards and
  underlying data cover exact numbers).
- **Revenue by Product Category**: bar chart, one row per category, bar width scaled to
  the largest category (Toys).
- **Revenue by Purchase Channel**: bar chart, In-store vs. Online.
- **Revenue by Mode of Payment**: bar chart across the five payment methods.
- **Top 10 Products by Revenue**: a plain table with revenue and transaction count per product.

## What's deliberately not here

No customer names, emails, or phone numbers anywhere in the file — the dashboard only
ever shows aggregated counts and revenue totals, matching the project's PII rule. No
Supabase keys or connection details either; the page has no live connection to anything.

## Self-check performed before calling this done

- Searched the file for `<script src=` and `fetch(` — neither appears anywhere.
- Confirmed `DATA` contains all six required keys (`kpis`, `revenue_by_month`,
  `revenue_by_category`, `revenue_by_channel`, `revenue_by_payment`, `top_products`) and
  every one has at least one item.
- Confirmed `revenue_by_month` has 25 entries in the dashboard, matching the 25 entries in
  `output/summary.json`.
- Confirmed every key the drawing JavaScript reads matches the DATA keys exactly (same
  spelling, checked by hand against the six keys above).
- Each visual is wrapped in its own `try/catch`; if a section's data were ever missing or
  malformed, that one card shows a red "could not be drawn" message instead of the whole
  page failing or staying blank.

**One limitation to flag:** I couldn't physically open the file in a browser to eyeball it
in this environment (no browser access to local files here), so I verified correctness
programmatically instead — parsing the embedded `DATA` object and checking structure,
key names, and item counts match `summary.json` exactly. If you spot anything visually off
when you open it yourself, let me know and I'll fix it directly rather than guessing.
