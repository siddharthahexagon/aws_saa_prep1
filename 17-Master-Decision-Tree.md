# AWS SAA-C03: Master Decision Tree - One-Shot Quick Reference

## 🎯 Ultimate Quick Reference for Exam Day

This file contains the most critical decision trees consolidated for rapid access during the exam. Use this when you need to make a decision in 10 seconds or less.

---

## ⚡ Super Quick Service Selection

```
SCENARIO KEYWORD → SERVICE

"Decouple" → SQS
"Fanout/Pub-sub" → SNS
"Queue + ordering" → SQS FIFO
"Event-driven" → EventBridge or Lambda
"Workflow orchestration" → Step Functions
"Real-time streaming" → Kinesis Data Streams
"Load to S3" → Kinesis Data Firehose
"Query S3 with SQL" → Athena
"Data warehouse" → Redshift
"Cache DynamoDB" → DAX
"Cache general" → ElastiCache (Redis/Memcached)
"Serverless compute" → Lambda
"Container orchestration" → ECS or EKS
"NoSQL, fast" → DynamoDB
"Relational, high performance" → Aurora
"Object storage" → S3
"Block storage" → EBS
"Shared file system (Linux)" → EFS
"Shared file system (Windows)" → FSx for Windows
"CDN/cache content" → CloudFront
"Route traffic globally" → Route 53
"Load balance HTTP" → ALB
"Load balance TCP/UDP" → NLB
"VPN to AWS" → Site-to-Site VPN
"Dedicated network" → Direct Connect
"Threat detection" → GuardDuty
"Vulnerability scan" → Inspector
"Audit API calls" → CloudTrail
"Encryption keys" → KMS
"SSL certificates" → ACM
"Secrets + rotation" → Secrets Manager
"Monitor metrics/logs" → CloudWatch
"Infrastructure as code" → CloudFormation
"Cost analysis" → Cost Explorer
"Multi-account management" → Organizations
```

---

## 🔥 Top 20 Exam Patterns (Must Know)

### 1. Decouple Components
```
Problem: Tight coupling, scaling issues
Solution: SQS between tiers
```

### 2. Fanout to Multiple Targets
```
Problem: Send to multiple destinations
Solution: SNS topic → Multiple SQS queues
```

### 3. High Availability
```
Problem: Single point of failure
Solution: Multi-AZ + Auto Scaling + ELB + Route 53
```

### 4. Disaster Recovery
```
Problem: Need backup region
Solution: Cross-region replication + Route 53 failover
```

### 5. Serverless Web App
```
Problem: No server management, scale to zero
Solution: S3 (static) + CloudFront + API Gateway + Lambda + DynamoDB
```

### 6. Caching for Performance
```
Problem: Slow database queries
Solution: ElastiCache (Redis) or DAX (for DynamoDB)
```

### 7. Static Content Delivery
```
Problem: Global users, slow loading
Solution: S3 + CloudFront (CDN)
```

### 8. Read-Heavy Database
```
Problem: Too many reads on primary database
Solution: RDS/Aurora with Read Replicas
```

### 9. Session Management
```
Problem: Store user sessions across instances
Solution: ElastiCache (Redis or Memcached) or DynamoDB
```

### 10. Cost Optimization for Steady Workload
```
Problem: High EC2 costs, predictable usage
Solution: Reserved Instances or Savings Plans (40-72% savings)
```

### 11. Cost Optimization for Variable Workload
```
Problem: Unpredictable usage, want savings
Solution: Spot Instances (90% savings) or Auto Scaling
```

### 12. Secure S3 Objects
```
Problem: S3 objects must be encrypted
Solution: SSE-S3 (simple) or SSE-KMS (audit trail)
```

### 13. EC2 Needs AWS API Access
```
Problem: EC2 must call AWS services
Solution: IAM Role (never access keys!)
```

### 14. Large Data Transfer to AWS
```
Problem: 50 TB to upload, slow internet
Solution: AWS Snowball Edge
```

### 15. On-Premises to AWS Connectivity
```
Quick setup (days) → Site-to-Site VPN
Dedicated, high bandwidth (weeks) → Direct Connect
```

### 16. Protect from DDoS
```
Basic (free) → Shield Standard
Advanced + support → Shield Advanced
```

### 17. Protect from SQL Injection
```
Problem: Web application vulnerabilities
Solution: AWS WAF
```

### 18. Track All API Calls
```
Problem: Audit and compliance
Solution: CloudTrail (enabled in all regions)
```

### 19. Automated Threat Detection
```
Problem: Find compromised instances
Solution: GuardDuty
```

### 20. Query Logs in S3
```
Problem: Analyze logs without loading
Solution: Amazon Athena
```

---

## 💡 Decision Tree: "I Need Storage"

```
START: Need storage?
│
├─→ For EC2 only? → EBS
├─→ Shared across instances? → EFS (Linux) or FSx (Windows)
├─→ Object storage (files)? → S3
├─→ Archive (7+ years)? → S3 Glacier Deep Archive
├─→ Caching? → ElastiCache
├─→ Database? → RDS, Aurora, or DynamoDB
└─→ Large data transfer? → Snowball or DataSync
```

---

## 💡 Decision Tree: "I Need Compute"

```
START: Need compute?
│
├─→ < 15 minutes, event-driven? → Lambda
├─→ Containers, no server management? → Fargate
├─→ Containers with control? → ECS or EKS
├─→ Full OS control? → EC2
├─→ Quick web app deployment? → Elastic Beanstalk
└─→ Batch processing? → AWS Batch
```

---

## 💡 Decision Tree: "I Need a Database"

```
START: Need database?
│
├─→ Relational (SQL)?
│   ├─→ Highest performance MySQL/PostgreSQL? → Aurora
│   ├─→ Standard RDBMS? → RDS
│   └─→ Oracle/SQL Server specific features? → RDS
│
├─→ NoSQL?
│   ├─→ Key-value, fast? → DynamoDB
│   ├─→ MongoDB-compatible? → DocumentDB
│   ├─→ Graph? → Neptune
│   └─→ Cassandra? → Keyspaces
│
├─→ Caching?
│   ├─→ DynamoDB cache? → DAX
│   └─→ General cache? → ElastiCache (Redis or Memcached)
│
├─→ Data warehouse?
│   └─→ Redshift
│
└─→ Time-series?
    └→ Timestream
```

---

## 💡 Decision Tree: "I Need Networking"

```
START: Networking?
│
├─→ Load balance HTTP? → ALB
├─→ Load balance TCP/UDP? → NLB
├─→ DNS? → Route 53
├─→ CDN? → CloudFront
├─→ Connect to on-premises?
│   ├─→ Quick (days)? → Site-to-Site VPN
│   └─→ Dedicated (weeks)? → Direct Connect
├─→ Private subnet internet access? → NAT Gateway
└─→ Expose API? → API Gateway
```

---

## 💡 Decision Tree: "I Need Security"

```
START: Security?
│
├─→ Access control?
│   ├─→ AWS services? → IAM Role
│   ├─→ Users? → IAM Users/Groups
│   └─→ Mobile/web app users? → Cognito
│
├─→ Encryption?
│   ├─→ Keys? → KMS (customer-managed) or CloudHSM (dedicated)
│   ├─→ SSL/TLS? → ACM
│   └─→ Secrets? → Secrets Manager (auto-rotate) or Parameter Store (free)
│
├─→ Threat detection? → GuardDuty
├─→ Vulnerability scan? → Inspector
├─→ DDoS protection? → Shield (Standard free, Advanced paid)
├─→ Web firewall? → WAF
└─→ Audit API calls? → CloudTrail
```

---

## 💡 Decision Tree: "I Need Integration"

```
START: Integration?
│
├─→ Queue (decouple)? → SQS
├─→ Pub/sub (fanout)? → SNS
├─→ Event routing? → EventBridge
├─→ Workflow? → Step Functions
├─→ Real-time streaming? → Kinesis Data Streams
└─→ Load to S3/Redshift? → Kinesis Data Firehose
```

---

## 🎓 Exam Heuristics - Always True

### 1. IAM Roles > Access Keys
**ALWAYS use IAM roles for EC2/Lambda/ECS. Never access keys.**

### 2. Multi-AZ = High Availability
**Multi-AZ provides automatic failover for RDS, ELB, NAT Gateway**

### 3. Read Replicas = Read Scaling
**NOT for failover, for scaling read workloads**

### 4. Encryption Keywords
- "Compliance, audit" → KMS
- "Free SSL/TLS" → ACM
- "Automatic rotation" → Secrets Manager

### 5. Cost Keywords
- "Cost-effective" + steady → Reserved Instances / Savings Plans
- "Cost-effective" + variable → Spot Instances
- "Lowest cost storage" → S3 Glacier Deep Archive

### 6. Performance Keywords
- "Low latency" → CloudFront, ElastiCache, DAX
- "Real-time" → Kinesis, Lambda, DynamoDB
- "High IOPS" → io2 Block Express

### 7. Serverless Keywords
- Lambda, Fargate, Aurora Serverless, DynamoDB, S3, Athena, Glue

### 8. High Availability Pattern
**Multi-AZ + Auto Scaling + ELB + Route 53**

### 9. Disaster Recovery Pattern
**Cross-region backups/replication + Route 53 failover**

### 10. Decoupling Pattern
**SQS between components or SNS for fanout**

---

## 🚨 Common Exam Traps - Don't Fall For These!

### ❌ Trap 1: RDS Multi-AZ = Read Scaling
**WRONG!** Multi-AZ is for failover, not read scaling.
**Correct:** Use Read Replicas for read scaling.

### ❌ Trap 2: Lambda for Long-Running Tasks
**WRONG!** Lambda max 15 minutes.
**Correct:** Use Fargate, ECS, or Batch for > 15 min.

### ❌ Trap 3: CloudWatch vs CloudTrail
**CloudWatch:** Monitoring (metrics, logs, alarms)
**CloudTrail:** Auditing (API calls, who did what)

### ❌ Trap 4: S3 IA vs Glacier
**S3 Standard-IA:** Instant access, infrequent
**Glacier:** Archive, retrieval time (minutes to hours)

### ❌ Trap 5: Security Groups vs NACLs
**Security Groups:** Stateful, allow only, instance-level
**NACLs:** Stateless, allow + deny, subnet-level

### ❌ Trap 6: Kinesis Data Streams vs Firehose
**Data Streams:** Real-time, custom processing, retention
**Firehose:** Near real-time, managed delivery, no retention

### ❌ Trap 7: Access Keys for EC2
**NEVER!** Always use IAM roles for EC2/Lambda.

### ❌ Trap 8: SQS FIFO Throughput
**300 TPS per FIFO queue** (3000 with batching)
Use Standard queue for unlimited throughput.

### ❌ Trap 9: Direct Connect Setup Time
**Weeks to months**, not instant. Use VPN for quick setup.

### ❌ Trap 10: Spot Instances for Production
**Only if interruption-tolerant!** Not for critical primary workloads.

---

## 📊 Service Selection Matrix (Most Common)

| Scenario | Service |
|----------|---------|
| **Decouple components** | SQS |
| **Fanout messages** | SNS |
| **Event-driven** | EventBridge, Lambda |
| **Workflow** | Step Functions |
| **Serverless compute** | Lambda |
| **Containers, serverless** | Fargate |
| **Relational, high perf** | Aurora |
| **NoSQL, millisecond** | DynamoDB |
| **Object storage** | S3 |
| **Block storage** | EBS |
| **File system (Linux)** | EFS |
| **CDN** | CloudFront |
| **DNS** | Route 53 |
| **Load balance HTTP** | ALB |
| **Load balance TCP** | NLB |
| **VPN** | Site-to-Site VPN |
| **Dedicated network** | Direct Connect |
| **Threat detection** | GuardDuty |
| **Audit logs** | CloudTrail |
| **Encryption keys** | KMS |
| **Secrets + rotation** | Secrets Manager |
| **Monitor** | CloudWatch |
| **IaC** | CloudFormation |
| **Cost analysis** | Cost Explorer |
| **Query S3** | Athena |
| **Data warehouse** | Redshift |
| **Big data** | EMR |
| **ETL** | Glue |
| **Streaming** | Kinesis |

---

## ⏱️ Time-Saving Shortcuts

### When You See These Words:

**"Decouple"** → Think SQS immediately

**"Lowest cost"** → S3 Glacier Deep Archive (storage) or Spot (compute)

**"Highest performance"** → Aurora (DB), io2 Block Express (storage)

**"Serverless"** → Lambda, Fargate, Aurora Serverless, DynamoDB

**"Real-time"** → Kinesis, Lambda, DynamoDB, EventBridge

**"High availability"** → Multi-AZ, Auto Scaling, ELB

**"Disaster recovery"** → Cross-region replication, Route 53 failover

**"Audit"** → CloudTrail

**"Monitor"** → CloudWatch

**"Least operational overhead"** → Managed services, serverless

**"Most secure"** → IAM roles, encryption, private subnets, MFA

---

## 🎯 Final Exam Day Checklist

### Before Reading Questions:
1. ✅ Identify the PRIMARY requirement (cost, performance, HA, simplicity)
2. ✅ Note constraints (time, budget, existing infrastructure)
3. ✅ Look for keywords (decouple, serverless, real-time, etc.)

### Elimination Strategy:
1. ❌ Remove services that don't fit the scenario
2. ❌ Remove solutions violating constraints
3. ✅ Choose AWS-native, managed solution
4. ✅ Choose most secure option if security-related

### Common Right Answer Patterns:
- ✅ Managed service over self-managed
- ✅ Serverless when appropriate
- ✅ IAM roles for AWS service access
- ✅ Multi-AZ for high availability
- ✅ Encryption enabled by default
- ✅ Least privilege access
- ✅ Decoupling with SQS/SNS

---

## 🏆 You're Ready!

**Remember:**
1. Read carefully, identify keywords
2. Eliminate wrong answers first
3. Choose AWS-native solutions
4. Prefer managed over self-managed
5. Always use IAM roles for AWS services
6. Multi-AZ = High Availability
7. Read Replicas = Read Scaling
8. Trust your preparation!

**Good luck on your exam!** 🎉

---

*Print this page and keep it handy during final review!*
