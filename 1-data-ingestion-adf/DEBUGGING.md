\# Debugging Issues \& Solutions



\## Issue 1: Sink Dataset Format Not Supported

\- Problem: Pipeline failed because sink dataset didn't support .xlsx format

\- Root Cause: ADLS Gen2 sink only accepts formats like CSV, Parquet, not Excel files

\- Solution: 

&#x20; - Deleted Excel sink dataset

&#x20; - Created new sink dataset with CSV format

&#x20; - Re-ran pipeline with CSV sink

\- Result: ✅ Pipeline executed successfully

\- Data Movement: Source dataset → CSV sink dataset via pipeline run



\## Summary

Pipeline debugging complete. Now working with CSV format.

