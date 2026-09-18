# AeroFlow Supply Chain Analytics

Python | pandas | Matplotlib | Supplier risk, inventory, forecasting and quality

An operational analysis of four supply chain domains, built to show where performance problems are concentrated and which teams can investigate them.

## Project Background

AeroFlow operates a network of suppliers, sites and part families. Late deliveries, stockouts, backorders, forecast error and quality incidents can all disrupt operations. Headline counts alone do not show whether a group has unusually high risk or simply handles more parts and orders.

Intended stakeholders: Procurement and Supplier Quality, Supply Chain Planning, Site Operations, Demand Planning, and Quality and Engineering.

Decision context: Prioritise reviews of supplier reliability, inventory availability, forecast accuracy and quality burden. The analysis uses per-order and per-part measures alongside totals so groups of different sizes can be compared more fairly.

## Business Questions

1. Which suppliers have the weakest on-time, in-full (OTIF) delivery performance, and are delivery and quality issues concentrated together?
2. Which part families, individual parts and sites show the greatest stockout and backorder exposure?
3. Where is forecast error highest, and does its pattern align with availability problems?
4. Which part criticality class has the greatest quality burden after accounting for the number of parts?
5. Which findings warrant an immediate operational review, and what should each team track afterward?

## Data Structure & Initial Checks

Four source datasets cover the core supply chain activity. The profiling and cleaning notebooks review structure, missing values and data types, standardise fields, and create cleaned datasets for analysis.

| Dataset | Contents |
|---|---|
| Parts Master | Part family, criticality class and supplier risk attributes |
| Purchase Orders | Supplier delivery performance, including on-time and in-full measures |
| Supply Chain History | Forecast demand, consumption, stockouts, backorders and site activity |
| Quality Incidents | Defect type, severity and scrap quantities |

The project retains source files in `Data/Raw/` and cleaned outputs in `Data/Clean/`. Supplier measures are normalised by purchase-order volume; inventory and quality measures are normalised by part count where comparisons across groups require it. The [profiling notebook](Python/01_data_profiling.ipynb) and [cleaning notebook](Python/02_data_cleaning.ipynb) document the preparation steps.

## Executive Summary

- Supplier risk is concentrated at SUP033: its OTIF rate was 3.01% across 631 purchase orders, and deliveries averaged 6.09 days late. It also had the highest Major or Critical quality-incident rate per 100 orders.
- Electrical parts had the highest stockout frequency at 15.98 per part, nearly twice LandingGear's 8.00. SITE02 had the largest combined stockout and backorder count, although availability pressure extended across all six sites.
- Cabin had the highest part-family forecast error at 2.49 units mean absolute error (MAE), yet low stockout exposure. Class B had the highest quality burden after normalisation, at 1.46 incidents and 1.89 scrap units per part. These patterns call for separate inventory, forecasting and quality responses.

## Insights Deep Dive

### Supplier reliability and quality

SUP033 was the clearest delivery outlier: 3.01% OTIF across 631 purchase orders, an on-time rate of 3.65%, and average delivery variance of 6.09 days late versus 1.18 days late overall. Its in-full rate was 76.23%, so lateness was the more pronounced component of its OTIF failure.

SUP033 recorded 11 quality incidents in total, ranking sixth by incidents per 100 orders. Seven were Major or Critical, giving it the highest serious incident rate after normalising by purchase-order volume: 1.11 per 100 orders. The severity profile adds to the case for supplier review without implying that delivery issues caused the quality incidents.

![Supplier OTIF Performance](Images/supplier_otif.png)

### Inventory exposure by part and site

Electrical averaged 15.98 stockouts per part, compared with 8.00 for LandingGear. This per-part comparison shows that the result is not solely explained by family size. P00179, P00124 and P00043 had especially frequent stockouts and backorders; Class A part P00062 also warrants attention. P00179 had 229 combined stockout and backorder flags, followed by P00124 with 176.

![Stockouts per Part Family](Images/stockouts_by_family.png)

SITE02 recorded the highest combined inventory-risk total, with 331 stockout and 430 backorder flags, or 761 combined. The SITE02–P00062 combination had 25 stockout records. Other sites also had elevated counts, so the issue should be assessed across the network as well as at SITE02.

![Inventory Risk by Site](Images/inventory_risk_by_site.png)

### Forecast accuracy and availability

Cabin had the highest part-family MAE at 2.49 units, while Engine had the lowest at 0.86. SITE06 and SITE04 had the highest site MAE at 2.11 and 2.08 units; SITE02 was near 1.1 despite having the highest combined inventory-risk count. Cabin's high error also coincided with low stockout exposure. These comparisons do not show a clear alignment between the groups with the highest forecast error and those with the most inventory-risk flags. They justify investigating replenishment, stocking policy and supply constraints before treating forecast accuracy as the main availability fix.

![Forecast Accuracy by Part Family](Images/forecast_mae_by_family.png)

### Quality burden after normalisation

Class B recorded the highest relative quality burden: approximately 1.46 incidents and 1.89 scrap units per part. Raw incident totals would have pointed toward Class C; adjusting for the number of parts changes the priority for further quality investigation.

![Quality Burden by Criticality](Images/quality_by_criticality.png)

## Recommendations

Expected impacts are directional. The analysis locates risks; owners should validate causes and test corrective actions with operational evidence.

| Priority | Recommendation and evidence | Suggested owner | Expected impact | Metric to track |
|---|---|---|---|---|
| 1 — High | Launch a SUP033 recovery review covering delivery reliability, corrective actions and contingency sourcing for critical parts. OTIF was 3.01% across 631 orders, with a 6.09-day average delay and the highest serious quality-incident rate per 100 orders. | Procurement and Supplier Quality | Improve delivery reliability and reduce exposure on critical parts. | SUP033 OTIF; average delivery delay; Major or Critical incidents per 100 orders |
| 2 — High | Review replenishment and safety-stock settings for Electrical parts, especially P00179, P00124, P00043 and Class A part P00062. Electrical averaged 15.98 stockouts per part. | Supply Chain Planning and Inventory Control | Reduce recurring stockouts and backorders in the highest-risk family. | Stockouts and backorders per part; combined inventory-risk flags |
| 3 — High | Investigate SITE02 inventory policy and replenishment, focusing on Electrical items and P00062. SITE02 had 761 combined stockout and backorder flags. | Site Operations and Inventory Control | Improve availability at the most exposed site while checking network-wide patterns. | SITE02 stockout and backorder counts; combined risk flags |
| 4 — Medium | Review demand patterns and forecast assumptions for Cabin parts and SITE06/SITE04 before changing the wider forecasting process. These groups had the highest MAE. | Demand Planning | Target forecast work where measured error is greatest. | MAE by part family and site; forecast bias where available |
| 5 — Medium | Prioritise Class B parts for defect prevention, root-cause analysis and scrap reduction. They had the highest incidents and scrap per part. | Quality and Engineering | Reduce the greatest normalised quality burden. | Incidents per part; scrap units per part; serious incident rate |

## Assumptions & Caveats

- Group comparisons use normalised measures where appropriate. Counts still matter for workload and exposure, but can favour larger groups when used alone.
- Stockout and backorder flags identify availability problems, not their financial or production cost. The analysis does not measure the effect of a missed part on output.
- The forecast comparison is observational. Lack of alignment across groups does not rule out forecast error as a contributor to individual shortages or prove that replenishment policy is the cause.
- MAE captures the average size of forecast errors, not their direction or demand volatility.
- Quality findings depend on recorded incidents and may miss delayed or unreported issues.
- Supplier capacity, transport disruptions and market conditions are not captured. Operational evidence and stakeholder review are needed before implementation.

## Tools & Technical Approach

Python, pandas, Matplotlib and Jupyter Notebook support a four-stage workflow:

1. [Data profiling](Python/01_data_profiling.ipynb) reviews the four datasets' structure, missing values, data types and initial quality issues.
2. [Data cleaning](Python/02_data_cleaning.ipynb) standardises fields, creates analysis flags and writes cleaned datasets.
3. [Exploratory analysis](Python/03_exploratory_analysis.ipynb) examines suppliers, parts, sites, forecasts and quality using both totals and appropriate normalised measures.
4. [Visualisation](Python/04_visualisation.ipynb) produces five charts focused on the decisions above.

GitHub hosts the repository and its reproducible notebooks.

## Repository Structure

```text
AeroFlow-Supply-Chain-Analytics/
├── Data/
│   ├── Raw/
│   │   ├── parts_master.csv
│   │   ├── purchase_orders.csv
│   │   ├── supply_chain_history.csv
│   │   └── quality_incidents.csv
│   └── Clean/
│       ├── parts_master_clean.csv
│       ├── purchase_orders_clean.csv
│       ├── supply_chain_history_clean.csv
│       └── quality_incidents_clean.csv
├── Images/
│   ├── supplier_otif.png
│   ├── stockouts_by_family.png
│   ├── inventory_risk_by_site.png
│   ├── forecast_mae_by_family.png
│   └── quality_by_criticality.png
├── Python/
│   ├── 01_data_profiling.ipynb
│   ├── 02_data_cleaning.ipynb
│   ├── 03_exploratory_analysis.ipynb
│   └── 04_visualisation.ipynb
├── .gitattributes
└── README.md
```
