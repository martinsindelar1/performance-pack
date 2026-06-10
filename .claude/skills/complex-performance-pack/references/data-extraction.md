# Data Extraction Reference

This document describes how to programmatically pull data from each input type. Use these as starting points — the Pandas snippets are tested against the formats Complex actually uses.

## 1. Triple Whale Pins Report (`Pins_report_for_MM_DD_YY_-_MM_DD_YY.xlsx`)

One file per month. Single sheet named `Data for <Month> 1-<Month> NN, YYYY`. Long-format with metrics as rows.

```python
import pandas as pd

df = pd.read_excel(path)  # auto-picks first sheet
# Columns: ['Metric', 'Current (...)', 'Previous (...)', '% Change']

# Helper to fetch a specific metric
def get(metric_name):
    row = df[df['Metric'] == metric_name]
    if row.empty:
        return None
    val = row.iloc[0, 1]  # current column
    return val

total_sales = get('Total Sales')              # str like "7,502,402 CZK"
net_profit  = get('Net Profit')               # str like "1,632,775 CZK"
orders      = get('Orders')                   # float like 5.153 (read as thousands!)
roas        = get('Blended ROAS')             # float like 5.45
ncpa        = get('New Customers CPA')        # str like "623.4 CZK"
ltvcpa      = get('LTV/CPA')                  # float like 5.85 — may be a datetime (bug)
new_cust_pct = get('New Customers')           # float like 0.44 = 44% of orders
ret_rev     = get('Returning Customer Revenue')  # str like "4,714,715 CZK"
sub_pct     = get('% Subscription Order Revenue')  # str like "2.674%" or 0
sub_active  = get('Total Active Subscribers')    # int

# Channel metrics
fb_cpa  = get('Facebook CPA')
goog_cpa = get('Google CPA')
tt_cpa  = get('TikTok CPA')
fb_roas = get('Facebook ROAS')  # may be datetime bug
goog_conv = get('Google Conversions')
goog_impr = get('Google Impressions')

# Cleaning helpers
def to_int(s):
    if isinstance(s, (int, float)):
        return int(s * 1000) if s < 100 else int(s)  # Orders read as thousands!
    return int(str(s).replace(',', '').replace(' CZK', '').replace('.', ''))

def to_float(s):
    if isinstance(s, (int, float)):
        return float(s)
    return float(str(s).replace(',', '').replace(' CZK', '').replace('%', ''))
```

**Gotchas:**
- `Orders` is read as `5.153` (thousands) not `5153`. Multiply by 1000 if it's < 100.
- `Total Sales`, `Net Profit`, `Returning Customer Revenue` are strings with `CZK` suffix and comma thousand separators.
- `New Customers` and `Returning Customers` are reported as **percentages of total**, not absolute counts. Multiply by Orders to get count.
- `LTV/CPA` and `Facebook ROAS` may be parsed as `datetime` objects when their numeric value (e.g., `5.05`) confuses Excel's auto-format. Detect with `isinstance(val, pd.Timestamp)` and recover from the % Change column: `current = previous × (1 + change_pct / 100)`.
- `% Change` column has values like `"4.10%"` (string) — parse with `float(s.rstrip('%')) / 100`.

## 2. Triple Whale Monthly Data (`Complex_-_Full_Monthly_Report_*.xlsx`)

Single file, sheet `Monthly Data`, wide format with one column per metric, one row per month.

```python
xls = pd.ExcelFile(path)
df = pd.read_excel(xls, sheet_name='Monthly Data')
# Columns include: Date, Order Revenue, Net Profit, Orders, Blended ROAS, NCPA, etc.

for i in range(len(df)):
    row = df.iloc[i]
    month = row['Date']  # e.g., "Jan 2026"
    rev = row['Order Revenue']  # already numeric
```

This format is cleaner than Pins — no date-bug, no percentage misinterpretation. Prefer it when available.

## 3. Triple Whale Summary Screenshot

Manual transcription. The dashboard shows 15+ KPI cards. Extract these into a dict:

```python
tw_summary = {
    'total_sales': 36323187,
    'ltv': 2235.45,
    'returning_customer_orders': 15121,
    'net_profit': 7693551,
    'orders': 24585,
    'roas': 5.28,
    'ad_spend': 7001556,
    'ncpa': 739.80,
    'ltv_cpa': 7.77,
    'returning_customers_pct': 0.62,
    'blended_cpa': 287.77,
    'fb_cpa': 247.47,
    'goog_cpa': 66.06,
    'tt_cpa': 161,
    'goog_conversions': 21083,
    'goog_impressions': 3622993,
    'fb_roas': 6.19,
    'sub_order_rev_pct': 0,
    'total_subscribers': 161,
    'new_customers_pct': 0.38,
    'returning_customer_rev': 25149862,
}
```

Use these for cross-check against the per-month aggregates. If they don't roughly sum, flag it.

## 4. Shopify — Sales by Billing Location (`Total_sales_by_billing_location_-_YYYY-MM-DD_-_YYYY-MM-DD.csv`)

```python
df = pd.read_csv(path)
# Columns: 'Billing country', 'Gross sales', 'Discounts', 'Returns', 'Net sales',
#          'Shipping charges', 'Taxes', 'Total sales', 'Orders'

total_revenue = df['Total sales'].sum()
total_orders = df['Orders'].sum()
country_breakdown = df.sort_values('Total sales', ascending=False)
```

Use for the optional **Expansion Tracker** sidebar (not currently in template, but flag if user asks).

## 5. Shopify — Net Sales Over Time (`Net_sales_over_time_-_YYYY-MM-DD_-_YYYY-MM-DD.csv`)

```python
df = pd.read_csv(path)
# Columns: 'Day', 'Net sales'
net_sales_total = df['Net sales'].sum()
```

Used only for cross-check vs. TW Order Revenue.

## 6. Shopify — New vs Returning (`New_vs_returning_customers_over_time_-_YYYY-MM-DD_-_YYYY-MM-DD.csv`)

```python
df = pd.read_csv(path)
# Columns: 'Day', 'New or returning customer', 'Customers', 'Orders', 'Total sales'

new = df[df['New or returning customer'] == 'New']
ret = df[df['New or returning customer'] == 'Returning']

new_customers = new['Customers'].sum()
new_orders = new['Orders'].sum()
new_revenue = new['Total sales'].sum()
ret_customers = ret['Customers'].sum()
ret_orders = ret['Orders'].sum()
ret_revenue = ret['Total sales'].sum()
```

## 7. Klaviyo Data

Two common formats:
- **Performance PDF/screenshot** (Czech labels: "Tržby e-mailing", "Otevírací rychlost", etc.)
- **Excel export with `Reporting` sheet**

For the Excel:
```python
xls = pd.ExcelFile(path)
rep = pd.read_excel(xls, sheet_name='Reporting')
# Columns are typically month-named: 'Jan', 'Feb', ... or with year
# Rows are metrics: 'Tržby celkem', 'Tržby e-mailing', 'Open rate', etc.

# Generic lookup
def kl(metric, month):
    row = rep[rep.iloc[:, 0].str.contains(metric, case=False, na=False)]
    if row.empty: return None
    return row[month].iloc[0]
```

Key Klaviyo metrics for the dashboard:
- `Tržby celkem` (total store revenue)
- `Tržby e-mailing` (Klaviyo total revenue)
- `Tržby z kampaní` (Campaigns revenue)
- `Tržby z automatizací` (Flows revenue)
- `Velikost databáze` (database size)
- `Nové kontakty` (new signups)
- `Odhlášené kontakty` (unsubscribes)
- `Open rate`, `Click rate`, `Konverzní poměr`

## 8. UppPromote (Influencer) Export

```python
xls = pd.ExcelFile(path)
print(xls.sheet_names)  # often ['Celkový přehled', 'Top affiliates', ...]

overview = pd.read_excel(xls, sheet_name='Celkový přehled')
# Pull monthly aggregates: Sales, Orders, Commission, Gross Profit

top = pd.read_excel(xls, sheet_name='Top affiliates')
# Columns: name, country, referrals, sales, commission
top10 = top.nlargest(10, 'Sales (or whatever the column is)')
```

Not currently in the Performance Pack template — used for the separate Influencer report.

## Final note

Always **print what you extracted** to the conversation before writing it into the dashboard. The user should be able to spot transcription errors before they get baked into HTML.
