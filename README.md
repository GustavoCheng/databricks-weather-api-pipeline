# Weather API Pipeline with Databricks

This project is part of my Databricks learning portfolio.  
The goal is to build a basic data engineering pipeline using real weather data from the Open-Meteo API.

## Project Objective

The objective of this project is to ingest, transform, and analyze historical weather data for São Paulo using Databricks.

This project was built to practice core Databricks concepts such as API ingestion, PySpark transformations, Delta Tables, SQL analysis, medallion architecture, and GitHub version control.

The pipeline follows a simple medallion architecture:

```text
Open-Meteo API
   ↓
Raw Table
   ↓
Silver Table
   ↓
Gold Table
   ↓
SQL Analysis
```

## Data Source

The data is collected from the Open-Meteo Historical Weather API.

The project uses daily weather data for São Paulo, Brazil, including:

- Maximum temperature
- Minimum temperature
- Mean temperature
- Total precipitation
- Maximum wind speed

The data is retrieved in JSON format, transformed with Python and PySpark, and stored as Delta tables in Databricks.

## Technologies Used

- Databricks
- Python
- PySpark
- SQL
- Delta Tables
- Open-Meteo API
- GitHub

## Project Structure

```text
databricks-weather-api-pipeline/
├── notebooks/
│   ├── 01_ingest_weather_api.ipynb
│   ├── 02_transform_weather_data.ipynb
│   ├── 03_create_monthly_weather_gold.ipynb
│   └── 04_sql_analysis.ipynb
└── README.md
```

## Pipeline Steps

### 1. API Ingestion

The notebook `01_ingest_weather_api` connects to the Open-Meteo API, retrieves historical daily weather data for São Paulo, converts the response into a Spark DataFrame, and saves it as a Delta table.

Created table:

```text
portfolio.weather_api_pipeline.raw_weather_sao_paulo
```

This table represents the raw ingestion layer of the pipeline.

### 2. Data Transformation

The notebook `02_transform_weather_data` reads the raw table and applies transformations, including:

- Renaming columns to analytical names
- Creating year, month, and day columns
- Creating a rain flag
- Calculating daily temperature range
- Rounding numeric values
- Keeping ingestion metadata

Created table:

```text
portfolio.weather_api_pipeline.silver_weather_sao_paulo
```

This table represents the curated silver layer of the pipeline.

### 3. Gold Analytical Table

The notebook `03_create_monthly_weather_gold` aggregates the daily weather data into a monthly analytical table.

The table includes:

- Total days by month
- Average temperature
- Average maximum temperature
- Average minimum temperature
- Highest temperature
- Lowest temperature
- Total precipitation
- Number of rainy days
- Rainy days rate
- Average maximum wind speed
- Average temperature range

Created table:

```text
portfolio.weather_api_pipeline.gold_monthly_weather_sao_paulo
```

This table represents the gold layer, ready for analytics and dashboarding.

### 4. SQL Analysis

The notebook `04_sql_analysis` contains SQL queries to analyze monthly weather indicators.

The analysis includes:

- Monthly weather overview
- Hottest months
- Rainiest months
- Driest months
- Monthly evolution of temperature, precipitation, rainy days rate, and wind speed

## Databricks Tables

The following Delta tables were created:

```text
portfolio.weather_api_pipeline.raw_weather_sao_paulo
portfolio.weather_api_pipeline.silver_weather_sao_paulo
portfolio.weather_api_pipeline.gold_monthly_weather_sao_paulo
```

## Example SQL Queries

Monthly weather overview:

```sql
SELECT *
FROM portfolio.weather_api_pipeline.gold_monthly_weather_sao_paulo
ORDER BY year, month;
```

Hottest months:

```sql
SELECT
  year,
  month,
  month_name,
  city,
  avg_temperature_celsius,
  highest_temperature_celsius
FROM portfolio.weather_api_pipeline.gold_monthly_weather_sao_paulo
ORDER BY avg_temperature_celsius DESC
LIMIT 5;
```

Rainiest months:

```sql
SELECT
  year,
  month,
  month_name,
  city,
  total_precipitation_mm,
  rainy_days,
  rainy_days_rate
FROM portfolio.weather_api_pipeline.gold_monthly_weather_sao_paulo
ORDER BY total_precipitation_mm DESC
LIMIT 5;
```

Monthly evolution:

```sql
SELECT
  CONCAT(CAST(year AS STRING), '-', LPAD(CAST(month AS STRING), 2, '0')) AS year_month,
  city,
  avg_temperature_celsius,
  total_precipitation_mm,
  rainy_days_rate,
  avg_windspeed_max_kmh
FROM portfolio.weather_api_pipeline.gold_monthly_weather_sao_paulo
ORDER BY year, month;
```

## Key Learnings

This project helped me practice:

- Consuming a public REST API with Python
- Working with JSON data from an external source
- Transforming API data into structured tables
- Working with PySpark DataFrames
- Creating Delta tables in Databricks
- Organizing data using raw, silver, and gold layers
- Creating analytical tables for reporting
- Running SQL analysis on curated data
- Versioning a Databricks project with GitHub

## Conclusion

This project demonstrates a basic data engineering workflow in Databricks using real public weather data from an API.

It is a simple but practical foundation for learning API ingestion, data transformation, Delta Tables, SQL analytics, and GitHub-based project versioning.
