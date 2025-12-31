# AWS SAA-C03 CHEATSHEET: MASTER QUICK REFERENCE

## 📋 ULTIMATE EXAM DAY REFERENCE

---

# ⚡ INSTANT ANSWER HEURISTICS

## Service Selection Keywords

| Keyword | → Answer |
|---------|----------|
| **Serverless compute** | Lambda |
| **Container orchestration** | ECS or EKS |
| **Serverless containers** | Fargate |
| **Object storage** | S3 |
| **Block storage** | EBS |
| **File storage Linux** | EFS |
| **File storage Windows** | FSx for Windows |
| **HPC file storage** | FSx for Lustre |
| **Relational database** | RDS or Aurora |
| **NoSQL key-value** | DynamoDB |
| **In-memory cache** | ElastiCache |
| **Data warehouse** | Redshift |
| **Search engine** | OpenSearch |
| **Message queue** | SQS |
| **Pub/sub** | SNS |
| **Event bus** | EventBridge |
| **Real-time streaming** | Kinesis |
| **API management** | API Gateway |
| **CDN** | CloudFront |
| **DNS** | Route 53 |
| **Load balancing HTTP** | ALB |
| **Load balancing TCP/UDP** | NLB |

---

# 🔐 SECURITY QUICK ANSWERS

| Scenario | Answer |
|----------|--------|
| Encrypt data at rest | **KMS** |
| Rotate database credentials | **Secrets Manager** |
| Store config parameters | **Parameter Store** |
| Block SQL injection | **WAF** |
| DDoS protection | **Shield** |
| SSL certificates | **ACM** |
| User authentication | **Cognito User Pools** |
| AWS credentials for apps | **Cognito Identity Pools** |
| Cross-account access | **IAM Roles with Trust Policy** |
| SSO for AWS accounts | **IAM Identity Center** |
| Threat detection | **GuardDuty** |
| Vulnerability scanning | **Inspector** |
| Sensitive data discovery | **Macie** |
| Security posture | **Security Hub** |

---

# 💾 STORAGE QUICK ANSWERS

| Scenario | Answer |
|----------|--------|
| Frequently accessed | **S3 Standard** |
| Unknown access pattern | **S3 Intelligent-Tiering** |
| Infrequent access | **S3 Standard-IA** |
| Archive, instant retrieval | **S3 Glacier Instant** |
| Archive, flexible | **S3 Glacier Flexible** |
| Long-term archive | **S3 Glacier Deep Archive** |
| WORM compliance | **S3 Object Lock** |
| Shared Linux files | **EFS** |
| Shared Windows files | **FSx for Windows** |
| High-performance parallel | **FSx for Lustre** |
| Hybrid cloud storage | **Storage Gateway** |
| Large data migration | **Snow Family** |

---

# 🗄️ DATABASE QUICK ANSWERS

| Scenario | Answer |
|----------|--------|
| MySQL/PostgreSQL managed | **RDS** |
| 5x MySQL performance | **Aurora** |
| Auto-scaling relational | **Aurora Serverless** |
| Cross-region DB | **Aurora Global Database** |
| NoSQL millisecond | **DynamoDB** |
| NoSQL microsecond | **DynamoDB + DAX** |
| Multi-region NoSQL | **DynamoDB Global Tables** |
| Redis caching | **ElastiCache Redis** |
| Simple caching | **ElastiCache Memcached** |
| Analytics queries | **Redshift** |
| MongoDB workloads | **DocumentDB** |
| Graph queries | **Neptune** |
| Ledger/audit | **QLDB** |

---

# 🌐 NETWORKING QUICK ANSWERS

| Scenario | Answer |
|----------|--------|
| Internet for private subnet | **NAT Gateway** |
| Connect two VPCs | **VPC Peering** |
| Connect 100+ VPCs | **Transit Gateway** |
| Private S3 access | **Gateway Endpoint** |
| Private AWS service access | **Interface Endpoint** |
| Dedicated connection | **Direct Connect** |
| Quick encrypted tunnel | **Site-to-Site VPN** |
| Instance firewall | **Security Group** |
| Subnet firewall | **NACL** |
| Block specific IPs | **NACL deny rule** |
| Global static IPs | **Global Accelerator** |
| Content delivery | **CloudFront** |
| Restrict S3 to CloudFront | **OAC + Bucket Policy** |

---

# 📊 MONITORING QUICK ANSWERS

| Scenario | Answer |
|----------|--------|
| Monitor metrics | **CloudWatch** |
| Memory metrics | **CloudWatch Agent** |
| Track API calls | **CloudTrail** |
| Resource compliance | **AWS Config** |
| Auto-remediate | **Config + SSM Automation** |
| Cost visualization | **Cost Explorer** |
| Spending alerts | **AWS Budgets** |
| Secure shell no SSH | **Session Manager** |
| Best practices | **Trusted Advisor** |

---

# 🏗️ HA/DR QUICK ANSWERS

| Scenario | Answer |
|----------|--------|
| Survive AZ failure | **Multi-AZ** |
| Survive region failure | **Multi-Region** |
| Lowest cost DR | **Backup & Restore** |
| Fast DR | **Warm Standby** |
| Zero downtime | **Active-Active Multi-Site** |
| Auto failover DNS | **Route 53 Failover** |
| < 1 sec replication | **Aurora Global Database** |
| Scale on demand | **Auto Scaling** |
| Decouple components | **SQS** |

---

# 📈 PERFORMANCE QUICK ANSWERS

| Scenario | Answer |
|----------|--------|
| Reduce DB load | **Read Replicas** |
| Reduce latency reads | **ElastiCache** |
| Reduce latency global | **CloudFront** |
| Reduce S3 retrieval | **S3 Intelligent-Tiering** |
| Speed up DNS | **Route 53 Latency Routing** |
| Reduce cold starts | **Lambda Provisioned Concurrency** |
| Connection pooling | **RDS Proxy** |

---

# 💰 COST QUICK ANSWERS

| Scenario | Answer |
|----------|--------|
| Steady workload discount | **Reserved Instances** |
| Flexible compute discount | **Compute Savings Plans** |
| Fault-tolerant cheap | **Spot Instances** |
| Reduce S3 costs | **Lifecycle policies** |
| Reduce data transfer | **VPC endpoints** |
| Right-size instances | **Compute Optimizer** |

---

# 🔢 CRITICAL NUMBERS

## Lambda Limits
| Limit | Value |
|-------|-------|
| Timeout | 15 minutes |
| Memory | 128 MB - 10 GB |
| Package size | 50 MB zipped, 250 MB unzipped |
| Container image | 10 GB |
| /tmp storage | 512 MB - 10 GB |

## S3 Limits
| Limit | Value |
|-------|-------|
| Object size | 5 TB max |
| Single PUT | 5 GB max |
| Multipart | Required > 100 MB |

## EBS IOPS
| Type | Max IOPS |
|------|----------|
| gp3 | 16,000 |
| io2 Block Express | 256,000 |
| io2/io1 | 64,000 |

## SQS
| Setting | Value |
|---------|-------|
| Message size | 256 KB |
| Retention | 4 days default, 14 max |
| Visibility timeout | 30 sec default |
| FIFO throughput | 3,000 msg/s |

## RDS
| Feature | Value |
|---------|-------|
| Read Replicas | 5 (RDS), 15 (Aurora) |
| Backup retention | 1-35 days |
| Multi-AZ failover | 1-2 minutes |

---

# ✅ PRE-EXAM CHECKLIST

```
COMPUTE
□ EC2 instance types (M, C, R, T, etc.)
□ EC2 purchasing options
□ Placement groups (cluster, spread, partition)
□ Lambda limits and triggers

STORAGE
□ S3 storage classes
□ S3 encryption types
□ EBS volume types
□ EFS vs EBS vs S3

DATABASE
□ RDS Multi-AZ vs Read Replicas
□ Aurora architecture
□ DynamoDB capacity modes
□ ElastiCache Redis vs Memcached

NETWORKING
□ VPC components
□ Security Groups vs NACLs
□ VPN vs Direct Connect
□ Route 53 routing policies

SECURITY
□ IAM policies and roles
□ KMS key types
□ Secrets Manager vs Parameter Store
□ Cognito User Pools vs Identity Pools

SERVERLESS
□ Lambda configuration
□ API Gateway types
□ SQS Standard vs FIFO
□ SNS fanout pattern

HA/DR
□ DR strategies (Backup, Pilot Light, Warm, Hot)
□ Multi-AZ vs Multi-Region
□ Auto Scaling policies
□ Load balancer types
```

---

# 🎯 EXAM TRAPS TO AVOID

| Trap | Reality |
|------|---------|
| EBS Multi-AZ | EBS is single-AZ, use snapshots |
| NAT Gateway HA | Single AZ, deploy per AZ |
| S3 encryption default | SSE-S3 is default now |
| Security Group deny | Cannot deny, use NACL |
| NACL stateful | NACL is stateless |
| Lambda VPC internet | Needs NAT Gateway |
| Read Replica failover | Manual, not automatic |
| Aurora Serverless v1 | v2 is instant scaling |
| Secrets Manager free | $0.40/secret/month |
| Transit Gateway free | $0.05/attachment/hour |

---

# 📝 EXAM STRATEGIES

## Question Approach
1. Read the LAST sentence first (what they want)
2. Identify keywords
3. Eliminate wrong answers
4. Choose most AWS-native solution
5. Consider cost vs performance trade-offs

## Common Patterns
- "Cost-effective" → Think Reserved/Spot/Savings Plans
- "Decoupled" → Think SQS/SNS
- "Real-time" → Think Kinesis
- "Serverless" → Think Lambda/Fargate
- "Highly available" → Think Multi-AZ
- "Disaster recovery" → Think Multi-Region

## Time Management
- ~1.5 minutes per question
- Flag difficult questions
- Review flagged at end
- Never leave blank

---

*Last Updated: December 2025*
*Exam Version: SAA-C03*
*Good luck on your exam! 🎉*
