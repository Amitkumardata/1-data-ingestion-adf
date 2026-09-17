# Medallion Architecture - Bronze to Silver

## Overview

This document describes the Bronze-to-Silver transformation layer in the data pipeline. The purpose of this stage is to clean, validate, normalize, and enrich the raw Bronze data before it is moved to the Silver layer for downstream analytics.

## Why Bronze-to-Silver is important

Bronze data is raw and often messy. It may contain:

- inconsistent column names
- whitespace and formatting issues
- mixed data types
- nulls and invalid values
- duplicate records
- malformed rows

The Silver layer is where we make the data usable, reliable, and business-ready.

## Processing flow

### 1. Load the raw Bronze data

Read the raw data into a PySpark DataFrame:

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("BronzeToSilver").getOrCreate()

df = spark.read.option("header", "true").csv("/mnt/data/bronze/sales_data.csv")
```

### 2. Inspect the incoming data

Before cleaning, inspect the structure:

```python
df.printSchema()
print("Row count:", df.count())
print(df.dtypes)
```

This helps us check:

- column names
- data types
- nulls
- unexpected values
- size of the dataset

### 3. Standardize columns

Normalize the naming format and clean field names.

```python
for col_name in df.columns:
    new_name = col_name.strip().lower().replace(" ", "_")
    df = df.withColumnRenamed(col_name, new_name)
```

### 4. Clean string values

Remove unwanted whitespace and normalize inconsistent values:

```python
from pyspark.sql import functions as F

df = df.withColumn("sales", F.trim(F.col("sales")))
```

This step is important for values that contain extra spaces or formatting issues.

### 5. Cast columns to correct types

A common issue is that raw Bronze data stores numeric values as strings. These must be converted before analysis.

```python
df = df.withColumn("sales", F.col("sales").cast("double"))
df = df.withColumn("quantity", F.col("quantity").cast("int"))
df = df.withColumn("order_date", F.to_date(F.col("order_date"), "yyyy-MM-dd"))
```

### 6. Validate required fields

Check for null or invalid records before writing to Silver.

```python
invalid_df = df.filter(
    F.col("sales").isNull() |
    F.col("quantity").isNull() |
    F.col("order_date").isNull()
)
```

### 7. Remove duplicates

Duplicate records should be removed from the valid Silver dataset.

```python
valid_df = df.dropDuplicates()
```

### 8. Add audit columns

Add metadata for traceability and monitoring:

```python
silver_df = (
    valid_df
    .withColumn("loaded_at", F.current_timestamp())
    .withColumn("source_file", F.input_file_name())
    .withColumn("batch_id", F.lit("batch_2026_09_17"))
)
```

Recommended audit columns:

- `loaded_at` - timestamp when record was processed
- `source_file` - source file path or file name
- `batch_id` - batch or run identifier

### 9. Create a quarantine DataFrame

Invalid rows should not be written into the Silver dataset. Instead, they should be quarantined for review.

```python
quarantine_df = invalid_df.withColumn("_invalid_reason", F.when(F.col("sales").isNull(), "missing_sales")
    .when(F.col("quantity").isNull(), "missing_quantity")
    .when(F.col("order_date").isNull(), "missing_order_date")
    .otherwise("unknown_error"))
```

## Common challenge encountered

One of the main issues I encountered was that the `sales` column used string values instead of numeric values, while other columns also had inconsistent data types. This caused issues during calculation, filtering, and validation.

### Example issue

| Column | Bronze value | Expected type |
|---|---|---|
| sales | "1250.50" | double |
| quantity | "10" | int |
| order_date | "2026-09-01" | date |

### Solution

- trim whitespace
- rename columns consistently
- cast string columns to numeric/date types
- validate nulls and business rules
- quarantine invalid records
- keep only clean records in Silver

## Validation checklist

Before writing Silver data, confirm:

- [ ] Bronze data loads successfully
- [ ] Schema is inspected with `printSchema()`
- [ ] Column count and record count are checked
- [ ] Column names are standardized
- [ ] String values are trimmed
- [ ] Data types are cast correctly
- [ ] Nulls and invalid values are identified
- [ ] Duplicates are removed
- [ ] Audit columns are added
- [ ] Quarantine records are written separately

## Output structure

The final output should be split into:

1. Valid Silver dataset
2. Quarantine dataset for invalid rows

```python
silver_df.write.mode("overwrite").parquet("/mnt/data/silver/sales_cleaned")
quarantine_df.write.mode("overwrite").parquet("/mnt/data/quarantine/sales_invalid")
```

## Result

After Bronze-to-Silver processing, the data becomes:

- cleaner
- standardized
- validated
- ready for analytical use
- easier to trust in downstream reporting and ML workloads

## Summary

The Bronze-to-Silver stage is where raw ingestion data becomes trustworthy, consistent, and business-ready. This step is critical before moving data into the Gold layer or preparing dashboards and reports.
