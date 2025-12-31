# AWS SAA-C03: DESIGN COST-OPTIMIZED ARCHITECTURES - PART 4

## 📋 PATTERNS, SCENARIOS & EXAM QUESTIONS

---

# 📐 COST OPTIMIZATION ARCHITECTURE PATTERNS

## Pattern 1: Cost-Optimized Web Application

```
┌─────────────────────────────────────────────────────────────────────┐
│                    COST-OPTIMIZED WEB APPLICATION                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   Users                                                              │
│     │                                                                │
│     ▼                                                                │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │              CLOUDFRONT (Free S3 transfer)                   │   │
│   │              + S3 Static Assets                              │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                          │                                           │
│                          ▼                                           │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │              APPLICATION LOAD BALANCER                       │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                          │                                           │
│                          ▼                                           │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │              AUTO SCALING GROUP                              │   │
│   │                                                              │   │
│   │   ┌─────────────────────────────────────────────────────┐   │   │
│   │   │  On-Demand (Reserved/Savings Plan) - Baseline       │   │   │
│   │   │  2 instances (minimum capacity)                      │   │   │
│   │   └─────────────────────────────────────────────────────┘   │   │
│   │                         +                                    │   │
│   │   ┌─────────────────────────────────────────────────────┐   │   │
│   │   │  Spot Instances - Scaling capacity                  │   │   │
│   │   │  0-10 instances (scale based on demand)             │   │   │
│   │   └─────────────────────────────────────────────────────┘   │   │
│   │                                                              │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                          │                                           │
│                          ▼                                           │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │              ELASTICACHE (Reserved Nodes)                    │   │
│   │              Redis for session/query caching                │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                          │                                           │
│                          ▼                                           │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │              RDS (Reserved Instance)                         │   │
│   │              Multi-AZ, gp3 storage                          │   │
│   │              ─────────────────────────                       │   │
│   │              Gateway Endpoint ──▶ S3 (Lifecycle to Glacier) │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│   COST OPTIMIZATIONS APPLIED:                                        │
│   ✓ CloudFront for static content (free from S3)                    │
│   ✓ Reserved Instances for baseline                                 │
│   ✓ Spot for scaling (up to 90% savings)                            │
│   ✓ ElastiCache reduces database load                               │
│   ✓ gp3 storage (cheaper than gp2)                                  │
│   ✓ Gateway Endpoint (free S3 access)                               │
│   ✓ S3 Lifecycle (auto-archive old data)                            │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

## Pattern 2: Cost-Optimized Data Processing

```
┌─────────────────────────────────────────────────────────────────────┐
│                    COST-OPTIMIZED DATA PIPELINE                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   Data Sources                                                       │
│       │                                                              │
│       ▼                                                              │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │              KINESIS DATA FIREHOSE                           │   │
│   │              (Direct to S3, no server management)            │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                          │                                           │
│                          ▼                                           │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │              S3 DATA LAKE                                    │   │
│   │                                                              │   │
│   │   Hot Data:    S3 Standard (recent)                         │   │
│   │   Warm Data:   S3 Intelligent-Tiering (auto-managed)        │   │
│   │   Cold Data:   S3 Glacier (archived via lifecycle)          │   │
│   │                                                              │   │
│   │   Format: Parquet (columnar, compressed)                    │   │
│   │   Partitioned by: date/customer/region                      │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                          │                                           │
│         ┌────────────────┼────────────────┐                         │
│         │                │                │                          │
│         ▼                ▼                ▼                          │
│   ┌──────────┐    ┌──────────┐    ┌──────────────┐                  │
│   │  Athena  │    │   EMR    │    │   Redshift   │                  │
│   │(Ad-hoc)  │    │ (Spot)   │    │ (Serverless) │                  │
│   │$5/TB scan│    │90% off   │    │ (Variable)   │                  │
│   └──────────┘    └──────────┘    └──────────────┘                  │
│                                                                      │
│   COST OPTIMIZATIONS APPLIED:                                        │
│   ✓ Kinesis Firehose (serverless, pay per data)                     │
│   ✓ S3 Lifecycle policies (auto-transition)                         │
│   ✓ Intelligent-Tiering for unknown access                          │
│   ✓ Parquet format (reduces Athena scan costs)                      │
│   ✓ Partitioning (reduces data scanned)                             │
│   ✓ EMR with Spot instances (90% savings)                           │
│   ✓ Redshift Serverless (pay per query)                             │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

## Pattern 3: Cost-Optimized Serverless Application

```
┌─────────────────────────────────────────────────────────────────────┐
│                    COST-OPTIMIZED SERVERLESS                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   Users                                                              │
│     │                                                                │
│     ▼                                                                │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │              CLOUDFRONT + S3                                 │   │
│   │              (Static frontend - very cheap)                  │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                          │                                           │
│                          ▼                                           │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │              API GATEWAY (HTTP API)                          │   │
│   │              71% cheaper than REST API                       │   │
│   │              + Response caching                              │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                          │                                           │
│                          ▼                                           │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │              LAMBDA (ARM64 / Graviton)                       │   │
│   │              20% cheaper than x86                            │   │
│   │              + Right-sized memory (Compute Optimizer)        │   │
│   │              + Compute Savings Plan (up to 17% off)          │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                          │                                           │
│                          ▼                                           │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │              DYNAMODB (On-Demand → Provisioned)              │   │
│   │                                                              │   │
│   │   Start: On-Demand (learn traffic patterns)                 │   │
│   │   Then:  Provisioned + Auto Scaling + Reserved Capacity     │   │
│   │          (up to 75% savings)                                │   │
│   │                                                              │   │
│   │   + DAX caching (reduce read costs)                         │   │
│   │   + TTL for automatic deletion                              │   │
│   │   + Standard-IA for cold tables                             │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│   COST OPTIMIZATIONS APPLIED:                                        │
│   ✓ HTTP API (cheaper than REST)                                    │
│   ✓ API caching (reduces Lambda invocations)                        │
│   ✓ Lambda ARM64 (20% cheaper)                                      │
│   ✓ Lambda right-sizing (Compute Optimizer)                         │
│   ✓ DynamoDB Provisioned (after learning patterns)                  │
│   ✓ DynamoDB Reserved Capacity (75% savings)                        │
│   ✓ DAX caching (fewer reads)                                       │
│   ✓ TTL (automatic cleanup)                                         │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

## Pattern 4: Cost-Optimized Hybrid Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                    COST-OPTIMIZED HYBRID                             │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   ON-PREMISES                           AWS CLOUD                    │
│   ┌─────────────────┐                   ┌─────────────────────────┐ │
│   │                 │                   │                          │ │
│   │   Existing      │   Direct Connect  │   Burst Capacity         │ │
│   │   Servers       │   (cheaper than   │   (Spot Instances)       │ │
│   │   (sunk cost)   │    VPN for high   │                          │ │
│   │                 │    volume)        │   DR Environment         │ │
│   │                 │ ◀──────────────▶  │   (Pilot Light)          │ │
│   │   Data Center   │                   │                          │ │
│   │                 │                   │   Archive                │ │
│   └─────────────────┘                   │   (S3 Glacier)           │ │
│                                         │                          │ │
│                                         │   Backup                 │ │
│                                         │   (AWS Backup)           │ │
│                                         │                          │ │
│                                         └─────────────────────────┘ │
│                                                                      │
│   COST OPTIMIZATIONS:                                                │
│   ✓ Direct Connect for high-volume data transfer                    │
│   ✓ Spot for burst capacity (90% savings)                           │
│   ✓ Pilot Light DR (minimal always-on cost)                         │
│   ✓ S3 Glacier for archive (very cheap)                             │
│   ✓ Keep predictable workloads on-prem (sunk cost)                  │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

# 📝 EXAM SCENARIOS

## Scenario 1: E-Commerce Cost Optimization

**Question:** An e-commerce company runs web servers on EC2 with steady baseline traffic but experiences 3x spikes during sales events. Database is RDS MySQL. How can they reduce costs?

**Answer:**
1. **EC2 Baseline**: Reserved Instances or Savings Plans for minimum capacity ✅
2. **EC2 Scaling**: Spot Instances for additional capacity during spikes ✅
3. **RDS**: Reserved Instance for database ✅
4. **Caching**: ElastiCache to reduce database load ✅
5. **Static Content**: CloudFront + S3 for images/CSS/JS ✅

**Why not On-Demand for everything?**
- On-Demand is most expensive for steady workloads
- Baseline traffic is predictable → use commitments

---

## Scenario 2: Data Lake Cost Optimization

**Question:** A company stores 100 TB in S3, with 80% accessed less than once per month. They query data with Athena but costs are high. How to optimize?

**Answer:**
1. **S3 Storage**: Move 80% to S3 Standard-IA or Intelligent-Tiering ✅
2. **Data Format**: Convert to Parquet (columnar, compressed) ✅
3. **Partitioning**: Partition by date/key fields ✅
4. **Athena**: Enable query result caching ✅

**Cost Impact:**
- Standard-IA: ~40% cheaper than Standard
- Parquet: Reduces Athena scan by 80-90%
- Partitioning: Only scans relevant data

---

## Scenario 3: Startup Cost Management

**Question:** A startup is unsure about their traffic patterns. They need a database and compute. What's the most cost-effective approach?

**Answer:**
1. **Database**: DynamoDB On-Demand (pay per request) ✅
2. **Compute**: Lambda or EC2 On-Demand ✅
3. **After 3 months**: Analyze patterns with Cost Explorer ✅
4. **Then**: Switch to Provisioned/Reserved based on patterns ✅

**Key Insight:** Start flexible, then optimize after understanding patterns.

---

## Scenario 4: Batch Processing

**Question:** A company runs nightly batch jobs that process data for 4 hours. Jobs can be restarted if interrupted. How to minimize costs?

**Answer:**
- **Spot Instances** ✅ (up to 90% savings)
- Jobs are fault-tolerant and can be restarted
- Use Spot Fleet with diversified allocation
- Set up checkpointing to save progress

**Why not Reserved?**
- Only runs 4 hours/night (not 24/7)
- Reserved would pay for unused hours

---

## Scenario 5: Development Environment

**Question:** A development team uses RDS and EC2 during business hours (8 AM - 6 PM) on weekdays. How to reduce costs?

**Answer:**
1. **EC2**: Use AWS Instance Scheduler to stop instances outside business hours ✅
2. **RDS**: Stop database when not in use (up to 7 days) ✅
3. **Alternative**: Aurora Serverless v2 (scales to near-zero) ✅

**Savings:**
- ~60% reduction (10 hours/day vs 24 hours)

---

## Scenario 6: Data Transfer Costs

**Question:** A company's AWS bill shows high data transfer costs from EC2 to S3 in the same region. How to reduce?

**Answer:**
- **S3 Gateway VPC Endpoint** ✅ (FREE)
- Currently using NAT Gateway = $0.045/GB
- Gateway Endpoint = $0/GB

**Additional:**
- Use private IPs for same-AZ traffic (free)
- Check if resources can be in same AZ

---

## Scenario 7: Archive Requirements

**Question:** A company must retain 5 years of compliance data but only accesses it for annual audits. Current storage cost is too high. What's the cheapest solution?

**Answer:**
- **S3 Glacier Deep Archive** ✅
- Cost: ~$0.00099/GB/month (99% cheaper than Standard)
- 12-48 hour retrieval is acceptable for annual audits
- Use S3 Lifecycle to transition automatically

---

## Scenario 8: Multi-Account Cost Management

**Question:** An enterprise has 50 AWS accounts. How can they get volume discounts and manage costs centrally?

**Answer:**
1. **AWS Organizations**: Consolidated billing ✅
   - Aggregate usage for volume discounts
   - Share Reserved Instances across accounts
   - Share Savings Plans across organization
2. **AWS Budgets**: Set budgets per account ✅
3. **Cost Explorer**: Analyze by account ✅
4. **SCPs**: Prevent expensive resource creation ✅

---

# ⚡ INSTANT ANSWER TABLE - COST OPTIMIZATION

## Complete Quick Reference

| Keyword/Scenario | Instant Answer |
|------------------|----------------|
| Steady-state EC2 | **Reserved Instances or Savings Plans** |
| Variable EC2 | **On-Demand** |
| Fault-tolerant workload | **Spot Instances** |
| Flexible compute commitment | **Compute Savings Plans** |
| Maximum EC2 savings, known family | **Standard RI or EC2 Savings Plan** |
| Batch processing | **Spot Instances** |
| EMR cost optimization | **Spot Instances (core: On-Demand, task: Spot)** |
| BYOL licensing | **Dedicated Hosts** |
| Right-sizing | **Compute Optimizer** |
| Lambda cheaper | **ARM64 (Graviton)** |
| Fargate cheaper | **Fargate Spot** |
| Unknown S3 access pattern | **S3 Intelligent-Tiering** |
| Infrequent S3 access | **S3 Standard-IA or One Zone-IA** |
| Archive, hours retrieval OK | **S3 Glacier Flexible Retrieval** |
| Long-term archive | **S3 Glacier Deep Archive** |
| Automate S3 cost savings | **Lifecycle Policies** |
| Cheaper EBS | **gp3 instead of gp2** |
| Cheapest EBS | **sc1 (Cold HDD)** |
| Free temporary storage | **Instance Store** |
| EFS cost reduction | **Lifecycle to IA** |
| Delete old snapshots | **Data Lifecycle Manager** |
| RDS steady-state | **Reserved Instances** |
| Variable database | **Aurora Serverless v2** |
| DynamoDB predictable | **Provisioned + Reserved Capacity** |
| DynamoDB variable | **On-Demand** |
| DynamoDB infrequent table | **Standard-IA table class** |
| Free S3/DynamoDB from VPC | **Gateway VPC Endpoint** |
| Reduce NAT Gateway costs | **VPC Endpoints** |
| Cheaper internet egress | **CloudFront** |
| Monitor costs | **Cost Explorer** |
| Cost alerts | **AWS Budgets** |
| Unusual spending | **Cost Anomaly Detection** |
| API cheaper than REST | **HTTP API** |
| Reduce Athena costs | **Parquet + Partitioning** |
| Dev/test outside hours | **Instance Scheduler / Stop** |

---

# 📋 COST OPTIMIZATION CHECKLIST

## Before Exam Review

### Compute
- [ ] Know all EC2 pricing models and when to use each
- [ ] Understand Savings Plans vs Reserved Instances
- [ ] Know Spot Instance use cases and strategies
- [ ] Understand Lambda pricing (requests + duration)
- [ ] Know ARM/Graviton cost benefits

### Storage
- [ ] Know all S3 storage classes and costs
- [ ] Understand S3 Lifecycle policies
- [ ] Know S3 Intelligent-Tiering benefits
- [ ] Understand EBS volume costs (gp3 vs gp2)
- [ ] Know when to use Instance Store

### Database
- [ ] Understand RDS Reserved Instances
- [ ] Know when to use Aurora Serverless
- [ ] Understand DynamoDB capacity modes
- [ ] Know DynamoDB Reserved Capacity

### Network
- [ ] Understand data transfer costs
- [ ] Know VPC Endpoint cost benefits
- [ ] Understand CloudFront pricing advantages
- [ ] Know NAT Gateway costs

### Management
- [ ] Know Cost Explorer features
- [ ] Understand AWS Budgets
- [ ] Know Compute Optimizer use cases

---

# 🎯 FINAL EXAM TIPS - COST DOMAIN

## Key Rules to Remember

```
┌─────────────────────────────────────────────────────────────────────┐
│                    COST OPTIMIZATION RULES                           │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   1. MATCH PRICING TO WORKLOAD PATTERN:                              │
│      • Steady → Reserved/Savings Plans                              │
│      • Variable → On-Demand                                         │
│      • Interruptible → Spot                                         │
│                                                                      │
│   2. MATCH STORAGE TO ACCESS PATTERN:                                │
│      • Frequent → Standard                                          │
│      • Infrequent → IA classes                                      │
│      • Archive → Glacier classes                                    │
│      • Unknown → Intelligent-Tiering                                │
│                                                                      │
│   3. USE FREE OPTIONS WHERE POSSIBLE:                                │
│      • S3 Gateway VPC Endpoint (free)                               │
│      • Same-AZ with private IP (free)                               │
│      • CloudFront from S3 (free transfer)                           │
│      • Instance Store (included with instance)                      │
│                                                                      │
│   4. AUTOMATE COST OPTIMIZATION:                                     │
│      • S3 Lifecycle policies                                        │
│      • EBS Data Lifecycle Manager                                   │
│      • Auto Scaling to match demand                                 │
│      • Instance Scheduler for dev/test                              │
│                                                                      │
│   5. MONITOR AND ANALYZE:                                            │
│      • Cost Explorer for analysis                                   │
│      • AWS Budgets for alerts                                       │
│      • Compute Optimizer for right-sizing                           │
│                                                                      │
│   6. CONSIDER TOTAL COST:                                            │
│      • Not just resource cost                                       │
│      • Data transfer costs                                          │
│      • Request costs                                                │
│      • Operational overhead                                         │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

## Common Exam Traps

| Trap | Reality |
|------|---------|
| "Just use Spot for everything" | Only for fault-tolerant workloads |
| "On-Demand is always bad" | Good for unpredictable/short-term |
| "Reserved is always best" | Only for steady-state, long-term |
| "S3 Standard is expensive" | Right choice for frequent access |
| "Serverless is always cheaper" | Not always - depends on usage pattern |
| "More memory = more cost (Lambda)" | Sometimes more memory = faster = cheaper |

## Exam Question Keywords

| If you see... | Think... |
|---------------|----------|
| "Cost-effective" | Match to workload pattern |
| "Reduce costs" | Look for overprovisioning |
| "Predictable workload" | Reserved/Savings Plans |
| "Batch processing" | Spot Instances |
| "Archive" | Glacier (check retrieval needs) |
| "Infrequently accessed" | IA storage classes |
| "Data transfer costs" | VPC Endpoints, same region/AZ |
| "Unknown traffic" | On-Demand, Intelligent-Tiering |

---

*Last Updated: December 2025*
*Exam Version: SAA-C03*
*Domain: Design Cost-Optimized Architectures (20%)*
