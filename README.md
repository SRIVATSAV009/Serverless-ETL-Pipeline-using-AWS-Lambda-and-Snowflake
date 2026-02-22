# Serverless ETL Pipeline using AWS Lambda, Glue (Spark) and Snowflake

## 📌 Overview

This project implements a fully automated, serverless ETL pipeline that extracts job market data from the Adzuna API, transforms it using AWS Glue with Apache Spark, and loads it into Snowflake using Snowpipe for near real-time analytics.

The architecture is event-driven, scalable, and production-ready.

---

## 🏗️ Architecture Overview

EventBridge (Scheduler)
→ AWS Step Functions (Orchestration)
→ Lambda (API Extraction)
→ Amazon S3 (Raw Data Layer)
→ AWS Glue + Apache Spark (Transformation)
→ Amazon S3 (Curated Parquet Layer)
→ Snowpipe (Auto Ingestion)
→ Snowflake (Data Warehouse)

---

## 🛠️ Technologies Used

- AWS Lambda
- AWS Glue (Apache Spark)
- AWS Step Functions
- Amazon S3
- Amazon EventBridge
- AWS IAM
- Snowflake
- Snowpipe
- Adzuna API
- Python (boto3, requests)
- Parquet format

---

## 📂 Project Structure
.
├── aws_glue_spark_transform.py
├── aws_step_function.json
├── lambda_extract.py
├── lambda_move_processed_files.py
├── snowflake_scripts.sql
└── README.md


---

## 🔹 Data Flow Explained

### 1️⃣ Extraction Layer

- Lambda function calls Adzuna API
- Handles pagination
- Stores raw JSON into:
  
- s3://adzuna-etl-project/raw_data/to_process/
- 
This acts as the **Raw (Bronze) Layer**.

---

### 2️⃣ Transformation Layer

- AWS Glue reads raw JSON
- Apache Spark:
  - Explodes nested JSON
  - Selects required fields
  - Deduplicates records
  - Converts to Parquet
- Writes output to:
  
s3://adzuna-etl-project/transformed_data/

This acts as the **Processed (Silver) Layer**.

---

### 3️⃣ Raw File Management

- Lambda moves processed raw files from:
  - `raw_data/to_process/`
  - to `raw_data/processed/`
- Prevents duplicate processing
- Ensures idempotent pipeline runs

---

### 4️⃣ Orchestration

AWS Step Functions manages:

1. Extraction Lambda
2. Glue transformation job
3. File movement Lambda

Includes error handling and retry logic.

---

### 5️⃣ Data Warehouse Integration

Snowflake is configured with:

- Storage Integration
- External Stage
- File Format (Parquet)
- Target table

Snowpipe automatically ingests new Parquet files from S3 into Snowflake.

---

### 6️⃣ Scheduling

Amazon EventBridge triggers the Step Function daily using a cron schedule.

---
🔐 Security & IAM

Least privilege IAM roles

Lambda execution role

Glue execution role

Step Function execution role

Snowflake IAM integration role

No hardcoded credentials (Environment variables used)

🚀 Key Features

Fully serverless architecture

Distributed Spark transformation

Columnar Parquet optimization

Event-driven ingestion (Snowpipe)

Automated daily execution

Raw and Processed data separation

Fault-tolerant orchestration

💡 Production Design Principles Applied

Idempotent processing

Separation of storage and compute

Immutable raw data

Columnar storage optimization

Secure IAM integrations

Modular pipeline stages

Scalable distributed transformation

📈 Potential Improvements

Add data quality validation (Great Expectations)

Implement Bronze/Silver/Gold architecture

Add monitoring dashboards

Implement CI/CD for infrastructure

Partition Parquet files for performance optimization

Add incremental merge strategy in Snowflake

🎯 Conclusion

This project demonstrates how to design and implement a modern, scalable, cloud-native ETL pipeline using AWS and Snowflake. It follows production-grade data engineering practices including automation, orchestration, distributed processing, and secure warehouse integration.

📎 Repository

GitHub Repository:
https://github.com/SRIVATSAV009/Serverless-ETL-Pipeline-using-AWS-Lambda-and-Snowflake
## 📊 Snowflake Table Schema

```sql
create or replace table adzuna_jobs_staging(
    job_id string,
    job_title string,
    job_location string,
    job_company string,
    job_category string,
    job_description string,
    job_url string,
    job_created timestamp,
    etl_at timestamp
);


