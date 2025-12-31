# Service Selection Matrix - AWS Solutions Architect Associate
## Quick Reference Guide for Common Scenarios

---

## 📋 Table of Contents
1. [Compute Selection Matrix](#compute-selection-matrix)
2. [Storage Selection Matrix](#storage-selection-matrix)
3. [Database Selection Matrix](#database-selection-matrix)
4. [Networking Selection Matrix](#networking-selection-matrix)
5. [Security Selection Matrix](#security-selection-matrix)
6. [Integration Selection Matrix](#integration-selection-matrix)
7. [Analytics Selection Matrix](#analytics-selection-matrix)
8. [Migration Selection Matrix](#migration-selection-matrix)

---

## 🖥️ Compute Selection Matrix

| **Scenario** | **Best Service** | **Why?** | **Alternatives** |
|--------------|------------------|----------|------------------|
| Traditional web application | **EC2** | Full control, established workload | Lightsail (simpler) |
| Microservices architecture | **ECS/EKS** | Container orchestration | EC2 + Docker |
| Serverless event processing | **Lambda** | No server management, pay per use | Fargate |
| Batch processing jobs | **AWS Batch** | Automatic scaling, cost-optimized | EC2 Spot |
| High-performance computing | **EC2 (HPC instances)** | GPU/FPGA support | Batch |
| Simple website/blog | **Lightsail** | Pre-configured, fixed pricing | EC2 |
| Container without orchestration | **Fargate** | Serverless containers | ECS on EC2 |
| Kubernetes workloads | **EKS** | Managed K8s service | Self-managed K8s |
| Short-lived tasks (<15 min) | **Lambda** | Event-driven, auto-scaling | Fargate |
| Long-running containers | **ECS/Fargate** | Persistent containers | EC2 |
| Machine learning inference | **SageMaker** | Built-in ML tools | Lambda/EC2 |
| Desktop applications (VDI) | **WorkSpaces** | Virtual desktops | EC2 Windows |

### Quick Decision Factors:
- **Need OS access?** → EC2
- **Need containers?** → ECS/EKS/Fargate
- **Event-driven/short tasks?** → Lambda
- **Kubernetes required?** → EKS
- **Simplicity first?** → Lightsail

---

## 💾 Storage Selection Matrix

| **Scenario** | **Best Service** | **Why?** | **Alternatives** |
|--------------|------------------|----------|------------------|
| Object storage (files, media) | **S3** | Durability, scalability, cost-effective | EFS (if file system needed) |
| Block storage for EC2 | **EBS** | Low-latency, persistent | Instance Store (ephemeral) |
| Shared file system (Linux) | **EFS** | NFS, multi-AZ access | FSx for Lustre |
| Shared file system (Windows) | **FSx for Windows** | SMB protocol, AD integration | EFS won't work |
| High-performance computing | **FSx for Lustre** | Sub-millisecond latency | EFS |
| Archive/long-term backup | **S3 Glacier** | Lowest cost for archival | S3 Standard-IA |
| Content delivery | **S3 + CloudFront** | Global distribution | S3 alone (slower) |
| Database backups | **S3** | Cost-effective, durable | EBS snapshots |
| Temporary/cache data | **Instance Store** | Highest IOPS, ephemeral | EBS io2 |
| Mission-critical databases | **EBS io2** | Highest durability (99.999%) | EBS gp3 |
| General-purpose workloads | **EBS gp3** | Balance of price/performance | EBS gp2 |
| Big data analytics | **S3** | Integrates with EMR, Athena | HDFS on EC2 |
| Disaster recovery | **S3 + Cross-Region Replication** | Automatic replication | Manual backups |
| Static website hosting | **S3** | Simple, cost-effective | EC2 + web server |
| Infrequent access data | **S3 Standard-IA** | Lower cost, quick access | S3 Glacier |

### Storage Class Decision Tree:
```
Data Access Pattern?
├─ Frequent access → S3 Standard
├─ Infrequent access (>30 days) → S3 Standard-IA
├─ Rare access (>90 days) → S3 Glacier Flexible
├─ Archive (>180 days) → S3 Glacier Deep Archive
└─ Unknown pattern → S3 Intelligent-Tiering
```

---

## 🗄️ Database Selection Matrix

| **Scenario** | **Best Service** | **Why?** | **Alternatives** |
|--------------|------------------|----------|------------------|
| Relational database (general) | **RDS** | Managed, multi-engine support | Aurora (better) |
| High-performance relational | **Aurora** | 5x faster than MySQL | RDS |
| NoSQL key-value store | **DynamoDB** | Fully managed, millisecond latency | DocumentDB |
| Document database (MongoDB) | **DocumentDB** | MongoDB-compatible | DynamoDB |
| In-memory cache | **ElastiCache** | Redis/Memcached | DynamoDB DAX |
| Data warehouse | **Redshift** | Petabyte-scale analytics | Athena (serverless) |
| Graph database | **Neptune** | Relationships/graphs | Neo4j on EC2 |
| Time-series data | **Timestream** | Purpose-built for time-series | DynamoDB |
| Ledger database | **QLDB** | Immutable, cryptographic | DynamoDB |
| Global database | **Aurora Global** | Cross-region replication | DynamoDB Global Tables |
| Serverless SQL queries | **Athena** | Query S3 data directly | Redshift Serverless |
| Real-time analytics | **DynamoDB Streams** | Change data capture | Kinesis |
| Migration from Oracle/SQL | **RDS** | Lift-and-shift | Aurora (better performance) |
| Gaming leaderboards | **DynamoDB** | Low latency, high throughput | ElastiCache |
| Session management | **DynamoDB/ElastiCache** | Fast read/write | RDS (overkill) |

### Database Type Decision:
```
Data Structure?
├─ Structured + Complex queries → RDS/Aurora
├─ Structured + Simple queries → DynamoDB
├─ Semi-structured → DocumentDB
├─ Key-value pairs → DynamoDB
├─ In-memory/cache → ElastiCache
├─ Analytics → Redshift
└─ Graphs/relationships → Neptune
```

---

## 🌐 Networking Selection Matrix

| **Scenario** | **Best Service** | **Why?** | **Alternatives** |
|--------------|------------------|----------|------------------|
| Content delivery (CDN) | **CloudFront** | Global edge locations | S3 Transfer Acceleration |
| Load balancing (HTTP/HTTPS) | **ALB** | Layer 7, path-based routing | NLB |
| Load balancing (TCP/UDP) | **NLB** | Layer 4, ultra-low latency | ALB |
| Load balancing (EC2 Classic) | **CLB** | Legacy support | ALB/NLB |
| Private connectivity to AWS | **Direct Connect** | Dedicated connection | VPN |
| Site-to-site VPN | **VPN Gateway** | Encrypted over internet | Direct Connect |
| Connect VPCs | **VPC Peering** | Direct connection | Transit Gateway |
| Hub-and-spoke network | **Transit Gateway** | Central routing | Multiple peerings |
| DNS management | **Route 53** | Managed DNS, health checks | Third-party DNS |
| API management | **API Gateway** | RESTful APIs, throttling | ALB |
| Private API access | **PrivateLink** | Secure, private connectivity | VPC Peering |
| Global traffic management | **Route 53** | Geolocation, latency-based | CloudFront |
| DDoS protection | **Shield + WAF** | Automatic + custom rules | Security groups only |
| Web application firewall | **WAF** | SQL injection, XSS protection | Network ACLs |
| Accelerate global apps | **Global Accelerator** | Static IPs, health checks | CloudFront |

### Load Balancer Selection:
- **HTTP/HTTPS + path routing?** → ALB
- **TCP/UDP + extreme performance?** → NLB  
- **Extreme low latency + static IP?** → NLB
- **WebSockets?** → ALB or NLB
- **gRPC?** → ALB

---

## 🔒 Security Selection Matrix

| **Scenario** | **Best Service** | **Why?** | **Alternatives** |
|--------------|------------------|----------|------------------|
| Manage encryption keys | **KMS** | Integrated, automated | CloudHSM (FIPS) |
| FIPS 140-2 Level 3 compliance | **CloudHSM** | Dedicated hardware | KMS (Level 2) |
| Secrets management | **Secrets Manager** | Automatic rotation | Parameter Store |
| Configuration parameters | **Systems Manager Parameter Store** | Free tier, simple | Secrets Manager |
| User authentication | **Cognito** | Managed user pools | Custom on EC2 |
| Federation (SAML) | **IAM Identity Center** | SSO, enterprise | Cognito |
| API authentication | **Cognito/API Gateway** | Built-in integration | Custom Lambda |
| Certificate management | **ACM** | Free SSL/TLS certs | Third-party CA |
| DDoS protection (basic) | **Shield Standard** | Always-on, free | None |
| DDoS protection (advanced) | **Shield Advanced** | 24/7 support, cost protection | WAF only |
| Web app security | **WAF** | Custom rules, SQL injection | Security groups |
| Network traffic filtering | **Security Groups** | Stateful firewall | NACLs |
| Subnet-level filtering | **NACLs** | Stateless firewall | Security Groups |
| Compliance auditing | **Config** | Resource compliance | CloudTrail |
| Security findings | **Security Hub** | Centralized security | GuardDuty alone |
| Threat detection | **GuardDuty** | ML-based anomalies | CloudWatch Logs |
| Data inspection | **Macie** | PII/sensitive data discovery | Manual scripts |

### Encryption Key Decision:
- **AWS managed keys?** → KMS (AWS managed)
- **Customer managed keys?** → KMS (Customer managed)
- **Need FIPS 140-2 Level 3?** → CloudHSM
- **Need single-tenant HSM?** → CloudHSM

---

## 🔗 Integration Selection Matrix

| **Scenario** | **Best Service** | **Why?** | **Alternatives** |
|--------------|------------------|----------|------------------|
| Message queue | **SQS** | Fully managed, reliable | SNS (pub/sub) |
| Pub/Sub messaging | **SNS** | Fan-out to multiple subscribers | EventBridge |
| Event-driven workflows | **EventBridge** | Event routing, scheduling | SNS |
| Workflow orchestration | **Step Functions** | Visual workflows, state machine | SWF |
| Application integration | **AppFlow** | No-code SaaS integration | Custom Lambda |
| API management | **API Gateway** | Throttling, caching | ALB |
| Real-time data streaming | **Kinesis Data Streams** | Real-time processing | SQS (not real-time) |
| ETL processing | **Kinesis Data Firehose** | Load to S3/Redshift | Lambda + SQS |
| Service mesh | **App Mesh** | Microservices communication | Manual config |
| Decoupling apps | **SQS** | Asynchronous processing | SNS |
| Email notifications | **SNS + SES** | Simple, scalable | Third-party |
| SMS notifications | **SNS** | Direct SMS sending | Third-party |
| Mobile push notifications | **SNS** | iOS, Android support | Third-party |

### Messaging Pattern Decision:
```
Communication Pattern?
├─ One-to-one async → SQS
├─ One-to-many → SNS
├─ Event-based → EventBridge
├─ Real-time streaming → Kinesis
└─ Workflow coordination → Step Functions
```

---

## 📊 Analytics Selection Matrix

| **Scenario** | **Best Service** | **Why?** | **Alternatives** |
|--------------|------------------|----------|------------------|
| Query S3 data | **Athena** | Serverless, SQL | Redshift Spectrum |
| Data warehouse | **Redshift** | Petabyte-scale, columnar | Athena |
| Real-time analytics | **Kinesis Analytics** | SQL on streaming data | Lambda |
| ETL jobs | **Glue** | Serverless ETL | EMR |
| Big data processing | **EMR** | Hadoop, Spark | Glue |
| Business intelligence | **QuickSight** | Serverless BI dashboards | Third-party BI |
| Log analytics | **CloudWatch Logs Insights** | Query logs | Athena |
| Data catalog | **Glue Data Catalog** | Metadata repository | Manual |
| Machine learning | **SageMaker** | End-to-end ML | EC2 + ML libs |
| Search/indexing | **OpenSearch** | Full-text search, logs | CloudWatch |
| Data lake | **S3 + Lake Formation** | Centralized, secure | S3 alone |
| Stream processing | **Kinesis** | Real-time ingestion | SQS + Lambda |

### Analytics Decision:
- **Serverless SQL?** → Athena
- **Complex analytics?** → Redshift
- **Real-time streaming?** → Kinesis
- **ETL jobs?** → Glue
- **Big data frameworks?** → EMR

---

## 🚚 Migration Selection Matrix

| **Scenario** | **Best Service** | **Why?** | **Alternatives** |
|--------------|------------------|----------|------------------|
| Large data transfer (TB-PB) | **Snowball** | Physical device | Direct Connect |
| Exabyte-scale transfer | **Snowmobile** | 100 PB per device | Multiple Snowballs |
| Database migration | **DMS** | Minimal downtime | Manual export/import |
| Server migration | **Application Migration Service** | Automated lift-and-shift | Manual setup |
| Online data transfer | **DataSync** | Automated, scheduled | AWS CLI |
| VMware migration | **VMware Cloud on AWS** | Hybrid cloud | Application Migration Service |
| Mainframe migration | **Mainframe Modernization** | Legacy systems | Rewrite |
| Application discovery | **Application Discovery Service** | Map dependencies | Manual |
| Migration planning | **Migration Hub** | Track progress | Spreadsheets |
| S3 transfer acceleration | **S3 Transfer Acceleration** | Fast uploads | Standard S3 |
| Continuous replication | **DMS** | Ongoing sync | Manual scripts |

### Data Transfer Decision:
```
Data Size?
├─ < 10 TB + Fast internet → Direct upload/DataSync
├─ 10-80 TB → Snowball Edge
├─ > 80 TB → Multiple Snowballs
└─ Exabyte scale → Snowmobile
```

---

## 🎯 Common Exam Scenarios - Quick Answers

### Scenario: "Most cost-effective"
- **Storage**: S3 Glacier Deep Archive
- **Compute**: Lambda or Spot Instances
- **Database**: DynamoDB On-Demand or Aurora Serverless

### Scenario: "Lowest latency"
- **Storage**: EBS io2 or Instance Store
- **Cache**: ElastiCache Redis
- **CDN**: CloudFront
- **Load Balancer**: NLB

### Scenario: "Highest availability"
- **Compute**: Multi-AZ Auto Scaling
- **Database**: Aurora Multi-AZ
- **Storage**: S3 (99.999999999% durability)

### Scenario: "Disaster recovery"
- **Backup**: S3 + Cross-Region Replication
- **Database**: Aurora Global Database
- **Compute**: AMI + ASG in another region

### Scenario: "Serverless"
- **Compute**: Lambda
- **Database**: DynamoDB or Aurora Serverless
- **API**: API Gateway
- **Analytics**: Athena

### Scenario: "Global users"
- **CDN**: CloudFront
- **DNS**: Route 53 (latency/geolocation routing)
- **Database**: DynamoDB Global Tables or Aurora Global
- **Network**: Global Accelerator

### Scenario: "Legacy application"
- **Migration**: Application Migration Service
- **Compute**: EC2
- **Database**: RDS (same engine)

### Scenario: "Microservices"
- **Compute**: ECS/EKS with Fargate
- **Service Discovery**: Cloud Map
- **Load Balancer**: ALB
- **API**: API Gateway

### Scenario: "Compliance/Audit"
- **Logging**: CloudTrail
- **Monitoring**: Config
- **Security**: Security Hub
- **Encryption**: KMS or CloudHSM

### Scenario: "Real-time processing"
- **Streaming**: Kinesis Data Streams
- **Processing**: Lambda or Kinesis Analytics
- **Storage**: DynamoDB

---

## 🔑 Key Exam Tips

### When You See These Keywords:

| **Keyword** | **Think** |
|-------------|-----------|
| "Serverless" | Lambda, DynamoDB, S3, Athena |
| "Cost-effective" | S3, Spot Instances, Lambda |
| "Low latency" | CloudFront, ElastiCache, NLB |
| "Disaster recovery" | Multi-region, backups, replication |
| "Secure" | KMS, encryption at rest/transit, VPC |
| "Scalable" | Auto Scaling, DynamoDB, S3 |
| "Durable" | S3 (11 nines), Aurora |
| "Archive" | Glacier, Glacier Deep Archive |
| "Real-time" | Kinesis, DynamoDB Streams |
| "Async" | SQS, SNS |
| "Decouple" | SQS, SNS, EventBridge |
| "Cache" | ElastiCache, CloudFront, DAX |
| "Audit" | CloudTrail, Config |
| "Monitor" | CloudWatch |

---

## ⚡ Quick Reference Formulas

### Cost Optimization:
1. **Right-size** → Use smallest instance needed
2. **Reserved Instances** → Predictable workloads (1-3 years)
3. **Spot Instances** → Flexible, fault-tolerant workloads
4. **S3 Lifecycle** → Move to cheaper storage classes
5. **Lambda** → Short, unpredictable workloads

### High Availability:
1. **Multi-AZ** → RDS, ELB, EC2 across AZs
2. **Auto Scaling** → Automatic capacity adjustment
3. **Health Checks** → ELB, Route 53
4. **CloudFront** → Global edge caching
5. **Read Replicas** → Offload read traffic

### Security:
1. **Least Privilege** → Minimal IAM permissions
2. **Encryption** → At rest (KMS) + in transit (TLS)
3. **Security Groups** → Whitelist only needed ports
4. **VPC** → Private subnets for databases
5. **MFA** → Root account and privileged users

### Performance:
1. **CloudFront** → Cache static content
2. **ElastiCache** → Cache database queries
3. **Read Replicas** → Scale read operations
4. **EBS Provisioned IOPS** → I/O intensive apps
5. **Auto Scaling** → Match capacity to demand

---

## 📝 Final Exam Strategy

### Process of Elimination:
1. ✅ **Identify keywords** in the question
2. ✅ **Eliminate wrong answers** first
3. ✅ **Look for AWS best practices**
4. ✅ **Choose managed over self-managed**
5. ✅ **Choose serverless when possible**

### Common Wrong Answer Patterns:
- ❌ "Use EC2 for everything" → Too manual
- ❌ Single AZ solutions → Not highly available
- ❌ Public S3 buckets → Security risk
- ❌ Root account for daily use → Security risk
- ❌ No encryption → Compliance issue

### Always Remember:
- 🎯 **AWS managed services** are usually the answer
- 🎯 **Serverless** when it fits the use case
- 🎯 **Multi-AZ** for high availability
- 🎯 **S3** for object storage (not EBS)
- 🎯 **CloudFront + S3** for static websites
- 🎯 **RDS** over database on EC2
- 🎯 **DynamoDB** for serverless NoSQL
- 🎯 **Lambda** for event-driven compute

---

**Last Updated**: December 2025
**Exam Version**: AWS SAA-C03

**Good luck with your exam! 🎉**
