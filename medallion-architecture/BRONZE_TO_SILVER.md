# Bronze-to-Silver Transformation

## Overview

This document describes the Bronze-to-Silver stage of the Medallion Architecture. The purpose of this layer is to transform raw ingested data into a clean, consistent, and trusted Silver dataset.

## Why this stage matters

Bronze data often contains issues such as:

- mixed data types
- invalid or null values
- inconsistent column names
- extra whitespace or formatting problems
- duplicate rows
- malformed records

The Silver layer fixes these issues before analytics and reporting.

## Typical processing flow

### 1. Load Bronze data

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("BronzeToSilver").getOrCreate()
df = spark.read.option("header", "true").csv("/mnt/data/bronze/raw_sales.csv")
```

### 2. Inspect schema and profile

```python
df.printSchema()
print("Row count:", df.count())
print(df.dtypes)
```

### 3. Standardize columns

```python
from pyspark.sql import functions as F

for col_name in df.columns:
    new_name = col_name.strip().lower().replace(" ", "_")
    df = df.withColumnRenamed(col_name, new_name)
```

### 4. Clean values

```python
df = df.withColumn("sales", F.trim(F.col("sales")))
df = df.withColumn("customer_id", F.trim(F.col("customer_id")))
```

### 5. Cast columns to correct types

```python
df = df.withColumn("sales", F.col("sales").cast("double"))
df = df.withColumn("quantity", F.col("quantity").cast("int"))
df = df.withColumn("order_date", F.to_date(F.col("order_date"), "yyyy-MM-dd"))
```

### 6. Validate and quarantine bad records

```python
invalid_df = df.filter(
    F.col("sales").isNull() |
    F.col("quantity").isNull() |
    F.col("order_date").isNull()
)

valid_df = df.filter(
    F.col("sales").isNotNull() &
    F.col("quantity").isNotNull() &
    F.col("order_date").isNotNull()
)
```

### 7. Remove duplicates

```python
valid_df = valid_df.dropDuplicates()
```

### 8. Add metadata columns

```python
silver_df = (
    valid_df
    .withColumn("loaded_at", F.current_timestamp())
    .withColumn("source_file", F.input_file_name())
    .withColumn("batch_id", F.lit("batch_2026_09_17"))
)
```

## Common issue encountered

In this project, the `sales` column was stored as a string instead of a numeric type, and several columns had inconsistent formatting and mismatched types. This required casting and validation before the data could be used reliably.

## Best practice

Always keep the invalid records separate in a quarantine table or folder so they can be reviewed without polluting the Silver layer.

## Output

The Bronze-to-Silver process should create:

- valid Silver dataset
- quarantine dataset for invalid rows
- audit metadata for traceability

## Summary

This stage is a data quality and standardization step. It turns raw ingestion data into trusted data that can support downstream analytics, dashboards, and the Gold layer.
