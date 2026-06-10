---
name: complex-performance-pack
description: Update the Complex Supplements Performance Pack HTML dashboard with new monthly marketing data. Use this skill whenever the user uploads Triple Whale exports (Pins reports, Monthly Data, Summary screenshots), Shopify CSV exports (Sales by country, Net sales, New vs returning), Klaviyo exports/screenshots, UppPromote data, or asks to "update the performance pack", "add [month] to the dashboard", "refresh the report", "build the new monthly readout for Complex", or anything similar about Complex Supplements marketing reporting. Trigger this even when the user just says "here's the new data" with attached files matching these source types.
---

# Complex Performance Pack — Monthly Update Skill

This skill keeps the Complex Supplements **Performance Pack** dashboard (HTML) in sync with the latest monthly marketing data. It accepts data from multiple sources, cross-checks values, then surgically edits the existing HTML template to add or update a month.

## When to use

Trigger this skill when the user:
- Uploads Triple Whale exports (Pins report Excel, Monthly Data Excel, Summary screenshot)
- Uploads Shopify CSV exports (Total sales by billing location, Net sales over time, New vs returning customers)
- Uploads Klaviyo data (Excel or screenshot showing Campaigns / Flows / Database)
- Uploads UppPromote (Influencer) Excel or screenshot
- Says anything like "update the performance pack", "add [month] data", "build the [month] report", "refresh the dashboard"
- Provides a mix of the above with intent to update the Complex Supplements dashboard

## Inputs (any combination)

| Source | Format | Key metrics extracted |
|---|---|---|
| Triple Whale — Pins report | `.xlsx` (one per month) | Total Sales, Net Profit, Orders, ROAS, NCPA, LTV/CPA, channel CPA/ROAS, Returning Rev |
| Triple Whale — Monthly Data | `.xlsx` (one with all months) | Same as above, in long format |
| Triple Whale — Summary | Screenshot | Total Sales, Net Profit, NCPA, LTV/CPA, ROAS, channel CPAs/ROAS |
| Shopify — Sales by billing country | `.csv` | Revenue per country (for expansion tracker; reference only) |
| Shopify — Net sales over time | `.csv` | Daily net sales (for cross-check) |
| Shopify — New vs returning | `.csv` | New customer count vs returning |
| Klaviyo | `.xlsx` or screenshot | Campaigns rev, Flows rev, Database size, signups, unsubs |
| UppPromote / Influencer | `.xlsx` or screenshot | Total sales, orders, commission, top affiliates |

The skill is forgiving about which inputs are present. With just one or two sources it does its best and flags what was missing. The Pins report Excel is the most complete single source and should be preferred when available.

## Output

A single self-contained HTML file with the new month added/updated across all 9 charts, the KPI hero, the executive readout, the data table, and the discount overlay.

This skill runs **locally inside the `performance_pack` git repo** and persists every run:

- The finished dashboard is written to `artefacts/performance-pack-<YYYY-MM-DD>.html` (the date the skill is run). Every run is kept here as a dated archive of previous versions.
- The same file is copied to `index.html` at the repo root — this is the canonical file served by GitHub Pages at `https://martinsindelar1.github.io/performance-pack/`.
- The change is committed and pushed to `origin/main`, which auto-redeploys the public site.

## Workflow

Follow these steps in order. Don't skip the cross-check step — the whole value of this skill is that the data is verified before it goes into the dashboard.

### 1. Start from the current dashboard

Use the **most recent existing dashboard** as your working base so that all previously-added months are preserved (the editing approach is additive — you append the new month to existing data arrays):

- **Preferred base:** `index.html` at the repo root. It is the latest cumulative version (all prior months).
- If for some reason `index.html` is missing, fall back to the newest file in `artefacts/`, and only if that is also empty fall back to `assets/template.html` (the canonical empty structure for a brand-new pack).

Copy the chosen base to a working file (e.g., `./_working.html`). Do **not** regenerate the HTML from scratch.

Then **view the working file once** to confirm the current state: which months are already present, which JS data arrays exist, which KPI cards are populated. This gives you the exact list of edit sites.

### 2. Identify the target month

The user usually says it explicitly ("add May", "build April"). If not, infer from the filenames or the date columns inside the uploaded files.

Determine whether the month is **partial** (e.g., May 1–26 of 31 days). If partial, you must mark it with `*` in chart labels and add an asterisked note in the section meta. Do not pro-rate the numbers — show actuals, label clearly.

### 3. Read and extract data

Use Python (pandas) for all Excel/CSV files. Don't try to eyeball numbers — pull them programmatically. See `references/data-extraction.md` for the exact column mappings per source type.

For screenshots, transcribe the visible numbers carefully and ask the user to confirm any value you're unsure about.

Common pitfalls to watch for:
- **Pins reports may have date-shaped values where ROAS/LTV-CPA should be** (Excel bug where `5.05` becomes `2026-05-05`). Detect by checking if a metric expected to be a small float is a `datetime`. Recover the value from the % Change column (`current = previous × (1 + change%)`).
- **TW "New Customers" metric is reported as a percentage** (e.g., `0.39` means 39% of all customers were new), not a count. To get absolute new customer count, multiply by `Orders` and round.
- **Pins shows Total Sales (Shopify-style)**, the Monthly Data sheet shows **Order Revenue (TW)**. These differ by a few %. The dashboard uses Order Revenue when available, Total Sales as fallback.
- **Subscription / skio fields** appear in Pins reports — `% Subscription Order Revenue` and `Total Active Subscribers`. These weren't in the template originally; surface them in the executive readout if they're non-zero.

### 4. Cross-check sources

If multiple sources cover the same metric, compare them and report any delta >3% to the user before writing. Typical comparisons:
- TW Order Revenue vs. Shopify Total Sales (expect 1–3% diff)
- TW Orders vs. Shopify Orders (should match within ±1%)
- TW New Customers vs. Shopify First-time customers (TW uses pixel definition — often 30–50% higher)

Don't fail if they disagree — just log it and pick TW for the dashboard (the template's existing data is TW-sourced).

### 5. Edit the template

Use `str_replace` edits. The template has 9 chart data arrays + a months array + KPI cards + an executive readout + a data table. See `references/edit-locations.md` for the exact strings to find and replace for each component.

**Critical**: every data array must end up with the same length as `months`. If you add a 5th month, every `data: [...]` array under "// ===== N. CHART =====" comments needs a 5th element.

When updating cumulative KPI hero numbers (top of the page), recompute them from scratch — don't increment the displayed value.

### 6. Rewrite the executive readout

The Positives / Negatives / Opportunities cards should reflect what changed in the new month. Don't keep stale narrative from previous months — rewrite each bullet to reflect current trends. Use the patterns in `references/narrative-patterns.md` as a starting point.

### 7. Validate

Quick checks before delivering:
- All canvas IDs still referenced by Chart.js code
- Months array length equals every chart's data array length
- HTML still ends with `</html>`
- File size sanity check (should be 55–80KB depending on month count)

### 8. Archive, update index, deploy

The working file is now the finished dashboard. Persist it in the repo and publish it. All paths are relative to the repo root (`/Users/x/Code/performance_pack`).

1. **Archive the run.** Create the `artefacts/` folder if it doesn't exist, and copy the finished working file there with today's date in the name:
   ```bash
   mkdir -p artefacts
   cp _working.html "artefacts/performance-pack-$(date +%F).html"
   ```
   `date +%F` gives `YYYY-MM-DD`. If a file for today already exists, overwrite it (a re-run on the same day replaces that day's archive).

2. **Update the live index.** Copy the same file to `index.html` at the repo root — this is what GitHub Pages serves:
   ```bash
   cp _working.html index.html
   ```
   Then remove the temporary working file (`rm -f _working.html`).

3. **Commit and push** (auto-push is intended — no confirmation needed):
   ```bash
   git add -A
   git commit -m "Update performance pack: <reporting period> (run <YYYY-MM-DD>)"
   git push origin main
   ```
   Use the `gh`-authenticated HTTPS remote already configured on this repo. If `git`/`gh` isn't on PATH, `gh` is installed at `~/bin/gh`.

4. **Confirm to the user**: report the archived filename, that `index.html` was updated, the commit hash, and remind them the public site will refresh within ~1 minute at `https://martinsindelar1.github.io/performance-pack/`.

## Reference files

- `references/data-extraction.md` — exact pandas patterns for each input type, column mappings, gotchas
- `references/edit-locations.md` — every string to find in the template for each chart and KPI
- `references/narrative-patterns.md` — phrasing patterns for the executive readout

## Assets

- `assets/template.html` — canonical Performance Pack HTML. Copy to workspace, edit, output.

## Don't

- Don't regenerate the HTML from scratch — always start from the current `index.html` (or the template on a first run)
- Don't hand-write the dated filename — derive it from `date +%F` so the archive stays consistent
- Don't skip the push — the whole point is that the public GitHub Pages site reflects the latest run
- Don't pro-rate partial months into "full month estimates" inside the chart data — use actuals and label with `*`
- Don't invent metrics not in the source data — if the user didn't give you Klaviyo data, leave Klaviyo flat from the previous month and tell them
- Don't quietly fix the Excel date bug without telling the user which values you reconstructed
