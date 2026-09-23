# Deploy Report — Agent 4

## What was checked before deploying

- `dashboard/index.html` renders correctly — verified structurally by Agent 3 (all six
  data sections present, no external dependencies) and confirmed again here.
- Scanned `dashboard/` for Supabase keys, API keys, secrets, tokens, `.env` references,
  and any `Customer_Name`/`Customer_Email`/`Customer_Number` values. **Nothing found.**
  Scanned the whole project for `.env*` files — none exist.
- Confirmed with Gary: project name **"ECommerce- Data-Agent"**, visibility **Public**.

**Project name note:** Vercel project names only allow lowercase letters, numbers, and
hyphens (no spaces or capitals), so `ECommerce- Data-Agent` was deployed as
`ecommerce-data-agent`. Flagging this rather than silently picking something different —
let me know if you want it renamed.

## Deployment

- Deployed through the Vercel MCP connector only — no CLI, no npm.
- Target: production.
- Vercel account has no teams, so this deployed to the personal account.

## Live link

**https://ecommerce-data-agent.vercel.app**

(A second, auto-generated alias also points to the same deployment:
`ecommerce-data-agent-axionsec.vercel.app`.)

## Public access

Vercel's account-level Deployment Protection ("Vercel Authentication") was on by default
and was blocking anonymous visitors — the first check of the live link returned a Vercel
login page instead of the dashboard, even though nothing about this specific project had
protection configured. Since Gary asked for the link to be public, I turned that setting
off for this project (`ssoProtection` set to disabled) and re-verified: the live page now
returns the actual dashboard content to an unauthenticated fetch, no login required.

## Verification

Fetched the live URL after deployment. It returns HTTP 200, the correct page title
("eCommerce Reporting Dashboard"), and all five section headers (KPI cards, monthly
trend, category/channel/payment breakdowns, top products table) — matching the local
file's structure. The chart content itself is drawn by JavaScript at page load (same as
the local file), so a plain HTTP fetch shows empty section containers; this is expected
and matches how the local `dashboard/index.html` behaves too. Recommend opening the link
in an actual browser to see the filled-in charts, same as with the local file.

## Status

**Live and public.** Deployment state: READY.

## Update — 2026-09-21

The original link (`ecommerce-data-agent.vercel.app`) broke: Gary connected that same Vercel
project to his personal GitHub repo `garynair/garynair` (for an unrelated LinkedIn/portfolio
purpose), and every push to that repo's `main` branch auto-redeployed the project with that
repo's content instead of the dashboard — which doesn't serve anything at `/`, hence the 404.

Fix: rather than trying to disconnect the git integration (no disconnect action is exposed
through the Vercel MCP tools available here — only the Vercel CLI supports `vercel git
disconnect`), a fresh, git-unconnected project was created and the same static dashboard
file deployed to it directly.

**New live link: https://ecommerce-analytics-dashboard-brown-xi.vercel.app**

This project has no git repo attached, so nothing else can silently overwrite it again.
`ecommerce-data-agent.vercel.app` remains connected to `garynair/garynair` and will keep
reflecting whatever that repo deploys — treat that domain as no longer the dashboard's home.
