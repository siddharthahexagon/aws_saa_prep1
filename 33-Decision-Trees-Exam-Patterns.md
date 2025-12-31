# AWS SAA-C03: Exam Question Decision Trees - Pattern Recognition

## 🎯 Quick Reference for Exam Question Patterns

This file provides decision trees for recognizing question patterns and selecting the right answer based on keywords and scenarios.

---

## Keyword → Service Quick Reference

```
When you see...                    Think...
────────────────────────────────────────────────────────────────
"decouple"                      → SQS
"fanout" / "pub-sub"            → SNS
"event-driven"                  → EventBridge or Lambda
"workflow orchestration"        → Step Functions
"real-time streaming"           → Kinesis Data Streams
"load streaming data to S3"     → Kinesis Data Firehose
"query S3 with SQL"             → Athena
"data warehouse"                → Redshift
"cache DynamoDB"                → DAX
"cache database/objects"        → ElastiCache (Redis/Memcached)
"serverless compute"            → Lambda
"containers serverless"         → Fargate
"NoSQL, millisecond"            → DynamoDB
"relational, high performance"  → Aurora
"object storage"                → S3
"block storage"                 → EBS
"shared file system (Linux)"    → EFS
"shared file system (Windows)"  → FSx for Windows
"CDN/cache content"             → CloudFront
"route traffic globally"        → Route 53
"load balance HTTP"             → ALB
"load balance TCP/UDP"          → NLB
"VPN quick setup"               → Site-to-Site VPN
"dedicated network connection"  → Direct Connect
"threat detection"              → GuardDuty
"vulnerability scan"            → Inspector
"audit API calls"               → CloudTrail
"encryption keys"               → KMS
"SSL certificates"              → ACM
"secrets with rotation"         → Secrets Manager
"monitor metrics/logs"          → CloudWatch
"infrastructure as code"        → CloudFormation
"cost analysis"                 → Cost Explorer
"multi-account management"      → Organizations
"sensitive data discovery S3"   → Macie
"configuration compliance"      → Config
"migrate database"              → DMS
"large data transfer"           → Snowball
"hybrid file storage"           → Storage Gateway
```

---

## High Availability Questions

```
Question Pattern: "High availability" / "survive failure" / "fault tolerant"
│
├─→ Web Application HA?
│   └─→ **Answer**: Multi-AZ + Auto Scaling + ELB + Route 53
│
├─→ Database HA?
│   ├─→ RDS → **Multi-AZ deployment**
│   └─→ Aurora → **Built-in (6 copies, 3 AZs)**
│
├─→ Cache HA?
│   └─→ **ElastiCache Multi-AZ** (Redis)
│
├─→ Lambda HA?
│   └─→ **Built-in** (automatic Multi-AZ)
│
├─→ S3 HA?
│   └─→ **Built-in** (11 9's durability)
│
└─→ NAT Gateway HA?
    └─→ **One NAT Gateway per AZ**
```

---

## Disaster Recovery Questions

```
Question Pattern: "DR" / "region failure" / "RPO" / "RTO"
│
├─→ Lowest Cost DR?
│   └─→ **Backup and Restore** (RPO/RTO: hours-days)
│       └─→ S3 CRR, RDS snapshots, AWS Backup
│
├─→ Fast Recovery, Low Cost?
│   └─→ **Pilot Light** (RPO: minutes-hours, RTO: hours)
│       └─→ Core systems running, scale on disaster
│
├─→ Faster Recovery?
│   └─→ **Warm Standby** (RPO/RTO: minutes)
│       └─→ Scaled-down version always running
│
├─→ Near-Zero Downtime?
│   └─→ **Multi-Site Active-Active** (RPO/RTO: near-zero)
│       └─→ Full capacity in multiple regions
│
└─→ Cross-Region Database?
    ├─→ Aurora → **Aurora Global Database**
    └─→ DynamoDB → **Global Tables**
```

---

## Cost Optimization Questions

```
Question Pattern: "cost-effective" / "reduce costs" / "lowest cost"
│
├─→ EC2 Cost Reduction?
│   ├─→ Steady workload → **Reserved Instances / Savings Plans**
│   ├─→ Interruptible workload → **Spot Instances**
│   └─→ Over-provisioned → **Compute Optimizer** (right-sizing)
│
├─→ Storage Cost Reduction?
│   ├─→ Infrequent access S3 → **S3-IA or Glacier**
│   ├─→ Unknown access pattern → **S3 Intelligent-Tiering**
│   ├─→ EBS → **gp3** (cheaper than gp2)
│   └─→ Lifecycle policies → **Auto-transition to cheaper tiers**
│
├─→ Database Cost Reduction?
│   ├─→ Variable workload → **Aurora Serverless** or **DynamoDB On-Demand**
│   └─→ Steady workload → **Reserved Instances**
│
├─→ Data Transfer Cost Reduction?
│   ├─→ S3/DynamoDB access → **VPC Gateway Endpoints** (free)
│   └─→ Global content → **CloudFront** (reduce origin traffic)
│
└─→ Track Costs?
    └─→ **Cost Explorer** + **Budgets** + **Cost Allocation Tags**
```

---

## Security Questions

```
Question Pattern: "secure" / "protect" / "encrypt" / "access control"
│
├─→ EC2 Access to AWS Services?
│   └─→ **IAM Role** (NEVER access keys!)
│
├─→ User Authentication (Web/Mobile App)?
│   └─→ **Amazon Cognito**
│
├─→ Encryption at Rest?
│   ├─→ Simple → **AWS Managed Keys**
│   ├─→ Audit/control → **KMS Customer Managed Keys**
│   └─→ Compliance (FIPS 140-2 L3) → **CloudHSM**
│
├─→ Encryption in Transit?
│   └─→ **HTTPS/TLS** (ACM for certificates)
│
├─→ Store Secrets?
│   ├─→ Auto-rotation → **Secrets Manager**
│   └─→ Simple/free → **Parameter Store**
│
├─→ DDoS Protection?
│   ├─→ Basic (free) → **Shield Standard**
│   └─→ Advanced → **Shield Advanced**
│
├─→ Web Application Firewall?
│   └─→ **AWS WAF** (SQL injection, XSS)
│
├─→ Threat Detection?
│   └─→ **GuardDuty** (ML-based)
│
├─→ Vulnerability Scanning?
│   └─→ **Inspector** (EC2, containers, Lambda)
│
└─→ Audit API Calls?
    └─→ **CloudTrail** (all regions)
```

---

## Performance Questions

```
Question Pattern: "improve performance" / "reduce latency" / "faster"
│
├─→ Database Read Performance?
│   ├─→ RDS/Aurora → **Read Replicas**
│   ├─→ DynamoDB → **DAX** (microsecond latency)
│   └─→ General caching → **ElastiCache**
│
├─→ Global Content Delivery?
│   └─→ **CloudFront** (CDN)
│
├─→ Global Application Performance?
│   └─→ **Global Accelerator** (AWS network)
│
├─→ S3 Upload Performance?
│   └─→ **S3 Transfer Acceleration**
│
├─→ EBS Performance?
│   └─→ **io2 Block Express** (highest IOPS)
│
└─→ Lambda Cold Start?
    └─→ **Provisioned Concurrency**
```

---

## Decoupling Questions

```
Question Pattern: "decouple" / "asynchronous" / "loosely coupled"
│
├─→ Queue Messages?
│   └─→ **SQS**
│       ├─→ Standard: High throughput
│       └─→ FIFO: Ordering + exactly-once
│
├─→ Fanout to Multiple Consumers?
│   └─→ **SNS → Multiple SQS Queues**
│
├─→ Event Routing?
│   └─→ **EventBridge**
│
└─→ Workflow Orchestration?
    └─→ **Step Functions**
```

---

## Migration Questions

```
Question Pattern: "migrate" / "move to AWS" / "transfer"
│
├─→ Database Migration?
│   ├─→ Same engine → **DMS** only
│   └─→ Different engine → **SCT + DMS**
│
├─→ VM Migration?
│   └─→ **Application Migration Service (MGN)**
│
├─→ Large Data Transfer?
│   ├─→ < 10 TB → Network transfer
│   ├─→ 10-80 TB → **Snowball Edge**
│   └─→ > 1 PB → **Snowmobile**
│
├─→ Ongoing Data Sync?
│   └─→ **DataSync**
│
└─→ Hybrid Storage?
    └─→ **Storage Gateway**
```

---

## Serverless Questions

```
Question Pattern: "serverless" / "no infrastructure" / "managed"
│
├─→ Compute < 15 min?
│   └─→ **Lambda**
│
├─→ Containers without servers?
│   └─→ **Fargate**
│
├─→ API?
│   ├─→ Simple, cheap → **HTTP API**
│   └─→ Full features → **REST API**
│
├─→ Database?
│   ├─→ NoSQL → **DynamoDB**
│   └─→ Relational → **Aurora Serverless**
│
├─→ Query S3?
│   └─→ **Athena**
│
└─→ ETL?
    └─→ **Glue**
```

---

## Storage Questions

```
Question Pattern: Storage selection
│
├─→ Object Storage (files via HTTP)?
│   └─→ **S3**
│
├─→ Block Storage (EC2)?
│   └─→ **EBS**
│
├─→ Shared File System?
│   ├─→ Linux (NFS) → **EFS**
│   ├─→ Windows (SMB) → **FSx for Windows**
│   └─→ High-performance → **FSx for Lustre**
│
├─→ Archive (7+ years)?
│   └─→ **S3 Glacier Deep Archive**
│
└─→ Caching?
    └─→ **ElastiCache**
```

---

## Database Questions

```
Question Pattern: Database selection
│
├─→ Relational?
│   ├─→ High performance → **Aurora**
│   └─→ Specific engine (Oracle, SQL Server) → **RDS**
│
├─→ NoSQL Key-Value?
│   └─→ **DynamoDB**
│
├─→ Document (MongoDB)?
│   └─→ **DocumentDB**
│
├─→ Graph?
│   └─→ **Neptune**
│
├─→ Time-series?
│   └─→ **Timestream**
│
├─→ Ledger (immutable)?
│   └─→ **QLDB**
│
├─→ Data Warehouse?
│   └─→ **Redshift**
│
└─→ Caching?
    ├─→ DynamoDB → **DAX**
    └─→ General → **ElastiCache**
```

---

## Networking Questions

```
Question Pattern: Networking selection
│
├─→ Load Balancing?
│   ├─→ HTTP/HTTPS → **ALB**
│   ├─→ TCP/UDP → **NLB**
│   └─→ Security appliances → **GWLB**
│
├─→ DNS?
│   └─→ **Route 53**
│
├─→ CDN?
│   └─→ **CloudFront**
│
├─→ On-Premises Connection?
│   ├─→ Quick (days) → **Site-to-Site VPN**
│   └─→ Dedicated (weeks) → **Direct Connect**
│
├─→ Private Subnet Internet?
│   └─→ **NAT Gateway**
│
├─→ Private Access to AWS Services?
│   ├─→ S3/DynamoDB → **Gateway Endpoint** (free)
│   └─→ Other services → **Interface Endpoint**
│
└─→ Connect Multiple VPCs?
    └─→ **Transit Gateway**
```

---

## Common Exam Traps

```
❌ TRAP                              ✅ CORRECT
────────────────────────────────────────────────────────────────
RDS Multi-AZ = read scaling      → Multi-AZ = failover only
                                   Read Replicas = read scaling

Lambda for > 15 min              → Use Fargate/ECS/Batch

Access keys on EC2               → Use IAM Roles

Single NAT Gateway for HA        → One NAT per AZ for HA

CloudWatch = audit               → CloudWatch = monitoring
                                   CloudTrail = auditing

S3 Standard-IA = archive         → S3-IA = infrequent access
                                   Glacier = archive

Security Groups = deny rules     → Security Groups = allow only
                                   NACLs = allow and deny

Kinesis Streams = delivery       → Streams = processing
                                   Firehose = delivery

Spot for critical apps           → Spot = interruptible only

Direct Connect = instant setup   → VPN = quick (days)
                                   Direct Connect = weeks/months
```

---

## Answer Selection Strategy

```
1. READ CAREFULLY
   │
   ├─→ Identify PRIMARY requirement
   │   (cost, performance, HA, simplicity)
   │
   ├─→ Note constraints
   │   (time, budget, existing infrastructure)
   │
   └─→ Look for keywords
       (decouple, serverless, real-time, etc.)

2. ELIMINATE WRONG ANSWERS
   │
   ├─→ Remove services that don't fit scenario
   ├─→ Remove solutions violating constraints
   └─→ Remove obviously wrong options

3. CHOOSE BEST ANSWER
   │
   ├─→ AWS-managed > self-managed
   ├─→ Serverless when appropriate
   ├─→ Most secure option if security-related
   └─→ Most cost-effective if cost-related

4. COMMON RIGHT PATTERNS
   │
   ├─→ IAM Roles for AWS service access
   ├─→ Multi-AZ for high availability
   ├─→ Encryption enabled
   ├─→ Least privilege
   └─→ Decoupling with SQS/SNS
```

---

## Quick Service Comparison Reference

### SQS vs SNS vs Kinesis
| Feature | SQS | SNS | Kinesis |
|---------|-----|-----|---------|
| Pattern | Queue | Pub/sub | Stream |
| Consumer | Pull | Push | Pull |
| Retention | 14 days | No | 1-365 days |
| Use Case | Decouple | Fanout | Real-time |

### ALB vs NLB
| Feature | ALB | NLB |
|---------|-----|-----|
| Layer | 7 (HTTP) | 4 (TCP/UDP) |
| Routing | Path/host | Connection |
| Performance | Good | Excellent |
| Use Case | Web apps | Gaming, IoT |

### RDS vs Aurora
| Feature | RDS | Aurora |
|---------|-----|--------|
| Performance | Standard | 5x MySQL, 3x PostgreSQL |
| Replicas | 5 | 15 |
| Failover | 60-120 sec | < 30 sec |
| Storage | Up to 64 TB | Up to 128 TB |

### VPN vs Direct Connect
| Feature | VPN | Direct Connect |
|---------|-----|----------------|
| Setup | Days | Weeks-Months |
| Bandwidth | 1.25 Gbps | 1-100 Gbps |
| Latency | Variable | Consistent |
| Cost | Low | High |

---

## 🏆 Final Exam Tips

1. **Time Management**: ~2 minutes per question
2. **Flag and Move**: Don't get stuck, flag and return
3. **Read All Options**: Sometimes "All of the above" or "None" is correct
4. **Trust AWS-Native**: AWS prefers its managed services
5. **Security First**: When in doubt, choose more secure option
6. **Cost vs Performance**: Know which is being asked
7. **Keywords Matter**: They often point to specific services

**Good luck on your exam!** 🎉
