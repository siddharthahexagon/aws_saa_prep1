# AWS SAA-C03: Serverless Architecture Decision Trees

## 🌳 Quick Navigation
- [Serverless Service Selection](#serverless-service-selection)
- [Lambda Decision Trees](#lambda-decision-trees)
- [API Gateway Decision](#api-gateway-decision)
- [Serverless Patterns](#serverless-patterns)
- [Event-Driven Architecture](#event-driven-architecture)
- [Serverless Data Processing](#serverless-data-processing)

---

## Serverless Service Selection

```
START: Building serverless application?
│
├─→ Compute?
│   │
│   ├─→ Event-driven, < 15 minutes?
│   │   └─→ **AWS Lambda**
│   │       ├─→ Triggers: API Gateway, S3, DynamoDB, SQS, SNS, EventBridge
│   │       ├─→ Max execution: 15 minutes
│   │       ├─→ Max memory: 10 GB
│   │       ├─→ Pricing: Per invocation + duration
│   │       └─→ Use case: Event processing, APIs, automation
│   │
│   ├─→ Containers, serverless?
│   │   └─→ **AWS Fargate**
│   │       ├─→ No server management
│   │       ├─→ Works with ECS or EKS
│   │       ├─→ Longer running (no time limit)
│   │       └─→ Use case: Containerized workloads, longer processes
│   │
│   └─→ Workflow orchestration?
│       └─→ **AWS Step Functions**
│           ├─→ Coordinate Lambda, ECS, SNS, SQS
│           ├─→ Visual workflows
│           ├─→ Error handling, retries
│           └─→ Use case: Complex workflows, state machines
│
├─→ API Layer?
│   │
│   ├─→ RESTful or HTTP APIs?
│   │   └─→ **Amazon API Gateway**
│   │       ├─→ HTTP API: Simpler, cheaper ($1/million)
│   │       ├─→ REST API: Full featured ($3.50/million)
│   │       └─→ WebSocket API: Real-time bidirectional
│   │
│   └─→ GraphQL?
│       └─→ **AWS AppSync**
│           ├─→ Managed GraphQL
│           ├─→ Real-time subscriptions
│           └─→ Offline sync
│
├─→ Database?
│   │
│   ├─→ NoSQL, key-value/document?
│   │   └─→ **Amazon DynamoDB**
│   │       ├─→ Serverless (on-demand mode)
│   │       ├─→ Single-digit millisecond latency
│   │       └─→ Global Tables for multi-region
│   │
│   ├─→ Relational?
│   │   └─→ **Aurora Serverless v2**
│   │       ├─→ MySQL or PostgreSQL compatible
│   │       ├─→ Auto-scaling capacity
│   │       └─→ Pay per ACU-second
│   │
│   └─→ Caching?
│       └─→ **ElastiCache Serverless**
│           ├─→ Redis or Memcached
│           └─→ Automatic scaling
│
├─→ Storage?
│   │
│   └─→ Object storage?
│       └─→ **Amazon S3**
│           ├─→ Serverless by nature
│           ├─→ Event notifications to Lambda
│           └─→ Unlimited storage
│
├─→ Messaging & Integration?
│   │
│   ├─→ Queue?
│   │   └─→ **Amazon SQS**
│   │       └─→ Fully serverless, auto-scaling
│   │
│   ├─→ Pub/sub?
│   │   └─→ **Amazon SNS**
│   │       └─→ Fully serverless
│   │
│   └─→ Event routing?
│       └─→ **Amazon EventBridge**
│           ├─→ Serverless event bus
│           └─→ Rules for routing
│
├─→ Analytics?
│   │
│   ├─→ Query S3 data?
│   │   └─→ **Amazon Athena**
│   │       ├─→ Serverless SQL queries
│   │       └─→ Pay per query (per TB scanned)
│   │
│   └─→ ETL?
│       └─→ **AWS Glue**
│           ├─→ Serverless ETL
│           ├─→ Data Catalog
│           └─→ Spark or Python
│
└─→ Serverless Services Summary
    │
    │ Category     │ Serverless Service        │
    │──────────────│───────────────────────────│
    │ Compute      │ Lambda, Fargate           │
    │ API          │ API Gateway, AppSync      │
    │ Database     │ DynamoDB, Aurora Serverless│
    │ Storage      │ S3                        │
    │ Messaging    │ SQS, SNS, EventBridge     │
    │ Orchestration│ Step Functions            │
    │ Analytics    │ Athena, Glue, QuickSight  │
    │ Streaming    │ Kinesis Data Firehose     │
```

---

## Lambda Decision Trees

```
START: Is Lambda appropriate?
│
├─→ Execution Time Check
│   ├─→ < 15 minutes? → ✅ Lambda OK
│   └─→ > 15 minutes? → ❌ Use Fargate/Batch
│
├─→ Memory Check
│   ├─→ < 10 GB? → ✅ Lambda OK
│   └─→ > 10 GB? → ❌ Use Fargate/EC2
│
├─→ Disk Space Check
│   ├─→ < 10 GB (/tmp)? → ✅ Lambda OK
│   └─→ > 10 GB? → ❌ Use EFS or Fargate
│
├─→ Invocation Pattern Check
│   ├─→ Event-driven? → ✅ Lambda ideal
│   ├─→ Sporadic traffic? → ✅ Lambda cost-effective
│   ├─→ Steady high volume 24/7? → Consider Fargate/EC2 (may be cheaper)
│   └─→ Long-running daemon? → ❌ Use EC2/Fargate
│
├─→ Cold Start Tolerance
│   ├─→ Can tolerate 1-3 sec? → ✅ Standard Lambda
│   ├─→ Need < 1 sec? → ✅ Provisioned Concurrency
│   └─→ Need instant always? → Consider always-on compute
│
└─→ Lambda Configuration Decisions
    │
    ├─→ Language Runtime?
    │   ├─→ Node.js, Python, Java, .NET, Go, Ruby → ✅ Native support
    │   └─→ Other languages → Custom runtime or container image
    │
    ├─→ Memory/CPU?
    │   ├─→ Start with 1024 MB
    │   ├─→ Use Lambda Power Tuning to optimize
    │   └─→ More memory = more CPU = faster (often cheaper)
    │
    ├─→ Concurrency?
    │   ├─→ Reserved Concurrency: Guarantee capacity, limit max
    │   ├─→ Provisioned Concurrency: Eliminate cold starts
    │   └─→ Default: Up to 1000 concurrent (soft limit)
    │
    ├─→ Networking?
    │   ├─→ Need VPC access? → VPC Lambda (adds latency)
    │   ├─→ Public internet only? → No VPC needed
    │   └─→ VPC + Internet? → NAT Gateway required
    │
    └─→ Invocation Mode?
        ├─→ Synchronous: Wait for response (API Gateway, direct invoke)
        ├─→ Asynchronous: Fire and forget (S3, SNS, EventBridge)
        └─→ Event Source Mapping: Polling (SQS, Kinesis, DynamoDB)
```

### Lambda Event Sources

```
Lambda Trigger Selection:
│
├─→ Synchronous Invocation (request-response)
│   ├─→ API Gateway
│   ├─→ ALB (Application Load Balancer)
│   ├─→ CloudFront (Lambda@Edge)
│   ├─→ Cognito
│   └─→ Direct SDK invoke
│
├─→ Asynchronous Invocation (fire-and-forget)
│   ├─→ S3 events
│   ├─→ SNS
│   ├─→ EventBridge
│   ├─→ CloudWatch Events
│   ├─→ CloudFormation
│   ├─→ CodePipeline
│   └─→ IoT
│   │
│   └─→ Features:
│       ├─→ Retry: 2 additional attempts
│       ├─→ Dead Letter Queue (DLQ)
│       └─→ Destinations (success/failure)
│
└─→ Event Source Mapping (polling)
    ├─→ SQS
    ├─→ Kinesis Data Streams
    ├─→ DynamoDB Streams
    ├─→ Amazon MQ
    └─→ MSK (Managed Kafka)
    │
    └─→ Features:
        ├─→ Lambda polls the source
        ├─→ Batching (process multiple records)
        ├─→ Concurrency control
        └─→ Error handling (bisect batch, max age)
```

---

## API Gateway Decision

```
START: Which API Gateway type?
│
├─→ Need advanced features (caching, API keys, usage plans)?
│   └─→ **REST API**
│       ├─→ Cost: $3.50/million requests
│       ├─→ Features:
│       │   ├─→ API caching
│       │   ├─→ API keys and usage plans
│       │   ├─→ Request/response validation
│       │   ├─→ Request/response transformation
│       │   ├─→ AWS WAF integration
│       │   └─→ Resource policies
│       └─→ Use case: Complex APIs, monetization
│
├─→ Simple API, cost-sensitive?
│   └─→ **HTTP API**
│       ├─→ Cost: $1.00/million requests (70% cheaper)
│       ├─→ Features:
│       │   ├─→ JWT authorizers
│       │   ├─→ CORS support
│       │   ├─→ OpenID Connect/OAuth 2.0
│       │   └─→ Auto-deployments
│       ├─→ Missing (vs REST API):
│       │   ├─→ No caching
│       │   ├─→ No API keys
│       │   ├─→ No request validation
│       │   └─→ No request transformation
│       └─→ Use case: Serverless backends, microservices
│
├─→ Need bidirectional communication?
│   └─→ **WebSocket API**
│       ├─→ Persistent connections
│       ├─→ Two-way messaging
│       ├─→ Connection management
│       └─→ Use case: Chat, real-time dashboards, gaming
│
├─→ Run code at edge?
│   └─→ **Lambda@Edge** (with CloudFront)
│       ├─→ Run Lambda at CloudFront edge locations
│       ├─→ Triggers:
│       │   ├─→ Viewer Request/Response
│       │   └─→ Origin Request/Response
│       └─→ Use case: A/B testing, header manipulation, redirects
│
└─→ REST vs HTTP API Comparison
    │
    │ Feature           │ REST API    │ HTTP API   │
    │───────────────────│─────────────│────────────│
    │ Cost/million      │ $3.50       │ $1.00      │
    │ Caching           │ Yes         │ No         │
    │ API Keys          │ Yes         │ No         │
    │ Usage Plans       │ Yes         │ No         │
    │ Request Validation│ Yes         │ No         │
    │ Transformation    │ Yes         │ No         │
    │ WAF Integration   │ Yes         │ No         │
    │ JWT Auth          │ Yes         │ Yes        │
    │ Lambda Integration│ Yes         │ Yes        │
    │ Latency           │ Higher      │ Lower      │
```

---

## Serverless Patterns

```
START: Common serverless patterns
│
├─→ Pattern: Serverless Web Application
│   │
│   │ ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   │ │  CloudFront │────▶│     S3      │     │  Static     │
│   │ │    (CDN)    │     │  (Static)   │     │  Content    │
│   │ └─────────────┘     └─────────────┘     └─────────────┘
│   │        │
│   │        ▼
│   │ ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   │ │ API Gateway │────▶│   Lambda    │────▶│  DynamoDB   │
│   │ │   (REST)    │     │  (Backend)  │     │    (DB)     │
│   │ └─────────────┘     └─────────────┘     └─────────────┘
│   │
│   └─→ Components:
│       ├─→ CloudFront + S3: Static hosting
│       ├─→ API Gateway: API endpoints
│       ├─→ Lambda: Business logic
│       ├─→ DynamoDB: Data storage
│       └─→ Cognito: Authentication (optional)
│
├─→ Pattern: Event-Driven Processing
│   │
│   │ ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   │ │     S3      │────▶│   Lambda    │────▶│   Action    │
│   │ │   (Upload)  │     │ (Process)   │     │ (Store/API) │
│   │ └─────────────┘     └─────────────┘     └─────────────┘
│   │
│   └─→ Examples:
│       ├─→ Image upload → Lambda → Resize → Store in S3
│       ├─→ Log upload → Lambda → Process → Store in ES
│       └─→ CSV upload → Lambda → Parse → Insert to DynamoDB
│
├─→ Pattern: Decoupled Microservices
│   │
│   │ ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   │ │  Service A  │────▶│     SQS     │────▶│  Service B  │
│   │ │  (Lambda)   │     │   (Queue)   │     │  (Lambda)   │
│   │ └─────────────┘     └─────────────┘     └─────────────┘
│   │
│   └─→ Benefits:
│       ├─→ Services scale independently
│       ├─→ Buffer for traffic spikes
│       ├─→ Fault isolation
│       └─→ Retry on failure
│
├─→ Pattern: Fan-Out
│   │
│   │                      ┌─────────────┐
│   │                 ┌───▶│   SQS 1     │───▶ Lambda 1
│   │ ┌─────────────┐ │    └─────────────┘
│   │ │     SNS     │─┼───▶ SQS 2 ───▶ Lambda 2
│   │ │   (Topic)   │ │    
│   │ └─────────────┘ └───▶ SQS 3 ───▶ Lambda 3
│   │
│   └─→ Use case:
│       ├─→ One event triggers multiple processes
│       ├─→ Different processing per subscriber
│       └─→ Scale each processor independently
│
├─→ Pattern: Saga (Distributed Transaction)
│   │
│   │ ┌─────────────────────────────────────────────────────┐
│   │ │                  Step Functions                      │
│   │ │  ┌───────┐   ┌───────┐   ┌───────┐   ┌───────────┐  │
│   │ │  │Step 1 │──▶│Step 2 │──▶│Step 3 │──▶│  Complete │  │
│   │ │  └───────┘   └───────┘   └───────┘   └───────────┘  │
│   │ │       │           │           │                      │
│   │ │       ▼           ▼           ▼                      │
│   │ │  Compensate  Compensate  Compensate  (on failure)    │
│   │ └─────────────────────────────────────────────────────┘
│   │
│   └─→ Use case:
│       ├─→ Order processing
│       ├─→ Payment workflows
│       └─→ Multi-step transactions
│
├─→ Pattern: CQRS (Command Query Responsibility Segregation)
│   │
│   │ ┌─────────────┐                   ┌─────────────┐
│   │ │   Write     │──▶ DynamoDB ──▶   │    Read     │
│   │ │   (API)     │      │            │   (API)     │
│   │ └─────────────┘      │            └─────────────┘
│   │                      ▼                   ▲
│   │              DynamoDB Streams ────▶ Read Replica
│   │                                    (OpenSearch, etc.)
│   │
│   └─→ Use case:
│       ├─→ Different read/write models
│       ├─→ High read scalability
│       └─→ Complex queries on read side
│
└─→ Pattern: Scheduled Tasks
    │
    │ ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
    │ │ EventBridge │────▶│   Lambda    │────▶│   Action    │
    │ │ (Schedule)  │     │  (Process)  │     │  (Any)      │
    │ └─────────────┘     └─────────────┘     └─────────────┘
    │
    └─→ Examples:
        ├─→ Daily report generation
        ├─→ Cleanup tasks
        └─→ Scheduled data sync
```

---

## Event-Driven Architecture

```
START: Event-driven design?
│
├─→ Event Bus Selection
│   │
│   ├─→ AWS events + custom + SaaS?
│   │   └─→ **Amazon EventBridge**
│   │       ├─→ AWS service events (100+ services)
│   │       ├─→ Custom events from your apps
│   │       ├─→ SaaS partner events
│   │       ├─→ Schema registry
│   │       ├─→ Archive and replay
│   │       └─→ Use case: Event-driven microservices
│   │
│   └─→ Simple notifications?
│       └─→ **Amazon SNS**
│           ├─→ Pub/sub messaging
│           ├─→ Push to multiple subscribers
│           └─→ Use case: Fanout, notifications
│
├─→ Event Flow Patterns
│   │
│   ├─→ Point-to-Point (Queue)
│   │   │
│   │   │ Producer ──▶ SQS Queue ──▶ Consumer
│   │   │
│   │   └─→ Use case: Decouple services, exactly-once processing
│   │
│   ├─→ Publish-Subscribe (Topic)
│   │   │
│   │   │              ┌──▶ Subscriber 1
│   │   │ Publisher ──▶│──▶ Subscriber 2
│   │   │              └──▶ Subscriber 3
│   │   │
│   │   └─→ Use case: Broadcast events to multiple consumers
│   │
│   └─→ Event Bus (Router)
│       │
│       │                    ┌──▶ Target 1 (rule match)
│       │ Event ──▶ EventBridge ──▶ Target 2 (rule match)
│       │                    └──▶ (no match - filtered)
│       │
│       └─→ Use case: Route events based on content
│
├─→ EventBridge Rule Patterns
│   │
│   ├─→ Match specific event
│   │   │
│   │   │ {
│   │   │   "source": ["aws.s3"],
│   │   │   "detail-type": ["Object Created"],
│   │   │   "detail": {
│   │   │     "bucket": { "name": ["my-bucket"] }
│   │   │   }
│   │   │ }
│   │   │
│   │   └─→ Use case: Trigger on specific S3 bucket
│   │
│   └─→ Schedule expression
│       │
│       │ rate(5 minutes)
│       │ cron(0 12 * * ? *)
│       │
│       └─→ Use case: Scheduled Lambda invocation
│
└─→ Error Handling in Event-Driven
    │
    ├─→ Dead Letter Queue (DLQ)
    │   ├─→ SQS DLQ for failed messages
    │   ├─→ SNS DLQ for failed deliveries
    │   └─→ Lambda DLQ/Destinations for failed invocations
    │
    ├─→ Retry Policies
    │   ├─→ SQS: Visibility timeout (retry same message)
    │   ├─→ Lambda async: 2 automatic retries
    │   └─→ Step Functions: Configurable retries per step
    │
    └─→ Destinations (Lambda)
        ├─→ On Success: Send to SNS, SQS, Lambda, EventBridge
        └─→ On Failure: Send to SNS, SQS, Lambda, EventBridge
```

---

## Serverless Data Processing

```
START: Serverless data processing?
│
├─→ Real-time streaming?
│   │
│   ├─→ Need custom processing?
│   │   └─→ **Kinesis Data Streams + Lambda**
│   │       ├─→ Lambda processes records in batches
│   │       ├─→ Parallel processing by shard
│   │       └─→ Use case: Real-time analytics, transformations
│   │
│   └─→ Just load to data store?
│       └─→ **Kinesis Data Firehose**
│           ├─→ Serverless data delivery
│           ├─→ Destinations: S3, Redshift, OpenSearch
│           ├─→ Optional Lambda transformation
│           └─→ Use case: Log aggregation, ETL to S3
│
├─→ Batch processing?
│   │
│   ├─→ Simple transformation?
│   │   └─→ **S3 + Lambda**
│   │       ├─→ S3 event triggers Lambda
│   │       ├─→ Process file
│   │       └─→ Write results
│   │
│   └─→ Complex ETL?
│       └─→ **AWS Glue**
│           ├─→ Serverless Spark/Python
│           ├─→ Data Catalog (metadata)
│           ├─→ ETL jobs (transform data)
│           └─→ Use case: Large-scale ETL, data lake
│
├─→ Query data in S3?
│   └─→ **Amazon Athena**
│       ├─→ Serverless SQL
│       ├─→ Query S3 directly
│       ├─→ Pay per query
│       └─→ Use case: Ad-hoc analytics, log analysis
│
├─→ Workflow orchestration?
│   └─→ **AWS Step Functions**
│       ├─→ Coordinate multiple Lambda functions
│       ├─→ Handle errors and retries
│       ├─→ Parallel processing
│       └─→ Use case: ETL pipelines, order processing
│
└─→ Data Pipeline Pattern
    │
    │ S3 (Raw) ──▶ Glue ETL ──▶ S3 (Processed) ──▶ Athena/QuickSight
    │     │                            │
    │     └──▶ Glue Catalog ◀──────────┘
    │
    └─→ Components:
        ├─→ S3: Data lake storage
        ├─→ Glue: ETL and catalog
        ├─→ Athena: SQL queries
        └─→ QuickSight: Visualization
```

---

## Exam Scenarios - Quick Answers

### Scenario 1: Build serverless REST API
**Answer**: API Gateway (HTTP or REST API) + Lambda + DynamoDB

### Scenario 2: Process images uploaded to S3
**Answer**: S3 event → Lambda (process) → Store result

### Scenario 3: API needs low latency, eliminate cold starts
**Answer**: Lambda with Provisioned Concurrency

### Scenario 4: Simple API, cost-sensitive, no caching needed
**Answer**: HTTP API (cheaper than REST API)

### Scenario 5: Coordinate multiple Lambda functions
**Answer**: AWS Step Functions

### Scenario 6: Real-time chat application
**Answer**: API Gateway WebSocket API + Lambda + DynamoDB

### Scenario 7: Query S3 data with SQL, serverless
**Answer**: Amazon Athena

### Scenario 8: Schedule Lambda to run every 5 minutes
**Answer**: EventBridge rule with schedule expression

### Scenario 9: Lambda needs to access RDS in VPC
**Answer**: VPC Lambda + RDS Proxy (for connection pooling)

### Scenario 10: Serverless ETL pipeline
**Answer**: S3 → Glue ETL → S3 (processed) → Athena

### Scenario 11: Fan-out events to multiple processors
**Answer**: SNS topic → Multiple SQS queues → Lambda functions

### Scenario 12: Serverless, relational database
**Answer**: Aurora Serverless v2

---

## 🎯 Key Takeaways

### Lambda Best Practices:
- **Memory**: More memory = more CPU = often cheaper
- **Cold starts**: Use Provisioned Concurrency if critical
- **VPC**: Only use if needed (adds latency)
- **Async**: Use destinations for error handling
- **Timeout**: Set appropriate timeout (max 15 min)

### API Gateway:
- **HTTP API**: 70% cheaper, simpler
- **REST API**: Full features (caching, API keys)
- **WebSocket**: Real-time bidirectional

### Serverless Patterns:
- **Web app**: CloudFront + S3 + API Gateway + Lambda + DynamoDB
- **Event-driven**: S3/SNS/EventBridge → Lambda
- **Decoupled**: SQS between services
- **Fan-out**: SNS → Multiple SQS → Lambda

### Event-Driven:
- **EventBridge**: Central event bus, rules-based routing
- **SNS**: Simple pub/sub, fanout
- **SQS**: Queue, decouple, buffer

### Data Processing:
- **Athena**: Query S3 with SQL
- **Glue**: Serverless ETL
- **Kinesis Firehose**: Stream to S3/Redshift

---

*Use these decision trees for serverless architecture questions on the exam!*
