# retail-analytics-dataform

A modern analytics engineering project for a luxury retail brand, built in **Dataform** (Google's native BigQuery transformation tool) using dbt-compatible patterns: modular SQL, source-to-mart layering, dependency management, and automated assertions.

---
## The Problem
A fast-scaling luxury London retailer (1 physical store + 1 Shopify store) was running weekly and monthly trade reviews on fragmented Google Sheets that took hours to update. Shopify native exports did not present the full picture of profitability, and embedded logic made analysis prone to silent error. The Commercial Director was spending hours assembling data instead of acting on it. Worse, no two reports agreed on core metrics like gross margin or sell-through rate.

---
## The Solution
Built a governed, self-service analytics platform the whole team can run:

| Layer            | What It Does 

| **Sources**      | Shopify raw exports, sample sale data, inventory feeds, master range sheet, custom calendar |
| **Staging**      | Cleaned, renamed, type-cast — one model per source entity |
| **Intermediate** | Business logic: order cost allocation, rolling SKU aggregations, ISO calendar alignment |
| **Marts**        | Consumption-ready tables for Looker dashboards and Sheets forecasting |

---
## Tech Stack

| Layer          | Tool                | Purpose 

| Raw Data       | Shopify CSV exports | Source of truth 
| Data Warehouse | Google BigQuery     | Centralized, secure cloud storage 
| Transformation | Dataform*           | Modular SQL, dependency management, assertions 
| Visualization  | Looker Studio       | Live trade dashboards 
| Planning       | Google Sheets       | Forecasting & Open-to-Buy (connected to mart tables) 

*dbt was initially used to write the code and for transformation, but to save the client money (additional dbt seats would mean a subscription of 100 Euros per month) I chose Google's Dataform. 
---

## Key Design Decisions

### 1. Dataform over dbt Cloud
BigQuery-native, lower total cost of ownership, same modular SQL and testing capabilities. The right engineering decision for a cost-conscious scaling retailer.

### 2. 4-4-5 ISO Retail Calendar
Custom calendar aligning weeks to retail seasons (Summer/Winter) for true like-for-like comparisons. Enables consistent PW, PWLY, P-4W, and YTD reporting.

### 3. Line-Item Cost Allocation
Order-level costs (shipping, tax, discounts, refunds) apportioned down to SKU level for true margin analysis. Without this, profitability by product is wrong.

---

## Project Structure

definitions/
├── staging/
│   ├── stg_order.sqlx                  # Shopify + sample sale orders, cleaned & unioned
│   ├── stg_inventory.sqlx              # Stock levels by SKU and location
│   ├── stg_products.sqlx               # Product master with live pricing
│   ├── stg_raw_range_sheet.sqlx        # Enriched product attributes (costs, RRP, delivery)
│   └── stg_dim_date.sqlx               # 4-4-5 ISO calendar (2024–2034)
├── intermediate/
│   ├── int_orders_filled.sqlx          # Order-level cost allocation logic
│   ├── int_sku_sales_rolling.sqlx      # PW, PW-1, PWLY, P-4W, P-5-8W, YTD, YTD LY aggregations
│   └── int_sample_sales_enriched.sqlx  # Sample sale data enriched with product master
└── marts/
├── dim_products.sqlx                   # Product dimension (range sheet + live price)
├── fct_order_lines.sqlx                # Order line facts with all KPIs
├── fct_inventory.sqlx                  # Inventory snapshot joined to product
├── mart_sku_performance.sqlx           # SKU-level trade metrics (lifetime + rolling)
└── mart_inventory_enriched.sqlx        # Stock + sales unified for merchandising



---

## Data Lineage

![Dataform Lineage](assets/lineage.png)

---

## KPI Definitions

| KPI                | Definition                                     | Formula                          | Grain          | Owner 

| Gross Margin %     | Profit after COGS, excl. markdowns             | `(Net Sales - COGS) / Net Sales` | Order-line     | Commercial Director 
| Sell-Through Rate  | Units sold / units received in period          | `Sold / Received`                | SKU-week       | Buying Team 
| Open-to-Buy        | Planned purchases minus committed spend        | `Plan - (On-Order + Receipts)`   | Category-month | Commercial Director 
| Stock Weeks Cover  | Current stock / average weekly sales           | `Stock / PW Sales`               | SKU            | Buying Team 
| RRP Markdown       | Monetary difference between RRP and paid price | `(RRP × Qty) - Net Sales`        | Order-line     | Commercial Director 
| Full-Price Sales % | Share of sales at zero discount                | `Full-Price Sales / Gross Sales` | SKU-week       | Buying Team 

---

## Results

| Before                                                  | After 

| Trade reporting took hours to complete                  | Reporting takes 15–20 minutes weekly to refresh Shopify exports 
| Decisions made on instinct and conflicting spreadsheets | 10–20% week-on-week lift from in-season pricing and buying interventions on live margin data 
| No forward planning view                                | Month-level forecasts with cashflow view & pre-season planning tools 
| Silent errors in manual formulas                        | Governed tables with Dataform assertions, auto-updating dashboards 
| "Built by a consultant" — bus factor of 1               | Documented architecture, modular code, team can self-serve 

---

## Client Feedback

> "Working with Joseph has completely changed the way we understand and use our commercial data. He understands that the value of data is not in producing more reports. It is in helping the team make better buying, stock and markdown decisions at speed. I would happily recommend Joseph to any retail or consumer business looking to turn complex data into clear, intelligent and commercially valuable insights."

— Robena, Commercial Director

---

## About

Built by **[Joseph @ Hood Operations](https://hoodoperations.com)**.

I install the commercial planning, forecasting and reporting systems that make revenue predictable — **engineered in Dataform, governed by tests, documented for your team, built to last after I leave.**

**[hoodoperations.com](https://hoodoperations.com)**  
**joseph@hoodoperations.com**
