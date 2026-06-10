# Narrative Patterns Reference

How to write the executive readout (Positives / Negatives / Opportunities) so it sounds like the Complex voice and reflects actual signals in the data.

## Voice & style

- Direct, founder-facing tone — no fluff, no consultant-speak
- Numbers come first, judgment second
- One bullet = one claim with a specific number
- Bold a key phrase per bullet to make scanning easy
- Use `<span class="num">XYZ</span>` for the most important number in each bullet
- Czech and English both acceptable — match what the rest of the report uses (template defaults to English)
- Avoid hedge words: "potentially", "may indicate", "could be" — say what the data shows

## Bullet templates

### Positives (5 bullets)

What to surface:
- **Biggest growth metric** of the month with the YoY or MoM number
- **Channel that improved** (ROAS up, CPA down, conversions up)
- **Compounding YTD trend** (cumulative net profit, cumulative orders)
- **New lever** that's starting to work (subscriptions, referral, a new market, a new flow)
- **Surprise win** — something nobody was tracking that lit up

Templates:
```
<strong>{Channel/Metric} {direction}</strong> — {number} {context}. {Implication in 5 words}.

<strong>{Metric} hit {value}</strong> in {month} (+{change}% MoM). {What it suggests}.

<strong>Net Profit {value} YTD</strong> (+{change}% YoY). {What's driving it}.
```

### Negatives (5 bullets)

What to surface (be honest, no spin):
- **The #1 red flag** — make it bold and lead with it
- **A trend reversing** (ROAS sliding, CPA climbing)
- **A channel that broke** (CTR collapse, ROAS halved)
- **Acquisition health** — almost always a problem to flag
- **Budget vs plan delta** if you have plan numbers

Templates:
```
<strong>New Customer CPA hit <span class="num">{value} CZK</span></strong> in {month} (+{change}% from Jan's {baseline}). {Interpretation}.

<strong>{Channel} ROAS {dropped/slid}</strong> to {value} ({direction} {change}% MoM). {What might be causing it}.

<strong>{Metric} softening across the board</strong> — {evidence}. {Implication}.
```

### Opportunities (5 bullets)

What to surface:
- **A playbook that worked once and could be replayed** (e.g., the April 2+1 mechanic)
- **An under-invested channel** with strong unit economics
- **A new product/feature/program** that just started
- **A behavior that's compounding** (returning revenue mix, AOV climb)
- **A timing window** (the next event, the next launch, the next promo)

Templates:
```
<strong>Replicate {past_winner}:</strong> {context}. {Next window} is the next opportunity.

<strong>{Channel/Lever} headroom:</strong> {current state}. {What unlocking it would do}.

<strong>{New thing} just launched</strong> — {early signal}. {Why it matters}.
```

## Worked examples (from May 2026 update)

### Positives
```
<li><strong>Q1 momentum was strong</strong> — revenue grew Jan→Apr every month, cumulative 36.3M CZK YTD (+40% YoY).</li>
<li><strong>May AOV jumped to 1,498 CZK</strong> (+17% MoM) — fewer but higher-value orders. Returning customer mix at 65%.</li>
<li><strong>Net Profit 7.71M YTD</strong> (+120% YoY). Returning customers 62% of base — loyalty engine solid.</li>
<li><strong>Subscriptions launching</strong> — active subscribers 43→159 (skio), 2.67% of order revenue in May. New recurring lever.</li>
<li><strong>April peak:</strong> CR hit 2.75%, TikTok ROAS 12.77×, Net Margin 20% — proof the model works when creative is fresh.</li>
```

### Negatives
```
<li><strong>New Customer CPA hit <span class="num">903 CZK</span></strong> in May (+45% from Jan's 623). Acquisition keeps getting more expensive.</li>
<li><strong>May softening across the board</strong> — even pro-rated, run-rate (~5.9M full month) is below Jan–Apr's ~8M floor.</li>
<li><strong>Blended ROAS dropped to 4.80</strong> in May, lowest of the year. All channels cooled simultaneously.</li>
<li><strong>Google ROAS volatile</strong> (22→14→18→14×) and CPA up +38% to 87 CZK. Needs structural fix, not just bid tweaks.</li>
<li><strong>Klaviyo softened</strong> — May total ~2.09M (run-rate below Apr's 3.53M). Flows still the weak spot.</li>
```

### Opportunities
```
<li><strong>Subscriptions (skio) just launched</strong> — 159 active subs, 2.67% of May revenue. Biggest untapped LTV lever for H2.</li>
<li><strong>Replicate April playbook:</strong> 2+1 mechanic + fresh creative drove CR to 2.75%. Summer Prep (Jun 22–28) is the next window.</li>
<li><strong>AOV is rising</strong> (1,498 in May) — bundles & quantity breaks working. Push harder on cart upsell.</li>
<li><strong>Returning revenue carries the business</strong> (62% of customers). Referral + Pro program could compound this.</li>
<li><strong>May dip = creative reset window</strong> before summer. Use the soft month to test new hooks cheaply.</li>
```

## Things to avoid

- Don't repeat the same number across multiple bullets — each bullet earns its own insight
- Don't write "we should consider..." — write "test X" or "do Y"
- Don't bury the lead — biggest signal goes first
- Don't include a bullet just to fill 5 — 3 strong bullets > 5 weak ones (the template tolerates fewer)
- Don't mention metrics the user didn't give you — don't fabricate ad-spend breakdowns when you only had Pins data
