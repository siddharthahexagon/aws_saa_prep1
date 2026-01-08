# AWS SAA-C03 Exam Heuristics & Key Features - Quick Reference Guide

## 🎯 Purpose
This guide contains **decision heuristics**, **keyword patterns**, and **quick selection rules** to help you rapidly answer SAA-C03 exam questions correctly.

---

## Table of Contents
- [Exam Question Keywords & Service Mapping](#exam-question-keywords--service-mapping)
- [Service Selection Heuristics](#service-selection-heuristics)
- [Architecture Pattern Recognition](#architecture-pattern-recognition)
- [Cost Optimization Heuristics](#cost-optimization-heuristics)
- [Security Heuristics](#security-heuristics)
- [High Availability Heuristics](#high-availability-heuristics)
- [Performance Optimization Heuristics](#performance-optimization-heuristics)
- [Common Exam Traps & How to Avoid Them](#common-exam-traps--how-to-avoid-them)
- [Critical Service Limits to Memorize](#critical-service-limits-to-memorize)
- [Decision Flowcharts](#decision-flowcharts)

---

## 🔑 Exam Question Keywords & Service Mapping

### When You See These Keywords → Think This Service

#### Storage Keywords

| Keyword/Phrase | Service | Why |
|----------------|---------|-----|
| **"frequently accessed"** | S3 Standard | High performance, frequent access |
| **"infrequently accessed"** | S3 Standard-IA | Lower cost, instant retrieval |
| **"archive"**, **"long-term storage"** | S3 Glacier | Lowest cost for archival |
| **"12 hours retrieval"** | S3 Glacier Deep Archive | Longest retrieval, lowest cost |
| **"lifecycle"**, **"transition"** | S3 Lifecycle Policies | Automate storage class transitions |
| **"shared file storage"**, **"NFS"** | EFS | Linux shared file system |
| **"Windows file server"**, **"SMB"** | FSx for Windows | Windows native file system |
| **"high performance computing"**, **"HPC"** | FSx for Lustre | Maximum IOPS and throughput |
| **"boot volume"**, **"single instance"** | EBS | Block storage for EC2 |
| **"million IOPS"** | EBS io2 Block Express | Highest IOPS available |
| **"static website"** | S3 + CloudFront | Cost-effective static hosting |

#### Compute Keywords

| Keyword/Phrase | Service | Why |
|----------------|---------|-----|
| **"serverless"**, **"no servers"** | Lambda | No infrastructure management |
| **"15 minutes or less"** | Lambda | Max execution time 15 min |
| **"containers"**, **"Docker"** | ECS or EKS | Container orchestration |
| **"Kubernetes"** | EKS | Managed Kubernetes |
| **"no infrastructure"**, **"containers"** | Fargate | Serverless containers |
| **"quick deployment"**, **"developer"** | Elastic Beanstalk | PaaS solution |
| **"batch processing"**, **"no time limit"** | AWS Batch | Long-running batch jobs |
| **"spot instances"**, **"cost-effective"** | EC2 Spot | Up to 90% discount |
| **"steady-state"**, **"predictable"** | Reserved Instances | Up to 75% discount |
| **"workload orchestration"** | Step Functions | Coordinate multiple services |

#### Database Keywords

| Keyword/Phrase | Service | Why |
|----------------|---------|-----|
| **"relational"**, **"SQL"**, **"ACID"** | RDS or Aurora | Relational database |
| **"MySQL/PostgreSQL compatible"** + **"5x faster"** | Aurora | AWS proprietary, high performance |
| **"read-heavy workload"** | RDS Read Replicas or ElastiCache | Offload read traffic |
| **"sub-millisecond latency"** | DynamoDB + DAX | In-memory cache for DynamoDB |
| **"NoSQL"**, **"key-value"**, **"serverless"** | DynamoDB | Managed NoSQL |
| **"data warehouse"**, **"analytics"**, **"OLAP"** | Redshift | Petabyte-scale analytics |
| **"graph database"**, **"relationships"** | Neptune | Graph data model |
| **"MongoDB compatible"** | DocumentDB | Managed document database |
| **"in-memory cache"**, **"session store"** | ElastiCache | Redis or Memcached |
| **"sorted sets"**, **"leaderboards"** | ElastiCache Redis | Redis data structures |
| **"multi-threaded"**, **"simple cache"** | ElastiCache Memcached | Simple key-value cache |
| **"immutable"**, **"audit trail"** | QLDB | Ledger database |

#### Networking Keywords

| Keyword/Phrase | Service | Why |
|----------------|---------|-----|
| **"low latency"**, **"global users"** | CloudFront | CDN caching at edge |
| **"static IP"**, **"failover < 30 sec"** | Global Accelerator | Static Anycast IPs |
| **"private connection"**, **"dedicated"** | Direct Connect | Dedicated network link |
| **"VPN"**, **"encrypted tunnel"** | Site-to-Site VPN | IPsec over internet |
| **"Layer 7"**, **"HTTP/HTTPS"**, **"path-based"** | Application Load Balancer | Advanced HTTP routing |
| **"Layer 4"**, **"TCP/UDP"**, **"static IP"** | Network Load Balancer | Ultra-low latency |
| **"third-party appliances"**, **"firewall"** | Gateway Load Balancer | Virtual appliance scaling |
| **"geolocation"**, **"route based on location"** | Route 53 Geolocation | Location-based routing |
| **"lowest latency"** | Route 53 Latency | Performance-based routing |
| **"failover"**, **"active-passive"** | Route 53 Failover | DR routing |
| **"connect multiple VPCs"**, **"transitive"** | Transit Gateway | Hub-and-spoke networking |
| **"private service access"** | PrivateLink | VPC-to-VPC without internet |

#### Security Keywords

| Keyword/Phrase | Service | Why |
|----------------|---------|-----|
| **"encryption keys"**, **"managed keys"** | KMS | Managed encryption keys |
| **"FIPS 140-2 Level 3"**, **"dedicated HSM"** | CloudHSM | Hardware security module |
| **"rotate secrets"**, **"database credentials"** | Secrets Manager | Automatic rotation |
| **"configuration"**, **"secrets"**, **"free"** | Parameter Store | Cheaper than Secrets Manager |
| **"SSL/TLS certificates"**, **"free"** | ACM | Managed certificates |
| **"DDoS protection"**, **"automatic"** | Shield Standard | Free DDoS protection |
| **"24/7 DDoS support"**, **"Layer 7"** | Shield Advanced | Enhanced DDoS protection |
| **"web application firewall"**, **"SQL injection"** | WAF | Layer 7 protection |
| **"threat detection"**, **"ML"**, **"VPC Flow Logs"** | GuardDuty | Intelligent threat detection |
| **"vulnerability scanning"**, **"CVE"** | Inspector | Automated security assessment |
| **"sensitive data"**, **"PII"**, **"S3"** | Macie | Data classification |
| **"compliance"**, **"configuration tracking"** | Config | Configuration history |
| **"API logging"**, **"audit"** | CloudTrail | Account activity logging |
| **"centralized security"**, **"multi-account"** | Security Hub | Aggregated security findings |

#### Analytics & Integration Keywords

| Keyword/Phrase | Service | Why |
|----------------|---------|-----|
| **"real-time streaming"**, **"replay"** | Kinesis Data Streams | Real-time with replay |
| **"near real-time"**, **"load to S3"** | Kinesis Data Firehose | Near real-time ETL |
| **"SQL on S3"**, **"serverless query"** | Athena | Interactive queries |
| **"ETL"**, **"data catalog"**, **"serverless"** | Glue | Serverless ETL |
| **"Hadoop"**, **"Spark"**, **"big data"** | EMR | Managed Hadoop framework |
| **"decouple"**, **"queue"**, **"message"** | SQS | Message queue |
| **"pub/sub"**, **"multiple subscribers"** | SNS | Publish-subscribe |
| **"fan-out"**, **"parallel processing"** | SNS + SQS | Multiple queue pattern |
| **"workflow"**, **"coordinate services"** | Step Functions | Serverless orchestration |
| **"event-driven"**, **"scheduled"** | EventBridge | Event bus service |
| **"search"**, **"log analytics"**, **"ELK"** | OpenSearch | Search and analytics |
| **"BI"**, **"dashboards"**, **"visualization"** | QuickSight | Business intelligence |
| **"Apache Kafka"** | MSK | Managed Kafka |

#### Migration Keywords

| Keyword/Phrase | Service | Why |
|----------------|---------|-----|
| **"migrate servers"**, **"lift-and-shift"** | MGN (Application Migration Service) | Automated server migration |
| **"migrate database"**, **"minimal downtime"** | DMS | Database migration |
| **"different database engines"** | DMS + SCT | Heterogeneous migration |
| **"online file transfer"**, **"fast"** | DataSync | Up to 10 Gbps transfer |
| **"FTP/SFTP to S3"** | Transfer Family | Managed FTP service |
| **"offline transfer"**, **"large data"** | Snow Family | Physical data transport |
| **"< 10 TB offline"** | Snowcone | Smallest Snow device |
| **"10-80 TB offline"** | Snowball Edge | Most common Snow device |
| **"> 10 PB offline"** | Snowmobile | Exabyte-scale transfer |
| **"track migration"** | Migration Hub | Central migration tracking |

---

## 🎯 Service Selection Heuristics

### Rule #1: Managed Service > Self-Managed
**If both options exist and cost isn't explicitly mentioned as constraint:**
- ✅ Choose RDS over EC2 + database
- ✅ Choose ELB over EC2 with load balancing software
- ✅ Choose Lambda over EC2 for event-driven tasks
- ✅ Choose ECS/Fargate over EC2 for containers

### Rule #2: Serverless > Server-Based (When Appropriate)
**For variable, unpredictable, or event-driven workloads:**
- ✅ Lambda over EC2
- ✅ DynamoDB over self-managed NoSQL
- ✅ Fargate over ECS on EC2
- ✅ Aurora Serverless over Aurora provisioned

### Rule #3: Multi-AZ by Default for Production
**High availability is almost always required:**
- ✅ RDS Multi-AZ
- ✅ ELB across multiple AZs
- ✅ Aurora (automatically Multi-AZ)
- ✅ S3 (automatically Multi-AZ)

### Rule #4: Read Replicas for Read-Heavy Workloads
**When "read-heavy", "reporting", "analytics" mentioned:**
- ✅ RDS Read Replicas
- ✅ Aurora Read Replicas (up to 15)
- ✅ ElastiCache
- ✅ DynamoDB DAX

### Rule #5: Cheapest Storage for Each Access Pattern
**Match storage class to access pattern:**
- **Daily/frequent** → S3 Standard
- **Weekly/monthly** → S3 Standard-IA
- **Quarterly/yearly** → S3 Glacier Flexible Retrieval
- **Compliance/rarely** → S3 Glacier Deep Archive
- **Unknown** → S3 Intelligent-Tiering

### Rule #6: CloudFront for Global Distribution
**When "global", "low latency", "edge locations" mentioned:**
- ✅ CloudFront for content delivery
- ✅ Global Accelerator for non-HTTP protocols
- ✅ Route 53 Latency routing for DNS

### Rule #7: Spot Instances for Fault-Tolerant Workloads
**When cost optimization + fault tolerance mentioned:**
- ✅ Spot for batch processing
- ✅ Spot for big data (EMR)
- ✅ Spot for containerized apps
- ❌ NOT for databases or critical apps

### Rule #8: VPC Peering vs Transit Gateway
**Connecting VPCs:**
- **2-3 VPCs** → VPC Peering (simpler, cheaper)
- **4+ VPCs** → Transit Gateway (scales better)
- **Transitive routing needed** → Transit Gateway

### Rule #9: Security Group vs NACL
**Security layers:**
- **Instance level, stateful** → Security Groups (primary)
- **Subnet level, stateless** → NACLs (secondary, defense in depth)
- **Deny rules needed** → NACLs (SGs only allow)

### Rule #10: S3 vs EBS vs EFS
**Storage selection:**
- **Object storage, web access** → S3
- **Block storage, single EC2** → EBS
- **Shared file storage, Linux** → EFS
- **Shared file storage, Windows** → FSx for Windows

---

## 🏗️ Architecture Pattern Recognition

### Pattern 1: Three-Tier Web Application
**Classic Architecture:**
```
Users → CloudFront → ALB → EC2 Auto Scaling (App Tier) → RDS Multi-AZ
                                ↓
                         ElastiCache (Session Store)
```

**Key Components:**
- CloudFront for global delivery
- ALB for Layer 7 routing
- Auto Scaling for elasticity
- RDS Multi-AZ for high availability
- ElastiCache for session management

**Exam Tip:** This is the most common architecture pattern. If question describes web app with global users and database, think this pattern.

---

### Pattern 2: Serverless Application
**Serverless Architecture:**
```
Users → API Gateway → Lambda → DynamoDB
                         ↓
                    S3 (static content)
                         ↓
                    CloudFront
```

**Key Components:**
- API Gateway for API management
- Lambda for compute
- DynamoDB for data persistence
- S3 + CloudFront for static content

**Exam Tip:** Look for "serverless", "no servers", "pay per request", "event-driven"

---

### Pattern 3: Data Analytics Pipeline
**Analytics Architecture:**
```
Sources → Kinesis Data Streams → Lambda → S3 → Athena → QuickSight
                    ↓
          Kinesis Data Firehose → Redshift
                    ↓
                  Glue ETL
```

**Key Components:**
- Kinesis for streaming ingestion
- Lambda for real-time processing
- S3 for data lake
- Glue for ETL
- Athena for ad-hoc queries
- Redshift for data warehouse
- QuickSight for visualization

**Exam Tip:** Look for "real-time", "analytics", "data lake", "streaming"

---

### Pattern 4: Hybrid Cloud
**Hybrid Architecture:**
```
On-Premises ←→ Direct Connect / VPN ←→ VPC
                                         ↓
                                    Private Subnet
                                         ↓
                                   AWS Services
```

**Key Components:**
- Direct Connect or VPN for connectivity
- Storage Gateway for hybrid storage
- DataSync for data transfer
- Systems Manager for hybrid management

**Exam Tip:** Look for "on-premises", "hybrid", "datacenter", "extend to cloud"

---

### Pattern 5: Event-Driven Architecture
**Event-Driven Pattern:**
```
Source → EventBridge → Lambda / SQS / Step Functions → Targets
   ↓
S3 Events / DynamoDB Streams / CloudWatch Events
```

**Key Components:**
- EventBridge for event routing
- SQS for decoupling
- SNS for fan-out
- Step Functions for orchestration
- Lambda for event processing

**Exam Tip:** Look for "decouple", "event-driven", "asynchronous", "microservices"

---

### Pattern 6: Disaster Recovery
**DR Patterns (Cheapest to Most Expensive):**

#### Backup & Restore (Cheapest)
- Backup to S3/Glacier
- Restore when needed
- RTO: Hours/Days, RPO: Hours
- Cost: $

#### Pilot Light
- Core services running at minimum
- Scale up when needed
- RTO: Hours, RPO: Minutes
- Cost: $$

#### Warm Standby
- Scaled-down version running
- Scale up for DR
- RTO: Minutes, RPO: Seconds
- Cost: $$$

#### Multi-Site Active/Active (Most Expensive)
- Full production in multiple regions
- Route 53 routes traffic
- RTO: Near-zero, RPO: Near-zero
- Cost: $$$$

**Exam Tip:** Match DR strategy to RTO/RPO requirements and budget

---

## 💰 Cost Optimization Heuristics

### Storage Cost Optimization

#### Rule: Move to Colder Storage Based on Access
```
Frequent → Standard ($$$)
↓
Infrequent → Standard-IA ($$)
↓
Rare → Glacier Flexible ($)
↓
Compliance → Glacier Deep Archive ($)
```

**Implementation:**
- Use S3 Lifecycle Policies
- Use S3 Intelligent-Tiering for unknown patterns
- Delete incomplete multipart uploads
- Enable S3 Analytics for insights

---

### Compute Cost Optimization

#### EC2 Purchasing Decision Tree
```
Workload Type?
├─ Steady, predictable → Reserved Instances (75% discount)
├─ Flexible, interruptible → Spot Instances (90% discount)
├─ Variable with baseline → Savings Plans
└─ Short-term, unpredictable → On-Demand
```

**Additional Optimization:**
- Right-size instances (Compute Optimizer)
- Use Auto Scaling (scale down when idle)
- Use Lambda for intermittent workloads
- Use Fargate for containers (no idle capacity)

---

### Database Cost Optimization

#### Database Cost Hierarchy (Expensive → Cheap)
1. **RDS Multi-AZ with Provisioned IOPS** → $$$$
2. **RDS Multi-AZ with GP3** → $$$
3. **RDS Single-AZ** → $$
4. **Aurora Serverless** → $$ (variable)
5. **DynamoDB On-Demand** → $$ (variable)
6. **DynamoDB Provisioned** → $ (if predictable)

**Optimization Tips:**
- Use read replicas instead of scaling up
- Use ElastiCache to reduce database load
- DynamoDB: Use provisioned for predictable workloads
- Aurora Serverless for variable workloads
- Delete old snapshots

---

### Data Transfer Cost Optimization

#### Cost Hierarchy (Expensive → Cheap)
1. **Internet Out** → $$$
2. **Cross-Region** → $$
3. **Cross-AZ** → $
4. **Within AZ** → FREE

**Optimization Strategies:**
- Use CloudFront (cheaper than direct S3)
- Keep resources in same AZ when possible
- Use VPC endpoints (no internet charges)
- Use Direct Connect for large, ongoing transfers
- Use S3 Transfer Acceleration selectively

---

## 🔐 Security Heuristics

### Encryption Decision Matrix

| Data State | AWS Service | Encryption Method |
|-------------|-------------|-------------------|
| **At Rest** | S3 | SSE-S3, SSE-KMS, SSE-C |
| **At Rest** | EBS | KMS |
| **At Rest** | RDS | KMS |
| **At Rest** | DynamoDB | KMS |
| **In Transit** | All Services | HTTPS/TLS |
| **Client-Side** | Any | Encrypt before upload |

**Exam Heuristics:**
- **"Encryption at rest"** → Enable KMS encryption
- **"Customer managed keys"** → KMS Customer Managed Keys
- **"Rotate keys automatically"** → KMS automatic rotation
- **"FIPS 140-2 Level 3"** → CloudHSM
- **"Client-side encryption"** → Encrypt before upload to AWS

---

### IAM Best Practices Quick Check

✅ **DO:**
- Use IAM roles for EC2/Lambda (NOT access keys)
- Enable MFA for privileged users
- Use least privilege principle
- Use IAM groups for permission management
- Rotate credentials regularly
- Use temporary credentials (STS)
- Enable CloudTrail for auditing

❌ **DON'T:**
- Use root account for daily tasks
- Share credentials
- Hardcode credentials in code
- Use broad permissions (e.g., *)
- Leave unused credentials active

**Exam Tip:** If question involves EC2 accessing S3, answer is IAM role, NOT access keys

---

### Network Security Layering

**Defense in Depth Pattern:**
```
Layer 1: Route 53 (DDoS with Shield)
Layer 2: CloudFront (WAF rules, geo-blocking)
Layer 3: ALB/NLB (Security Groups, SSL termination)
Layer 4: VPC (NACLs at subnet level)
Layer 5: EC2 (Security Groups, OS hardening)
Layer 6: Application (WAF, authentication)
Layer 7: Data (Encryption, access control)
```

**Exam Heuristics:**
- **Security Groups** = Primary security control (stateful)
- **NACLs** = Secondary/defense in depth (stateless)
- **WAF** = Layer 7 attack protection
- **Shield** = DDoS protection
- **GuardDuty** = Threat detection

---

## 🔄 High Availability Heuristics

### Multi-AZ vs Multi-Region Decision

#### Use Multi-AZ When:
- ✅ High availability within region required
- ✅ RTO: Minutes, RPO: Near-zero
- ✅ Cost-conscious
- ✅ Most common requirement

#### Use Multi-Region When:
- ✅ Disaster recovery across regions
- ✅ Global user base (low latency)
- ✅ Data sovereignty requirements
- ✅ RTO: Near-zero, RPO: Seconds

---

### Load Balancer Selection

```
Traffic Type?
├─ HTTP/HTTPS
│   ├─ Path/host routing needed → ALB
│   ├─ Lambda targets → ALB
│   └─ WebSocket support → ALB
├─ TCP/UDP/TLS
│   ├─ Extreme performance → NLB
│   ├─ Static IP needed → NLB
│   └─ Preserve source IP → NLB
└─ Third-party appliances → GWLB
```

---

### Auto Scaling Configuration

**Target Tracking Policy (Recommended):**
- Track metric (CPU, requests, custom)
- AWS handles scaling
- Example: Keep CPU at 70%

**Step Scaling Policy:**
- Multiple thresholds
- Different scaling amounts
- More control, more complex

**Scheduled Scaling:**
- Predictable traffic patterns
- Scale before load arrives

**Exam Tip:** Target tracking is simplest and most common

---

## ⚡ Performance Optimization Heuristics

### Caching Strategy

**Multi-Layer Caching:**
```
User Request
    ↓
1. CloudFront (Edge Cache) ← Closest to user
    ↓ (Cache Miss)
2. ElastiCache (Application Cache) ← Fast in-memory
    ↓ (Cache Miss)
3. Database Read Replica ← Offload primary
    ↓ (No replica)
4. Primary Database ← Last resort
```

**Exam Heuristics:**
- **"Global users"** → CloudFront
- **"Microsecond latency"** → ElastiCache Redis or DAX
- **"Session store"** → ElastiCache
- **"Read-heavy"** → Read replicas + ElastiCache
- **"DynamoDB + faster"** → DAX

---

### Database Performance Optimization

#### RDS/Aurora Performance
1. **Read Replicas** → Offload reads (up to 15 for Aurora)
2. **ElastiCache** → Cache frequently accessed data
3. **Provisioned IOPS** → Consistent performance
4. **Instance size** → Right-size based on workload
5. **Connection pooling** → Reduce connection overhead

#### DynamoDB Performance
1. **DAX** → Microsecond latency
2. **Partition key design** → Even distribution
3. **GSI** → Query flexibility
4. **Auto Scaling** → Handle variable load
5. **Batch operations** → Reduce requests

**Exam Tip:** Always consider caching before scaling up database

---

### Compute Performance Optimization

#### EC2 Instance Selection
```
Workload Type → Instance Family
├─ Balanced → T3, M5, M6i
├─ Compute intensive → C5, C6i, C7g
├─ Memory intensive → R5, R6i, X2
├─ Storage intensive → I3, I4i, D3
└─ GPU/ML → P4, G4, Inf1
```

**Placement Groups:**
- **Cluster** → Low latency, HPC (same AZ)
- **Spread** → Critical instances (separate hardware)
- **Partition** → Distributed systems (separate racks)

---

## 🚨 Common Exam Traps & How to Avoid Them

### Trap #1: Over-Engineering
**Scenario:** Question has simple requirement, complex answer looks impressive
**Solution:** Choose simplest option that meets ALL requirements

**Example:**
- ❌ "Use Lambda + Step Functions + SQS + SNS + DynamoDB"
- ✅ "Use Lambda + S3" (if that's all that's needed)

---

### Trap #2: Ignoring Cost Constraint
**Scenario:** "Most cost-effective" in question, expensive solution in answer
**Solution:** Always prioritize cost when mentioned

**Example:**
- Question: "Most cost-effective solution for infrequent backups"
- ❌ S3 Standard
- ✅ S3 Glacier

---

### Trap #3: Missing Multi-AZ Requirement
**Scenario:** "High availability" mentioned, single-AZ solution offered
**Solution:** Production = Multi-AZ by default

**Example:**
- ❌ RDS Single-AZ
- ✅ RDS Multi-AZ (automatic failover)

---

### Trap #4: Choosing Self-Managed Over Managed
**Scenario:** EC2 + software vs managed service, no cost constraint
**Solution:** Managed service unless specific reason

**Example:**
- ❌ EC2 + MySQL
- ✅ RDS MySQL (automated backups, patching, Multi-AZ)

---

### Trap #5: Wrong Security Service
**Scenario:** Mixing up similar security services
**Solution:** Learn specific use cases

**Common Confusion:**
- **GuardDuty** = Threat DETECTION (finds threats)
- **Inspector** = Vulnerability SCANNING (finds CVEs)
- **Macie** = Data CLASSIFICATION (finds PII)
- **Config** = Configuration COMPLIANCE (tracks changes)
- **CloudTrail** = API LOGGING (who did what)

---

### Trap #6: Serverless Time Limit
**Scenario:** Long-running task + Lambda suggested
**Solution:** Lambda max 15 minutes

**Example:**
- Question: "Process video files taking 30 minutes"
- ❌ Lambda
- ✅ ECS, Batch, EC2, or Step Functions

---

### Trap #7: Mixing Up RTO and RPO
**Scenario:** Confusing recovery time vs data loss

**Remember:**
- **RTO (Recovery Time Objective)** = How long to recover
- **RPO (Recovery Point Objective)** = How much data loss acceptable

**Example:**
- "Must recover within 1 hour" → RTO = 1 hour
- "Can lose up to 4 hours of data" → RPO = 4 hours

---

### Trap #8: Wrong Load Balancer Type
**Scenario:** Using ALB when NLB needed (or vice versa)

**Quick Check:**
- **ALB**: HTTP/HTTPS, path/host routing, Lambda targets
- **NLB**: TCP/UDP, static IP, extreme performance, source IP preservation
- **GWLB**: Third-party virtual appliances

---

### Trap #9: S3 Consistency Misunderstanding
**Scenario:** Old exam knowledge (eventual consistency)
**Solution:** S3 is now STRONGLY CONSISTENT for all operations

**Modern S3:**
- ✅ Strong read-after-write consistency
- ✅ No more eventual consistency concerns
- ✅ Applies to all requests (new or overwrite)

---

### Trap #10: VPC Peering Transitive Routing
**Scenario:** Assuming A-B, B-C means A-C
**Solution:** VPC Peering is NOT transitive

**Example:**
- VPC-A peers with VPC-B
- VPC-B peers with VPC-C
- VPC-A CANNOT reach VPC-C
- Need: A-C peering OR Transit Gateway

---

## 📊 Critical Service Limits to Memorize

### Compute Limits

| Service | Limit | Important For |
|---------|-------|---------------|
| Lambda execution time | **15 minutes** | Long-running tasks |
| Lambda memory | **10 GB max** | Memory-intensive |
| Lambda package size | **50 MB zipped, 250 MB unzipped** | Large dependencies |
| Lambda /tmp storage | **512 MB - 10 GB** | Temporary storage |
| Lambda concurrent executions | **1,000 (default)** | High traffic |
| EC2 On-Demand instances | **20 per region (default)** | Scaling limits |

### Storage Limits

| Service | Limit | Important For |
|---------|-------|---------------|
| S3 object size | **5 TB max** | Large files |
| S3 PUT size | **5 GB max (use multipart > 100 MB)** | Uploads |
| S3 bucket name | **Globally unique** | Naming conflicts |
| S3 buckets per account | **100 (soft limit, 1000 hard)** | Multi-bucket designs |
| EBS volume size | **64 TB max** | Large volumes |
| EFS file size | **52.6 TB max** | Large files |

### Database Limits

| Service | Limit | Important For |
|---------|-------|---------------|
| RDS storage | **64 TB max (except SQL Server)** | Large databases |
| Aurora storage | **128 TB max** | Very large databases |
| DynamoDB item size | **400 KB max** | Item design |
| DynamoDB partition key length | **2048 bytes max** | Key design |
| RDS Read Replicas | **5 per instance (15 for Aurora)** | Read scaling |

### Networking Limits

| Service | Limit | Important For |
|---------|-------|---------------|
| VPC per region | **5 (soft limit)** | Multi-VPC designs |
| Subnets per VPC | **200** | Subnet design |
| Security Groups per VPC | **2,500** | Security design |
| Rules per Security Group | **60 inbound, 60 outbound** | Complex rules |
| VPC CIDR blocks | **5 per VPC** | IP planning |
| Route 53 health checks | **200 per account** | Failover design |

---

## 🎨 Decision Flowcharts

### Flowchart 1: Storage Service Selection
```
Need to store data?
    ↓
Object or Block or File?
    ↓
┌─────────────┬──────────────┬─────────────┐
│   OBJECT    │    BLOCK     │    FILE     │
└─────────────┴──────────────┴─────────────┘
      ↓              ↓              ↓
  Access Pattern  Single/Multi?  Protocol?
      ↓              ↓              ↓
┌─────────────┬─────────────┬──────────────┐
│  Frequent   │   Single    │   NFS/POSIX  │
│  → S3 Std   │   → EBS     │   → EFS      │
│             │             │              │
│ Infrequent  │   Multi     │   SMB        │
│  → S3 IA    │   → EFS     │   → FSx Win  │
│             │             │              │
│  Archive    │  High Perf  │   HPC        │
│  → Glacier  │   → io2     │   → FSx Lus  │
└─────────────┴─────────────┴──────────────┘
```

---

### Flowchart 2: Database Service Selection
```
Need database?
    ↓
SQL or NoSQL?
    ↓
┌─────────────────────┬─────────────────────┐
│        SQL          │       NoSQL         │
└─────────────────────┴─────────────────────┘
         ↓                      ↓
    OLTP or OLAP?         Data Model?
         ↓                      ↓
┌─────────────────┐   ┌──────────────────┐
│      OLTP       │   │   Key-Value      │
│  (Transactional)│   │   → DynamoDB     │
│        ↓        │   │                  │
│  Performance?   │   │   Document       │
│        ↓        │   │   → DocumentDB   │
│  Standard → RDS │   │                  │
│  Fast → Aurora  │   │   Graph          │
│                 │   │   → Neptune      │
│      OLAP       │   │                  │
│   (Analytics)   │   │   Wide-Column    │
│   → Redshift    │   │   → Keyspaces    │
└─────────────────┘   └──────────────────┘
```

---

### Flowchart 3: Compute Service Selection
```
Need compute?
    ↓
Duration of task?
    ↓
┌──────────────────┬─────────────────┬───────────────────┐
│  < 15 minutes    │  Variable       │  Continuous       │
│  Event-driven    │  Workload       │  Workload         │
│  → Lambda        │                 │                   │
└──────────────────┴─────────────────┴───────────────────┘
                           ↓                 ↓
                     Containerized?    Infrastructure?
                           ↓                 ↓
                   ┌───────────────┐  ┌──────────────┐
                   │      YES      │  │  Full Control│
                   │  → ECS/EKS    │  │  → EC2       │
                   │               │  │              │
                   │  Manage nodes?│  │  Quick Deploy│
                   │  No→ Fargate  │  │  → Beanstalk │
                   │  Yes→ EC2     │  │              │
                   └───────────────┘  │  Batch Jobs  │
                                      │  → AWS Batch │
                                      └──────────────┘
```

---

### Flowchart 4: High Availability Design
```
Need HA?
    ↓
Single Region or Multi-Region?
    ↓
┌─────────────────────────┬─────────────────────────┐
│    SINGLE REGION        │    MULTI-REGION         │
│    (Most Common)        │    (Best HA/DR)         │
└─────────────────────────┴─────────────────────────┘
         ↓                           ↓
   Multi-AZ Deployment        Route 53 Routing
         ↓                           ↓
┌─────────────────────┐    ┌──────────────────────┐
│ • ALB across AZs    │    │ • Failover Routing   │
│ • Auto Scaling      │    │ • Latency Routing    │
│ • RDS Multi-AZ      │    │ • Geolocation        │
│ • Aurora (auto)     │    │ • Health Checks      │
│ • ELB health checks │    │ • Active-Active      │
│ • S3 (auto)         │    │ • Active-Passive     │
└─────────────────────┘    └──────────────────────┘
```

---

### Flowchart 5: Security Implementation
```
Implement Security?
    ↓
Layer?
    ↓
┌──────────┬───────────┬───────────┬───────────┐
│ Network  │   Data    │  Identity │ Detection │
└──────────┴───────────┴───────────┴───────────┘
     ↓          ↓           ↓           ↓
┌──────────┬──────────┬──────────┬──────────┐
│ • VPC    │ • KMS    │ • IAM    │ • GDuty  │
│ • SG     │ • Encrypt│ • Cognito│ • Inspctr│
│ • NACL   │ • S3 Enc │ • MFA    │ • Macie  │
│ • WAF    │ • EBS Enc│ • Roles  │ • Config │
│ • Shield │ • RDS Enc│ • Policies│• CTrail │
└──────────┴──────────┴──────────┴──────────┘
```

---

## 🎯 Final Exam Strategy

### Time Management
- **2 minutes per question** average
- **Flag difficult questions** → return later
- **Don't obsess** → move on if stuck
- **Review flagged** questions if time permits

### Question Analysis Framework

#### Step 1: Identify Question Type
- [ ] Architecture design
- [ ] Service selection
- [ ] Troubleshooting
- [ ] Cost optimization
- [ ] Security implementation
- [ ] Performance improvement

#### Step 2: Extract Requirements
- [ ] **MUST have** (non-negotiable)
- [ ] **SHOULD have** (desired)
- [ ] **Constraints** (cost, time, complexity)

#### Step 3: Eliminate Wrong Answers
- [ ] Doesn't meet MUST requirements
- [ ] Wrong service for use case
- [ ] Violates best practices
- [ ] Over-engineered solution

#### Step 4: Select Best Answer
- [ ] Meets all requirements
- [ ] Follows AWS best practices
- [ ] Simplest viable solution
- [ ] Most cost-effective (if mentioned)

---

### Answer Selection Priorities

When multiple answers seem correct, prioritize in this order:

1. **Meets all explicit requirements** ✅
2. **Managed service over self-managed** ✅
3. **Serverless over server-based** (when appropriate) ✅
4. **Multi-AZ for production** ✅
5. **Least operational overhead** ✅
6. **Most cost-effective** (if cost mentioned) ✅
7. **Simplest solution** ✅

---

## 📚 Must-Know Comparisons

### 1. RDS vs Aurora vs DynamoDB

| Feature | RDS | Aurora | DynamoDB |
|---------|-----|--------|----------|
| **Type** | Relational | Relational | NoSQL |
| **Performance** | Good | 5x MySQL | Excellent |
| **Scaling** | Vertical + Read Replicas | Vertical + 15 Replicas | Auto (Horizontal) |
| **Availability** | Multi-AZ (manual) | Multi-AZ (auto) | Multi-AZ (auto) |
| **Use Case** | Standard SQL | High-perf SQL | Key-value, scale |
| **Cost** | $$ | $$$ | $ (variable) |

---

### 2. S3 Storage Classes

| Class | Latency | Cost | Use Case |
|-------|---------|------|----------|
| **Standard** | ms | $$$ | Frequent access |
| **Intelligent-Tiering** | ms | $$ (auto) | Unknown pattern |
| **Standard-IA** | ms | $$ | Infrequent access |
| **One Zone-IA** | ms | $ | Non-critical IA |
| **Glacier Instant** | ms | $ | Archive w/ instant |
| **Glacier Flexible** | min-hrs | $ | Archive |
| **Glacier Deep** | 12 hrs | $ | Long-term archive |

---

### 3. Load Balancers

| Feature | ALB | NLB | GWLB |
|---------|-----|-----|------|
| **Layer** | 7 (HTTP) | 4 (TCP) | 3 (Network) |
| **Performance** | Good | Excellent | Good |
| **Static IP** | No | Yes | No |
| **Path routing** | Yes | No | No |
| **WebSocket** | Yes | Yes | No |
| **Use Case** | Web apps | TCP/UDP, perf | Appliances |

---

### 4. Kinesis Services

| Service | Latency | Storage | Replay | Management |
|---------|---------|---------|--------|------------|
| **Data Streams** | Real-time | 1-365 days | Yes | Manual shards |
| **Data Firehose** | Near real-time | None | No | Auto-scale |
| **Data Analytics** | Real-time | None | N/A | Serverless |
| **Video Streams** | Real-time | Varies | Yes | Managed |

---

## 🎓 Exam Day Quick Checklist

### Before Exam
- [ ] Review all service definitions
- [ ] Memorize critical limits
- [ ] Practice scenario questions
- [ ] Sleep well (8 hours)
- [ ] Eat proper breakfast
- [ ] Arrive 15 minutes early

### During Exam
- [ ] Read questions carefully (twice)
- [ ] Identify keywords (underline mentally)
- [ ] Eliminate wrong answers first
- [ ] Flag difficult questions
- [ ] Manage time (2 min/question)
- [ ] Review flagged questions
- [ ] Check all answers before submit

### Answer Selection Mantras
1. **"Simplest solution that meets requirements"**
2. **"Managed over self-managed"**
3. **"Multi-AZ for production"**
4. **"Match service to use case"**
5. **"Cost matters when mentioned"**

---

## 🏆 Final Power Tips

### Top 10 Exam Success Factors

1. **Read the entire question** before looking at answers
2. **Identify the service category** first (compute, storage, database, etc.)
3. **Look for constraint keywords** (cost, performance, simple, secure)
4. **Eliminate obviously wrong** answers immediately
5. **Watch for "most", "best", "least"** in questions
6. **Multi-AZ is almost always** correct for production
7. **Managed services** are preferred in AWS
8. **Serverless when appropriate** (variable workloads)
9. **Security Group = first choice** for security (stateful)
10. **When in doubt, choose the AWS-native** solution

---

### Common Question Patterns

#### Pattern: "Most cost-effective solution"
→ Look for: S3 Intelligent-Tiering, Spot Instances, Reserved Instances, Serverless, Auto Scaling

#### Pattern: "Minimal operational overhead"
→ Look for: Managed services, Serverless, Auto-scaling, Multi-AZ (automatic)

#### Pattern: "High availability"
→ Look for: Multi-AZ, Auto Scaling, Load Balancers, Multiple regions, Read replicas

#### Pattern: "Lowest latency for global users"
→ Look for: CloudFront, Global Accelerator, Route 53 Latency routing, Multi-region deployment

#### Pattern: "Real-time processing"
→ Look for: Kinesis Data Streams, Lambda, DynamoDB Streams, EventBridge

#### Pattern: "Decouple components"
→ Look for: SQS, SNS, EventBridge, Step Functions

#### Pattern: "Secure data at rest"
→ Look for: KMS encryption, Enable encryption on service, S3 encryption

---

**Remember:** You need **720/1000 (72%)** to pass. You don't need perfection!

**Good luck on your AWS SAA-C03 exam! 🚀**

---

*Last Updated: January 2026 - SAA-C03 Exam*
*This guide complements the 8 service definition files*
