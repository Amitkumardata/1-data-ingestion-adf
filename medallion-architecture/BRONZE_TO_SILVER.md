# Bronze-to-Silver Transformation

```text
Source: retailsupplychain.csv (order-line grain)
    →
ADF ingestion (orchestration)
    →
ADLS Bronze: raw/retail_sales/retailsupplychain.csv (unchanged)
    →
Databricks/PySpark Silver:
        - read Bronze CSV
        - clean types, standardize dates
        - add audit columns
        - optionally separate rejected rows
        - write as Delta: silver_retail_sales
    →
Gold tables (Delta):
        - gold_customer_daily_metrics
        - gold_product_category_daily_metrics
        - gold_region_daily_metrics
    →
Synapse / Azure SQL (or Fabric)
    →
Power BI reports and KPIs
```
