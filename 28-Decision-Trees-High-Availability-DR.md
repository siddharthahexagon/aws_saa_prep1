# AWS SAA-C03: High Availability & Disaster Recovery Decision Trees

## 🌳 Quick Navigation
- [High Availability Architecture](#high-availability-architecture)
- [Disaster Recovery Strategies](#disaster-recovery-strategies)
- [Multi-AZ vs Multi-Region](#multi-az-vs-multi-region)
- [Backup & Recovery](#backup--recovery)
- [Fault Tolerance Patterns](#fault-tolerance-patterns)

---

## High Availability Architecture

```
START: Need high availability?
│
├─→ What type of workload?
│   │
│   ├─→ Web Application?
│   │   └─→ **HA Web Application Pattern**
│   │       ├─→ DNS: Route 53 (health checks + failover)
│   │       ├─→ CDN: CloudFront (edge caching)
│   │       ├─→ Load Balancer: ALB (cross-AZ)
│   │       ├─→ Compute: Auto Scaling Group (multi-AZ)
│   │       ├─→ Database: RDS Multi-AZ or Aurora
│   │       ├─→ Cache: ElastiCache Multi-AZ
│   │       ├─→ Storage: S3 (11 9's durability)
│   │       └─→ Sessions: ElastiCache or DynamoDB
│   │
│   ├─→ Database?
│   │   └─→ **HA Database Pattern**
│   │       ├─→ RDS:
│   │       │   ├─→ Multi-AZ: Synchronous standby (failover)
│   │       │   ├─→ Read Replicas: Async (read scaling)
│   │       │   ├─→ Automated backups: Up to 35 days
│   │       │   └─→ Failover time: 60-120 seconds
│   │       │
│   │       ├─→ Aurora:
│   │       │   ├─→ Multi-AZ by default (6 copies, 3 AZs)
│   │       │   ├─→ Up to 15 read replicas
│   │       │   ├─→ Auto-failover: < 30 seconds
│   │       │   ├─→ Aurora Global: Cross-region (< 1 sec replication)
│   │       │   └─→ Writer + Reader endpoints
│   │       │
│   │       └─→ DynamoDB:
│   │           ├─→ Multi-AZ by default (3 AZs)
│   │           ├─→ Global Tables: Multi-region active-active
│   │           └─→ Point-in-time recovery
│   │
│   ├─→ Serverless Application?
│   │   └─→ **HA Serverless Pattern**
│   │       ├─→ API Gateway: Multi-AZ by default
│   │       ├─→ Lambda: Multi-AZ by default
│   │       ├─→ DynamoDB: Multi-AZ by default
│   │       ├─→ S3: 11 9's durability, 4 9's availability
│   │       ├─→ Step Functions: Multi-AZ by default
│   │       └─→ EventBridge: Multi-AZ by default
│   │       └─→ **Serverless = Built-in HA (no extra config)**
│   │
│   ├─→ Container Workload?
│   │   └─→ **HA Container Pattern**
│   │       ├─→ ECS/EKS:
│   │       │   ├─→ Deploy across multiple AZs
│   │       │   ├─→ Service Auto Scaling
│   │       │   ├─→ ALB for traffic distribution
│   │       │   └─→ Multiple task replicas
│   │       │
│   │       └─→ Fargate:
│   │           ├─→ AWS manages infrastructure HA
│   │           ├─→ Multi-AZ task placement
│   │           └─→ Auto Scaling
│   │
│   └─→ Stateful Application?
│       └─→ **HA Stateful Pattern**
│           ├─→ Externalize state:
│           │   ├─→ Sessions → ElastiCache or DynamoDB
│           │   ├─→ Files → S3 or EFS
│           │   └─→ Data → RDS Multi-AZ
│           ├─→ Sticky sessions (if required): ALB
│           └─→ Avoid local storage for state
│
├─→ HA Components Checklist
│   ├─→ ✅ Multiple Availability Zones (min 2, prefer 3)
│   ├─→ ✅ Elastic Load Balancer (ALB/NLB)
│   ├─→ ✅ Auto Scaling Group
│   ├─→ ✅ Health Checks (EC2, ELB, custom)
│   ├─→ ✅ Multi-AZ Database (RDS, Aurora, ElastiCache)
│   ├─→ ✅ Route 53 (DNS failover)
│   └─→ ✅ Stateless design (externalized state)
│
└─→ HA Levels by Service
    ├─→ Built-in Multi-AZ:
    │   ├─→ S3, DynamoDB, Lambda, API Gateway
    │   ├─→ SQS, SNS, EventBridge, Step Functions
    │   └─→ Aurora, ALB/NLB, NAT Gateway (per AZ)
    │
    └─→ Requires Multi-AZ Configuration:
        ├─→ RDS (enable Multi-AZ)
        ├─→ ElastiCache (cluster mode or Multi-AZ)
        ├─→ EC2 (Auto Scaling across AZs)
        ├─→ EFS (automatically Multi-AZ)
        └─→ Redshift (multi-node cluster)
```

---

## Disaster Recovery Strategies

```
START: Choose DR strategy?
│
├─→ What's your RPO/RTO requirement?
│   │
│   ├─→ RPO: Hours-Days, RTO: Hours-Days (Lowest Cost)?
│   │   └─→ **Backup and Restore**
│   │       ├─→ Strategy: Backup data, restore when needed
│   │       ├─→ RPO: Hours to days (backup frequency)
│   │       ├─→ RTO: Hours to days (restore time)
│   │       ├─→ Cost: $ (lowest)
│   │       ├─→ Implementation:
│   │       │   ├─→ S3 Cross-Region Replication
│   │       │   ├─→ RDS/Aurora automated backups + snapshots
│   │       │   ├─→ EBS snapshots (cross-region copy)
│   │       │   ├─→ AWS Backup (centralized)
│   │       │   └─→ Glacier for archives
│   │       └─→ Use case: Non-critical systems, cost-sensitive
│   │
│   ├─→ RPO: Minutes-Hours, RTO: Hours (Low Cost)?
│   │   └─→ **Pilot Light**
│   │       ├─→ Strategy: Core components always running, scale on disaster
│   │       ├─→ RPO: Minutes to hours (replication lag)
│   │       ├─→ RTO: Hours (time to scale up)
│   │       ├─→ Cost: $$ (low)
│   │       ├─→ Implementation:
│   │       │   ├─→ Database: RDS read replica in DR region (or Aurora Global)
│   │       │   ├─→ Core services: Running (minimal capacity)
│   │       │   ├─→ Other services: AMIs ready, not running
│   │       │   ├─→ Auto Scaling: Configure but min=0
│   │       │   └─→ Route 53: Health checks + failover policy
│   │       └─→ Use case: Important systems, moderate budget
│   │
│   ├─→ RPO: Minutes, RTO: Minutes (Medium Cost)?
│   │   └─→ **Warm Standby**
│   │       ├─→ Strategy: Scaled-down version always running
│   │       ├─→ RPO: Minutes (continuous replication)
│   │       ├─→ RTO: Minutes (scale up existing resources)
│   │       ├─→ Cost: $$$ (medium)
│   │       ├─→ Implementation:
│   │       │   ├─→ Database: Aurora Global or RDS cross-region replica
│   │       │   ├─→ Compute: Minimal EC2 fleet always running
│   │       │   ├─→ Auto Scaling: Min > 0 (always running)
│   │       │   ├─→ Load Balancer: Active in DR region
│   │       │   └─→ Route 53: Weighted or failover routing
│   │       └─→ Use case: Business-critical, faster recovery needed
│   │
│   └─→ RPO: Near-zero, RTO: Near-zero (Highest Cost)?
│       └─→ **Multi-Site Active-Active**
│           ├─→ Strategy: Full capacity in multiple regions
│           ├─→ RPO: Near-zero (synchronous or near-sync replication)
│           ├─→ RTO: Near-zero (instant failover)
│           ├─→ Cost: $$$$ (highest)
│           ├─→ Implementation:
│           │   ├─→ Database: Aurora Global (active-active) or DynamoDB Global Tables
│           │   ├─→ Compute: Full fleet in both regions
│           │   ├─→ Traffic: Route 53 latency-based or weighted
│           │   ├─→ Data: S3 Cross-Region Replication
│           │   └─→ State: Distributed (DynamoDB Global Tables)
│           └─→ Use case: Mission-critical, zero downtime requirement
│
├─→ DR Strategy Comparison Matrix
│   │
│   │ Strategy        │ RPO          │ RTO          │ Cost │
│   │─────────────────│──────────────│──────────────│──────│
│   │ Backup/Restore  │ Hours-Days   │ Hours-Days   │ $    │
│   │ Pilot Light     │ Minutes-Hrs  │ Hours        │ $$   │
│   │ Warm Standby    │ Minutes      │ Minutes      │ $$$  │
│   │ Multi-Site      │ Near-zero    │ Near-zero    │ $$$$ │
│
└─→ DR Implementation Decision Tree
    │
    ├─→ Database DR?
    │   ├─→ RDS → Cross-region read replica (promote on failover)
    │   ├─→ Aurora → Global Database (< 1 sec replication)
    │   ├─→ DynamoDB → Global Tables (active-active)
    │   └─→ Redshift → Cross-region snapshots
    │
    ├─→ Compute DR?
    │   ├─→ EC2 → AMIs copied to DR region, launch on failover
    │   ├─→ Lambda → Deploy to multiple regions
    │   └─→ Containers → ECR replication, deploy to DR cluster
    │
    ├─→ Storage DR?
    │   ├─→ S3 → Cross-Region Replication (CRR)
    │   ├─→ EBS → Snapshot copy to DR region
    │   └─→ EFS → DataSync for cross-region
    │
    └─→ Networking DR?
        ├─→ Route 53 → Failover routing policy
        ├─→ CloudFront → Multi-origin with failover
        └─→ Global Accelerator → Endpoint groups in multiple regions
```

---

## Multi-AZ vs Multi-Region

```
START: Multi-AZ or Multi-Region?
│
├─→ What problem are you solving?
│   │
│   ├─→ High Availability (survive AZ failure)?
│   │   └─→ **Multi-AZ** (within single region)
│   │       ├─→ Protection: AZ failure, hardware failure
│   │       ├─→ Latency: Minimal (< 2ms between AZs)
│   │       ├─→ Data replication: Synchronous
│   │       ├─→ Cost: Moderate (2x resources)
│   │       ├─→ Implementation:
│   │       │   ├─→ EC2: Auto Scaling across AZs
│   │       │   ├─→ RDS: Multi-AZ deployment
│   │       │   ├─→ ELB: Cross-AZ load balancing
│   │       │   └─→ ElastiCache: Multi-AZ replication
│   │       └─→ Use case: Standard HA requirement
│   │
│   ├─→ Disaster Recovery (survive region failure)?
│   │   └─→ **Multi-Region** (across regions)
│   │       ├─→ Protection: Region failure, geographic disasters
│   │       ├─→ Latency: Higher (50-100ms+ between regions)
│   │       ├─→ Data replication: Usually asynchronous
│   │       ├─→ Cost: Higher (duplicate infrastructure)
│   │       ├─→ Implementation:
│   │       │   ├─→ Route 53: Failover routing
│   │       │   ├─→ Aurora Global: Cross-region replication
│   │       │   ├─→ DynamoDB: Global Tables
│   │       │   ├─→ S3: Cross-Region Replication
│   │       │   └─→ CloudFront: Global distribution
│   │       └─→ Use case: Mission-critical, compliance
│   │
│   ├─→ Global Users (low latency worldwide)?
│   │   └─→ **Multi-Region Active-Active**
│   │       ├─→ Strategy: Serve users from nearest region
│   │       ├─→ Routing: Route 53 latency-based routing
│   │       ├─→ Data: DynamoDB Global Tables (active-active)
│   │       ├─→ Content: CloudFront (edge caching)
│   │       └─→ Use case: Global applications, gaming
│   │
│   └─→ Data Sovereignty (data in specific region)?
│       └─→ **Region-Specific** (data stays in region)
│           ├─→ Strategy: Keep data in required region
│           ├─→ Cross-region DR: May be prohibited
│           ├─→ Compliance: GDPR, data residency laws
│           └─→ Use case: Healthcare, government, financial
│
├─→ Multi-AZ vs Multi-Region Comparison
│   │
│   │ Factor              │ Multi-AZ          │ Multi-Region      │
│   │─────────────────────│───────────────────│───────────────────│
│   │ Protection          │ AZ failure        │ Region failure    │
│   │ Replication         │ Synchronous       │ Asynchronous      │
│   │ Latency             │ < 2ms             │ 50-100ms+         │
│   │ Data consistency    │ Strong            │ Eventually        │
│   │ Cost                │ 2x                │ 3x+               │
│   │ Failover time       │ Seconds-Minutes   │ Minutes-Hours     │
│   │ Complexity          │ Low               │ High              │
│
└─→ When to use what?
    │
    ├─→ Standard web app, HA requirement
    │   └─→ **Multi-AZ only** (cost-effective)
    │
    ├─→ Critical app, must survive region failure
    │   └─→ **Multi-AZ + Multi-Region** (comprehensive)
    │
    ├─→ Global users, low latency needed
    │   └─→ **Multi-Region Active-Active** (performance)
    │
    └─→ Compliance requires specific region
        └─→ **Single Region, Multi-AZ** (compliance)
```

---

## Backup & Recovery

```
START: Need backup solution?
│
├─→ Centralized backup management?
│   └─→ **AWS Backup**
│       ├─→ Centralized backup across services
│       ├─→ Supported services:
│       │   ├─→ EC2 (EBS), EFS, FSx
│       │   ├─→ RDS, Aurora, DynamoDB
│       │   ├─→ Storage Gateway, Neptune, DocumentDB
│       │   └─→ S3 (backup support)
│       ├─→ Features:
│       │   ├─→ Backup plans (automated schedules)
│       │   ├─→ Backup vaults (storage)
│       │   ├─→ Cross-region backup
│       │   ├─→ Cross-account backup
│       │   ├─→ Lifecycle policies
│       │   └─→ Compliance reports
│       └─→ Use case: Enterprise backup management
│
├─→ Service-specific backup?
│   │
│   ├─→ EC2/EBS?
│   │   └─→ **EBS Snapshots**
│   │       ├─→ Incremental snapshots to S3
│   │       ├─→ Copy to other regions
│   │       ├─→ Create AMIs from snapshots
│   │       ├─→ Automate with Data Lifecycle Manager (DLM)
│   │       └─→ Fast Snapshot Restore (FSR) for low RTO
│   │
│   ├─→ RDS/Aurora?
│   │   └─→ **RDS Automated Backups**
│   │       ├─→ Automatic daily backups
│   │       ├─→ Retention: 0-35 days
│   │       ├─→ Point-in-time recovery (5-min granularity)
│   │       ├─→ Manual snapshots (retained indefinitely)
│   │       └─→ Aurora: Continuous backup to S3
│   │
│   ├─→ DynamoDB?
│   │   └─→ **DynamoDB Backup**
│   │       ├─→ On-demand backups (manual)
│   │       ├─→ Point-in-time recovery (35 days, 1-sec granularity)
│   │       ├─→ No performance impact
│   │       └─→ Cross-region: Export to S3 + import
│   │
│   ├─→ S3?
│   │   └─→ **S3 Protection**
│   │       ├─→ Versioning: Keep all versions
│   │       ├─→ Cross-Region Replication (CRR)
│   │       ├─→ Same-Region Replication (SRR)
│   │       ├─→ Object Lock: Prevent deletion (WORM)
│   │       └─→ MFA Delete: Extra protection
│   │
│   ├─→ EFS?
│   │   └─→ **EFS Backup**
│   │       ├─→ AWS Backup integration
│   │       ├─→ Automatic backups
│   │       └─→ Cross-region: DataSync
│   │
│   └─→ Redshift?
│       └─→ **Redshift Snapshots**
│           ├─→ Automated snapshots (every 8 hours or 5 GB)
│           ├─→ Manual snapshots
│           ├─→ Copy to other regions
│           └─→ Restore to new cluster
│
├─→ Backup Strategy by RPO
│   │
│   ├─→ RPO: Days?
│   │   └─→ Daily automated backups (RDS, EBS DLM)
│   │
│   ├─→ RPO: Hours?
│   │   └─→ Frequent snapshots + cross-region copy
│   │
│   ├─→ RPO: Minutes?
│   │   └─→ Aurora Global, DynamoDB PITR, S3 CRR
│   │
│   └─→ RPO: Seconds?
│       └─→ Synchronous replication (Multi-AZ, Aurora Global)
│
└─→ Recovery Decision Tree
    │
    ├─→ Need to recover deleted data?
    │   ├─→ S3: Restore from versioned object
    │   ├─→ RDS: Point-in-time recovery
    │   ├─→ DynamoDB: Point-in-time recovery
    │   └─→ EBS: Restore from snapshot
    │
    ├─→ Need to recover corrupted data?
    │   ├─→ RDS: Point-in-time recovery to specific time
    │   ├─→ S3: Restore previous version
    │   └─→ Aurora: Backtrack (rewind database)
    │
    └─→ Need to recover entire environment?
        ├─→ CloudFormation: Redeploy stack
        ├─→ Infrastructure: Terraform/CDK
        └─→ Data: Restore from AWS Backup
```

---

## Fault Tolerance Patterns

```
START: Building fault-tolerant system?
│
├─→ Pattern: Redundancy
│   │
│   ├─→ Compute Redundancy?
│   │   └─→ **Auto Scaling Groups**
│   │       ├─→ Multiple instances across AZs
│   │       ├─→ Replace unhealthy instances automatically
│   │       ├─→ Maintain desired capacity
│   │       └─→ Scale based on demand
│   │
│   ├─→ Database Redundancy?
│   │   └─→ **Multi-AZ Deployments**
│   │       ├─→ RDS: Synchronous standby
│   │       ├─→ Aurora: 6 copies across 3 AZs
│   │       └─→ DynamoDB: 3 copies across AZs
│   │
│   └─→ Network Redundancy?
│       └─→ **Multiple Paths**
│           ├─→ Multiple NAT Gateways (one per AZ)
│           ├─→ Multiple VPN tunnels
│           ├─→ Direct Connect + VPN backup
│           └─→ Multiple ISPs
│
├─→ Pattern: Health Checks
│   │
│   ├─→ EC2 Health Checks?
│   │   ├─→ Instance status checks
│   │   ├─→ System status checks
│   │   └─→ Auto Scaling: Replace failed instances
│   │
│   ├─→ Load Balancer Health Checks?
│   │   ├─→ HTTP/HTTPS health checks
│   │   ├─→ Custom health check endpoints
│   │   ├─→ Remove unhealthy targets
│   │   └─→ Connection draining
│   │
│   └─→ Route 53 Health Checks?
│       ├─→ Endpoint health checks
│       ├─→ Calculated health checks (combine)
│       ├─→ CloudWatch alarm health checks
│       └─→ Failover routing
│
├─→ Pattern: Loose Coupling
│   │
│   ├─→ Synchronous → Asynchronous?
│   │   └─→ **Decouple with Queues**
│   │       ├─→ SQS between components
│   │       ├─→ Components fail independently
│   │       ├─→ Buffer during spikes
│   │       └─→ Retry failed operations
│   │
│   └─→ Event-Driven Architecture?
│       └─→ **EventBridge + Lambda**
│           ├─→ Components react to events
│           ├─→ No direct dependencies
│           └─→ Scalable and resilient
│
├─→ Pattern: Graceful Degradation
│   │
│   ├─→ Cache for Resilience?
│   │   └─→ **ElastiCache/CloudFront**
│   │       ├─→ Serve cached content if backend fails
│   │       ├─→ Reduce load on database
│   │       └─→ Improve response time
│   │
│   └─→ Circuit Breaker?
│       └─→ **Lambda/App Logic**
│           ├─→ Fail fast when dependency down
│           ├─→ Prevent cascade failures
│           └─→ Return cached/default response
│
├─→ Pattern: Stateless Design
│   │
│   └─→ Externalize State?
│       ├─→ Sessions → ElastiCache/DynamoDB
│       ├─→ Files → S3/EFS
│       ├─→ Configuration → Parameter Store
│       └─→ Secrets → Secrets Manager
│       └─→ **Stateless = Easy to scale and replace**
│
└─→ Pattern: Self-Healing
    │
    ├─→ Auto Recovery?
    │   ├─→ EC2 Auto Recovery (CloudWatch alarm)
    │   ├─→ Auto Scaling replacement
    │   └─→ ECS service auto-recovery
    │
    └─→ Automated Remediation?
        ├─→ Systems Manager Automation
        ├─→ Lambda + EventBridge
        └─→ Config Rules + Auto Remediation
```

---

## Exam Scenarios - Quick Answers

### Scenario 1: Web app must survive AZ failure
**Answer**: Multi-AZ deployment: ALB + Auto Scaling (multi-AZ) + RDS Multi-AZ

### Scenario 2: RPO < 1 hour, RTO < 4 hours, cost-effective
**Answer**: Pilot Light DR strategy

### Scenario 3: Critical app, near-zero downtime requirement
**Answer**: Multi-Site Active-Active with Aurora Global Database

### Scenario 4: Global users, need low latency worldwide
**Answer**: Multi-Region with Route 53 latency-based routing + CloudFront

### Scenario 5: Centralized backup across all AWS services
**Answer**: AWS Backup

### Scenario 6: Recover database to specific point in time
**Answer**: RDS/Aurora Point-in-Time Recovery

### Scenario 7: Protect S3 objects from accidental deletion
**Answer**: S3 Versioning + MFA Delete + Object Lock

### Scenario 8: Application needs to survive region failure
**Answer**: Multi-Region with Route 53 failover + Aurora Global Database

### Scenario 9: Decouple components for fault tolerance
**Answer**: SQS between components (loose coupling)

### Scenario 10: Automatically replace failed EC2 instances
**Answer**: Auto Scaling Group with health checks

---

## 🎯 Key Takeaways

### High Availability:
- **Multi-AZ** = Survive AZ failure (standard HA)
- **Multi-Region** = Survive region failure (DR)
- **Serverless** = Built-in HA (Lambda, DynamoDB, S3)

### Disaster Recovery Strategies (Cost vs Recovery):
- **Backup & Restore**: $ | RPO/RTO: Hours-Days
- **Pilot Light**: $$ | RPO: Minutes-Hours, RTO: Hours
- **Warm Standby**: $$$ | RPO/RTO: Minutes
- **Multi-Site**: $$$$ | RPO/RTO: Near-zero

### Fault Tolerance Best Practices:
1. Design for failure (assume everything fails)
2. Use multiple AZs (minimum 2, prefer 3)
3. Implement health checks at all levels
4. Use loose coupling (SQS, SNS, EventBridge)
5. Design stateless applications
6. Automate recovery (Auto Scaling, self-healing)
7. Test DR procedures regularly

### Quick RPO/RTO Reference:
- **RPO** = Recovery Point Objective (data loss tolerance)
- **RTO** = Recovery Time Objective (downtime tolerance)
- Lower RPO/RTO = Higher cost
- Choose strategy based on business requirements

---

*Use these decision trees for HA/DR architecture questions on the exam!*
