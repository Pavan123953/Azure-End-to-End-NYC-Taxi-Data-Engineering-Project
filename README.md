
# 🚖 Azure End-to-End NYC Taxi Data Engineering Project

## 📌 Overview
A full-stack data engineering pipeline built on Microsoft Azure, designed to ingest and transform **NYC Taxi & Limo Commission (TLC)** trip records into analytics-ready datasets. This repo uses **Azure Data Factory**, **Databricks**, **Delta Lake**, and the **Medallion Architecture** (Bronze → Silver → Gold). The pipeline emphasizes **data quality**, **governance**, and **scalable analytics**.

## 📂 Data Involved

### 1. Source Data
- **NYC TLC Trip Records** (Green-2023):
  - Available as monthly **Parquet** files on NYC.gov.
  - Key fields include:
    - `pickup_datetime`, `dropoff_datetime`
    - `PULocationID`, `DOLocationID` (taxi zones)
    - `trip_distance`, `passenger_count`
    - `fare_amount`, `total_amount`, `payment_type`
    - `RatecodeID`, `store_and_fwd_flag`

### 2. Raw Data Folder
- This repo’s `Raw Data` folder mirrors the source data structure:
  ```
  Raw Data/
    green_tripdata_2024-01.parquet
    green_tripdata_2024-02.parquet
    ...
  ```
- Data is pre-downloaded from public TLC releases and stored in **ADLS Gen2** Bronze layer.

## 🏗️ Architecture
```
[ TLC Parquet files ] → ADF Ingestion → Bronze (Raw) → Databricks → Silver (Cleansed) → Gold (Aggregated)
           ↑                                         ↑                                ↑
     ADLS Gen2 Storage                    PySpark Processing               BI/ML Consumption
```
- **Bronze**: Raw trips stored as-is in Azure Data Lake.
- **Silver**: Structured Delta tables with:
  - Parsed timestamps, type-casting, zone mapping
  - Deduplication and null-handling
- **Gold**: Generated KPIs (e.g., trips per zone, revenue trends, peak hours) and ML feature tables.

## 🚀 Getting Started

1. **Prerequisites**
   - Azure subscription (ADF, Databricks, ADLS Gen2)
   - Delta Lake compatible runtime
   - Service principal or managed identity with storage access

2. **Deployment Steps**
   - Import ADF pipelines (Azure Resource Manager)
   - Spin up Databricks cluster
   - Import notebooks and configure storage/credentials
   - Trigger pipeline → ingest raw data → process layers → build Gold

## 🔍 Data Flow & Transformations

### Bronze
- Store raw Parquet files monthly/yearly
- No transformations to preserve raw fidelity

### Silver
- Parse timestamps, compute trip durations
- Map `PULocationID`/`DOLocationID` to neighborhood zones
- Clean anomalies (zero or abnormally high distances/fare)
- Sample schema:
  | Column            | Type        |
  |------------------|-------------|
  | pickup_datetime   | Timestamp   |
  | dropoff_datetime  | Timestamp   |
  | trip_duration     | Double      |
  | trip_distance     | Double      |
  | passenger_count   | Int         |
  | pickup_zone       | String      |
  | dropoff_zone      | String      |
  | payment_type      | Int         |
  | total_amount      | Double      |

### Gold
- Aggregated KPIs:
  - Trips per zone/hour/day
  - Revenue and fare averages
  - High-demand zones, rush-hour analytics
  - Prepared feature set for ML (e.g., demand prediction or fare prediction)

## 📊 Consumption Use Cases
- **BI Dashboards**: Connect Power BI to Gold for visual analytics
- **Databricks SQL**: Use Delta for ad-hoc queries
- **ML / Forecasting**: Feature tables ready for modeling, e.g., demand prediction



## 📚 References
- [NYC TLC Trip Record Data – Yellow, Green, FHV](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page)
- [NYC Taxi Trip Records Data Dictionary](https://www.nyc.gov/assets/tlc/downloads/pdf/data_dictionary_trip_records_yellow.pdf)
- [Azure Open Datasets – Yellow Taxi](https://azure.microsoft.com/en-us/services/open-datasets/catalog/nyc-tlc-yellow-taxi-trip-records/)


