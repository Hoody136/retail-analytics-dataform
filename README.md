# From Hours to Twenty Minutes: Rebuilding Commercial Reporting for a Luxury Retailer

A modern analytics engineering project for a luxury retail brand, built in **Dataform** (Google's native BigQuery transformation tool) using dbt-compatible patterns: modular SQL, source-to-mart layering, dependency management, and automated assertions.

---

## The Problem

A fast-scaling luxury London retailer (1 physical store + 1 Shopify store) was running weekly and monthly trade reviews on fragmented Google Sheets that took hours to update. Shopify native exports did not present the full picture of profitability, and embedded logic made analysis prone to silent error. The Commercial Director was spending hours assembling data instead of acting on it. Worse, no two reports agreed on core metrics like gross margin or sell-through rate.

---

## The Solution

Built a governed, self-service analytics platform the whole team can run:

- **Sources** — Shopify raw exports, sample sale data, inventory feeds, master range sheet, custom calendar
- **Staging** — Cleaned, renamed, type-cast — one model per source entity
- **Intermediate** — Business logic: order cost allocation, rolling SKU aggregations, ISO calendar alignment
- **Marts** — Consumption-ready tables for Looker dashboards and Sheets forecasting

---

## Tech Stack

- **Raw Data:** Shopify CSV exports
- **Data Warehouse:** Google BigQuery
- **Transformation:** Dataform (https://cloud.google.com/dataform, dbt-compatible patterns, BigQuery-native, no subscription overhead)
- **Visualization:** Looker Studio
- **Planning:** Google Sheets (connected to mart tables)

---

## Key Design Decisions

**1. Dataform over dbt Cloud**

BigQuery-native, lower total cost of ownership, same modular SQL and testing capabilities. The right engineering decision for a cost-conscious scaling retailer.

**2. 4-4-5 ISO Retail Calendar**

Custom calendar aligning weeks to retail seasons (Summer/Winter) for true like-for-like comparisons. Enables consistent PW, PWLY, P-4W, and YTD reporting.

**3. Line-Item Cost Allocation**

Order-level costs (shipping, tax, discounts, refunds) apportioned down to SKU level for true margin analysis. Without this, profitability by product is wrong.

---

## Project Structure

**Staging** (`definitions/staging/`)
- `stg_order.sqlx` — Shopify + sample sale orders, cleaned & unioned
- `stg_inventory.sqlx` — Stock levels by SKU and location
- `stg_products.sqlx` — Product master with live pricing
- `stg_raw_range_sheet.sqlx` — Enriched product attributes (costs, RRP, delivery)
- `stg_dim_date.sqlx` — 4-4-5 ISO calendar (2024–2034)

**Intermediate** (`definitions/intermediate/`)
- `int_orders_filled.sqlx` — Order-level cost allocation logic
- `int_sku_sales_rolling.sqlx` — PW, PW-1, PWLY, P-4W, P-5-8W, YTD, YTD LY aggregations
- `int_sample_sales_enriched.sqlx` — Sample sale data enriched with product master

**Marts** (`definitions/marts/`)
- `dim_products.sqlx` — Product dimension (range sheet + live price)
- `fct_order_lines.sqlx` — Order line facts with all KPIs
- `fct_inventory.sqlx` — Inventory snapshot joined to product
- `mart_sku_performance.sqlx` — SKU-level trade metrics (lifetime + rolling)
- `mart_inventory_enriched.sqlx` — Stock + sales unified for merchandising


---

## Data Lineage

<!-- TODO: Add Dataform lineage screenshot to assets/lineage.png, then uncomment below -->
<!-- ![Dataform Lineage](assets/lineage.png) -->

---

## KPI Definitions

- **Gross Margin %** — Profit after COGS. Formula: `(Net Sales - COGS) / Net Sales`. Grain: order-line. Owner: Commercial Director.
- **Sell-Through Rate** — Units sold / units received. Formula: `Sold / Received`. Grain: SKU-week. Owner: Buying Team.
- **Open-to-Buy** — Planned purchases minus committed spend. Formula: `Plan - (On-Order + Receipts)`. Grain: category-month. Owner: Commercial Director.
- **Stock Weeks Cover** — Current stock / average weekly sales. Formula: `Stock / PW Sales`. Grain: SKU. Owner: Buying Team.
- **RRP Markdown** — Monetary difference between RRP and paid price. Formula: `(RRP × Qty) - Net Sales`. Grain: order-line. Owner: Commercial Director.
- **Full-Price Sales %** — Share of sales at zero discount. Formula: `Full-Price Sales / Gross Sales`. Grain: SKU-week. Owner: Buying Team.

---

## Results

**Before:**
- Trade reporting took hours to complete
- Decisions made on instinct and conflicting spreadsheets
- No forward planning view
- Silent errors in manual formulas
- "Built by a consultant" — bus factor of 1

**After:**
- Reporting takes 15–20 minutes weekly to refresh Shopify exports
- 10–20% week-on-week lift from in-season pricing and buying interventions on live margin data
- Month-level forecasts with cashflow view and pre-season planning tools
- Governed tables with Dataform assertions, auto-updating dashboards
- Documented architecture, modular code, team can self-serve

---

## Client Feedback

> "Working with Joseph has completely changed the way we understand and use our commercial data. He understands that the value of data is not in producing more reports. It is in helping the team make better buying, stock and markdown decisions at speed. I would happily recommend Joseph to any retail or consumer business looking to turn complex data into clear, intelligent and commercially valuable insights."

— **Robena**, Commercial Director

---

## About

Built by **[Joseph @ Hood Operations](https://hoodoperations.com)**.

I install the commercial planning, forecasting and reporting systems that make revenue predictable — **engineered in Dataform, governed by tests, documented for your team, built to last after I leave.**

**[hoodoperations.com](https://hoodoperations.com)**  
**joseph@hoodoperations.com**


