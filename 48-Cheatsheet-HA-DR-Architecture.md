# AWS SAA-C03 CHEATSHEET: HIGH AVAILABILITY, DR & ARCHITECTURE

## 📋 PRINT-READY EXAM REFERENCE

---

# 🏗️ WELL-ARCHITECTED FRAMEWORK

## Six Pillars

| Pillar | Focus |
|--------|-------|
| **Operational Excellence** | Run and monitor systems |
| **Security** | Protect data and systems |
| **Reliability** | Recover from failures |
| **Performance Efficiency** | Use resources efficiently |
| **Cost Optimization** | Avoid unnecessary costs |
| **Sustainability** | Minimize environmental impact |

---

# ⚡ HIGH AVAILABILITY CONCEPTS

## Key Terms

| Term | Definition |
|------|------------|
| **High Availability** | System remains operational |
| **Fault Tolerance** | Continue operating despite failures |
| **Disaster Recovery** | Recover from catastrophic events |
| **RTO** | Recovery Time Objective (max downtime) |
| **RPO** | Recovery Point Objective (max data loss) |

## Availability Targets

| Availability | Downtime/Year | Downtime/Month |
|--------------|---------------|----------------|
| **99%** | 3.65 days | 7.2 hours |
| **99.9%** | 8.76 hours | 43.8 minutes |
| **99.99%** | 52.56 minutes | 4.38 minutes |
| **99.999%** | 5.26 minutes | 26.3 seconds |

---

# 🔄 DISASTER RECOVERY STRATEGIES

## DR Strategies (Lowest to Highest Cost)

| Strategy | RTO | RPO | Cost | Description |
|----------|-----|-----|------|-------------|
| **Backup & Restore** | Hours | Hours | $ | Restore from backups |
| **Pilot Light** | 10s min | Minutes | $$ | Minimal always-on core |
| **Warm Standby** | Minutes | Seconds | $$$ | Scaled-down full system |
| **Multi-Site/Hot** | Real-time | Zero | $$$$ | Full production duplicate |

## DR Strategy Details

### Backup & Restore
- Cheapest option
- Highest RTO/RPO
- Restore from S3, snapshots
- Use case: Non-critical, cost-sensitive

### Pilot Light
- Core services always running (DB replication)
- Compute stopped until needed
- Scale up during disaster
- Use case: Moderate criticality

### Warm Standby
- Scaled-down version running
- Can handle limited traffic
- Scale up quickly
- Use case: Important applications

### Multi-Site Active-Active
- Full production in multiple regions
- Real-time data replication
- Instant failover
- Use case: Mission-critical, zero downtime

## DR Components

### Data Replication

| Service | Method |
|---------|--------|
| **S3** | Cross-region replication |
| **RDS** | Read replicas, Multi-AZ |
| **Aurora** | Global Database |
| **DynamoDB** | Global Tables |
| **EBS** | Snapshots + copy |

### Compute Recovery

| Service | Method |
|---------|--------|
| **EC2** | AMIs + Auto Scaling |
| **ECS/EKS** | Multi-region deployment |
| **Lambda** | Deploy to multiple regions |

### Network Recovery

| Service | Method |
|---------|--------|
| **Route 53** | Failover routing |
| **Global Accelerator** | Automatic failover |
| **CloudFront** | Origin failover |

---

# 🌐 MULTI-REGION ARCHITECTURE

## Multi-Region Patterns

### Active-Passive
- Primary region handles traffic
- Secondary on standby
- Route 53 failover routing
- Lower cost, higher RTO

### Active-Active
- Both regions handle traffic
- Route 53 latency/geolocation routing
- Data sync required
- Higher cost, lower RTO

## Multi-Region Data

### Database Options

| Pattern | Service | RPO |
|---------|---------|-----|
| **Async replication** | RDS Read Replica | Minutes |
| **Near-sync** | Aurora Global Database | < 1 second |
| **Multi-master** | DynamoDB Global Tables | < 1 second |

### Storage Options

| Service | Replication |
|---------|-------------|
| **S3 CRR** | Async, minutes |
| **EFS** | Same region only |
| **FSx** | Cross-region backup |

---

# 🎯 HA PATTERNS BY SERVICE

## Compute HA

### EC2
- Auto Scaling across AZs
- Multiple AZ deployment
- Application Load Balancer
- Launch Template versioning

### ECS/EKS
- Multi-AZ task placement
- Service auto scaling
- ALB health checks
- Multi-region with Route 53

### Lambda
- Auto multi-AZ
- Reserved concurrency
- Multi-region with Route 53

## Database HA

### RDS
| Feature | Use Case |
|---------|----------|
| **Multi-AZ** | Same-region HA |
| **Read Replicas** | Read scaling, DR |
| **Cross-region replica** | DR |

### Aurora
| Feature | Use Case |
|---------|----------|
| **Multi-AZ** | Default (3 AZs) |
| **Global Database** | Cross-region DR |
| **Serverless v2** | Auto scaling |

### DynamoDB
| Feature | Use Case |
|---------|----------|
| **Multi-AZ** | Default (3 AZs) |
| **Global Tables** | Multi-region |
| **On-demand** | Auto scaling |

## Storage HA

### S3
- 99.999999999% durability
- Multi-AZ by default
- Cross-region replication for DR

### EBS
- Single AZ
- Snapshots for backup
- Multi-Attach for io1/io2

### EFS
- Multi-AZ by default
- Regional by default

---

# 🔀 LOAD BALANCING PATTERNS

## Load Balancer Selection

| Scenario | Load Balancer |
|----------|---------------|
| HTTP/HTTPS | ALB |
| TCP/UDP | NLB |
| Static IP needed | NLB |
| Extreme performance | NLB |
| Path-based routing | ALB |
| Third-party appliances | GLB |

## Health Check Configuration

| Setting | Recommendation |
|---------|----------------|
| **Interval** | 10-30 seconds |
| **Threshold** | 2-3 failures |
| **Path** | /health (lightweight) |
| **Timeout** | < Interval |

## Connection Draining

| Setting | Value |
|---------|-------|
| **Default** | 300 seconds |
| **Range** | 0-3600 seconds |
| **Recommendation** | Match request duration |

---

# 📈 AUTO SCALING PATTERNS

## Scaling Strategies

| Strategy | Use Case |
|----------|----------|
| **Target Tracking** | Maintain metric (CPU 50%) |
| **Step Scaling** | Different actions per threshold |
| **Scheduled** | Predictable patterns |
| **Predictive** | ML-based forecasting |

## Scaling Metrics

| Metric | Best For |
|--------|----------|
| **CPUUtilization** | General workloads |
| **RequestCountPerTarget** | Web applications |
| **Custom metric** | Application-specific |

## Scaling Best Practices

| Practice | Description |
|----------|-------------|
| **Cooldown** | Prevent thrashing (300s default) |
| **Warmup** | Wait for instance ready |
| **Min/Max** | Set appropriate limits |
| **Health checks** | Use ELB health checks |

---

# 🔐 RESILIENCE PATTERNS

## Circuit Breaker
- Stop cascading failures
- Fallback responses
- Implement with Step Functions

## Bulkhead
- Isolate components
- Limit blast radius
- Use separate pools

## Retry with Backoff
- Exponential backoff
- Jitter (randomization)
- Max retries limit

## Throttling
- Rate limiting
- API Gateway throttling
- SQS for buffering

## Queue-Based Load Leveling
- SQS between components
- Handle traffic spikes
- Decouple producers/consumers

---

# 🏭 ARCHITECTURE PATTERNS

## Microservices

| Component | AWS Service |
|-----------|-------------|
| **Compute** | Lambda, ECS, EKS |
| **API** | API Gateway, ALB |
| **Data** | DynamoDB, RDS |
| **Messaging** | SQS, SNS, EventBridge |
| **Service Mesh** | App Mesh |

## Event-Driven

| Pattern | Services |
|---------|----------|
| **Event source** | S3, DynamoDB, CloudWatch |
| **Event bus** | EventBridge |
| **Processing** | Lambda, Step Functions |
| **Fanout** | SNS → SQS |

## Serverless

| Layer | Service |
|-------|---------|
| **API** | API Gateway |
| **Compute** | Lambda |
| **Data** | DynamoDB, Aurora Serverless |
| **Storage** | S3 |
| **Auth** | Cognito |

## Three-Tier Architecture

| Tier | Components |
|------|------------|
| **Presentation** | CloudFront, S3, ALB |
| **Application** | EC2, ECS, Lambda |
| **Data** | RDS, DynamoDB, ElastiCache |

---

# 🌍 GLOBAL ARCHITECTURE

## Global Services

| Service | Type |
|---------|------|
| **IAM** | Global |
| **Route 53** | Global |
| **CloudFront** | Global |
| **WAF** | Global (CloudFront) |
| **Global Accelerator** | Global |

## Regional Services

| Service | Region Scope |
|---------|--------------|
| **VPC** | Regional |
| **S3** | Regional (global namespace) |
| **EC2** | AZ |
| **RDS** | AZ (Multi-AZ option) |
| **Lambda** | Regional |

## Global Routing

| Service | Use Case |
|---------|----------|
| **Route 53 Latency** | Lowest latency |
| **Route 53 Geolocation** | Compliance, localization |
| **Route 53 Geoproximity** | Shift traffic |
| **Global Accelerator** | Non-HTTP, static IPs |
| **CloudFront** | Static/dynamic content |

---

# 🎯 HA/DR QUICK HEURISTICS

| Keyword/Scenario | Answer |
|------------------|--------|
| Survive AZ failure | **Multi-AZ deployment** |
| Survive region failure | **Multi-region deployment** |
| Zero RPO | **Synchronous replication** |
| Lowest cost DR | **Backup & Restore** |
| < 1 second cross-region | **Aurora Global Database** |
| Global NoSQL | **DynamoDB Global Tables** |
| Automatic failover DNS | **Route 53 Failover** |
| Static IP failover | **Global Accelerator** |
| Scale based on demand | **Auto Scaling** |
| Decouple components | **SQS** |
| Prevent cascade failures | **Circuit breaker** |
| Event-driven | **EventBridge + Lambda** |

---

# 📊 HA/DR DECISION MATRIX

| Requirement | Solution |
|-------------|----------|
| Single region HA | Multi-AZ + ALB + ASG |
| Multi-region HA | Route 53 + Multi-region deployment |
| Database DR | Read Replicas / Global Database |
| Stateless app HA | ASG across AZs |
| Session persistence | ElastiCache / DynamoDB |
| Zero downtime deploy | Blue/green, canary |
| Cost-optimized DR | Backup & Restore |
| Fast DR | Warm standby / Multi-site |

---

# ✅ EXAM DAY QUICK CHECKS

```
□ Know DR strategies and RTO/RPO
□ Know Multi-AZ vs Multi-Region
□ Know Aurora Global Database features
□ Know DynamoDB Global Tables
□ Know Route 53 routing policies for DR
□ Know Auto Scaling strategies
□ Know Load Balancer health checks
□ Know resilience patterns
□ Know global vs regional services
```

---

*Last Updated: December 2025*
*Exam Version: SAA-C03*
