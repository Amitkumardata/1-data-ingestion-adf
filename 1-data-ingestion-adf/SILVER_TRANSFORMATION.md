# Bronze-to-Silver PySpark Transformation

## Overview

This notebook performs the initial transformation and data-quality processing before data moves from the Bronze layer to the Silver layer.

## Processing steps

### 1. Load Bronze data

Read the Bronze input data into a PySpark DataFrame in the notebook.

### 2. Profile the input

Inspect the incoming data before transformation:

- `printSchema()` to review the schema
- `count()` to measure the number of records
- `dtypes` to review column data types

### 3. Build the Silver DataFrame

Create a separate DataFrame for Silver processing. The transformation includes:

- Normalizing column names
- Renaming columns where required
- Removing leading and trailing spaces with `trim`/`strip`
- Selecting the required columns
- Casting columns to the expected data types
- Verifying the final columns and schema

### 4. Add audit columns

Add the following metadata columns for traceability:

| Column | Purpose |
|---|---|
| `loaded_at` | Timestamp associated with loading the record |
| `source_file` | Name or path of the source file |
| `batch_id` | Identifier for the ingestion or processing batch |

### 5. Validate records

Apply validation rules for required fields, data types, and business conditions. Records that do not pass validation are treated as invalid.

### 6. Deduplicate valid data

Remove duplicate records from the valid dataset before writing to Silver.

### 7. Quarantine invalid records

Create a quarantine DataFrame containing rejected or invalid rows, then write it to a separate quarantine records path. This keeps bad records available for investigation without allowing them into the Silver layer.

A recommended enhancement is to add `_invalid_reason` to every quarantined row, such as `missing_customer_id`, `invalid_order_date`, `negative_amount`, `invalid_data_type`, or `duplicate_record`.

## Validation checklist

- [ ] Bronze data loads successfully.
- [ ] Input schema, count, and data types are inspected.
- [ ] Silver columns are normalized and correctly typed.
- [ ] Whitespace is removed from applicable string fields.
- [ ] Audit columns are populated.
- [ ] Duplicate records are removed.
- [ ] Invalid records are separated into quarantine.
- [ ] Silver and quarantine outputs are written to their expected paths.
