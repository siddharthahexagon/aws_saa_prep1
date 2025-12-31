# AWS SAA-C03: DESIGN COST-OPTIMIZED ARCHITECTURES - PART 3

## 📋 DATABASE & NETWORK COST OPTIMIZATION

---

# 🗄️ DATABASE COST OPTIMIZATION

## RDS Pricing Components

### Definition
**Amazon RDS** pricing is based on instance type, storage, I/O, backup storage, and data transfer.

### Pricing Components

| Component | Billing Basis |
|-----------|---------------|
| **Instance Hours** | Per hour (On-Demand) or Reserved |
| **Storage** | Per GB-month (varies by type) |
| **I/O** | Per million requests (Magnetic only) |
| **Backup Storage** | Free up to DB size, then per GB-month |
| **Data Transfer** | Out to internet and other regions |
| **Snapshots** | Per GB-month (beyond free backup) |

### RDS Reserved Instances

```
┌─────────────────────────────────────────────────────────────────────┐
│                    RDS RESERVED INSTANCES                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   DISCOUNT LEVELS:                                                   │
│                                                                      │
│   1-Year Commitment:                                                 │
│   • No Upfront: ~20% savings                                        │
│   • Partial Upfront: ~30% savings                                   │
│   • All Upfront: ~35% savings                                       │
│                                                                      │
│   3-Year Commitment:                                                 │
│   • No Upfront: ~35% savings                                        │
│   • Partial Upfront: ~50% savings                                   │
│   • All Upfront: ~55% savings                                       │
│                                                                      │
│   FLEXIBILITY:                                                       │
│   • Can change DB instance size within same family                  │
│   • Cannot change DB engine                                         │
│   • Regional scope                                                  │
│                                                                      │
│   RECOMMENDATION:                                                    │
│   • Production databases → 3-year All Upfront                       │
│   • Unknown future → 1-year Partial Upfront                         │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### RDS Cost Optimization Strategies

```
┌─────────────────────────────────────────────────────────────────────┐
│                    RDS COST OPTIMIZATION                             │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   1. RIGHT-SIZE INSTANCES:                                           │
│      • Monitor CPU, memory, connections                             │
│      • Scale down over-provisioned instances                        │
│      • Use Performance Insights to identify needs                   │
│                                                                      │
│   2. USE RESERVED INSTANCES:                                         │
│      • For steady-state production databases                        │
│      • Up to 55% savings vs On-Demand                               │
│                                                                      │
│   3. USE AURORA SERVERLESS:                                          │
│      • For variable/unpredictable workloads                         │
│      • Pay per ACU-second                                           │
│      • Scale to zero when not in use                                │
│                                                                      │
│   4. STOP DEV/TEST INSTANCES:                                        │
│      • RDS can be stopped for up to 7 days                          │
│      • Only storage charges apply when stopped                      │
│                                                                      │
│   5. DELETE UNUSED RESOURCES:                                        │
│      • Old snapshots                                                │
│      • Unused read replicas                                         │
│      • Abandoned databases                                          │
│                                                                      │
│   6. CHOOSE RIGHT STORAGE:                                           │
│      • gp3 is more cost-effective than gp2                          │
│      • Use Magnetic only for small workloads                        │
│      • Enable storage auto scaling (prevent over-provisioning)      │
│                                                                      │
│   7. CONSIDER OPEN SOURCE:                                           │
│      • MySQL, PostgreSQL, MariaDB                                   │
│      • No license fees                                              │
│      • Cheaper than commercial engines (Oracle, SQL Server)         │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Aurora Pricing

### Aurora Pricing Model

| Component | Billing |
|-----------|---------|
| **Instance** | Per hour (On-Demand or Reserved) |
| **Storage** | Per GB-month ($0.10) |
| **I/O** | Per million requests ($0.20) |
| **Backup** | Per GB-month (beyond retention) |
| **Data Transfer** | Standard rates |

### Aurora Serverless v2 Pricing

```
┌─────────────────────────────────────────────────────────────────────┐
│                    AURORA SERVERLESS V2 PRICING                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   PRICING MODEL:                                                     │
│   • Pay per ACU (Aurora Capacity Unit) per hour                     │
│   • 1 ACU ≈ 2 GB RAM + proportional CPU + networking                │
│   • ~$0.12 per ACU-hour                                             │
│                                                                      │
│   SCALING:                                                           │
│   • Min: 0.5 ACU                                                    │
│   • Max: 128 ACU                                                    │
│   • Scales in increments of 0.5 ACU                                 │
│                                                                      │
│   COST COMPARISON:                                                   │
│                                                                      │
│   Scenario: Variable workload (4 hours peak, 20 hours low)          │
│                                                                      │
│   PROVISIONED (db.r6g.large):                                       │
│   • 24 hours × $0.29/hr = $6.96/day                                │
│                                                                      │
│   SERVERLESS V2 (min 2 ACU, max 8 ACU):                             │
│   • 4 hours × 8 ACU × $0.12 = $3.84 (peak)                         │
│   • 20 hours × 2 ACU × $0.12 = $4.80 (low)                         │
│   • Total: $8.64/day (more expensive in this case)                  │
│                                                                      │
│   BETTER FOR SERVERLESS:                                             │
│   • Workloads that can scale to 0.5 ACU                            │
│   • Very spiky with long idle periods                               │
│   • Dev/test environments                                           │
│                                                                      │
│   BETTER FOR PROVISIONED:                                            │
│   • Consistent workloads                                            │
│   • Always need minimum capacity                                    │
│   • Predictable costs                                               │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## DynamoDB Pricing

### Definition
**DynamoDB** pricing varies based on capacity mode, storage, and features used.

### Capacity Mode Comparison

| Mode | Pricing | Best For |
|------|---------|----------|
| **Provisioned** | Per RCU/WCU-hour | Predictable traffic |
| **On-Demand** | Per request | Variable/unknown traffic |

### Pricing Details

```
┌─────────────────────────────────────────────────────────────────────┐
│                    DYNAMODB PRICING                                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   PROVISIONED CAPACITY:                                              │
│   • Write: $0.00065 per WCU-hour                                    │
│   • Read: $0.00013 per RCU-hour                                     │
│                                                                      │
│   ON-DEMAND CAPACITY:                                                │
│   • Write: $1.25 per million writes                                 │
│   • Read: $0.25 per million reads                                   │
│                                                                      │
│   STORAGE:                                                           │
│   • Standard: $0.25 per GB-month                                    │
│   • Standard-IA: $0.10 per GB-month (+ retrieval fee)               │
│                                                                      │
│   GLOBAL TABLES:                                                     │
│   • Replicated write: $1.875 per million writes                     │
│                                                                      │
│   STREAMS:                                                           │
│   • Read: $0.02 per 100,000 read requests                          │
│                                                                      │
│   DAX:                                                               │
│   • Per node-hour (based on node type)                              │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### DynamoDB Cost Optimization

```
┌─────────────────────────────────────────────────────────────────────┐
│                    DYNAMODB COST OPTIMIZATION                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   1. CHOOSE RIGHT CAPACITY MODE:                                     │
│      ┌─────────────────────────────────────────────────────────┐    │
│      │   Traffic Pattern          │  Best Mode                 │    │
│      │   ──────────────────────────────────────────────────── │    │
│      │   Predictable, steady      │  Provisioned + Auto Scale │    │
│      │   Variable, unknown        │  On-Demand                 │    │
│      │   Spiky (> 4x baseline)    │  On-Demand                 │    │
│      │   New application          │  On-Demand (then switch)   │    │
│      └─────────────────────────────────────────────────────────┘    │
│                                                                      │
│   2. USE RESERVED CAPACITY (Provisioned):                            │
│      • 1-year: ~50% savings                                         │
│      • 3-year: ~75% savings                                         │
│      • For predictable baseline capacity                            │
│                                                                      │
│   3. USE STANDARD-IA TABLE CLASS:                                    │
│      • 60% lower storage cost                                       │
│      • For infrequently accessed data                               │
│      • Slightly higher read/write cost                              │
│                                                                      │
│   4. OPTIMIZE ITEM SIZE:                                             │
│      • Smaller items = fewer RCU/WCU                                │
│      • Compress large attributes                                    │
│      • Store large objects in S3, pointer in DynamoDB               │
│                                                                      │
│   5. USE PROJECTION EXPRESSIONS:                                     │
│      • Only retrieve needed attributes                              │
│      • Reduces data transfer and RCU                                │
│                                                                      │
│   6. USE BATCH OPERATIONS:                                           │
│      • BatchGetItem, BatchWriteItem                                 │
│      • More efficient than individual requests                      │
│                                                                      │
│   7. DELETE UNUSED INDEXES:                                          │
│      • GSI/LSI consume storage                                      │
│      • GSI consumes WCU on writes                                   │
│                                                                      │
│   8. ENABLE TTL:                                                     │
│      • Automatically delete expired items                           │
│      • No cost for TTL deletes                                      │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Provisioned vs On-Demand Cost Comparison

```
EXAMPLE: 10 million reads/month, 2 million writes/month

ON-DEMAND:
• Reads: 10M × $0.25/1M = $2.50
• Writes: 2M × $1.25/1M = $2.50
• Total: $5.00/month

PROVISIONED (assuming even distribution):
• Reads: 10M / 30 days / 24 hours / 3600 sec = ~4 RCU
• Writes: 2M / 30 days / 24 hours / 3600 sec = ~1 WCU
• Read cost: 4 RCU × 730 hours × $0.00013 = $0.38
• Write cost: 1 WCU × 730 hours × $0.00065 = $0.47
• Total: $0.85/month (83% cheaper!)

CONCLUSION: Provisioned is much cheaper for predictable traffic
```

---

## ElastiCache Pricing

### Pricing Model

| Component | Billing |
|-----------|---------|
| **Node Hours** | Per hour (On-Demand or Reserved) |
| **Data Transfer** | Out to internet |
| **Backup Storage** | Per GB-month (beyond free) |

### Cost Optimization

```
┌─────────────────────────────────────────────────────────────────────┐
│                    ELASTICACHE COST OPTIMIZATION                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   1. RIGHT-SIZE NODES:                                               │
│      • Monitor memory usage                                         │
│      • Use CloudWatch metrics                                       │
│      • Scale vertically (bigger node) or horizontally (more nodes) │
│                                                                      │
│   2. USE RESERVED NODES:                                             │
│      • Up to 55% savings vs On-Demand                               │
│      • For production caches                                        │
│                                                                      │
│   3. USE SERVERLESS (NEW):                                           │
│      • ElastiCache Serverless (pay for what you use)               │
│      • For variable workloads                                       │
│                                                                      │
│   4. CHOOSE RIGHT ENGINE:                                            │
│      • Memcached for simple caching (no replication overhead)       │
│      • Redis for complex features                                   │
│                                                                      │
│   5. OPTIMIZE DATA EXPIRATION:                                       │
│      • Set TTL on cached items                                      │
│      • Don't cache forever                                          │
│      • Evict cold data                                              │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Redshift Pricing

### Pricing Components

| Component | Billing |
|-----------|---------|
| **Compute Nodes** | Per hour (On-Demand or Reserved) |
| **Spectrum** | Per TB scanned |
| **Concurrency Scaling** | Per second (1 free hour/day) |
| **Storage** | Included in node or Managed Storage |

### Redshift Serverless

```
┌─────────────────────────────────────────────────────────────────────┐
│                    REDSHIFT SERVERLESS                               │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   PRICING:                                                           │
│   • Compute: $0.36 per RPU-hour (Redshift Processing Unit)          │
│   • Storage: $0.024 per GB-month                                    │
│                                                                      │
│   GOOD FOR:                                                          │
│   • Variable/unpredictable query workloads                          │
│   • Development and testing                                         │
│   • Occasional analytics                                            │
│                                                                      │
│   PROVISIONED BETTER FOR:                                            │
│   • Consistent, predictable workloads                               │
│   • Reserved Instance discounts (up to 70% off)                     │
│   • 24/7 production workloads                                       │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

# 🌐 NETWORK & DATA TRANSFER COSTS

## Data Transfer Pricing Overview

### AWS Data Transfer Model

```
┌─────────────────────────────────────────────────────────────────────┐
│                    AWS DATA TRANSFER COSTS                           │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   DATA INTO AWS (INGRESS):                                           │
│   • FREE from internet to AWS                                       │
│                                                                      │
│   DATA WITHIN AWS:                                                   │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │                                                              │   │
│   │   Same AZ (private IP):        FREE                         │   │
│   │   Same AZ (public/EIP):        $0.01/GB each way            │   │
│   │   Different AZ (same region):  $0.01/GB each way            │   │
│   │   Different Region:            $0.02/GB (varies by region)  │   │
│   │                                                              │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│   DATA OUT TO INTERNET (EGRESS):                                     │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │                                                              │   │
│   │   First 1 GB/month:    FREE                                 │   │
│   │   Up to 10 TB/month:   $0.09/GB                            │   │
│   │   10-50 TB/month:      $0.085/GB                           │   │
│   │   50-150 TB/month:     $0.07/GB                            │   │
│   │   > 150 TB/month:      $0.05/GB                            │   │
│   │                                                              │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│   VPC ENDPOINTS:                                                     │
│   • Gateway Endpoint (S3, DynamoDB): FREE                           │
│   • Interface Endpoint: $0.01/GB + $0.01/hour                       │
│                                                                      │
│   CLOUDFRONT:                                                        │
│   • Often cheaper than direct data transfer                         │
│   • ~$0.085/GB (varies by region)                                   │
│   • Free data transfer TO CloudFront from S3                        │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

## Data Transfer Cost Optimization

```
┌─────────────────────────────────────────────────────────────────────┐
│                    DATA TRANSFER OPTIMIZATION                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   1. USE SAME AZ WITH PRIVATE IP:                                    │
│      • Place related resources in same AZ                           │
│      • Use private IP addresses                                     │
│      • FREE data transfer                                           │
│                                                                      │
│   2. USE VPC ENDPOINTS:                                              │
│      • Gateway endpoints for S3/DynamoDB (FREE)                     │
│      • Keeps traffic within AWS network                             │
│      • No NAT Gateway charges                                       │
│                                                                      │
│   3. USE CLOUDFRONT:                                                 │
│      • Free data transfer from S3 to CloudFront                     │
│      • Lower egress rates than direct transfer                      │
│      • Better for frequently accessed content                       │
│                                                                      │
│   4. USE AWS DIRECT CONNECT:                                         │
│      • Lower data transfer rates (~$0.02/GB)                        │
│      • For large, consistent data transfer                          │
│      • Compare: VPN (~$0.09/GB egress)                              │
│                                                                      │
│   5. COMPRESS DATA:                                                  │
│      • Reduce bytes transferred                                     │
│      • Enable compression at application layer                      │
│                                                                      │
│   6. USE S3 TRANSFER ACCELERATION:                                   │
│      • Costs extra but faster uploads                               │
│      • Only use when speed is critical                              │
│                                                                      │
│   7. AVOID NAT GATEWAY FOR AWS SERVICES:                            │
│      • NAT Gateway charges $0.045/GB processed                      │
│      • Use VPC endpoints instead                                    │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

## NAT Gateway vs VPC Endpoint Cost

```
EXAMPLE: 1 TB/month S3 access from private subnet

NAT GATEWAY:
• Processing: 1,000 GB × $0.045 = $45
• Hourly: 730 hours × $0.045 = $32.85
• Total: $77.85/month

S3 GATEWAY ENDPOINT:
• Total: $0 (FREE)

SAVINGS: $77.85/month (100% savings!)
```

---

# 💵 AWS COST MANAGEMENT TOOLS

## Cost Management Services

| Service | Purpose | Use Case |
|---------|---------|----------|
| **Cost Explorer** | Analyze past costs | Understanding spending |
| **Budgets** | Set spending alerts | Proactive cost control |
| **Cost Anomaly Detection** | Detect unusual spending | Automatic alerts |
| **Savings Plans/RI Recommendations** | Purchase suggestions | Commitment planning |
| **Billing Dashboard** | Current month view | Quick overview |
| **Cost and Usage Report (CUR)** | Detailed data | Deep analysis |

## AWS Cost Explorer

### Definition
**AWS Cost Explorer** visualizes, understands, and manages AWS costs and usage over time.

### Features

```
┌─────────────────────────────────────────────────────────────────────┐
│                    COST EXPLORER FEATURES                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   1. COST VISUALIZATION:                                             │
│      • View costs by service, region, account                       │
│      • Daily, monthly, or custom date ranges                        │
│      • Filter and group data                                        │
│                                                                      │
│   2. FORECASTING:                                                    │
│      • Predict future costs (up to 12 months)                       │
│      • Based on historical usage patterns                           │
│                                                                      │
│   3. RECOMMENDATIONS:                                                │
│      • Savings Plans recommendations                                │
│      • Reserved Instance recommendations                            │
│      • Right-sizing recommendations                                 │
│                                                                      │
│   4. REPORTS:                                                        │
│      • Create saved reports                                         │
│      • Schedule and share reports                                   │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

## AWS Budgets

### Definition
**AWS Budgets** allows you to set custom cost and usage budgets and receive alerts.

### Budget Types

| Type | Description |
|------|-------------|
| **Cost Budget** | Alert on spending threshold |
| **Usage Budget** | Alert on service usage |
| **Reservation Budget** | RI/SP utilization and coverage |
| **Savings Plans Budget** | SP utilization and coverage |

### Budget Actions

```
✓ SNS notification
✓ Email alerts
✓ Apply IAM policy (stop new resource creation)
✓ Apply SCP (Organization-wide)
✓ Stop EC2/RDS instances (with approval)
```

---

# ✅ DATABASE & NETWORK COST HEURISTICS

## Instant Answer Table

| Keyword/Scenario | Answer |
|------------------|--------|
| RDS steady-state production | **Reserved Instances** |
| Variable database workload | **Aurora Serverless v2** |
| Stop dev/test RDS | **Stop instance (up to 7 days)** |
| DynamoDB predictable traffic | **Provisioned + Auto Scaling** |
| DynamoDB unknown traffic | **On-Demand** |
| DynamoDB infrequent tables | **Standard-IA table class** |
| Cache cost optimization | **Reserved Nodes** |
| Redshift variable queries | **Redshift Serverless** |
| Free S3/DynamoDB access | **Gateway VPC Endpoint** |
| Reduce NAT Gateway costs | **VPC Endpoints** |
| Cheaper internet egress | **CloudFront** |
| Monitor AWS costs | **Cost Explorer** |
| Cost alerts | **AWS Budgets** |
| Unusual spending alerts | **Cost Anomaly Detection** |

---

*Continued in Part 4: Patterns, Scenarios & Exam Questions*

---

*Last Updated: December 2025*
*Exam Version: SAA-C03*
*Domain: Design Cost-Optimized Architectures (20%)*
