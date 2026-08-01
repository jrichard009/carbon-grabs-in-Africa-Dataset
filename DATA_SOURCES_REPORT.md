# Carbon Grab Visualization — Data Sources Report

## Project Overview
This repository contains data and analyses on carbon markets in Africa. It visualizes **tele-coupling between global carbon emitters and African host nations** through carbon credit investment flows — mapping how developed countries (US, UK, China, EU, etc.) finance carbon offset projects across the continent, and how those projects affect local landscapes. This phenomenon is often referred to as **"carbon grabbing."**

---

## Primary Data Source

### CarbonPlan OffsetsDB
| Field | Detail |
|-------|--------|
| **Provider** | [CarbonPlan](https://carbonplan.org/) — independent non-profit research organization |
| **Dataset** | OffsetsDB (latest version) |
| **Download URL (CSV)** | https://carbonplan-offsets-db.s3.us-west-2.amazonaws.com/production/latest/offsets-db.csv.zip |
| **Download URL (Parquet)** | https://carbonplan-offsets-db.s3.us-west-2.amazonaws.com/production/latest/offsets-db.parquet.zip |
| **Data Page** | https://carbonplan.org/data |
| **GitHub Repository** | https://github.com/carbonplan/offsets-db |
| **Documentation** | https://carbonplan.org/offsets-db/methods |
| **License** | Terms of access defined in `TERMS_OF_DATA_ACCESS.txt` |
| **Registry Covered** | Verra (Verified Carbon Standard — VCS) |
| **S3 Bucket (all versions)** | https://carbonplan-offsets-db.s3.us-west-2.amazonaws.com/ |

#### Data Contents

**`projects.csv`** — One row per carbon offset project:
| Column | Description |
|--------|-------------|
| `project_id` | Unique Verra VCS identifier (e.g., VCS6032) |
| `name` | Full project name |
| `country` | Host country (ISO name format) |
| `project_type` | Technology/methodology (REDD+, Cookstove, Solar, etc.) |
| `proponent` | Entity developing/operating the project |
| `registry` | Carbon registry (all "verra" in this dataset) |
| `protocol` | Verra methodology code |
| `issued` | Total credits issued (metric tons CO₂e) |
| `retired` | Total credits retired |
| `status` | Project status (listed, retired, etc.) |
| `project_url` | Link to Verra registry project page |
| `is_compliance` | Whether project supplies compliance markets |
| `category` | Project category classification |
| `first_issuance_at` | Date of first credit issuance |
| `first_retirement_at` | Date of first credit retirement |
| `listed_at` | Date project was listed |

**`credits.csv`** — One row per credit transaction:
| Column | Description |
|--------|-------------|
| `project_id` | Links to `projects.csv` |
| `quantity` | Number of credits in transaction |
| `transaction_type` | "issuance" or "retirement" |
| `transaction_date` | Date of transaction |
| `retirement_beneficiary` | Entity that retired the credits (buyer) |
| `retirement_beneficiary_harmonized` | Normalized beneficiary name |
| `vintage` | Year the credits represent |
| `transaction_url` | Link to registry transaction |

---

## Data Processing Pipeline

### Step 1: Extraction
- Downloaded `offsets-db.csv.zip` from CarbonPlan S3 bucket
- Extracted `projects.csv` and `credits.csv`

### Step 2: African Project Filtering
- Filtered 11,659 global projects to **2,458 African projects** across **46 countries**
- Country name matching against a standardized list of African nation names (handling variants like "DR Congo", "Congo, Dem. Rep.", "DRC", "Cote d'Ivoire", "CIV")

### Step 3: Buyer/Investor Attribution
Buyer countries were **inferred** from project proponent names using:
- **Known company mappings** (e.g., Wildlife Works Carbon LLC → United States, South Pole → Switzerland, Acorn B.V. → Netherlands)
- **Keyword matching** on proponent names (e.g., "Inc.", "LLC" → US; "Ltd" → UK; "B.V.", "GmbH" → EU; "Hainan", "Sino" → China)
- "Multiple Proponents" flagged when ambiguous
- "Local/Unknown" for African-based proponents or unattributed entities

**Limitation:** The Verra registry does not directly record the nationality of credit buyers in the project-level data. Buyer attribution is **inferred**, not explicit. For precise buyer nationality, one would need access to retirement beneficiary data from `credits.csv` (many fields are blank) or supplementary commercial databases.

### Step 4: Aggregation
- **Project-level**: Each African project with full detail (`carbon_flows_africa.csv` — 2,458 rows)
- **Flow-level**: Aggregated by buyer country → African country pair (`carbon_flows_summary.csv` — 192 links)
- **Land-focused**: REDD+ and forest/land projects filtered (`carbon_grabs_africa.csv` — 284 projects)

---

## Supplementary Data Sources

### Country Geographic Data
| Source | URL | Use |
|--------|-----|-----|
| **Plotly built-in country shapes** | https://plotly.com/python/reference/#scattergeo | Choropleth maps use Plotly's internal ISO 3166-1 alpha-3 country boundary data |
| **Natural Earth (country shapes)** | https://www.naturalearthdata.com/downloads/10m-cultural-vectors/10m-admin-0-countries/ | Source of Plotly's internal country boundary data |
| **Manual centroid coordinates** | https://github.com/datasets/geo-boundaries-world-110m | Country center points (lat/lon) for bubble placement and arc drawing |
| **ISO 3166-1 alpha-3 codes** | https://www.iso.org/iso-3166-country-codes.html | Manually mapped from country names in the dataset |

### Carbon Registries and Related Databases
| Source | URL | Coverage |
|--------|-----|----------|
| **Verra VCS Registry** | https://registry.verra.org/ | Primary registry for projects in this dataset |
| **Gold Standard** | https://www.goldstandard.org/ | Not included in this dataset |
| **ART-TREES** | https://arttrees.org/ | Jurisdictional REDD+ — not included |
| **Plan Vivo** | https://www.planvivo.org/ | Community forest projects — not included |
| **Ecosystem Marketplace** | https://www.ecosystemmarketplace.com/ | Voluntary carbon market price data |
| **Carbon Plan CDR Database** | https://carbonplan.org/data/cdr | Carbon dioxide removal proposals (2020–2021) |

---

## Key Data Limitations

1. **Buyer nationality is inferred, not recorded** — Verra project data lists proponents, not ultimate credit purchasers. Retirement beneficiary fields in `credits.csv` are largely blank.

2. **Canada gap** — Zero direct Canadian projects found in Verra data. Canadian buyers likely operate through UK, Swiss, or Dutch subsidiaries (a known pattern in voluntary carbon markets).

3. **Single registry** — This dataset covers only **Verra (VCS)**. It does not include:
   - Gold Standard projects
   - ART-TREES (jurisdictional REDD+)
   - Africa Carbon Credit Initiative (ACCI)
   - Plan Vivo, VCS-like registries
   - Bilateral government agreements

4. **"Issued" vs "Retired"** — Many projects show `issued = 0` (newly listed, no credits yet issued). Analysis focuses on issued credits where available.

5. **Proponent ≠ beneficiary** — The entity listed as "proponent" may be a local implementer, not the ultimate credit owner. True buyer tracing requires transaction-level data.

6. **Temporal scope** — Projects span from early 2000s to 2024. Some older projects may have incomplete records.

---

## Output Files Generated

| File | Rows | Description |
|------|------|-------------|
| `projects.csv` | 11,659 | Raw CarbonPlan project data (all countries) |
| `credits.csv` | 532,799 | Raw CarbonPlan credit transactions |
| `carbon_flows_africa.csv` | 2,458 | All African projects with inferred buyer countries |
| `carbon_flows_summary.csv` | 192 | Aggregated buyer→Africa flow links |
| `carbon_grabs_africa.csv` | 284 | REDD+ and land-based projects only |
| `carbon_flows_aggregated.csv` | 192 | Matrix with project counts and credit totals |
| `emissions_long.csv` | 14,365 | Territorial emissions data (1960–2024, 221 countries) — separate dataset |
| `export_emissions.xlsx` | — | Original wide-format emissions data |

---

## Visualizations

| File | Type | Description |
|------|------|-------------|
| `carbon_grab_map.html` | Interactive web map | 4 tabs: Flow Map, Africa Choropleth, World Choropleth, Buyers Bar |
| `carbon_grab_visualization.html` | Interactive web map | 4 tabs: Africa Bubble Map, Flow Map, Sankey, Table |
| `carbon_grab_tableau_guide.md` | Guide | Step-by-step Tableau Desktop instructions |
| `Tableau_Setup_Guide.md` | Guide | Global emissions map setup |

---

## Reproducibility

All data processing scripts were run once to generate the output CSVs and HTML files. The raw source files (`projects.csv`, `credits.csv`, `metadata.json`, `TERMS_OF_DATA_ACCESS.txt`) are retained in the project folder for audit.

To reproduce:
1. Download latest OffsetsDB from CarbonPlan
2. Run the data transformation pipeline (scripts were removed after execution; logic documented above)
3. Generate HTML visualizations from the processed CSVs

---

## Citation

If using this data in academic or journalistic work:

> **CarbonPlan.** (2024). *OffsetsDB: Database of carbon offset projects and credits.* https://carbonplan.org/data — https://carbonplan-offsets-db.s3.us-west-2.amazonaws.com/production/latest/offsets-db.csv.zip

> **Verra.** (2024). *Verified Carbon Standard (VCS) Programme.* https://registry.verra.org/ — Registry data accessed via CarbonPlan OffsetsDB.

> **Natural Earth.** (2024). *Country boundary vectors.* https://www.naturalearthdata.com/downloads/10m-cultural-vectors/10m-admin-0-countries/

---

## Contact for Data Queries

- **CarbonPlan**: research@carbonplan.org — https://carbonplan.org/contact
- **Verra**: info@verra.org — https://verra.org/contact-us/
- **Project author**: See project repository for contact details
