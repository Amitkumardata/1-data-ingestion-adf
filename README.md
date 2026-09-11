# Tittle: P-2-Retail-Analytics-Data-Modernization
 "Hybrid Retail Data Modernization and Cloud Migration Platform"

Problem:
"A retail supply Chain dataset track the product movement, orders and sales performance from manufactured to end customer: Multiple Pipeline were failed every night 
No automation for monitoring data received once in a week due to data analytics team analyze blindly and assume no accurate data

Overview
Designed and Implement a hybrid retail data platform integrating on-prem ERP,POS and supply chain system with azure cloud service for centerlized analytics scalable processing and enterprises reporting . Build modern data pipeline and cloud based transformation frameworks enabling efficient reporting and operational Insight

Medallion Architecture: 
Show the Bronze -> Silver -> Gold diagram and List the tools you used 

TECH STACK:
AZURE DATA FACTORY: Ingested data from source system to landing moved into ADLS gen 2 
AZURE DATABRICKS: Data transformation created medallion Architecture
|__My Organization
|    |-Compute
|       |--volume 
|           |--lives raw data
|  
| |_TABLES/
|    |--Silver_layer
|    |-- Gold layer

HOW TO RUN (the important one)
   Step-by-step commands:
      python src/bronze/ingest.py
      python src/silver/clean.py
      python src/gold/transform.py
      python src/run_pipeline.py

RESULTS (numbers - from Day 10)
   Before/after optimization numbers, row counts,
   flagged transactions found. NUMBERS make it strong.

   CHALLENGES & TRADE-OFFS (honest)
   -The numeric columns like sales and Quanity data rows store string type dificult to calculate then to_cast(Double) to convert the data type from string to numeric float
   - create quarantine folder to store  irrelevant data like negative transaction amount invalid data null values
   "The pipeline was slow because it looped row by
   row; I fixed it with vectorization, cutting runtime
   from 27s to 3s."

   Repository Structure
   '''
  |__data/
  | |-bronze\   # Raw ingestion data 
  | |-silver\    # Cleaned & structure data
  | |-gold\      # Organized business ready data for report
  |
  |__Notebooks/
  | |-Ingestion
  | |-Cleaning.ipynb
      

