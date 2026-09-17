# Hybrid Retail Data Modernization and Cloud Migration Platform

## Project Overview

This project focuses on modernizing a retail analytics platform by integrating on-premise ERP, POS, and supply chain systems with Azure cloud services. The goal is to centralize operational and sales data, improve ingestion reliability, standardize transformations, and enable faster, more trusted analytics.

## Business Problem

The retail organization was receiving weekly supply-chain and sales data across multiple sources, but the ingestion pipelines were unstable and reporting teams were analyzing inconsistent and incomplete data. Daily operations required a reliable ingestion process, standardized data quality checks, and a scalable cloud-based modern data platform.

## Solution Architecture

The solution uses a hybrid data modernization approach across multiple layers:

- Azure Data Factory for ingestion
- ADLS Gen2 for storage
- PySpark for transformation
- Medallion architecture to structure data into Bronze, Silver, and Gold
- Azure-based analytics platform for reporting and decision-making

## Medallion Architecture

Bronze -> Silver -> Gold

- Bronze: raw and ingested data
- Silver: cleaned, validated, and standardized data
- Gold: business-ready analytical data for dashboards and reporting

## Tech Stack

- Azure Data Factory (ADF)
- Azure Data Lake Storage Gen2
- PySpark
- Databricks or notebook-based processing
- Power BI / analytics layer (planned)

## Repository Structure

```text
1-data-ingestion-adf/
├── ingestion/
│   ├── README.md
│   └── pipeline-notes/
├── medallion-architecture/
│   ├── README.md
│   └── BRONZE_TO_SILVER.md
├── notebooks/
│   └── README.md
├── docs/
│   └── README.md
├── README.md
└── .gitignore
```

## Current Progress

- ADF ingestion pipeline designed and documented
- Source-to-landing data movement configured
- Bronze-to-Silver transformation documented
- Data quality and quarantine handling introduced
- Validation and profiling checklist in progress

## Key Challenges Solved

- Mixed data types in raw data, especially numeric fields stored as strings
- Inconsistent column naming and formatting
- Null and invalid records in source data
- Duplicate records and data quality issues
- Need for quarantine records to isolate bad data

## Recommended GitHub Repository Name

The recommended repo name for this project is:

`hybrid-retail-analytics-modernization`

This is the name that best matches the portfolio project and the domain focus of retail analytics and modernization.

## Next Steps

- Continue Bronze-to-Silver documentation
- Add data profiling output and file-level checks
- Prepare Silver-to-Gold transformation notes
- Add operational runbook and troubleshooting documentation
