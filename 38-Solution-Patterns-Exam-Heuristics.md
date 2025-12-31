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

---

**Good luck on your exam!** 🎉

*50 Solution Patterns covering 90%+ of SAA-C03 architecture questions*
