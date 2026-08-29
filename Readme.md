# 🌾 Smart Agriculture Data Lake & Crop Intelligence Pipeline

An end-to-end cloud-based data engineering project that integrates **weather, soil, crop yield, and market price data** into a centralized data lake and transforms it into analytics-ready datasets for agricultural insights.

The project demonstrates a complete modern data engineering pipeline using **Azure Data Factory, Azure Data Lake Storage Gen2, Azure Databricks, PySpark, Delta Lake, and Power BI**.

---

## 📌 Project Overview

Agricultural decision-making requires combining multiple types of data such as:

- 🌦️ Weather conditions
- 🌱 Soil properties
- 🌾 Crop production and yield
- 💰 Agricultural market prices

These datasets usually come from different sources and have different structures.

This project builds an automated data pipeline that:

1. Ingests raw agricultural CSV files.
2. Stores them in Azure Data Lake Storage Gen2.
3. Creates Bronze Delta tables for raw data.
4. Performs data validation and quality checks.
5. Cleans and transforms data into Silver Delta tables.
6. Creates Gold analytical datasets.
7. Orchestrates the entire workflow using Azure Data Factory.
8. Visualizes agricultural insights using Power BI.

---

# 🏗️ Architecture

```text
                    ┌─────────────────────┐
                    │   Raw CSV Datasets  │
                    │                     │
                    │  Weather            │
                    │  Soil               │
                    │  Crop Yield         │
                    │  Market Price       │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Azure Data Factory   │
                    │                     │
                    │ Copy Activity       │
                    │ ForEach Activity    │
                    │ Trigger             │
                    └──────────┬──────────┘
                               │
                               ▼
              ┌────────────────────────────────┐
              │ Azure Data Lake Storage Gen2   │
              │                                │
              │          RAW Layer              │
              │  weather/weather.csv           │
              │  soil/soil.csv                 │
              │  crop_yield/crop_yield.csv     │
              │  market_price/market_price.csv │
              └───────────────┬────────────────┘
                              │
                              ▼
              ┌────────────────────────────────┐
              │       Azure Databricks         │
              │                                │
              │       Bronze Layer             │
              │   Raw Delta Tables             │
              │                                │
              │       Silver Layer             │
              │   Cleaned Delta Tables         │
              │                                │
              │       Gold Layer               │
              │   Analytics-ready Tables       │
              └───────────────┬────────────────┘
                              │
                              ▼
                    ┌─────────────────────┐
                    │      Power BI       │
                    │                     │
                    │ Dashboards &        │
                    │ Agricultural        │
                    │ Analytics            │
                    └─────────────────────┘
🛠️ Technologies Used
Technology	Purpose
Python	Data processing and automation
PySpark	Distributed data transformation
SQL	Data querying and analytics
Azure Data Factory	Data ingestion and orchestration
Azure Data Lake Storage Gen2	Cloud data storage
Azure Databricks	Data processing
Delta Lake	Reliable data storage
Power BI	Data visualization
Git/GitHub	Version control
📂 Datasets

The project works with four agricultural datasets.

1. 🌦️ Weather Dataset

Contains weather information for agricultural regions.

Important columns:

date
district
latitude
longitude
temperature_c
precipitation_mm
relative_humidity_pct
solar_radiation_kwh_m2
2. 🌱 Soil Dataset

Contains soil characteristics for different locations.

Important columns:

sample_date
state
district
soil_type
ph
organic_carbon_pct
nitrogen_kg_ha
phosphorus_kg_ha
potassium_kg_ha
moisture_pct
3. 🌾 Crop Yield Dataset

Contains crop production and yield information.

Important columns:

state
district
year
crop
area_harvested_ha
production_tonnes
yield_kg_per_ha
4. 💰 Market Price Dataset

Contains agricultural commodity market prices.

Important columns:

arrival_date
state
district
market
commodity
variety
min_price_per_quintal
max_price_per_quintal
modal_price_per_quintal
☁️ Azure Data Lake Storage Structure

The ADLS Gen2 storage account contains three main processing layers.

smartagrinil
│
├── raw
│   ├── weather
│   │   └── weather.csv
│   │
│   ├── soil
│   │   └── soil.csv
│   │
│   ├── crop_yield
│   │   └── crop_yield.csv
│   │
│   └── market_price
│       └── market_price.csv
│
└── processed
    │
    ├── bronze
    │   ├── weather
    │   ├── soil
    │   ├── crop_yield
    │   └── market_price
    │
    ├── silver
    │   ├── weather
    │   ├── soil
    │   ├── crop_yield
    │   └── market_price
    │
    └── gold
        ├── ...

🥉 Bronze Layer

The Bronze layer stores the ingested datasets in Delta Lake format.

The Bronze layer provides a reliable intermediate copy of the raw data before transformations are applied.

Bronze Processing

For each dataset:

CSV
 ↓
Spark CSV Reader
 ↓
Schema Inference
 ↓
Data Validation
 ↓
Delta Format
 ↓
Bronze Layer

Example:

df = (
    spark.read
    .format("csv")
    .option("header", "true")
    .option("inferSchema", "true")
    .load(raw_path)
)

df.write \
    .format("delta") \
    .mode("overwrite") \
    .save(bronze_path)
🔍 Bronze Data Validation

The Bronze ingestion process performs validation checks such as:

Schema validation
Null checking
Duplicate detection
Numeric field validation
Invalid negative values
Required field validation
Record count verification

The purpose is to identify invalid records before further processing.

🥈 Silver Layer

The Silver layer contains cleaned and standardized datasets.

The Silver transformation process includes:

Data Cleaning
Remove duplicate records
Remove completely empty rows
Trim unnecessary spaces
Handle missing values
Validate numeric columns
Remove invalid negative values
Validate important business columns
Standardize data types

The general flow is:

Bronze Delta
     ↓
Read Delta
     ↓
Remove duplicates
     ↓
Remove empty records
     ↓
Trim strings
     ↓
Handle missing values
     ↓
Validate numeric values
     ↓
Apply business rules
     ↓
Silver Delta
🥇 Gold Layer

The Gold layer contains analytics-ready datasets.

The purpose of the Gold layer is to transform cleaned data into datasets that are useful for business analysis and Power BI dashboards.

Examples of analytical operations include:

Aggregations
Grouping
Average calculations
Total production
Average crop yield
Market price analysis
Weather analysis
District-level analysis
Crop-level analysis
Joining related agricultural datasets

The Gold layer is optimized for reporting and analytical queries.

⚙️ Azure Data Factory Pipeline

Azure Data Factory is used to orchestrate the complete data pipeline.

Main Pipeline
PL_Ingest_Agriculture_Data

The pipeline uses a ForEach Activity to process all four datasets.

The configuration contains:

[
    {
        "folder": "weather",
        "file": "weather.csv"
    },
    {
        "folder": "soil",
        "file": "soil.csv"
    },
    {
        "folder": "crop_yield",
        "file": "crop_yield.csv"
    },
    {
        "folder": "market_price",
        "file": "market_price.csv"
    }
]

🔄 ForEach Activity

Instead of creating four separate Copy Activities, a single ForEach activity is used.

ForEach
   │
   ├── Weather
   │      ↓
   │   Copy Activity
   │
   ├── Soil
   │      ↓
   │   Copy Activity
   │
   ├── Crop Yield
   │      ↓
   │   Copy Activity
   │
   └── Market Price
          ↓
      Copy Activity

This makes the pipeline reusable and easier to maintain.

📥 Copy Activity

The Copy Activity transfers files from the source location into the Raw layer of ADLS Gen2.

Source
   ↓
CSV Dataset
   ↓
Copy Activity
   ↓
ADLS Gen2
   ↓
Raw Layer

🔗 ADF → Databricks Orchestration

After ingestion, Azure Data Factory triggers Databricks notebooks.

The pipeline follows:

ADF Ingestion
      ↓
Databricks Bronze
      ↓
Databricks Silver
      ↓
Databricks Gold

The Databricks notebook activities execute the transformation logic.

📓 Databricks Notebooks

The project contains three main transformation notebooks.

01_Bronze_Ingestion

Responsibilities:

Read CSV files
Infer/validate schema
Validate incoming records
Write Delta tables
Store data in Bronze layer
02_Silver_Cleaning

Responsibilities:

Read Bronze Delta tables
Remove duplicates
Remove empty records
Trim string columns
Handle missing values
Validate numeric columns
Remove invalid values
Write cleaned Delta tables
03_Gold_Analytics

Responsibilities:

Read Silver Delta tables
Perform analytical transformations
Aggregate agricultural data
Create reporting datasets
Write Gold Delta tables

⏰ ADF Trigger

An ADF trigger is configured to automate pipeline execution.

Instead of manually running the pipeline, the trigger can execute the pipeline according to a defined schedule.

Example:

Scheduled Trigger
       ↓
ADF Pipeline
       ↓
Data Ingestion
       ↓
Bronze
       ↓
Silver
       ↓
Gold
       ↓
Power BI

📊 Power BI Dashboard

The Gold Delta data is used as the analytical source for Power BI.

The dashboard can provide insights such as:

Weather Analysis
Average temperature
Total precipitation
Humidity trends
District-wise weather conditions
Soil Analysis
Average soil pH
Soil moisture
Nitrogen levels
Phosphorus levels
Potassium levels
Soil type distribution
Crop Yield Analysis
Total production
Average yield
Crop-wise production
District-wise yield
Year-wise production trends
Market Analysis
Average modal price
Minimum market price
Maximum market price
Commodity-wise price
Market-wise price comparison
Price trends over time
📈 Suggested Power BI Dashboard Pages
Page 1 — Agriculture Overview

KPIs:

Total Production
Average Crop Yield
Average Market Price
Average Temperature
Average Soil Moisture

Charts:

Crop production
Yield by district
Market price trends
Weather trends
Page 2 — Crop Yield Analysis

Visualizations:

Crop-wise production
Crop-wise yield
District-wise production
Year-wise yield
Top performing crops
Page 3 — Soil Analysis

Visualizations:

Soil pH by district
Soil moisture
NPK distribution
Soil type distribution
Page 4 — Market Price Analysis

Visualizations:

Commodity prices
Market-wise prices
Minimum vs maximum price
Modal price trends
Top expensive commodities
Page 5 — Weather Analysis

Visualizations:

Temperature trend
Rainfall trend
Humidity
District-wise weather
Weather comparison
🔐 Security

Credentials and secrets should not be hard-coded in production notebooks.

Recommended approaches:

Azure Key Vault
Managed Identity
Azure Databricks secret scopes
Azure Data Factory managed identity

Example:

ADF
 │
 ├── Managed Identity
 │
 ▼
Azure Key Vault
 │
 └── Secrets
       │
       ▼
ADLS / Databricks

🚀 End-to-End Workflow

The complete project workflow is:

                 DATA SOURCES
                     │
                     ▼
             Azure Data Factory
                     │
              ForEach Activity
                     │
                     ▼
               Copy Activity
                     │
                     ▼
             ADLS Gen2 - RAW
                     │
                     ▼
          Databricks Bronze Layer
                     │
               Validation
                     │
                     ▼
          Databricks Silver Layer
                     │
                Cleaning
                     │
                     ▼
           Databricks Gold Layer
                     │
              Aggregation
                     │
                     ▼
                 Power BI
                     │
                     ▼
              Dashboards

🧪 Data Quality Checks

The pipeline performs several data quality checks:

Check	Description
Null Check	Identifies missing values
Duplicate Check	Removes duplicate records
Schema Check	Validates expected data types
Range Check	Detects invalid numeric values
Empty Row Check	Removes completely empty records
String Cleaning	Removes unnecessary spaces
Business Validation	Validates important fields
Record Count	Verifies records after processing

📁 Project Structure
Smart-Agriculture-Data-Engineering/
│
├── README.md
│
├── notebooks/
│   ├── 01_Bronze_Ingestion
│   ├── 02_Silver_Cleaning
│   └── 03_Gold_Analytics
│
├── datasets/
│   ├── weather.csv
│   ├── soil.csv
│   ├── crop_yield.csv
│   └── market_price.csv
│
├── adf/
│   └── pipelines/
│
└── powerbi/
    └── Smart_Agriculture_Dashboard.pbix

💡 Key Data Engineering Concepts Demonstrated

This project demonstrates practical knowledge of:

ETL pipelines
ELT architecture
Data Lake architecture
Medallion architecture
Azure Data Factory
Azure Data Lake Storage Gen2
Azure Databricks
PySpark
Delta Lake
Data validation
Data cleaning
Data transformation
Data aggregation
Pipeline orchestration
ForEach Activity
Copy Activity
Databricks Notebook Activity
ADF Triggers
Cloud data engineering
Business intelligence
Power BI

🏆 Project Highlights
End-to-End Pipeline

Built a complete data pipeline from raw agricultural CSV files to analytical dashboards.

Medallion Architecture

Implemented:

Bronze → Silver → Gold

for structured and maintainable data processing.

Scalable Processing

Used PySpark and Databricks for distributed data transformation.

Automated Ingestion

Used ADF ForEach and Copy Activities to ingest multiple datasets using a reusable pipeline.

Data Quality

Implemented validation and cleaning rules before analytical processing.

Analytics

Created Gold datasets optimized for agricultural reporting and Power BI visualization.

🔮 Future Enhancements

The project can be extended with:

Real-time weather data ingestion
Kafka streaming
Azure Event Hubs
Machine learning crop yield prediction
Crop recommendation system
Price prediction
Weather-based crop risk analysis
Azure Machine Learning integration
Data quality monitoring
Pipeline failure notifications
Incremental data loading
Slowly Changing Dimensions
Unity Catalog
CI/CD using Azure DevOps or GitHub Actions
🎯 Business Value

The pipeline can help agricultural stakeholders:

Compare crop yields across districts
Analyze soil health
Understand weather conditions
Monitor agricultural commodity prices
Identify high-performing crops
Analyze historical agricultural trends
Support data-driven agricultural decisions

👩‍💻 Author

Nilakshi Kuldhar
Aspiring Data Engineer

Technologies
Python | SQL | PySpark | Azure Data Factory
Azure Data Lake Gen2 | Databricks | Delta Lake | Power BI

⭐ Conclusion
The Smart Agriculture Data Lake & Crop Intelligence Pipeline demonstrates how modern cloud data engineering technologies can be used to transform raw agricultural data into reliable, cleaned, and analytics-ready information.
