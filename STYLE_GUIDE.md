# Style Guide — "Complex™ Terminal-Brutalist Dashboard"

> Instructions for an AI agent building UI in this visual style. Follow these rules to reproduce the exact look of the Complex™ Performance Pack. This is a **dark, editorial, terminal-brutalist** aesthetic: near-black canvas, one electric-yellow accent, heavy display type, monospace labels, hairline borders, and **zero rounded corners**.

---

## 1. Core Principles (read first)

1. **Dark, not gray.** The canvas is near-black (`#0a0a0a`). Cards are barely-lifted off it (`#131313`). Never use light backgrounds.
2. **One accent, used sparingly.** Electric yellow (`#e5ff00`) is the single brand color. Use it for emphasis, active states, key numbers, and section markers — never for large fills or body text.
3. **Sharp edges.** `border-radius` is effectively `0`. The only exceptions are tiny status pills/dots (`2px` or `50%`). Never soften card corners.
4. **Hairline borders do the work.** Structure comes from `1px solid #262626` borders, not shadows. Shadows are essentially absent.
5. **Three typefaces, strict roles.** Display = Archivo Black. Body = Space Grotesk. Labels/data/numbers = JetBrains Mono. Never mix these roles up.
6. **Labels are LOUD, small, and spaced.** Meta labels are uppercase, ~10px, mono, with wide `letter-spacing` (`0.14em`–`0.18em`). This is the signature texture.
7. **Numbers are huge and tight.** Headline figures use Archivo Black with negative letter-spacing (`-0.035em`) at 32px+.
8. **Restraint over decoration.** Subtle radial-gradient glows and a 2%-opacity grain overlay add depth. Nothing else.

---

## 2. Design Tokens

Define these as CSS variables (or your framework's token equivalent).

### Color

| Token | Value | Use |
|---|---|---|
| `--bg` | `#0a0a0a` | Page canvas |
| `--bg-elev` | `#131313` | Card / panel surface |
| `--bg-elev-2` | `#1a1a1a` | Nested surface, table headers, badges |
| `--border` | `#262626` | Default hairline border |
| `--border-bright` | `#3a3a3a` | Hover / emphasized border |
| `--text` | `#f5f5f4` | Primary text |
| `--text-dim` | `#a3a3a3` | Secondary text, labels |
| `--text-faint` | `#525252` | Tertiary text, meta keys |
| `--accent` | `#e5ff00` | **Signature electric yellow** — accents only |
| `--green` | `#4ade80` | Positive / up |
| `--red` | `#ef4444` | Negative / urgent |
| `--amber` | `#fbbf24` | Warning / medium |
| `--blue` | `#60a5fa` | Info / cool |
| `--pink` | `#f472b6` | Categorical data |
| `--purple` | `#c084fc` | Categorical data |

**Status-tint backgrounds** (for pills/badges): use the status color at low alpha, e.g. `rgba(74,222,128,0.12)` for a green tint, `rgba(239,68,68,0.12)` for red.

**Brand/channel gradients** (decorative 2px top-rules on cards):
- Meta: `linear-gradient(90deg, #1877f2, #00d4ff)`
- Google: `linear-gradient(90deg, #4285f4, #ea4335, #fbbc04, #34a853)`
- TikTok: `linear-gradient(90deg, #ff0050, #00f2ea)`
- Email/Klaviyo: `linear-gradient(90deg, #e5ff00, #34d399)`

### Typography

| Role | Family | Where |
|---|---|---|
| Display | `'Archivo Black', sans-serif` | h1, h2, KPI values, card titles |
| Body | `'Space Grotesk', sans-serif` | Paragraphs, list items, descriptions |
| Mono | `'JetBrains Mono', monospace` | Labels, badges, table data, numbers, meta |

**Google Fonts import** (put in `<head>`):
```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;500;700&family=Space+Grotesk:wght@300;400;500;600;700&family=Archivo+Black&display=swap" rel="stylesheet">
```

**Type scale & treatment:**
- `h1` — Archivo Black, `clamp(48px, 6vw, 84px)`, `line-height: 0.95`, `letter-spacing: -0.045em`
- `h2` (section) — Archivo Black, 26px, `letter-spacing: -0.025em`
- KPI value — Archivo Black, 32px, `letter-spacing: -0.035em`, `line-height: 1`
- Card title — Archivo Black, 16–18px, `letter-spacing: -0.02em`
- Body text — Space Grotesk, 13px, `line-height: 1.5`–`1.55`
- Label / meta — JetBrains Mono, 10–11px, `text-transform: uppercase`, `letter-spacing: 0.14em`–`0.18em`, color `--text-dim`
- Tiny badge — JetBrains Mono, 9px, uppercase, `letter-spacing: 0.16em`

### Spacing & Layout

- Page wrapper: `max-width: 1480px`, centered, `padding: 32px 40px 80px`.
- Grid gap between cards: `16px`. Section bottom margin: `56px`.
- Card padding: `20px`–`24px`.
- Default grids: KPIs and channels = 4 columns; exec summary = 3; charts and actions = 2. Collapse to 2 → 1 on narrow screens.

### Borders & Radius

- Standard border: `1px solid var(--border)`.
- **`border-radius: 0`** on all cards, tables, inputs, panels.
- Allowed radius: `2px` on small pills, `50%` on status dots only.
- Accent rules: a colored `3px` left border (`border-left`) marks priority on cards; a `2px` gradient strip via `::before` marks channel cards along the top.

---

## 3. Canvas Treatment

Apply to `body` to get the signature depth. Both effects are subtle — do not exaggerate.

**Radial-gradient glow** (fixed background):
```css
background-image:
  radial-gradient(circle at 15% 20%, rgba(229,255,0,0.04) 0%, transparent 35%),
  radial-gradient(circle at 85% 80%, rgba(96,165,250,0.03) 0%, transparent 40%);
background-attachment: fixed;
```

**Film-grain overlay** (a fixed, non-interactive pseudo-element at 2% opacity):
```css
body::before {
  content: ""; position: fixed; inset: 0;
  pointer-events: none; opacity: 0.02; z-index: 1;
  background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='3'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)'/%3E%3C/svg%3E");
}
```
Keep page content above the grain with `position: relative; z-index: 2`.

---

## 4. Component Patterns

Each is described as a recipe; reproduce the structure and the styling intent.

### KPI Card
- Surface `--bg-elev`, hairline border, `20px 22px` padding.
- Top row: a small mono uppercase label (`--text-dim`) on the left, a status pill on the right (`trend-up` green tint / `trend-down` red tint / `trend-flat` gray tint).
- Then the huge Archivo Black value.
- Then a small mono sub-line (`--text-dim`).
- Hover: border brightens to `--border-bright`.

### Executive Summary Card
- Three variants distinguished by a `3px` **top** border: positive = green, negative = red, opportunity = accent.
- Heading is mono uppercase, colored to match the variant.
- List items separated by `1px dashed` borders; key figures inline use mono bold, colored by variant (accent / green / red).

### Channel Card
- Identified by a `2px` gradient strip across the top (via `::before`) using the brand gradients above.
- Header: Archivo Black name + a small status badge (`hot` = accent, `warn` = amber, `cool` = blue) with matching border color.
- Metric rows: mono uppercase label left, mono value right, separated by `1px dotted` borders.
- Footer takeaway: italic, dim, separated by a solid top border.

### Data Table
- Wrapped in a bordered container; full width; `border-collapse: collapse`; mono font.
- `th`: `--bg-elev-2` background, mono uppercase 10px, dim, `letter-spacing: 0.14em`.
- `td`: `1px solid --border` bottom rule; numeric cells right-aligned with `font-variant-numeric: tabular-nums`.
- Row hover: faint white wash `rgba(255,255,255,0.02)`.
- Delta cells colored green/red/dim.

### Action Card
- A `3px` colored **left** border signals priority: urgent = red, medium = amber, opportunity = accent (default accent).
- Header: Archivo Black title + a mono uppercase priority tag (bordered, colored to match).
- Body in dim body text; footer is a 3-column meta grid (mono, faint uppercase keys over values), separated by a dashed top border.

### Section Header
- A flex row: mono accent-colored number (e.g. `01`), Archivo Black `h2`, then a faint mono meta string pushed right. Underlined by a `1px` bottom border.

### Header / Title Block
- Top bar: brand mark (Archivo Black logo, one letter or mark in accent) with a mono sub-label separated by a left border; right side shows a mono meta block with a pulsing green status dot.
- Below: a giant Archivo Black `h1`, optionally with an accent-colored mark, plus a mono uppercase subtitle.

### Notes Box & Pills
- Notes box: bordered panel, body text, list items prefixed with an accent `→` arrow (mono).
- "Lens tag": small bordered chip, mono uppercase accent text, prefixed with an accent `■`.

---

## 5. Motion

Keep motion minimal and functional.
- **Pulse dot** — a live/status indicator: `width/height: 6px`, `border-radius: 50%`, green with a soft `box-shadow` glow, animating opacity `1 → 0.3 → 1` over `1.6s` infinite.
- **Hover transitions** — border-color only, `0.2s ease`. No transforms, no bounce.

```css
@keyframes pulse { 0%, 100% { opacity: 1; } 50% { opacity: 0.3; } }
```

---

## 6. Charts (if using Chart.js)

Match the dashboard by setting global defaults and a shared theme:

```js
Chart.defaults.color = '#a3a3a3';
Chart.defaults.font.family = "'JetBrains Mono', monospace";
Chart.defaults.font.size = 11;
Chart.defaults.borderColor = '#262626';

const palette = {
  accent:'#e5ff00', green:'#4ade80', red:'#ef4444', amber:'#fbbf24',
  blue:'#60a5fa', pink:'#f472b6', purple:'#c084fc', dim:'#525252',
  fb:'#1877f2', google:'#ea4335', tiktok:'#ff0050', klaviyo:'#34d399'
};

const gridConfig = {
  grid: { color: '#1a1a1a', drawTicks: false },
  ticks: { padding: 8 },
  border: { display: false }
};

const tooltipStyle = {
  backgroundColor:'#0a0a0a', borderColor:'#262626', borderWidth:1,
  titleFont:{ family:"'JetBrains Mono', monospace", size:10, weight:'500' }, titleColor:'#a3a3a3',
  bodyFont:{ family:"'JetBrains Mono', monospace", size:12 }, bodyColor:'#f5f5f4',
  padding:12, cornerRadius:0, displayColors:true, boxWidth:8, boxHeight:8, boxPadding:6
};
```
Charts use the same flat, square, mono aesthetic: `cornerRadius: 0` tooltips, thin `#1a1a1a` gridlines, no axis border, doughnut segments separated by a `#0a0a0a` 3px border.

---

## 7. Do / Don't

**Do**
- Keep backgrounds dark and borders hairline-thin.
- Reserve `--accent` for emphasis, key numbers, active states, and markers.
- Use mono uppercase + wide letter-spacing for every label and badge.
- Right-align numbers with tabular figures.
- Let whitespace and borders create structure.

**Don't**
- Don't round corners (beyond tiny pills/dots).
- Don't add drop shadows or glassmorphism.
- Don't use accent yellow for large fills or body text (poor contrast, kills its impact).
- Don't introduce a fourth typeface or use the display font for body copy.
- Don't use gradients except the thin brand/channel top-rules and the subtle canvas glows.

---

## 8. Quick Start

1. Add the Google Fonts `<link>` from §2.
2. Define the tokens from §2 as `:root` CSS variables.
3. Apply the canvas treatment from §3 to `body`; wrap content in a `max-width: 1480px` centered container at `z-index: 2`.
4. Build components per §4, pulling colors and type from the tokens.
5. If charting, apply the §6 defaults.

> A drop-in `complex-style.css` implementing all of this already exists in this project — reference it for exact selectors and values.
