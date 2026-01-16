# AWS SAA-C03: MASTER HEURISTICS - Complete Quick Decision Guide

## 🎯 Ultimate Quick Reference: All Services, All Domains, All Decision Patterns

**Purpose:** Lightning-fast service selection for ANY exam question. If-then rules for instant answers.

---

## 📋 TABLE OF CONTENTS

1. [COMPUTE - Quick Decision Heuristics](#compute---quick-decision-heuristics)
2. [STORAGE - Quick Decision Heuristics](#storage---quick-decision-heuristics)
3. [DATABASE - Quick Decision Heuristics](#database---quick-decision-heuristics)
4. [NETWORKING - Quick Decision Heuristics](#networking---quick-decision-heuristics)
5. [SECURITY - Quick Decision Heuristics](#security---quick-decision-heuristics)
6. [MONITORING - Quick Decision Heuristics](#monitoring---quick-decision-heuristics)
7. [INTEGRATION - Quick Decision Heuristics](#integration---quick-decision-heuristics)
8. [ANALYTICS - Quick Decision Heuristics](#analytics---quick-decision-heuristics)
9. [MIGRATION - Quick Decision Heuristics](#migration---quick-decision-heuristics)
10. [DOMAIN-SPECIFIC PATTERNS](#domain-specific-patterns)

---

# COMPUTE - Quick Decision Heuristics

## ⚡ EC2 vs Lambda vs Container vs Batch - Master Decision

| If Question Says... | Then Choose | Why |
|---------------------|-------------|-----|
| "Serverless" + < 15 min | **Lambda** | Serverless, automatic scaling, pay per invocation |
| "Event-driven" + short duration | **Lambda** | Perfect for event triggers |
| "> 15 minutes" or "persistent connection" | **EC2** or **Container** | Lambda max is 15 min |
| "Full OS control" or "custom software" | **EC2** | Need OS-level access |
| "Containers" + "AWS native" | **ECS** | Native AWS service, simpler |
| "Containers" + "Kubernetes" or "K8s" | **EKS** | Kubernetes required |
| "Containers" + "no server management" | **Fargate** | Serverless containers |
| "Batch processing" + "any scale" | **AWS Batch** | Auto-provisions, manages queues |
| "Simple web hosting" + "fixed price" | **Lightsail** | VPS, predictable billing |
| "Quick deploy" + "web app" + "don't manage infra" | **Elastic Beanstalk** | PaaS, handles everything |

---

## 🖥️ EC2 Instance Type Selection - One Rule Per Use Case

| Keyword in Question | Instance Family | Specific Type |
|---------------------|----------------|---------------|
| "General purpose" / "balanced" / "no specific requirement" | **M** | M5, M6i |
| "Burstable" / "variable workload" / "spiky traffic" | **T** | T3, T3a, T4g |
| "CPU-intensive" / "batch processing" / "HPC" | **C** | C5, C6i, C6g |
| "Memory-intensive" / "in-memory DB" / "caching" | **R** | R5, R6i, R6g |
| "SAP HANA" / "extreme memory" / "massive RAM" | **X** | X1, X2 |
| "High I/O" / "NoSQL" / "sequential read/write" | **I** | I3, I3en |
| "Data warehousing" / "MapReduce" / "distributed FS" | **D** | D2, D3 |
| "Machine learning training" / "GPU compute" | **P** | P3, P4 |
| "Graphics" / "video encoding" / "rendering" | **G** | G4, G5 |
| "ML inference" / "cost-effective inference" | **Inf** | Inf1, Inf2 |
| "Arm processor" / "Graviton" / "best price-performance" | **g suffix** | T4g, M6g, C6g, R6g |

---

## 💰 EC2 Pricing Model - Instant Selection

| Requirement | Model | Discount | Key Point |
|-------------|-------|----------|-----------|
| **"Steady-state" + "predictable" + "1-3 years"** | Reserved (Standard) | Up to 72% | Cannot change instance family |
| **"Long-term" + "may change type"** | Reserved (Convertible) | Up to 66% | Can change instance type/family |
| **"Flexible compute" + commitment** | Savings Plans | Up to 72% | Applies to EC2, Fargate, Lambda |
| **"Fault-tolerant" + "flexible start/end"** | Spot | Up to 90% | Can be interrupted with 2-min warning |
| **"Test/dev" + "short-term" + "unpredictable"** | On-Demand | None | Pay by hour/second, no commitment |
| **"BYOL" + "per-socket licensing"** | Dedicated Host | Varies | Physical server, per-socket visibility |
| **"Compliance" + "hardware isolation"** | Dedicated Instance | Varies | Hardware isolation, no per-socket visibility |

---

## 🐳 Container Service Selection Matrix

| Requirement | Service | Details |
|-------------|---------|---------|
| **"Docker" + "simple" + "AWS native"** | ECS | Easiest to start, deep AWS integration |
| **"Kubernetes" or "K8s" mentioned** | EKS | Managed Kubernetes |
| **"No server management" + containers** | Fargate (with ECS/EKS) | Serverless containers |
| **"Store container images"** | ECR | Docker image registry |
| **"Batch jobs" + containers** | AWS Batch | Can use ECS under the hood |

---

## ⚙️ Lambda - Use or Not Use

### ✅ USE Lambda When:
- Event-driven processing (S3 uploads, DynamoDB changes, API calls)
- < 15 minutes execution time
- Unpredictable or variable traffic
- Want zero server management
- Pay only for actual compute time

### ❌ DON'T Use Lambda When:
- Need > 15 minutes
- Long-running processes (web servers, databases)
- Need persistent connections
- Very high sustained traffic (EC2 might be cheaper)
- Need full OS control

---

# STORAGE - Quick Decision Heuristics

## 📦 Main Storage Service Selection

| If Question Says... | Then Choose | Why |
|---------------------|-------------|-----|
| **"Object storage" / "unlimited" / "files"** | **S3** | Object storage, unlimited capacity |
| **"Block storage" / "EC2 volume" / "database"** | **EBS** | Block storage for EC2 |
| **"Shared file system" / "multiple EC2" / "Linux"** | **EFS** | NFS for Linux, shared across instances |
| **"Windows file system" / "SMB" / "Active Directory"** | **FSx for Windows** | Windows native, AD integration |
| **"HPC" / "parallel file system" / "Lustre"** | **FSx for Lustre** | High performance, sub-ms latency |
| **"NetApp" / "multi-protocol" / "NFS + SMB"** | **FSx for NetApp ONTAP** | NetApp features on AWS |
| **"OpenZFS" / "snapshots" / "cloning"** | **FSx for OpenZFS** | ZFS features, Linux |
| **"Temporary" / "highest performance" / "ephemeral"** | **Instance Store** | Physically attached, data lost on stop |
| **"Hybrid storage" / "on-prem to cloud"** | **Storage Gateway** | Extends on-prem to AWS |
| **"Archive" / "long-term retention" / "7+ years"** | **S3 Glacier** | Low-cost archival storage |

---

## 🪣 S3 Storage Class Selection - Lightning Fast

| Requirement Pattern | Storage Class | Key Characteristic |
|---------------------|---------------|-------------------|
| **"Frequently accessed" / default** | S3 Standard | 99.99% availability, 11 9s durability |
| **"Unknown access pattern" / "changing"** | Intelligent-Tiering | Auto-moves between tiers, small fee |
| **"Infrequent" + "instant retrieval"** | Standard-IA | 30-day min, retrieval fee |
| **"Infrequent" + "recreatable" / "secondary"** | One Zone-IA | Single AZ, 20% cheaper |
| **"Archive" + "instant access"** | Glacier Instant Retrieval | Archive with ms access |
| **"Archive" + "hours OK"** | Glacier Flexible Retrieval | Minutes to hours retrieval |
| **"7+ years" / "compliance" / "lowest cost"** | Glacier Deep Archive | 12-48 hour retrieval |

---

## 💿 EBS Volume Type Selection

| Use Case Keywords | Volume Type | Performance | Cost |
|-------------------|-------------|-------------|------|
| **"General purpose" / default / "boot volume"** | **gp3** | 3,000-16,000 IOPS | Moderate |
| **"High performance" / "> 16,000 IOPS"** | **io2** | 64,000 IOPS max | Highest |
| **"Database" / "mission-critical" / "low latency"** | **io2 Block Express** | 256,000 IOPS | Very high |
| **"Big data" / "data warehouse" / "throughput"** | **st1 (HDD)** | 500 MB/s throughput | Low |
| **"Cold storage" / "infrequent access" / "lowest cost"** | **sc1 (HDD)** | 250 MB/s throughput | Lowest |

**Quick Rule:**
- Need IOPS? → SSD (gp3 or io2)
- Need throughput? → HDD (st1)
- Cheapest? → sc1

---

## 📁 File System Selection Matrix

| Requirement | Service | Protocol | Key Point |
|-------------|---------|----------|-----------|
| **Linux + shared + NFS** | EFS | NFS v4 | Auto-scales, pay per use |
| **Windows + SMB + AD** | FSx for Windows | SMB | Windows native features |
| **HPC + parallel + fast** | FSx for Lustre | POSIX | Sub-millisecond latency |
| **Multi-protocol + enterprise** | FSx for NetApp ONTAP | NFS, SMB, iSCSI | NetApp snapshots/clones |
| **Linux + ZFS features** | FSx for OpenZFS | NFS | ZFS snapshots/clones |

---

## 🚚 Data Transfer Method Selection

| Scenario | Method | Reason |
|----------|--------|--------|
| **"< 10 TB" + "good internet"** | **DataSync** or **S3 Transfer Acceleration** | Online transfer |
| **"10-80 TB" + "limited bandwidth"** | **Snowcone** (8 TB) or **Snowball Edge** (80 TB) | Offline device |
| **"80 TB - 80 PB"** | **Snowball Edge** (80 TB each) | Multiple devices |
| **"> 100 PB" / "exabyte"** | **Snowmobile** (100 PB) | Shipping container |
| **"On-prem to AWS" + "continuous sync"** | **DataSync** | Automated, scheduled transfers |
| **"Edge computing" + "data collection"** | **Snowcone** | Small, rugged, edge processing |
| **"Edge ML" + "compute needed"** | **Snowball Edge Compute Optimized** | vCPUs, GPUs available |

---

# DATABASE - Quick Decision Heuristics

## 🗄️ Database Type Selection - First Decision

| Requirement | Category | Then Consider |
|-------------|----------|---------------|
| **"Relational" / "SQL" / "ACID" / "joins"** | Relational | RDS or Aurora |
| **"NoSQL" / "key-value" / "document" / "scale"** | NoSQL | DynamoDB, DocumentDB |
| **"Graph" / "relationships" / "social"** | Graph | Neptune |
| **"Cache" / "session" / "millisecond"** | Cache | ElastiCache (Redis/Memcached), DAX |
| **"Data warehouse" / "OLAP" / "analytics" / "petabyte"** | Warehouse | Redshift |
| **"Time-series" / "IoT" / "metrics"** | Time-Series | Timestream |
| **"Ledger" / "immutable" / "audit trail"** | Ledger | QLDB |

---

## 🔄 Relational Database Selection

| Requirement | Service | Why |
|-------------|---------|-----|
| **"MySQL" or "PostgreSQL" + "5x performance"** | **Aurora** | AWS-built, auto-scaling storage |
| **"MySQL/PostgreSQL" + "standard"** | **RDS** | Managed, simpler |
| **"Oracle" / "SQL Server" / "MariaDB" / "Db2"** | **RDS** | Only option for these engines |
| **"Serverless" + "unpredictable workload"** | **Aurora Serverless v2** | Auto-scales compute, pay per ACU |
| **"Global" + "< 1 sec replication"** | **Aurora Global Database** | Cross-region, RPO < 1s |

### RDS Multi-AZ vs Read Replica

| Feature | Multi-AZ | Read Replica |
|---------|----------|--------------|
| **Purpose** | High Availability | Read Scaling & DR |
| **Synchronous** | Yes | No (async) |
| **Automatic Failover** | Yes | No |
| **Can be promoted** | Automatic | Manual |
| **Same region** | Usually | Cross-region possible |
| **Use when** | "HA" / "automatic failover" | "Read-heavy" / "reporting" / "DR" |

---

## ⚡ NoSQL Database Selection

| Keyword | Service | Details |
|---------|---------|---------|
| **"DynamoDB" / "serverless NoSQL" / "key-value"** | **DynamoDB** | Single-digit ms, auto-scale |
| **"MongoDB" / "document database"** | **DocumentDB** | MongoDB-compatible |
| **"Cassandra" / "wide-column"** | **Keyspaces** | Cassandra-compatible |
| **"Graph" / "relationships" / "fraud detection"** | **Neptune** | Graph database (Gremlin/SPARQL) |
| **"In-memory" / "Redis" / "microsecond DynamoDB"** | **DAX** | DynamoDB Accelerator |
| **"Redis" / "persist" / "complex data"** | **ElastiCache Redis** | Managed Redis with persistence |
| **"Memcached" / "simple caching"** | **ElastiCache Memcached** | Multi-threaded, no persistence |

---

## 🎯 DynamoDB Capacity Mode

| Requirement | Mode | Reason |
|-------------|------|--------|
| **"Predictable traffic" / "cost optimization"** | **Provisioned** | Cheaper if consistent |
| **"Unknown" / "spiky" / "unpredictable"** | **On-Demand** | Pay per request, auto-scales |

### DynamoDB Index Selection

| Index Type | When to Use | Limitation |
|------------|-------------|------------|
| **LSI (Local Secondary Index)** | Same partition key, different sort key | Max 5, must create at table creation |
| **GSI (Global Secondary Index)** | Different partition key and/or sort key | Max 20, can create anytime |

**Exam Heuristic:**
- "Different partition key" → **GSI**
- "Same partition, different sort" → **LSI**
- "Query pattern changed" → **GSI** (can add later)

---

## 🗂️ Caching Strategy Selection

| Scenario | Service | Why |
|----------|---------|-----|
| **"DynamoDB" + "microsecond latency"** | **DAX** | DynamoDB-specific, transparent |
| **"Database" + "complex queries" + "cache"** | **ElastiCache Redis** | General-purpose, flexible |
| **"Session store" / "persist sessions"** | **ElastiCache Redis** | Persistence + replication |
| **"Simple cache" + "multi-threaded"** | **ElastiCache Memcached** | Simpler, no persistence |
| **"Leaderboard" / "ranking" / "sorted sets"** | **ElastiCache Redis** | Redis data structures |
| **"Pub/Sub" / "real-time messaging"** | **ElastiCache Redis** | Redis pub/sub feature |

---

## 📊 Analytics & Data Warehouse

| Requirement | Service | Use Case |
|-------------|---------|----------|
| **"Petabyte" / "data warehouse" / "OLAP"** | **Redshift** | Columnar storage, MPP |
| **"Query S3 with SQL" / "serverless query"** | **Athena** | Query S3 directly, pay per query |
| **"Redshift" + "query S3"** | **Redshift Spectrum** | Extend Redshift to S3 |
| **"Big data" / "Hadoop" / "Spark"** | **EMR** | Managed Hadoop/Spark cluster |
| **"Visualize" / "BI" / "dashboards"** | **QuickSight** | Serverless BI tool |

---

# NETWORKING - Quick Decision Heuristics

## 🌐 VPC Components - Quick Selection

| Requirement | Component | Details |
|-------------|-----------|---------|
| **"Private network" / "isolated resources"** | **VPC** | Your own virtual network |
| **"Public access" / "internet-facing"** | **Public Subnet** + **IGW** | Route to Internet Gateway |
| **"Private but needs internet" / "outbound only"** | **Private Subnet** + **NAT Gateway** | NAT for outbound, no inbound |
| **"Instance firewall" / "allow rules only"** | **Security Group** | Stateful, instance-level |
| **"Subnet firewall" / "allow + deny"** | **NACL** | Stateless, subnet-level |
| **"Connect two VPCs"** | **VPC Peering** | Direct connection, non-transitive |
| **"Connect many VPCs/on-prem"** | **Transit Gateway** | Hub, transitive routing |
| **"Private S3 access"** | **VPC Endpoint (Gateway)** | Free, route table entry |
| **"Private AWS service access"** | **VPC Endpoint (Interface)** | ENI with private IP, costs apply |

---

## 🔀 Load Balancer Selection - One Rule

| Requirement | Load Balancer | OSI Layer | Key Feature |
|-------------|---------------|-----------|-------------|
| **"HTTP/HTTPS" / "web app" / "path routing"** | **ALB** | Layer 7 | Path, host, header routing |
| **"TCP/UDP" / "extreme performance" / "static IP"** | **NLB** | Layer 4 | Ultra-low latency, millions RPS |
| **"Third-party appliance" / "firewall" / "IDS"** | **GWLB** | Layer 3 | For virtual appliances |
| **"Legacy"** (avoid) | **CLB** | Layer 4/7 | Deprecated |

**ALB vs NLB Decision:**
- HTTP/HTTPS? → **ALB**
- Need path routing? → **ALB**
- Need static IP? → **NLB**
- Need extreme performance? → **NLB**
- TCP/UDP (not HTTP)? → **NLB**

---

## 🌍 DNS & Content Delivery

### Route 53 Routing Policies - Quick Selection

| Requirement | Policy | Use Case |
|-------------|--------|----------|
| **"Single resource" / simple** | Simple | One IP, no health check |
| **"Active-passive failover" / "DR"** | Failover | Primary + secondary with health check |
| **"Geographically closest" / "lowest latency"** | Latency | Route based on measured latency |
| **"User location" / "compliance" / "content restriction"** | Geolocation | Route based on user's geographic location |
| **"Combine geo + latency"** | Geoproximity | Bias-based routing |
| **"Multiple resources" / "distribute load"** | Weighted | Percentage-based traffic split |
| **"Multiple records" / "random" / "all healthy"** | Multivalue Answer | Return multiple IPs with health checks |

### CloudFront vs Global Accelerator

| Feature | CloudFront | Global Accelerator |
|---------|------------|-------------------|
| **Layer** | Layer 7 (HTTP/HTTPS) | Layer 4 (TCP/UDP) |
| **Caching** | Yes | No |
| **Use Case** | Static/dynamic content delivery | TCP/UDP acceleration |
| **Static IP** | No | Yes (2 anycast IPs) |
| **Choose when** | "Cache" / "content delivery" / "HTTP" | "Non-HTTP" / "static IP" / "no cache" |

---

## 🔗 Hybrid Connectivity

| Requirement | Service | Details |
|-------------|---------|---------|
| **"Dedicated connection" / "consistent latency"** | **Direct Connect** | 1/10/100 Gbps, private |
| **"Quick setup" / "encrypted"** | **Site-to-Site VPN** | Over internet, IPsec |
| **"Remote users" / "VPN access"** | **Client VPN** | Managed VPN for users |
| **"Multiple VPNs" / "scale VPN"** | **VPN CloudHub** | Multiple sites via VPN |
| **"Direct Connect + VPN"** | **DX + VPN** | Encrypted over DX |
| **"Extend on-prem to AWS"** | **Storage Gateway** / **Outposts** | Hybrid storage/compute |

**Quick Decision:**
- Need immediately? → **VPN** (hours)
- Need best performance? → **Direct Connect** (weeks to provision)
- Need both security + performance? → **Direct Connect + VPN**

---

## 🔐 VPC Security Layers

| Layer | Service | Type | Key Point |
|-------|---------|------|-----------|
| **Instance Level** | Security Group | Stateful | Allow only, no deny |
| **Subnet Level** | NACL | Stateless | Allow + deny, numbered rules |
| **VPC Level** | Network Firewall | Stateful | IDS/IPS, deep packet inspection |
| **Application Level** | WAF | Layer 7 | SQL injection, XSS, rate limiting |

**Exam Pattern:**
- "Block specific IP" → **NACL** (security groups can't deny)
- "Stateless" → **NACL**
- "Return traffic automatic" → **Security Group**

---

# SECURITY - Quick Decision Heuristics

## 🔒 Identity & Access Management

### IAM Components Quick Reference

| Component | Purpose | When to Use |
|-----------|---------|-------------|
| **User** | Long-term credentials for person | Individual human access |
| **Group** | Collection of users | Manage permissions for multiple users |
| **Role** | Temporary credentials | EC2, Lambda, cross-account, federation |
| **Policy** | JSON permission document | Define what actions are allowed |
| **SCP (Service Control Policy)** | Organization-level restrictions | Limit what accounts can do |

### IAM vs Cognito vs Directory Service

| Service | Use Case | Details |
|---------|----------|---------|
| **IAM** | AWS users, services, cross-account | Internal AWS access |
| **Cognito User Pools** | App users (millions) | Sign-up/sign-in for apps |
| **Cognito Identity Pools** | Temporary AWS credentials for app users | Access AWS services from apps |
| **Directory Service** | Active Directory integration | Corporate AD users |
| **IAM Identity Center** | SSO across AWS accounts | Centralized access management |

---

## 🔐 Encryption & Secrets Management

| Requirement | Service | Why |
|-------------|---------|-----|
| **"Encryption keys" / "manage keys"** | **KMS** | Managed, automatic rotation |
| **"FIPS 140-2 Level 3" / "hardware"** | **CloudHSM** | Dedicated HSM, you control keys |
| **"Database passwords" / "auto-rotate"** | **Secrets Manager** | Automatic rotation for RDS |
| **"Configuration" / "free tier" / "simple"** | **Parameter Store** | Free tier, hierarchical, no auto-rotation |
| **"SSL/TLS certificate" / "free"** | **ACM** | Free, auto-renewal, integrates with ALB/CloudFront |

**KMS vs CloudHSM:**
- Shared hardware? → **KMS**
- Dedicated hardware? → **CloudHSM**
- AWS manages keys? → **KMS**
- You control keys entirely? → **CloudHSM**

---

## 🛡️ Threat Detection & Protection

### Security Services Matrix

| Service | What It Does | When to Choose |
|---------|--------------|----------------|
| **GuardDuty** | ML-based threat detection | "Detect threats" / "malicious activity" |
| **Inspector** | Vulnerability scanning | "Scan EC2/Lambda/containers" / "CVE" |
| **Macie** | Discover sensitive data in S3 | "PII detection" / "data classification" |
| **Detective** | Investigate security findings | "Root cause analysis" / "visualize" |
| **Security Hub** | Centralized security dashboard | "Aggregate findings" / "compliance" |
| **Config** | Track resource configuration | "Configuration changes" / "compliance rules" |
| **CloudTrail** | API call logging | "Who did what when" / "audit trail" |

### WAF & DDoS Protection

| Service | Protection Type | Cost | Use Case |
|---------|----------------|------|----------|
| **WAF** | Layer 7 (application) | Pay per rule | SQL injection, XSS, rate limiting |
| **Shield Standard** | Layer 3/4 DDoS | Free | Automatic DDoS protection |
| **Shield Advanced** | Enhanced DDoS + support | $3,000/month | 24/7 DRT, cost protection |
| **Network Firewall** | Network layer | Pay per hour/GB | IDS/IPS for VPC |

**Quick Decision:**
- Block SQL injection? → **WAF**
- DDoS protection (basic)? → **Shield Standard** (automatic)
- Enterprise DDoS + support? → **Shield Advanced**
- Network-level filtering? → **Network Firewall**

---

# MONITORING - Quick Decision Heuristics

## 📊 Monitoring Services Selection

| Requirement | Service | Purpose |
|-------------|---------|---------|
| **"Metrics" / "CPU" / "memory" / "disk"** | **CloudWatch Metrics** | Collect metrics |
| **"Logs" / "application logs" / "centralize"** | **CloudWatch Logs** | Log aggregation |
| **"Alert" / "notify" / "threshold"** | **CloudWatch Alarms** | Metric-based alerts |
| **"Event-driven" / "cron" / "schedule"** | **EventBridge** | Event routing, cron |
| **"API calls" / "who did what" / "audit"** | **CloudTrail** | API audit logs |
| **"Configuration changes" / "compliance"** | **Config** | Resource configuration tracking |
| **"Distributed tracing" / "request flow"** | **X-Ray** | Trace requests across services |
| **"APM" / "application performance"** | **CloudWatch Application Insights** | Auto-discover app issues |
| **"Service health" / "AWS outages"** | **Personal Health Dashboard** | AWS service status |

---

## 🏗️ Infrastructure as Code

| Requirement | Service | Details |
|-------------|---------|---------|
| **"Infrastructure as Code" / "JSON/YAML"** | **CloudFormation** | Native IaC, templates |
| **"Programming language" / "TypeScript/Python"** | **CDK** | Code-based, generates CloudFormation |
| **"Serverless apps" / "Lambda + API Gateway"** | **SAM** | Extends CloudFormation for serverless |
| **"Change sets" / "preview changes"** | **CloudFormation Change Sets** | Preview before apply |
| **"Drift detection" / "manual changes"** | **CloudFormation Drift Detection** | Detect out-of-band changes |

---

## 💰 Cost Management

| Requirement | Tool | Purpose |
|-------------|------|---------|
| **"Analyze costs" / "breakdown" / "visualize"** | **Cost Explorer** | Cost analysis and forecasting |
| **"Alert on cost" / "budget threshold"** | **Budgets** | Set budget alerts |
| **"Cost allocation" / "tag-based"** | **Cost Allocation Tags** | Track costs by tags |
| **"Recommendations" / "savings"** | **Trusted Advisor** | Best practice checks |
| **"Reserved Instance planning"** | **Cost Explorer RI Recommendations** | RI purchase recommendations |
| **"Consolidated billing" / "multiple accounts"** | **Organizations** | Single payer account |

---

# INTEGRATION - Quick Decision Heuristics

## 🔄 Application Integration Services

### Messaging Services Quick Selection

| Requirement | Service | Pattern | Details |
|-------------|---------|---------|---------|
| **"Queue" / "decouple" / "async"** | **SQS** | Queue | Message queue, pull-based |
| **"Pub/Sub" / "fan-out" / "multiple subscribers"** | **SNS** | Pub/Sub | Push-based, topic subscriptions |
| **"Event bus" / "multiple sources" / "rules"** | **EventBridge** | Event Bus | Route events, 100+ AWS sources |
| **"Workflow" / "orchestration" / "state machine"** | **Step Functions** | Orchestration | Coordinate multiple services |
| **"JMS/ActiveMQ/RabbitMQ" / "lift-and-shift"** | **Amazon MQ** | Managed Broker | For legacy apps needing JMS |

### SQS vs SNS vs EventBridge

| Feature | SQS | SNS | EventBridge |
|---------|-----|-----|-------------|
| **Pattern** | Queue (pull) | Pub/Sub (push) | Event bus (route) |
| **Consumers** | Single (with duplication) | Multiple | Multiple targets |
| **Delivery** | At-least-once | At-least-once | At-least-once |
| **Use when** | "Decouple" / "queue" | "Fan-out" / "multiple" | "Event-driven" / "AWS services" |

---

## 📊 Streaming & Real-Time

| Requirement | Service | Purpose |
|-------------|---------|---------|
| **"Real-time" / "streaming data" / "custom processing"** | **Kinesis Data Streams** | Real-time stream processing |
| **"Load to S3/Redshift/Elasticsearch" / "no code"** | **Kinesis Data Firehose** | Load to destinations, zero admin |
| **"Real-time SQL" / "streaming analytics"** | **Kinesis Data Analytics** | SQL on streaming data |
| **"Video streaming"** | **Kinesis Video Streams** | Video ingestion/processing |
| **"Kafka" / "MSK" / "high throughput"** | **MSK (Managed Kafka)** | Managed Apache Kafka |

**Kinesis Data Streams vs Firehose:**
- Need custom processing? → **Data Streams**
- Just load to S3/Redshift? → **Firehose** (simpler)

---

# ANALYTICS - Quick Decision Heuristics

## 📈 Analytics Services Matrix

| Use Case | Service | Why |
|----------|---------|-----|
| **"Query S3 with SQL" / "serverless"** | **Athena** | Serverless, pay per query |
| **"Data warehouse" / "petabyte" / "OLAP"** | **Redshift** | Columnar, MPP |
| **"ETL" / "transform data" / "serverless"** | **Glue** | Serverless ETL, Data Catalog |
| **"Big data" / "Hadoop" / "Spark"** | **EMR** | Managed Hadoop/Spark |
| **"Visualize" / "BI" / "dashboards"** | **QuickSight** | Serverless BI, ML insights |
| **"Data pipeline" / "workflow"** | **Data Pipeline** | Orchestrate data movement |
| **"Search" / "full-text" / "logs"** | **OpenSearch** | Managed Elasticsearch |
| **"Machine learning" / "training"** | **SageMaker** | Build, train, deploy ML models |

---

# MIGRATION - Quick Decision Heuristics

## 🚀 Migration Services Selection

### Server Migration

| Scenario | Service | Details |
|----------|---------|---------|
| **"Lift-and-shift" / "rehost servers"** | **Application Migration Service (MGN)** | Automated server migration |
| **"VM migration" / "VMware"** | **Application Migration Service** | Supports VMware, Hyper-V |
| **"Discover on-prem inventory"** | **Application Discovery Service** | Discover servers, dependencies |
| **"Migration planning" / "tracking"** | **Migration Hub** | Centralized migration tracking |

### Database Migration

| Scenario | Service | Details |
|----------|---------|---------|
| **"Migrate database" / "continuous replication"** | **DMS (Database Migration Service)** | Homogeneous or heterogeneous |
| **"Same engine" (e.g., MySQL → MySQL)** | **DMS** (simpler) | Straightforward migration |
| **"Different engine" (e.g., Oracle → Aurora)** | **DMS + SCT** | Schema Conversion Tool needed |
| **"Schema conversion"** | **Schema Conversion Tool (SCT)** | Convert schema for DMS |

### Data Transfer

| Data Size | Time Constraint | Service |
|-----------|----------------|---------|
| **< 10 TB** | Days OK | **DataSync** |
| **10-80 TB** | Limited bandwidth | **Snowcone** (8 TB) or **Snowball** (80 TB) |
| **> 80 TB** | Large-scale | **Snowball Edge** (multiple) |
| **> 100 PB** | Massive | **Snowmobile** (100 PB per truck) |

---

## 🏗️ Migration Strategies (6 Rs)

| Strategy | Description | When to Use |
|----------|-------------|-------------|
| **Rehost** | Lift-and-shift, no changes | "Quick migration" / "no time for changes" |
| **Replatform** | Lift-tinker-shift, minor optimizations | "Some cloud benefits" / "minimal changes" |
| **Refactor** | Re-architect for cloud-native | "Maximize cloud benefits" / "long-term" |
| **Repurchase** | Move to SaaS | "Replace with SaaS" / "no custom needed" |
| **Retire** | Decommission | "Not needed" / "redundant" |
| **Retain** | Keep on-premises | "Can't migrate yet" / "compliance" |

---

# DOMAIN-SPECIFIC PATTERNS

## 🎨 DOMAIN 1: DESIGN SECURE ARCHITECTURES (30%)

### Security Pattern Checklist

| Requirement | Solution | Service/Feature |
|-------------|----------|-----------------|
| **Encryption at rest** | Enable encryption | KMS, S3 default encryption |
| **Encryption in transit** | Use HTTPS/TLS | ALB, CloudFront, ACM |
| **Principle of least privilege** | Minimal permissions | IAM policies, SCPs |
| **Defense in depth** | Multiple security layers | SG + NACL + WAF |
| **Secure credentials** | No hardcoding | Secrets Manager, Parameter Store |
| **MFA for sensitive operations** | Require MFA | IAM MFA, MFA Delete |
| **Network isolation** | Private subnets | VPC with private subnets |
| **Audit and logging** | Enable logging | CloudTrail, VPC Flow Logs |
| **Threat detection** | Automated detection | GuardDuty |
| **DDoS protection** | Anti-DDoS | Shield, WAF |

---

## 🏗️ DOMAIN 2: DESIGN RESILIENT ARCHITECTURES (26%)

### High Availability Patterns

| Requirement | Pattern | Implementation |
|-------------|---------|----------------|
| **Eliminate single point of failure** | Multi-AZ | Deploy across multiple AZs |
| **Automatic failover** | Multi-AZ RDS/Aurora | Automatic database failover |
| **Load distribution** | Load balancer | ALB/NLB across AZs |
| **Auto-scaling** | Elastic capacity | Auto Scaling Groups |
| **Loose coupling** | Decouple components | SQS, SNS, EventBridge |
| **Stateless applications** | Externalize state | DynamoDB, ElastiCache for sessions |
| **Health checks** | Monitor health | ELB health checks, Route 53 |
| **Graceful degradation** | Circuit breaker | API Gateway throttling |

### Disaster Recovery Tiers

| Strategy | RTO | RPO | Cost | Complexity |
|----------|-----|-----|------|------------|
| **Backup & Restore** | Hours | Hours | Lowest | Lowest |
| **Pilot Light** | Minutes-Hours | Minutes | Low | Low |
| **Warm Standby** | Minutes | Seconds-Minutes | Medium | Medium |
| **Multi-Site Active/Active** | Real-time | Real-time | Highest | Highest |

**Quick Selection:**
- "Lowest cost" → **Backup & Restore**
- "Minutes RTO" → **Pilot Light** or **Warm Standby**
- "Zero downtime" → **Multi-Site Active/Active**

---

## 🚀 DOMAIN 3: DESIGN HIGH-PERFORMING ARCHITECTURES (24%)

### Performance Patterns

| Requirement | Solution | Service |
|-------------|----------|---------|
| **Global low latency** | Edge locations | CloudFront |
| **Database read scaling** | Read replicas | RDS Read Replicas, Aurora Replicas |
| **Database write scaling** | Sharding | DynamoDB auto-sharding |
| **Caching** | Multi-tier caching | CloudFront + ElastiCache + DAX |
| **Fast storage** | SSD, in-memory | io2, ElastiCache, DAX |
| **Parallel processing** | Distributed | Lambda concurrent invocations, EMR |
| **Compute optimization** | Right-sizing | Appropriate instance types |
| **Network optimization** | Low-latency networking | Placement groups, Enhanced Networking |

### Caching Hierarchy

```
User → CloudFront (edge cache)
     → ALB
     → EC2 (application cache)
     → ElastiCache/DAX (database cache)
     → Database
```

**Cache at every layer for maximum performance.**

---

## 💰 DOMAIN 4: DESIGN COST-OPTIMIZED ARCHITECTURES (20%)

### Cost Optimization Patterns

| Strategy | Implementation | Savings |
|----------|----------------|---------|
| **Right-sizing** | Choose appropriate instance types | 20-40% |
| **Reserved capacity** | RIs, Savings Plans | Up to 72% |
| **Spot instances** | Use for fault-tolerant workloads | Up to 90% |
| **Auto-scaling** | Scale down when not needed | 30-50% |
| **Storage tiers** | S3 Lifecycle, Intelligent-Tiering | 50-95% |
| **Serverless** | Lambda, Fargate, Aurora Serverless | Pay per use |
| **Data transfer** | Reduce cross-region/internet | Varies |
| **CloudFront** | Cache to reduce origin requests | Varies |
| **Scheduled scaling** | Turn off dev/test environments | 40-60% |

### Storage Cost Optimization

| Data Access Pattern | Optimal Storage Class | Savings |
|---------------------|----------------------|---------|
| **Frequent access** | S3 Standard | Baseline |
| **Unknown pattern** | Intelligent-Tiering | Auto-optimized |
| **Infrequent access** | Standard-IA | ~50% |
| **Archive, instant access** | Glacier Instant | ~70% |
| **Archive, hours OK** | Glacier Flexible | ~80% |
| **Long-term archive** | Glacier Deep Archive | ~95% |

---

## 🔥 CRITICAL EXAM PATTERNS - MEMORIZE THESE

### Pattern: "Most Cost-Effective"

1. Check if serverless works (Lambda, Fargate, Aurora Serverless)
2. Consider Spot instances for fault-tolerant
3. Use appropriate storage class (S3 Glacier, Standard-IA)
4. Implement caching (CloudFront, ElastiCache)
5. Right-size instances
6. Use Reserved Instances for steady-state

### Pattern: "Highest Performance"

1. Use SSD (io2, io2 Block Express)
2. Use caching (ElastiCache, DAX, CloudFront)
3. Use read replicas (RDS, Aurora)
4. Use enhanced networking
5. Use placement groups (cluster for HPC)
6. Use compute-optimized instances (C-family)

### Pattern: "Most Secure"

1. Encryption at rest + in transit
2. Least privilege IAM policies
3. Private subnets with NAT Gateway
4. Security Groups + NACLs
5. Enable MFA
6. Enable CloudTrail + GuardDuty
7. Use Secrets Manager (not hardcoding)

### Pattern: "Highly Available"

1. Multi-AZ deployment
2. Multi-region (if required)
3. Auto Scaling Groups
4. Load Balancer (ALB/NLB)
5. Loose coupling (SQS, SNS)
6. Health checks (ELB, Route 53)
7. Automated backups

### Pattern: "Serverless"

```
Frontend: S3 + CloudFront
API: API Gateway + Lambda
Database: DynamoDB
Auth: Cognito
Storage: S3
Monitoring: CloudWatch
```

---

## 🎯 EXAM-SPECIFIC HEURISTICS

### Keyword → Service Mapping (Instant Recognition)

| Keyword in Question | Instant Answer |
|---------------------|----------------|
| "Serverless" (compute) | Lambda |
| "Serverless" (database) | DynamoDB, Aurora Serverless |
| "5x performance" + MySQL/PostgreSQL | Aurora |
| "Petabyte" / "data warehouse" | Redshift |
| "NoSQL" + "serverless" | DynamoDB |
| "Cache" + "DynamoDB" | DAX |
| "Cache" + "database" | ElastiCache |
| "Queue" / "decouple" | SQS |
| "Pub/Sub" / "fan-out" | SNS |
| "Real-time streaming" | Kinesis |
| "SQL on S3" | Athena |
| "ETL" | Glue |
| "BI" / "visualize" | QuickSight |
| "Disaster Recovery" | Multi-region, backups |
| "Multi-AZ" | High Availability |
| "Read Replica" | Read scaling |
| "CDN" | CloudFront |
| "DNS" | Route 53 |
| "Hybrid" | Direct Connect, Storage Gateway |
| "Lift-and-shift" | MGN (Application Migration Service) |
| "Migrate database" | DMS |
| "Containers" + "Kubernetes" | EKS |
| "Containers" + "simple" | ECS |
| "Containers" + "serverless" | Fargate |
| "Batch processing" | AWS Batch |
| "Machine learning" | SageMaker |
| "IoT" | IoT Core, Timestream |
| "Edge computing" | Lambda@Edge, CloudFront Functions |
| "Secrets" + "rotation" | Secrets Manager |
| "Configuration" + "free" | Parameter Store |
| "Encryption keys" | KMS |
| "Threat detection" | GuardDuty |
| "Vulnerability scanning" | Inspector |
| "DDoS protection" | Shield, WAF |
| "API calls audit" | CloudTrail |
| "Resource configuration" | Config |
| "Metrics" | CloudWatch |
| "IaC" | CloudFormation |
| "Multi-account" | Organizations |

---

## 🚨 COMMON EXAM TRAPS - AVOID THESE

### Trap 1: "Most Cost-Effective" ≠ Cheapest Service

❌ **Wrong:** Choose cheapest AWS service  
✅ **Right:** Consider total cost (data transfer, requests, storage, compute)

Example: Lambda might cost more than EC2 for sustained high traffic

---

### Trap 2: Multi-AZ ≠ Multi-Region

- **Multi-AZ:** High availability (same region, automatic failover)
- **Multi-Region:** Disaster recovery (different regions, global reach)

---

### Trap 3: Read Replicas ≠ Multi-AZ Standby

- **Read Replicas:** Read scaling, async replication, manual promotion
- **Multi-AZ:** HA, sync replication, automatic failover

---

### Trap 4: Security Group ≠ NACL

- **Security Group:** Stateful, instance-level, allow only
- **NACL:** Stateless, subnet-level, allow + deny

---

### Trap 5: S3 ≠ EFS ≠ EBS

- **S3:** Object storage, unlimited, web access
- **EFS:** File system, shared across EC2, Linux
- **EBS:** Block storage, attached to single EC2

---

### Trap 6: CloudFront ≠ Global Accelerator

- **CloudFront:** HTTP/HTTPS, caching, Layer 7
- **Global Accelerator:** TCP/UDP, no caching, Layer 4

---

### Trap 7: IAM Role ≠ IAM User

- **Role:** Temporary credentials, no password, assumable
- **User:** Long-term credentials, password/access key

---

### Trap 8: Provisioned IOPS ≠ General Purpose SSD

- **io2/io2 Block Express:** Need > 16,000 IOPS, predictable performance
- **gp3:** General purpose, cost-effective, up to 16,000 IOPS

---

### Trap 9: Lambda ≠ EC2 for Long-Running

- **Lambda:** Max 15 minutes
- **EC2:** Unlimited duration

---

### Trap 10: NAT Gateway ≠ Internet Gateway

- **NAT Gateway:** Private subnet outbound internet access only
- **Internet Gateway:** Bidirectional internet access for public subnet

---

## 📋 FINAL EXAM CHECKLIST - MEMORIZE THESE LIMITS

### Critical Service Limits

| Service | Limit | What It Means |
|---------|-------|---------------|
| **Lambda** | 15 min max, 10 GB memory | Can't use for > 15 min jobs |
| **S3** | Unlimited storage, 5 TB max object | No limit on total storage |
| **SQS** | 14 days max retention | Messages deleted after 14 days |
| **DynamoDB** | 400 KB max item size | Large items need S3 |
| **API Gateway** | 29 sec timeout | Long processes need async |
| **ALB** | 60 sec idle timeout (default) | Long connections need adjustment |
| **EC2** | 5 VPC per region (soft limit) | Can request increase |
| **RDS** | 40 database instances per account | Soft limit |
| **EBS** | 64 TB max volume size | Use multiple volumes if needed |

---

## 🎓 STUDY STRATEGY FOR THIS FILE

### Week Before Exam:
1. Read through entire file 3 times
2. Create flashcards for keyword mappings
3. Practice with sample questions
4. Review "Common Exam Traps" section

### Day Before Exam:
1. Review "Critical Exam Patterns" section
2. Review "Keyword → Service Mapping" table
3. Memorize service limits
4. Quick scan of all heuristic tables

### 1 Hour Before Exam:
1. Review "Common Exam Traps" one more time
2. Review domain-specific patterns
3. Quick mental rehearsal of decision trees

---

## 🏆 HOW TO USE THIS FILE DURING PRACTICE EXAMS

1. **Read the question carefully**
2. **Identify keywords** (serverless, multi-AZ, petabyte, etc.)
3. **Check keyword mapping table** for instant answer
4. **Verify with relevant heuristic table**
5. **Check for exam traps**
6. **Select answer**

### Example Question Workflow:

**Question:** "A company needs to store infrequently accessed data that must be retrieved instantly when needed. What is the most cost-effective solution?"

**Your Thought Process:**
1. Keywords: "infrequently accessed" + "instant retrieval" + "cost-effective"
2. Check S3 storage class heuristics
3. Find: "Infrequent + instant retrieval" → **S3 Standard-IA** or **S3 Glacier Instant Retrieval**
4. Between the two, Glacier Instant is cheaper
5. Answer: **S3 Glacier Instant Retrieval**

Time taken: **10-15 seconds**

---

## 🎯 FINAL QUICK TIPS

1. **If in doubt, choose serverless** (Lambda, DynamoDB, Aurora Serverless, Fargate)
2. **Security questions:** Look for least privilege + encryption + MFA
3. **Cost questions:** Look for Reserved/Spot + appropriate storage class + caching
4. **Performance questions:** Look for caching + read replicas + right instance type
5. **HA questions:** Look for Multi-AZ + load balancer + Auto Scaling
6. **DR questions:** Check RTO/RPO requirements → match to DR tier
7. **When "serverless" is mentioned:** Always consider Lambda, DynamoDB, Aurora Serverless first
8. **When "most cost-effective":** Consider spot instances, lifecycle policies, reserved capacity
9. **When "highest performance":** Consider caching at every layer
10. **When confused between two services:** Ask "What's the core requirement?" and match to primary use case

---

## ✅ CONFIDENCE CHECKLIST

Before the exam, ensure you can answer these instantly:

- [ ] I can choose between EC2, Lambda, and containers in 5 seconds
- [ ] I can select the right EC2 instance type based on keywords
- [ ] I can choose the correct S3 storage class for any scenario
- [ ] I know when to use RDS vs DynamoDB
- [ ] I understand Multi-AZ vs Read Replica vs Multi-Region
- [ ] I can differentiate between Security Group and NACL
- [ ] I know when to use ALB vs NLB
- [ ] I understand all Route 53 routing policies
- [ ] I can select between CloudFront and Global Accelerator
- [ ] I know all DR strategies and their RTO/RPO
- [ ] I can identify the right caching strategy
- [ ] I understand IAM roles vs users vs policies
- [ ] I know when to use KMS vs Secrets Manager vs Parameter Store
- [ ] I can choose between SQS, SNS, and EventBridge
- [ ] I understand all security services (GuardDuty, Inspector, Macie, etc.)

---

## 🎯 ONE-PAGE SUMMARY FOR EXAM DAY

### Top 20 Service Decisions (Covers 80% of Questions)

1. **Serverless compute** → Lambda
2. **Serverless database** → DynamoDB
3. **Relational + 5x performance** → Aurora
4. **Object storage** → S3
5. **Block storage** → EBS
6. **Shared file system (Linux)** → EFS
7. **Cache database** → ElastiCache Redis
8. **Cache DynamoDB** → DAX
9. **Queue/decouple** → SQS
10. **Pub/Sub** → SNS
11. **Web load balancer** → ALB
12. **High-performance load balancer** → NLB
13. **CDN** → CloudFront
14. **DNS** → Route 53
15. **Data warehouse** → Redshift
16. **SQL on S3** → Athena
17. **Streaming** → Kinesis
18. **Container orchestration** → ECS or EKS
19. **Serverless containers** → Fargate
20. **Multi-AZ HA** → Multi-AZ RDS/Aurora + ALB + Auto Scaling

**Master these 20, and you'll answer most questions correctly!**

---

**Good luck on your AWS SAA-C03 exam! 🚀**

*Remember: AWS favors well-architected, resilient, secure, performant, and cost-optimized solutions. When in doubt, choose the answer that best embodies these principles.*
