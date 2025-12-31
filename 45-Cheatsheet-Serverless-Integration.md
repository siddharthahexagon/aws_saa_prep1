# AWS SAA-C03 CHEATSHEET: SERVERLESS & APPLICATION SERVICES

## 📋 PRINT-READY EXAM REFERENCE

---

# ⚡ AWS LAMBDA

## Definition
Serverless compute service that runs code in response to events.

## Lambda Limits

| Limit | Value |
|-------|-------|
| **Memory** | 128 MB - 10,240 MB |
| **Timeout** | 15 minutes max |
| **Environment variables** | 4 KB |
| **Deployment package** | 50 MB (zipped), 250 MB (unzipped) |
| **Container image** | 10 GB |
| **/tmp storage** | 512 MB - 10,240 MB |
| **Concurrent executions** | 1,000 default (soft limit) |

## Lambda Invocation Types

| Type | Description | Use Case |
|------|-------------|----------|
| **Synchronous** | Wait for response | API Gateway |
| **Asynchronous** | Don't wait | S3, SNS, EventBridge |
| **Event Source Mapping** | Poll-based | SQS, Kinesis, DynamoDB |

## Lambda Event Sources

### Synchronous
- API Gateway
- ALB
- CloudFront (Lambda@Edge)
- Cognito
- Step Functions

### Asynchronous
- S3
- SNS
- EventBridge
- SES
- CloudFormation
- CodeCommit
- IoT

### Event Source Mapping (Poll)
- SQS / SQS FIFO
- Kinesis Data Streams
- DynamoDB Streams
- Amazon MQ / MSK

## Lambda Networking

| Configuration | Description |
|--------------|-------------|
| **Default** | AWS-managed network, internet access |
| **VPC** | Access VPC resources, need NAT for internet |

### VPC Lambda
- Define VPC, subnets, security groups
- Uses ENI (Elastic Network Interface)
- Cold start may be longer
- **Internet**: Need NAT Gateway + route

## Lambda Concurrency

| Type | Description |
|------|-------------|
| **Unreserved** | Shared pool |
| **Reserved** | Guaranteed for function |
| **Provisioned** | Pre-initialized instances |

### Provisioned Concurrency
- Eliminate cold starts
- Pay for provisioned capacity
- Good for production traffic

## Lambda Versions & Aliases

| Concept | Description |
|---------|-------------|
| **$LATEST** | Mutable, current code |
| **Version** | Immutable snapshot |
| **Alias** | Pointer to version |
| **Weighted alias** | Traffic shifting |

## Lambda Layers

| Feature | Description |
|---------|-------------|
| **Purpose** | Share code/dependencies |
| **Limit** | 5 layers per function |
| **Size** | 250 MB total unzipped |
| **Use case** | Common libraries, SDKs |

## Lambda Destinations

| Type | Description |
|------|-------------|
| **On Success** | Route successful results |
| **On Failure** | Route failures |
| **Destinations** | SQS, SNS, Lambda, EventBridge |

## Lambda with RDS

| Challenge | Solution |
|-----------|----------|
| **Connection exhaustion** | Use RDS Proxy |
| **Cold start connections** | RDS Proxy pooling |
| **IAM auth** | RDS Proxy + IAM |

---

# 🚪 AMAZON API GATEWAY

## Definition
Managed service to create, publish, and secure APIs at any scale.

## API Gateway Types

| Type | Protocol | Use Case |
|------|----------|----------|
| **REST API** | HTTP | Full features |
| **HTTP API** | HTTP | Lower cost, lower latency |
| **WebSocket API** | WebSocket | Real-time, chat |

### REST vs HTTP API

| Feature | REST API | HTTP API |
|---------|----------|----------|
| **Cost** | Higher | 70% lower |
| **Latency** | Higher | Lower |
| **Usage plans** | Yes | No |
| **API keys** | Yes | Yes |
| **WAF** | Yes | No |
| **Resource policies** | Yes | No |
| **Caching** | Yes | No |

## API Gateway Integrations

| Integration | Description |
|-------------|-------------|
| **Lambda** | Invoke Lambda function |
| **HTTP** | Proxy to HTTP endpoint |
| **AWS Service** | Call any AWS service |
| **Mock** | Return static response |
| **VPC Link** | Access private resources |

## API Gateway Security

| Method | Description |
|--------|-------------|
| **IAM** | AWS IAM policies |
| **Lambda Authorizer** | Custom auth logic |
| **Cognito** | User pool authorizer |
| **API Keys** | Usage tracking |
| **Resource Policies** | Cross-account access |

### Lambda Authorizer Types

| Type | Token | Request |
|------|-------|---------|
| **Input** | Bearer token | Headers, query strings |
| **Caching** | Yes | Yes |
| **Use case** | OAuth, JWT | Custom headers |

## API Gateway Caching

| Setting | Description |
|---------|-------------|
| **TTL** | 0-3600 seconds (default 300) |
| **Size** | 0.5 GB - 237 GB |
| **Stage-level** | Per stage configuration |
| **Invalidation** | Header `Cache-Control: max-age=0` |

## API Gateway Throttling

| Limit | Default |
|-------|---------|
| **Account-level** | 10,000 requests/second |
| **Stage-level** | Configurable |
| **Method-level** | Configurable |
| **Burst limit** | 5,000 requests |

## Usage Plans & API Keys

| Feature | Description |
|---------|-------------|
| **Usage Plans** | Rate limits per customer |
| **API Keys** | Identify customers |
| **Throttling** | Per key limits |
| **Quotas** | Monthly request limits |

---

# 📨 AMAZON SQS (SIMPLE QUEUE SERVICE)

## Definition
Fully managed message queuing service.

## SQS Queue Types

| Type | Description | Use Case |
|------|-------------|----------|
| **Standard** | At-least-once, best-effort order | High throughput |
| **FIFO** | Exactly-once, ordered | Order matters |

### Standard vs FIFO

| Feature | Standard | FIFO |
|---------|----------|------|
| **Throughput** | Unlimited | 3,000 msg/s (batching) |
| **Order** | Best effort | Guaranteed |
| **Delivery** | At-least-once | Exactly-once |
| **Deduplication** | No | 5-min window |
| **Suffix** | None | `.fifo` required |

## SQS Settings

| Setting | Description | Default |
|---------|-------------|---------|
| **Visibility Timeout** | Message hidden time | 30 seconds |
| **Message Retention** | How long messages kept | 4 days (max 14) |
| **Max Message Size** | Maximum size | 256 KB |
| **Delivery Delay** | Delay before visible | 0 (max 15 min) |
| **Long Polling** | Wait for messages | 0 (max 20 sec) |
| **Dead Letter Queue** | Failed messages | Optional |

## SQS Long Polling

| Feature | Description |
|---------|-------------|
| **Purpose** | Reduce API calls |
| **Wait time** | 1-20 seconds |
| **Setting** | `ReceiveMessageWaitTimeSeconds` |
| **Benefit** | Lower cost, lower latency |

## SQS Dead Letter Queue (DLQ)

| Feature | Description |
|---------|-------------|
| **Purpose** | Capture failed messages |
| **MaxReceiveCount** | Failures before DLQ |
| **Redrive** | Move back to source queue |
| **Best practice** | Set retention to 14 days |

## SQS with Lambda

| Feature | Description |
|---------|-------------|
| **Event source mapping** | Lambda polls SQS |
| **Batch size** | 1-10,000 messages |
| **Batch window** | 0-300 seconds |
| **Concurrency** | Scale with queue depth |

## SQS Temporary Queues

| Feature | Description |
|---------|-------------|
| **Purpose** | Request-response patterns |
| **Lifecycle** | Auto-delete when idle |
| **Virtual queues** | Single queue, multiple clients |

---

# 📢 AMAZON SNS (SIMPLE NOTIFICATION SERVICE)

## Definition
Fully managed pub/sub messaging service.

## SNS Concepts

| Concept | Description |
|---------|-------------|
| **Topic** | Communication channel |
| **Publisher** | Sends messages |
| **Subscriber** | Receives messages |
| **Message** | Up to 256 KB |

## SNS Subscribers

| Subscriber | Description |
|------------|-------------|
| **SQS** | Queue messages |
| **Lambda** | Invoke function |
| **HTTP/S** | Webhook endpoint |
| **Email** | Email notification |
| **SMS** | Text message |
| **Mobile Push** | iOS, Android |
| **Kinesis Firehose** | Stream to S3 |

## SNS Topic Types

| Type | Description |
|------|-------------|
| **Standard** | Best-effort ordering |
| **FIFO** | Ordered, deduplication |

### FIFO SNS
- Only SQS FIFO subscribers
- 300 msg/s or 10 MB/s
- Exactly-once delivery
- Suffix: `.fifo`

## SNS Features

| Feature | Description |
|---------|-------------|
| **Message filtering** | Filter by attributes |
| **Fanout** | Send to multiple subscribers |
| **Raw delivery** | Skip SNS wrapper |
| **Encryption** | KMS encryption |
| **DLQ** | For failed Lambda deliveries |

## SNS + SQS Fanout

| Pattern | Description |
|---------|-------------|
| **Fanout** | SNS → Multiple SQS queues |
| **Use case** | Parallel processing |
| **Benefit** | Decoupled, scalable |

---

# 📊 AMAZON KINESIS

## Definition
Real-time data streaming services.

## Kinesis Services

| Service | Description | Use Case |
|---------|-------------|----------|
| **Data Streams** | Real-time streaming | Real-time analytics |
| **Data Firehose** | Load streaming data | ETL to destinations |
| **Data Analytics** | SQL on streams | Real-time SQL |
| **Video Streams** | Video streaming | Video analytics |

## Kinesis Data Streams

| Feature | Description |
|---------|-------------|
| **Shards** | Capacity units |
| **Retention** | 24 hours - 365 days |
| **Ordering** | Per shard |
| **Consumers** | Multiple |
| **Replay** | Yes |

### Shard Capacity

| Metric | Capacity |
|--------|----------|
| **Write** | 1,000 records/s or 1 MB/s |
| **Read** | 2,000 records/s or 2 MB/s |

### Consumer Types

| Type | Description |
|------|-------------|
| **Shared** | 2 MB/s per shard (shared) |
| **Enhanced** | 2 MB/s per consumer per shard |

## Kinesis Data Firehose

| Feature | Description |
|---------|-------------|
| **Serverless** | No shards to manage |
| **Destinations** | S3, Redshift, OpenSearch, HTTP |
| **Near real-time** | 60-900 seconds buffer |
| **Transform** | Lambda transformation |
| **Compression** | GZIP, ZIP, Snappy |

### Firehose vs Streams

| Feature | Firehose | Streams |
|---------|----------|---------|
| **Managed** | Fully | Partial |
| **Real-time** | Near (60s) | Yes (70ms) |
| **Replay** | No | Yes |
| **Destinations** | Limited | Custom |
| **Transform** | Lambda | Consumer code |

---

# 🔄 AWS STEP FUNCTIONS

## Definition
Orchestrate serverless workflows as state machines.

## Step Functions Features

| Feature | Description |
|---------|-------------|
| **Visual workflow** | Design in console |
| **State types** | Task, Choice, Wait, Parallel, etc. |
| **Error handling** | Retry, catch |
| **Express** | High volume, short duration |
| **Standard** | Long-running workflows |

## Workflow Types

| Type | Duration | Execution |
|------|----------|-----------|
| **Standard** | Up to 1 year | At-most-once |
| **Express** | Up to 5 minutes | At-least-once |

## State Types

| State | Description |
|-------|-------------|
| **Task** | Do work (Lambda, activity) |
| **Choice** | Branching logic |
| **Wait** | Delay execution |
| **Parallel** | Parallel branches |
| **Map** | Iterate over array |
| **Pass** | Pass input to output |
| **Succeed** | End successfully |
| **Fail** | End with failure |

---

# 🔄 AMAZON EVENTBRIDGE

## Definition
Serverless event bus for application integration.

## EventBridge Concepts

| Concept | Description |
|---------|-------------|
| **Event Bus** | Receives events |
| **Rules** | Match events to targets |
| **Targets** | Destinations for matched events |
| **Schema Registry** | Event schemas |
| **Scheduler** | Scheduled events |

## Event Sources

| Source | Description |
|--------|-------------|
| **AWS Services** | 90+ services |
| **Custom** | Your applications |
| **SaaS Partners** | Zendesk, Auth0, etc. |

## Event Targets

| Target | Description |
|--------|-------------|
| **Lambda** | Invoke function |
| **Step Functions** | Start execution |
| **SNS** | Publish message |
| **SQS** | Send message |
| **Kinesis** | Put record |
| **API Gateway** | Invoke API |
| **EC2 Actions** | Stop, terminate |
| **And more** | 15+ targets |

## EventBridge Rules

| Rule Type | Description |
|-----------|-------------|
| **Event Pattern** | Match by content |
| **Schedule** | Cron or rate |

### Event Pattern Example
```json
{
  "source": ["aws.ec2"],
  "detail-type": ["EC2 Instance State-change Notification"],
  "detail": {
    "state": ["terminated"]
  }
}
```

---

# 📨 AMAZON SES (SIMPLE EMAIL SERVICE)

## Definition
Email sending and receiving service.

## SES Features

| Feature | Description |
|---------|-------------|
| **Sending** | Transactional, marketing |
| **Receiving** | Receive and process |
| **Templates** | Email templates |
| **Configuration sets** | Track engagement |
| **Dedicated IPs** | Reputation control |

## SES Use Cases

| Use Case | Description |
|----------|-------------|
| **Transactional** | Order confirmations |
| **Marketing** | Newsletters |
| **Notifications** | Alerts, notifications |

---

# 🎯 SERVERLESS QUICK EXAM HEURISTICS

| Keyword/Scenario | Answer |
|------------------|--------|
| Run code without servers | **Lambda** |
| 15 minute max execution | **Lambda** |
| Longer than 15 minutes | **Step Functions / EC2 / ECS** |
| Create REST API | **API Gateway** |
| Lower cost API | **HTTP API** |
| WebSocket | **API Gateway WebSocket** |
| Decouple applications | **SQS** |
| Message ordering required | **SQS FIFO** |
| Fan-out to multiple services | **SNS** |
| Real-time streaming | **Kinesis Data Streams** |
| Load streaming to S3 | **Kinesis Firehose** |
| Orchestrate Lambda | **Step Functions** |
| Event-driven architecture | **EventBridge** |
| React to AWS events | **EventBridge** |
| Send emails | **SES** |
| Lambda + database | **RDS Proxy** |

---

# 📊 INTEGRATION SERVICE SELECTION

| Pattern | Services |
|---------|----------|
| **Async processing** | SQS + Lambda |
| **Fan-out** | SNS + SQS |
| **Real-time** | Kinesis + Lambda |
| **Event-driven** | EventBridge + Lambda |
| **Workflows** | Step Functions |
| **Request-response** | API Gateway + Lambda |

---

# ✅ EXAM DAY QUICK CHECKS

```
□ Know Lambda limits (memory, timeout, package size)
□ Know Lambda invocation types
□ Know API Gateway types (REST vs HTTP)
□ Know SQS Standard vs FIFO
□ Know SNS fanout pattern
□ Know Kinesis Streams vs Firehose
□ Know Step Functions workflow types
□ Know EventBridge concepts
□ Know service integration patterns
```

---

*Last Updated: December 2025*
*Exam Version: SAA-C03*
