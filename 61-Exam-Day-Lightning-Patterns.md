# AWS SAA-C03: EXAM DAY LIGHTNING PATTERNS ⚡

## 🎯 2-SECOND DECISION GUIDE - Pattern Recognition for Speed

**Use this file the morning of your exam. Each pattern = 2-5 seconds to answer.**

---

## 🔥 PART 1: IF YOU SEE THIS WORD → CHOOSE THIS SERVICE

### Compute Keywords
| Word/Phrase | Service | Confidence |
|-------------|---------|------------|
| Serverless | Lambda, Fargate, Aurora Serverless, DynamoDB | 95% |
| Event-driven | Lambda, EventBridge | 90% |
| < 15 minutes | Lambda | 99% |
| > 15 minutes | EC2, ECS, Batch | 99% |
| Container + Kubernetes | EKS | 95% |
| Container + simple | ECS | 90% |
| Container + no servers | Fargate | 95% |
| Batch processing | AWS Batch | 90% |
| Quick deploy web app | Elastic Beanstalk | 85% |

### Storage Keywords
| Word/Phrase | Service | Confidence |
|-------------|---------|------------|
| Object storage | S3 | 99% |
| Unlimited storage | S3 | 99% |
| Block storage | EBS | 95% |
| Shared file system + Linux | EFS | 95% |
| Shared file system + Windows | FSx for Windows | 95% |
| HPC + parallel | FSx for Lustre | 90% |
| Archive | S3 Glacier | 95% |
| 7+ years retention | Glacier Deep Archive | 90% |
| Temporary storage | Instance Store | 95% |

### Database Keywords
| Word/Phrase | Service | Confidence |
|-------------|---------|------------|
| 5x performance + MySQL/PostgreSQL | Aurora | 95% |
| NoSQL + serverless | DynamoDB | 95% |
| Key-value | DynamoDB | 90% |
| Petabyte + warehouse | Redshift | 95% |
| MongoDB | DocumentDB | 99% |
| Graph database | Neptune | 99% |
| Time-series | Timestream | 95% |
| Ledger + immutable | QLDB | 99% |
| Cassandra | Keyspaces | 99% |

### Networking Keywords
| Word/Phrase | Service | Confidence |
|-------------|---------|------------|
| CDN | CloudFront | 99% |
| DNS | Route 53 | 99% |
| Web load balancer | ALB | 95% |
| High-performance LB + static IP | NLB | 95% |
| Private connection | Direct Connect | 90% |
| Quick VPN | Site-to-Site VPN | 95% |
| Connect multiple VPCs | Transit Gateway | 90% |

### Security Keywords
| Word/Phrase | Service | Confidence |
|-------------|---------|------------|
| Encryption keys | KMS | 95% |
| Auto-rotate secrets | Secrets Manager | 95% |
| Free SSL certificate | ACM | 99% |
| DDoS protection (free) | Shield Standard | 99% |
| Web application firewall | WAF | 99% |
| Threat detection | GuardDuty | 95% |
| PII detection | Macie | 99% |
| API audit log | CloudTrail | 99% |

### Integration Keywords
| Word/Phrase | Service | Confidence |
|-------------|---------|------------|
| Queue + decouple | SQS | 99% |
| Pub/Sub + fan-out | SNS | 95% |
| Event bus | EventBridge | 90% |
| Real-time streaming | Kinesis Data Streams | 90% |
| Load to S3/Redshift | Kinesis Firehose | 90% |

### Analytics Keywords
| Word/Phrase | Service | Confidence |
|-------------|---------|------------|
| SQL on S3 | Athena | 99% |
| ETL | Glue | 95% |
| Big data + Hadoop/Spark | EMR | 95% |
| BI + visualize | QuickSight | 95% |

---

## ⚡ PART 2: IF-THEN INSTANT DECISION RULES

### Rule 1: Compute Selection (Use First Match)
```
IF "serverless" AND duration < 15 min
   THEN Lambda

IF "serverless" AND duration > 15 min
   THEN Fargate

IF "full control" OR "custom software"
   THEN EC2

IF "containers" AND "Kubernetes"
   THEN EKS

IF "containers" AND NOT "Kubernetes"
   THEN ECS

IF "batch" AND "any scale"
   THEN AWS Batch
```

---

### Rule 2: Storage Class Selection
```
IF "frequently accessed"
   THEN S3 Standard

IF "unknown pattern" OR "changing pattern"
   THEN S3 Intelligent-Tiering

IF "infrequent" AND "instant retrieval" AND "multi-AZ"
   THEN S3 Standard-IA

IF "infrequent" AND "instant retrieval" AND "single AZ OK"
   THEN S3 One Zone-IA

IF "archive" AND "instant access"
   THEN S3 Glacier Instant Retrieval

IF "archive" AND "hours OK"
   THEN S3 Glacier Flexible Retrieval

IF "7+ years" OR "compliance" OR "lowest cost"
   THEN S3 Glacier Deep Archive
```

---

### Rule 3: Database Selection
```
IF "relational" AND "5x performance"
   THEN Aurora

IF "relational" AND "standard"
   THEN RDS

IF "NoSQL" AND "serverless"
   THEN DynamoDB

IF "MongoDB"
   THEN DocumentDB

IF "Cassandra"
   THEN Keyspaces

IF "graph" OR "relationships"
   THEN Neptune

IF "data warehouse" OR "petabyte" OR "OLAP"
   THEN Redshift

IF "cache" AND "DynamoDB"
   THEN DAX

IF "cache" AND "database"
   THEN ElastiCache Redis

IF "time-series" OR "IoT"
   THEN Timestream

IF "ledger" OR "immutable"
   THEN QLDB
```

---

### Rule 4: Load Balancer Selection
```
IF "HTTP" OR "HTTPS" OR "path routing"
   THEN ALB

IF "TCP" OR "UDP" OR "extreme performance" OR "static IP"
   THEN NLB

IF "third-party appliance" OR "firewall"
   THEN GWLB
```

---

### Rule 5: High Availability Pattern
```
IF "HA" AND "database"
   THEN Multi-AZ RDS/Aurora

IF "HA" AND "compute"
   THEN Auto Scaling Group + Multi-AZ + ALB/NLB

IF "HA" AND "storage"
   THEN S3 (automatic) OR EFS (automatic)

IF "HA" AND "network"
   THEN Multiple NAT Gateways (one per AZ)
```

---

### Rule 6: Disaster Recovery Selection
```
IF "lowest cost" AND "hours RTO/RPO"
   THEN Backup & Restore

IF "minutes RTO" AND "low cost"
   THEN Pilot Light

IF "minutes RTO" AND "seconds RPO"
   THEN Warm Standby

IF "near-zero RTO/RPO"
   THEN Multi-Site Active/Active
```

---

### Rule 7: Caching Selection
```
IF "CDN" OR "global users" OR "edge"
   THEN CloudFront

IF "DynamoDB" AND "microsecond"
   THEN DAX

IF "database" AND "complex queries"
   THEN ElastiCache Redis

IF "session store" OR "persist"
   THEN ElastiCache Redis

IF "simple cache" AND "multi-threaded"
   THEN ElastiCache Memcached
```

---

### Rule 8: Security Pattern
```
IF "encrypt data at rest"
   THEN KMS + S3 encryption OR EBS encryption

IF "encrypt data in transit"
   THEN HTTPS/TLS + ACM certificate

IF "rotate database passwords"
   THEN Secrets Manager

IF "detect threats"
   THEN GuardDuty

IF "scan vulnerabilities"
   THEN Inspector

IF "find PII"
   THEN Macie

IF "audit API calls"
   THEN CloudTrail

IF "DDoS protection" (basic)
   THEN Shield Standard (automatic/free)

IF "DDoS protection" (enterprise)
   THEN Shield Advanced
```

---

### Rule 9: Integration Pattern
```
IF "queue" OR "decouple" OR "async"
   THEN SQS

IF "pub/sub" OR "fan-out" OR "multiple subscribers"
   THEN SNS

IF "event-driven" OR "multiple sources"
   THEN EventBridge

IF "orchestrate" OR "workflow" OR "state machine"
   THEN Step Functions

IF "real-time stream" AND "custom processing"
   THEN Kinesis Data Streams

IF "load to S3/Redshift" AND "no processing"
   THEN Kinesis Firehose
```

---

### Rule 10: Cost Optimization Pattern
```
IF "steady-state" AND "predictable" AND "1-3 years"
   THEN Reserved Instances (Standard)

IF "long-term" AND "may change instance type"
   THEN Reserved Instances (Convertible)

IF "flexible compute commitment"
   THEN Savings Plans

IF "fault-tolerant" AND "flexible timing"
   THEN Spot Instances

IF "variable/unpredictable" AND "short-term"
   THEN On-Demand

IF "serverless" AND "pay per use"
   THEN Lambda, Fargate, Aurora Serverless
```

---

## 🎯 PART 3: DOMAIN-SPECIFIC INSTANT PATTERNS

### Design Secure Architectures (30%)

| Scenario | Instant Answer |
|----------|----------------|
| Prevent accidental deletion | MFA Delete + Versioning + Object Lock |
| Secure S3 bucket | Block public access + bucket policy + encryption |
| Secure database | Private subnet + Security Group + encryption at rest/transit |
| Least privilege | IAM policies with minimum permissions |
| Rotate secrets automatically | Secrets Manager |
| Detect unauthorized access | GuardDuty + CloudTrail |
| Network isolation | Private subnet + NAT Gateway (no IGW) |
| Encrypt everything | KMS for keys, enable encryption on all services |

---

### Design Resilient Architectures (26%)

| Scenario | Instant Answer |
|----------|----------------|
| Eliminate single point of failure | Multi-AZ deployment |
| Auto-healing | Auto Scaling + health checks |
| Decouple components | SQS between services |
| Stateless application | Store sessions in ElastiCache/DynamoDB |
| Database failover | Multi-AZ RDS (automatic) |
| Read scaling | Read Replicas |
| Regional failover | Multi-region with Route 53 failover |
| Disaster recovery (low cost) | Backup to S3 + Glacier |
| Disaster recovery (fast) | Warm Standby or Multi-Site |

---

### Design High-Performing Architectures (24%)

| Scenario | Instant Answer |
|----------|----------------|
| Global low latency | CloudFront |
| Database query performance | Add indexes, use caching (ElastiCache/DAX) |
| Read-heavy database | Read Replicas |
| Write-heavy database | DynamoDB (auto-sharding) |
| Fast storage | io2 EBS, Instance Store |
| Parallel processing | Lambda concurrent invocations, EMR |
| Reduce database load | ElastiCache or DAX |
| Low-latency compute | Enhanced Networking, Placement Groups |

---

### Design Cost-Optimized Architectures (20%)

| Scenario | Instant Answer |
|----------|----------------|
| Reduce storage costs | Lifecycle policies (S3 to Glacier) |
| Reduce compute costs (predictable) | Reserved Instances |
| Reduce compute costs (flexible) | Spot Instances |
| Reduce data transfer costs | CloudFront caching, VPC Endpoints |
| Pay only for what you use | Serverless (Lambda, Fargate, Aurora Serverless) |
| Stop dev/test environments | Scheduled Auto Scaling (off nights/weekends) |
| Right-sizing | Use Compute Optimizer, choose appropriate instance types |

---

## 🔴 PART 4: CRITICAL COMPARISON PATTERNS

### Multi-AZ vs Multi-Region vs Read Replica

| Feature | Multi-AZ | Multi-Region | Read Replica |
|---------|----------|--------------|--------------|
| **Purpose** | HA (failover) | DR + global reach | Read scaling |
| **Replication** | Synchronous | Asynchronous | Asynchronous |
| **Failover** | Automatic | Manual (or Route 53) | Manual promotion |
| **RTO** | Minutes | Minutes-Hours | Hours |
| **Cost** | Low | High | Medium |
| **Choose when** | "HA" / "automatic failover" | "DR" / "global" | "Read-heavy" / "reporting" |

---

### Security Group vs NACL

| Feature | Security Group | NACL |
|---------|----------------|------|
| **Level** | Instance | Subnet |
| **Statefulness** | Stateful | Stateless |
| **Rules** | Allow only | Allow + Deny |
| **Return traffic** | Automatic | Must configure |
| **Choose when** | Default firewall | Need to deny specific IPs |

**Critical:** NACL can DENY, Security Group cannot!

---

### ALB vs NLB vs GWLB

| Feature | ALB | NLB | GWLB |
|---------|-----|-----|------|
| **Layer** | 7 (HTTP/HTTPS) | 4 (TCP/UDP) | 3 (Network) |
| **Routing** | Path, host, header | Port-based | N/A |
| **Performance** | Good | Extreme | N/A |
| **Static IP** | No | Yes | No |
| **Use case** | Web apps | High performance, gaming | Virtual appliances |

---

### CloudFront vs Global Accelerator

| Feature | CloudFront | Global Accelerator |
|---------|------------|-------------------|
| **Layer** | 7 (HTTP/HTTPS) | 4 (TCP/UDP) |
| **Caching** | Yes | No |
| **Static IP** | No | Yes (2 anycast) |
| **Use case** | Content delivery | TCP/UDP acceleration |

---

### S3 vs EBS vs EFS vs Instance Store

| Feature | S3 | EBS | EFS | Instance Store |
|---------|----|----|-----|----------------|
| **Type** | Object | Block | File | Block |
| **Capacity** | Unlimited | 64 TB max | Unlimited | Limited (GB-TB) |
| **Shared** | Yes (web) | No (single EC2) | Yes (multiple EC2) | No |
| **Durability** | 11 9s | 99.999% | 11 9s | Lost on stop |
| **Use case** | Files, backups | Boot volumes, databases | Shared file system | Temporary, high performance |

---

### Lambda vs EC2 vs Fargate

| Feature | Lambda | EC2 | Fargate |
|---------|--------|-----|---------|
| **Max duration** | 15 min | Unlimited | Unlimited |
| **Server management** | None | Full | None |
| **Scaling** | Automatic | Manual/Auto Scaling | Automatic |
| **Pricing** | Per invocation | Per hour | Per vCPU/memory |
| **Use case** | Event-driven, short | Long-running, custom | Containers, no servers |

---

### RDS vs DynamoDB vs Redshift vs Aurora

| Feature | RDS | DynamoDB | Redshift | Aurora |
|---------|-----|----------|----------|--------|
| **Type** | Relational | NoSQL | Data Warehouse | Relational |
| **Scale** | Vertical | Horizontal | Petabyte | Horizontal (storage) |
| **Use case** | OLTP | High-scale OLTP | OLAP/Analytics | High-perf OLTP |
| **Serverless** | No (except Aurora) | Yes | No (but can pause) | Yes (Serverless v2) |

---

### SQS vs SNS vs EventBridge vs Step Functions

| Service | Pattern | Use Case |
|---------|---------|----------|
| **SQS** | Queue (pull) | Decouple, async processing, buffering |
| **SNS** | Pub/Sub (push) | Fan-out, multiple subscribers |
| **EventBridge** | Event bus | Event routing from AWS services |
| **Step Functions** | Orchestration | Coordinate multiple services |

---

## ⚡ PART 5: LIGHTNING DECISION TREES

### Tree 1: Storage Selection (3 Questions)

```
Is it files/objects?
├─ YES → S3 (+ choose storage class based on access pattern)
└─ NO → Is it block storage for EC2?
        ├─ YES → EBS (+ choose volume type based on IOPS/throughput)
        └─ NO → Is it shared file system?
                ├─ Linux → EFS
                └─ Windows → FSx for Windows
```

---

### Tree 2: Database Selection (4 Questions)

```
Is it relational (SQL)?
├─ YES → Do you need 5x performance?
│        ├─ YES → Aurora
│        └─ NO → RDS
└─ NO → Is it NoSQL?
        ├─ YES → Is it key-value or document?
        │        ├─ YES → DynamoDB
        │        └─ NO → Graph? → Neptune
        └─ NO → Is it analytics/warehouse?
                └─ YES → Redshift
```

---

### Tree 3: Compute Selection (3 Questions)

```
Is duration < 15 minutes?
├─ YES → Is it event-driven?
│        ├─ YES → Lambda
│        └─ NO → Still consider Lambda or Fargate
└─ NO → Do you need full OS control?
        ├─ YES → EC2
        └─ NO → Is it containerized?
                ├─ YES → Do you need Kubernetes?
                │        ├─ YES → EKS
                │        └─ NO → ECS
                └─ NO → Elastic Beanstalk
```

---

### Tree 4: Caching Selection (2 Questions)

```
What are you caching?
├─ DynamoDB? → DAX
├─ Web content? → CloudFront
└─ Database queries or sessions?
    ├─ Need persistence? → ElastiCache Redis
    └─ Simple cache? → ElastiCache Memcached
```

---

## 🚨 PART 6: EXAM TRAP AVOIDANCE (MEMORIZE!)

### Trap Detection Keywords

| If Question Says... | Trap | Correct Answer |
|---------------------|------|----------------|
| "Most cost-effective" + steady traffic | DON'T choose Lambda | Reserved Instances |
| "Most cost-effective" + storage | DON'T choose S3 Standard | Choose appropriate tier (IA, Glacier) |
| "Multi-AZ" | DON'T confuse with Multi-Region | Multi-AZ = HA, same region |
| "Disaster Recovery" | DON'T choose Multi-AZ only | Need Multi-Region or backups |
| "Read scaling" | DON'T choose Multi-AZ | Choose Read Replicas |
| "Block specific IP" | DON'T choose Security Group | Choose NACL (can deny) |
| "Serverless" + "> 15 min" | DON'T choose Lambda | Choose Fargate or EC2 |
| "Cache" + "DynamoDB" | DON'T choose ElastiCache | Choose DAX |
| "Shared file system" + "Linux" | DON'T choose EBS | Choose EFS |
| "Global acceleration" + "TCP/UDP" | DON'T choose CloudFront | Choose Global Accelerator |

---

## 🎯 PART 7: 60-SECOND FINAL REVIEW

### Top 15 Services (Know These Cold)

1. **EC2** = Virtual servers, full control
2. **Lambda** = Serverless compute, < 15 min
3. **S3** = Object storage, unlimited
4. **EBS** = Block storage for EC2
5. **RDS** = Managed relational database
6. **DynamoDB** = NoSQL, serverless
7. **VPC** = Private network
8. **ALB** = Layer 7 load balancer
9. **Route 53** = DNS
10. **CloudFront** = CDN
11. **IAM** = Identity and access
12. **CloudWatch** = Metrics and logs
13. **CloudTrail** = API audit logs
14. **SQS** = Message queue
15. **SNS** = Pub/Sub notifications

### Top 10 Heuristics

1. **Serverless** → Lambda, DynamoDB, Aurora Serverless, Fargate
2. **Cache** → CloudFront (web), ElastiCache (DB), DAX (DynamoDB)
3. **HA** → Multi-AZ + Auto Scaling + Load Balancer
4. **Cost** → Right-size + Reserved/Spot + Lifecycle policies + Serverless
5. **Performance** → Caching + Read Replicas + Right instance type
6. **Security** → Encryption + Least privilege + MFA + Private subnets
7. **Decouple** → SQS, SNS, EventBridge
8. **Storage** → S3 (objects), EBS (block), EFS (shared file)
9. **Database** → Aurora (relational, high-perf), DynamoDB (NoSQL), Redshift (warehouse)
10. **Network** → ALB (HTTP), NLB (TCP/UDP), CloudFront (CDN), Route 53 (DNS)

---

## ✅ FINAL CONFIDENCE CHECK

**Can you answer these in < 5 seconds each?**

1. "Serverless compute, event-driven, < 15 min" → ?
   - **Answer: Lambda**

2. "NoSQL, serverless, key-value" → ?
   - **Answer: DynamoDB**

3. "Web load balancer, path-based routing" → ?
   - **Answer: ALB**

4. "Object storage, unlimited capacity" → ?
   - **Answer: S3**

5. "Cache DynamoDB queries for microsecond latency" → ?
   - **Answer: DAX**

6. "Queue messages to decouple services" → ?
   - **Answer: SQS**

7. "Fan-out to multiple subscribers" → ?
   - **Answer: SNS**

8. "Relational database, 5x MySQL performance" → ?
   - **Answer: Aurora**

9. "Data warehouse, petabyte scale" → ?
   - **Answer: Redshift**

10. "CDN for global content delivery" → ?
    - **Answer: CloudFront**

**If you got 10/10 in under 50 seconds, you're ready! 🎯**

---

## 🏆 EXAM DAY MANTRAS

1. **Read the question twice** - Don't rush
2. **Look for keywords** - They point to specific services
3. **Eliminate wrong answers first** - Often easier than finding right one
4. **AWS favors:** Managed services, automation, security, cost-optimization
5. **When stuck:** Choose the most serverless/managed option
6. **Trust your first instinct** - If you studied, it's usually right
7. **Time management:** 130 minutes for 65 questions = 2 min per question
8. **Flag and move on** - Don't get stuck on hard questions

---

## 🎯 PANIC BUTTON (Use If Stuck on a Question)

1. **What's the domain?** (Security, Resilience, Performance, Cost)
2. **What's the core requirement?** (HA, DR, scaling, security, cost)
3. **What keywords are present?** (Match to service)
4. **Eliminate obviously wrong answers**
5. **Choose the most managed/serverless option**

---

## 📝 LAST-MINUTE MEMORY DUMP

**Write these down on scratch paper at exam start:**

```
Lambda: 15 min max
S3: 5 TB max object
SQS: 14 day max retention
DynamoDB: 400 KB max item
API Gateway: 29 sec timeout

Multi-AZ = HA (same region)
Multi-Region = DR (different regions)
Read Replica = Read scaling (async)

Security Group = Stateful, allow only, instance-level
NACL = Stateless, allow+deny, subnet-level

ALB = Layer 7 (HTTP)
NLB = Layer 4 (TCP/UDP)

CloudFront = Layer 7, caching
Global Accelerator = Layer 4, no caching

DR Tiers:
- Backup/Restore: Hours
- Pilot Light: Minutes-Hours
- Warm Standby: Minutes
- Multi-Site: Real-time
```

---

**You've got this! Trust your preparation. 🚀**

*Final tip: Stay calm, read carefully, and remember that most questions have obvious answers once you identify the key requirement and match it to the service pattern.*
