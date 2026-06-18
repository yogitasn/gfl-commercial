Route Profitability Analytics Pipeline (Delta Lake)
📌 Overview

This project builds a Medallion Architecture (Bronze → Silver → Gold) pipeline using PySpark + Delta Lake to analyze route profitability for a commercial waste management dataset.

The goal is to:

Ingest raw route-level data
Build clean business-ready KPIs
Identify underperforming routes
Provide BI-ready aggregated insights sliceable by date, region, BU, and area
🏗️ Architecture
CSV Source
   ↓
🥉 Bronze Layer (Raw Ingestion)
   ↓
🥈 Silver Layer (Clean + Feature Engineering)
   ↓
🥇 Gold Layer (Aggregated BI-ready dataset)
   ↓
Power BI / Tableau Dashboard
🥉 Bronze Layer
Purpose

Raw ingestion of CSV data into Delta format without business transformations.

Key Actions
Schema enforcement
Data type casting
Storage in Delta table
Output Table
bronze.route_raw
🥈 Silver Layer
Purpose

Transform raw data into analysis-ready route-level dataset.

Key Transformations
Removed duplicates (route_id + date)
Handled nulls safely
Created business KPIs
Key Features Created
Operational KPIs
stop_completion_rate
route_utilization_pct
delay_minutes flag
Efficiency Metrics
cost_per_stop
revenue_per_km
fuel_efficiency
labour_efficiency
Risk Flags
underperforming_flag
margin_category
Output Table
silver.route_profitability
🥇 Gold Layer
Purpose

Create BI-ready aggregated dataset for reporting and dashboards.

Grain

date + region + BU + area

Key Aggregations
Revenue & Cost
total_revenue
total_cost
total_profit
fuel_cost, labour_cost, disposal_cost, etc.
Operational Metrics
stop_completion_rate (weighted)
route_utilization
Efficiency Metrics
avg revenue per km
avg fuel efficiency
Output Table
gold.route_profitability_summary
📊 Dimensional Model (Logical Design)

Although Gold acts as the fact table, a logical star schema supports BI slicing.

Fact Table
fact_route_profitability
Dimensions
dim_date
dim_region
dim_bu
dim_area
dim_route
ERD
                dim_date
                    |
dim_region --- fact_route_profitability --- dim_route
                    |
                 dim_bu
                    |
                dim_area
⚡ Delta Lake Features Used
1. Schema Evolution

Used overwriteSchema=true to allow iterative KPI development.

2. Partitioning

Gold table partitioned by:

year, month
3. Z-Ordering

Improves query performance for BI filters:

OPTIMIZE gold.route_profitability_summary
ZORDER BY (region, bu, area);
4. Time Travel (Delta Feature)

Enables rollback and historical comparison of KPI definitions.

📉 Underperforming Route Definition

A route is considered underperforming if:

Gross margin < 15%
OR gross profit < 0
OR operational inefficiencies exist (low stop completion or utilization)
🔍 Key Analysis Performed
1. Underperforming Routes

Identified routes with:

Low margin
High cost per stop
Low completion rate
2. Cost Drivers

Breakdown of:

fuel cost %
labour cost %
disposal cost %
3. Trend Analysis (3 Years)

Evaluated:

profitability trend
cost inflation impact
operational efficiency changes
💡 Key Insights
Labour cost is the dominant driver of low-margin routes
Fuel inefficiency strongly impacts rural regions
Certain BUs consistently underperform due to route density imbalance
Profitability shows moderate improvement over time but cost pressure remains high