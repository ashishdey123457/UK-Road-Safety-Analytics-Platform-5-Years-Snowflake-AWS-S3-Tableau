# UK Road Safety Analytics Platform (5 Years) | AWS S3 + Snowflake + Tableau

### Dashboard Link
https://public.tableau.com/views/tableausnowflakedashboardUKRoadAccident/Dashboard1

## Dashboard Preview
<img width="1498" height="904" alt="Image" src="https://github.com/user-attachments/assets/886aa4cd-7ee2-4e5b-a85f-1935a2c8ab75" />

---
## Data Source
https://www.gov.uk/government/statistical-data-sets/road-safety-open-data

---

## Problem Statement

UK road safety data is large and spread across multiple datasets (Collisions, Vehicles, Casualties). It can be difficult to turn raw CSV files into a clean analytics model and then build dashboards that are accurate, fast, and easy to explore.

This project builds an end-to-end analytics workflow that ingests raw UK road safety data into a cloud warehouse, models it for BI, and delivers an interactive Tableau dashboard to explore trends, severity patterns, hotspots, and vehicle involvement.

---

## What the dashboard shows

- KPI overview (collisions, casualties, severity)
- Year-based filtering and trend analysis
- Collision severity breakdown
- Geographic hotspot analysis using latitude/longitude map
- Vehicle type vs collisions analysis
- Contributing factors analysis (speed limit, road type, weather, light, road surface)

---

## Data Source

Official UK Government Road Safety data (5-year history), split into:
- Collisions
- Vehicles
- Casualties

---

## Architecture

UK Road Safety CSV files → AWS S3 → Snowflake → Tableau

- AWS S3 stores raw CSV files (separate buckets for collision, vehicle, casualty)
- Snowflake handles ingestion, type conversion, modeling, and BI views
- Tableau connects to Snowflake analytics views to build dashboards

---

## Snowflake Data Modeling (High Level)

### Schemas
- `UK_ROAD_SAFETY.RAW`  
  Raw landing tables (all columns stored as STRING)
- `UK_ROAD_SAFETY.STAGING`  
  Cleaned tables with correct data types using `TRY_TO_*` conversions
- `UK_ROAD_SAFETY.ANALYTICS`  
  BI-ready fact table, rollups, and views for Tableau

### Core Analytics Objects
- `ANALYTICS.FACT_COLLISION`  
  Collision-level fact table (one row per collision)
- `ANALYTICS.AGG_VEHICLE_BY_COLLISION`  
  Vehicles per collision
- `ANALYTICS.AGG_CASUALTY_BY_COLLISION`  
  Casualties per collision
- `ANALYTICS.VW_COLLISION_TABLEAU`  
  Tableau-ready collision dataset (fact + rollups)
- `ANALYTICS.VW_COLLISIONS_GEO`  
  Map-optimized dataset (lat/long non-null)
- `ANALYTICS.VW_KPI_BY_YEAR`  
  Year-level KPI summary
- `ANALYTICS.VW_VEHICLE_ANALYSIS`  
  Vehicle analysis view joined with collision attributes

---

## Steps Followed

### 1) AWS S3 Setup
- Uploaded raw CSV files to S3 (separate buckets for collision, vehicle, casualty)

### 2) Snowflake Integration and Load
- Created Storage Integration for S3 access
- Created a CSV file format
- Created external stages for each S3 bucket
- Loaded data using `COPY INTO` into RAW tables

### 3) RAW Layer (Landing)
- Created raw tables as STRING columns:
  - `RAW.COLLISION_RAW`
  - `RAW.VEHICLE_RAW`
  - `RAW.CASUALTY_RAW`

### 4) STAGING Layer (Cleaning + Type Casting)
- Created staging tables with correct data types using `TRY_TO_NUMBER`, `TRY_TO_DATE`, `TRY_TO_DOUBLE`, etc.:
  - `STAGING.COLLISION`
  - `STAGING.VEHICLE`
  - `STAGING.CASUALTY`

### 5) ANALYTICS Layer (BI Modeling)
- Built collision fact table at collision grain:
  - `ANALYTICS.FACT_COLLISION`
- Created rollups to avoid duplicate joins and improve BI performance:
  - `ANALYTICS.AGG_VEHICLE_BY_COLLISION`
  - `ANALYTICS.AGG_CASUALTY_BY_COLLISION`
- Created Tableau-ready view:
  - `ANALYTICS.VW_COLLISION_TABLEAU`
- Built KPI and map views:
  - `ANALYTICS.VW_KPI_BY_YEAR`
  - `ANALYTICS.VW_COLLISIONS_GEO`
- Built vehicle analysis view:
  - `ANALYTICS.VW_VEHICLE_ANALYSIS`

### 6) Tableau Dashboarding
- Connected Tableau to Snowflake analytics views
- Built dashboard sheets including KPIs, trends, severity, geo map, and vehicle type analysis
- Added interactivity (year filter, tooltips)
- Published the dashboard to Tableau Public

---

## Data Quality & Validation

Validation checks included:
- Stage file listing checks (`LIST @stage`)
- Row count checks after loads
- Distinct key checks (collision, vehicle, casualty)
- Sample data sanity checks (`LIMIT` queries)

---

## Insights 

- Collision patterns vary across years and locations, with clear hotspot clusters on the map.
- Severity breakdown helps compare risk patterns across time periods.
- Vehicle type analysis highlights which categories are most involved in collisions.
- Road and environmental factors can be explored to compare collision patterns under different conditions.

---

## Tools Used

- AWS S3
- Snowflake (SQL, stages, COPY INTO, views, modeling)
- Tableau Desktop / Tableau Public

---

## Author

Created by Ashish Dey

Feel free to open an issue or connect for suggestions or improvements.
