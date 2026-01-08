# AWS Application Integration, Analytics & Serverless - Complete Definitions (SAA-C03)

## Table of Contents

### Application Integration
- [Amazon SQS](#amazon-sqs)
- [Amazon SNS](#amazon-sns)
- [Amazon MQ](#amazon-mq)
- [AWS Step Functions](#aws-step-functions)
- [Amazon EventBridge](#amazon-eventbridge)
- [Amazon AppFlow](#amazon-appflow)

### Analytics
- [Amazon Kinesis](#amazon-kinesis)
- [Amazon Athena](#amazon-athena)
- [AWS Glue](#aws-glue)
- [Amazon EMR](#amazon-emr)
- [Amazon OpenSearch Service](#amazon-opensearch-service)
- [Amazon QuickSight](#amazon-quicksight)
- [AWS Data Pipeline](#aws-data-pipeline)
- [AWS Lake Formation](#aws-lake-formation)
- [Amazon MSK](#amazon-msk)

### Serverless
- [AWS Lambda](#aws-lambda-detailed)
- [AWS SAM](#aws-sam)
- [AWS AppSync](#aws-appsync)

---

## Application Integration Services

## Amazon SQS (Simple Queue Service)

**Definition**: Fully managed message queuing service for decoupling and scaling microservices, distributed systems, and serverless applications.

**Key Features**:
- Fully managed
- Unlimited throughput and messages
- Message retention: 1 minute to 14 days (default 4 days)
- Low latency (< 10 ms)
- Duplicate messages possible (at least once delivery)
- Out-of-order messages possible (best effort ordering)

**Queue Types**:

### Standard Queue
- **Throughput**: Unlimited
- **Ordering**: Best-effort ordering
- **Delivery**: At-least-once delivery
- **Use Case**: High throughput, duplicates OK

### FIFO Queue
- **Throughput**: 300 TPS (3,000 with batching)
- **Ordering**: Exactly-once processing, guaranteed ordering
- **Delivery**: Exactly-once delivery
- **Naming**: Must end with .fifo
- **Use Case**: Order matters, no duplicates
- **Deduplication**: Content-based or message deduplication ID
- **Message Groups**: Ordered within group, parallel processing across groups

**Message Attributes**:
- Message body (up to 256 KB)
- Message attributes (metadata)
- Message ID
- Receipt handle
- Message deduplication ID (FIFO)
- Message group ID (FIFO)

**Visibility Timeout**:
- Message invisible after polling
- Default: 30 seconds (0 sec - 12 hours)
- Change with ChangeMessageVisibility API
- If not processed in time, message visible again
- Set timeout longer than processing time

**Long Polling**:
- Wait for messages to arrive (1-20 seconds)
- Reduces API calls
- Reduces cost
- Preferred over short polling
- Set WaitTimeSeconds > 0

**Dead Letter Queue (DLQ)**:
- Receives messages that fail processing
- MaxReceiveCount threshold
- Isolate problematic messages
- Debug and analyze
- Must be same type as source (Standard → Standard, FIFO → FIFO)

**Delay Queues**:
- Postpone message delivery
- Delay: 0 seconds to 15 minutes
- Default: 0 seconds
- Change per-message or per-queue

**Message Timers**:
- Per-message delay (overrides queue delay)
- FIFO queues don't support message timers

**Security**:
- Encryption in transit (HTTPS)
- Encryption at rest (KMS)
- Access control (IAM policies)
- SQS access policies (resource-based)

**Use Cases**:
- Decouple microservices
- Buffering and batch operations
- Request offloading
- Fault tolerance
- Async processing
- Load leveling

**Exam Tips**:
- Standard: Unlimited throughput, best-effort ordering, at-least-once
- FIFO: Limited throughput, guaranteed ordering, exactly-once
- Message size up to 256 KB (use S3 for larger)
- Visibility timeout prevents duplicate processing
- Long polling reduces costs and empty responses
- DLQ for problematic messages
- Delay queues postpone delivery
- FIFO names must end with .fifo
- Use Message Group ID for ordered processing in FIFO
- SQS doesn't push messages (consumers poll)

---

## Amazon SNS (Simple Notification Service)

**Definition**: Fully managed pub/sub messaging service for decoupling and scaling microservices, distributed systems, and serverless applications.

**Key Features**:
- Pub/Sub model
- Push notifications
- Multiple subscribers per topic
- Fan-out pattern
- Message filtering
- Delivery retries
- High throughput
- Mobile push notifications

**Components**:

### Topics
- Communication channel
- Standard or FIFO
- Multiple subscriptions

### Subscriptions
- Endpoint receiving messages
- Protocol types:
  - HTTP/HTTPS
  - Email/Email-JSON
  - SMS
  - SQS
  - Lambda
  - Mobile push (APNS, FCM, etc.)
  - Kinesis Data Firehose

### Publishers
- Send messages to topics
- AWS services or applications

**Topic Types**:

### Standard Topics
- Unlimited throughput
- Best-effort ordering
- At-least-once delivery

### FIFO Topics
- 300 TPS (3,000 with batching)
- Guaranteed ordering
- Exactly-once delivery
- Must subscribe FIFO SQS queues

**Message Attributes**:
- Metadata key-value pairs
- Filter policies use attributes
- Up to 10 attributes

**Message Filtering**:
- Subscribers receive subset of messages
- Filter policy (JSON)
- Reduces unnecessary processing
- No additional cost

**Delivery Policies**:
- HTTP/HTTPS retries
- Immediate, geometric, arithmetic, exponential
- Maximum receive rate
- Throttling

**Fan-Out Pattern**:
- SNS topic → multiple SQS queues
- Parallel processing
- Decoupling
- Persistence (SQS)

**Security**:
- Encryption in transit (HTTPS)
- Encryption at rest (KMS)
- Access control (IAM policies)
- Topic policies (resource-based)
- VPC endpoints

**Mobile Push**:
- iOS (APNS)
- Android (FCM)
- Windows, Baidu
- Application platform

**Use Cases**:
- Application alerts
- Mobile push notifications
- Email/SMS notifications
- Fan-out to multiple services
- Event-driven architectures
- Distributed system coordination

**SNS + SQS Fan-Out**:
- SNS publishes to multiple SQS queues
- Each service has own queue
- Parallel, async processing
- Persistent messages
- Decoupled architecture

**Exam Tips**:
- SNS for pub/sub, push notifications
- Multiple subscribers per topic
- Fan-out pattern with SQS for parallel processing
- Message filtering at subscription level
- FIFO topics for ordered, exactly-once delivery
- Standard topics for high throughput
- Cannot retrieve messages (delivery attempt only)
- Use with SQS for persistence and retry
- Mobile push notifications supported
- Integration with AWS services for event notifications

---

## Amazon MQ

**Definition**: Managed message broker service for Apache ActiveMQ and RabbitMQ, supporting industry-standard APIs and protocols.

**Key Features**:
- Managed service for ActiveMQ and RabbitMQ
- JMS, NMS, AMQP, STOMP, MQTT, WebSocket
- Supports existing applications
- High availability (Multi-AZ)
- Automatic failover
- Encryption

**Supported Engines**:

### Apache ActiveMQ
- JMS API
- Multi-protocol support
- Network of brokers
- Persistent and non-persistent messages

### RabbitMQ
- AMQP 0-9-1 protocol
- Exchanges and queues
- Flexible routing
- Clustering

**Deployment Modes**:

### Single-Instance Broker
- One broker in one AZ
- Lower cost
- Dev/test environments

### Active/Standby Broker (Multi-AZ)
- Two brokers in different AZs
- Automatic failover
- Shared storage (EFS for RabbitMQ, EBS for ActiveMQ)
- Production workloads

### Network of Brokers (ActiveMQ)
- Multiple brokers
- Horizontal scaling
- Load distribution

**Storage**:
- **ActiveMQ**: EBS (durability within AZ)
- **RabbitMQ**: EFS (Multi-AZ durability)

**Security**:
- VPC deployment
- Encryption in transit (TLS)
- Encryption at rest
- Authentication (simple, LDAP)
- Authorization

**Use Cases**:
- Migrate existing applications to AWS
- JMS-compliant applications
- AMQP-based applications
- Hybrid architectures
- Legacy application integration

**Amazon MQ vs SQS/SNS**:

| Feature | Amazon MQ | SQS/SNS |
|---------|-----------|---------|
| **Protocols** | JMS, AMQP, MQTT, STOMP | AWS API |
| **Use Case** | Migrate existing apps | New cloud-native apps |
| **Management** | More control | Fully managed |
| **Scaling** | Manual | Automatic |
| **Cost** | Instance-based | Usage-based |

**Exam Tips**:
- Amazon MQ for migrating existing message broker applications
- Supports ActiveMQ and RabbitMQ
- Use when you need JMS, AMQP, MQTT, STOMP protocols
- SQS/SNS for new cloud-native applications
- Multi-AZ for high availability
- VPC deployment required
- More operational overhead than SQS/SNS
- Use for lift-and-shift migrations

---

## AWS Step Functions

**Definition**: Serverless orchestration service that lets you coordinate multiple AWS services into serverless workflows using visual workflows.

**Key Features**:
- Visual workflow designer
- State machine
- Parallel execution
- Error handling and retries
- Human approval steps
- Service integrations (200+ AWS services)
- Standard and Express workflows

**State Types**:
- **Task**: Single unit of work (Lambda, Batch, SNS, etc.)
- **Choice**: Conditional branching
- **Parallel**: Execute branches in parallel
- **Wait**: Delay execution
- **Succeed**: Successful termination
- **Fail**: Failure termination
- **Pass**: Pass input to output
- **Map**: Iterate over items

**Workflow Types**:

### Standard Workflows
- Duration: Up to 1 year
- Execution rate: 2,000 per second
- Exactly-once execution
- State transitions charged
- Execution history
- Use case: Long-running, auditable workflows

### Express Workflows
- Duration: Up to 5 minutes
- Execution rate: 100,000 per second
- At-least-once execution
- Cheaper
- Use case: High-volume, short-duration workflows

**Express Workflow Types**:
- **Synchronous**: Wait for completion, return result
- **Asynchronous**: Fire and forget

**Error Handling**:
- Retry with exponential backoff
- Catch errors
- Fallback states
- All errors or specific errors

**Integrations**:
- AWS Lambda
- AWS Batch
- ECS/Fargate
- SNS, SQS
- DynamoDB
- SageMaker
- Glue
- EMR
- Many more

**Wait for Callback**:
- Pause execution
- Wait for external task
- Send task token
- Resume on callback

**Use Cases**:
- Orchestrate microservices
- Data processing pipelines
- IT automation
- Batch processing
- Human approval workflows
- ETL pipelines

**Exam Tips**:
- Step Functions for workflow orchestration
- Visual workflow designer
- Standard for long-running (up to 1 year)
- Express for high-volume, short-duration (up to 5 minutes)
- Error handling with retry and catch
- Integrates with 200+ AWS services
- State machine defined in JSON (Amazon States Language)
- No servers to manage
- Pay per state transition (Standard) or execution (Express)
- Use for coordinating distributed applications

---

## Amazon EventBridge

**Definition**: Serverless event bus service for building event-driven applications at scale using events from AWS services, SaaS applications, and custom applications.

(Note: Previously called CloudWatch Events)

**Key Features**:
- Event-driven architecture
- Multiple event buses
- Event patterns and rules
- Schema registry
- Archive and replay
- SaaS integration
- Cross-account event routing

**Components**:

### Event Buses
- **Default**: AWS service events
- **Custom**: Your application events
- **Partner**: SaaS events (Datadog, Zendesk, etc.)

### Rules
- Match event patterns
- Schedule (cron/rate)
- Route to targets

### Targets
- Lambda functions
- Step Functions
- SNS, SQS
- Kinesis
- ECS tasks
- CodePipeline
- SSM Run Command
- 20+ targets

**Event Pattern**:
- JSON format
- Filter events
- Content-based filtering
- Logical operators

**Event Structure**:
```json
{
  "version": "0",
  "id": "unique-id",
  "detail-type": "EC2 Instance State-change Notification",
  "source": "aws.ec2",
  "account": "123456789012",
  "time": "2021-01-01T00:00:00Z",
  "region": "us-east-1",
  "resources": ["arn:aws:ec2:..."],
  "detail": { }
}
```

**Schema Registry**:
- Discover event schemas
- Generate code bindings
- Version schemas
- Simplify development

**Archive and Replay**:
- Archive events indefinitely
- Replay past events
- Debug and test
- Reprocess events

**Input Transformation**:
- Modify event before sending to target
- Extract specific fields
- Add static text

**Use Cases**:
- Event-driven architectures
- Real-time processing
- Application integration
- Monitoring and alerting
- Scheduled tasks
- SaaS integration

**Exam Tips**:
- EventBridge for event-driven architectures
- Default bus for AWS services, custom for applications
- Rules match events and route to targets
- Schedule events with cron or rate expressions
- Schema registry for event discovery
- Archive and replay for debugging
- Cross-account event routing
- Successor to CloudWatch Events (more features)
- Content-based filtering with event patterns

---

## Amazon AppFlow

**Definition**: Fully managed integration service for securely transferring data between SaaS applications and AWS services.

**Key Features**:
- Bi-directional data transfer
- No code required
- Data transformation
- Filtering
- Validation
- Encryption
- Private connectivity (PrivateLink)

**Supported Sources**:
- Salesforce
- SAP
- Zendesk
- Slack
- ServiceNow
- Google Analytics
- Marketo
- And more

**Supported Destinations**:
- S3
- Redshift
- Snowflake
- Salesforce
- And more

**Flow Triggers**:
- On-demand
- On schedule
- On event

**Data Transformation**:
- Map fields
- Concatenate
- Mask fields
- Truncate
- Validate

**Use Cases**:
- SaaS data integration
- Data lake ingestion
- Customer 360 view
- Compliance and security
- Analytics preparation

**Exam Tips**:
- AppFlow for SaaS to AWS integration
- Bi-directional data transfer
- No code required
- Data transformation and validation
- Private connectivity with PrivateLink
- Scheduled, event-based, or on-demand flows

---

## Analytics Services

## Amazon Kinesis

**Definition**: Platform for streaming data on AWS, making it easy to collect, process, and analyze real-time streaming data.

**Services**:

### Kinesis Data Streams

**Definition**: Collect and process real-time streaming data.

**Key Features**:
- Real-time (~ 200 ms latency)
- Durable storage (1-365 days retention)
- Replay capability
- Multiple consumers
- Ordering within shard
- Manual scaling (shards)

**Components**:
- **Producers**: Put records into streams
- **Shards**: Units of capacity (1 MB/s in, 2 MB/s out)
- **Consumers**: Get records from streams
- **Records**: Data blob (up to 1 MB), partition key, sequence number

**Capacity Modes**:
- **Provisioned**: Specify number of shards
- **On-Demand**: Auto-scaling (more expensive)

**Consumers**:
- Kinesis Client Library (KCL)
- AWS Lambda
- Kinesis Data Analytics
- Kinesis Data Firehose

**Use Cases**:
- Real-time analytics
- Log and event processing
- IoT data ingestion
- Click stream analysis

### Kinesis Data Firehose

**Definition**: Load streaming data into data stores and analytics services.

**Key Features**:
- Near real-time (60 seconds minimum latency)
- Fully managed (auto-scaling)
- No data storage
- Data transformation with Lambda
- Compression
- No replay capability

**Destinations**:
- **AWS**: S3, Redshift, OpenSearch, Splunk
- **Third-Party**: Datadog, New Relic, MongoDB, Splunk
- **Custom HTTP Endpoint**

**Data Transformation**:
- Lambda functions
- Format conversion (JSON to Parquet/ORC)

**Buffer**:
- Buffer size (1 MB - 128 MB)
- Buffer interval (60 - 900 seconds)
- Whichever condition met first

**Use Cases**:
- Stream to S3 for data lake
- Stream to Redshift for analytics
- Stream to OpenSearch for indexing
- Real-time ETL

### Kinesis Data Analytics

**Definition**: Analyze streaming data in real-time with SQL or Apache Flink.

**Key Features**:
- Real-time analytics
- Standard SQL or Apache Flink
- Fully managed
- Auto-scaling
- Pay for resources consumed

**Sources**:
- Kinesis Data Streams
- Kinesis Data Firehose

**Destinations**:
- Kinesis Data Streams
- Kinesis Data Firehose
- Lambda

**Use Cases**:
- Real-time dashboards
- Real-time metrics
- Streaming ETL
- Real-time alerts

### Kinesis Video Streams

**Definition**: Stream video from connected devices to AWS for analytics and ML.

**Key Features**:
- Video ingestion
- Storage
- Playback
- Real-time and batch processing
- ML integration (SageMaker, Rekognition)

**Use Cases**:
- Smart home security
- Industrial automation
- Video analytics

**Kinesis Services Comparison**:

| Service | Latency | Storage | Replay | Use Case |
|---------|---------|---------|--------|----------|
| **Data Streams** | Real-time | Yes | Yes | Custom processing |
| **Data Firehose** | Near real-time | No | No | Load to destinations |
| **Data Analytics** | Real-time | No | N/A | SQL/Flink analytics |
| **Video Streams** | Real-time | Yes | Yes | Video ingestion |

**Exam Tips**:
- **Data Streams**: Real-time, custom processing, replay
- **Data Firehose**: Near real-time, load to destinations, no replay
- **Data Analytics**: Real-time SQL/Flink analytics
- Data Streams requires manual shard management
- Firehose is fully managed, auto-scaling
- Firehose can transform data with Lambda
- Data Streams retention: 1-365 days
- Use Firehose to load data into S3, Redshift, OpenSearch
- Use Data Streams for custom consumers and replay

---

## Amazon Athena

**Definition**: Interactive query service that makes it easy to analyze data in S3 using standard SQL.

**Key Features**:
- Serverless
- SQL queries on S3 data
- Pay per query (data scanned)
- ANSI SQL support
- JDBC/ODBC connectivity
- Integration with QuickSight
- Federated queries

**Data Formats**:
- CSV, JSON, Apache Parquet, Apache ORC
- Avro, and more
- Compressed formats (GZIP, SNAPPY, etc.)

**Partitioning**:
- Reduce data scanned
- Lower cost
- Faster queries
- Format: s3://bucket/key=value/

**Optimization**:
- Use columnar formats (Parquet, ORC)
- Compress data
- Partition data
- Use appropriate data types
- Avoid scanning unnecessary data

**Federated Queries**:
- Query data across sources
- Data Source Connectors (Lambda-based)
- Relational, non-relational, object, custom

**Workgroups**:
- Isolate queries
- Control costs
- IAM policies
- Query history
- Output location

**Use Cases**:
- Ad-hoc queries on S3 data
- Log analysis
- Business intelligence
- Query CloudTrail logs
- Query VPC Flow Logs
- Cost and usage analysis

**Exam Tips**:
- Athena for SQL queries on S3 data
- Serverless, pay per query (data scanned)
- Use Parquet/ORC for best performance
- Partition data to reduce cost
- Compress data to reduce cost
- Integrate with QuickSight for visualization
- Query CloudTrail logs, VPC Flow Logs, ELB logs
- Federated queries across multiple sources
- No data loading required (query in place)

---

## AWS Glue

**Definition**: Fully managed ETL (extract, transform, load) service that makes it easy to prepare and load data for analytics.

**Key Components**:

### Glue Data Catalog

**Definition**: Centralized metadata repository for data assets.

**Key Features**:
- Table definitions
- Schema registry
- Partition information
- Integration with Athena, Redshift Spectrum, EMR
- Crawlers for automatic schema discovery

**Crawlers**:
- Scan data sources
- Infer schemas
- Create table definitions
- Update on schedule
- Sources: S3, RDS, Redshift, DynamoDB

### Glue ETL

**Definition**: Create and run ETL jobs.

**Key Features**:
- Scala or Python (PySpark)
- Serverless
- Auto-scaling
- Job bookmarks (incremental processing)
- Development endpoints
- Visual ETL editor

**Glue Studio**:
- Visual interface for ETL
- Drag-and-drop
- No code required
- Generate code

### Glue DataBrew

**Definition**: Visual data preparation tool.

**Key Features**:
- 250+ pre-built transformations
- No code required
- Data quality
- Data profiling

### Glue Elastic Views

**Definition**: Replicate and combine data across multiple data stores using SQL.

**Key Features**:
- No ETL code
- Continuous data replication
- Virtual view

**Security**:
- Encryption at rest (S3, catalog)
- Encryption in transit (SSL)
- IAM permissions
- Resource policies
- VPC support

**Use Cases**:
- Data lake preparation
- ETL workflows
- Schema discovery
- Data cataloging
- Data transformation

**Exam Tips**:
- Glue for serverless ETL
- Data Catalog for metadata management
- Crawlers automatically discover schemas
- Integration with Athena, Redshift Spectrum, EMR
- Pay for DPU-hours (data processing units)
- Job bookmarks for incremental processing
- Glue Studio for visual ETL
- DataBrew for data preparation
- Use for data lake and analytics workloads

---

## Amazon EMR (Elastic MapReduce)

**Definition**: Cloud big data platform for processing vast amounts of data using open-source tools such as Apache Spark, Hive, HBase, Flink, Hudi, and Presto.

**Key Features**:
- Managed Hadoop framework
- Resizable clusters
- Spot Instance support
- EBS and S3 storage
- Multiple applications (Spark, Hive, HBase, etc.)
- Serverless option (EMR Serverless)

**Architecture**:
- **Master Node**: Manages cluster
- **Core Nodes**: Run tasks and store data (HDFS)
- **Task Nodes**: Run tasks only (no HDFS)

**Deployment Options**:

### EMR on EC2
- Full control over cluster
- Custom instance types
- Spot Instances for cost savings

### EMR on EKS
- Run Spark on existing EKS cluster
- Shared infrastructure

### EMR Serverless
- No infrastructure management
- Automatic scaling
- Pay per use

**Storage**:
- **HDFS**: Local storage on cluster
- **EMRFS**: S3 with HDFS-like access
- **Local Storage**: Instance store

**Applications**:
- Apache Spark, Hadoop, Hive
- HBase, Presto, Flink
- Hudi, Delta Lake
- Zeppelin, Jupyter

**Security**:
- IAM roles
- Kerberos authentication
- EMRFS encryption
- In-transit encryption
- At-rest encryption
- VPC deployment
- Security groups

**Auto Scaling**:
- Scale core and task nodes
- Based on YARN metrics
- Custom scaling rules

**Use Cases**:
- Big data processing
- Log analysis
- Machine learning
- Data transformations
- Genomics
- Financial analysis

**Exam Tips**:
- EMR for big data processing with Hadoop ecosystem
- Master, Core, Task node types
- Use Spot Instances for task nodes (cost savings)
- EMRFS for S3 access with HDFS API
- EMR Serverless for no infrastructure management
- Multiple applications on same cluster
- Auto Scaling for dynamic capacity
- Long-running clusters or transient (spin up/down)

---

## Amazon OpenSearch Service

**Definition**: Managed service for OpenSearch (fork of Elasticsearch) for log analytics, full-text search, and application monitoring.

**Key Features**:
- Managed OpenSearch/Elasticsearch
- OpenSearch Dashboards (Kibana)
- Indexing and search
- Real-time application monitoring
- Log analytics
- Security analytics
- Full-text search

**Deployment**:
- Domain (cluster)
- Master and data nodes
- UltraWarm nodes (cost-effective storage)
- Cold storage (S3)
- Multi-AZ deployment

**Node Types**:
- **Master Nodes**: Cluster management
- **Data Nodes**: Store data and execute queries
- **UltraWarm Nodes**: Cost-effective for read-only data
- **Cold Storage**: S3-based, lowest cost

**Data Ingestion**:
- Kinesis Data Firehose
- CloudWatch Logs
- AWS IoT
- Logstash
- Beats
- Custom applications

**Security**:
- VPC deployment
- Encryption at rest
- Encryption in transit
- Fine-grained access control
- IAM integration
- Cognito authentication

**Scaling**:
- Vertical scaling (instance types)
- Horizontal scaling (add nodes)
- Auto-Tune for performance optimization

**Use Cases**:
- Log analytics (application, infrastructure logs)
- Security analytics (SIEM)
- Full-text search
- Application monitoring
- Click stream analytics

**Exam Tips**:
- OpenSearch for search and log analytics
- Formerly Elasticsearch Service
- Integrates with Kinesis Data Firehose for log ingestion
- OpenSearch Dashboards for visualization (formerly Kibana)
- UltraWarm for cost-effective storage
- Multi-AZ for high availability
- VPC deployment for security
- Use for ELK stack (Elasticsearch, Logstash, Kibana) equivalent

---

## Amazon QuickSight

**Definition**: Cloud-powered business intelligence service for creating visualizations, performing ad-hoc analysis, and getting business insights from data.

**Key Features**:
- Serverless
- Interactive dashboards
- ML Insights (anomaly detection, forecasting)
- Embedded analytics
- Pay-per-session pricing
- Mobile access

**Data Sources**:
- AWS: RDS, Aurora, Redshift, Athena, S3, OpenSearch
- On-premises: SQL Server, MySQL, PostgreSQL
- SaaS: Salesforce, ServiceNow
- Files: CSV, Excel, JSON
- SPICE in-memory engine

**SPICE**:
- Super-fast, Parallel, In-memory Calculation Engine
- Columnar storage
- Caching for performance
- 10 GB per user (Enterprise)

**User Types**:
- **Reader**: View and interact with dashboards
- **Author**: Create and publish dashboards

**Editions**:
- **Standard**: Basic BI capabilities
- **Enterprise**: Advanced features, encryption, ML Insights

**ML Insights**:
- Anomaly detection
- Forecasting
- Auto-narratives
- Contribution analysis

**Embedded Analytics**:
- Embed dashboards in applications
- Customizable branding
- Row-level security

**Use Cases**:
- Business intelligence
- Ad-hoc analysis
- Interactive dashboards
- Embedded analytics
- Data visualization

**Exam Tips**:
- QuickSight for BI and visualization
- Serverless, pay-per-session
- SPICE for in-memory analysis
- ML Insights for anomaly detection and forecasting
- Integrates with Athena, Redshift, RDS, S3
- Embedded analytics in applications
- Enterprise edition for ML Insights

---

## AWS Data Pipeline

**Definition**: Web service for orchestrating and automating the movement and transformation of data.

**Key Features**:
- Data workflow orchestration
- Scheduled or event-driven
- Data movement between AWS services
- On-premises integration
- Fault tolerance
- Retries

**Components**:
- **Pipeline**: Workflow definition
- **Data Nodes**: Data locations (S3, RDS, DynamoDB, Redshift)
- **Activities**: Actions (EMR, Hive, Copy, SQL)
- **Resources**: Compute (EC2, EMR)
- **Schedule**: When to run

**Use Cases**:
- ETL workflows
- Data movement
- Periodic data processing
- Log file processing

**Data Pipeline vs Glue**:

| Feature | Data Pipeline | Glue |
|---------|---------------|------|
| **Focus** | Data movement | ETL |
| **Processing** | EC2, EMR | Serverless Spark |
| **Management** | More control | Fully managed |
| **Use Case** | Complex workflows | Serverless ETL |

**Exam Tips**:
- Data Pipeline for data movement workflows
- Schedule or event-driven
- Can use on-premises resources
- Consider Glue for new ETL workloads (more modern)
- Data Pipeline for complex, custom workflows

---

## AWS Lake Formation

**Definition**: Service that makes it easy to set up, secure, and manage data lakes.

**Key Features**:
- Centralized data lake
- Data ingestion
- Data cataloging
- Security and governance
- Fine-grained access control
- Audit logging

**Capabilities**:
- Import data from databases, data warehouses, S3
- Crawlers for metadata discovery
- Data transformations (Glue)
- Blueprints for common patterns
- Row and column-level security
- Integration with analytics services

**Security**:
- Centralized permissions
- Lake Formation permissions (granular)
- Replace S3 and IAM permissions
- Column-level security
- Row-level security (data filters)

**Integration**:
- Athena
- Redshift Spectrum
- EMR
- Glue
- QuickSight

**Use Cases**:
- Centralized data lake
- Data lake security
- Simplified permissions
- Data cataloging
- Compliance

**Exam Tips**:
- Lake Formation for data lake management
- Centralized security and governance
- Fine-grained access control (row/column level)
- Integration with Glue Data Catalog
- Blueprints for data ingestion
- Simplifies data lake setup
- Use for securing data lakes

---

## Amazon MSK (Managed Streaming for Apache Kafka)

**Definition**: Fully managed service for Apache Kafka, making it easy to build and run applications that use Kafka to process streaming data.

**Key Features**:
- Managed Apache Kafka
- High availability (Multi-AZ)
- Auto-recovery
- Encryption
- Monitoring (CloudWatch, Prometheus)
- Fully compatible with Kafka APIs

**MSK Cluster**:
- Brokers across multiple AZs
- Zookeeper nodes (managed)
- EBS storage
- Control plane and data plane

**MSK Serverless**:
- Serverless Kafka
- Automatic scaling
- Pay for throughput
- No broker management

**MSK Connect**:
- Managed Kafka Connect
- Source and sink connectors
- Stream data to/from Kafka
- S3, databases, search engines

**Security**:
- VPC deployment
- Encryption in transit (TLS)
- Encryption at rest (KMS)
- IAM authentication
- Apache Kafka ACLs
- SASL/SCRAM authentication

**Use Cases**:
- Real-time data pipelines
- Stream processing
- Event-driven architectures
- Log aggregation
- IoT data streaming

**MSK vs Kinesis Data Streams**:

| Feature | MSK | Kinesis Data Streams |
|---------|-----|----------------------|
| **Compatibility** | Apache Kafka | AWS proprietary |
| **Message Size** | 1 MB default (configurable) | 1 MB max |
| **Retention** | Configurable (default 7 days) | 1-365 days |
| **Consumers** | Unlimited | 5 per shard (shared), 20 per shard (enhanced) |
| **Use Case** | Kafka ecosystem | AWS-native |

**Exam Tips**:
- MSK for managed Apache Kafka
- Use when you need Kafka compatibility
- MSK Serverless for auto-scaling Kafka
- MSK Connect for Kafka Connect
- Kinesis for AWS-native streaming
- MSK for existing Kafka applications
- Multi-AZ for high availability

---

## Serverless Services

## AWS Lambda (Detailed)

(Core information covered in Compute section; additional serverless details here)

**Additional Lambda Concepts**:

### Lambda@Edge
- Run Lambda at CloudFront edge locations
- Customize content delivery
- Viewer request/response, origin request/response
- Low latency
- Reduced backend load

### Lambda Layers
- Package dependencies separately
- Share code across functions
- Up to 5 layers per function
- Version layers
- Reduce deployment package size

### Lambda Destinations
- Success and failure destinations
- Asynchronous invocations
- SQS, SNS, EventBridge, another Lambda
- Replace DLQ functionality

### Lambda Extensions
- Augment Lambda functions
- Monitoring, observability, security tools
- External and internal extensions
- Third-party integrations

**Invocation Types**:
- **Synchronous**: Wait for response (API Gateway, ALB)
- **Asynchronous**: Lambda queues, retries (S3, SNS, EventBridge)
- **Polling**: Lambda polls (SQS, Kinesis, DynamoDB Streams)

**Error Handling**:
- Retry (async: 2 times, polling: until success or expiration)
- DLQ (Dead Letter Queue) for failed events
- Destinations for success/failure routing

**Exam Tips**:
- Lambda@Edge for edge customization
- Layers for code sharing
- Destinations for async success/failure handling
- 15-minute execution limit
- Use Step Functions for orchestration
- Concurrent execution limits

---

## AWS SAM (Serverless Application Model)

**Definition**: Open-source framework for building serverless applications, extends CloudFormation with serverless-specific resources.

**Key Features**:
- Simplified CloudFormation syntax
- Local testing and debugging
- Built-in best practices
- One-command deployment
- Integration with CodeDeploy

**SAM Template**:
- YAML or JSON
- Transform: AWS::Serverless-2016-10-31
- Simplified resource definitions

**SAM Resources**:
- **AWS::Serverless::Function**: Lambda function
- **AWS::Serverless::Api**: API Gateway
- **AWS::Serverless::SimpleTable**: DynamoDB table
- **AWS::Serverless::Application**: Nested applications
- **AWS::Serverless::LayerVersion**: Lambda layer
- **AWS::Serverless::StateMachine**: Step Functions

**SAM CLI**:
- Build, test, deploy
- Local Lambda invocation
- Local API Gateway
- Generate sample events
- Validate templates

**Use Cases**:
- Serverless application development
- Local testing before deployment
- CI/CD for serverless
- Simplified CloudFormation for serverless

**Exam Tips**:
- SAM for serverless application development
- Extends CloudFormation
- Local testing with SAM CLI
- Simplified syntax for Lambda, API Gateway, DynamoDB
- Deployment with sam deploy
- Use for serverless IaC

---

## AWS AppSync

**Definition**: Managed GraphQL service for building data-driven mobile and web applications.

**Key Features**:
- GraphQL API
- Real-time subscriptions
- Offline data sync
- Multiple data sources
- Fine-grained security
- Caching

**Data Sources**:
- DynamoDB
- Lambda
- RDS (Aurora Serverless)
- OpenSearch
- HTTP endpoints

**Resolvers**:
- Connect GraphQL fields to data sources
- VTL (Velocity Template Language) or Lambda
- Request/response mapping

**Real-Time**:
- WebSocket subscriptions
- Automatic updates to clients
- Pub/sub pattern

**Offline**:
- AWS Amplify DataStore
- Local caching
- Sync when online

**Security**:
- API keys
- IAM
- Cognito User Pools
- OpenID Connect

**Use Cases**:
- Mobile backends
- Real-time dashboards
- Collaborative applications
- Offline-first apps
- GraphQL APIs

**Exam Tips**:
- AppSync for GraphQL APIs
- Real-time subscriptions
- Offline synchronization
- Multiple data sources
- Integration with Cognito for auth
- Use for mobile/web app backends

---

## Summary Tables

### Application Integration Comparison

| Service | Type | Use Case |
|---------|------|----------|
| **SQS** | Queue | Decouple components, async processing |
| **SNS** | Pub/Sub | Push notifications, fan-out |
| **MQ** | Message Broker | Migrate existing apps (JMS, AMQP) |
| **Step Functions** | Orchestration | Workflow coordination |
| **EventBridge** | Event Bus | Event-driven architecture |
| **AppFlow** | Integration | SaaS to AWS data transfer |

### Analytics Services Comparison

| Service | Type | Use Case |
|---------|------|----------|
| **Kinesis Data Streams** | Real-time Stream | Custom real-time processing |
| **Kinesis Data Firehose** | Near Real-time ETL | Load to S3, Redshift, OpenSearch |
| **Athena** | Interactive Query | SQL on S3 data |
| **Glue** | ETL | Serverless data preparation |
| **EMR** | Big Data Processing | Hadoop ecosystem |
| **OpenSearch** | Search & Analytics | Log analysis, full-text search |
| **QuickSight** | BI & Visualization | Dashboards and insights |
| **MSK** | Managed Kafka | Kafka-compatible streaming |

---

## Exam Focus Areas

1. **SQS**: Standard vs FIFO, visibility timeout, DLQ
2. **SNS**: Pub/sub, fan-out with SQS
3. **Kinesis**: Data Streams vs Firehose differences
4. **Step Functions**: Workflow orchestration, Standard vs Express
5. **EventBridge**: Event-driven architecture, event patterns
6. **Athena**: SQL on S3, partitioning for cost optimization
7. **Glue**: Data Catalog, ETL, crawlers
8. **EMR**: Hadoop ecosystem, node types, Spot Instances
9. **MSK vs Kinesis**: When to use which
10. **Lambda**: Triggers, limits, best practices

---

*Last Updated: January 2026 - SAA-C03 Exam*
