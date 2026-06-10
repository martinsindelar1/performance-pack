# Edit Locations Reference

Every site in `template.html` that needs updating when a new month is added. Use `str_replace` against these exact strings.

## 1. `<title>` tag

```
<title>Complex™ — Performance Pack · Jan–<LAST_MONTH> <YEAR></title>
```

Update `LAST_MONTH` to the new latest month.

## 2. Header meta

Search for:
```html
<span class="pulse"></span> Live · Auto-updated <DD MMM YYYY><br/>
```

Update the date to today (or the report run date).

## 3. Title block subtitle

```html
<div class="subtitle">Q1 + Apr + May (partial) 2026 · Channel-mix view · Founder readout</div>
```

Update the period text. Use `(partial)` if the new month isn't complete.

## 4. Section 1 header

```html
<h2>Top-line · Jan–<LAST_MONTH> <YEAR></h2>
<span class="section-meta"><N> months · ...</span>
```

## 5. KPI hero cards (8 cards)

Located in `<div class="kpi-grid">`. Each card has:
```html
<div class="kpi-label">{LABEL} <span class="kpi-trend trend-{up|down|flat}">{DELTA}</span></div>
<div class="kpi-value">{VALUE}</div>
<div class="kpi-sub">{SUBTITLE}</div>
```

These show **cumulative YTD** values, not just the new month. Recompute from scratch when adding a month.

Default 8 cards:
1. Order Revenue (cumulative)
2. Blended ROAS (average or latest — note in subtitle)
3. New Customer CPA (latest month value, with YTD trend)
4. Klaviyo Revenue (cumulative)
5. Total Orders (cumulative)
6. Ad Spend (cumulative)
7. Net Profit (cumulative)
8. LTV/CPA (latest)

Pick whichever 8 KPIs are most meaningful to highlight. You can swap one out (e.g., drop "vs FY26 Budget" for "LTV/CPA") if the story has changed.

## 6. Executive readout cards (3 cards)

Located in `<div class="exec-grid">`:
- `<div class="exec-card pos">` — Positives
- `<div class="exec-card neg">` — Negatives
- `<div class="exec-card opp">` — Opportunities

Rewrite all bullets from scratch. See `narrative-patterns.md` for guidance.

## 7. Channel cards (4 cards)

`<div class="channel fb|google|tiktok|klaviyo">`. Each has 5 metrics + a takeaway. Update:
- `Spend Jan–<LAST_MONTH>`
- `ROAS avg` (running average)
- Latest month's CPC/CTR/CPA
- `% of ad mix`
- Takeaway sentence

## 8. JavaScript data arrays

All chart data is in the `<script>` at the bottom. Find each chart block by its comment header (`// ===== N. CHART NAME =====`) and update its `data: [...]` arrays.

### Months array (used by every chart)
```js
const months = ['Jan', 'Feb', 'Mar', 'Apr'];
```
Append the new month. Use `'May*'` (with asterisk) for partial months.

### Chart 1 — Spend Mix Doughnut
```js
data: [<META_SPEND>, <GOOGLE_SPEND>, <TIKTOK_SPEND>],
```
Single cumulative array of 3 values.

### Chart 2 — ROAS by Channel
4 line datasets, each with one number per month:
```js
{ label: 'Meta',    data: [6.41, 6.37, 6.93, 5.81], ... },
{ label: 'Google',  data: [21.24, 22.32, 13.73, 18.45], ... },
{ label: 'TikTok',  data: [7.79, 7.71, 2.10, 12.77], ... },
{ label: 'Blended', data: [5.45, 5.35, 5.37, 5.25], ... }
```

### Chart 3 — Revenue Composition
5 stacked datasets:
```js
{ label: 'Klaviyo (Owned)',     data: [...] },
{ label: 'Meta-attributed',     data: [...].map(v => v*0.45) },
{ label: 'Google-attributed',   data: [...].map(v => v*0.18) },
{ label: 'TikTok-attributed',   data: [...].map(v => v*0.13) },
{ label: 'Direct / Other',      data: [...] }  // computed inline
```

The "Direct / Other" array is computed inline as:
```js
total_revenue - klaviyo - meta_conv*0.45 - google_conv*0.18 - tiktok_conv*0.13
```
Update both the raw conv-value arrays and the inline computation.

### Chart 4 — Klaviyo Flows vs Campaigns
```js
{ label: 'Flows',     data: [...] },
{ label: 'Campaigns', data: [...] }
```

### Chart 5 — CPA Trend (single line)
```js
data: [623, 728, 721, 813],
```

### Chart 6 — New vs Returning Revenue
```js
{ label: 'New Customer Rev',       data: [...] },
{ label: 'Returning Customer Rev', data: [...] }
```

### Chart 7 — Conversion Rate
```js
data: [1.88, 1.70, 1.96, 2.75],
```

### Chart 8 — Traffic Volatility
```js
{ label: 'Users',  data: [...] },
{ label: 'Orders', data: [...] }
```

### Chart 9 — Discount Overlay (combo chart)
3 datasets — bar revenue + line AOV + line conversion rate.
Also update the events tooltip dict:
```js
const events = { Jan: 'NEW YEAR SALE — 15%', Feb: 'FOUNDER DAYS — 20%', ... };
```

## 9. Data table

`<table class="data-table">`. Update:
1. Add a `<th class="num">{Month} {Year}</th>` to the `<thead>`
2. Add a `<td class="num">{value}</td>` to **every** row in `<tbody>`
3. Update the final delta column (`Δ Jan→{LastMonth}`)

Default table has ~21 rows. If you're not sure of a value, leave the cell with a placeholder rather than guess.

## 10. Discount calendar overlay text (section 5 notes)

Find:
```html
<div class="notes-box">
  <h4>Reading the overlay</h4>
  <ul>
    <li>...</li>
  </ul>
</div>
```

Rewrite all bullets to reflect the latest period.

## 11. Action plan cards (section 7)

Three cards (urgent / opportunity / steady). Update:
- Action title
- Description body
- Owner / KPI / Deadline meta

These should reflect what to do **next month** based on the new data, not be left from previous months.

## Sanity-check list before saving

Run through this before delivering the HTML:

- [ ] `months` array length matches every chart's data array length
- [ ] All KPI hero values are cumulative (not single-month)
- [ ] Executive readout doesn't contain stale month references
- [ ] Data table has the right number of `<td>` cells per row
- [ ] Discount overlay `events` dict has an entry for every month in `months`
- [ ] Partial months are marked with `*` in chart labels AND noted in section meta
- [ ] File ends with `</html>`
