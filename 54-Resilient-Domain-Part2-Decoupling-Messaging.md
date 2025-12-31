# AWS SAA-C03: DESIGN RESILIENT ARCHITECTURES - PART 2

## 📋 DECOUPLING MECHANISMS & MESSAGING

---

# 📨 AMAZON SQS (Simple Queue Service)

## Definition
**Amazon SQS** is a fully managed message queuing service that enables you to decouple and scale microservices, distributed systems, and serverless applications.

---

## Queue Types Comparison

| Feature | Standard Queue | FIFO Queue |
|---------|---------------|------------|
| **Throughput** | Unlimited | 3,000 msg/s (batching), 300 msg/s (without) |
| **Ordering** | Best-effort | Strict FIFO |
| **Delivery** | At-least-once | Exactly-once |
| **Deduplication** | No | Yes (5-minute window) |
| **Naming** | Any | Must end with `.fifo` |
| **Use Case** | High throughput | Order matters, no duplicates |

---

## SQS Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                    SQS MESSAGE LIFECYCLE                             │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   PRODUCER                  SQS QUEUE                   CONSUMER    │
│                                                                      │
│   ┌────────┐              ┌────────────┐              ┌────────┐   │
│   │ App    │─── Send ────▶│            │◀─── Poll ────│ Worker │   │
│   │ Server │   Message    │  Message   │   Messages   │        │   │
│   └────────┘              │  Queue     │              └────────┘   │
│                           │            │                    │       │
│   ┌────────┐              │ ┌────────┐ │                    │       │
│   │ Lambda │─── Send ────▶│ │ Msg 1  │ │              Process       │
│   │        │   Message    │ ├────────┤ │                    │       │
│   └────────┘              │ │ Msg 2  │ │                    ▼       │
│                           │ ├────────┤ │              ┌────────┐   │
│   ┌────────┐              │ │ Msg 3  │ │              │ Delete │   │
│   │ EC2    │─── Send ────▶│ └────────┘ │◀─────────────│ Message│   │
│   │        │   Message    │            │              └────────┘   │
│   └────────┘              └────────────┘                           │
│                                                                      │
│   MESSAGE STATES:                                                    │
│   1. Sent → In Queue (visible)                                      │
│   2. Received → In-flight (invisible during visibility timeout)     │
│   3. Processed → Deleted by consumer                                │
│   4. Not deleted → Returns to queue after visibility timeout        │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Key Concepts

### Visibility Timeout

```
┌─────────────────────────────────────────────────────────────────────┐
│                    VISIBILITY TIMEOUT                                │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   Timeline:                                                          │
│                                                                      │
│   Message      Consumer        Visibility         Message            │
│   Received     Processing      Timeout Expires    Available Again   │
│       │            │                │                  │             │
│   ────●────────────●────────────────●──────────────────●────▶       │
│       │            │                │                  │             │
│       │◀──── Visibility Timeout ────▶                  │             │
│       │     (Default: 30 seconds)   │                  │             │
│       │                             │                  │             │
│       │     Message INVISIBLE       │   Message VISIBLE              │
│       │     to other consumers      │   to other consumers           │
│                                                                      │
│   BEST PRACTICE:                                                     │
│   Set visibility timeout > processing time                           │
│   Use ChangeMessageVisibility to extend if needed                   │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Long Polling vs Short Polling

| Feature | Short Polling | Long Polling |
|---------|---------------|--------------|
| **Behavior** | Returns immediately | Waits up to 20 seconds |
| **Empty Responses** | Many | Fewer |
| **Cost** | Higher (more API calls) | Lower |
| **Latency** | Higher | Lower |
| **Configuration** | Default | `WaitTimeSeconds` = 1-20 |

### Dead Letter Queue (DLQ)

```
┌─────────────────────────────────────────────────────────────────────┐
│                    DEAD LETTER QUEUE                                 │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │                    SOURCE QUEUE                              │   │
│   │   maxReceiveCount = 3                                        │   │
│   │                                                              │   │
│   │   Message received 1st time → Processing fails              │   │
│   │   Message received 2nd time → Processing fails              │   │
│   │   Message received 3rd time → Processing fails              │   │
│   │                            │                                 │   │
│   │                            ▼                                 │   │
│   │            Message moved to DLQ                             │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                            │                                         │
│                            ▼                                         │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │                    DEAD LETTER QUEUE                         │   │
│   │                                                              │   │
│   │   • Analyze failed messages                                  │   │
│   │   • Debug processing issues                                  │   │
│   │   • Set up CloudWatch alarms                                 │   │
│   │   • Redrive to source queue after fixing                     │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Configuration Rules

| Setting | Value/Range | Notes |
|---------|-------------|-------|
| **Message Size** | 1 byte - 256 KB | Use S3 for larger |
| **Retention Period** | 1 minute - 14 days | Default: 4 days |
| **Visibility Timeout** | 0 seconds - 12 hours | Default: 30 seconds |
| **Delay Queue** | 0 seconds - 15 minutes | Delay before visible |
| **Long Poll Wait** | 0 - 20 seconds | 0 = short polling |
| **Receive Messages** | 1 - 10 per request | Batch for efficiency |
| **Message Groups** | FIFO only | For ordering |
| **Deduplication ID** | FIFO only | 5-minute window |

---

## SQS with Auto Scaling

```
┌─────────────────────────────────────────────────────────────────────┐
│                    QUEUE-BASED AUTO SCALING                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │                    CloudWatch Alarm                          │   │
│   │   Metric: ApproximateNumberOfMessagesVisible                │   │
│   │   Threshold: > 1000 messages                                 │   │
│   └───────────────────────────┬─────────────────────────────────┘   │
│                               │                                      │
│                               ▼                                      │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │                    Auto Scaling Group                        │   │
│   │   Target Tracking: BacklogPerInstance                       │   │
│   │                                                              │   │
│   │   BacklogPerInstance = ApproximateNumberOfMessagesVisible   │   │
│   │                        ────────────────────────────────────  │   │
│   │                              Running Capacity                │   │
│   │                                                              │   │
│   │   Target: 100 messages per instance                         │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│   FORMULA for EC2 instances needed:                                  │
│   Instances = Messages in Queue / Acceptable Backlog per Instance   │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Limits

| Limit | Standard | FIFO |
|-------|----------|------|
| Throughput | Unlimited | 3,000 msg/s (batch), 300 msg/s (no batch) |
| In-flight messages | 120,000 | 20,000 |
| Message groups | N/A | Unlimited |
| Queues per account | Unlimited | Unlimited |

---

## Exam Heuristics

| Keyword/Scenario | Answer |
|------------------|--------|
| Decouple components | **SQS** |
| Process messages in order | **SQS FIFO** |
| Exactly-once processing | **SQS FIFO** |
| High throughput queue | **SQS Standard** |
| Handle failed messages | **Dead Letter Queue** |
| Scale workers based on queue | **CloudWatch + ApproximateNumberOfMessages** |
| Reduce API calls | **Long polling (WaitTimeSeconds)** |
| Large message (>256KB) | **SQS + S3 (Extended Client Library)** |
| Delay message delivery | **Delay Queue** |
| Prevent duplicate processing | **SQS FIFO with deduplication** |
| Buffer writes to database | **SQS + Lambda/EC2 workers** |

---

# 📢 AMAZON SNS (Simple Notification Service)

## Definition
**Amazon SNS** is a fully managed pub/sub messaging service for application-to-application (A2A) and application-to-person (A2P) communication.

---

## SNS Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                    SNS PUB/SUB ARCHITECTURE                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│                         PUBLISHER                                    │
│   ┌────────┐  ┌────────┐  ┌────────┐  ┌────────┐                   │
│   │  App   │  │   S3   │  │CloudWatch│ │EventBridge│                │
│   │        │  │ Event  │  │ Alarm   │ │        │                    │
│   └───┬────┘  └───┬────┘  └────┬────┘ └───┬────┘                   │
│       │           │            │          │                         │
│       └───────────┴──────┬─────┴──────────┘                         │
│                          │                                           │
│                          ▼                                           │
│               ┌─────────────────────┐                               │
│               │     SNS TOPIC       │                               │
│               │   (Fan-Out Hub)     │                               │
│               └─────────┬───────────┘                               │
│                         │                                            │
│       ┌─────────────────┼─────────────────┐                         │
│       │         │       │       │         │                         │
│       ▼         ▼       ▼       ▼         ▼                         │
│   ┌───────┐ ┌───────┐ ┌───────┐ ┌───────┐ ┌───────┐               │
│   │  SQS  │ │Lambda │ │ HTTP  │ │ Email │ │  SMS  │               │
│   │ Queue │ │       │ │Endpoint│ │       │ │       │               │
│   └───────┘ └───────┘ └───────┘ └───────┘ └───────┘               │
│                                                                      │
│   SUBSCRIBERS                                                        │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Subscription Types

| Type | Use Case | Delivery |
|------|----------|----------|
| **SQS** | Queue processing | Asynchronous |
| **Lambda** | Serverless processing | Synchronous |
| **HTTP/HTTPS** | Webhooks | Synchronous |
| **Email** | Notifications | Asynchronous |
| **Email-JSON** | Structured notifications | Asynchronous |
| **SMS** | Mobile alerts | Asynchronous |
| **Mobile Push** | App notifications | Asynchronous |
| **Kinesis Firehose** | Data streaming | Asynchronous |

---

## SNS + SQS Fan-Out Pattern

```
┌─────────────────────────────────────────────────────────────────────┐
│                    FAN-OUT PATTERN                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   Order Service                                                      │
│       │                                                              │
│       │ Publish: "Order Created"                                     │
│       ▼                                                              │
│   ┌─────────────────────┐                                           │
│   │     SNS Topic       │                                           │
│   │   "order-events"    │                                           │
│   └─────────┬───────────┘                                           │
│             │                                                        │
│   ┌─────────┼─────────┬─────────┬─────────┐                        │
│   │         │         │         │         │                         │
│   ▼         ▼         ▼         ▼         ▼                         │
│ ┌─────┐  ┌─────┐  ┌─────┐  ┌─────┐  ┌─────┐                        │
│ │SQS  │  │SQS  │  │SQS  │  │SQS  │  │Lambda│                       │
│ │Email│  │Inventory│ │Ship│  │Analytics│ │Fraud│                   │
│ │Queue│  │Queue│  │Queue│  │Queue│  │Check│                        │
│ └──┬──┘  └──┬──┘  └──┬──┘  └──┬──┘  └──┬──┘                        │
│    │        │        │        │        │                            │
│    ▼        ▼        ▼        ▼        ▼                            │
│  Email   Inventory Shipping Analytics  Fraud                        │
│  Service  Service  Service  Service   Detection                     │
│                                                                      │
│   BENEFITS:                                                          │
│   ✓ Loose coupling                                                  │
│   ✓ Independent scaling                                             │
│   ✓ Add new subscribers without code changes                        │
│   ✓ Guaranteed delivery to each queue                               │
│   ✓ Different processing speeds per service                         │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Configuration Rules

| Setting | Value | Notes |
|---------|-------|-------|
| **Message Size** | 256 KB | Use S3 for larger |
| **Message Filtering** | JSON policy | Filter by attributes |
| **Delivery Retry** | Configurable | Different per protocol |
| **DLQ Support** | Yes | For failed deliveries |
| **Encryption** | SSE-KMS | At-rest encryption |
| **Access Policy** | JSON | Cross-account, resource-based |

---

## Message Filtering

```
┌─────────────────────────────────────────────────────────────────────┐
│                    MESSAGE FILTERING                                 │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   SNS Topic: order-events                                           │
│                                                                      │
│   Message:                                                           │
│   {                                                                  │
│     "order_type": "express",                                        │
│     "region": "us-east",                                            │
│     "amount": 150                                                   │
│   }                                                                  │
│                                                                      │
│   Subscription Filters:                                              │
│                                                                      │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │ Queue: express-orders                                        │   │
│   │ Filter: {"order_type": ["express"]}                          │   │
│   │ ✓ Receives message                                           │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │ Queue: large-orders                                          │   │
│   │ Filter: {"amount": [{"numeric": [">=", 100]}]}               │   │
│   │ ✓ Receives message                                           │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │ Queue: standard-orders                                       │   │
│   │ Filter: {"order_type": ["standard"]}                         │   │
│   │ ✗ Does NOT receive message                                   │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Exam Heuristics

| Keyword/Scenario | Answer |
|------------------|--------|
| Fan-out to multiple queues | **SNS + SQS** |
| Pub/sub messaging | **SNS** |
| Send to multiple subscribers | **SNS Topic** |
| Email notifications | **SNS (Email)** |
| SMS notifications | **SNS (SMS)** |
| Push notifications | **SNS (Mobile Push)** |
| Filter messages per subscriber | **SNS Message Filtering** |
| Cross-account messaging | **SNS with access policy** |
| Event-driven fanout | **SNS + Lambda/SQS** |

---

# 🔄 AMAZON EVENTBRIDGE

## Definition
**Amazon EventBridge** is a serverless event bus that connects applications using events. It receives events from various sources and routes them to targets based on rules.

---

## EventBridge Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                    EVENTBRIDGE ARCHITECTURE                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   EVENT SOURCES                                                      │
│   ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐          │
│   │  AWS   │ │ Custom │ │  SaaS  │ │Scheduled│ │  API   │          │
│   │Services│ │  Apps  │ │Partners│ │  Rules  │ │Dest.   │          │
│   └───┬────┘ └───┬────┘ └───┬────┘ └───┬────┘ └───┬────┘          │
│       │          │          │          │          │                 │
│       └──────────┴──────────┴──────────┴──────────┘                 │
│                          │                                           │
│                          ▼                                           │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │                    EVENT BUS                                 │   │
│   │                                                              │   │
│   │   ┌───────────────────────────────────────────────────────┐ │   │
│   │   │                      RULES                             │ │   │
│   │   │                                                        │ │   │
│   │   │  Rule 1: source = "aws.ec2" AND detail-type = "stop"  │ │   │
│   │   │  Rule 2: source = "custom.orders" AND $.status = "new"│ │   │
│   │   │  Rule 3: Schedule: rate(5 minutes)                     │ │   │
│   │   └───────────────────────────────────────────────────────┘ │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                          │                                           │
│       ┌──────────────────┼──────────────────┐                       │
│       │         │        │        │         │                        │
│       ▼         ▼        ▼        ▼         ▼                        │
│   ┌───────┐ ┌───────┐ ┌───────┐ ┌───────┐ ┌───────┐               │
│   │Lambda │ │  SQS  │ │  SNS  │ │Step   │ │ API   │               │
│   │       │ │       │ │       │ │Funct. │ │Gateway│               │
│   └───────┘ └───────┘ └───────┘ └───────┘ └───────┘               │
│                                                                      │
│   TARGETS (150+ integrations)                                       │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Event Buses

| Type | Description | Use Case |
|------|-------------|----------|
| **Default** | AWS service events | React to AWS events |
| **Custom** | Application events | Custom event routing |
| **Partner** | SaaS partner events | Third-party integrations |
| **Cross-Account** | Events from other accounts | Multi-account architecture |

---

## Event Pattern Examples

```json
// EC2 Instance State Change
{
  "source": ["aws.ec2"],
  "detail-type": ["EC2 Instance State-change Notification"],
  "detail": {
    "state": ["stopped", "terminated"]
  }
}

// Custom Order Event
{
  "source": ["custom.orders"],
  "detail-type": ["Order Placed"],
  "detail": {
    "status": ["pending"],
    "amount": [{"numeric": [">=", 100]}]
  }
}
```

---

## Schedule Expressions

| Type | Format | Example |
|------|--------|---------|
| **Rate** | rate(value unit) | `rate(5 minutes)` |
| **Cron** | cron(min hr day mon dow yr) | `cron(0 12 * * ? *)` |

---

## Exam Heuristics

| Keyword/Scenario | Answer |
|------------------|--------|
| React to AWS service events | **EventBridge** |
| Schedule Lambda execution | **EventBridge Scheduled Rule** |
| Event-driven architecture | **EventBridge** |
| SaaS integration (Zendesk, Datadog) | **EventBridge Partner Events** |
| Cross-account events | **EventBridge with resource policy** |
| Complex event routing | **EventBridge Rules** |
| Archive and replay events | **EventBridge Archive** |
| Schema discovery | **EventBridge Schema Registry** |

---

# 🔁 AWS STEP FUNCTIONS

## Definition
**AWS Step Functions** is a serverless orchestration service that lets you coordinate multiple AWS services into serverless workflows (state machines).

---

## Step Functions Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                    STEP FUNCTIONS STATE MACHINE                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │   Order Processing Workflow                                  │   │
│   │                                                              │   │
│   │   ┌─────────────┐                                           │   │
│   │   │   Start     │                                           │   │
│   │   └──────┬──────┘                                           │   │
│   │          │                                                   │   │
│   │          ▼                                                   │   │
│   │   ┌─────────────┐     ┌─────────────┐                       │   │
│   │   │  Validate   │────▶│   Choice    │                       │   │
│   │   │   Order     │     │   (Branch)  │                       │   │
│   │   │  (Lambda)   │     └──────┬──────┘                       │   │
│   │   └─────────────┘            │                               │   │
│   │                     ┌────────┴────────┐                      │   │
│   │                     ▼                 ▼                      │   │
│   │              ┌──────────┐      ┌──────────┐                 │   │
│   │              │ Process  │      │  Reject  │                 │   │
│   │              │ Payment  │      │  Order   │                 │   │
│   │              │ (Lambda) │      │  (Fail)  │                 │   │
│   │              └────┬─────┘      └──────────┘                 │   │
│   │                   │                                          │   │
│   │                   ▼                                          │   │
│   │   ┌─────────────────────────────────────┐                   │   │
│   │   │           Parallel                   │                   │   │
│   │   │   ┌──────────┐  ┌──────────┐       │                   │   │
│   │   │   │ Update   │  │  Send    │       │                   │   │
│   │   │   │Inventory │  │ Notif.   │       │                   │   │
│   │   │   │(Lambda)  │  │ (SNS)    │       │                   │   │
│   │   │   └──────────┘  └──────────┘       │                   │   │
│   │   └───────────────────┬─────────────────┘                   │   │
│   │                       │                                      │   │
│   │                       ▼                                      │   │
│   │              ┌──────────────┐                                │   │
│   │              │    Wait      │                                │   │
│   │              │ (30 seconds) │                                │   │
│   │              └──────┬───────┘                                │   │
│   │                     │                                         │   │
│   │                     ▼                                         │   │
│   │              ┌──────────────┐                                │   │
│   │              │   Ship       │                                │   │
│   │              │   Order      │                                │   │
│   │              │  (Lambda)    │                                │   │
│   │              └──────┬───────┘                                │   │
│   │                     │                                         │   │
│   │                     ▼                                         │   │
│   │              ┌──────────────┐                                │   │
│   │              │    End       │                                │   │
│   │              │  (Success)   │                                │   │
│   │              └──────────────┘                                │   │
│   │                                                              │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## State Types

| State | Description | Use Case |
|-------|-------------|----------|
| **Task** | Execute work (Lambda, Activity) | Call a function |
| **Choice** | Branch based on conditions | If/else logic |
| **Parallel** | Execute branches concurrently | Parallel processing |
| **Map** | Iterate over array | Process each item |
| **Wait** | Delay execution | Time-based waits |
| **Pass** | Pass input to output | Transform data |
| **Succeed** | End successfully | Success terminal |
| **Fail** | End with failure | Failure terminal |

---

## Workflow Types

| Type | Duration | Execution | Use Case |
|------|----------|-----------|----------|
| **Standard** | Up to 1 year | Exactly-once | Long-running |
| **Express** | Up to 5 minutes | At-least-once | High-volume, short |

### Express Workflow Types

| Subtype | Behavior | Use Case |
|---------|----------|----------|
| **Synchronous** | Wait for completion | API orchestration |
| **Asynchronous** | Fire and forget | Event processing |

---

## Integration Patterns

| Pattern | Description |
|---------|-------------|
| **Request Response** | Call service, wait for response |
| **Run a Job (.sync)** | Start job, wait for completion |
| **Wait for Callback** | Send token, wait for callback |

---

## Exam Heuristics

| Keyword/Scenario | Answer |
|------------------|--------|
| Orchestrate multiple Lambda | **Step Functions** |
| Visual workflow | **Step Functions** |
| Long-running workflow | **Step Functions Standard** |
| High-volume short workflow | **Step Functions Express** |
| Human approval workflow | **Step Functions + Wait for Callback** |
| Parallel processing | **Step Functions Parallel state** |
| Process array of items | **Step Functions Map state** |
| Retry with backoff | **Step Functions Retry configuration** |
| Error handling workflow | **Step Functions Catch/Retry** |
| Saga pattern | **Step Functions** |

---

# 🏭 DECOUPLING COMPARISON

## SQS vs SNS vs EventBridge vs Step Functions

| Feature | SQS | SNS | EventBridge | Step Functions |
|---------|-----|-----|-------------|----------------|
| **Pattern** | Queue | Pub/Sub | Event Bus | Orchestration |
| **Consumers** | Pull | Push | Push | N/A |
| **Persistence** | Yes (14 days) | No | Archive optional | Execution history |
| **Ordering** | FIFO available | No | No | Sequential |
| **Filtering** | No | Yes | Yes | Choice state |
| **Retry** | Automatic | Configurable | Configurable | Configurable |
| **Use Case** | Buffering | Broadcasting | Routing | Workflows |

---

## When to Use What

```
┌─────────────────────────────────────────────────────────────────────┐
│                    DECOUPLING DECISION TREE                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   Need to decouple?                                                  │
│        │                                                             │
│        ▼                                                             │
│   ┌────────────────────────────────────────────────────────────┐    │
│   │ One producer, one consumer?  ──▶  SQS                       │    │
│   │                                                              │    │
│   │ One producer, multiple consumers? ──▶  SNS (+ SQS for each) │    │
│   │                                                              │    │
│   │ React to AWS events? ──▶  EventBridge                       │    │
│   │                                                              │    │
│   │ Complex routing rules? ──▶  EventBridge                     │    │
│   │                                                              │    │
│   │ Coordinate multiple services? ──▶  Step Functions           │    │
│   │                                                              │    │
│   │ Need message persistence? ──▶  SQS                          │    │
│   │                                                              │    │
│   │ Need exactly-once? ──▶  SQS FIFO                           │    │
│   │                                                              │    │
│   │ Need ordering? ──▶  SQS FIFO or Step Functions             │    │
│   │                                                              │    │
│   │ Schedule tasks? ──▶  EventBridge Scheduler                  │    │
│   │                                                              │    │
│   │ Human approval needed? ──▶  Step Functions                  │    │
│   └────────────────────────────────────────────────────────────┘    │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

# 📊 AMAZON MQ

## Definition
**Amazon MQ** is a managed message broker service for Apache ActiveMQ and RabbitMQ that makes it easy to migrate to AWS without rewriting messaging code.

## When to Use

| Scenario | Service |
|----------|---------|
| New cloud-native app | **SQS/SNS** |
| Migrate from on-premises | **Amazon MQ** |
| Need JMS, AMQP, MQTT, STOMP | **Amazon MQ** |
| Need OpenWire protocol | **Amazon MQ** |
| Simple queue/topic | **SQS/SNS** |

## Exam Heuristics

| Keyword/Scenario | Answer |
|------------------|--------|
| Migrate from RabbitMQ | **Amazon MQ (RabbitMQ)** |
| Migrate from ActiveMQ | **Amazon MQ (ActiveMQ)** |
| JMS compatibility | **Amazon MQ** |
| MQTT for IoT | **Amazon MQ or IoT Core** |
| Cloud-native messaging | **SQS/SNS** |

---

# ✅ DECOUPLING EXAM HEURISTICS SUMMARY

| Keyword/Scenario | Answer |
|------------------|--------|
| Buffer between components | **SQS** |
| Decouple microservices | **SQS** |
| Fan-out | **SNS + SQS** |
| Event-driven architecture | **EventBridge** |
| React to AWS events | **EventBridge** |
| Workflow orchestration | **Step Functions** |
| Process in order | **SQS FIFO** |
| Exactly-once processing | **SQS FIFO** |
| At-least-once delivery | **SQS Standard or SNS** |
| Multiple subscribers | **SNS Topic** |
| Filter messages | **SNS Filtering or EventBridge Rules** |
| Schedule tasks | **EventBridge Scheduler** |
| Human approval | **Step Functions + Callback** |
| Long-running process | **Step Functions Standard** |
| High-volume events | **Step Functions Express** |
| Retry with backoff | **Step Functions or SQS** |
| Dead letter handling | **SQS DLQ or SNS DLQ** |
| Migrate from RabbitMQ/ActiveMQ | **Amazon MQ** |

---

*Continued in Part 3: Disaster Recovery Strategies*

---

*Last Updated: December 2025*
*Exam Version: SAA-C03*
*Domain: Design Resilient Architectures (26%)*
