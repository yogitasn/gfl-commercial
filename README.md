## Overview

This project builds a Medallion Architecture (Bronze → Silver → Gold) pipeline using PySpark + Delta Lake to analyze route profitability for a commercial waste management dataset.

## Objectives
Ingest raw route-level data
Build clean business-ready KPIs
Identify underperforming routes
Provide BI-ready aggregated insights sliceable by date, region, BU, and area

## Architecture
CSV Source
    ↓
Bronze Layer (Raw Ingestion)
    ↓
Silver Layer (Data Cleaning + Feature Engineering)
    ↓
Gold Layer (Aggregated BI-ready Dataset)
    ↓
Power BI / Tableau Dashboard

## Bronze Layer
# Purpose

Raw ingestion of CSV data into Delta format without any transformations.

# Key Actions
Schema enforcement
Data type casting
Storage in Delta table

# Output Table
gfl-bronze.routes

## Silver Layer
# Purpose

Transform raw data into analysis-ready route-level dataset.

1. Key Transformations
Removed duplicates (route_id + date)
Handled missing values
Created business KPIs
Key Features Created
2. Operational KPIs
stop_completion_rate
route_utilization_pct
delay_minutes flag
3. Efficiency Metrics
cost_per_stop
revenue_per_km
fuel_efficiency
labour_efficiency
4. Risk Flags
underperforming_flag
margin_category
5. Output Table
silver.route_profitability
Gold Layer
Purpose

Create BI-ready aggregated dataset for dashboards and reporting.

📐 Grain

date + region + BU + area

1. Key Aggregations
Revenue & Cost
total_revenue
total_cost
total_profit
fuel_cost, labour_cost, disposal_cost, maintenance_cost, admin_cost
2. Operational Metrics
stop_completion_rate (aggregated)
route_utilization
3. Efficiency Metrics
avg revenue per km
avg fuel efficiency
4. Output Table
gold.route_profitability_summary
📊 Dimensional Model (Logical Design)

Although Gold acts as the fact table, a logical star schema supports BI slicing.

# Fact Table
fact_route_profitability
# Dimensions
dim_date
dim_region
dim_bu
dim_area
dim_route
🧩 ERD
                dim_date
                    |
dim_region --- fact_route_profitability --- dim_route
                    |
                 dim_bu
                    |
                dim_area

Delta Lake Features Used

1. Schema Evolution

Enabled using:

option("overwriteSchema", "true")

Allows iterative KPI and schema updates.

2. Partitioning Strategy

Gold table partitioned by:

year, month

Improves performance for time-based queries.

3. Z-Ordering

Optimizes BI filtering performance:

OPTIMIZE gold.route_profitability_summary
ZORDER BY (region, bu, area);
4. Time Travel

Delta Lake enables:

Historical KPI comparison
Data rollback
Auditability
# Underperforming Route Definition

A route is considered underperforming if:

Gross margin < 15%
OR gross profit < 0
OR operational inefficiencies (low stop completion / utilization)
# Key Analysis Performed
1. Underperforming Routes

Identified routes with:

Low margin
High cost per stop
Poor completion rate
2. Cost Driver Analysis

Breakdown of:

Fuel cost %
Labour cost %
Disposal cost %
3. Trend Analysis (3-Year Window)

Evaluated:

Profitability trends
Cost inflation impact
Operational efficiency changes
# Key Insights
Labour cost is the dominant driver of low-margin routes
Fuel inefficiency strongly impacts rural regions
Certain BUs consistently underperform due to route density issues
Profitability improves slightly over time but cost pressure remains high
# Recommendation
Optimize route clustering in rural regions
Reduce empty travel distance to improve fuel efficiency
Improve labour utilization per route
Focus on high-cost BU restructuring

# Tech Stack
PySpark
Delta Lake
Databricks
SQL
Python

# AI Usage Disclosure

AI tools were used to:

Assist with PySpark transformation logic
Help design dimensional model
Refine KPI definitions
Improve documentation quality

All outputs were validated and adjusted for correctness.
 
# Summary

This project demonstrates:

End-to-end Delta Lake pipeline (Bronze → Silver → Gold)
Medallion architecture implementation
KPI engineering for logistics profitability
Cost driver analysis and business insights
BI-ready data modeling with Delta optimizations