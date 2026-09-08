# Swiggy Sales Analysis Dashboard (Excel)

📊 An interactive Excel dashboard analyzing ~197K food delivery orders from Swiggy, uncovering sales trends, food preferences, and regional performance across India.

![Dashboard Preview](swiggy_dashboard.png)

## Introduction

This project dives into a large-scale food delivery dataset to answer a practical business question: **where is the money actually coming from, and does the business behave differently by time, category, or geography?** Using nearly 200,000 order-level records, the analysis surfaces sales trends over time, category-level preferences (Veg vs Non-Veg), state and city-level performance, and quarterly business health — all consolidated into a single interactive dashboard.

🔍 Full workbook: `Swiggy_Data.xlsx` (Analysis, Dashboard, and raw Data sheets)

## Background

Raw transactional data is only useful once it's summarized and made explorable. This project was built to practice the full Excel analytics workflow — from a raw 197K-row export to a polished, interactive dashboard a non-technical stakeholder could use to answer their own questions on the fly.

The questions this dashboard answers:
- What are the overall sales, rating, and order volume KPIs?
- How does revenue trend month-to-month and week-to-week?
- Do customers prefer Veg or Non-Veg food, and by how much?
- Which days of the week drive the most sales?
- How does performance compare across quarters?
- Which states and cities generate the most revenue?
- How can a viewer filter all of this by month, category, or restaurant without touching a formula?

## Dataset

The `Swiggy Data` sheet contains **197,430 order-level records**. The raw source data included **10 columns**; I engineered **4 additional columns** using Excel formulas to enable the time-based and food-type analysis in this project.

**Raw columns (original source data):**

| Column | Description |
|---|---|
| State | Indian state where the order was placed |
| City | City within that state |
| Order Date | Date of the order |
| Restaurant Name | Name of the restaurant fulfilling the order |
| Location | Restaurant's neighborhood/area |
| Category | Dish category (e.g. Snack, Recommended) |
| Dish Name | Specific item ordered |
| Price (INR) | Order value in Indian Rupees |
| Rating | Customer rating for the dish/order |
| Rating Count | Number of ratings the dish has received |

**Columns I added:**

| Column | Description | How it was built |
|---|---|---|
| Day | Day of the week | `=TEXT([@[Order Date]],"ddd")` |
| Week | Week number | `=WEEKNUM([@[Order Date]])` |
| Quarter | Calendar quarter (Q1–Q3) | `="Q"&INT((MONTH([@[Order Date]])-1)/3)+1` |
| Food Type | Veg or Non-Veg classification | Keyword-matching formula on `Dish Name` — see below |

Adding these four columns was a necessary step before any of the time-trend (Monthly/Daily/Weekly/Quarterly) or Veg-vs-Non-Veg analysis in this project was possible — none of that breakdown exists in the raw export.

**Food Type classification formula** — since the raw data had no dedicated Veg/Non-Veg column, this was derived by scanning each `Dish Name` for non-vegetarian keywords:

```
=IF(OR(
    ISNUMBER(SEARCH("chicken",J3)),
    ISNUMBER(SEARCH("egg",J3)),
    ISNUMBER(SEARCH("fish",J3)),
    ISNUMBER(SEARCH("mutton",J3)),
    ISNUMBER(SEARCH("prawn",J3)),
    ISNUMBER(SEARCH("biryani",J3)),
    ISNUMBER(SEARCH("kabab",J3)),
    ISNUMBER(SEARCH("kebab",J3)),
    ISNUMBER(SEARCH("Non-Veg",J3)),
    ISNUMBER(SEARCH("non veg",J3))
    ),"Non-Veg","Veg")
```

This checks the dish name against a list of common non-vegetarian ingredients and dish types (meat, egg, seafood, biryani, kababs) — if any keyword is found, the dish is tagged `Non-Veg`; otherwise it defaults to `Veg`. `SEARCH` was used instead of `FIND` specifically because it's case-insensitive, so "Chicken," "chicken," and "CHICKEN" are all caught by the same condition.

*Note: `Day`, `Week`, and `Quarter` use Excel table structured references (e.g. `[@[Order Date]]`) rather than plain cell references (e.g. `C3`) — this keeps the formulas valid even if columns are reordered or new rows are added to the table.*

*Source: Kaggle*

## Tools I Used

- **Microsoft Excel**: PivotTables and PivotCharts for all aggregation and trend analysis
- **Excel Slicers**: interactive filters (Month, Category, Restaurant Name) connected across the dashboard
- **Filled Map Chart** (Geography data type): state-by-state sales visualization
- **KPI Card Layout**: custom-formatted cells styled as summary cards for at-a-glance metrics

## The Analysis

### 1. Key Performance Indicators
Five headline metrics anchor the dashboard:

| Metric | Value |
|---|---|
| Total Sales | ₹53.01M |
| Total Orders | 197.43K |
| Average Order Value | ₹268.51 |
| Average Rating | 4.34 / 5 |
| Total Ratings Given | 5.59M |

A 4.34 average rating across nearly 200K orders indicates consistently high customer satisfaction at scale — not just among a small sample.

### 2. Monthly & Weekly Sales Trend
Built with a PivotTable grouping `Order Date` by month, visualized as a line chart.

- Monthly sales are **remarkably stable**, ranging narrowly between ₹6.27M (February, the lowest) and ₹6.83M (May, the highest) — a spread of less than 9%.
- This stability suggests **consistent, steady-state demand** rather than a growth or decline trend across the 8 months of data available (January–August).

### 3. Sales by Food Type (Veg vs Non-Veg)
A donut chart built from a two-category PivotTable.

- **Veg: ₹34.18M (64%)** vs **Non-Veg: ₹18.83M (36%)**
- Vegetarian orders generate nearly **double** the revenue of non-vegetarian orders — a strong signal for menu and inventory prioritization if this were a real business decision.

### 4. Daily Sales Trend
A day-of-week PivotTable and bar chart.

- **Saturday is the strongest day (₹7.78M)**, followed closely by Sunday (₹7.64M) and Thursday (₹7.66M).
- **Tuesday is the weakest (₹7.36M)** — though the gap between the best and worst day is relatively small (~5.7%), showing demand is fairly evenly spread across the week rather than concentrated on weekends alone.

### 5. Quarterly Performance
A summary table tracking Sales, Rating, and Orders per quarter.

| Quarter | Sales | Avg Rating | Orders |
|---|---|---|---|
| Q1 | ₹19.67M | 4.34 | 73.1K |
| Q2 | ₹19.90M | 4.34 | 74.2K |
| Q3 | ₹13.44M | 4.34 | 50.2K |

**Important caveat included in the analysis**: Q3's lower total is *not* a decline — the dataset only covers January through August, meaning Q3 contains just two months (July–August) of data instead of three. Average rating stayed identical (4.34) across all three quarters regardless of volume, showing satisfaction didn't waver even as order volume shifted.

### 6. State & City-Level Performance
A Filled Map chart (Geography data type) plus a Top-5-cities bar chart.

- **Karnataka is the clear outlier at ₹5.46M** — more than 75% higher than the next closest state (Uttar Pradesh, ₹3.12M).
- Cross-referencing the city-level breakdown explains why: **Bengaluru alone accounts for ₹5.46M**, matching Karnataka's entire state total almost exactly. This indicates Karnataka's performance is driven by a single major metro, not broad statewide demand — an important distinction for anyone reading the map at face value.
- The remaining Top 5 cities (Lucknow, Hyderabad, Mumbai, New Delhi) each contribute a much more modest ₹2.8M–₹3.1M, clustered closely together.

### 7. Interactive Filtering (Slicers)
Three slicers — **Month, Category, and Restaurant Name** — are connected to the dashboard's PivotTables and charts, letting a viewer drill into any specific month, food category, or restaurant and watch every chart update simultaneously, without writing a single formula.

## What I Learned

- **Feature engineering with formulas**: the raw data only had 10 columns — `Day`, `Week`, `Quarter`, and `Food Type` didn't exist and had to be derived from `Order Date` and `Dish Name`/`Category` before any time-based or Veg/Non-Veg analysis was possible.
- **PivotTable-driven dashboards**: built and connected multiple PivotTables feeding different chart types (line, donut, bar, filled map) from a single ~200K-row source table.
- **Geography data types**: converted plain-text state names into recognized geographic entities to power a working Filled Map visualization.
- **Slicer connectivity**: linked slicers across multiple PivotTables so a single filter selection updates the entire dashboard at once.
- **KPI card design**: used cell formatting and layout (rather than a coding tool) to create a clean, scannable summary-metrics header.
- **Reading around data limitations**: recognized that an apparent "Q3 decline" was actually a partial-quarter artifact, not a real business trend — a reminder to always check the underlying date range before drawing conclusions.

## Conclusions

- **Demand is stable, not seasonal**: month-to-month sales vary by less than 9%, suggesting consistent baseline demand rather than sharp seasonal spikes.
- **Vegetarian food dominates revenue**: at 64% of total sales, Veg options are the primary revenue driver.
- **Geography is concentrated, not distributed**: Karnataka's outsized state performance is almost entirely explained by Bengaluru — a reminder that state-level maps can mask city-level concentration.
- **Customer satisfaction is stable regardless of volume**: a flat 4.34 average rating across all quarters, days, and food types suggests operational consistency rather than satisfaction being tied to how busy the platform is.
- **Q3 figures need context**: any comparison across quarters should note that Q3 in this dataset is a partial quarter, not a genuine downturn.

This project reinforced how much a well-structured PivotTable + slicer + chart combination can do without ever touching VBA or Power Query — and how important it is to sanity-check any single number (like a quarter's total) against the underlying data range before treating it as an insight.
