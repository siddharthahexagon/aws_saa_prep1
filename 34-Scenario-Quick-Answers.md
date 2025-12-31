# AWS SAA-C03: 200+ Scenario-Based Quick Answers

## 🎯 Comprehensive Exam Scenarios with Instant Answers

This file provides 200+ exam-style scenarios organized by topic with immediate answers and brief explanations.

---

## 🖥️ COMPUTE SCENARIOS

### EC2 Scenarios

| # | Scenario | Answer |
|---|----------|--------|
| 1 | Need full control over OS and applications | **EC2** |
| 2 | Long-running web server workload | **EC2 with Auto Scaling** |
| 3 | Batch processing that can be interrupted | **EC2 Spot Instances** |
| 4 | Predictable 24/7 workload, reduce cost | **EC2 Reserved Instances / Savings Plans** |
| 5 | Need dedicated hardware for licensing | **Dedicated Hosts** |
| 6 | Compliance requires isolated hardware | **Dedicated Instances** |
| 7 | CPU-intensive scientific computing | **C-series instances (C5, C6i)** |
| 8 | Memory-intensive database workload | **R-series or X-series instances** |
| 9 | Machine learning training | **P-series instances (GPU)** |
| 10 | High-performance networking | **Enhanced networking / Placement groups** |
| 11 | Simple WordPress site | **Lightsail** |
| 12 | Deploy web app without managing servers | **Elastic Beanstalk** |

### Lambda Scenarios

| # | Scenario | Answer |
|---|----------|--------|
| 13 | Event-driven image processing < 5 min | **Lambda** |
| 14 | Process S3 uploads automatically | **S3 event → Lambda** |
| 15 | Run code every 5 minutes | **EventBridge rule → Lambda** |
| 16 | Need execution > 15 minutes | **Fargate or Batch (not Lambda)** |
| 17 | Eliminate Lambda cold starts | **Provisioned Concurrency** |
| 18 | Lambda needs to access RDS in VPC | **VPC Lambda + RDS Proxy** |
| 19 | Serverless REST API | **API Gateway + Lambda** |
| 20 | Process DynamoDB stream changes | **DynamoDB Streams → Lambda** |

### Container Scenarios

| # | Scenario | Answer |
|---|----------|--------|
| 21 | Run containers without managing servers | **Fargate** |
| 22 | AWS-native container orchestration | **ECS** |
| 23 | Need Kubernetes | **EKS** |
| 24 | Containerize legacy Java app | **AWS App2Container → ECS/EKS** |
| 25 | Container image vulnerability scanning | **Amazon Inspector (ECR)** |
| 26 | Store container images | **Amazon ECR** |

---

## 💾 STORAGE SCENARIOS

### S3 Scenarios

| # | Scenario | Answer |
|---|----------|--------|
| 27 | Store frequently accessed objects | **S3 Standard** |
| 28 | Infrequent access, need instant retrieval | **S3 Standard-IA** |
| 29 | Infrequent access, single AZ OK | **S3 One Zone-IA** |
| 30 | Archive data, retrieval in minutes | **S3 Glacier Instant Retrieval** |
| 31 | Archive data, retrieval in hours OK | **S3 Glacier Flexible Retrieval** |
| 32 | 7+ year compliance archive, 12+ hr retrieval OK | **S3 Glacier Deep Archive** |
| 33 | Unknown access pattern | **S3 Intelligent-Tiering** |
| 34 | Protect from accidental deletion | **S3 Versioning + MFA Delete** |
| 35 | Prevent object deletion (WORM) | **S3 Object Lock** |
| 36 | Replicate to another region for DR | **S3 Cross-Region Replication (CRR)** |
| 37 | Auto-move objects to cheaper storage | **S3 Lifecycle Policy** |
| 38 | Query S3 data with SQL | **Amazon Athena** |
| 39 | Host static website | **S3 + CloudFront** |
| 40 | Accelerate global uploads | **S3 Transfer Acceleration** |

### EBS Scenarios

| # | Scenario | Answer |
|---|----------|--------|
| 41 | General purpose boot volume | **gp3** |
| 42 | High IOPS database workload | **io2 or io2 Block Express** |
| 43 | High throughput big data workload | **st1 (Throughput Optimized HDD)** |
| 44 | Lowest cost infrequent access | **sc1 (Cold HDD)** |
| 45 | Need 256,000 IOPS | **io2 Block Express** |
| 46 | Migrate from gp2 to save cost | **gp3 (cheaper, better performance)** |
| 47 | EBS volume backup | **EBS Snapshot** |
| 48 | Automate snapshot lifecycle | **Data Lifecycle Manager (DLM)** |

### File System Scenarios

| # | Scenario | Answer |
|---|----------|--------|
| 49 | Shared file system for Linux (NFS) | **Amazon EFS** |
| 50 | Shared file system for Windows (SMB) | **FSx for Windows File Server** |
| 51 | High-performance parallel file system | **FSx for Lustre** |
| 52 | NetApp features needed | **FSx for NetApp ONTAP** |
| 53 | On-prem apps need cloud storage | **Storage Gateway** |
| 54 | NFS access to S3 | **Storage Gateway - File Gateway** |
| 55 | iSCSI block storage backed by S3 | **Storage Gateway - Volume Gateway** |
| 56 | Replace tape backup with cloud | **Storage Gateway - Tape Gateway** |

---

## 🗄️ DATABASE SCENARIOS

### RDS/Aurora Scenarios

| # | Scenario | Answer |
|---|----------|--------|
| 57 | Standard MySQL/PostgreSQL | **RDS** |
| 58 | Highest performance MySQL/PostgreSQL | **Aurora** |
| 59 | Need Oracle or SQL Server | **RDS** |
| 60 | Auto-scaling relational database | **Aurora Serverless v2** |
| 61 | Database failover < 30 seconds | **Aurora** |
| 62 | Database failover < 120 seconds | **RDS Multi-AZ** |
| 63 | Scale read workloads | **Read Replicas** |
| 64 | Cross-region DR for Aurora | **Aurora Global Database** |
| 65 | Point-in-time database recovery | **RDS Automated Backups / PITR** |
| 66 | Rewind Aurora to earlier point | **Aurora Backtrack** |

### DynamoDB Scenarios

| # | Scenario | Answer |
|---|----------|--------|
| 67 | NoSQL key-value store, millisecond latency | **DynamoDB** |
| 68 | DynamoDB with predictable workload | **Provisioned Capacity** |
| 69 | DynamoDB with unpredictable workload | **On-Demand Capacity** |
| 70 | Cache DynamoDB for microsecond latency | **DAX** |
| 71 | Multi-region active-active | **DynamoDB Global Tables** |
| 72 | Query by non-key attribute | **Global Secondary Index (GSI)** |
| 73 | Auto-delete expired items | **TTL (Time to Live)** |
| 74 | Process DynamoDB changes | **DynamoDB Streams** |

### Caching Scenarios

| # | Scenario | Answer |
|---|----------|--------|
| 75 | Cache database queries (not DynamoDB) | **ElastiCache** |
| 76 | Cache with persistence and replication | **ElastiCache for Redis** |
| 77 | Simple caching, multi-threaded | **ElastiCache for Memcached** |
| 78 | Session store for web app | **ElastiCache Redis or DynamoDB** |
| 79 | Leaderboard for gaming app | **ElastiCache for Redis (Sorted Sets)** |

### Other Database Scenarios

| # | Scenario | Answer |
|---|----------|--------|
| 80 | Data warehouse, OLAP | **Amazon Redshift** |
| 81 | Document database (MongoDB) | **Amazon DocumentDB** |
| 82 | Graph database | **Amazon Neptune** |
| 83 | Time-series data (IoT, metrics) | **Amazon Timestream** |
| 84 | Immutable ledger | **Amazon QLDB** |
| 85 | Cassandra-compatible | **Amazon Keyspaces** |

---

## 🌐 NETWORKING SCENARIOS

### Load Balancer Scenarios

| # | Scenario | Answer |
|---|----------|--------|
| 86 | HTTP/HTTPS load balancing | **ALB** |
| 87 | Path-based routing (/api, /images) | **ALB** |
| 88 | Host-based routing (api.example.com) | **ALB** |
| 89 | TCP/UDP load balancing | **NLB** |
| 90 | Ultra-low latency, millions req/sec | **NLB** |
| 91 | Need static IP for load balancer | **NLB** |
| 92 | Lambda as target | **ALB** |
| 93 | Deploy 3rd-party firewall | **Gateway Load Balancer (GWLB)** |
| 94 | WebSocket support | **ALB or NLB** |

### Route 53 Scenarios

| # | Scenario | Answer |
|---|----------|--------|
| 95 | Simple DNS for single server | **Simple Routing** |
| 96 | A/B testing, gradual rollout | **Weighted Routing** |
| 97 | Route to nearest region | **Latency-Based Routing** |
| 98 | Active-passive failover | **Failover Routing** |
| 99 | Route by user country | **Geolocation Routing** |
| 100 | Route with bias adjustment | **Geoproximity Routing** |
| 101 | Multiple healthy endpoints | **Multivalue Answer Routing** |

### VPC Scenarios

| # | Scenario | Answer |
|---|----------|--------|
| 102 | Instance-level firewall | **Security Groups** |
| 103 | Subnet-level firewall | **Network ACLs (NACLs)** |
| 104 | Block specific IP address | **NACL with deny rule** |
| 105 | Internet access for public subnet | **Internet Gateway** |
| 106 | Internet access for private subnet | **NAT Gateway** |
| 107 | Private access to S3 | **VPC Gateway Endpoint (free)** |
| 108 | Private access to other AWS services | **VPC Interface Endpoint** |
| 109 | Connect two VPCs | **VPC Peering** |
| 110 | Connect 100+ VPCs | **Transit Gateway** |
| 111 | Capture network traffic for analysis | **VPC Flow Logs** |

### Hybrid Connectivity Scenarios

| # | Scenario | Answer |
|---|----------|--------|
| 112 | Quick VPN to AWS (days) | **Site-to-Site VPN** |
| 113 | Dedicated private connection (weeks) | **AWS Direct Connect** |
| 114 | Direct Connect with encryption | **Direct Connect + VPN** |
| 115 | Connect on-prem to multiple VPCs | **Transit Gateway + VPN/DX** |

### Content Delivery Scenarios

| # | Scenario | Answer |
|---|----------|--------|
| 116 | Cache static content globally | **CloudFront** |
| 117 | Accelerate dynamic app (no cache) | **Global Accelerator** |
| 118 | Need static anycast IPs | **Global Accelerator** |
| 119 | DDoS protection for CloudFront | **Shield Standard (free)** |
| 120 | CloudFront private content | **Signed URLs or Signed Cookies** |

---

## 🔒 SECURITY SCENARIOS

### IAM Scenarios

| # | Scenario | Answer |
|---|----------|--------|
| 121 | EC2 needs to access S3 | **IAM Role (never access keys!)** |
| 122 | Lambda needs to access DynamoDB | **IAM Execution Role** |
| 123 | Cross-account access | **IAM Role with trust policy** |
| 124 | Temporary credentials | **IAM Role / STS** |
| 125 | Limit maximum permissions | **Permission Boundaries** |
| 126 | Restrict permissions across accounts | **Service Control Policies (SCPs)** |
| 127 | Federated access (SAML) | **IAM Identity Center or SAML provider** |

### Authentication Scenarios

| # | Scenario | Answer |
|---|----------|--------|
| 128 | Mobile app user authentication | **Amazon Cognito User Pools** |
| 129 | Mobile app AWS resource access | **Amazon Cognito Identity Pools** |
| 130 | Enterprise SSO across AWS accounts | **IAM Identity Center** |
| 131 | Use on-premises Active Directory | **AD Connector or Managed AD** |

### Encryption Scenarios

| # | Scenario | Answer |
|---|----------|--------|
| 132 | Simple S3 encryption | **SSE-S3** |
| 133 | S3 encryption with audit trail | **SSE-KMS** |
| 134 | Customer-provided encryption keys | **SSE-C** |
| 135 | Manage encryption keys with audit | **AWS KMS** |
| 136 | Dedicated hardware for keys (FIPS 140-2 L3) | **AWS CloudHSM** |
| 137 | Free SSL/TLS certificates | **AWS Certificate Manager (ACM)** |
| 138 | Store database credentials with rotation | **AWS Secrets Manager** |
| 139 | Store configuration values (free) | **Systems Manager Parameter Store** |

### Threat Detection Scenarios

| # | Scenario | Answer |
|---|----------|--------|
| 140 | Detect threats automatically (ML) | **Amazon GuardDuty** |
| 141 | Scan EC2 for vulnerabilities | **Amazon Inspector** |
| 142 | Find PII in S3 buckets | **Amazon Macie** |
| 143 | Audit all API calls | **AWS CloudTrail** |
| 144 | Track resource configuration changes | **AWS Config** |
| 145 | Centralized security findings | **AWS Security Hub** |
| 146 | Investigate security incidents | **Amazon Detective** |

### Network Security Scenarios

| # | Scenario | Answer |
|---|----------|--------|
| 147 | Basic DDoS protection (free) | **AWS Shield Standard** |
| 148 | Advanced DDoS + support | **AWS Shield Advanced** |
| 149 | Protect from SQL injection/XSS | **AWS WAF** |
| 150 | Managed firewall for VPC | **AWS Network Firewall** |
| 151 | Centrally manage WAF rules | **AWS Firewall Manager** |

---

## 📊 INTEGRATION & ANALYTICS SCENARIOS

### Messaging Scenarios

| # | Scenario | Answer |
|---|----------|--------|
| 152 | Decouple components | **Amazon SQS** |
| 153 | Guaranteed ordering | **SQS FIFO Queue** |
| 154 | Fanout to multiple subscribers | **SNS → Multiple SQS** |
| 155 | Event-driven routing | **Amazon EventBridge** |
| 156 | Workflow orchestration | **AWS Step Functions** |
| 157 | Migrate from RabbitMQ/ActiveMQ | **Amazon MQ** |

### Streaming Scenarios

| # | Scenario | Answer |
|---|----------|--------|
| 158 | Real-time data streaming | **Kinesis Data Streams** |
| 159 | Load streaming data to S3 | **Kinesis Data Firehose** |
| 160 | Real-time SQL on streams | **Kinesis Data Analytics** |
| 161 | Video streaming from devices | **Kinesis Video Streams** |

### Analytics Scenarios

| # | Scenario | Answer |
|---|----------|--------|
| 162 | Query S3 with SQL (serverless) | **Amazon Athena** |
| 163 | Data warehouse | **Amazon Redshift** |
| 164 | Query S3 from Redshift | **Redshift Spectrum** |
| 165 | Big data processing (Hadoop/Spark) | **Amazon EMR** |
| 166 | Serverless ETL | **AWS Glue** |
| 167 | Search and log analytics | **Amazon OpenSearch Service** |
| 168 | BI dashboards | **Amazon QuickSight** |
| 169 | Data catalog (metadata) | **AWS Glue Data Catalog** |

---

## 🚀 MIGRATION SCENARIOS

### Data Transfer Scenarios

| # | Scenario | Answer |
|---|----------|--------|
| 170 | Transfer 50 TB, limited bandwidth | **AWS Snowball Edge** |
| 171 | Transfer 5 PB | **AWS Snowmobile** |
| 172 | Transfer 8 TB in remote location | **AWS Snowcone** |
| 173 | Continuous file sync to AWS | **AWS DataSync** |
| 174 | Hybrid file access (NFS to S3) | **Storage Gateway - File Gateway** |

### Database Migration Scenarios

| # | Scenario | Answer |
|---|----------|--------|
| 175 | Migrate MySQL to RDS MySQL | **AWS DMS** |
| 176 | Migrate Oracle to Aurora PostgreSQL | **AWS SCT + DMS** |
| 177 | Minimal downtime database migration | **DMS with CDC** |

### Application Migration Scenarios

| # | Scenario | Answer |
|---|----------|--------|
| 178 | Lift-and-shift VMs to EC2 | **AWS Application Migration Service (MGN)** |
| 179 | Discover on-prem apps | **AWS Application Discovery Service** |
| 180 | Track migration progress | **AWS Migration Hub** |
| 181 | Extend VMware to AWS | **VMware Cloud on AWS** |

---

## 💰 COST & MANAGEMENT SCENARIOS

### Cost Scenarios

| # | Scenario | Answer |
|---|----------|--------|
| 182 | Analyze AWS spending | **AWS Cost Explorer** |
| 183 | Set spending alerts | **AWS Budgets** |
| 184 | Detailed cost reports | **AWS Cost and Usage Reports** |
| 185 | Right-size EC2 instances | **AWS Compute Optimizer** |
| 186 | Share Reserved Instances across accounts | **AWS Organizations Consolidated Billing** |
| 187 | Tag resources for cost allocation | **Cost Allocation Tags** |

### Management Scenarios

| # | Scenario | Answer |
|---|----------|--------|
| 188 | Monitor metrics and logs | **Amazon CloudWatch** |
| 189 | Trace requests across services | **AWS X-Ray** |
| 190 | Infrastructure as Code | **AWS CloudFormation** |
| 191 | Secure shell without SSH keys | **Systems Manager Session Manager** |
| 192 | Automate patching | **Systems Manager Patch Manager** |
| 193 | Run commands on multiple instances | **Systems Manager Run Command** |
| 194 | Automated AMI creation | **EC2 Image Builder** |
| 195 | Best practices recommendations | **AWS Trusted Advisor** |
| 196 | Architecture review | **AWS Well-Architected Tool** |
| 197 | Multi-account setup | **AWS Control Tower** |
| 198 | Self-service IT catalog | **AWS Service Catalog** |
| 199 | Personal AWS service health | **AWS Personal Health Dashboard** |
| 200 | Centralized backup management | **AWS Backup** |

---

## 🏗️ ARCHITECTURE PATTERN SCENARIOS

### High Availability Patterns

| # | Scenario | Answer |
|---|----------|--------|
| 201 | HA web application | **Multi-AZ + Auto Scaling + ALB + Route 53** |
| 202 | HA database | **RDS Multi-AZ or Aurora** |
| 203 | HA NAT Gateway | **One NAT Gateway per AZ** |
| 204 | HA caching | **ElastiCache Multi-AZ** |

### Serverless Patterns

| # | Scenario | Answer |
|---|----------|--------|
| 205 | Serverless web app | **S3 + CloudFront + API Gateway + Lambda + DynamoDB** |
| 206 | Serverless data pipeline | **S3 → Glue → S3 → Athena** |
| 207 | Serverless image processing | **S3 → Lambda → S3** |
| 208 | Serverless workflow | **API Gateway → Step Functions → Lambda** |

### Decoupling Patterns

| # | Scenario | Answer |
|---|----------|--------|
| 209 | Decouple order processing | **SQS between services** |
| 210 | Fanout notifications | **SNS → Multiple SQS** |
| 211 | Event-driven microservices | **EventBridge → Lambda** |
| 212 | Queue-based scaling | **SQS + Auto Scaling (queue depth)** |

---

## 🎯 EXAM DAY QUICK REFERENCE

### When the question says... Think...

| Keyword | Service |
|---------|---------|
| "decouple" | SQS |
| "fanout" | SNS |
| "event-driven" | EventBridge / Lambda |
| "workflow" | Step Functions |
| "real-time streaming" | Kinesis Data Streams |
| "load to S3" | Kinesis Data Firehose |
| "serverless" | Lambda / Fargate / DynamoDB |
| "SQL on S3" | Athena |
| "data warehouse" | Redshift |
| "cost-effective" | Reserved / Spot / Glacier |
| "millisecond latency" | DynamoDB / ElastiCache |
| "microsecond latency" | DAX |
| "audit API calls" | CloudTrail |
| "monitor metrics" | CloudWatch |
| "threat detection" | GuardDuty |
| "vulnerability scan" | Inspector |
| "sensitive data in S3" | Macie |
| "DDoS protection" | Shield |
| "web firewall" | WAF |
| "secrets rotation" | Secrets Manager |
| "encryption keys" | KMS |
| "SSL certificate" | ACM |

---

## 🏆 You're Ready!

**Remember:**
1. Read questions carefully
2. Identify the PRIMARY requirement
3. Eliminate wrong answers
4. Choose AWS-managed over self-managed
5. Security and HA are priorities
6. Trust your preparation!

**Good luck on your exam!** 🎉

---

*This file contains 200+ scenarios covering all SAA-C03 exam topics*
