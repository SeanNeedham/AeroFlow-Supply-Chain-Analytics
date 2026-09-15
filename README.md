# AeroFlow Supply Chain Analytics

**Python | pandas | Matplotlib | Supplier risk · Inventory · Forecasting · Quality**

An operational risk analysis across four supply chain domains, built to find where performance actually breaks down rather than to report KPIs.

---

## Executive Summary

### The Problem

AeroFlow runs a complex network of suppliers, sites and part families. Late deliveries, stockouts, backorders, forecast error and quality incidents all disrupt operations — but headline KPIs showed only that problems existed, not where they were concentrated or which team owned them.

### The Solution

A structured pandas workflow that profiles, cleans and analyses four datasets, then normalises every metric before comparing groups.

- Raw totals were rejected in favour of **per-part and per-order measures**, so large groups could not masquerade as high-risk ones.
- Findings were reduced to **five Matplotlib visualisations**, one per decision.
- Each insight was converted into an action with a **named owner, expected impact and tracking metric**.

### The Impact

- Isolated a single supplier, **SUP033**, operating at a **3.0% OTIF** rate across **631 purchase orders**.
- Concentrated inventory risk to one part family and one site: **Electrical** and **SITE02**.
- Demonstrated that forecast error was **not** the driver of availability failures — preventing a costly misdirected fix.
- Reclassified quality risk from **Class C to Class B** once normalised per part.

---

## Key Operational Insights

### SUP033 is a genuine outlier, not just a weak performer
OTIF of approximately **3.0%** across **631 purchase orders**, with average delivery variance around **6.1 days late**. It also recorded the **highest serious quality incident rate** once normalised by order volume — so the delivery failure and the quality failure point at the same supplier.

![Supplier OTIF Performance](Images/supplier_otif.png)

### Inventory risk is concentrated in Electrical
Electrical recorded roughly **16 stockouts per part** — about **twice** the rate of LandingGear and well above every other family. Per-part normalisation was essential here: raw stockout counts would simply have ranked the largest families first.

![Stockouts per Part Family](Images/stockouts_by_family.png)

### SITE02 carries the heaviest combined exposure
Highest combined stockout and backorder exposure of any site, though risk remains elevated across the wider network.

![Inventory Risk by Site](Images/inventory_risk_by_site.png)

### Forecasting is not the cause of the availability problem
Cabin parts had the highest forecast error at approximately **2.49 units MAE** — yet Cabin also had very low stockout exposure. The two do not correlate, which rules out forecast accuracy as the primary driver of AeroFlow's availability issues and redirects effort toward replenishment policy instead.

![Forecast Accuracy by Part Family](Images/forecast_mae_by_family.png)

### Normalising quality data changed the answer
**Class B** parts carry the highest normalised quality burden at approximately **1.46 incidents** and **1.89 scrap units per part**. Raw incident totals alone would have overstated the relative risk sitting in Class C.

![Quality Burden by Criticality](Images/quality_by_criticality.png)

---

## Recommendations & Business Actions

### Priority 1 — SUP033 supplier recovery · **High**
**Action:** Launch a recovery plan covering delivery reliability, corrective actions and contingency sourcing for critical parts.
**Owner:** Procurement / Supplier Quality
**Impact:** Improved OTIF, reduced delivery delay, lower exposure on critical parts.
**Track:** SUP033 OTIF %, average delivery delay, serious incidents per 100 orders

### Priority 2 — Electrical inventory risk · **High**
**Action:** Review replenishment and safety-stock settings for Electrical parts, specifically **P00179, P00124, P00043** and Class A part **P00062**.
**Owner:** Supply Chain Planning / Inventory Control
**Impact:** Fewer recurring stockouts and backorders in the highest-risk family.
**Track:** Stockouts per part, backorders per part, inventory-risk events

### Priority 3 — SITE02 inventory exposure · **High**
**Action:** Investigate SITE02 inventory policy and replenishment performance, focusing on Electrical items and Class A part **P00062**.
**Owner:** Site Operations / Inventory Control
**Impact:** Reduced stockout and backorder exposure, better availability of critical parts.
**Track:** SITE02 stockout count, backorder count, inventory-risk events

### Priority 4 — Forecast accuracy improvement · **Medium**
**Action:** Target Cabin parts and **SITE06 / SITE04**, where error is highest. Review demand patterns and forecast assumptions before changing anything more broadly.
**Owner:** Demand Planning
**Impact:** Accuracy gains where error is greatest, without disturbing areas already performing well.
**Track:** MAE by part family, MAE by site

### Priority 5 — Class B quality improvement · **Medium**
**Action:** Prioritise Class B parts for defect prevention, root-cause analysis and scrap reduction.
**Owner:** Quality / Engineering
**Impact:** Lower normalised quality burden and reduced avoidable scrap.
**Track:** Quality incidents per part, scrap per part, serious incident rate

### What this analysis cannot tell you
- No external factors are captured — supplier capacity constraints, transport disruption and market conditions sit outside the data.
- Stockout and backorder analysis locates availability failures but does not measure their **financial or production cost**.
- MAE compares groups cleanly but does not capture forecast bias or demand volatility.
- Quality findings rest on recorded incidents and may miss unreported or delayed issues.
- All recommendations require validation with business stakeholders before implementation.

---

## The Dataset & Metrics

Four cleaned datasets covering core supply chain activity.

| Dataset | Contents |
|---|---|
| **Parts Master** | Part family, criticality class, supplier risk attributes |
| **Purchase Orders** | Supplier delivery performance — OTIF, on-time and in-full measures |
| **Supply Chain History** | Forecast demand, consumption, stockouts, backorders, site activity |
| **Quality Incidents** | Defect type, severity, scrap quantities |

**Metrics measured**

- **OTIF rate** by supplier
- Average delivery variance in days
- Serious quality incidents per supplier, normalised by order volume
- **Stockouts per part** and **backorders per part**, by part family and by site
- Combined inventory-risk exposure by site
- **Mean absolute error (MAE)** by part family and by site
- Quality incidents per part and scrap units per part, by criticality class

---

## Methodology & Technical Stack

**Stack:** Python · pandas · Matplotlib · Jupyter Notebook · GitHub

### 1. Data Profiling — `01_data_profiling.ipynb`
Reviewed structure, missing values, data types and initial quality issues across all four datasets.

### 2. Data Cleaning — `02_data_cleaning.ipynb`
Standardised fields, created analysis flags and produced clean datasets for downstream use.

### 3. Exploratory Analysis — `03_exploratory_analysis.ipynb`
Grouped analysis across suppliers, parts, sites and quality categories, using **normalised metrics throughout** so that group size did not distort comparison.

### 4. Visualisation — `04_visualisation.ipynb`
Matplotlib charts built to expose outliers rather than summarise averages.

### 5. Recommendations
Findings translated into prioritised actions with owners, expected impact and trackable metrics.

### Repository structure

```text
AeroFlow/
├── Data/
│   ├── Raw/
│   └── Cleaned/
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
├── SQL/
├── PowerBI/
├── .gitattributes
└── README.md
```
