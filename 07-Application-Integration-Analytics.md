# AWS SAA-C03: Application Integration & Analytics Cheat Sheet

## 📋 Table of Contents
- [SQS (Simple Queue Service)](#sqs)
- [SNS (Simple Notification Service)](#sns)
- [EventBridge](#eventbridge)
- [Step Functions](#step-functions)
- [SWF (Simple Workflow Service)](#swf)
- [AppSync](#appsync)
- [MQ](#mq)
- [Kinesis](#kinesis)
- [Athena](#athena)
- [EMR](#emr)
- [Glue](#glue)
- [QuickSight](#quicksight)
- [Data Pipeline](#data-pipeline)
- [MSK (Managed Streaming for Kafka)](#msk)

---

## SQS (Simple Queue Service)

### Definition
Fully managed message queuing service for decoupling application components.

### Key Features
- **Queue Types**:
  - **Standard**: Unlimited throughput, at-least-once delivery, best-effort ordering
  - **FIFO**: Up to 3,000 msg/sec, exactly-once processing, strict ordering
- **Message Size**: 256KB max (use S3 for larger)
- **Retention**: 1 minute to 14 days (default 4 days)
- **Visibility Timeout**: 0 seconds to 12 hours (default 30 seconds)
- **Delay Queues**: Postpone message delivery (0-15 minutes)
- **Dead Letter Queue (DLQ)**: Handle failed messages
- **Long Polling**: Reduce empty responses (1-20 seconds)
- **Short Polling**: Immediate response (may be empty)
- **Message Attributes**: Metadata (up to 10)
- **Batching**: Send/receive/delete up to 10 messages
- **Encryption**: At rest (KMS), in transit (HTTPS)
- **Access Policy**: Resource-based permissions

### Exam Tips & Heuristics
✅ **When to Use SQS:**
- Decouple application components
- Asynchronous processing
- Buffer between components
- Load leveling
- Batch processing

⚠️ **Common Pitfalls:**
- Standard queue doesn't guarantee order
- Messages can be delivered multiple times (Standard)
- FIFO throughput limited compared to Standard
- Visibility timeout must be set appropriately
- DLQ not automatic (must configure)

🎯 **Exam Heuristics:**
- "Decouple components" = **SQS**
- "Message queue" = **SQS**
- "Order matters" = **SQS FIFO**
- "High throughput" = **SQS Standard**
- "Exactly-once processing" = **SQS FIFO**
- "Failed message handling" = **Dead Letter Queue**
- "Reduce API calls" = **Long Polling**
- "Delay message delivery" = **Delay Queue**
- "Process in batches" = **SQS** with batch operations
- "Auto Scaling based on queue" = **CloudWatch** metrics + **Auto Scaling**

### Standard vs FIFO
| Feature | Standard | FIFO |
|---------|----------|------|
| Throughput | Unlimited | 3,000 msg/sec (10,000 with batching) |
| Ordering | Best-effort | Strict (per group ID) |
| Delivery | At-least-once | Exactly-once |
| Use Case | High throughput, order not critical | Order and deduplication required |

---

## SNS (Simple Notification Service)

### Definition
Fully managed pub/sub messaging service for application-to-application and application-to-person communication.

### Key Features
- **Topics**: Communication channels
- **Subscriptions**: Endpoints receiving messages
  - **Protocols**: HTTP/HTTPS, Email, Email-JSON, SMS, SQS, Lambda, Mobile Push, Kinesis Firehose
- **Message Size**: 256KB
- **Fan-out**: One message to many subscribers
- **Message Filtering**: Subscription-level filtering
- **Message Attributes**: Metadata
- **Delivery Retries**: Automatic with exponential backoff
- **DLQ**: For failed deliveries
- **Encryption**: At rest (KMS), in transit (HTTPS)
- **FIFO Topics**: Ordering and deduplication (with SQS FIFO)
- **Access Policy**: Resource-based permissions

### Exam Tips & Heuristics
✅ **When to Use SNS:**
- Pub/sub messaging
- Fan-out to multiple subscribers
- Send notifications (email, SMS)
- Event-driven architectures
- Real-time alerts

⚠️ **Common Pitfalls:**
- Cannot retrieve messages (push only, no pull)
- Email requires confirmation
- FIFO topics only work with SQS FIFO subscribers
- SMS costs extra and has restrictions

🎯 **Exam Heuristics:**
- "Pub/sub" = **SNS**
- "Fan-out pattern" = **SNS** + **SQS** (SNS to multiple SQS queues)
- "Send to multiple subscribers" = **SNS**
- "Email/SMS notifications" = **SNS**
- "Push to Lambda" = **SNS** or **EventBridge**
- "Filter messages per subscriber" = **SNS** message filtering
- "Order matters" = **SNS FIFO** + **SQS FIFO**
- "One message, many queues" = **SNS fan-out to SQS**

---

## EventBridge

### Definition
Serverless event bus for building event-driven applications (formerly CloudWatch Events).

### Key Features
- **Event Buses**: 
  - **Default**: AWS service events
  - **Custom**: Application events
  - **Partner**: SaaS integrations
- **Rules**: Match event patterns or schedules
- **Event Patterns**: Filter events (JSON)
- **Targets**: Lambda, SQS, SNS, Step Functions, Kinesis, ECS, etc. (20+ targets)
- **Scheduled Events**: Cron or rate expressions
- **Input Transformation**: Modify event before sending to target
- **Dead Letter Queue**: Failed event delivery
- **Archive & Replay**: Store and replay events
- **Schema Registry**: Discover and validate event schemas
- **Cross-Account**: Send events to other accounts
- **API Destinations**: HTTP endpoints as targets

### Exam Tips & Heuristics
✅ **When to Use EventBridge:**
- Event-driven architectures
- React to AWS service events
- Scheduled tasks
- Cross-account event routing
- SaaS integration
- Decouple microservices

⚠️ **Common Pitfalls:**
- Previously called CloudWatch Events (same service)
- Event pattern matching can be complex
- Targets have limits (5 per rule)
- Some events not available in all regions

🎯 **Exam Heuristics:**
- "React to AWS events" = **EventBridge**
- "Scheduled Lambda" = **EventBridge** (cron/rate)
- "Event-driven" = **EventBridge**
- "SaaS integration" = **EventBridge** partner event bus
- "Cross-account events" = **EventBridge** cross-account
- "Replay events" = **EventBridge** archive & replay
- "HTTP endpoint target" = **EventBridge** API destinations
- "Schedule tasks" = **EventBridge** (not CloudWatch Events, same service)

### SNS vs EventBridge
| Feature | SNS | EventBridge |
|---------|-----|-------------|
| Model | Pub/Sub | Event bus |
| Filtering | Subscription-level | Rule-based patterns |
| Targets | 6 protocols | 20+ AWS services |
| SaaS Integration | No | Yes (partner event bus) |
| Scheduling | No | Yes (cron/rate) |
| Use Case | Notifications, fan-out | Event-driven architectures |

---

## Step Functions

### Definition
Serverless orchestration service for coordinating distributed applications using visual workflows.

### Key Features
- **State Machines**: Workflow definitions (JSON)
- **States**:
  - **Task**: Do work (Lambda, ECS, SNS, etc.)
  - **Choice**: Branching logic
  - **Parallel**: Execute branches in parallel
  - **Wait**: Delay execution
  - **Pass**: Pass input to output
  - **Succeed/Fail**: End execution
  - **Map**: Iterate over array
- **Workflow Types**:
  - **Standard**: Up to 1 year, exactly-once execution, full history
  - **Express**: Up to 5 minutes, at-least-once, high-rate execution
- **Integration**: 200+ AWS services via SDK integrations
- **Error Handling**: Retry and catch logic
- **Input/Output Processing**: JSONPath
- **Execution History**: Detailed logs
- **Visual Workflow**: Drag-and-drop designer

### Exam Tips & Heuristics
✅ **When to Use Step Functions:**
- Orchestrate microservices
- Long-running workflows
- Complex business logic
- Error handling and retries
- Parallel processing
- Human approval workflows

⚠️ **Common Pitfalls:**
- Express workflows don't show execution history
- Standard workflows more expensive per execution
- 25,000 events per execution limit (Standard)
- State machine definitions can be complex

🎯 **Exam Heuristics:**
- "Orchestrate Lambda functions" = **Step Functions**
- "Workflow with retries" = **Step Functions**
- "Long-running process" = **Step Functions Standard**
- "High-rate event processing" = **Step Functions Express**
- "Human approval" = **Step Functions** (wait for callback)
- "Parallel execution" = **Step Functions** Parallel state
- "Complex logic" = **Step Functions** Choice state
- "Coordinate microservices" = **Step Functions**
- ">15 minutes Lambda" = **Step Functions** (chain multiple Lambdas)

---

## SWF (Simple Workflow Service)

### Definition
Legacy workflow orchestration service (Step Functions is recommended for new workloads).

### Key Features
- **Workflow**: Long-running processes (up to 1 year)
- **Deciders**: Control workflow logic
- **Activity Workers**: Perform tasks
- **Tasks**: Work units
- **Markers**: Record events
- **Child Workflows**: Nested workflows

### Exam Tips & Heuristics
✅ **When to Use SWF:**
- Legacy applications (existing SWF)
- External signals and manual tasks
- Specific SWF features not in Step Functions

⚠️ **Common Pitfalls:**
- Complex to implement
- Step Functions preferred for new workloads
- Requires infrastructure for deciders/workers

🎯 **Exam Heuristics:**
- "New workflow" = **Step Functions** (not SWF)
- "Legacy workflow" = **SWF** (if already using it)
- "Long-running process" = **Step Functions** (not SWF for new apps)
- Most exam questions will point to Step Functions, not SWF

---

## AppSync

### Definition
Managed GraphQL service for building data-driven applications with real-time and offline capabilities.

### Key Features
- **GraphQL API**: Single endpoint for queries, mutations, subscriptions
- **Data Sources**: DynamoDB, Lambda, HTTP, RDS, OpenSearch, EventBridge
- **Resolvers**: Map GraphQL to data sources
- **Real-time**: WebSocket subscriptions
- **Offline**: Local data sync for mobile
- **Authentication**: API Key, IAM, Cognito, OIDC, Lambda
- **Caching**: Reduce data source calls
- **Conflict Resolution**: For offline sync
- **Schema**: Type definitions

### Exam Tips & Heuristics
✅ **When to Use AppSync:**
- GraphQL APIs
- Real-time data synchronization
- Mobile apps with offline support
- Aggregate multiple data sources
- Pub/sub capabilities

⚠️ **Common Pitfalls:**
- Different from REST APIs (API Gateway)
- Requires GraphQL knowledge
- Caching not enabled by default

🎯 **Exam Heuristics:**
- "GraphQL" = **AppSync**
- "Real-time mobile app" = **AppSync**
- "Offline data sync" = **AppSync**
- "Multiple data sources, single API" = **AppSync**
- "REST API" = **API Gateway** (not AppSync)

---

## Amazon MQ

### Definition
Managed message broker for Apache ActiveMQ and RabbitMQ.

### Key Features
- **Brokers**: ActiveMQ, RabbitMQ
- **Protocols**: AMQP, MQTT, OpenWire, STOMP, WebSocket
- **High Availability**: Multi-AZ with failover
- **Storage**: EBS-backed
- **Encryption**: At rest and in transit
- **Migration**: Lift-and-shift existing message brokers
- **Standards**: Industry-standard protocols

### Exam Tips & Heuristics
✅ **When to Use MQ:**
- Migrate existing message brokers
- Need specific protocols (AMQP, MQTT)
- Existing applications using ActiveMQ/RabbitMQ
- Standards compliance

⚠️ **Common Pitfalls:**
- More complex than SQS/SNS
- Requires infrastructure management
- SQS/SNS preferred for new AWS-native apps

🎯 **Exam Heuristics:**
- "Migrate ActiveMQ/RabbitMQ" = **Amazon MQ**
- "MQTT protocol" = **Amazon MQ** or **IoT Core**
- "New application on AWS" = **SQS/SNS** (not MQ)
- "Standards-based messaging" = **Amazon MQ**
- "Existing message broker" = **Amazon MQ**

---

## Kinesis

### Definition
Platform for real-time data streaming and analytics.

### Key Features

#### Kinesis Data Streams
- **Real-time**: Collect and process data in real-time
- **Shards**: Capacity units (1MB/sec in, 2MB/sec out per shard)
- **Retention**: 24 hours (default) to 365 days
- **Producers**: Kinesis Agent, SDK, KPL (Kinesis Producer Library)
- **Consumers**: Lambda, Kinesis Data Analytics, KCL, EC2, Firehose
- **Ordering**: Per shard (partition key)
- **Replay**: Can reprocess data
- **Provisioned**: Must manage shard count

#### Kinesis Data Firehose
- **Load Data**: Near real-time (60 seconds latency minimum)
- **Destinations**: S3, Redshift, OpenSearch, HTTP endpoints, Splunk, Datadog, etc.
- **Transformation**: Lambda for data transformation
- **Compression**: GZIP, ZIP, Snappy (S3 only)
- **Batching**: Automatic batching
- **Serverless**: No shard management
- **No Replay**: Data not stored after delivery

#### Kinesis Data Analytics
- **Real-time Analytics**: SQL queries on streaming data
- **Sources**: Data Streams, Firehose
- **Destinations**: Data Streams, Firehose, Lambda
- **Apache Flink**: For Java, Scala, Python (advanced)

#### Kinesis Video Streams
- **Video Ingestion**: Stream video from devices
- **Playback**: HLS, MPEG-DASH
- **Storage**: Automatic storage and encryption
- **ML Integration**: Rekognition Video, SageMaker

### Exam Tips & Heuristics
✅ **When to Use Kinesis:**
- Real-time data streaming
- Log and event data collection
- IoT telemetry
- Clickstream analytics
- Real-time metrics

⚠️ **Common Pitfalls:**
- Data Streams vs Firehose (replay vs no replay)
- Data Streams requires shard management
- Firehose is near real-time (60 sec min latency)
- Kinesis vs SQS (streaming vs message queue)

🎯 **Exam Heuristics:**
- "Real-time streaming" = **Kinesis Data Streams**
- "Load to S3/Redshift" = **Kinesis Firehose**
- "Replay stream data" = **Kinesis Data Streams** (not Firehose)
- "Near real-time to S3" = **Kinesis Firehose**
- "Real-time SQL analytics" = **Kinesis Data Analytics**
- "Video streaming" = **Kinesis Video Streams**
- "Custom processing" = **Kinesis Data Streams** + Lambda/EC2
- "Serverless ingestion to S3" = **Kinesis Firehose**
- "Order per partition" = **Kinesis Data Streams**

### Kinesis Data Streams vs Firehose
| Feature | Data Streams | Firehose |
|---------|-------------|----------|
| Use Case | Custom processing, replay | Load to destinations |
| Latency | Real-time (~200ms) | Near real-time (60s min) |
| Storage | 24h-365 days | No storage |
| Management | Manual shards | Fully managed |
| Replay | Yes | No |
| Destinations | Custom | S3, Redshift, OpenSearch, etc. |

---

## Athena

### Definition
Serverless interactive query service to analyze data in S3 using standard SQL.

### Key Features
- **SQL Queries**: ANSI SQL on S3 data
- **Serverless**: No infrastructure
- **Pricing**: Pay per query (TB scanned)
- **Data Formats**: CSV, JSON, ORC, Parquet, Avro
- **Compression**: Reduce costs and improve performance
- **Partitioning**: Improve query performance
- **Integration**: Glue Data Catalog for metadata
- **Federated Query**: Query other data sources (DynamoDB, RDS, Redshift)
- **CTAS**: Create Table As Select for ETL
- **Workgroups**: Organize users and queries

### Exam Tips & Heuristics
✅ **When to Use Athena:**
- Ad-hoc queries on S3 data
- Log analysis
- Cost-effective analytics
- Serverless analytics
- Quick insights without ETL

⚠️ **Common Pitfalls:**
- Costs based on data scanned (use partitions/compression)
- Not for frequent small queries
- Query performance depends on data format
- Requires data in S3

🎯 **Exam Heuristics:**
- "Query S3 with SQL" = **Athena**
- "Serverless analytics" = **Athena**
- "Ad-hoc analysis" = **Athena**
- "Analyze logs in S3" = **Athena**
- "Cost-effective analytics" = **Athena** with Parquet/ORC
- "Reduce query costs" = Use **Parquet/ORC** + **partitioning**
- "ETL on S3 data" = **Athena CTAS** or **Glue**
- "Query CloudTrail logs" = **Athena**

---

## EMR (Elastic MapReduce)

### Definition
Managed Hadoop framework for big data processing at petabyte scale.

### Key Features
- **Frameworks**: Hadoop, Spark, HBase, Presto, Hive, Pig, Flink
- **Cluster**: Master, Core, Task nodes
- **Storage**: HDFS, EMRFS (S3), local instance store
- **Pricing**: EC2 pricing + EMR charges
- **Spot Instances**: Cost optimization for task nodes
- **EMR Notebooks**: Jupyter-based notebooks
- **EMR Serverless**: No cluster management
- **Auto Scaling**: Based on YARN metrics
- **Security**: IAM, Kerberos, encryption

### Exam Tips & Heuristics
✅ **When to Use EMR:**
- Big data processing
- Log analysis at scale
- ETL workflows
- Machine learning (Spark MLlib)
- Genomics, financial modeling

⚠️ **Common Pitfalls:**
- Requires cluster management (unless EMR Serverless)
- Master node failure loses cluster
- Transient clusters for cost savings
- Task nodes on Spot for cost optimization

🎯 **Exam Heuristics:**
- "Hadoop/Spark" = **EMR**
- "Big data processing" = **EMR**
- "Petabyte-scale analysis" = **EMR** or **Redshift**
- "Custom big data frameworks" = **EMR**
- "Cost optimization big data" = **EMR** with Spot instances
- "Serverless big data" = **EMR Serverless** or **Athena/Glue**

---

## Glue

### Definition
Serverless ETL service for preparing data for analytics.

### Key Features
- **Glue Data Catalog**: Metadata repository (central schema)
- **Crawlers**: Discover schema automatically
- **ETL Jobs**: Python or Scala (Apache Spark)
- **Glue Studio**: Visual ETL designer
- **Triggers**: Schedule or event-based
- **DynamicFrames**: Spark DataFrames with schema flexibility
- **Development Endpoints**: Test and develop ETL
- **Data Quality**: Validate data quality
- **Glue DataBrew**: Visual data preparation (no code)

### Exam Tips & Heuristics
✅ **When to Use Glue:**
- ETL workflows
- Data catalog for Athena, Redshift Spectrum, EMR
- Discover data schemas
- Serverless data preparation
- Transform data for analytics

⚠️ **Common Pitfalls:**
- Costs based on DPU-hours
- Not for real-time ETL (batch)
- Crawlers can be slow on large datasets
- Requires S3 as source/target mostly

🎯 **Exam Heuristics:**
- "ETL" = **Glue**
- "Data catalog" = **Glue Data Catalog**
- "Discover schema" = **Glue Crawler**
- "Transform for Athena" = **Glue ETL**
- "Serverless ETL" = **Glue**
- "Visual data preparation" = **Glue DataBrew**
- "Central metadata" = **Glue Data Catalog**

---

## QuickSight

### Definition
Serverless business intelligence service for creating dashboards and visualizations.

### Key Features
- **Data Sources**: S3, Athena, RDS, Redshift, Aurora, DynamoDB, etc.
- **SPICE**: In-memory calculation engine (fast)
- **Dashboards**: Interactive visualizations
- **ML Insights**: Anomaly detection, forecasting
- **Embedding**: Embed in applications
- **Row-Level Security**: Control data access
- **Pricing**: Per session (readers) or per user (authors)

### Exam Tips & Heuristics
✅ **When to Use QuickSight:**
- Business intelligence dashboards
- Data visualization
- Ad-hoc analysis
- Self-service BI
- Embed analytics in apps

⚠️ **Common Pitfalls:**
- SPICE has capacity limits (can add more)
- Not for operational monitoring (use CloudWatch)
- Requires data preparation (Glue, Athena)

🎯 **Exam Heuristics:**
- "BI dashboards" = **QuickSight**
- "Visualize data" = **QuickSight**
- "Business analytics" = **QuickSight**
- "Embed dashboards" = **QuickSight**
- "Self-service BI" = **QuickSight**

---

## Data Pipeline

### Definition
Web service for orchestrating data workflows (legacy, consider Glue or Step Functions).

### Key Features
- **Activities**: Define tasks (copy, transform, query)
- **Preconditions**: Check before running
- **Scheduling**: Regular intervals
- **Data Nodes**: S3, RDS, DynamoDB, Redshift
- **Resources**: EC2 or EMR for processing
- **Retries**: Automatic on failure

### Exam Tips & Heuristics
✅ **When to Use Data Pipeline:**
- Legacy workloads (existing pipelines)
- Complex dependencies
- Cross-region data movement

⚠️ **Common Pitfalls:**
- Legacy service (Glue/Step Functions preferred)
- Requires infrastructure (EC2/EMR)
- More complex than Glue

🎯 **Exam Heuristics:**
- "New data workflow" = **Glue** or **Step Functions** (not Data Pipeline)
- "Legacy ETL" = **Data Pipeline** (if already using)
- Most exam questions will point to Glue, not Data Pipeline

---

## MSK (Managed Streaming for Kafka)

### Definition
Fully managed Apache Kafka service for building real-time streaming applications.

### Key Features
- **Kafka Versions**: Multiple versions supported
- **Broker Nodes**: Multi-AZ deployment
- **Storage**: EBS volumes
- **Encryption**: At rest and in transit
- **Authentication**: IAM, TLS, SASL/SCRAM
- **Monitoring**: CloudWatch, Prometheus
- **MSK Connect**: Managed Kafka Connect
- **MSK Serverless**: On-demand Kafka clusters

### Exam Tips & Heuristics
✅ **When to Use MSK:**
- Migrate existing Kafka workloads
- Need Kafka ecosystem
- High-throughput streaming
- Event sourcing

⚠️ **Common Pitfalls:**
- More complex than Kinesis
- Requires Kafka expertise
- Kinesis preferred for AWS-native apps

🎯 **Exam Heuristics:**
- "Apache Kafka" = **MSK**
- "Migrate Kafka to AWS" = **MSK**
- "New streaming on AWS" = **Kinesis** (simpler) or **MSK** (if Kafka needed)
- "Kafka ecosystem tools" = **MSK**

---

## 🎓 Integration & Analytics Service Selection Guide

| Requirement | Service |
|------------|---------|
| Message queue | SQS |
| Pub/sub messaging | SNS |
| Event-driven architecture | EventBridge |
| Workflow orchestration | Step Functions |
| GraphQL API | AppSync |
| Migrate message broker | Amazon MQ |
| Real-time streaming | Kinesis Data Streams |
| Load data to S3/Redshift | Kinesis Firehose |
| Query S3 with SQL | Athena |
| Big data processing | EMR |
| Serverless ETL | Glue |
| BI dashboards | QuickSight |
| Managed Kafka | MSK |

---

## 💡 Key Exam Patterns

### Decoupling Patterns
- SQS for asynchronous processing
- SNS for fan-out
- EventBridge for event routing
- Step Functions for orchestration

### Streaming Patterns
- Kinesis Data Streams for real-time processing
- Kinesis Firehose for loading data
- Kinesis Analytics for real-time analytics
- MSK for Kafka workloads

### Analytics Patterns
- Athena for ad-hoc queries
- Redshift for data warehousing
- EMR for big data frameworks
- QuickSight for visualization
- Glue for ETL and catalog

### Fan-out Pattern
- SNS topic → Multiple SQS queues
- Enables parallel, asynchronous processing
- Each subscriber gets copy of message

