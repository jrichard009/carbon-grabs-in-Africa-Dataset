# Carbon Grab Visualization - Tableau Setup Guide

## Overview
Visualize how developed countries (US, China, UK, EU) invest in carbon credit projects 
in African nations, creating "tele-coupling" between global emitters and local landscapes.

---

## Data Files Available

| File | Purpose | Rows |
|------|---------|------|
| **`carbon_flows_africa.csv`** | Project-level detail (every project, proponent, credits) | 2,458 |
| **`carbon_flows_summary.csv`** | Aggregated buyer→Africa flow links | 192 |
| **`carbon_grabs_africa.csv`** | REDD+ and land-based projects only (284 projects) | 284 |
| **`carbon_flows_aggregated.csv`** | Matrix of buyer×country with credit totals | 192 |

---

## Visualization 1: Sankey Flow Diagram (Tele-Coupling Flows)

### Steps:
1. **Connect** to `carbon_flows_summary.csv`
2. Drag **`source_country`** to the left side of the canvas
3. Drag **`target_country`** to the right side
4. Right-click the connection → **Show Me → Sankey Diagram**
5. Drag **`total_issued_credits`** to **Size** on the Marks card
6. Color by `source_country` (emitters in red, African countries in green)

### Alternative (if Sankey not available):
- Use **`project_count`** on Size instead
- Filter to top 10-15 flows for clarity

---

## Visualization 2: Africa Choropleth Map (Carbon Grab Impact)

### Steps:
1. **Connect** to `carbon_grabs_africa.csv` (REDD+/land projects)
2. Double-click **`african_country`** → Tableau auto-generates map
3. Drag **`issued_credits`** to **Color**
4. Set aggregation to **SUM**
5. Edit Colors → choose **Orange-Red** sequential palette
6. Add **`project_type`** to **Detail** or **Shape**

### Add Context:
- Drag **`buyer_country`** to **Filter** → Show Filter → select specific emitters
- Add **`proponent`** to Tooltip for company names

---

## Visualization 3: Flow Map (Arc Map)

### Steps:
1. **Connect** to `carbon_flows_summary.csv`
2. Double-click **`target_country`** → map of Africa appears
3. Drag **`source_country`** to **Detail**
4. Change mark type to **Line**
5. Drag **`total_issued_credits`** to **Size** and **Color**
6. For curved arcs: requires spatial data (lat/lon) — use the HTML visualization instead

---

## Visualization 4: Bar Chart — Top Buyers by Credits

### Steps:
1. **Connect** to `carbon_flows_summary.csv`
2. Drag **`source_country`** to Rows
3. Drag **`total_issued_credits`** to Columns
4. Sort descending
5. Color by `source_country`

### Add:
- Drag **`project_count`** to Label
- Add reference line for average

---

## Visualization 5: REDD+ Deep Dive (Carbon Grab Focus)

### Steps:
1. **Connect** to `carbon_grabs_africa.csv`
2. Filter to `project_type` = "REDD+"
3. Create a **tree map** or **bar chart**:
   - X-axis: `african_country`
   - Y-axis: SUM(`issued_credits`)
   - Color: `buyer_country`
4. Add `proponent` as tooltip

### Key Finding to Highlight:
> **Wildlife Works Carbon LLC** (US) holds **64M+ credits** across DRC, Kenya, and Zimbabwe — 
> the single largest carbon grab beneficiary.

---

## Dashboard Layout

Arrange 4 sheets in a 2×2 grid:

```
┌─────────────────────┬─────────────────────┐
│  Sankey Flow        │  Africa Choropleth  │
│  (tele-coupling)    │  (carbon grab map)  │
├─────────────────────┼─────────────────────┤
│  Top Buyers Bar     │  REDD+ Deep Dive    │
│  (who buys most)    │  (land grabs focus) │
└─────────────────────┴─────────────────────┘
```

### Dashboard Filters:
- Add **`buyer_country`** filter → Apply to All Sheets
- Add **`project_type`** filter → Apply to All Sheets
- Add **`african_country`** filter → Apply to All Sheets

---

## Key Talking Points

1. **The US dominates REDD+ credits**: Wildlife Works LLC alone controls 64M credits
2. **UK leads in project count** (749) but in smaller projects (cookstoves, boreholes)
3. **China's footprint is small but strategic**: 9 projects, focused on Gabon REDD+ and solar
4. **Canada is nearly invisible** in Verra data — operates through UK/Swiss intermediaries
5. **DR Congo holds the largest single credit pools** (peatland REDD+), mostly controlled by foreign entities
6. **221 African projects** span 48 countries — carbon markets now touch most of the continent
