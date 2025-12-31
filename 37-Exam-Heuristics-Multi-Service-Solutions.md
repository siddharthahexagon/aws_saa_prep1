# AWS SAA-C03: Exam Heuristics & Multi-Service Solutions

## 🎯 Combined Architecture Patterns & Decision Rules for the Exam

This file covers the multi-step solutions and service combinations that appear frequently on the exam.

---

## 🏗️ PART 1: COMMON MULTI-SERVICE ARCHITECTURES

---

### 🌐 1. HIGHLY AVAILABLE WEB APPLICATION

**Scenario:** "Design a highly available, scalable web application"

```
Architecture:
┌─────────────────────────────────────────────────────────────────────┐
│                         Route 53 (DNS)                              │
│                    (Failover or Latency Routing)                    │
└─────────────────────────────────────────────────────────────────────┘
                                   │
                                   ▼
┌─────────────────────────────────────────────────────────────────────┐
│                    CloudFront (CDN) - Optional                      │
│               (Cache static content, SSL termination)               │
└─────────────────────────────────────────────────────────────────────┘
                                   │
                                   ▼
┌─────────────────────────────────────────────────────────────────────┐
│                   Application Load Balancer (ALB)                   │
│                        (Multi-AZ deployment)                        │
└─────────────────────────────────────────────────────────────────────┘
                          │                │
                          ▼                ▼
              ┌───────────────┐    ┌───────────────┐
              │  EC2 in AZ-a  │    │  EC2 in AZ-b  │
              │ (Auto Scaling │    │ (Auto Scaling │
              │    Group)     │    │    Group)     │
              └───────────────┘    └───────────────┘
                          │                │
                          ▼                ▼
┌─────────────────────────────────────────────────────────────────────┐
│                  RDS Multi-AZ or Aurora                             │
│              (Primary in AZ-a, Standby in AZ-b)                     │
└─────────────────────────────────────────────────────────────────────┘
```

**Components & Why:**
| Component | Purpose | Exam Heuristic |
|-----------|---------|----------------|
| Route 53 | DNS + health checks | "DNS failover" or "route to healthy" |
| CloudFront | Cache static content | "Global users" or "reduce latency" |
| ALB | Distribute HTTP traffic | "Web application" + "multiple instances" |
| Auto Scaling | Handle load changes | "Scale based on demand" |
| Multi-AZ RDS/Aurora | Database HA | "Database high availability" |

**Key Exam Points:**
- ✅ Always use Multi-AZ for production databases
- ✅ ALB for HTTP/HTTPS, not NLB
- ✅ Auto Scaling Group spans multiple AZs
- ✅ Route 53 health checks for DNS failover

---

### 🖼️ 2. SERVERLESS IMAGE PROCESSING PIPELINE

**Scenario:** "Process images automatically when uploaded"

```
Architecture:
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   S3 Bucket  │────▶│    Lambda    │────▶│   S3 Bucket  │
│   (Source)   │     │  (Process)   │     │   (Output)   │
└──────────────┘     └──────────────┘     └──────────────┘
       │                    │
       │                    ▼
       │            ┌──────────────┐
       │            │  DynamoDB    │
       │            │  (Metadata)  │
       │            └──────────────┘
       │
       ▼
┌──────────────┐
│ S3 Event     │
│ Notification │
└──────────────┘
```

**Flow:**
1. User uploads image to S3 source bucket
2. S3 Event triggers Lambda function
3. Lambda processes image (resize, thumbnail, etc.)
4. Lambda saves processed image to output bucket
5. Lambda stores metadata in DynamoDB

**Exam Heuristic:**
- "Automatically process uploads" → S3 Event + Lambda
- "Image processing" → Lambda (if < 15 min)
- "Store metadata" → DynamoDB (serverless, scales)

---

### 📊 3. REAL-TIME DATA ANALYTICS PIPELINE

**Scenario:** "Process streaming data in real-time"

```
Architecture:
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   IoT/Apps   │────▶│   Kinesis    │────▶│   Lambda     │
│  (Producers) │     │Data Streams  │     │ (Real-time)  │
└──────────────┘     └──────────────┘     └──────────────┘
                            │                    │
                            │                    ▼
                            │            ┌──────────────┐
                            │            │  DynamoDB    │
                            │            │  (Real-time) │
                            │            └──────────────┘
                            │
                            ▼
                     ┌──────────────┐     ┌──────────────┐
                     │   Kinesis    │────▶│     S3       │
                     │   Firehose   │     │  (Data Lake) │
                     └──────────────┘     └──────────────┘
                                                │
                                                ▼
                                         ┌──────────────┐
                                         │    Athena    │
                                         │   (Query)    │
                                         └──────────────┘
```

**Components & Why:**
| Component | Purpose | Exam Heuristic |
|-----------|---------|----------------|
| Kinesis Data Streams | Real-time ingestion | "Real-time" + "custom processing" |
| Lambda | Process each record | "Transform data" + "serverless" |
| Kinesis Firehose | Load to S3/Redshift | "Deliver to S3" + "near real-time" |
| S3 | Data lake storage | "Store raw data" + "analytics" |
| Athena | Query S3 data | "SQL on S3" + "serverless" |

**Alternative - Just Load to S3:**
```
Producers → Kinesis Firehose → S3 → Athena
```
Use this when: No real-time processing needed, just delivery

---

### 🔄 4. DECOUPLED MICROSERVICES ARCHITECTURE

**Scenario:** "Decouple services for reliability and scalability"

```
Architecture:
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   Service A  │────▶│     SQS      │────▶│   Service B  │
│  (Producer)  │     │   (Queue)    │     │  (Consumer)  │
└──────────────┘     └──────────────┘     └──────────────┘
                            │
                            ▼
                     ┌──────────────┐
                     │ Dead Letter  │
                     │    Queue     │
                     └──────────────┘
```

**Exam Heuristic:**
- "Decouple" → SQS
- "Handle failures gracefully" → SQS + DLQ
- "Retry failed messages" → Dead Letter Queue

**Variation - Fan-Out Pattern:**
```
                     ┌──────────────┐────▶ SQS Queue A ────▶ Service A
                     │              │
Service ────────────▶│     SNS      │────▶ SQS Queue B ────▶ Service B
                     │              │
                     └──────────────┘────▶ SQS Queue C ────▶ Service C
```

**Exam Heuristic:**
- "One event, multiple consumers" → SNS + SQS (Fan-out)
- "Fanout" → SNS to multiple SQS queues

---

### 🛒 5. E-COMMERCE ORDER PROCESSING

**Scenario:** "Process orders reliably with multiple steps"

```
Architecture:
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│  API Gateway │────▶│    Lambda    │────▶│Step Functions│
│   (Orders)   │     │  (Validate)  │     │  (Workflow)  │
└──────────────┘     └──────────────┘     └──────────────┘
                                                │
                     ┌──────────────────────────┼──────────────────────────┐
                     │                          │                          │
                     ▼                          ▼                          ▼
              ┌──────────────┐          ┌──────────────┐          ┌──────────────┐
              │    Lambda    │          │    Lambda    │          │    Lambda    │
              │   Payment    │          │  Inventory   │          │   Shipping   │
              └──────────────┘          └──────────────┘          └──────────────┘
                     │                          │                          │
                     ▼                          ▼                          ▼
              ┌──────────────┐          ┌──────────────┐          ┌──────────────┐
              │   Payment    │          │  DynamoDB    │          │     SNS      │
              │   Service    │          │ (Inventory)  │          │   (Notify)   │
              └──────────────┘          └──────────────┘          └──────────────┘
```

**Exam Heuristic:**
- "Orchestrate multiple steps" → Step Functions
- "Workflow with error handling" → Step Functions
- "Coordinate Lambda functions" → Step Functions

---

### 🌍 6. GLOBAL APPLICATION WITH DR

**Scenario:** "Deploy globally with disaster recovery"

```
Architecture:
┌─────────────────────────────────────────────────────────────────────┐
│                    Route 53 (Latency/Failover)                      │
└─────────────────────────────────────────────────────────────────────┘
                          │                │
              ┌───────────┘                └───────────┐
              ▼                                        ▼
┌─────────────────────────────┐        ┌─────────────────────────────┐
│       PRIMARY REGION        │        │      SECONDARY REGION       │
│         (us-east-1)         │        │        (eu-west-1)          │
├─────────────────────────────┤        ├─────────────────────────────┤
│  ALB → EC2 Auto Scaling     │        │  ALB → EC2 Auto Scaling     │
│            │                │        │            │                │
│            ▼                │        │            ▼                │
│    Aurora Primary ─────────────────────▶ Aurora Replica            │
│    (Write)                  │        │    (Read)                   │
└─────────────────────────────┘        └─────────────────────────────┘

S3 Bucket (Primary) ──── Cross-Region Replication ────▶ S3 Bucket (DR)
```

**Components & Why:**
| Component | Purpose | Exam Heuristic |
|-----------|---------|----------------|
| Route 53 Latency | Route to nearest | "Global users" + "low latency" |
| Route 53 Failover | DR failover | "Automatic failover" |
| Aurora Global | Cross-region DB | "< 1 sec replication" + "< 1 min failover" |
| S3 CRR | Replicate objects | "Cross-region object replication" |

**DR Strategy Levels:**

| Strategy | RTO | RPO | Cost | Exam Heuristic |
|----------|-----|-----|------|----------------|
| Backup & Restore | Hours | Hours | $ | "Lowest cost DR" |
| Pilot Light | Minutes | Minutes | $$ | "Minimal standby" + "quick scale" |
| Warm Standby | Minutes | Seconds | $$$ | "Reduced capacity standby" |
| Multi-Site Active | Seconds | Near-zero | $$$$ | "Zero downtime" + "active-active" |

---

### 🔐 7. SECURE APPLICATION WITH SECRETS

**Scenario:** "Securely manage database credentials"

```
Architecture:
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│     EC2      │────▶│   Secrets    │────▶│     RDS      │
│  (App Server)│     │   Manager    │     │  (Database)  │
└──────────────┘     └──────────────┘     └──────────────┘
       │                    │
       │                    ▼
       │            ┌──────────────┐
       │            │     KMS      │
       │            │ (Encryption) │
       │            └──────────────┘
       │
       ▼
┌──────────────┐
│  IAM Role    │
│ (Permission) │
└──────────────┘
```

**Flow:**
1. EC2 instance has IAM Role with Secrets Manager permission
2. Application retrieves secret from Secrets Manager
3. Secret is encrypted with KMS
4. Secrets Manager auto-rotates credentials
5. Application connects to RDS with current credentials

**Exam Heuristic:**
- "Rotate database credentials" → Secrets Manager
- "Encrypt secrets" → KMS
- "Application needs credentials" → IAM Role + Secrets Manager

**Alternative - Parameter Store:**
- Use when: No rotation needed, simple configs, cost-sensitive
- DON'T use when: Need auto-rotation (use Secrets Manager)

---

### 📦 8. DATA LAKE ARCHITECTURE

**Scenario:** "Build a data lake for analytics"

```
Architecture:
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│  Data Sources│     │              │     │     S3       │
│  - Databases │────▶│    Glue      │────▶│  Data Lake   │
│  - Apps      │     │    (ETL)     │     │  (Raw Zone)  │
│  - Files     │     │              │     │              │
└──────────────┘     └──────────────┘     └──────────────┘
                            │                    │
                            ▼                    │
                     ┌──────────────┐           │
                     │  Glue Data   │           │
                     │   Catalog    │           │
                     └──────────────┘           │
                            │                    │
                            ▼                    ▼
┌─────────────────────────────────────────────────────────────────────┐
│                            Athena                                    │
│                    (Serverless SQL Queries)                          │
└─────────────────────────────────────────────────────────────────────┘
                                   │
                                   ▼
┌─────────────────────────────────────────────────────────────────────┐
│                          QuickSight                                  │
│                      (BI Dashboards)                                 │
└─────────────────────────────────────────────────────────────────────┘
```

**Components & Why:**
| Component | Purpose | Exam Heuristic |
|-----------|---------|----------------|
| S3 | Data lake storage | "Store any data" + "cheap" |
| Glue | ETL processing | "Transform data" + "serverless ETL" |
| Glue Catalog | Metadata | "Schema discovery" + "catalog" |
| Athena | Query data | "SQL on S3" + "serverless" |
| QuickSight | Visualization | "Dashboards" + "BI" |

**Alternative - With Redshift:**
```
S3 → Glue → S3 → Redshift → QuickSight
```
Use Redshift when: Complex queries, joins, regular reporting

---

### 🔄 9. HYBRID CLOUD ARCHITECTURE

**Scenario:** "Connect on-premises to AWS"

```
Architecture:
┌─────────────────────────────────────────────────────────────────────┐
│                        ON-PREMISES                                   │
│  ┌──────────────┐     ┌──────────────┐     ┌──────────────┐        │
│  │   Servers    │     │   Storage    │     │   Database   │        │
│  └──────────────┘     │   Gateway    │     │              │        │
│         │             └──────────────┘     └──────────────┘        │
│         │                    │                    │                 │
└─────────┼────────────────────┼────────────────────┼─────────────────┘
          │                    │                    │
          ▼                    ▼                    ▼
┌─────────────────────────────────────────────────────────────────────┐
│              Direct Connect or Site-to-Site VPN                      │
└─────────────────────────────────────────────────────────────────────┘
          │                    │                    │
          ▼                    ▼                    ▼
┌─────────────────────────────────────────────────────────────────────┐
│                             AWS                                      │
│  ┌──────────────┐     ┌──────────────┐     ┌──────────────┐        │
│  │     EC2      │     │      S3      │     │     RDS      │        │
│  └──────────────┘     └──────────────┘     └──────────────┘        │
└─────────────────────────────────────────────────────────────────────┘
```

**Connectivity Options:**
| Option | Speed | Setup | Use Case | Exam Heuristic |
|--------|-------|-------|----------|----------------|
| Site-to-Site VPN | Variable | Days | Quick, encrypted | "Quick setup" or "encrypted" |
| Direct Connect | 1-100 Gbps | Weeks | Consistent, high bandwidth | "Consistent latency" or "high throughput" |
| DX + VPN | 1-100 Gbps | Weeks | DX with encryption | "Consistent" + "encrypted" |

**Storage Gateway Use Cases:**
| Type | On-Prem Protocol | AWS Backend | Exam Heuristic |
|------|------------------|-------------|----------------|
| File Gateway | NFS/SMB | S3 | "NFS to S3" |
| Volume (Cached) | iSCSI | S3 | "Block storage, data in cloud" |
| Volume (Stored) | iSCSI | S3 (backup) | "Block storage, data on-prem" |
| Tape Gateway | iSCSI (VTL) | S3/Glacier | "Replace tape backup" |

---

### 🛡️ 10. SECURE MULTI-TIER ARCHITECTURE

**Scenario:** "Design a secure 3-tier application"

```
Architecture:
┌─────────────────────────────────────────────────────────────────────┐
│                              VPC                                     │
├─────────────────────────────────────────────────────────────────────┤
│  ┌─────────────────────── PUBLIC SUBNET ──────────────────────────┐ │
│  │                                                                 │ │
│  │   ┌─────────────┐                                              │ │
│  │   │    ALB      │  ◄──── Internet Gateway                      │ │
│  │   └─────────────┘                                              │ │
│  │                                                                 │ │
│  └─────────────────────────────────────────────────────────────────┘ │
│                               │                                      │
│                               ▼                                      │
│  ┌─────────────────── PRIVATE SUBNET (App) ───────────────────────┐ │
│  │                                                                 │ │
│  │   ┌─────────────┐    ┌─────────────┐                           │ │
│  │   │  EC2 (App)  │    │  EC2 (App)  │  ◄──── NAT Gateway        │ │
│  │   │  AZ-a       │    │  AZ-b       │       (for updates)       │ │
│  │   └─────────────┘    └─────────────┘                           │ │
│  │                                                                 │ │
│  └─────────────────────────────────────────────────────────────────┘ │
│                               │                                      │
│                               ▼                                      │
│  ┌─────────────────── PRIVATE SUBNET (Data) ──────────────────────┐ │
│  │                                                                 │ │
│  │   ┌─────────────┐    ┌─────────────┐                           │ │
│  │   │ RDS Primary │    │ RDS Standby │  ◄──── No internet access │ │
│  │   │  AZ-a       │    │  AZ-b       │                           │ │
│  │   └─────────────┘    └─────────────┘                           │ │
│  │                                                                 │ │
│  └─────────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────┘
```

**Security Layers:**
| Layer | Component | Purpose | Exam Heuristic |
|-------|-----------|---------|----------------|
| Edge | WAF + Shield | DDoS, SQL injection | "Protect from attacks" |
| Network | Security Groups | Instance firewall | "Allow specific traffic" |
| Network | NACLs | Subnet firewall | "Block specific IPs" |
| Data | KMS | Encryption at rest | "Encrypt data" |
| Data | SSL/TLS | Encryption in transit | "Encrypt traffic" |
| Access | IAM Roles | Service permissions | "Service access" |

**Exam Heuristic:**
- Public subnet: Only load balancers, NAT gateways, bastion hosts
- Private subnet: Application servers, databases
- "Block IP" → NACL (not Security Group)
- "Instance firewall" → Security Group

---

## 🎯 PART 2: EXAM HEURISTICS BY CATEGORY

---

### 💰 COST OPTIMIZATION HEURISTICS

| Scenario | Solution | Why |
|----------|----------|-----|
| Steady-state 24/7 workload | Reserved Instances or Savings Plans | Up to 72% savings |
| Batch processing, can be interrupted | Spot Instances | Up to 90% savings |
| Short-term, unpredictable | On-Demand | Flexibility |
| Infrequent S3 access (30+ days) | S3 Standard-IA | Lower storage cost |
| Unknown S3 access pattern | S3 Intelligent-Tiering | Auto-optimizes |
| Archive data (hours retrieval OK) | S3 Glacier Flexible | Very low cost |
| 7+ year compliance archive | S3 Glacier Deep Archive | Lowest cost |
| Auto-delete old data | S3 Lifecycle Policy | Automated savings |
| Right-size instances | Compute Optimizer | Recommendations |
| Unused EBS volumes | Delete or snapshot | Stop paying |
| Idle load balancers | Remove if no traffic | Stop paying |

### 🔒 SECURITY HEURISTICS

| Scenario | Solution | Why |
|----------|----------|-----|
| EC2 needs to access S3 | IAM Role | Never use access keys on EC2 |
| Store database credentials | Secrets Manager | Auto-rotation |
| Store config values | Parameter Store | Free, simple |
| Encrypt data at rest | KMS | Managed keys |
| Need FIPS 140-2 Level 3 | CloudHSM | Dedicated HSM |
| SSL certificate for ALB | ACM | Free, auto-renew |
| Detect threats automatically | GuardDuty | ML-based |
| Scan for vulnerabilities | Inspector | CVEs |
| Find PII in S3 | Macie | Data discovery |
| Audit API calls | CloudTrail | Who did what |
| Track config changes | Config | Compliance |
| Block IP address | NACL | Deny rules |
| Web app attacks (SQLi, XSS) | WAF | Layer 7 protection |
| DDoS protection | Shield | Free (Standard) |

### ⚡ PERFORMANCE HEURISTICS

| Scenario | Solution | Why |
|----------|----------|-----|
| Reduce database load | ElastiCache or DAX | Caching |
| Cache DynamoDB specifically | DAX | Microsecond latency |
| Global static content | CloudFront | Edge caching |
| Global dynamic content | Global Accelerator | AWS backbone |
| Reduce Lambda cold starts | Provisioned Concurrency | Pre-warm |
| High IOPS database | io2 EBS or Instance Store | Performance |
| High throughput big data | st1 EBS | Sequential reads |
| Parallel file system | FSx for Lustre | HPC workloads |
| Read-heavy database | Read Replicas | Scale reads |
| Accelerate S3 uploads | Transfer Acceleration | Global uploads |

### 🔄 HIGH AVAILABILITY HEURISTICS

| Scenario | Solution | Why |
|----------|----------|-----|
| Database HA | Multi-AZ RDS or Aurora | Automatic failover |
| Fastest DB failover | Aurora | 10-30 seconds |
| Application HA | Multi-AZ + Auto Scaling + ALB | Redundancy |
| NAT Gateway HA | One per AZ | AZ-level redundancy |
| Cross-region DR | Aurora Global + S3 CRR | < 1 sec RPO |
| Active-active multi-region | DynamoDB Global Tables | No failover needed |
| Stateless application | Store sessions externally | ElastiCache or DynamoDB |
| DNS failover | Route 53 health checks | Automatic |

### 📊 ANALYTICS HEURISTICS

| Scenario | Solution | Why |
|----------|----------|-----|
| Query S3 with SQL | Athena | Serverless, pay per query |
| Data warehouse | Redshift | OLAP, complex queries |
| Real-time streaming | Kinesis Data Streams | Custom processing |
| Just deliver to S3 | Kinesis Firehose | Managed delivery |
| ETL jobs | Glue | Serverless ETL |
| Data catalog | Glue Data Catalog | Schema discovery |
| Big data processing | EMR | Hadoop/Spark |
| BI dashboards | QuickSight | Visualization |
| Log analytics | OpenSearch | Search and analyze |

### 🔗 INTEGRATION HEURISTICS

| Scenario | Solution | Why |
|----------|----------|-----|
| Decouple services | SQS | Queue-based |
| Fan-out to multiple | SNS → multiple SQS | Pub/sub |
| Guaranteed order | SQS FIFO | Exactly-once, ordered |
| Event-driven automation | EventBridge | Rules and scheduling |
| Orchestrate Lambda | Step Functions | Workflows |
| Existing MQ migration | Amazon MQ | ActiveMQ/RabbitMQ |
| Handle failed messages | Dead Letter Queue | Debugging |

### 🚀 MIGRATION HEURISTICS

| Scenario | Solution | Why |
|----------|----------|-----|
| Lift-and-shift VMs | MGN (Application Migration Service) | Minimal changes |
| Migrate database | DMS | Minimal downtime |
| Different DB engine | SCT + DMS | Schema conversion |
| Transfer 10 TB, slow internet | Snowball Edge | Offline transfer |
| Transfer 100 PB | Snowmobile | Exabyte scale |
| Ongoing file sync | DataSync | Automated |
| SFTP/FTPS workflow | Transfer Family | Managed SFTP |

---

## 🎯 PART 3: KEYWORD → SOLUTION MAPPING

### When the question says... Choose this solution:

| Keyword(s) | Solution |
|------------|----------|
| "Decouple" | SQS |
| "Fan-out" | SNS → SQS |
| "Event-driven" | Lambda + EventBridge |
| "Workflow" / "Orchestrate" | Step Functions |
| "Real-time streaming" | Kinesis Data Streams |
| "Load streaming data to S3" | Kinesis Firehose |
| "Query S3" | Athena |
| "Data warehouse" | Redshift |
| "Serverless" | Lambda / Fargate / DynamoDB |
| "NoSQL" / "Key-value" | DynamoDB |
| "Millisecond latency" | DynamoDB |
| "Microsecond latency" | DAX (for DynamoDB) |
| "Cache database" | ElastiCache Redis |
| "Session store" | ElastiCache Redis or DynamoDB |
| "Graph database" | Neptune |
| "Ledger" / "Immutable" | QLDB |
| "Time-series" | Timestream |
| "MongoDB" | DocumentDB |
| "Cassandra" | Keyspaces |
| "Audit API calls" | CloudTrail |
| "Track config changes" | Config |
| "Threat detection" | GuardDuty |
| "Vulnerability scan" | Inspector |
| "PII in S3" | Macie |
| "Secrets rotation" | Secrets Manager |
| "Encryption keys" | KMS |
| "SSL certificate" | ACM |
| "Block IP" | NACL |
| "DDoS" | Shield |
| "SQL injection" / "XSS" | WAF |
| "Path-based routing" | ALB |
| "Static IP for LB" | NLB |
| "Global static content" | CloudFront |
| "Global acceleration, no cache" | Global Accelerator |
| "Lowest latency routing" | Route 53 Latency |
| "Country-based routing" | Route 53 Geolocation |
| "A/B testing" | Route 53 Weighted |
| "Active-passive DR" | Route 53 Failover |
| "Connect VPCs" | VPC Peering (2) or Transit Gateway (many) |
| "Private S3 access" | VPC Gateway Endpoint |
| "Dedicated connection" | Direct Connect |
| "Quick encrypted connection" | Site-to-Site VPN |
| "NFS on-prem to S3" | Storage Gateway - File Gateway |
| "Replace tape backup" | Storage Gateway - Tape Gateway |
| "Lift and shift" | MGN |
| "Database migration" | DMS |
| "Different database engine" | SCT + DMS |
| "Transfer petabytes offline" | Snowball Edge |

---

## 🎯 PART 4: COMMON EXAM TRAPS

### ❌ WRONG vs ✅ RIGHT

| Trap | Wrong Answer | Right Answer | Why |
|------|--------------|--------------|-----|
| EC2 needs S3 access | Access keys in code | IAM Role | Security best practice |
| "Highly available database" | Single RDS | Multi-AZ RDS | HA requires Multi-AZ |
| "Serverless SQL" | RDS | Athena | Athena is serverless |
| "Lowest cost archive" | Glacier Instant | Glacier Deep Archive | Deep Archive is cheapest |
| "Block specific IP" | Security Group | NACL | SGs can't deny |
| "Secrets with rotation" | Parameter Store | Secrets Manager | PS doesn't auto-rotate |
| "Connect 10 VPCs" | VPC Peering | Transit Gateway | Peering doesn't scale |
| "Real-time, custom processing" | Firehose | Data Streams | Firehose is near real-time |
| "< 1 sec replication" | RDS Read Replica | Aurora Global | RDS takes minutes |
| "Guaranteed message order" | SQS Standard | SQS FIFO | Standard is best-effort |
| "Cache with persistence" | Memcached | Redis | Memcached has no persistence |
| "Multi-region active-active" | RDS Multi-AZ | DynamoDB Global Tables | Multi-AZ is single region |

---

## 🏁 QUICK REFERENCE: COMPLETE SOLUTION PATTERNS

### Pattern 1: Static Website
```
S3 → CloudFront → Route 53
```

### Pattern 2: Dynamic Website
```
Route 53 → CloudFront → ALB → EC2 (ASG) → RDS Multi-AZ
```

### Pattern 3: Serverless API
```
Route 53 → CloudFront → API Gateway → Lambda → DynamoDB
```

### Pattern 4: Data Pipeline
```
Sources → Kinesis Firehose → S3 → Glue → Athena → QuickSight
```

### Pattern 5: Real-Time Processing
```
Sources → Kinesis Data Streams → Lambda → DynamoDB
```

### Pattern 6: Decoupled Processing
```
Producer → SQS → Consumer (with DLQ for failures)
```

### Pattern 7: Fan-Out
```
Producer → SNS → Multiple SQS → Multiple Consumers
```

### Pattern 8: Event-Driven
```
S3/DynamoDB/Custom → EventBridge → Lambda/Step Functions
```

### Pattern 9: Hybrid Storage
```
On-Prem (NFS) → Storage Gateway → S3 → Glacier (Lifecycle)
```

### Pattern 10: Cross-Region DR
```
Primary: Route 53 → ALB → EC2 → Aurora
Secondary: Route 53 Failover → ALB → EC2 → Aurora Global Replica
```

---

## 🏆 EXAM DAY CHECKLIST

✅ Read the ENTIRE question carefully
✅ Identify the PRIMARY requirement (cost, performance, security, availability)
✅ Look for keywords that map to specific services
✅ Eliminate obviously wrong answers first
✅ Choose AWS-managed over self-managed when possible
✅ Remember: Security and HA are priorities unless cost is specified
✅ "Serverless" usually means Lambda, Fargate, DynamoDB, Athena
✅ "Decouple" almost always means SQS
✅ "Real-time" usually means Kinesis (Streams for custom, Firehose for delivery)
✅ Trust your preparation!

---

**Good luck on your exam!** 🎉

*This guide covers 80%+ of the multi-service architecture questions on SAA-C03*
