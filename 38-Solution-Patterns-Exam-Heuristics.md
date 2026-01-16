# AWS SAA-C03: Solution Patterns & Exam Heuristics

## 🎯 Pattern-Based Approach to Exam Questions

When you see a specific requirement pattern, immediately think of these solutions.

---

## 📋 TABLE OF CONTENTS

1. [Web Application Patterns](#-web-application-patterns)
2. [Serverless Patterns](#-serverless-patterns)
3. [Data Processing Patterns](#-data-processing-patterns)
4. [Database Patterns](#-database-patterns)
5. [Storage Patterns](#-storage-patterns)
6. [Networking Patterns](#-networking-patterns)
7. [Security Patterns](#-security-patterns)
8. [High Availability Patterns](#-high-availability-patterns)
9. [Disaster Recovery Patterns](#-disaster-recovery-patterns)
10. [Cost Optimization Patterns](#-cost-optimization-patterns)
11. [Migration Patterns](#-migration-patterns)
12. [Hybrid Cloud Patterns](#-hybrid-cloud-patterns)
13. [Decoupling Patterns](#-decoupling-patterns)
14. [Caching Patterns](#-caching-patterns)
15. [Monitoring Patterns](#-monitoring-patterns)

---

## 🌐 WEB APPLICATION PATTERNS

### Pattern 1: Static Website
**When you see:** "Host static website" / "Static content" / "HTML/CSS/JS only"

```
S3 (static files) → CloudFront (CDN) → Route 53 (DNS)
```

| Component | Purpose | Exam Tip |
|-----------|---------|----------|
| S3 | Host static files | Enable static website hosting |
| CloudFront | Global delivery, HTTPS | Use OAC for S3 access |
| Route 53 | Custom domain | Alias record to CloudFront |

**Key Points:**
- ✅ S3 website endpoint for HTTP
- ✅ CloudFront for HTTPS (S3 doesn't support HTTPS directly)
- ✅ Origin Access Control (OAC) to secure S3

---

### Pattern 2: Dynamic Web Application (Standard)
**When you see:** "Dynamic website" / "Web application" / "User login"

```
Route 53 → CloudFront → ALB → EC2 (Auto Scaling) → RDS Multi-AZ
                                      ↓
                              ElastiCache (sessions)
```

| Component | Purpose | Exam Tip |
|-----------|---------|----------|
| Route 53 | DNS, health checks | Latency or Failover routing |
| CloudFront | Cache static assets | Dynamic content passes through |
| ALB | HTTP/HTTPS routing | Path/host-based routing |
| Auto Scaling | Handle load | Target tracking policy |
| RDS Multi-AZ | Database HA | Automatic failover |
| ElastiCache | Session storage | Redis for persistence |

---

### Pattern 3: Serverless Web Application
**When you see:** "Serverless" / "No server management" / "Pay per request"

```
Route 53 → CloudFront → S3 (frontend) 
                    ↓
            API Gateway → Lambda → DynamoDB
```

| Component | Purpose | Exam Tip |
|-----------|---------|----------|
| S3 | Static frontend (React/Vue) | Host SPA |
| API Gateway | REST/HTTP API | Throttling, caching |
| Lambda | Business logic | Max 15 min |
| DynamoDB | Serverless database | Auto-scaling |

**Serverless Stack:**
- Frontend: S3 + CloudFront
- API: API Gateway + Lambda
- Database: DynamoDB
- Auth: Cognito

---

### Pattern 4: Microservices Web Application
**When you see:** "Microservices" / "Independent scaling" / "Multiple teams"

```
Route 53 → ALB → ECS/EKS (Fargate) → Service A
                              ↓
                        Service B → SQS → Service C
                              ↓
                           Aurora
```

| Component | Purpose | Exam Tip |
|-----------|---------|----------|
| ECS/Fargate | Container orchestration | Serverless containers |
| EKS | Kubernetes required | When K8s specified |
| SQS | Decouple services | Async communication |
| Aurora | Shared database | Or DynamoDB per service |

---

## ⚡ SERVERLESS PATTERNS

### Pattern 5: Event-Driven Processing
**When you see:** "Automatically process" / "Trigger on event" / "React to changes"

```
Event Source → Lambda → Destination

Event Sources:           Destinations:
- S3 (object upload)     - S3 (processed data)
- DynamoDB Streams       - DynamoDB
- Kinesis               - SQS/SNS
- SQS                   - EventBridge
- API Gateway           - Step Functions
- EventBridge           - Other services
```

**Common Triggers:**
| Event | Pattern | Use Case |
|-------|---------|----------|
| S3 ObjectCreated | S3 → Lambda → S3 | Image processing |
| DynamoDB Stream | DynamoDB → Lambda → SNS | Change notifications |
| API Gateway | APIGW → Lambda → DynamoDB | REST API |
| Schedule | EventBridge → Lambda | Cron jobs |
| SQS | SQS → Lambda | Queue processing |

---

### Pattern 6: Serverless Workflow
**When you see:** "Orchestrate" / "Multiple steps" / "Error handling" / "State machine"

```
API Gateway → Step Functions → Lambda A → Lambda B → Lambda C
                    ↓                         ↓
              Error Handler              DynamoDB
```

**Step Functions Use Cases:**
- ✅ Order processing workflows
- ✅ ETL pipelines
- ✅ Approval workflows
- ✅ Long-running processes (> 15 min total)
- ✅ Retry with exponential backoff

**Exam Heuristic:** "Orchestrate multiple Lambda" = Step Functions

---

### Pattern 7: Serverless API
**When you see:** "REST API" / "API endpoint" / "Mobile backend"

```
CloudFront → API Gateway → Lambda → DynamoDB
                  ↓
              Cognito (auth)
```

**API Gateway Types:**
| Type | Use Case | Exam Tip |
|------|----------|----------|
| REST API | Full features, caching | Most common |
| HTTP API | Low latency, low cost | Simple proxy |
| WebSocket | Real-time, bidirectional | Chat apps |

---

## 📊 DATA PROCESSING PATTERNS

### Pattern 8: Real-Time Streaming
**When you see:** "Real-time" / "Streaming data" / "Millisecond processing"

```
Producers → Kinesis Data Streams → Lambda/KDA → Consumers
                                        ↓
                                   DynamoDB (real-time state)
```

| Component | Purpose | Exam Tip |
|-----------|---------|----------|
| Kinesis Data Streams | Ingest real-time | Shards for throughput |
| Lambda | Process records | 1 invocation per shard |
| Kinesis Data Analytics | SQL on streams | Real-time analytics |
| DynamoDB | Store results | Low latency |

---

### Pattern 9: Near Real-Time Data Loading
**When you see:** "Load to S3/Redshift" / "Near real-time" / "No custom processing"

```
Producers → Kinesis Data Firehose → S3/Redshift/OpenSearch
                    ↓
              (Optional Lambda for transform)
```

**Firehose Destinations:**
- S3 (most common)
- Redshift (via S3 copy)
- OpenSearch
- Splunk
- HTTP endpoint

**Exam Heuristic:** 
- "Real-time + custom processing" → Kinesis Data Streams
- "Just deliver to S3/Redshift" → Kinesis Firehose

---

### Pattern 10: Batch Data Processing
**When you see:** "ETL" / "Transform data" / "Data pipeline"

```
S3 (raw) → Glue (ETL) → S3 (processed) → Athena/Redshift
              ↓
        Glue Catalog (metadata)
```

| Component | Purpose | Exam Tip |
|-----------|---------|----------|
| Glue | Serverless ETL | Auto-generates code |
| Glue Catalog | Metadata store | Schema discovery |
| Athena | Query S3 | Serverless SQL |
| Redshift | Data warehouse | Complex analytics |

---

### Pattern 11: Big Data Processing
**When you see:** "Hadoop" / "Spark" / "Petabyte scale" / "Big data"

```
S3 (data lake) → EMR (Spark/Hadoop) → S3 (results) → Athena/Redshift
```

**EMR Use Cases:**
- Machine learning training
- Log analysis
- Genomics processing
- Financial analysis

**Exam Heuristic:** "Hadoop/Spark/Big Data" = EMR

---

### Pattern 12: Data Lake Analytics
**When you see:** "Query S3" / "Ad-hoc analysis" / "Data lake"

```
S3 (data lake) → Glue Catalog → Athena → QuickSight
                                  ↓
                            Redshift Spectrum
```

| Scenario | Solution |
|----------|----------|
| Ad-hoc SQL on S3 | Athena |
| Complex joins, aggregations | Redshift |
| Query S3 from Redshift | Redshift Spectrum |
| Dashboards | QuickSight |

---

## 🗄️ DATABASE PATTERNS

### Pattern 13: Relational Database (Standard)
**When you see:** "SQL" / "ACID" / "Relational" / "Transactions"

```
Application → RDS Multi-AZ → Read Replicas (optional)
                  ↓
             Automated backups
```

**RDS Decision:**
| Requirement | Solution |
|-------------|----------|
| Standard MySQL/PostgreSQL | RDS |
| Highest performance | Aurora |
| Oracle/SQL Server | RDS |
| Auto-scaling storage | Aurora |
| Fast failover (< 30s) | Aurora |

---

### Pattern 14: Read-Heavy Database
**When you see:** "Read-heavy" / "Scale reads" / "Reporting queries"

```
Application (writes) → RDS/Aurora Primary
                            ↓
Application (reads) → Read Replica(s) (up to 15 for Aurora)
```

**Key Points:**
- Read Replicas for read scaling
- Can be in different regions (cross-region)
- Async replication (eventual consistency)
- Can promote to standalone

---

### Pattern 15: Global Database
**When you see:** "Multi-region" / "Global users" / "< 1 second replication"

```
Primary Region:                  Secondary Region(s):
Aurora Primary  ──────────────▶  Aurora Replica
(Read/Write)     < 1 sec         (Read only)
                replication          ↓
                              Promote on failover
```

**Aurora Global Database:**
- RPO < 1 second
- RTO < 1 minute
- Up to 5 secondary regions
- Up to 16 read replicas per region

---

### Pattern 16: NoSQL High Performance
**When you see:** "Key-value" / "Millisecond latency" / "Any scale"

```
Application → DynamoDB → DynamoDB Streams → Lambda
                ↓
              DAX (cache)
```

**DynamoDB Decision:**
| Requirement | Solution |
|-------------|----------|
| Unpredictable traffic | On-Demand |
| Predictable traffic | Provisioned + Auto Scaling |
| Microsecond reads | DAX |
| Multi-region active-active | Global Tables |
| React to changes | DynamoDB Streams |

---

### Pattern 17: Caching Database Results
**When you see:** "Cache queries" / "Reduce database load" / "Session store"

```
Application → ElastiCache → RDS
              (check cache first)
```

**Cache Strategy:**
| Pattern | Description | Use Case |
|---------|-------------|----------|
| Lazy Loading | Load on miss | Read-heavy |
| Write-Through | Update cache on write | Consistency |
| TTL | Expire cache | Fresh data |

**Redis vs Memcached:**
| Feature | Redis | Memcached |
|---------|-------|-----------|
| Persistence | ✅ | ❌ |
| Replication | ✅ | ❌ |
| Complex data | ✅ | ❌ |
| Multi-threaded | ❌ | ✅ |
| **Exam default** | ✅ | |

---

## 💾 STORAGE PATTERNS

### Pattern 18: Tiered Storage
**When you see:** "Optimize cost" / "Lifecycle" / "Archive old data"

```
S3 Standard → (30 days) → S3 Standard-IA → (90 days) → Glacier → (365 days) → Deep Archive
```

**Lifecycle Policy Example:**
| Age | Storage Class | Cost |
|-----|---------------|------|
| 0-30 days | Standard | $$$ |
| 30-90 days | Standard-IA | $$ |
| 90-365 days | Glacier Flexible | $ |
| 365+ days | Deep Archive | ¢ |

---

### Pattern 19: Cross-Region Data Protection
**When you see:** "Cross-region backup" / "Compliance in another region" / "DR"

```
S3 Bucket (Primary Region) ──── CRR ────▶ S3 Bucket (DR Region)
                                              ↓
                                         Versioning enabled
```

**S3 Replication:**
| Type | Use Case |
|------|----------|
| CRR (Cross-Region) | DR, compliance, latency |
| SRR (Same-Region) | Log aggregation, prod/test sync |

**Requirements:**
- Versioning enabled on both buckets
- IAM role for replication
- Can replicate to different storage class

---

### Pattern 20: Shared File System
**When you see:** "Shared storage" / "Multiple instances" / "NFS/SMB"

```
Linux NFS:
EC2 Instance A ─┐
EC2 Instance B ─┼──▶ EFS
EC2 Instance C ─┘

Windows SMB:
EC2 Instance A ─┐
EC2 Instance B ─┼──▶ FSx for Windows
EC2 Instance C ─┘
```

**File System Selection:**
| Requirement | Solution |
|-------------|----------|
| Linux NFS | EFS |
| Windows SMB | FSx for Windows |
| High-performance HPC | FSx for Lustre |
| NetApp features | FSx for NetApp ONTAP |

---

### Pattern 21: Hybrid Storage
**When you see:** "On-premises + cloud" / "Extend storage" / "Backup to cloud"

```
On-Premises:                          AWS:
NFS/SMB Apps → Storage Gateway → S3 → Glacier (lifecycle)
```

**Storage Gateway Types:**
| Type | Protocol | Backend | Use Case |
|------|----------|---------|----------|
| File Gateway | NFS/SMB | S3 | File shares |
| Volume (Cached) | iSCSI | S3 | Data in cloud |
| Volume (Stored) | iSCSI | S3 | Data on-prem, backup to cloud |
| Tape Gateway | iSCSI | S3/Glacier | Replace tape |

---

## 🌐 NETWORKING PATTERNS

### Pattern 22: Load Balancing
**When you see:** Choose the right load balancer

| Requirement | Load Balancer | Why |
|-------------|---------------|-----|
| HTTP/HTTPS | **ALB** | Layer 7, path routing |
| Path-based routing | **ALB** | /api, /images |
| Host-based routing | **ALB** | api.example.com |
| WebSocket | **ALB** | Sticky sessions |
| TCP/UDP | **NLB** | Layer 4 |
| Ultra-low latency | **NLB** | Millions RPS |
| Static IP | **NLB** | Whitelisting |
| Firewall appliances | **GWLB** | 3rd party |

---

### Pattern 23: DNS Routing
**When you see:** Choose the right Route 53 policy

| Requirement | Policy | Use Case |
|-------------|--------|----------|
| Single resource | Simple | Basic DNS |
| Distribute traffic % | Weighted | A/B testing |
| Lowest latency | Latency | Global performance |
| Active-passive DR | Failover | Disaster recovery |
| By user country | Geolocation | Compliance, localization |
| By distance + bias | Geoproximity | Shift traffic |
| Multiple healthy | Multivalue | Simple LB |

---

### Pattern 24: Private Connectivity
**When you see:** "Private access to AWS services"

```
VPC → VPC Endpoint → AWS Service

Gateway Endpoint (free):        Interface Endpoint (cost):
- S3                           - All other services
- DynamoDB                     - Uses PrivateLink
```

**Exam Heuristic:**
- "Private S3 access" → Gateway Endpoint
- "Private access to other services" → Interface Endpoint

---

### Pattern 25: Multi-VPC Connectivity
**When you see:** "Connect VPCs"

```
2 VPCs:                    Many VPCs:
VPC A ←→ VPC B             VPC A ─┐
(VPC Peering)              VPC B ─┼──▶ Transit Gateway
                           VPC C ─┘
```

| Scenario | Solution |
|----------|----------|
| 2 VPCs, simple | VPC Peering |
| Many VPCs | Transit Gateway |
| Transitive routing | Transit Gateway |
| Hub-spoke topology | Transit Gateway |

---

### Pattern 26: Hybrid Connectivity
**When you see:** "Connect on-premises to AWS"

| Requirement | Solution | Setup Time |
|-------------|----------|------------|
| Quick, encrypted | Site-to-Site VPN | Hours/Days |
| Consistent latency | Direct Connect | Weeks |
| DX + encryption | DX + VPN | Weeks |
| Connect to many VPCs | Transit Gateway | Days |

---

### Pattern 27: Global Content Delivery
**When you see:** "Global users" / "Low latency" / "Cache content"

```
Static content:           Dynamic content:
CloudFront → S3           CloudFront → ALB → EC2
(edge cache)              (pass-through)
```

**CloudFront vs Global Accelerator:**
| Feature | CloudFront | Global Accelerator |
|---------|------------|-------------------|
| Caching | ✅ Yes | ❌ No |
| Static IP | ❌ No | ✅ Yes (anycast) |
| Layer | 7 (HTTP) | 4 (TCP/UDP) |
| Use Case | CDN, cache | Non-HTTP, gaming |

---

## 🔒 SECURITY PATTERNS

### Pattern 28: Defense in Depth
**When you see:** "Secure architecture" / "Multiple layers"

```
Internet → WAF → Shield → CloudFront → ALB → Security Group → EC2
                                        ↓
                                      NACL
```

| Layer | Service | Protection |
|-------|---------|------------|
| Edge | WAF | SQL injection, XSS |
| Edge | Shield | DDoS |
| Edge | CloudFront | Edge security |
| VPC | Security Group | Instance firewall |
| VPC | NACL | Subnet firewall (block IP) |
| Host | Inspector | Vulnerability scan |

---

### Pattern 29: Secure Data Flow
**When you see:** "Encrypt data" / "Secure credentials"

```
Application → Secrets Manager → RDS
     ↓              ↓
  IAM Role         KMS
```

**Security Pattern:**
| Requirement | Solution |
|-------------|----------|
| EC2 needs S3 | IAM Role (not keys!) |
| Lambda needs DynamoDB | Execution Role |
| Store credentials | Secrets Manager |
| Rotate credentials | Secrets Manager |
| Store config | Parameter Store |
| Encrypt data | KMS |
| SSL certificate | ACM |

---

### Pattern 30: Compliance & Audit
**When you see:** "Audit" / "Compliance" / "Track changes"

```
                    ┌─────────────────────────────────────────┐
                    │            Security Hub                  │
                    │         (central dashboard)              │
                    └─────────────────────────────────────────┘
                                      ↑
        ┌─────────────────┬──────────┼──────────┬─────────────┐
        ↑                 ↑          ↑          ↑             ↑
   CloudTrail          Config    GuardDuty   Inspector     Macie
   (API audit)      (config)    (threats)    (vulns)       (PII)
```

| Requirement | Service |
|-------------|---------|
| Who did what (API) | CloudTrail |
| Config changes | Config |
| Threat detection | GuardDuty |
| Vulnerabilities | Inspector |
| Sensitive data | Macie |
| Central view | Security Hub |

---

## 🔄 HIGH AVAILABILITY PATTERNS

### Pattern 31: Multi-AZ Application
**When you see:** "High availability" / "Fault tolerant"

```
                    ALB (Multi-AZ)
                    /            \
             AZ-a                  AZ-b
            /    \                /    \
         EC2     NAT           EC2     NAT
          |       |             |       |
         RDS ←─────────────────→ RDS Standby
      (Primary)               (Sync replication)
```

**Multi-AZ Checklist:**
- ✅ ALB spans multiple AZs
- ✅ Auto Scaling Group in multiple AZs
- ✅ RDS Multi-AZ enabled
- ✅ NAT Gateway per AZ
- ✅ Subnets in each AZ

---

### Pattern 32: Auto Scaling
**When you see:** "Handle variable load" / "Scale automatically"

```
CloudWatch Alarm → Auto Scaling → Launch/Terminate EC2
                        ↓
              Scaling Policy (Target Tracking)
```

**Scaling Policies:**
| Policy | Use Case | Exam Tip |
|--------|----------|----------|
| Target Tracking | Maintain metric (CPU 70%) | Most common |
| Step Scaling | Different actions per threshold | Complex rules |
| Scheduled | Known traffic patterns | Predictable |
| Predictive | ML-based | Proactive |

**Exam Heuristic:** "Simple auto scaling" = Target Tracking

---

### Pattern 33: Queue-Based Scaling
**When you see:** "Process messages" / "Decouple scaling"

```
Producers → SQS → Consumers (Auto Scaling)
              ↓
         ApproximateNumberOfMessages → Scale out/in
```

**Pattern:**
- Scale based on queue depth
- More messages = more consumers
- Decouples producer from consumer

---

## 🆘 DISASTER RECOVERY PATTERNS

### Pattern 34: Backup & Restore (RTO: Hours)
**When you see:** "Lowest cost DR" / "Hours of downtime acceptable"

```
Primary Region:              DR Region:
EC2 + RDS → Backup → S3 CRR → S3 → Restore when needed
                AMI                  AMI
                Snapshots            Snapshots
```

**Characteristics:**
- Lowest cost
- Longest RTO (hours)
- Data backed up, no running resources in DR

---

### Pattern 35: Pilot Light (RTO: Minutes)
**When you see:** "Quick recovery" / "Minimal cost DR"

```
Primary Region:              DR Region:
EC2 (running) ─────────────▶ AMI (ready to launch)
RDS (running) ──── async ──▶ RDS (running, minimal)
```

**Characteristics:**
- Core services running (database)
- Compute ready to launch
- Scale up on failover

---

### Pattern 36: Warm Standby (RTO: Minutes)
**When you see:** "Fast failover" / "Reduced capacity standby"

```
Primary Region:              DR Region:
EC2 ASG (10) ───────────────▶ EC2 ASG (2) → Scale to 10
RDS (running) ─── async ────▶ RDS (running)
```

**Characteristics:**
- Reduced capacity running
- Scale up on failover
- Faster than pilot light

---

### Pattern 37: Multi-Site Active-Active (RTO: Near-Zero)
**When you see:** "Zero downtime" / "Active-active" / "Both sites serving"

```
                Route 53 (Latency routing)
                    /            \
          Primary Region      Secondary Region
         (Active, 50%)        (Active, 50%)
              |                    |
        Aurora Primary ←─────→ Aurora Replica
              (< 1 sec replication)
```

**Characteristics:**
- Both regions active
- Highest cost
- Near-zero RTO/RPO
- Use Aurora Global or DynamoDB Global Tables

---

## 💰 COST OPTIMIZATION PATTERNS

### Pattern 38: Compute Cost Optimization
**When you see:** "Reduce compute cost"

| Workload Type | Solution | Savings |
|---------------|----------|---------|
| Steady 24/7 | Reserved Instances | Up to 72% |
| Flexible commitment | Savings Plans | Up to 72% |
| Fault-tolerant batch | Spot Instances | Up to 90% |
| Variable but predictable | Scheduled Scaling | Pay for what you need |
| Oversized instances | Compute Optimizer | Right-size |

---

### Pattern 39: Storage Cost Optimization
**When you see:** "Reduce storage cost"

| Pattern | Solution |
|---------|----------|
| Unknown access | S3 Intelligent-Tiering |
| Infrequent access | S3 Standard-IA |
| Archive | Glacier or Deep Archive |
| Auto-tier | Lifecycle Policies |
| Delete old versions | Lifecycle expiration |
| Unused EBS | Delete or snapshot |

---

### Pattern 40: Data Transfer Cost Optimization
**When you see:** "Reduce data transfer cost"

| Pattern | Solution |
|---------|----------|
| Within AZ | Free |
| Between AZs | Minimize cross-AZ calls |
| To internet | Use CloudFront |
| Large outbound | Direct Connect |
| VPC to S3 | Gateway Endpoint (free) |

---

## 🚀 MIGRATION PATTERNS

### Pattern 41: Database Migration
**When you see:** "Migrate database"

```
Homogeneous (same engine):
Source DB → DMS → Target RDS

Heterogeneous (different engine):
Source DB → SCT (schema) → Target Schema
         → DMS (data)   → Target Data
```

| Migration Type | Tools |
|----------------|-------|
| Oracle → Oracle | DMS only |
| Oracle → PostgreSQL | SCT + DMS |
| SQL Server → Aurora | SCT + DMS |
| On-prem → RDS | DMS + VPN/DX |

---

### Pattern 42: Server Migration
**When you see:** "Lift and shift" / "Migrate VMs"

```
On-Prem VMs → MGN (continuous replication) → EC2
```

**Migration Services:**
| Service | Use Case |
|---------|----------|
| MGN | Lift-and-shift VMs |
| DMS | Database migration |
| DataSync | File transfer |
| Snow Family | Offline transfer |

---

### Pattern 43: Data Transfer
**When you see:** "Transfer large data"

| Data Size | Network | Solution |
|-----------|---------|----------|
| < 1 TB | Good | Direct upload or DataSync |
| 1-10 TB | Limited | Snowcone |
| 10 TB - 1 PB | Limited | Snowball Edge |
| > 1 PB | Limited | Multiple Snowballs or Snowmobile |
| Ongoing sync | Any | DataSync |

---

## 🔗 DECOUPLING PATTERNS

### Pattern 44: Queue-Based Decoupling
**When you see:** "Decouple" / "Async processing"

```
Producer → SQS → Consumer
            ↓
          DLQ (failed messages)
```

**SQS Types:**
| Type | Use Case |
|------|----------|
| Standard | High throughput, at-least-once |
| FIFO | Order matters, exactly-once |

---

### Pattern 45: Fan-Out Pattern
**When you see:** "One event, multiple consumers" / "Fan-out"

```
Producer → SNS → SQS Queue A → Consumer A
             → SQS Queue B → Consumer B
             → SQS Queue C → Consumer C
```

**Exam Heuristic:** "Fanout" = SNS → Multiple SQS

---

### Pattern 46: Event-Driven Architecture
**When you see:** "Event-driven" / "React to events"

```
Event Source → EventBridge → Target(s)
                   ↓
               Rules (filter/route)
```

**EventBridge Use Cases:**
- Scheduled events (cron)
- AWS service events
- Custom application events
- SaaS integration

---

## 📊 CACHING PATTERNS

### Pattern 47: Database Caching
**When you see:** "Cache database" / "Reduce DB load"

```
App → Check Cache → Hit? → Return
           ↓
          Miss → Query DB → Store in Cache → Return
```

**Caching Solutions:**
| Database | Cache |
|----------|-------|
| RDS/Aurora | ElastiCache Redis |
| DynamoDB | DAX |

---

### Pattern 48: API/Content Caching
**When you see:** "Cache API responses" / "Reduce latency"

```
Client → CloudFront (edge cache) → API Gateway (cache) → Lambda
```

**Caching Layers:**
| Layer | Service | TTL |
|-------|---------|-----|
| Edge | CloudFront | Seconds to days |
| API | API Gateway | Up to 1 hour |
| Application | ElastiCache | Custom |

---

## 📈 MONITORING PATTERNS

### Pattern 49: Application Monitoring
**When you see:** "Monitor application" / "Troubleshoot"

```
Application → CloudWatch Metrics → Alarms → SNS → Actions
          → CloudWatch Logs → Insights
          → X-Ray → Traces
```

| Requirement | Service |
|-------------|---------|
| Metrics | CloudWatch Metrics |
| Logs | CloudWatch Logs |
| Alarms | CloudWatch Alarms |
| Tracing | X-Ray |
| APM | CloudWatch Application Insights |

---

### Pattern 50: Infrastructure Monitoring
**When you see:** "Monitor infrastructure" / "Compliance"

```
AWS Resources → CloudWatch (metrics) → Dashboard
            → CloudTrail (API audit) → S3
            → Config (configuration) → Rules
            → VPC Flow Logs (network) → CloudWatch Logs
```

---

## 🎯 QUICK REFERENCE: PATTERN MATCHING

### When you see... Use this pattern:

| Keyword | Pattern | Solution |
|---------|---------|----------|
| "Static website" | Static Web | S3 + CloudFront |
| "Dynamic web app" | Standard Web | ALB + EC2 ASG + RDS |
| "Serverless web" | Serverless | S3 + APIGW + Lambda + DynamoDB |
| "Microservices" | Containers | ECS/EKS + ALB + SQS |
| "Real-time" | Streaming | Kinesis Data Streams |
| "Load to S3" | Delivery | Kinesis Firehose |
| "ETL" | Data Processing | Glue |
| "Query S3" | Analytics | Athena |
| "Data warehouse" | Analytics | Redshift |
| "Decouple" | Messaging | SQS |
| "Fan-out" | Messaging | SNS → SQS |
| "Workflow" | Orchestration | Step Functions |
| "High availability" | HA | Multi-AZ + ASG + ALB |
| "Disaster recovery" | DR | CRR + Aurora Global |
| "Lowest cost DR" | DR | Backup & Restore |
| "Cache database" | Caching | ElastiCache/DAX |
| "Global content" | CDN | CloudFront |
| "Connect on-prem" | Hybrid | VPN or Direct Connect |
| "Migrate database" | Migration | DMS (+SCT) |
| "Lift and shift" | Migration | MGN |

---

## 🏆 EXAM SUCCESS CHECKLIST

✅ **Identify the primary requirement first**
- Cost? Performance? Security? Availability?

✅ **Match keywords to patterns**
- "Decouple" → SQS
- "Serverless" → Lambda + DynamoDB
- "Real-time" → Kinesis

✅ **Apply the right pattern**
- Don't over-engineer
- Choose managed services

✅ **Verify the solution**
- Does it meet ALL requirements?
- Is it the SIMPLEST solution?

# AWS Architecture Patterns - Quick Reference Guide
## 1️⃣ COMPUTE SERVICES - QUICK DECISIONS

### **EC2 (Elastic Compute Cloud)**

#### Instance Type Selection Heuristics:
| Keyword in Question | Instance Family | Quick Reason |
|---------------------|----------------|--------------|
| "General purpose" / "Balanced" | **T3/M6g** | Balanced CPU/Memory |
| "Compute intensive" / "High CPU" | **C6g/C6i** | Compute optimized |
| "Memory intensive" / "In-memory DB" | **R6g/R6i** | Memory optimized |
| "Storage intensive" / "Big Data" | **I3/D3** | Storage optimized |
| "GPU" / "ML training" | **P4/G5** | GPU instances |
| "Burstable" / "Variable load" | **T3/T3a** | Burstable performance |

#### Pricing Heuristics:
| Requirement | Choice | Savings |
|-------------|--------|---------|
| **Steady-state, predictable** | Reserved Instances (1-3yr) | 40-60% |
| **Flexible compute commitment** | Savings Plans | 40-60% |
| **Fault-tolerant, flexible time** | Spot Instances | Up to 90% |
| **Short-term, unpredictable** | On-Demand | No commitment |
| **Host compliance requirements** | Dedicated Hosts | License control |
| **Isolated instance** | Dedicated Instances | Physical isolation |

#### Storage Heuristics:
| Requirement | EBS Type | When to Use |
|-------------|----------|-------------|
| **"Highest IOPS"** | io2 Block Express | >64,000 IOPS |
| **"High performance"** | io2/io1 | Databases, 16K IOPS+ |
| **"General SSD"** | gp3 | Most workloads (default) |
| **"Throughput optimized"** | st1 | Big Data, log processing |
| **"Cold storage"** | sc1 | Infrequent access |
| **"Fastest storage"** | Instance Store | Ephemeral, buffer/cache |

#### Placement Group Heuristics:
| Requirement | Placement Group | Use Case |
|-------------|----------------|----------|
| **"Lowest latency"** | Cluster | HPC, tightly coupled |
| **"High availability"** | Spread | Critical apps, max 7/AZ |
| **"Balanced"** | Partition | Distributed databases |

---

### **Lambda**

#### Quick Decision Rules:
| Scenario | Decision |
|----------|----------|
| "Serverless compute" | ✅ Lambda |
| "Event-driven" | ✅ Lambda |
| "Runs > 15 minutes" | ❌ Use Fargate/EC2 |
| "Needs persistent storage" | Use EFS with Lambda |
| "Cold start issue" | Provisioned Concurrency |
| "Complex workflow" | Lambda + Step Functions |

#### Lambda Triggers Heuristics:
| Event Source | Use Case |
|--------------|----------|
| **API Gateway** | REST/HTTP APIs |
| **S3** | Object creation/deletion |
| **DynamoDB Streams** | Change data capture |
| **SQS** | Queue processing |
| **SNS** | Event notifications |
| **EventBridge** | Scheduled/cross-service events |
| **Kinesis** | Stream processing |
| **ALB** | HTTP(S) requests |

---

### **ECS/EKS (Containers)**

#### Container Service Selection:
| Requirement | Service | Reason |
|-------------|---------|--------|
| **"Docker containers"** | ECS | AWS-native, simpler |
| **"Kubernetes"** | EKS | K8s standard, portable |
| **"No server management"** | Fargate | Serverless containers |
| **"EC2 control"** | ECS/EKS on EC2 | Instance-level control |
| **"Batch processing"** | AWS Batch | Job scheduling |

#### Launch Type Heuristics:
| Scenario | Launch Type |
|----------|-------------|
| **"Simplest, serverless"** | Fargate |
| **"Cost optimization, control"** | EC2 |
| **"Spot instances"** | EC2 with Spot |
| **"GPU workloads"** | EC2 with GPU instances |

---

### **Elastic Beanstalk**

#### Quick Heuristic:
- **"Quick deployment"** + **"No infrastructure management"** = Elastic Beanstalk
- Supports: Java, .NET, Node.js, Python, Ruby, Go, PHP, Docker
- ✅ Use when developer wants to focus on code, not infrastructure
- ❌ Don't use when you need full infrastructure control

---

### **Lightsail**

#### Quick Heuristic:
- **"Simple web app"** + **"Predictable pricing"** + **"Small scale"** = Lightsail
- ✅ Simplified VPS alternative
- ❌ Not for complex architectures

---

## 2️⃣ STORAGE SERVICES - QUICK DECISIONS

### **S3 (Simple Storage Service)**

#### Storage Class Selection (CRITICAL EXAM TOPIC):
| Access Pattern | Storage Class | Retrieval | Cost |
|----------------|--------------|-----------|------|
| **Frequent (>1/month)** | S3 Standard | Instant | Highest |
| **Infrequent (>30 days)** | S3 Standard-IA | Instant | Medium |
| **One Zone, non-critical** | S3 One Zone-IA | Instant | Lower |
| **Archive (>90 days)** | Glacier Flexible | Minutes-hours | Low |
| **Deep archive (>180 days)** | Glacier Deep Archive | 12-48 hours | Lowest |
| **Unknown/changing pattern** | S3 Intelligent-Tiering | Automatic | Optimized |

#### S3 Feature Heuristics:
| Requirement | Feature | Quick Tip |
|-------------|---------|-----------|
| **"Version control"** | Versioning | Cannot be disabled, only suspended |
| **"Accidental deletion"** | Versioning + MFA Delete | Protects from permanent delete |
| **"Cross-region copy"** | Cross-Region Replication (CRR) | Automatic async copy |
| **"Same-region copy"** | Same-Region Replication (SRR) | Compliance, aggregation |
| **"Lifecycle management"** | Lifecycle Policies | Auto-transition/expire objects |
| **"Event notification"** | S3 Event Notifications | SNS/SQS/Lambda |
| **"Query data in place"** | S3 Select | Filter at S3 level |
| **"Fast uploads globally"** | Transfer Acceleration | CloudFront edge locations |
| **"Large file uploads"** | Multipart Upload | Files >100 MB |
| **"Block all public"** | S3 Block Public Access | Account/bucket level |
| **"Encryption at rest"** | SSE-S3/SSE-KMS/SSE-C | Default: SSE-S3 |
| **"Access logging"** | Server Access Logging | Audit S3 access |
| **"Object locking"** | S3 Object Lock | WORM compliance |

#### S3 Performance Heuristics:
| Issue | Solution |
|-------|----------|
| **"Slow uploads"** | Multipart Upload + Transfer Acceleration |
| **"High request rate"** | Use random prefix (not sequential) |
| **"Large scans"** | S3 Select or Athena |

---

### **EBS (Elastic Block Store)**

#### Already covered in EC2 section above ✅

#### Snapshot Heuristics:
| Requirement | Action |
|-------------|--------|
| **"Backup EBS"** | Create snapshots (stored in S3) |
| **"Copy to another region"** | Copy snapshot to target region |
| **"Share snapshot"** | Modify snapshot permissions |
| **"Encrypt unencrypted"** | Copy snapshot with encryption |
| **"Fast restore"** | Fast Snapshot Restore (FSR) |

---

### **EFS (Elastic File System)**

#### Quick Heuristics:
| Scenario | Decision |
|----------|----------|
| **"Shared file system"** + **"Linux"** | EFS |
| **"NFS protocol"** | EFS |
| **"Multi-AZ by default"** | EFS (automatic) |
| **"Petabyte scale"** | EFS |
| **"Cost optimization"** | EFS Infrequent Access (IA) |

#### Storage Class:
| Access Pattern | Class |
|----------------|-------|
| **Regular access** | EFS Standard |
| **Infrequent access (>30 days)** | EFS IA (auto-transition) |

#### Performance Mode:
| Workload | Mode |
|----------|------|
| **General purpose** | General Purpose |
| **High throughput, big data** | Max I/O |

---

### **FSx**

#### FSx Service Selection:
| Requirement | FSx Type | Use Case |
|-------------|----------|----------|
| **"Windows file server"** | FSx for Windows File Server | SMB, Active Directory |
| **"High-performance computing"** | FSx for Lustre | ML, HPC, video processing |
| **"NetApp ONTAP"** | FSx for NetApp ONTAP | Multi-protocol (NFS, SMB) |
| **"OpenZFS"** | FSx for OpenZFS | Linux workloads |

---

### **Storage Gateway**

#### Gateway Type Selection:
| Requirement | Gateway Type | Use Case |
|-------------|-------------|----------|
| **"Backup on-prem to S3"** | File Gateway | NFS/SMB to S3 |
| **"Block storage, iSCSI"** | Volume Gateway | Virtual tape library |
| **"Tape backup to cloud"** | Tape Gateway | Replace physical tapes |

---

## 3️⃣ DATABASE SERVICES - QUICK DECISIONS

### **RDS (Relational Database Service)**

#### Engine Selection:
| Requirement | Engine | Reason |
|-------------|--------|--------|
| **"MySQL compatible, 5x performance"** | Aurora MySQL | AWS-optimized |
| **"PostgreSQL compatible, 3x performance"** | Aurora PostgreSQL | AWS-optimized |
| **"Open source, popular"** | MySQL/PostgreSQL | Community standard |
| **"Microsoft SQL Server"** | SQL Server | Windows ecosystem |
| **"Oracle"** | Oracle | Enterprise apps |
| **"IBM"** | DB2 | Legacy IBM systems |
| **"MariaDB"** | MariaDB | MySQL fork |

#### High Availability Heuristics:
| Requirement | Solution | RTO/RPO |
|-------------|----------|---------|
| **"High availability"** | Multi-AZ | Minutes/Zero |
| **"Read scaling"** | Read Replicas | N/A (async) |
| **"Disaster recovery"** | Cross-Region Read Replica | Manual promotion |
| **"Global database"** | Aurora Global Database | <1 second lag |

#### Backup Heuristics:
| Feature | Detail |
|---------|--------|
| **Automated Backups** | 1-35 days retention |
| **Manual Snapshots** | Until you delete |
| **Point-in-Time Recovery** | Any second within retention |

---

### **DynamoDB**

#### When to Choose DynamoDB:
| Scenario | Why DynamoDB |
|----------|--------------|
| **"Serverless database"** | Fully managed, no servers |
| **"Microsecond latency"** | Single-digit millisecond |
| **"Key-value"** / **"Document"** | NoSQL design |
| **"Massive scale"** | Unlimited storage |
| **"Event-driven"** | DynamoDB Streams |

#### Capacity Mode:
| Requirement | Mode | Use Case |
|-------------|------|----------|
| **"Predictable traffic"** | Provisioned | Cost-effective for steady load |
| **"Unpredictable/spiky"** | On-Demand | Pay per request |

#### Performance Optimizations:
| Issue | Solution |
|-------|----------|
| **"Cache reads"** | DAX (DynamoDB Accelerator) |
| **"Hot partition"** | Better partition key design |
| **"Global access"** | Global Tables (multi-region) |
| **"Change tracking"** | DynamoDB Streams |

---

### **ElastiCache**

#### Engine Selection:
| Requirement | Engine | Reason |
|-------------|--------|--------|
| **"Simple caching"** | Memcached | Simple, multi-threaded |
| **"Advanced features"** | Redis | Persistence, pub/sub, sorted sets |
| **"Persistence"** | Redis | Snapshots, AOF |
| **"High availability"** | Redis (Multi-AZ) | Automatic failover |
| **"Replication"** | Redis | Read replicas |

---

### **Redshift**

#### Quick Heuristics:
| Scenario | Decision |
|----------|----------|
| **"Data warehouse"** | Redshift |
| **"OLAP" / "Analytics"** | Redshift |
| **"Petabyte-scale analytics"** | Redshift |
| **"SQL on data warehouse"** | Redshift |
| **"Columnar storage"** | Redshift |

#### Features:
- **Redshift Spectrum**: Query S3 data directly
- **Concurrency Scaling**: Handle burst queries
- **RA3 nodes**: Compute/storage separation

---

### **Other Database Services**

| Service | Use Case | Quick Heuristic |
|---------|----------|-----------------|
| **Neptune** | Graph database | Social networks, fraud detection |
| **DocumentDB** | MongoDB-compatible | Document store, MongoDB migration |
| **Timestream** | Time-series data | IoT, metrics, logs |
| **QLDB** | Immutable ledger | Audit trail, blockchain-like |
| **Keyspaces** | Cassandra-compatible | Wide-column NoSQL |

---

## 4️⃣ NETWORKING - QUICK DECISIONS

### **VPC (Virtual Private Cloud)**

#### Subnet Design Heuristics:
| Type | Use | Route |
|------|-----|-------|
| **Public Subnet** | Resources need internet | Route to IGW |
| **Private Subnet** | Backend, databases | Route to NAT |
| **Isolated Subnet** | No internet at all | Local routes only |

#### Internet Access:
| Scenario | Component |
|----------|-----------|
| **"EC2 needs internet (public)"** | Internet Gateway (IGW) |
| **"Private EC2 needs outbound internet"** | NAT Gateway/Instance |
| **"Private subnet, AZ-specific"** | NAT Gateway (per AZ) |
| **"Cost-effective NAT"** | NAT Instance (manage yourself) |

#### VPC Connectivity:
| Requirement | Solution | Use Case |
|-------------|----------|----------|
| **"Connect VPCs (same account/region)"** | VPC Peering | Non-transitive |
| **"Connect multiple VPCs (hub-spoke)"** | Transit Gateway | Centralized routing |
| **"Connect on-prem to VPC"** | VPN / Direct Connect | Hybrid |
| **"Quick VPN setup"** | Site-to-Site VPN | Internet-based |
| **"Dedicated connection"** | Direct Connect | Private, consistent |
| **"Access AWS services privately"** | VPC Endpoints | No internet needed |

#### Security:
| Component | Level | Stateful? |
|-----------|-------|-----------|
| **Security Groups** | Instance | Yes (return traffic allowed) |
| **NACLs** | Subnet | No (explicit rules both ways) |

#### VPC Endpoint Types:
| Type | Use Case | Services |
|------|----------|----------|
| **Gateway Endpoint** | S3, DynamoDB | Free, route table entry |
| **Interface Endpoint** | Most AWS services | Powered by PrivateLink (ENI) |

---

### **Route 53**

#### Routing Policy Selection (CRITICAL):
| Requirement | Routing Policy | Use Case |
|-------------|----------------|----------|
| **"Single resource"** | Simple | One A record |
| **"Multiple resources, random"** | Simple (multiple values) | Basic LB |
| **"A/B testing, gradual migration"** | Weighted | 80% old, 20% new |
| **"Lowest latency to users"** | Latency-based | Global users |
| **"Active-passive DR"** | Failover | Primary/secondary |
| **"Route by user location"** | Geolocation | Content localization |
| **"Route by geography + bias"** | Geoproximity | Traffic distribution |
| **"Multiple IPs with health checks"** | Multivalue Answer | Simple LB with health |

---

### **CloudFront**

#### Quick Heuristics:
| Requirement | Solution |
|-------------|----------|
| **"Global content delivery"** | CloudFront |
| **"Cache at edge"** | CloudFront |
| **"DDoS protection"** | CloudFront + Shield |
| **"HTTPS for S3 static site"** | CloudFront (S3 doesn't support HTTPS) |
| **"Geo-restriction"** | CloudFront geo-restriction |
| **"Custom SSL"** | CloudFront + ACM certificate |
| **"Dynamic content"** | CloudFront (cache misses go to origin) |
| **"Field-level encryption"** | CloudFront field-level encryption |

#### Origin Types:
- S3 bucket (use OAC for security)
- Custom Origin (EC2, ALB, on-prem server)

---

### **Global Accelerator**

#### Quick Heuristic:
| Use Global Accelerator When | Why |
|------------------------------|-----|
| **"Non-HTTP applications"** | TCP/UDP support |
| **"Static IP needed"** | 2 Anycast IPs |
| **"Fast failover"** | Health checks, instant failover |
| **"Gaming, VoIP"** | Low latency, UDP |

**CloudFront vs Global Accelerator:**
- **CloudFront**: HTTP/HTTPS, caching
- **Global Accelerator**: TCP/UDP, no caching, static IPs

---

### **Load Balancers**

#### Load Balancer Selection:
| Requirement | Load Balancer | Use Case |
|-------------|---------------|----------|
| **"HTTP/HTTPS, Layer 7"** | Application Load Balancer (ALB) | Web apps, path/host routing |
| **"TCP/UDP, Layer 4"** | Network Load Balancer (NLB) | High performance, static IP |
| **"Legacy EC2-Classic"** | Classic Load Balancer | Deprecated, avoid |
| **"Lambda target"** | ALB | Invoke Lambda via HTTP |
| **"WebSockets"** | ALB | Long-lived connections |
| **"Millions of requests/sec"** | NLB | Ultra-low latency |
| **"PrivateLink"** | NLB | Expose services via PrivateLink |

#### ALB Features:
- Path-based routing (`/api`, `/images`)
- Host-based routing (`api.example.com`, `www.example.com`)
- Query string routing
- HTTP header routing
- Target groups: EC2, Lambda, IP addresses

---

## 5️⃣ SECURITY, IDENTITY & COMPLIANCE - QUICK DECISIONS

### **IAM (Identity and Access Management)**

#### Core Heuristics:
| Principle | Rule |
|-----------|------|
| **Least Privilege** | Minimum permissions needed |
| **Users** | For humans (avoid in production) |
| **Groups** | Organize users |
| **Roles** | For AWS services & cross-account |
| **Policies** | JSON documents defining permissions |

#### Cross-Account Access:
| Scenario | Solution |
|----------|----------|
| **"Access another AWS account"** | IAM Role with trust policy |
| **"Share S3 bucket"** | Bucket policy or IAM role |
| **"Service needs permissions"** | IAM Role (not IAM user) |

#### Policy Types:
| Type | Scope |
|------|-------|
| **Identity-based** | Attached to users/groups/roles |
| **Resource-based** | Attached to resources (S3, SQS) |
| **Permission boundaries** | Max permissions for IAM entity |
| **SCP (Service Control Policy)** | AWS Organizations, limit accounts |

---

### **KMS (Key Management Service)**

#### Encryption Heuristics:
| Requirement | Solution |
|-------------|----------|
| **"Encrypt at rest"** | KMS (most services integrate) |
| **"Customer-managed keys"** | Customer Managed Keys (CMK) |
| **"AWS-managed keys"** | AWS Managed Keys (free) |
| **"Import your own keys"** | Customer Managed Keys (imported) |
| **"Automatic rotation"** | Enable auto-rotation (CMK) |
| **"Compliance, FIPS"** | KMS (FIPS 140-2 Level 2) |
| **"CloudHSM"** | FIPS 140-2 Level 3 |

---

### **Secrets Manager vs Parameter Store**

| Requirement | Service | Reason |
|-------------|---------|--------|
| **"Automatic rotation"** | Secrets Manager | Built-in rotation |
| **"Database credentials"** | Secrets Manager | RDS integration |
| **"Application config"** | Parameter Store | Free tier, hierarchical |
| **"Cross-account secrets"** | Secrets Manager | Resource policy |

---

### **AWS Certificate Manager (ACM)**

#### Quick Heuristics:
- **"HTTPS certificate"** → ACM (free for AWS services)
- Works with: CloudFront, ALB, NLB, API Gateway
- Automatic renewal
- Cannot export private key (use ACM Private CA if needed)

---

### **WAF (Web Application Firewall)**

#### Quick Heuristics:
| Requirement | Solution |
|-------------|----------|
| **"Block SQL injection"** | WAF with SQL injection rule |
| **"Block XSS"** | WAF with XSS rule |
| **"Rate limiting"** | WAF rate-based rule |
| **"Geo-blocking"** | WAF geo-match rule |
| **"Bot protection"** | AWS WAF Bot Control |

Attach WAF to: CloudFront, ALB, API Gateway, AppSync

---

### **Shield**

| Requirement | Service | Cost |
|-------------|---------|------|
| **"Basic DDoS protection"** | Shield Standard | Free (auto-enabled) |
| **"Advanced DDoS + response team"** | Shield Advanced | $3000/month |

---

### **Security Hub**

#### Quick Heuristic:
- **"Centralized security"** + **"Compliance checks"** = Security Hub
- Aggregates findings from: GuardDuty, Inspector, Macie, IAM Access Analyzer

---

### **GuardDuty**

#### Quick Heuristic:
- **"Threat detection"** / **"Anomaly detection"** = GuardDuty
- Analyzes: CloudTrail, VPC Flow Logs, DNS logs
- Detects: Compromised instances, reconnaissance, cryptocurrency mining

---

### **Inspector**

#### Quick Heuristic:
- **"Vulnerability scanning"** for EC2, ECR, Lambda = Inspector
- Checks: CVEs, network exposure, best practices

---

### **Macie**

#### Quick Heuristic:
- **"Discover PII in S3"** / **"Sensitive data"** = Macie
- Uses ML to identify and protect sensitive data

---

### **CloudTrail**

#### Quick Heuristics:
| Requirement | Solution |
|-------------|----------|
| **"Who did what?"** | CloudTrail |
| **"API call logging"** | CloudTrail |
| **"Compliance audit"** | CloudTrail → S3 |
| **"Real-time analysis"** | CloudTrail → CloudWatch Logs |
| **"Multi-account logging"** | Organization trail |

---

### **Config**

#### Quick Heuristics:
| Requirement | Solution |
|-------------|----------|
| **"Resource inventory"** | AWS Config |
| **"Configuration compliance"** | Config Rules |
| **"Configuration history"** | Config timeline |
| **"Remediation"** | Config with SSM Automation |

---

## 6️⃣ APPLICATION INTEGRATION - QUICK DECISIONS

### **SQS (Simple Queue Service)**

#### Queue Type Selection:
| Requirement | Queue Type | Details |
|-------------|-----------|---------|
| **"Decouple components"** | SQS Standard | Best-effort ordering |
| **"Exactly-once processing"** | SQS FIFO | Max 300 TPS (3000 with batching) |
| **"Order matters"** | SQS FIFO | Guaranteed ordering |
| **"High throughput"** | SQS Standard | Unlimited TPS |

#### Key Concepts:
- **Visibility Timeout**: Message invisible during processing
- **Dead Letter Queue**: Failed messages after retries
- **Long Polling**: Reduce API calls, more efficient

---

### **SNS (Simple Notification Service)**

#### Quick Heuristics:
| Requirement | Solution |
|-------------|----------|
| **"Pub/Sub pattern"** | SNS |
| **"Fan-out to multiple targets"** | SNS → SQS/Lambda/HTTP/Email |
| **"SMS notifications"** | SNS |
| **"Mobile push"** | SNS |
| **"Email alerts"** | SNS |

#### SNS + SQS (Fan-out Pattern):
```
SNS Topic
  ├→ SQS Queue 1
  ├→ SQS Queue 2
  └→ Lambda
```

---

### **EventBridge (CloudWatch Events)**

#### Quick Heuristics:
| Requirement | Solution |
|-------------|----------|
| **"Scheduled events" (cron)** | EventBridge |
| **"React to AWS events"** | EventBridge |
| **"Event-driven architecture"** | EventBridge |
| **"Route events to targets"** | EventBridge rules |
| **"SaaS integration"** | EventBridge (partner events) |

**Targets**: Lambda, Step Functions, SQS, SNS, Kinesis, ECS tasks, etc.

---

### **Step Functions**

#### Quick Heuristics:
| Requirement | Solution |
|-------------|----------|
| **"Orchestrate Lambda"** | Step Functions |
| **"Visual workflow"** | Step Functions |
| **"Long-running process"** | Step Functions Standard |
| **"High-volume, short"** | Step Functions Express |
| **"Human approval step"** | Step Functions with callback |

---

### **API Gateway**

#### API Type Selection:
| Requirement | API Type | Use Case |
|-------------|----------|----------|
| **"REST API"** | REST API | Full features, caching |
| **"HTTP API"** | HTTP API | Simpler, cheaper, 70% less cost |
| **"WebSocket"** | WebSocket API | Real-time, bidirectional |

#### Features:
- Throttling (10,000 RPS default)
- Caching (reduce backend calls)
- Request/response transformation
- API keys, usage plans
- CORS configuration

---

### **AppSync**

#### Quick Heuristic:
- **"GraphQL API"** / **"Real-time subscriptions"** = AppSync
- Integrates with: DynamoDB, Lambda, RDS, HTTP endpoints

---

## 7️⃣ ANALYTICS & BIG DATA - QUICK DECISIONS

### **Kinesis**

#### Kinesis Service Selection:
| Requirement | Service | Use Case |
|-------------|---------|----------|
| **"Real-time streaming"** | Kinesis Data Streams | Custom processing |
| **"Load to S3/Redshift/OpenSearch"** | Kinesis Data Firehose | No code, managed delivery |
| **"Real-time analytics (SQL)"** | Kinesis Data Analytics | SQL queries on streams |
| **"Process video streams"** | Kinesis Video Streams | Video analytics |

#### Kinesis Data Streams Heuristics:
- Shard = 1 MB/s in, 2 MB/s out
- Data retention: 24 hours (default) to 365 days
- Use when: Custom processing, exactly-once, ordering

#### Kinesis Firehose Heuristics:
- Near real-time (60 seconds latency)
- Auto-scales
- Use when: Simple ETL to S3/Redshift/OpenSearch

---

### **Athena**

#### Quick Heuristics:
| Scenario | Decision |
|----------|----------|
| **"Query S3 with SQL"** | Athena |
| **"Serverless analytics"** | Athena |
| **"No ETL needed"** | Athena |
| **"Ad-hoc queries"** | Athena |

**Cost optimization**: Use columnar formats (Parquet, ORC), partition data

---

### **Glue**

#### Quick Heuristics:
| Requirement | Component |
|-------------|-----------|
| **"ETL jobs"** | Glue ETL |
| **"Discover schema"** | Glue Crawler |
| **"Data catalog"** | Glue Data Catalog |
| **"Serverless Spark"** | Glue |

---

### **EMR (Elastic MapReduce)**

#### Quick Heuristic:
- **"Big Data frameworks"** (Hadoop, Spark, HBase, Presto) = EMR
- Use when: Need control over cluster, complex processing

---

### **QuickSight**

#### Quick Heuristic:
- **"Business Intelligence"** / **"Dashboards"** / **"Visualizations"** = QuickSight
- Serverless, pay-per-session

---

### **OpenSearch (Elasticsearch)**

#### Quick Heuristics:
| Scenario | Decision |
|----------|----------|
| **"Full-text search"** | OpenSearch |
| **"Log analytics"** | OpenSearch |
| **"Real-time dashboards"** | OpenSearch + Kibana |
| **"Clickstream analysis"** | OpenSearch |

---

### **MSK (Managed Streaming for Kafka)**

#### Quick Heuristic:
- **"Apache Kafka"** / **"Existing Kafka apps"** = MSK
- Alternative to Kinesis when you need Kafka specifically

---

## 8️⃣ MIGRATION & TRANSFER - QUICK DECISIONS

### **DMS (Database Migration Service)**

#### Quick Heuristics:
| Requirement | Solution |
|-------------|----------|
| **"Migrate database"** | DMS |
| **"Homogeneous migration"** (MySQL→MySQL) | DMS only |
| **"Heterogeneous migration"** (Oracle→PostgreSQL) | SCT + DMS |
| **"Continuous replication"** | DMS (CDC - Change Data Capture) |
| **"Minimal downtime"** | DMS |

---

### **MGN (Application Migration Service)**

#### Quick Heuristic:
- **"Lift and shift"** / **"Rehost"** = MGN
- Replaces AWS SMS (Server Migration Service)
- Continuous replication, minimal downtime

---

### **DataSync**

#### Quick Heuristics:
| Scenario | Service |
|----------|---------|
| **"Transfer on-prem to AWS"** (NFS/SMB) | DataSync |
| **"Transfer between AWS storage"** | DataSync |
| **"Scheduled transfers"** | DataSync |
| **"Incremental transfers"** | DataSync |

**DataSync vs Storage Gateway:**
- **DataSync**: One-time or scheduled migrations
- **Storage Gateway**: Ongoing hybrid storage

---

### **Snow Family**

#### Device Selection:
| Requirement | Device | Capacity | Use Case |
|-------------|--------|----------|----------|
| **"<80 TB"** | Snowcone | 8 TB | Edge computing, small migrations |
| **"<1 PB"** | Snowball Edge | 80 TB | Large migrations, edge compute |
| **">10 PB"** | Snowmobile | 100 PB | Datacenter migration |

#### Quick Heuristics:
- Use Snow when: Limited bandwidth, large data, secure transfer
- **Snowcone**: Portable, rugged
- **Snowball Edge**: Storage + compute
- **Snowmobile**: Exabyte-scale (truck)

---

### **Transfer Family**

#### Quick Heuristic:
- **"SFTP/FTPS/FTP to S3"** = AWS Transfer Family
- Managed file transfer service

---

## 9️⃣ MANAGEMENT & GOVERNANCE - QUICK DECISIONS

### **CloudFormation**

#### Quick Heuristics:
| Requirement | Solution |
|-------------|----------|
| **"Infrastructure as Code"** | CloudFormation |
| **"Repeatable deployments"** | CloudFormation |
| **"Cross-region deployment"** | CloudFormation StackSets |
| **"Drift detection"** | CloudFormation |
| **"Rollback on failure"** | CloudFormation (automatic) |

**Key Concepts:**
- Stack = collection of resources
- Template = JSON/YAML definition
- StackSets = multi-account/region

---

### **Systems Manager**

#### Systems Manager Components:
| Requirement | Component |
|-------------|-----------|
| **"Run commands on EC2"** | Run Command |
| **"Patch management"** | Patch Manager |
| **"Session access (no SSH)"** | Session Manager |
| **"Store configuration"** | Parameter Store |
| **"Automation workflows"** | Automation |
| **"Inventory management"** | Inventory |
| **"Compliance reporting"** | State Manager |

---

### **Organizations**

#### Quick Heuristics:
| Requirement | Solution |
|-------------|----------|
| **"Multiple AWS accounts"** | AWS Organizations |
| **"Consolidated billing"** | Organizations |
| **"Apply policies to accounts"** | Service Control Policies (SCPs) |
| **"Account-level restrictions"** | SCPs |

---

### **Control Tower**

#### Quick Heuristic:
- **"Multi-account setup"** + **"Best practices"** + **"Guardrails"** = Control Tower
- Built on Organizations, automates account setup

---

### **Trusted Advisor**

#### Quick Heuristics:
| Category | Checks |
|----------|--------|
| **Cost Optimization** | Underutilized resources |
| **Performance** | Service limits, throughput |
| **Security** | Open ports, IAM usage |
| **Fault Tolerance** | Backups, Multi-AZ |
| **Service Limits** | Approaching limits |

**Tiers:**
- Basic/Developer: Core checks
- Business/Enterprise: All checks

---

### **CloudWatch**

#### CloudWatch Components:
| Requirement | Component |
|-------------|-----------|
| **"Metrics"** | CloudWatch Metrics |
| **"Logs"** | CloudWatch Logs |
| **"Alarms"** | CloudWatch Alarms |
| **"Dashboards"** | CloudWatch Dashboards |
| **"Events"** | EventBridge (formerly CloudWatch Events) |
| **"Application monitoring"** | CloudWatch Application Insights |
| **"Container monitoring"** | Container Insights |
| **"Lambda monitoring"** | Lambda Insights |

---

### **X-Ray**

#### Quick Heuristic:
- **"Distributed tracing"** / **"Debug microservices"** = X-Ray
- Visualize request flow through services

---

## 🔟 ADDITIONAL SERVICES - QUICK DECISIONS

### **Cognito**

#### Cognito Component Selection:
| Requirement | Component | Use Case |
|-------------|-----------|----------|
| **"User pools"** | Cognito User Pools | Authentication (sign-up/sign-in) |
| **"Identity pools"** | Cognito Identity Pools | Authorization (AWS credentials) |
| **"Social login"** | User Pools | Facebook, Google, Amazon |
| **"SAML/OIDC"** | User Pools | Enterprise SSO |

---

### **SES (Simple Email Service)**

#### Quick Heuristic:
- **"Send transactional email"** / **"Marketing emails"** = SES
- **SNS** for notifications, **SES** for email campaigns

---

### **SQS vs SNS vs EventBridge**

| Use Case | Service | Reason |
|----------|---------|--------|
| **Decouple, queue** | SQS | Point-to-point |
| **Fan-out** | SNS | Pub/sub, multiple subscribers |
| **Event routing** | EventBridge | Rules, filtering, SaaS integration |
| **Schedule events** | EventBridge | Cron expressions |

---

### **Well-Architected Framework Pillars**

Quick reference for holistic architecture questions:

1. **Operational Excellence**: Automation, monitoring, continuous improvement
2. **Security**: Least privilege, encryption, defense in depth
3. **Reliability**: Multi-AZ, backup, auto-scaling
4. **Performance Efficiency**: Right-sizing, caching, serverless
5. **Cost Optimization**: Reserved, Spot, S3 lifecycle, right-sizing
6. **Sustainability**: Efficient resources, serverless when possible

---

## 🎯 UNIVERSAL EXAM SHORTCUTS

### **Keyword → Service Mapping**

| Keyword | Think Of |
|---------|----------|
| **"Serverless"** | Lambda, DynamoDB, API Gateway, S3 |
| **"Decouple"** | SQS, SNS, EventBridge |
| **"Cache"** | CloudFront, ElastiCache, DAX, API Gateway |
| **"Real-time"** | Kinesis Data Streams, DynamoDB Streams |
| **"Near real-time"** | Kinesis Firehose |
| **"Lowest latency"** | CloudFront, Global Accelerator, ElastiCache |
| **"High availability"** | Multi-AZ, Auto Scaling, ALB |
| **"Disaster recovery"** | Backups, Multi-Region, Pilot Light |
| **"Cost-effective"** | S3 IA/Glacier, Spot, Reserved, Serverless |
| **"Managed"** | RDS, DynamoDB, ECS Fargate, Lambda |
| **"Query S3"** | Athena, Redshift Spectrum |
| **"ETL"** | Glue |
| **"Big Data"** | EMR, Kinesis, Redshift |
| **"Graph"** | Neptune |
| **"Time-series"** | Timestream |
| **"Immutable"** | QLDB |
| **"Hybrid"** | Storage Gateway, Direct Connect, VPN |
| **"Migrate database"** | DMS (+SCT) |
| **"Lift and shift"** | MGN |
| **"Infrastructure as Code"** | CloudFormation |
| **"Threat detection"** | GuardDuty |
| **"Compliance"** | Config, CloudTrail, Security Hub |
| **"Container"** | ECS, EKS, Fargate |

---

## 🏆 FINAL EXAM STRATEGY

### **Question Analysis Framework:**

1. **Identify Primary Requirement**
   - Cost? Performance? Security? Availability?

2. **Spot Keywords**
   - "Serverless", "real-time", "decouple", "cost-effective"

3. **Eliminate Wrong Answers**
   - Over-engineered solutions
   - Non-managed when managed exists
   - Single-AZ when HA is mentioned

4. **Apply Heuristics**
   - Match keywords to service shortcuts above

5. **Verify Solution**
   - Does it meet ALL requirements?
   - Is it the SIMPLEST solution that works?

### **Time Management:**
- 65 questions ÷ 130 minutes = **2 minutes per question**
- Flag difficult questions (review at end)
- Don't overthink - first instinct often correct
- Use elimination strategy

### **Common Traps:**
- ❌ Choosing complex over simple
- ❌ Missing "cost-effective" requirement
- ❌ Ignoring "high availability" cues
- ❌ Selecting legacy services (Classic LB, etc.)

---

## 📚 SUMMARY: TOP 20 EXAM HEURISTICS

1. **"Serverless"** → Lambda + DynamoDB + S3 + API Gateway
2. **"High Availability"** → Multi-AZ + ALB + Auto Scaling
3. **"Disaster Recovery"** → Multi-Region replication
4. **"Cost-effective storage"** → S3 Intelligent-Tiering or Glacier
5. **"Decouple"** → SQS between components
6. **"Fan-out"** → SNS topic → Multiple SQS queues
7. **"Real-time"** → Kinesis Data Streams
8. **"Query S3"** → Athena
9. **"Cache database"** → ElastiCache (RDS) or DAX (DynamoDB)
10. **"Global content"** → CloudFront
11. **"Lowest latency routing"** → Route 53 Latency-based
12. **"Managed database"** → RDS (relational) or DynamoDB (NoSQL)
13. **"Migrate database"** → DMS (+ SCT if heterogeneous)
14. **"Encryption at rest"** → KMS
15. **"Encryption in transit"** → TLS/SSL (HTTPS)
16. **"Cross-account access"** → IAM Role with trust policy
17. **"Audit API calls"** → CloudTrail
18. **"Resource compliance"** → AWS Config
19. **"Threat detection"** → GuardDuty
20. **"Infrastructure as Code"** → CloudFormation

---

**🎉 You're now equipped with comprehensive service-specific shortcuts for AWS SAA-C03!**

*Use these heuristics to make split-second decisions and ace your exam!*
