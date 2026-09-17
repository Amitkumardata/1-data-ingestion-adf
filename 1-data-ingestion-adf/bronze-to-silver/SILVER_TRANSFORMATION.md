# Bronze-to-Silver PySpark Transformation

This folder documents the PySpark processing used to transform Bronze data into clean Silver data.

## Processing steps

1. Read and load the Bronze data into a PySpark notebook.
2. Inspect the input using `printSchema()`, `count()`, and `dtypes`.
3. Create a separate Silver DataFrame.
4. Normalize and rename columns.
5. Remove leading and trailing spaces using `trim`/`strip`.
6. Select the required columns.
7. Cast columns to the expected data types.
8. Verify the final schema and transformed columns.
9. Add audit columns: `loaded_at`, `source_file`, and `batch_id`.
10. Validate required fields and business rules.
11. Remove duplicate records from the valid Silver data.
12. Create a quarantine DataFrame for invalid or rejected records.
13. Write quarantined records to a separate quarantine path.

## Data-quality challenge

During Bronze-to-Silver processing, the `sales` column was stored as a string and other columns had mismatched data types. The solution was to inspect the schema, trim and standardize values, cast columns to the expected types, and separate invalid records before writing the Silver output.

## Recommended quarantine metadata

Add an `_invalid_reason` column to explain why a record was rejected, for example:

- `missing_customer_id`
- `invalid_order_date`
- `negative_amount`
- `invalid_data_type`
- `duplicate_record`
