# AWS SAA-C03: DESIGN RESILIENT ARCHITECTURES - PART 4

## 📋 MULTI-TIER ARCHITECTURES, PATTERNS & EXAM SCENARIOS

---

# 🏗️ MULTI-TIER ARCHITECTURE PATTERNS

## Pattern 1: Classic Three-Tier Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                    THREE-TIER WEB APPLICATION                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   TIER 1: PRESENTATION (Web Tier)                                   │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │                    PUBLIC SUBNET                             │   │
│   │                                                              │   │
│   │            ┌─────────────────────────┐                      │   │
│   │            │  Application Load       │                      │   │
│   │            │     Balancer            │                      │   │
│   │            └───────────┬─────────────┘                      │   │
│   │                        │                                     │   │
│   │   ┌───────────┬────────┴────────┬───────────┐               │   │
│   │   ▼           ▼                 ▼           ▼               │   │
│   │ ┌───┐       ┌───┐             ┌───┐       ┌───┐            │   │
│   │ │Web│       │Web│             │Web│       │Web│             │   │
│   │ │ 1 │       │ 2 │             │ 3 │       │ 4 │             │   │
│   │ └───┘       └───┘             └───┘       └───┘            │   │
│   │  AZ-a        AZ-a              AZ-b        AZ-b             │   │
│   │                                                              │   │
│   │  Auto Scaling Group (min: 2, desired: 4, max: 10)           │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                              │                                       │
│                              ▼                                       │
│   TIER 2: APPLICATION (App Tier)                                    │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │                    PRIVATE SUBNET                            │   │
│   │                                                              │   │
│   │            ┌─────────────────────────┐                      │   │
│   │            │  Internal Load Balancer │                      │   │
│   │            └───────────┬─────────────┘                      │   │
│   │                        │                                     │   │
│   │   ┌───────────┬────────┴────────┬───────────┐               │   │
│   │   ▼           ▼                 ▼           ▼               │   │
│   │ ┌───┐       ┌───┐             ┌───┐       ┌───┐            │   │
│   │ │App│       │App│             │App│       │App│             │   │
│   │ │ 1 │       │ 2 │             │ 3 │       │ 4 │             │   │
│   │ └───┘       └───┘             └───┘       └───┘            │   │
│   │  AZ-a        AZ-a              AZ-b        AZ-b             │   │
│   │                                                              │   │
│   │  Auto Scaling Group (min: 2, desired: 4, max: 20)           │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                              │                                       │
│                              ▼                                       │
│   TIER 3: DATA (Database Tier)                                      │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │                    PRIVATE SUBNET (Isolated)                 │   │
│   │                                                              │   │
│   │   ┌─────────────────┐        ┌─────────────────┐           │   │
│   │   │   RDS Primary   │◀──────▶│   RDS Standby   │           │   │
│   │   │     (AZ-a)      │  Sync  │     (AZ-b)      │           │   │
│   │   └─────────────────┘        └─────────────────┘           │   │
│   │                                                              │   │
│   │   ┌─────────────────┐        ┌─────────────────┐           │   │
│   │   │   ElastiCache   │        │   ElastiCache   │           │   │
│   │   │   Node (AZ-a)   │        │   Node (AZ-b)   │           │   │
│   │   └─────────────────┘        └─────────────────┘           │   │
│   │                                                              │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│   RESILIENCE FEATURES:                                              │
│   ✓ Multi-AZ deployment                                             │
│   ✓ Auto Scaling at each tier                                       │
│   ✓ Load balancing for distribution                                 │
│   ✓ RDS Multi-AZ for database HA                                    │
│   ✓ ElastiCache cluster for caching                                 │
│   ✓ Private subnets for security                                    │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Pattern 2: Serverless Resilient Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                    SERVERLESS RESILIENT ARCHITECTURE                 │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   ┌─────────────┐         ┌─────────────────────────────────────┐   │
│   │  Route 53   │────────▶│            CloudFront               │   │
│   │ (DNS + HC)  │         │         (Edge locations)            │   │
│   └─────────────┘         └─────────────────┬───────────────────┘   │
│                                             │                        │
│                    ┌────────────────────────┴────────────────────┐   │
│                    │                                             │   │
│                    ▼                                             ▼   │
│   ┌────────────────────────────┐         ┌─────────────────────────┐│
│   │         S3 Bucket          │         │      API Gateway        ││
│   │    (Static Content)        │         │  (REST/HTTP API)        ││
│   │                            │         │                         ││
│   │  • Multi-AZ by default     │         │  • Multi-AZ by default  ││
│   │  • 99.999999999% durability│         │  • Auto scaling         ││
│   │  • Cross-region replication│         │  • Throttling           ││
│   └────────────────────────────┘         └───────────┬─────────────┘│
│                                                      │               │
│                                                      ▼               │
│                                    ┌─────────────────────────────┐   │
│                                    │         Lambda              │   │
│                                    │                             │   │
│                                    │  • Multi-AZ by default      │   │
│                                    │  • Auto scaling             │   │
│                                    │  • Retry on failure         │   │
│                                    │  • DLQ for failed events    │   │
│                                    └───────────────┬─────────────┘   │
│                                                    │                 │
│                           ┌────────────────────────┴────────────────┐│
│                           │                                         ││
│                           ▼                                         ▼│
│   ┌────────────────────────────┐         ┌─────────────────────────┐│
│   │        DynamoDB            │         │          SQS            ││
│   │                            │         │                         ││
│   │  • Multi-AZ by default     │         │  • Multi-AZ by default  ││
│   │  • Global Tables option    │         │  • DLQ for retries      ││
│   │  • Point-in-time recovery  │         │  • Visibility timeout   ││
│   │  • On-demand capacity      │         │  • Message retention    ││
│   └────────────────────────────┘         └─────────────────────────┘│
│                                                                      │
│   BUILT-IN RESILIENCE:                                              │
│   ✓ All services are inherently Multi-AZ                           │
│   ✓ Auto scaling without configuration                              │
│   ✓ No servers to manage or patch                                   │
│   ✓ Pay per use (cost scales with demand)                           │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Pattern 3: Decoupled Microservices

```
┌─────────────────────────────────────────────────────────────────────┐
│                    DECOUPLED MICROSERVICES                           │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│                           ┌─────────────┐                           │
│                           │     ALB     │                           │
│                           └──────┬──────┘                           │
│                                  │                                   │
│              ┌───────────────────┼───────────────────┐              │
│              │                   │                   │              │
│              ▼                   ▼                   ▼              │
│   ┌──────────────────┐ ┌──────────────────┐ ┌──────────────────┐   │
│   │   Order Service  │ │  Product Service │ │   User Service   │   │
│   │    (ECS/EKS)     │ │    (ECS/EKS)     │ │    (ECS/EKS)     │   │
│   │                  │ │                  │ │                  │   │
│   │  ┌────┐ ┌────┐  │ │  ┌────┐ ┌────┐  │ │  ┌────┐ ┌────┐  │   │
│   │  │ T1 │ │ T2 │  │ │  │ T1 │ │ T2 │  │ │  │ T1 │ │ T2 │  │   │
│   │  └────┘ └────┘  │ │  └────┘ └────┘  │ │  └────┘ └────┘  │   │
│   └────────┬─────────┘ └────────┬─────────┘ └────────┬─────────┘   │
│            │                    │                    │              │
│            │           ┌────────┴────────┐          │              │
│            │           │                 │          │              │
│            ▼           ▼                 ▼          ▼              │
│   ┌────────────┐  ┌────────────┐  ┌────────────┐  ┌────────────┐  │
│   │    SQS     │  │    SQS     │  │    SQS     │  │    SNS     │  │
│   │   Order    │  │  Inventory │  │  Shipping  │  │  Events    │  │
│   │   Queue    │  │   Queue    │  │   Queue    │  │   Topic    │  │
│   └─────┬──────┘  └─────┬──────┘  └─────┬──────┘  └─────┬──────┘  │
│         │               │               │               │          │
│         ▼               ▼               ▼               │          │
│   ┌────────────┐  ┌────────────┐  ┌────────────┐       │          │
│   │  Order     │  │ Inventory  │  │  Shipping  │       │          │
│   │  Processor │  │  Processor │  │  Processor │       │          │
│   │  (Lambda)  │  │  (Lambda)  │  │  (Lambda)  │       │          │
│   └─────┬──────┘  └─────┬──────┘  └─────┬──────┘       │          │
│         │               │               │               │          │
│         ▼               ▼               ▼               ▼          │
│   ┌────────────┐  ┌────────────┐  ┌────────────┐  ┌────────────┐  │
│   │  DynamoDB  │  │  DynamoDB  │  │  DynamoDB  │  │   Lambda   │  │
│   │   Orders   │  │  Inventory │  │  Shipments │  │  Analytics │  │
│   └────────────┘  └────────────┘  └────────────┘  └────────────┘  │
│                                                                      │
│   RESILIENCE FEATURES:                                              │
│   ✓ Services are independent (failure isolation)                    │
│   ✓ Queues buffer traffic spikes                                    │
│   ✓ Async processing with retries                                   │
│   ✓ Each service can scale independently                            │
│   ✓ DLQs for failed message handling                                │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Pattern 4: Global Application (Multi-Region)

```
┌─────────────────────────────────────────────────────────────────────┐
│                    GLOBAL MULTI-REGION APPLICATION                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│                    ┌───────────────────────────┐                    │
│                    │         Route 53          │                    │
│                    │   (Latency + Health Check)│                    │
│                    └─────────────┬─────────────┘                    │
│                                  │                                   │
│           ┌──────────────────────┴──────────────────────┐           │
│           │                                             │           │
│           ▼                                             ▼           │
│   ┌───────────────────────────┐       ┌───────────────────────────┐│
│   │      US-EAST-1            │       │      EU-WEST-1            ││
│   │                           │       │                           ││
│   │  ┌─────────────────────┐ │       │  ┌─────────────────────┐  ││
│   │  │     CloudFront      │ │       │  │     CloudFront      │  ││
│   │  │   (Edge Caching)    │ │       │  │   (Edge Caching)    │  ││
│   │  └──────────┬──────────┘ │       │  └──────────┬──────────┘  ││
│   │             │            │       │             │              ││
│   │  ┌──────────▼──────────┐ │       │  ┌──────────▼──────────┐  ││
│   │  │   Application (ASG) │ │       │  │   Application (ASG) │  ││
│   │  │   + Load Balancer   │ │       │  │   + Load Balancer   │  ││
│   │  └──────────┬──────────┘ │       │  └──────────┬──────────┘  ││
│   │             │            │       │             │              ││
│   │  ┌──────────▼──────────┐ │       │  ┌──────────▼──────────┐  ││
│   │  │   Aurora Global     │◀┼───────┼─▶│   Aurora Global     │  ││
│   │  │   (Primary)         │ │  <1s  │  │   (Secondary)       │  ││
│   │  └─────────────────────┘ │Replica│  └─────────────────────┘  ││
│   │                           │       │                           ││
│   │  ┌─────────────────────┐ │       │  ┌─────────────────────┐  ││
│   │  │  DynamoDB Global    │◀┼───────┼─▶│  DynamoDB Global    │  ││
│   │  │   Table             │ │Active │  │   Table             │  ││
│   │  └─────────────────────┘ │Active │  └─────────────────────┘  ││
│   │                           │       │                           ││
│   │  ┌─────────────────────┐ │       │  ┌─────────────────────┐  ││
│   │  │    S3 + CRR         │◀┼───────┼─▶│    S3 + CRR         │  ││
│   │  └─────────────────────┘ │       │  └─────────────────────┘  ││
│   │                           │       │                           ││
│   └───────────────────────────┘       └───────────────────────────┘│
│                                                                      │
│   GLOBAL RESILIENCE:                                                │
│   ✓ Users routed to closest region (latency-based)                  │
│   ✓ Automatic failover if region unhealthy                          │
│   ✓ Data replicated across regions                                  │
│   ✓ Near-zero RPO with global tables/Aurora Global                  │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

# 📝 EXAM SCENARIO QUESTIONS

## Scenario 1: E-Commerce Peak Traffic

**Question:** An e-commerce company experiences 10x traffic during Black Friday sales. The current architecture uses EC2 instances behind an ALB with RDS MySQL. The database becomes the bottleneck. What solution provides the best resilience and performance?

**Options:**
A. Upgrade RDS instance size
B. Add RDS Read Replicas + ElastiCache + Auto Scaling
C. Replace RDS with DynamoDB
D. Add more EC2 instances

**Answer: B**

**Explanation:**
- **B is correct**: Multi-pronged approach:
  - Read Replicas offload read traffic from primary
  - ElastiCache reduces database hits
  - Auto Scaling handles compute scaling
- A is partial: Vertical scaling has limits
- C is wrong: Migration during peak is risky, may require code changes
- D is wrong: More EC2 doesn't help if database is bottleneck

---

## Scenario 2: Global Application Latency

**Question:** A company has users worldwide but their application is only in us-east-1. Users in Asia report slow response times. What's the most effective solution?

**Options:**
A. Add CloudFront for static content
B. Deploy application to ap-southeast-1 with Route 53 latency routing
C. Increase EC2 instance sizes
D. Use Global Accelerator

**Answer: B**

**Explanation:**
- **B is correct**: Multi-region deployment with latency-based routing routes users to nearest region
- A is partial: Only helps static content, not dynamic requests
- C is wrong: Doesn't address network latency
- D is partial: Improves routing but still single region

**Full Solution:**
- Deploy to ap-southeast-1
- Aurora Global Database for data sync
- Route 53 latency-based routing
- CloudFront for static content

---

## Scenario 3: Decoupling Order Processing

**Question:** An order processing system directly calls inventory and shipping services. When shipping service is down, orders fail. How should the architecture be improved?

**Options:**
A. Add retry logic to order service
B. Use SQS to decouple services
C. Increase timeout values
D. Add load balancer between services

**Answer: B**

**Explanation:**
- **B is correct**: SQS provides:
  - Decoupling (orders persist even if downstream fails)
  - Retry capability (messages remain until processed)
  - Buffer for traffic spikes
- A is partial: Retries help but don't prevent data loss
- C is wrong: Doesn't solve the fundamental coupling
- D is wrong: Load balancer doesn't help if service is completely down

---

## Scenario 4: Database Failover

**Question:** A company requires their database to automatically failover with minimal data loss. RPO must be under 1 second and RTO under 1 minute. The database is MySQL.

**Options:**
A. RDS MySQL with Read Replicas
B. RDS MySQL Multi-AZ
C. Aurora MySQL with Aurora Replicas
D. Aurora Global Database

**Answer: C**

**Explanation:**
- **C is correct**: Aurora provides:
  - RPO: Virtually zero (shared storage)
  - RTO: Under 30 seconds
  - Up to 15 replicas
  - Automatic failover
- A is wrong: Read replicas have async replication (data loss possible)
- B is wrong: RTO is 60-120 seconds
- D is overkill: For same-region, Aurora replicas are sufficient

---

## Scenario 5: Handling Traffic Spikes

**Question:** A news website experiences unpredictable traffic spikes when breaking news occurs. The current architecture cannot scale fast enough. What's the best solution?

**Options:**
A. Pre-provision extra capacity
B. Use predictive scaling
C. CloudFront + S3 for static content + Lambda for dynamic + DynamoDB On-Demand
D. Increase Auto Scaling maximum

**Answer: C**

**Explanation:**
- **C is correct**: Serverless scales instantly:
  - CloudFront absorbs traffic at edge
  - S3 for static content (infinitely scalable)
  - Lambda scales to thousands concurrently
  - DynamoDB On-Demand scales automatically
- A is wrong: Wastes money during normal traffic
- B is wrong: Predictive doesn't work for unpredictable spikes
- D is partial: EC2 takes minutes to launch

---

## Scenario 6: Zero-Downtime Deployment

**Question:** A company needs to deploy updates without any downtime. They currently use EC2 with Auto Scaling and ALB. What deployment strategy should they use?

**Options:**
A. Rolling deployment
B. Blue-green deployment with ALB weighted target groups
C. In-place deployment with downtime window
D. Immutable deployment

**Answer: B**

**Explanation:**
- **B is correct**: Blue-green with weighted target groups:
  - Deploy new version to new target group
  - Gradually shift traffic (10% → 50% → 100%)
  - Instant rollback by shifting back
  - Zero downtime
- A is wrong: Rolling can have mixed versions serving traffic
- C is wrong: Has downtime
- D is partial: Better than rolling but blue-green is best

---

## Scenario 7: Message Processing Failure

**Question:** A Lambda function processes SQS messages but occasionally fails. Failed messages are being processed repeatedly, causing delays. What should be implemented?

**Options:**
A. Increase Lambda timeout
B. Increase visibility timeout and configure Dead Letter Queue
C. Use FIFO queue
D. Increase Lambda memory

**Answer: B**

**Explanation:**
- **B is correct**:
  - Visibility timeout > processing time prevents duplicate processing
  - DLQ captures failed messages after X retries
  - Failed messages can be analyzed and reprocessed
- A is wrong: If processing fails, timeout doesn't help
- C is wrong: FIFO is for ordering, not failure handling
- D is wrong: Memory doesn't fix processing failures

---

## Scenario 8: Cross-Region Disaster Recovery

**Question:** A company needs DR in another region with RPO of 15 minutes and RTO of 1 hour. They use RDS PostgreSQL and S3. Budget is limited.

**Options:**
A. Multi-Site Active-Active
B. Warm Standby
C. Pilot Light
D. Backup and Restore

**Answer: C**

**Explanation:**
- **C is correct**: Pilot Light matches requirements:
  - RDS Read Replica (continuous replication, RPO < 15 min) ✓
  - S3 CRR (continuous replication) ✓
  - RTO ~10-30 minutes (faster than required 1 hour) ✓
  - Lower cost than warm standby
- A is wrong: Overkill, expensive
- B is wrong: More expensive than needed
- D is wrong: RPO would be hours, not 15 minutes

---

# 🎯 RESILIENCE DOMAIN INSTANT ANSWERS

## High Availability

| Question Pattern | Answer |
|------------------|--------|
| Web tier HA | **ALB + Auto Scaling across AZs** |
| Database HA | **RDS Multi-AZ** |
| Cache HA | **ElastiCache Redis Cluster Mode** |
| DNS HA | **Route 53 (100% SLA)** |
| NAT HA | **NAT Gateway per AZ** |
| Session persistence | **ElastiCache/DynamoDB for sessions** |

## Scaling

| Question Pattern | Answer |
|------------------|--------|
| Scale based on CPU | **Target Tracking (CPU)** |
| Scale based on requests | **Target Tracking (ALBRequestCount)** |
| Scale at specific time | **Scheduled Scaling** |
| Scale based on queue depth | **Target Tracking (custom metric)** |
| Predictable patterns | **Predictive Scaling** |
| Instant scaling | **Serverless (Lambda, Fargate, DynamoDB On-Demand)** |

## Decoupling

| Question Pattern | Answer |
|------------------|--------|
| Buffer between services | **SQS** |
| Fan-out to multiple | **SNS + SQS** |
| React to AWS events | **EventBridge** |
| Orchestrate workflow | **Step Functions** |
| Order matters | **SQS FIFO** |

## Disaster Recovery

| Question Pattern | Answer |
|------------------|--------|
| Lowest cost DR | **Backup and Restore** |
| RTO 10-30 min | **Pilot Light** |
| RTO minutes | **Warm Standby** |
| RTO near-zero | **Multi-Site Active-Active** |
| Cross-region DB | **Read Replica / Aurora Global** |
| Global active-active DB | **DynamoDB Global Tables** |

## Load Balancing

| Question Pattern | Answer |
|------------------|--------|
| HTTP/HTTPS routing | **ALB** |
| Path/host routing | **ALB** |
| Static IP needed | **NLB** |
| Ultra-low latency | **NLB** |
| Third-party appliances | **GWLB** |
| WebSockets | **ALB** |

---

# ✅ FINAL RESILIENCE CHECKLIST

```
BEFORE THE EXAM, VERIFY YOU KNOW:

□ ALB vs NLB vs GWLB differences
□ Auto Scaling policies (Target Tracking, Step, Scheduled, Predictive)
□ Launch Template vs Launch Configuration
□ Lifecycle Hooks (Pending, Terminating)
□ Health check types (EC2, ELB, Route 53)
□ Route 53 routing policies (all 8 types)
□ Route 53 Alias vs CNAME
□ SQS Standard vs FIFO
□ SQS visibility timeout and DLQ
□ SNS + SQS fan-out pattern
□ EventBridge vs SNS vs SQS use cases
□ Step Functions state types
□ DR strategies (Backup/Restore, Pilot Light, Warm Standby, Multi-Site)
□ RPO vs RTO definitions
□ RDS Multi-AZ vs Read Replicas
□ Aurora Global Database
□ DynamoDB Global Tables
□ S3 Cross-Region Replication
□ AWS Backup features
□ Multi-tier architecture patterns
□ Decoupling patterns
```

---

*Last Updated: December 2025*
*Exam Version: SAA-C03*
*Domain: Design Resilient Architectures (26%)*
