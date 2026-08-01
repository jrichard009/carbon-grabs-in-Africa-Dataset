# Global Emissions Map - Tableau Setup Guide

## Data File
Use **`emissions_long.csv`** (already generated from your original Excel/CSV).

It has 3 columns:
| Column | Type | Description |
|--------|------|-------------|
| Year | Integer | 1960–2024 |
| Country | String | Country name (221 countries) |
| Emissions | Number | Emissions value (may be blank for missing data) |

---

## Steps in Tableau Desktop

### 1. Connect to Data
1. Open Tableau Desktop
2. Choose **Text File** as your data source
3. Select **`emissions_long.csv`**

### 2. Build the Map
1. Double-click **`Country`** → Tableau auto-generates a map
2. Drag **`Emissions`** onto **Color** on the Marks card
3. Change the aggregation of Emissions to **SUM** (default)
4. Set color palette: click **Color > Edit Colors** > choose a sequential palette (e.g., *Orange-Red* or *Blue*)

### 3. Add Year Slider (Filter)
1. Drag **`Year`** to the **Filters** shelf
2. Choose **Range of values** (or "All values")
3. Right-click the filter > **Show Filter** → a year slider appears
4. Optionally drag **`Year`** to the **Pages** shelf for animation

### 4. Format the Map
- Right-click the map > **Map Layers** > enable borders, adjust colors
- Title the sheet: double-click the title → "Global CO₂ Emissions by Country"
- Add tooltips: click **Tooltip** on Marks card → customize

### 5. (Optional) Dashboard
1. Click **New Dashboard** tab
2. Drag your map sheet onto the dashboard
3. Add a legend, filter panel, and title

---

## Quick Alternative: Use the Original Excel Directly
If you prefer not to use the CSV:
1. In Tableau, choose **Microsoft Excel** as your data source
2. Select **`export_emissions.xlsx`**
3. You'll need to **pivot** the country columns:
   - Select all country columns (1960, 1961, etc. are rows; countries are columns)
   - Click **Pivot** to transform to long format
   - Rename pivoted columns to `Country` and `Emissions`

The pre-processed `emissions_long.csv` skips this step.
