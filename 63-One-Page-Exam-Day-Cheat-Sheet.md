# AWS SAA-C03: ONE-PAGE EXAM DAY CHEAT SHEET

**Print this. Memorize this. Pass the exam. 🚀**

---

## ⚡ INSTANT SERVICE SELECTION (Top 30)

| Keyword | Service | Type |
|---------|---------|------|
| **Serverless compute** | Lambda | Compute |
| **5x performance** | Aurora | Database |
| **NoSQL + serverless** | DynamoDB | Database |
| **Object storage** | S3 | Storage |
| **Block storage** | EBS | Storage |
| **Shared file (Linux)** | EFS | Storage |
| **Shared file (Windows)** | FSx for Windows | Storage |
| **Petabyte warehouse** | Redshift | Database |
| **SQL on S3** | Athena | Analytics |
| **Cache DB** | ElastiCache Redis | Database |
| **Cache DynamoDB** | DAX | Database |
| **Queue** | SQS | Integration |
| **Pub/Sub** | SNS | Integration |
| **CDN** | CloudFront | Network |
| **DNS** | Route 53 | Network |
| **Web LB** | ALB | Network |
| **High-perf LB** | NLB | Network |
| **Encryption keys** | KMS | Security |
| **Secrets + rotation** | Secrets Manager | Security |
| **Threat detection** | GuardDuty | Security |
| **API audit** | CloudTrail | Security |
| **PII detection** | Macie | Security |
| **DDoS (basic)** | Shield Standard | Security |
| **Web firewall** | WAF | Security |
| **Real-time stream** | Kinesis | Analytics |
| **ETL** | Glue | Analytics |
| **Container (simple)** | ECS | Compute |
| **Container (K8s)** | EKS | Compute |
| **Serverless container** | Fargate | Compute |
| **Batch processing** | AWS Batch | Compute |

---

## 🎯 CRITICAL COMPARISONS

### Multi-AZ vs Multi-Region vs Read Replica
- **Multi-AZ** = HA (failover), same region, sync, automatic
- **Multi-Region** = DR, different regions, async, manual
- **Read Replica** = Read scaling, async, manual promote

### Security Group vs NACL
- **Security Group** = Stateful, instance, allow only
- **NACL** = Stateless, subnet, allow + deny

### ALB vs NLB
- **ALB** = HTTP/HTTPS, Layer 7, path routing
- **NLB** = TCP/UDP, Layer 4, high perf, static IP

### CloudFront vs Global Accelerator
- **CloudFront** = HTTP, caching, Layer 7
- **Global Accelerator** = TCP/UDP, no cache, Layer 4

### S3 vs EBS vs EFS vs Instance Store
- **S3** = Object, unlimited, web access
- **EBS** = Block, single EC2, persistent
- **EFS** = File (NFS), shared, Linux
- **Instance Store** = Block, temp, highest perf

---

## 🔥 DECISION RULES (Use First Match)

### Storage Class
```
Unknown pattern → Intelligent-Tiering
Frequent → Standard
Infrequent + instant → Standard-IA
Infrequent + single AZ → One Zone-IA
Archive + instant → Glacier Instant
Archive + hours → Glacier Flexible
7+ years → Glacier Deep Archive
```

### Database
```
Relational + 5x → Aurora
Relational + standard → RDS
NoSQL + serverless → DynamoDB
MongoDB → DocumentDB
Graph → Neptune
Warehouse → Redshift
Cache DynamoDB → DAX
Cache database → ElastiCache Redis
```

### Compute
```
< 15 min + event → Lambda
> 15 min → EC2/ECS/Fargate
Containers + K8s → EKS
Containers + simple → ECS
Serverless containers → Fargate
Batch → AWS Batch
```

---

## 🚨 EXAM TRAPS - AVOID!

| Trap | Wrong Answer | Correct Answer |
|------|--------------|----------------|
| Block specific IP | Security Group | NACL |
| > 15 min serverless | Lambda | Fargate/EC2 |
| Multi-AZ for DR | Multi-AZ only | Multi-Region |
| Read scaling | Multi-AZ | Read Replica |
| Cache DynamoDB | ElastiCache | DAX |
| Shared Linux FS | EBS | EFS |
| TCP/UDP global | CloudFront | Global Accelerator |
| Cost-effective + steady | Lambda | Reserved Instances |

---

## 📊 CRITICAL LIMITS

| Service | Limit | Impact |
|---------|-------|--------|
| Lambda | 15 min max | Can't use for longer |
| DynamoDB | 400 KB item | Use S3 for large |
| SQS | 14 days retention | Auto-delete after |
| S3 | 5 TB max object | Multipart for larger |
| API Gateway | 29 sec timeout | Long tasks = async |

---

## 🏆 DOMAIN QUICK WINS

### Secure (30%)
- Encryption: KMS + enable on all services
- Least privilege: Minimal IAM permissions
- MFA: Sensitive operations
- Private: Private subnets + NAT Gateway
- Audit: CloudTrail + Config + GuardDuty

### Resilient (26%)
- Multi-AZ: RDS, ALB, NAT Gateway
- Auto Scaling: EC2, DynamoDB
- Decouple: SQS, SNS, EventBridge
- Backup: Automated + cross-region
- Health checks: ELB, Route 53

### Performance (24%)
- Cache: CloudFront + ElastiCache/DAX
- Read replicas: RDS/Aurora
- Right instance: Match workload type
- Placement groups: HPC/cluster
- Enhanced networking: High throughput

### Cost (20%)
- Reserved/Spot: Predictable/flexible
- Lifecycle: S3 → IA → Glacier
- Serverless: Lambda, Fargate, Aurora Serverless
- Right-size: Compute Optimizer
- Monitor: Cost Explorer, Budgets

---

## 🎯 EXAM STRATEGY

### Time Management
- 65 questions / 130 min = 2 min/question
- Flag hard questions, return later
- Don't get stuck

### Approach
1. Read question twice
2. Identify domain & keywords
3. Match to service pattern
4. Eliminate wrong answers
5. Choose most managed/serverless

### When Stuck
- What's the core requirement?
- What domain is it?
- Which keywords are present?
- Choose serverless/managed option

---

## 💡 TOP 10 HEURISTICS

1. **"Serverless"** → Lambda, DynamoDB, Aurora Serverless, Fargate
2. **"Cache"** → CloudFront (web), ElastiCache (DB), DAX (DynamoDB)
3. **"HA"** → Multi-AZ + Auto Scaling + Load Balancer
4. **"DR"** → Multi-Region + Backups
5. **"Cost"** → Right-size + Reserved/Spot + Lifecycle
6. **"Performance"** → Cache + Read Replicas + Right instance
7. **"Security"** → Encrypt + Least privilege + MFA + Private
8. **"Decouple"** → SQS, SNS, EventBridge
9. **"Storage"** → S3 (object), EBS (block), EFS (shared)
10. **"Database"** → Aurora (relational high-perf), DynamoDB (NoSQL), Redshift (warehouse)

---

## ✅ CONFIDENCE CHECK (< 5 sec each)

1. Serverless + event-driven + < 15 min = **Lambda**
2. NoSQL + serverless + key-value = **DynamoDB**
3. Web LB + path routing = **ALB**
4. Object storage + unlimited = **S3**
5. Cache DynamoDB + microsecond = **DAX**
6. Queue + decouple = **SQS**
7. Fan-out + multiple subscribers = **SNS**
8. Relational + 5x performance = **Aurora**
9. Warehouse + petabyte = **Redshift**
10. CDN + global delivery = **CloudFront**

**10/10? You're ready! 🎯**

---

## 🔢 LAST-MINUTE MEMORY DUMP

**Write on scratch paper at exam start:**

```
Lambda: 15 min max
S3: 5 TB max object
SQS: 14 day max
DynamoDB: 400 KB max item
API Gateway: 29 sec timeout

Multi-AZ = HA (same region)
Multi-Region = DR (cross region)
Read Replica = Read scale (async)

SG = Stateful, allow, instance
NACL = Stateless, allow+deny, subnet

ALB = L7 HTTP
NLB = L4 TCP/UDP

CloudFront = L7 + cache
Global Accel = L4 + no cache

S3 Tiers:
Standard → Intelligent → IA → Glacier

DR Tiers:
Backup/Restore: Hours
Pilot Light: Min-Hours
Warm Standby: Minutes
Multi-Site: Real-time
```

---

## 🎓 FINAL MANTRAS

1. **AWS favors:** Managed, serverless, automated, secure
2. **When in doubt:** Choose serverless option
3. **Read carefully:** Keywords matter
4. **Eliminate first:** Often easier than finding right answer
5. **Trust your gut:** First instinct usually right
6. **Stay calm:** You've prepared for this

---

## 🚀 YOU'VE GOT THIS!

**Remember:**
- Most questions are pattern recognition
- Keywords → Services → Answers
- You know more than you think
- Stay confident, read carefully
- Trust your preparation

**Good luck on your AWS Solutions Architect Associate exam! 🎯**

---

*One last tip: Take a deep breath, believe in yourself, and remember that this certification is just the beginning of your cloud journey. You're going to do great!*
