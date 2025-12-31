# Configuration Questionnaire: Data & Analytics
## Step-by-Step Service Selection for Data Workflows

---

## 📋 Table of Contents
1. [Data Lake Architecture](#data-lake-architecture)
2. [Real-Time Analytics Pipeline](#real-time-analytics-pipeline)
3. [Batch Processing Workflows](#batch-processing-workflows)
4. [Business Intelligence Solutions](#business-intelligence-solutions)
5. [ETL Configurations](#etl-configurations)
6. [Log Analytics](#log-analytics)

---

## 🏞️ SCENARIO 1: Data Lake Architecture

### Initial Questions:
```
Q1: What type of data are you storing?
    ├─ Structured (CSV, Parquet) → S3 + Glue + Athena
    ├─ Semi-structured (JSON, XML) → S3 + Glue + Athena
    └─ Unstructured (Images, Videos) → S3 + Recognition services

Q2: What is the data volume?
    ├─ < 1 TB → Simple S3 + Athena
    ├─ 1-100 TB → S3 + Glue + Athena
    └─ > 100 TB → S3 + Glue + Athena + Redshift Spectrum

Q3: Do you need real-time access?
    └─ YES → Add Kinesis for streaming ingestion

Q4: Who needs access?
    ├─ Data Scientists → SageMaker integration
    ├─ Business Analysts → QuickSight dashboards
    └─ Applications → Athena API or Redshift
```

### Configuration Matrix:

#### **Basic Data Lake (Small to Medium)**
```
Services Required:
├─ S3 (Data storage)
├─ AWS Glue (Data catalog + ETL)
├─ Athena (Query service)
├─ Lake Formation (Governance)
├─ IAM (Access control)
└─ CloudWatch (Monitoring)

Configuration Steps:

[Step 1: Storage Structure]
1. Create S3 Buckets:
   a. Raw Data Bucket:
      - s3://company-datalake-raw/
      - Folder structure: /year/month/day/
      - Versioning: Enabled
      - Encryption: SSE-S3
      
   b. Processed Data Bucket:
      - s3://company-datalake-processed/
      - Partitioned by date
      - Compressed (Parquet format)
      
   c. Analytics Bucket:
      - s3://company-datalake-analytics/
      - Aggregated datasets
      - Optimized for queries

2. S3 Lifecycle Policies:
   - Raw data: Move to IA after 30 days, Glacier after 90 days
   - Processed: Move to IA after 60 days
   - Analytics: Keep in Standard

[Step 2: Data Catalog]
3. AWS Glue Data Catalog:
   - Create database: "datalake_db"
   - Crawlers for automatic schema detection:
     a. Raw Data Crawler:
        - Source: s3://company-datalake-raw/
        - Schedule: Daily at midnight
        - Update existing tables
     
     b. Processed Data Crawler:
        - Source: s3://company-datalake-processed/
        - Schedule: After ETL jobs complete

4. Define Tables Manually (optional):
   - For known schemas
   - Partition keys for performance
   - Compression and file format settings

[Step 3: ETL Pipeline]
5. Glue ETL Jobs:
   a. Raw to Processed Job:
      - Input: Raw data from S3
      - Transformations:
        • Data cleansing (remove nulls)
        • Format conversion (CSV → Parquet)
        • Data validation
      - Output: Processed bucket
      - Schedule: Hourly
      
   b. Aggregation Job:
      - Input: Processed data
      - Transformations:
        • Group by date/category
        • Calculate metrics
        • Join multiple sources
      - Output: Analytics bucket
      - Schedule: Daily

6. Glue Job Configuration:
   - DPU: 2-10 (based on data volume)
   - Max retries: 3
   - Timeout: 2880 minutes
   - Job bookmarks: Enabled (track processed data)

[Step 4: Query Service]
7. Athena Configuration:
   - Workgroup: "datalake_workgroup"
   - Query result location: s3://athena-results/
   - Encryption: SSE-S3
   - Data scanned per query limit: 1 TB
   
8. Create Views:
   - Frequently used queries
   - Pre-joined tables
   - Aggregated metrics

[Step 5: Governance]
9. Lake Formation Setup:
   - Register S3 locations
   - Define data lake administrators
   - Column-level access control:
     a. Data Scientists: Full access
     b. Analysts: Read-only, no PII columns
     c. Applications: Specific tables only
   
10. Tag-based access control:
    - Tags: Confidential, Internal, Public
    - Policies based on tags

[Step 6: Access Patterns]
11. For Data Scientists:
    - SageMaker notebook instances
    - IAM role with Lake Formation permissions
    - Direct S3 access for large datasets
    
12. For Business Analysts:
    - QuickSight connected to Athena
    - Pre-built dashboards
    - Scheduled reports
    
13. For Applications:
    - Athena JDBC/ODBC drivers
    - Query via API (Lambda + Athena)

[Step 7: Monitoring]
14. CloudWatch Dashboards:
    - Glue job success/failure rates
    - Athena query performance
    - S3 storage growth
    - Cost tracking
    
15. Alarms:
    - Glue job failures
    - High Athena costs
    - Unusual S3 access patterns

Architecture Diagram:
Data Sources → S3 (Raw) → Glue ETL → S3 (Processed) → Athena/QuickSight
                                                      ↓
                                            Lake Formation (Governance)

Cost Estimate:
- S3: $0.023/GB (first 50 TB)
- Glue: $0.44/DPU-hour
- Athena: $5/TB scanned
- Total: ~$100-500/month for small data lake

Best Practices:
✅ Use Parquet/ORC for 10x cost savings
✅ Partition data by date for faster queries
✅ Compress data (Snappy/GZIP)
✅ Use Glue bookmarks to avoid reprocessing
✅ Set Athena query limits
```

#### **Enterprise Data Lake (Large Scale)**
```
Additional Services:
├─ AWS Glue DataBrew (Data preparation)
├─ Redshift Spectrum (Complex queries)
├─ EMR (Big data processing)
├─ DataSync (On-premises ingestion)
├─ Step Functions (Orchestration)
└─ KMS (Encryption)

Enhanced Configuration:

[Advanced ETL]
1. EMR Cluster for complex transformations:
   - Spark/Hadoop jobs
   - Auto-scaling: 3-20 nodes
   - Spot instances for cost savings
   
2. Glue DataBrew:
   - Visual data preparation
   - 250+ built-in transformations
   - Profile data quality

[Advanced Analytics]
3. Redshift Spectrum:
   - Query S3 data directly
   - Join with Redshift tables
   - Complex aggregations
   
4. Redshift Cluster:
   - For frequently accessed data
   - Materialized views
   - Concurrency scaling

[Data Ingestion]
5. Multiple ingestion methods:
   - Batch: AWS DataSync, S3 Transfer
   - Streaming: Kinesis Firehose
   - Database: DMS
   - On-premises: Storage Gateway

[Orchestration]
6. Step Functions State Machine:
   - Coordinate multiple Glue jobs
   - Error handling and retries
   - Parallel processing
   - SNS notifications on completion

[Security]
7. Encryption:
   - S3: SSE-KMS with customer managed key
   - Glue: Encryption at rest
   - Athena: Query results encrypted
   
8. VPC Endpoints:
   - Private connectivity to S3/Glue
   - No internet gateway needed

Cost: $1,000-10,000+/month
```

---

## 🔥 SCENARIO 2: Real-Time Analytics Pipeline

### Initial Questions:
```
Q1: What is the data velocity?
    ├─ < 1000 records/sec → Kinesis Data Streams
    ├─ 1000-100K records/sec → Kinesis + Shards
    └─ > 100K records/sec → Multiple streams + MSK (Kafka)

Q2: What processing is needed?
    ├─ Simple transformations → Kinesis Data Firehose
    ├─ Complex processing → Kinesis Analytics or Lambda
    └─ Custom logic → Flink on EMR or ECS

Q3: What are the destinations?
    ├─ Storage → S3, Redshift
    ├─ Analytics → OpenSearch, Redshift
    └─ Applications → Lambda, DynamoDB
```

### Configuration Matrix:

#### **Real-Time Analytics Pipeline (Complete)**
```
Services Required:
├─ Kinesis Data Streams (Ingestion)
├─ Kinesis Data Analytics (Processing)
├─ Kinesis Data Firehose (Delivery)
├─ Lambda (Custom processing)
├─ DynamoDB (Real-time state)
├─ OpenSearch (Search & visualization)
├─ S3 (Archive)
├─ CloudWatch (Monitoring)
└─ SNS (Alerting)

Configuration Steps:

[Step 1: Data Ingestion]
1. Kinesis Data Stream:
   - Stream name: "clickstream-events"
   - Shards: Start with 5
   - Retention: 24 hours (extend to 7 days if needed)
   - Encryption: KMS
   - Enhanced metrics: Enabled
   
2. Data Producers:
   a. Web Application:
      - Kinesis Producer Library (KPL)
      - Batching: 500 records or 500KB
      - Aggregation: Enabled
      
   b. Mobile Apps:
      - AWS Mobile SDK
      - Kinesis Agent on servers
      
   c. Log Files:
      - Kinesis Agent for log files
      - Auto-rotation and compression

[Step 2: Real-Time Processing]
3. Kinesis Data Analytics Application:
   - Input: Kinesis Data Stream
   - SQL queries for transformations:
     
     Example 1: Anomaly Detection
     ```sql
     CREATE OR REPLACE STREAM "TEMP_STREAM" (
       user_id VARCHAR(50),
       event_type VARCHAR(50),
       event_count INTEGER,
       event_time TIMESTAMP
     );
     
     CREATE OR REPLACE PUMP "STREAM_PUMP" AS 
     INSERT INTO "TEMP_STREAM"
     SELECT STREAM 
       user_id,
       event_type,
       COUNT(*) OVER (
         PARTITION BY user_id
         RANGE INTERVAL '5' MINUTE PRECEDING
       ) as event_count,
       ROWTIME as event_time
     FROM "SOURCE_SQL_STREAM_001"
     WHERE event_count > 100;
     ```
     
     Example 2: Aggregations
     ```sql
     CREATE OR REPLACE STREAM "AGGREGATED_STREAM" (
       category VARCHAR(50),
       total_sales DOUBLE,
       window_time TIMESTAMP
     );
     
     CREATE OR REPLACE PUMP "AGG_PUMP" AS
     INSERT INTO "AGGREGATED_STREAM"
     SELECT STREAM
       category,
       SUM(amount) as total_sales,
       ROWTIME as window_time
     FROM "SOURCE_SQL_STREAM_001"
     GROUP BY category,
       STEP("SOURCE_SQL_STREAM_001".ROWTIME BY INTERVAL '1' MINUTE);
     ```
   
   - Output: Another Kinesis stream or Lambda
   
4. Lambda Functions (Alternative Processing):
   - Triggered by Kinesis stream
   - Batch size: 100 records
   - Batch window: 5 seconds
   - Concurrent executions: 10
   - Processing logic:
     • Enrich data (lookup DynamoDB)
     • Filter invalid records
     • Transform formats
   - Error handling:
     • Failed records → DLQ (SQS)
     • Retry: 3 times

[Step 3: Data Storage]
5. Kinesis Firehose Delivery Stream:
   
   a. S3 Delivery:
      - Buffer size: 5 MB
      - Buffer interval: 60 seconds
      - Compression: GZIP
      - Prefix: year=!{timestamp:yyyy}/month=!{timestamp:MM}/day=!{timestamp:dd}/
      - Error output prefix: errors/
      - Lambda transformation: Optional (data format)
      
   b. Redshift Delivery:
      - Intermediate S3 bucket
      - COPY command after buffer fills
      - Retry duration: 3600 seconds
      
   c. OpenSearch Delivery:
      - Index rotation: Daily
      - Buffer size: 5 MB
      - Buffer interval: 60 seconds
      - Index: clickstream-YYYY-MM-DD

6. DynamoDB for Real-Time State:
   - Table: "real_time_metrics"
   - Partition Key: metric_id
   - Sort Key: timestamp
   - TTL: Enabled (expire after 7 days)
   - On-Demand capacity or Auto Scaling
   - Global Secondary Index: user_id
   - DynamoDB Streams: Enabled for change capture

[Step 4: Visualization & Alerting]
7. OpenSearch Dashboards:
   - Real-time visualizations
   - Metrics: Events per second, error rates
   - Geographic maps
   - Time-series graphs
   
8. CloudWatch Alarms:
   - High incoming records (> threshold)
   - Iterator age (lag in processing)
   - GetRecords.IteratorAgeMilliseconds > 60000
   - Lambda errors
   
9. SNS Topics:
   - Critical alerts → PagerDuty
   - Warning alerts → Email
   - Info alerts → Slack

[Step 5: Advanced Features]
10. Kinesis Scaling:
    - Application Auto Scaling:
      • Target metric: IncomingRecords
      • Target value: 1000 per shard
      • Scale out: Add shards
      • Scale in: Merge shards
    
11. Data Replay:
    - Kinesis retention allows replay
    - Reset iterator to earlier position
    - Reprocess historical data

[Step 6: Monitoring]
12. CloudWatch Dashboard:
    - Incoming records per stream
    - Processing latency
    - Firehose delivery success rate
    - Lambda duration and errors
    - DynamoDB consumed capacity
    
13. X-Ray Tracing:
    - End-to-end request tracing
    - Identify bottlenecks
    - Lambda execution analysis

Architecture Flow:
Data Sources → Kinesis Streams → [Processing Layer] → Storage/Analytics
                                 ↓
                     Kinesis Analytics + Lambda
                                 ↓
                  Firehose → S3 / Redshift / OpenSearch
                                 ↓
                  DynamoDB (real-time queries)
                                 ↓
                  CloudWatch Alarms → SNS

Performance:
- Latency: < 1 second (ingestion to processing)
- Throughput: 1 MB/sec per shard (can scale to GB/sec)
- Durability: 99.9% (data replicated across 3 AZs)

Cost Estimate:
- Kinesis Streams: $0.015/shard-hour + $0.014/million PUT records
- Analytics: $0.11/KPU-hour
- Firehose: $0.029/GB
- Total: $200-2,000/month (depends on volume)

Use Cases:
✅ Clickstream analytics
✅ IoT sensor data
✅ Log monitoring
✅ Gaming leaderboards
✅ Financial transactions
```

#### **Simplified Real-Time Pipeline (Cost-Optimized)**
```
Services Required:
├─ Kinesis Data Firehose (Direct ingestion + delivery)
├─ Lambda (Simple transformations)
├─ S3 (Storage)
├─ Athena (Ad-hoc queries)
└─ CloudWatch (Monitoring)

Configuration:
1. Kinesis Firehose only (no Data Streams):
   - Direct PUT from applications
   - Lambda transformation inline
   - Deliver to S3 in Parquet format
   
2. Query with Athena:
   - Near real-time (1-minute delay)
   - On-demand querying
   
Cost: 70% less than full pipeline
Trade-off: Slightly higher latency, less flexibility
```

---

## ⚙️ SCENARIO 3: Batch Processing Workflows

### Configuration Matrix:

#### **Nightly Batch Processing (ETL)**
```
Services Required:
├─ AWS Batch (Job orchestration)
├─ S3 (Input/output storage)
├─ ECR (Container images)
├─ Lambda (Trigger)
├─ EventBridge (Scheduling)
├─ SNS (Notifications)
└─ CloudWatch (Monitoring)

Configuration Steps:

[Step 1: Compute Environment]
1. AWS Batch Compute Environment:
   - Type: Managed
   - Service Role: AWSBatchServiceRole
   - Instance types: m5.large, m5.xlarge, c5.large
   - Min vCPUs: 0 (cost-effective)
   - Max vCPUs: 256
   - Desired vCPUs: 0
   - Allocation strategy: BEST_FIT_PROGRESSIVE
   - Spot instances: 80% of capacity (cost savings)
   - VPC: Private subnets with NAT Gateway

[Step 2: Job Queue]
2. Create Job Queue:
   - Priority: 1
   - State: ENABLED
   - Compute environments: Link to above
   
3. Multiple queues (optional):
   - High Priority Queue: Reserved instances
   - Low Priority Queue: Spot instances

[Step 3: Job Definitions]
4. Job Definition for Data Processing:
   - Job name: "data-processing-job"
   - Container image: ECR URI
   - vCPUs: 4
   - Memory: 8192 MB
   - Job role: Access to S3, DynamoDB
   - Environment variables:
     • INPUT_BUCKET=my-input-bucket
     • OUTPUT_BUCKET=my-output-bucket
     • BATCH_DATE=$(date)
   - Retry strategy: 3 attempts
   - Timeout: 3600 seconds

[Step 4: Container Setup]
5. Create Docker container:
   ```dockerfile
   FROM python:3.9
   WORKDIR /app
   COPY requirements.txt .
   RUN pip install -r requirements.txt
   COPY process_data.py .
   CMD ["python", "process_data.py"]
   ```
   
6. Push to ECR:
   - Repository: data-processing
   - Image scanning: Enabled
   - Encryption: AES-256

[Step 5: Orchestration]
7. EventBridge Rule:
   - Schedule: cron(0 2 * * ? *) # 2 AM daily
   - Target: Lambda function
   
8. Lambda Orchestrator:
   - Triggered by EventBridge
   - Submits multiple Batch jobs
   - Manages dependencies between jobs
   - Code example:
     ```python
     import boto3
     
     batch = boto3.client('batch')
     
     def lambda_handler(event, context):
         # Job 1: Extract
         extract_job = batch.submit_job(
             jobName='extract-job',
             jobQueue='my-job-queue',
             jobDefinition='extract-job-def'
         )
         
         # Job 2: Transform (depends on Job 1)
         transform_job = batch.submit_job(
             jobName='transform-job',
             jobQueue='my-job-queue',
             jobDefinition='transform-job-def',
             dependsOn=[{'jobId': extract_job['jobId']}]
         )
         
         # Job 3: Load (depends on Job 2)
         load_job = batch.submit_job(
             jobName='load-job',
             jobQueue='my-job-queue',
             jobDefinition='load-job-def',
             dependsOn=[{'jobId': transform_job['jobId']}]
         )
         
         return {'statusCode': 200}
     ```

[Step 6: Error Handling]
9. Failed Job Handler Lambda:
   - Triggered by CloudWatch Events (job FAILED status)
   - Send notification via SNS
   - Log details to CloudWatch
   - Optionally retry with different parameters

[Step 7: Monitoring]
10. CloudWatch Dashboards:
    - Jobs submitted vs completed
    - Average job duration
    - Compute environment utilization
    - Cost per job
    
11. Alarms:
    - Job failures
    - Long-running jobs (> 2 hours)
    - Queue backlog

Cost: $50-500/month (mostly compute, pay only when running)
```

#### **Big Data Batch Processing (EMR)**
```
Services Required:
├─ EMR (Hadoop/Spark)
├─ S3 (Data lake)
├─ Glue Data Catalog
├─ Step Functions (Orchestration)
└─ CloudWatch

Configuration:

1. EMR Cluster (Transient):
   - Release: emr-6.9.0
   - Applications: Spark, Hive, Presto
   - Master: m5.xlarge (1 instance)
   - Core: m5.xlarge (2-10 instances, auto-scaling)
   - Task: m5.xlarge (0-20 instances, Spot)
   - Auto-termination: After idle for 1 hour
   
2. Spark Job Steps:
   - Step 1: Data validation
   - Step 2: Transformations
   - Step 3: Aggregations
   - Step 4: Write to S3 (Parquet)
   
3. Step Functions Workflow:
   - Create EMR cluster
   - Wait for cluster ready
   - Submit Spark jobs
   - Wait for completion
   - Terminate cluster
   - Update Glue catalog

Cost: $100-1,000/month (depends on data volume)
```

---

## 📊 SCENARIO 4: Business Intelligence Solutions

### Configuration Matrix:

#### **Self-Service BI Platform**
```
Services Required:
├─ QuickSight (BI tool)
├─ Athena (Data source)
├─ S3 (Data lake)
├─ RDS/Redshift (Optional: data warehouse)
└─ Glue (Data preparation)

Configuration Steps:

[Step 1: Data Preparation]
1. Consolidate data sources:
   - S3 data lake
   - RDS databases
   - SaaS applications (via AppFlow)
   
2. Glue DataBrew recipes:
   - Clean data
   - Join multiple sources
   - Create aggregated datasets
   - Output to S3 (Parquet)

[Step 2: QuickSight Setup]
3. QuickSight Account:
   - Edition: Enterprise (for ML insights)
   - Authentication: IAM or SSO
   - VPC connection: For private RDS
   
4. Data Sources:
   - Athena (for S3 data lake)
   - Direct connection to RDS
   - Direct connection to Redshift
   - SPICE import for performance

[Step 3: SPICE Optimization]
5. Import to SPICE:
   - For datasets < 1 GB
   - Refresh schedule: Daily at 6 AM
   - Incremental refresh: For large datasets
   
Benefits:
- 10x faster than direct query
- Reduced costs on data sources

[Step 4: Dataset Creation]
6. Create QuickSight datasets:
   - Sales dataset (Athena)
   - Customer dataset (RDS)
   - Product dataset (S3 via Athena)
   - Join datasets in QuickSight

[Step 5: Dashboard Creation]
7. Build dashboards:
   - Executive Dashboard:
     • KPI tiles (revenue, users, growth)
     • Trend line charts
     • Geographic maps
     
   - Sales Dashboard:
     • Sales by region (pie chart)
     • Sales over time (line chart)
     • Top products (bar chart)
     • Sales funnel
     
   - Operations Dashboard:
     • Real-time metrics
     • Alert indicators
     • Drill-down capabilities

[Step 6: ML Insights]
8. Enable ML Insights:
   - Anomaly detection
   - Forecasting (up to 1 year)
   - Auto-narratives
   - Contribution analysis

[Step 7: Sharing & Embedding]
9. Dashboard sharing:
   - Share with users/groups
   - Email reports (daily/weekly)
   - Embed in web applications
   - Row-level security (RLS)

[Step 8: Governance]
10. Row-Level Security:
    - Filter data by user attributes
    - Example: Sales reps see only their region
    
11. Column-Level Security:
    - Hide sensitive columns (SSN, salary)

Cost:
- Author: $18/month per user
- Reader: $0.30/session ($5 max/month)
- SPICE: $0.38/GB/month
Total: $100-1,000/month (depends on users)

Use Cases:
✅ Executive dashboards
✅ Self-service analytics
✅ Embedded analytics in apps
✅ Operational reporting
```

---

## 🔄 SCENARIO 5: ETL Configurations

### Configuration Matrix:

#### **Complete ETL Pipeline**
```
Services Required:
├─ Glue (ETL jobs)
├─ Glue DataBrew (Visual ETL)
├─ Step Functions (Orchestration)
├─ S3 (Staging/final)
├─ DMS (Database sources)
├─ EventBridge (Scheduling)
└─ SNS (Notifications)

Configuration:

[Step 1: Data Extraction]
1. From Databases (DMS):
   - Source: On-premises Oracle/SQL Server
   - Target: S3 (Parquet)
   - Replication instance: dms.t3.medium
   - Task: Full load + CDC (ongoing replication)
   
2. From SaaS (AppFlow):
   - Salesforce → S3
   - Schedule: Daily
   - Incremental load

3. From APIs (Lambda):
   - Invoke REST APIs
   - Transform to JSON
   - Store in S3

[Step 2: Data Transformation]
4. Glue Studio Jobs:
   - Visual ETL designer
   - Sources: Multiple S3 paths, databases
   - Transformations:
     a. Change Schema
     b. Filter (remove test data)
     c. Join (combine datasets)
     d. Aggregate (group by)
     e. ApplyMapping (rename columns)
     f. DropNullFields
   - Target: S3 (Parquet, partitioned)
   
5. Glue PySpark Script (Advanced):
   ```python
   import sys
   from awsglue.transforms import *
   from awsglue.utils import getResolvedOptions
   from pyspark.context import SparkContext
   from awsglue.context import GlueContext
   from awsglue.job import Job
   
   args = getResolvedOptions(sys.argv, ['JOB_NAME'])
   sc = SparkContext()
   glueContext = GlueContext(sc)
   spark = glueContext.spark_session
   job = Job(glueContext)
   job.init(args['JOB_NAME'], args)
   
   # Read from catalog
   datasource = glueContext.create_dynamic_frame.from_catalog(
       database = "my_database",
       table_name = "my_table",
       transformation_ctx = "datasource"
   )
   
   # Apply transformations
   mapped = ApplyMapping.apply(
       frame = datasource,
       mappings = [
           ("old_name", "string", "new_name", "string"),
           ("price", "double", "price", "decimal(10,2)")
       ]
   )
   
   # Write to S3
   glueContext.write_dynamic_frame.from_options(
       frame = mapped,
       connection_type = "s3",
       connection_options = {
           "path": "s3://my-bucket/output/",
           "partitionKeys": ["year", "month", "day"]
       },
       format = "parquet"
   )
   
   job.commit()
   ```

[Step 3: Orchestration]
6. Step Functions State Machine:
   ```json
   {
     "StartAt": "Extract",
     "States": {
       "Extract": {
         "Type": "Parallel",
         "Branches": [
           {"StartAt": "ExtractDB", "States": {...}},
           {"StartAt": "ExtractAPI", "States": {...}}
         ],
         "Next": "Transform"
       },
       "Transform": {
         "Type": "Task",
         "Resource": "arn:aws:states:::glue:startJobRun.sync",
         "Parameters": {
           "JobName": "transform-job"
         },
         "Next": "Load"
       },
       "Load": {
         "Type": "Task",
         "Resource": "arn:aws:states:::glue:startJobRun.sync",
         "Parameters": {
           "JobName": "load-job"
         },
         "End": true
       }
     }
   }
   ```

Cost: $100-1,000/month
```

---

## 📝 SCENARIO 6: Log Analytics

### Configuration Matrix:

#### **Centralized Log Analytics**
```
Services Required:
├─ CloudWatch Logs (Collection)
├─ Kinesis Firehose (Aggregation)
├─ S3 (Archive)
├─ Athena (Ad-hoc queries)
├─ OpenSearch (Search & visualization)
└─ Lambda (Processing)

Configuration:

1. Log Collection:
   - CloudWatch Agent on EC2
   - Container logs (ECS/EKS)
   - Lambda logs (automatic)
   - Application logs

2. Processing:
   - CloudWatch subscription filter → Kinesis Firehose
   - Lambda transformation (parse, enrich)
   - Firehose → S3 + OpenSearch

3. Analysis:
   - OpenSearch for real-time search
   - Athena for historical analysis
   - CloudWatch Insights for quick queries

Cost: $50-500/month
```

---

## 📊 Quick Comparison Table

| **Scenario** | **Best For** | **Complexity** | **Cost/Month** |
|--------------|--------------|----------------|----------------|
| Basic Data Lake | Small-medium data | Low | $100-500 |
| Enterprise Data Lake | Large-scale data | High | $1K-10K |
| Real-Time Pipeline | Streaming data | Medium | $200-2K |
| Batch Processing | Scheduled ETL | Low | $50-500 |
| BI Platform | Business reporting | Medium | $100-1K |
| ETL Configuration | Data integration | Medium | $100-1K |
| Log Analytics | Operational insights | Low | $50-500 |

---

**Next**: See `23-Config-Database-Solutions.md` for database configurations
