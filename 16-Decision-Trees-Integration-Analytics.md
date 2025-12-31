# AWS SAA-C03: Integration, Messaging & Analytics Decision Trees

## 🌳 Quick Navigation
- [Messaging & Integration](#messaging--integration)
- [Event-Driven Architecture](#event-driven-architecture)
- [Streaming Data](#streaming-data)
- [Analytics & Big Data](#analytics--big-data)
- [ETL & Data Processing](#etl--data-processing)

---

## Messaging & Integration

```
START: Need to integrate applications?
│
├─→ Message queue (producer-consumer)?
│   └─→ **Amazon SQS** (Simple Queue Service)
│       ├─→ Types:
│       │   ├─→ Standard Queue:
│       │   │   ├─→ Unlimited throughput
│       │   │   ├─→ At-least-once delivery (may have duplicates)
│       │   │   ├─→ Best-effort ordering
│       │   │   └─→ Use case: Decouple components, scale independently
│       │   │
│       │   └─→ FIFO Queue:
│       │       ├─→ Exactly-once processing
│       │       ├─→ Strict ordering (First In, First Out)
│       │       ├─→ 300 TPS (3000 with batching)
│       │       └─→ Use case: Order-critical operations, prevent duplicates
│       │
│       ├─→ Features:
│       │   ├─→ Message retention: 1 min to 14 days (default 4 days)
│       │   ├─→ Message size: Up to 256 KB
│       │   ├─→ Visibility timeout (in-flight message invisible)
│       │   ├─→ Long polling (reduce API calls, save cost)
│       │   ├─→ Dead Letter Queue (DLQ) for failed messages
│       │   └─→ Delay queues (postpone delivery)
│       │
│       ├─→ Integration:
│       │   ├─→ Producers: Any application, SDK
│       │   ├─→ Consumers: EC2, Lambda, ECS
│       │   └─→ Auto Scaling based on queue depth
│       │
│       └─→ Use case: Decouple microservices, buffer requests, async processing
│
├─→ Publish-subscribe (one-to-many)?
│   └─→ **Amazon SNS** (Simple Notification Service)
│       ├─→ Pub/sub messaging pattern
│       ├─→ Topics (publish once, multiple subscribers)
│       ├─→ Subscribers:
│       │   ├─→ SQS, Lambda, HTTP/HTTPS endpoints
│       │   ├─→ Email, SMS, mobile push
│       │   ├─→ Kinesis Data Firehose
│       │   └─→ Up to 12.5M subscriptions per topic
│       ├─→ Features:
│       │   ├─→ Message filtering (subscribers get subset)
│       │   ├─→ Message fanout (deliver to multiple destinations)
│       │   ├─→ DLQ for failed deliveries
│       │   └─→ FIFO topics (ordering + deduplication)
│       └─→ Use case: Fanout to multiple subscribers, notifications, alerts
│
├─→ Managed message broker (JMS, AMQP)?
│   └─→ **Amazon MQ**
│       ├─→ Managed Apache ActiveMQ or RabbitMQ
│       ├─→ Industry-standard protocols: JMS, AMQP, STOMP, MQTT, WebSocket
│       ├─→ High availability (active/standby)
│       ├─→ Use case: Migrate existing apps using message brokers
│       └─→ NOTE: For new apps, prefer SQS/SNS
│
├─→ GraphQL API (real-time, offline)?
│   └─→ **AWS AppSync**
│       ├─→ Managed GraphQL service
│       ├─→ Real-time subscriptions
│       ├─→ Offline sync
│       ├─→ Data sources: DynamoDB, Lambda, RDS, HTTP
│       └─→ Use case: Mobile/web apps, real-time collaboration
│
└─→ Workflow orchestration (state machine)?
    └─→ **AWS Step Functions**
        ├─→ Visual workflow orchestration
        ├─→ State machine (serverless workflow)
        ├─→ Types:
        │   ├─→ Standard: Long-running (up to 1 year), exactly-once
        │   └─→ Express: High-volume, short duration (< 5 min), at-least-once
        ├─→ Integrations: Lambda, ECS, SNS, SQS, DynamoDB, etc.
        ├─→ Error handling, retries, parallel execution
        └─→ Use case: Order processing, ETL, microservice orchestration
```

### SQS Standard vs FIFO

| Feature | Standard Queue | FIFO Queue |
|---------|---------------|------------|
| **Throughput** | Unlimited | 300 TPS (3000 with batching) |
| **Ordering** | Best-effort | Strict FIFO |
| **Delivery** | At-least-once (duplicates possible) | Exactly-once |
| **Use Case** | High throughput, duplicates OK | Order-critical, no duplicates |

### SQS vs SNS vs Kinesis

| Feature | SQS | SNS | Kinesis |
|---------|-----|-----|---------|
| **Pattern** | Queue (point-to-point) | Pub/sub (fanout) | Streaming |
| **Consumers** | Pull-based, multiple | Push-based, multiple | Pull-based, multiple |
| **Retention** | Up to 14 days | No retention | 1-365 days |
| **Ordering** | FIFO queue only | FIFO topic only | Per shard |
| **Use Case** | Decouple, buffer | Fanout, notifications | Real-time streaming |

---

## Event-Driven Architecture

```
START: Event-driven application?
│
├─→ Event bus (route events)?
│   └─→ **Amazon EventBridge**
│       ├─→ Serverless event bus
│       ├─→ Sources:
│       │   ├─→ AWS services (100+ services)
│       │   ├─→ Custom applications (via API)
│       │   ├─→ SaaS partners (Zendesk, Shopify, etc.)
│       │   └─→ Scheduled events (cron)
│       ├─→ Event patterns (filter and route)
│       ├─→ Targets:
│       │   ├─→ Lambda, Step Functions, SQS, SNS
│       │   ├─→ ECS tasks, Batch, EC2 instances
│       │   ├─→ 20+ AWS services
│       │   └─→ Cross-account, cross-region
│       ├─→ Features:
│       │   ├─→ Schema registry (discover event structure)
│       │   ├─→ Archive and replay
│       │   ├─→ Dead letter queue
│       │   └─→ Input transformation
│       └─→ Use case: Event-driven microservices, workflow automation
│
├─→ Simple workflow (SWF - Legacy)?
│   └─→ **Amazon SWF** (Simple Workflow Service)
│       ├─→ Legacy service (use Step Functions instead)
│       ├─→ Task coordination, human intervention
│       └─→ Use case: Existing SWF applications (migrate to Step Functions)
│
└─→ Combine SQS + SNS for fanout?
    └─→ **SNS + SQS Fanout Pattern**
        ├─→ SNS topic → Multiple SQS queues
        ├─→ Each queue has independent consumer
        └─→ Use case: Process same message multiple ways
```

### EventBridge vs CloudWatch Events

- **EventBridge** is the evolution of CloudWatch Events
- EventBridge = CloudWatch Events + more features
- EventBridge supports:
  - SaaS integration
  - Custom event buses
  - Schema registry
  - Cross-account events

---

## Streaming Data

```
START: Real-time streaming data?
│
├─→ Data streams (real-time processing)?
│   └─→ **Amazon Kinesis Data Streams**
│       ├─→ Real-time data streaming
│       ├─→ Shards (provisioned throughput):
│       │   ├─→ Write: 1 MB/sec or 1000 records/sec per shard
│       │   ├─→ Read: 2 MB/sec per shard
│       │   └─→ Scale by adding/removing shards
│       ├─→ Modes:
│       │   ├─→ Provisioned: Specify shard count
│       │   └─→ On-Demand: Auto-scaling (simpler, more expensive)
│       ├─→ Retention: 1-365 days
│       ├─→ Ordering: Per shard (partition key)
│       ├─→ Consumers:
│       │   ├─→ Lambda, KCL (Kinesis Client Library), Kinesis Data Analytics
│       │   └─→ Enhanced Fan-Out: 2 MB/sec per consumer per shard
│       └─→ Use case: Real-time analytics, log processing, IoT, clickstreams
│
├─→ Load data into data stores (no processing)?
│   └─→ **Amazon Kinesis Data Firehose**
│       ├─→ Fully managed, serverless
│       ├─→ Near real-time (60 sec minimum latency)
│       ├─→ Destinations:
│       │   ├─→ S3, Redshift (via S3), OpenSearch
│       │   ├─→ 3rd-party: Splunk, Datadog, New Relic, MongoDB
│       │   └─→ Custom HTTP endpoints
│       ├─→ Features:
│       │   ├─→ Automatic scaling
│       │   ├─→ Data transformation (Lambda)
│       │   ├─→ Data format conversion (JSON → Parquet/ORC)
│       │   ├─→ Compression, encryption
│       │   └─→ No data retention (just delivery)
│       └─→ Use case: Load streaming data to S3, Redshift, OpenSearch
│
├─→ Real-time SQL analytics on streams?
│   └─→ **Amazon Kinesis Data Analytics**
│       ├─→ Real-time analytics on streaming data
│       ├─→ Languages:
│       │   ├─→ SQL (for SQL applications)
│       │   └─→ Apache Flink (for advanced processing)
│       ├─→ Sources: Kinesis Data Streams, Kinesis Data Firehose
│       ├─→ Destinations: Kinesis Data Streams, Kinesis Data Firehose, Lambda
│       └─→ Use case: Real-time dashboards, anomaly detection, streaming ETL
│
└─→ Video streaming?
    └─→ **Amazon Kinesis Video Streams**
        ├─→ Ingest video from devices
        ├─→ Playback, storage, processing
        └─→ Use case: Security cameras, video analytics, ML
```

### Kinesis Data Streams vs Firehose

| Feature | Data Streams | Data Firehose |
|---------|-------------|---------------|
| **Processing** | Custom (write code) | Managed delivery |
| **Latency** | Real-time (~200 ms) | Near real-time (60+ sec) |
| **Retention** | 1-365 days | No retention (just delivery) |
| **Scaling** | Manual (shards) | Automatic |
| **Destinations** | Custom consumers | S3, Redshift, OpenSearch, HTTP |
| **Cost** | Per shard-hour | Per GB ingested |
| **Use Case** | Custom real-time processing | Load to data stores |

---

## Analytics & Big Data

```
START: Analytics on large datasets?
│
├─→ Query S3 data with SQL (serverless)?
│   └─→ **Amazon Athena**
│       ├─→ Serverless, interactive SQL queries
│       ├─→ Query S3 data directly (no loading)
│       ├─→ Pay per query (per TB scanned)
│       ├─→ Formats: CSV, JSON, Parquet, ORC, Avro
│       ├─→ Integrates with AWS Glue (data catalog)
│       ├─→ Output: S3, QuickSight
│       ├─→ Performance optimization:
│       │   ├─→ Use columnar formats (Parquet, ORC)
│       │   ├─→ Partition data
│       │   └─→ Compress data
│       └─→ Use case: Ad-hoc queries, log analysis, S3 data exploration
│
├─→ Data warehouse (OLAP, BI)?
│   └─→ **Amazon Redshift**
│       ├─→ Petabyte-scale data warehouse
│       ├─→ Columnar storage, MPP (massively parallel processing)
│       ├─→ 10x faster than traditional data warehouses
│       ├─→ Node types:
│       │   ├─→ RA3: Managed storage (compute/storage independent)
│       │   ├─→ DC2: Compute-intensive (local SSD)
│       │   └─→ DS2: Storage-intensive (legacy)
│       ├─→ Redshift Spectrum: Query S3 directly (extend Redshift)
│       ├─→ Redshift Serverless: Auto-scaling, pay per use
│       ├─→ Features:
│       │   ├─→ Materialized views
│       │   ├─→ Result caching
│       │   ├─→ Concurrency scaling
│       │   └─→ Automatic snapshots
│       └─→ Use case: Business intelligence, OLAP, data warehousing
│
├─→ Big data processing (Hadoop, Spark)?
│   └─→ **Amazon EMR** (Elastic MapReduce)
│       ├─→ Managed Hadoop, Spark, HBase, Presto, Flink
│       ├─→ Cluster types:
│       │   ├─→ Transient: Terminate after job
│       │   └─→ Long-running: Persistent cluster
│       ├─→ Storage:
│       │   ├─→ HDFS (ephemeral, on-cluster)
│       │   ├─→ EMRFS (persistent, on S3)
│       │   └─→ EBS for local caching
│       ├─→ Cost optimization: Spot instances for task nodes
│       └─→ Use case: Big data processing, ML, ETL at scale
│
├─→ Search and analytics (logs, metrics)?
│   └─→ **Amazon OpenSearch Service** (formerly Elasticsearch)
│       ├─→ Managed OpenSearch / Elasticsearch
│       ├─→ Full-text search, log analytics, visualizations
│       ├─→ Kibana / OpenSearch Dashboards
│       ├─→ Integrations: Kinesis Data Firehose, CloudWatch Logs, S3
│       └─→ Use case: Log analytics, full-text search, application monitoring
│
├─→ Business intelligence dashboards?
│   └─→ **Amazon QuickSight**
│       ├─→ Serverless BI service
│       ├─→ Interactive dashboards, visualizations
│       ├─→ SPICE engine (in-memory calculation)
│       ├─→ ML-powered insights:
│       │   ├─→ Anomaly detection
│       │   ├─→ Forecasting
│       │   └─→ Auto-narratives
│       ├─→ Data sources: RDS, Aurora, Redshift, Athena, S3, SaaS apps
│       ├─→ Pay-per-session pricing
│       └─→ Use case: Business intelligence, data visualization
│
└─→ Real-time analytics dashboards?
    └─→ **Kinesis Data Analytics** or **OpenSearch**
        └─→ Kinesis: SQL on streams → Real-time dashboards
        └─→ OpenSearch: Index streaming data → Visualize with Dashboards
```

### Analytics Service Selection

| Service | Use Case | Key Feature |
|---------|----------|-------------|
| **Athena** | Ad-hoc queries on S3 | Serverless SQL, pay per query |
| **Redshift** | Data warehouse, BI | Petabyte-scale, columnar, MPP |
| **EMR** | Big data processing | Hadoop, Spark, scalable |
| **OpenSearch** | Log analytics, search | Full-text search, Kibana |
| **QuickSight** | BI dashboards | ML insights, pay-per-session |
| **Kinesis Data Analytics** | Real-time streaming SQL | SQL on streams, Apache Flink |

---

## ETL & Data Processing

```
START: ETL (Extract, Transform, Load)?
│
├─→ Serverless ETL?
│   └─→ **AWS Glue**
│       ├─→ Serverless ETL service
│       ├─→ Components:
│       │   ├─→ Data Catalog:
│       │   │   ├─→ Metadata repository
│       │   │   ├─→ Central schema registry
│       │   │   └─→ Used by Athena, Redshift Spectrum, EMR
│       │   │
│       │   ├─→ Crawlers:
│       │   │   ├─→ Auto-discover schema
│       │   │   ├─→ Populate Data Catalog
│       │   │   └─→ Sources: S3, RDS, DynamoDB, JDBC
│       │   │
│       │   ├─→ ETL Jobs:
│       │   │   ├─→ Python or Scala (Apache Spark)
│       │   │   ├─→ Visual editor or code
│       │   │   ├─→ Job bookmarks (track processed data)
│       │   │   └─→ Triggers (scheduled, on-demand, event-based)
│       │   │
│       │   └─→ DataBrew:
│       │       ├─→ Visual data preparation
│       │       └─→ No coding required
│       │
│       ├─→ Glue Studio: Visual ETL job designer
│       └─→ Use case: Data lake preparation, ETL pipelines, data cataloging
│
├─→ Managed Apache Kafka?
│   └─→ **Amazon MSK** (Managed Streaming for Kafka)
│       ├─→ Fully managed Apache Kafka
│       ├─→ High availability, automatic recovery
│       ├─→ MSK Serverless: Auto-scaling
│       ├─→ Integrations: Kinesis Data Analytics, Lambda, Glue
│       └─→ Use case: Real-time data pipelines, streaming applications
│
├─→ Data pipeline orchestration?
│   └─→ **AWS Data Pipeline** or **Step Functions**
│       ├─→ AWS Data Pipeline:
│       │   ├─→ Orchestrate data movement and transformation
│       │   ├─→ Runs on EMR, EC2, or on-premises
│       │   └─→ Use case: Legacy data workflows (consider Glue instead)
│       │
│       └─→ Step Functions:
│           ├─→ General workflow orchestration
│           └─→ Use case: Modern ETL workflows, microservice orchestration
│
└─→ Real-time data sync (on-prem ↔ AWS)?
    └─→ **AWS DataSync**
        ├─→ Automated data transfer
        ├─→ Sources: NFS, SMB, HDFS, S3, EFS, FSx
        └─→ Use case: Migration, hybrid workflows, data replication
```

---

## Exam Scenarios - Quick Answers

### Scenario 1: Decouple web tier from processing tier
**Answer**: Amazon SQS

### Scenario 2: Send email notifications to multiple subscribers
**Answer**: Amazon SNS

### Scenario 3: Process 1 million records/sec, strict ordering
**Answer**: Kinesis Data Streams (multiple shards) + partition key

### Scenario 4: Load streaming data to S3 for analysis
**Answer**: Kinesis Data Firehose → S3

### Scenario 5: Event-driven architecture, route events to multiple targets
**Answer**: Amazon EventBridge

### Scenario 6: Orchestrate Lambda functions with error handling
**Answer**: AWS Step Functions

### Scenario 7: Query S3 logs with SQL, no infrastructure
**Answer**: Amazon Athena

### Scenario 8: Data warehouse for BI reports, petabyte-scale
**Answer**: Amazon Redshift

### Scenario 9: Big data processing with Hadoop/Spark
**Answer**: Amazon EMR

### Scenario 10: Serverless ETL, auto-discover schema
**Answer**: AWS Glue (Crawlers + ETL Jobs)

### Scenario 11: Real-time log analysis and search
**Answer**: Kinesis Data Firehose → OpenSearch

### Scenario 12: Fanout one message to multiple SQS queues
**Answer**: SNS → Multiple SQS subscriptions

### Scenario 13: Interactive BI dashboards with ML insights
**Answer**: Amazon QuickSight

### Scenario 14: Migrate existing ActiveMQ application
**Answer**: Amazon MQ

### Scenario 15: Real-time streaming analytics with SQL
**Answer**: Kinesis Data Analytics

---

## 🎯 Key Takeaways

### Messaging:
- **SQS**: Queue, decouple, buffer (Standard for throughput, FIFO for ordering)
- **SNS**: Pub/sub, fanout to multiple subscribers
- **MQ**: Managed message broker for migrations (use SQS/SNS for new apps)

### Event-Driven:
- **EventBridge**: Event bus, route events, integrate AWS services + SaaS
- **Step Functions**: Workflow orchestration, state machine

### Streaming:
- **Kinesis Data Streams**: Real-time streaming, custom processing, retention
- **Kinesis Data Firehose**: Managed delivery to S3, Redshift, OpenSearch
- **Kinesis Data Analytics**: Real-time SQL analytics on streams

### Analytics:
- **Athena**: Serverless SQL on S3, pay per query
- **Redshift**: Data warehouse, OLAP, petabyte-scale
- **EMR**: Big data processing, Hadoop, Spark
- **OpenSearch**: Log analytics, full-text search
- **QuickSight**: BI dashboards, ML insights

### ETL:
- **Glue**: Serverless ETL, data catalog, crawlers
- **DataBrew**: Visual data preparation, no coding
- **MSK**: Managed Kafka for real-time pipelines

### Common Patterns:
- **Decouple**: SQS between tiers
- **Fanout**: SNS → Multiple SQS queues
- **Stream Processing**: Kinesis Data Streams → Lambda/Analytics
- **Data Lake**: S3 + Glue (catalog) + Athena (query)
- **Real-time Analytics**: Kinesis → Kinesis Data Analytics → Dashboard
- **Event-Driven**: EventBridge → Lambda/Step Functions

---

## Integration Decision Matrix

| Requirement | Service |
|------------|---------|
| **Decouple components** | SQS |
| **One-to-many messaging** | SNS |
| **Order-critical queue** | SQS FIFO |
| **Real-time streaming** | Kinesis Data Streams |
| **Load to S3/Redshift** | Kinesis Data Firehose |
| **Event routing** | EventBridge |
| **Workflow orchestration** | Step Functions |
| **Query S3 with SQL** | Athena |
| **Data warehouse** | Redshift |
| **Big data processing** | EMR |
| **Serverless ETL** | Glue |
| **BI dashboards** | QuickSight |

---

*Use these decision trees for quick integration and analytics decisions during the exam!*
