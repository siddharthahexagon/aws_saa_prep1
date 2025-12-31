# AWS SAA-C03: Cost Optimization Decision Trees

## 🌳 Quick Navigation
- [EC2 Pricing Decision](#ec2-pricing-decision)
- [Storage Cost Optimization](#storage-cost-optimization)
- [Database Cost Optimization](#database-cost-optimization)
- [Data Transfer Cost Optimization](#data-transfer-cost-optimization)
- [Serverless Cost Optimization](#serverless-cost-optimization)
- [Cost Management Tools](#cost-management-tools)

---

## EC2 Pricing Decision

```
START: How to pay for EC2?
│
├─→ What's the workload pattern?
│   │
│   ├─→ Steady, predictable, 24/7?
│   │   └─→ **Reserved Instances or Savings Plans**
│   │       │
│   │       ├─→ Know exact instance type?
│   │       │   └─→ **EC2 Instance Savings Plans**
│   │       │       ├─→ Specific instance family (e.g., M5 in us-east-1)
│   │       │       ├─→ Flexible: Size, OS, tenancy
│   │       │       ├─→ Savings: Up to 72%
│   │       │       └─→ Commitment: 1 or 3 years
│   │       │
│   │       ├─→ Need flexibility across services?
│   │       │   └─→ **Compute Savings Plans**
│   │       │       ├─→ Any EC2, Fargate, Lambda
│   │       │       ├─→ Any region, family, size, OS
│   │       │       ├─→ Savings: Up to 66%
│   │       │       └─→ Commitment: $/hour for 1 or 3 years
│   │       │
│   │       └─→ Standard Reserved Instances?
│   │           └─→ **Standard RIs**
│   │               ├─→ Specific instance type
│   │               ├─→ Can sell on RI Marketplace
│   │               ├─→ Savings: 40-60%
│   │               └─→ Consider Savings Plans instead
│   │
│   ├─→ Variable/unpredictable, no commitment?
│   │   └─→ **On-Demand Instances**
│   │       ├─→ Pay per second (Linux) / per hour (Windows)
│   │       ├─→ No upfront commitment
│   │       ├─→ Most flexible, highest cost
│   │       └─→ Use case: Short-term, spiky, unpredictable
│   │
│   ├─→ Interruptible workload (fault-tolerant)?
│   │   └─→ **Spot Instances**
│   │       ├─→ Spare EC2 capacity at steep discount
│   │       ├─→ Savings: Up to 90%
│   │       ├─→ Can be interrupted with 2-min notice
│   │       ├─→ Strategies:
│   │       │   ├─→ Spot Fleet: Mix instance types/AZs
│   │       │   ├─→ Spot Block: 1-6 hours (being phased out)
│   │       │   └─→ Capacity Optimized: Lowest interruption
│   │       ├─→ Best for:
│   │       │   ├─→ Batch processing, big data
│   │       │   ├─→ CI/CD, testing
│   │       │   ├─→ Containerized workloads
│   │       │   └─→ Stateless web servers
│   │       └─→ **NOT for**: Critical databases, stateful apps
│   │
│   ├─→ Need dedicated hardware (compliance/licensing)?
│   │   ├─→ Need socket/core visibility (BYOL)?
│   │   │   └─→ **Dedicated Hosts**
│   │   │       ├─→ Physical server dedicated to you
│   │   │       ├─→ Visibility: Socket, cores, host ID
│   │   │       ├─→ Licensing: BYOL (Windows Server, SQL Server, etc.)
│   │   │       ├─→ Can be purchased On-Demand or Reserved
│   │   │       └─→ Use case: Licensing compliance
│   │   │
│   │   └─→ Just need isolated hardware?
│   │       └─→ **Dedicated Instances**
│   │           ├─→ Instances on dedicated hardware
│   │           ├─→ No socket/core visibility
│   │           ├─→ May share hardware with same account
│   │           └─→ Use case: Compliance, security
│   │
│   └─→ Need specific time windows?
│       └─→ **Scheduled Reserved Instances** (Legacy)
│           ├─→ Capacity reservation for recurring schedule
│           ├─→ Use Savings Plans + Auto Scaling instead
│           └─→ Being phased out
│
├─→ Cost Optimization Strategies
│   │
│   ├─→ Right-sizing?
│   │   └─→ **AWS Compute Optimizer**
│   │       ├─→ Analyze usage patterns
│   │       ├─→ Recommend optimal instance type
│   │       └─→ Use case: Eliminate over-provisioning
│   │
│   ├─→ Scheduled scaling?
│   │   └─→ **Auto Scaling Scheduled Actions**
│   │       ├─→ Scale down nights/weekends
│   │       ├─→ Scale up during business hours
│   │       └─→ Use case: Predictable traffic patterns
│   │
│   └─→ Stop unused instances?
│       └─→ **EC2 Instance Scheduler**
│           ├─→ Automatically start/stop instances
│           └─→ Use case: Dev/test environments
│
└─→ Pricing Model Comparison
    │
    │ Pricing Model        │ Discount    │ Commitment │ Flexibility │
    │──────────────────────│─────────────│────────────│─────────────│
    │ On-Demand            │ 0%          │ None       │ Highest     │
    │ Savings Plans        │ Up to 72%   │ 1-3 years  │ High        │
    │ Reserved Instances   │ Up to 72%   │ 1-3 years  │ Low         │
    │ Spot Instances       │ Up to 90%   │ None       │ Medium      │
    │ Dedicated Hosts      │ Varies      │ Optional   │ Low         │
```

---

## Storage Cost Optimization

```
START: Optimize storage costs?
│
├─→ S3 Cost Optimization
│   │
│   ├─→ Choose right storage class
│   │   │
│   │   ├─→ Frequently accessed?
│   │   │   └─→ **S3 Standard** (~$0.023/GB)
│   │   │
│   │   ├─→ Infrequently accessed?
│   │   │   ├─→ Need multi-AZ? → **S3 Standard-IA** (~$0.0125/GB)
│   │   │   └─→ Single AZ OK? → **S3 One Zone-IA** (~$0.01/GB)
│   │   │
│   │   ├─→ Archive (rarely accessed)?
│   │   │   ├─→ Need instant retrieval? → **Glacier Instant** (~$0.004/GB)
│   │   │   ├─→ Can wait minutes-hours? → **Glacier Flexible** (~$0.0036/GB)
│   │   │   └─→ Can wait 12+ hours? → **Glacier Deep Archive** (~$0.00099/GB)
│   │   │
│   │   └─→ Unknown access pattern?
│   │       └─→ **S3 Intelligent-Tiering**
│   │           ├─→ Auto-moves between tiers
│   │           ├─→ Small monitoring fee
│   │           └─→ No retrieval fees
│   │
│   ├─→ Lifecycle Policies
│   │   └─→ **S3 Lifecycle Rules**
│   │       ├─→ Transition to cheaper class (after X days)
│   │       ├─→ Expire objects (delete after X days)
│   │       ├─→ Delete incomplete multipart uploads
│   │       └─→ Example:
│   │           ├─→ Day 0-30: Standard
│   │           ├─→ Day 31-90: Standard-IA
│   │           ├─→ Day 91-365: Glacier
│   │           └─→ Day 366+: Delete or Deep Archive
│   │
│   ├─→ Reduce storage usage
│   │   ├─→ Enable versioning (but consider cost)
│   │   ├─→ Use lifecycle rules to expire old versions
│   │   ├─→ Delete incomplete multipart uploads
│   │   └─→ Use S3 Analytics to find optimization opportunities
│   │
│   └─→ S3 Storage Class Analysis
│       └─→ Identify objects to move to lower-cost tier
│
├─→ EBS Cost Optimization
│   │
│   ├─→ Choose right volume type
│   │   ├─→ General purpose: gp3 (cheaper than gp2)
│   │   ├─→ High IOPS: io2 (provision only needed IOPS)
│   │   ├─→ Throughput: st1 (cheaper for sequential)
│   │   └─→ Archive: sc1 (cheapest, infrequent access)
│   │
│   ├─→ Delete unused volumes
│   │   └─→ EBS volumes persist after EC2 termination
│   │       └─→ Regularly audit and delete orphaned volumes
│   │
│   ├─→ Snapshot optimization
│   │   ├─→ Incremental snapshots (only changed blocks)
│   │   ├─→ Delete old snapshots (use Data Lifecycle Manager)
│   │   ├─→ Use EBS Snapshots Archive for long-term
│   │   └─→ Copy to cheaper region if compliance allows
│   │
│   └─→ gp3 vs gp2 Decision
│       └─→ **Always prefer gp3**
│           ├─→ Cheaper baseline (3,000 IOPS free)
│           ├─→ Separate IOPS/throughput provisioning
│           └─→ Migrate gp2 → gp3 for instant savings
│
├─→ EFS Cost Optimization
│   │
│   ├─→ Storage classes
│   │   ├─→ Standard: Frequently accessed
│   │   └─→ Infrequent Access (IA): Lower cost
│   │
│   └─→ Enable lifecycle management
│       └─→ Auto-move files to IA after X days
│
└─→ Storage Cost Quick Reference
    │
    │ Service          │ Cheapest Option              │ Cost/GB/Month │
    │──────────────────│──────────────────────────────│───────────────│
    │ S3               │ Glacier Deep Archive         │ ~$0.00099     │
    │ EBS              │ sc1 (Cold HDD)               │ ~$0.015       │
    │ EFS              │ EFS IA                       │ ~$0.016       │
```

---

## Database Cost Optimization

```
START: Optimize database costs?
│
├─→ RDS Cost Optimization
│   │
│   ├─→ Reserved Instances
│   │   └─→ **RDS Reserved Instances**
│   │       ├─→ 1-year or 3-year commitment
│   │       ├─→ Up to 60% savings
│   │       └─→ Apply to: Instance, multi-AZ, engine
│   │
│   ├─→ Right-sizing
│   │   ├─→ Use Performance Insights to analyze
│   │   ├─→ Downsize over-provisioned instances
│   │   └─→ Consider Aurora Serverless for variable workloads
│   │
│   ├─→ Storage optimization
│   │   ├─→ gp3 instead of gp2 (cheaper)
│   │   ├─→ Provisioned IOPS only when needed
│   │   └─→ Delete old snapshots
│   │
│   └─→ Stop unused databases
│       └─→ RDS instances can be stopped for up to 7 days
│           └─→ Use for dev/test environments
│
├─→ Aurora Cost Optimization
│   │
│   ├─→ Aurora vs RDS
│   │   ├─→ Aurora: Higher hourly cost
│   │   ├─→ But: Better performance, may need smaller instance
│   │   └─→ Consider total cost (compute + storage + IOPS)
│   │
│   ├─→ Aurora Serverless v2
│   │   └─→ **Use for variable workloads**
│   │       ├─→ Scales automatically
│   │       ├─→ Pay per ACU-second
│   │       └─→ Can scale to zero (Serverless v1 only)
│   │
│   ├─→ Reserved Instances
│   │   └─→ Aurora supports RDS Reserved Instances
│   │
│   └─→ Read Replicas
│       └─→ Use replicas instead of scaling up primary
│
├─→ DynamoDB Cost Optimization
│   │
│   ├─→ Capacity Mode Selection
│   │   │
│   │   ├─→ Predictable workload?
│   │   │   └─→ **Provisioned Capacity**
│   │   │       ├─→ Lower cost per request
│   │   │       ├─→ Reserved Capacity: Additional savings
│   │   │       └─→ Enable Auto Scaling
│   │   │
│   │   └─→ Variable/unpredictable workload?
│   │       └─→ **On-Demand Capacity**
│   │           ├─→ Pay per request
│   │           ├─→ No idle capacity cost
│   │           └─→ Higher per-request cost
│   │
│   ├─→ Reserved Capacity
│   │   └─→ **DynamoDB Reserved Capacity**
│   │       ├─→ Commit to minimum throughput
│   │       ├─→ 1-3 year terms
│   │       └─→ Up to 77% savings
│   │
│   ├─→ Table Class
│   │   ├─→ Standard: Frequent access
│   │   └─→ **Standard-IA**: Infrequent access (60% cheaper storage)
│   │
│   └─→ TTL (Time to Live)
│       └─→ Auto-delete old items (free deletion)
│
├─→ ElastiCache Cost Optimization
│   │
│   ├─→ Reserved Nodes
│   │   └─→ Up to 55% savings
│   │
│   └─→ Right-sizing
│       └─→ Choose appropriate node type
│
└─→ Redshift Cost Optimization
    │
    ├─→ Reserved Nodes
    │   └─→ Up to 75% savings
    │
    ├─→ Pause/Resume
    │   └─→ Pause when not in use (no compute charges)
    │
    └─→ Redshift Serverless
        └─→ Pay per RPU-hour (variable workloads)
```

---

## Data Transfer Cost Optimization

```
START: Optimize data transfer costs?
│
├─→ Understanding Data Transfer Costs
│   │
│   ├─→ FREE:
│   │   ├─→ Data transfer IN (to AWS) from internet
│   │   ├─→ Data transfer within same AZ (via private IP)
│   │   ├─→ S3 to CloudFront
│   │   ├─→ S3 Transfer Acceleration upload
│   │   └─→ VPC endpoints (Gateway endpoints for S3/DynamoDB)
│   │
│   ├─→ PAID:
│   │   ├─→ Data transfer OUT to internet
│   │   ├─→ Data transfer between AZs
│   │   ├─→ Data transfer between regions
│   │   ├─→ NAT Gateway data processing
│   │   └─→ Interface VPC endpoints
│   │
│   └─→ Data Transfer Pricing (approximate):
│       ├─→ To internet: $0.09/GB (first 10 TB)
│       ├─→ Between AZs: $0.01/GB each way
│       ├─→ Between regions: $0.02/GB
│       └─→ NAT Gateway: $0.045/GB processed
│
├─→ Reduce Inter-AZ Transfer
│   │
│   ├─→ Keep traffic in same AZ
│   │   ├─→ Use AZ affinity for services
│   │   ├─→ Deploy read replicas in same AZ as app
│   │   └─→ Use private IP addresses
│   │
│   └─→ Single NAT Gateway strategy
│       └─→ Consider: Cost vs HA tradeoff
│           ├─→ One NAT per AZ: Higher HA, higher cost
│           └─→ One NAT total: Lower cost, single point of failure
│
├─→ Reduce Internet Transfer
│   │
│   ├─→ Use CloudFront
│   │   ├─→ Cache at edge (reduce origin traffic)
│   │   └─→ CloudFront to origin is free
│   │
│   ├─→ Compress data
│   │   ├─→ Enable gzip compression
│   │   └─→ Use efficient formats (Parquet, ORC)
│   │
│   └─→ Use AWS PrivateLink
│       └─→ Avoid NAT Gateway for AWS service access
│
├─→ Reduce Cross-Region Transfer
│   │
│   ├─→ Process data locally
│   │   └─→ Run analytics in same region as data
│   │
│   └─→ Replicate only what's needed
│       └─→ Filter S3 replication rules
│
├─→ Direct Connect Cost Savings
│   │
│   └─→ **Lower data transfer rates**
│       ├─→ Outbound: ~$0.02/GB (vs $0.09 internet)
│       └─→ Consider for high data volume
│
└─→ VPC Endpoint Strategy
    │
    ├─→ Gateway Endpoints (S3, DynamoDB)
    │   └─→ **FREE - Always use**
    │
    └─→ Interface Endpoints
        └─→ Cost: ~$0.01/hour + $0.01/GB
            └─→ Compare vs NAT Gateway cost
```

---

## Serverless Cost Optimization

```
START: Optimize serverless costs?
│
├─→ Lambda Cost Optimization
│   │
│   ├─→ Memory & Duration
│   │   ├─→ Right-size memory (affects CPU allocation)
│   │   ├─→ Use AWS Lambda Power Tuning
│   │   ├─→ Optimize code for faster execution
│   │   └─→ Consider: More memory = faster = sometimes cheaper
│   │
│   ├─→ Invocation Optimization
│   │   ├─→ Batch processing (fewer invocations)
│   │   ├─→ Use SQS batching
│   │   └─→ Combine small functions
│   │
│   ├─→ Provisioned Concurrency
│   │   └─→ Use only when cold starts are critical
│   │       └─→ Otherwise: Pay for idle capacity
│   │
│   ├─→ Architecture Choices
│   │   ├─→ Use Step Functions for orchestration
│   │   ├─→ Avoid Lambda for long-running tasks
│   │   └─→ Consider containers for high-volume steady workloads
│   │
│   └─→ Graviton2 (ARM)
│       └─→ **20% cheaper, 19% better performance**
│
├─→ API Gateway Cost Optimization
│   │
│   ├─→ Choose right API type
│   │   ├─→ HTTP API: $1.00/million (simpler, cheaper)
│   │   └─→ REST API: $3.50/million (more features)
│   │
│   ├─→ Enable caching
│   │   └─→ Reduce backend invocations
│   │
│   └─→ Request/response compression
│       └─→ Reduce data transfer
│
├─→ DynamoDB Serverless Optimization
│   │
│   └─→ See "DynamoDB Cost Optimization" section
│
└─→ S3 Serverless Optimization
    │
    └─→ See "Storage Cost Optimization" section
```

---

## Cost Management Tools

```
START: Which cost management tool?
│
├─→ Analyze past spending?
│   └─→ **AWS Cost Explorer**
│       ├─→ Visualize cost and usage
│       ├─→ Filter by service, account, tag
│       ├─→ 12-month history + 12-month forecast
│       ├─→ RI/Savings Plans recommendations
│       └─→ Use case: Cost analysis, trend identification
│
├─→ Set spending limits and alerts?
│   └─→ **AWS Budgets**
│       ├─→ Create cost, usage, RI, Savings Plans budgets
│       ├─→ Alert when threshold exceeded
│       ├─→ Automatic actions (e.g., stop EC2)
│       └─→ Use case: Cost control, proactive alerts
│
├─→ Detailed billing reports?
│   └─→ **AWS Cost and Usage Reports (CUR)**
│       ├─→ Most detailed cost data
│       ├─→ Hourly/daily granularity
│       ├─→ Resource-level detail
│       ├─→ Export to S3
│       └─→ Use case: Deep analysis, chargeback
│
├─→ Right-size resources?
│   └─→ **AWS Compute Optimizer**
│       ├─→ ML-based recommendations
│       ├─→ EC2, EBS, Lambda, Auto Scaling
│       └─→ Use case: Eliminate over-provisioning
│
├─→ Find Savings Plans opportunities?
│   └─→ **Savings Plans Recommendations**
│       └─→ In Cost Explorer
│
├─→ Allocate costs by team/project?
│   └─→ **Cost Allocation Tags**
│       ├─→ Tag resources with cost center, project
│       ├─→ AWS-generated tags (e.g., createdBy)
│       ├─→ User-defined tags
│       └─→ Use case: Chargeback, showback
│
├─→ Centralized billing?
│   └─→ **AWS Organizations - Consolidated Billing**
│       ├─→ Single bill for all accounts
│       ├─→ Volume discounts (aggregated usage)
│       ├─→ Share Reserved Instances/Savings Plans
│       └─→ Use case: Multi-account organizations
│
└─→ Check for best practices?
    └─→ **AWS Trusted Advisor**
        ├─→ Cost optimization recommendations
        ├─→ Idle resources, underutilized instances
        ├─→ RI optimization
        └─→ Use case: Quick wins, best practices
```

---

## Exam Scenarios - Quick Answers

### Scenario 1: Steady 24/7 web server workload, reduce costs
**Answer**: Reserved Instances or Savings Plans (1-3 year)

### Scenario 2: Batch processing, can handle interruptions
**Answer**: Spot Instances

### Scenario 3: Need BYOL (Bring Your Own License) for SQL Server
**Answer**: Dedicated Hosts

### Scenario 4: S3 objects rarely accessed after 90 days
**Answer**: S3 Lifecycle policy to transition to Glacier after 90 days

### Scenario 5: DynamoDB table with unpredictable traffic spikes
**Answer**: On-Demand capacity mode

### Scenario 6: Right-size EC2 instances
**Answer**: AWS Compute Optimizer

### Scenario 7: Track costs by department
**Answer**: Cost Allocation Tags + Cost Explorer

### Scenario 8: Alert when monthly spend exceeds $10,000
**Answer**: AWS Budgets with email notification

### Scenario 9: Analyze detailed hourly costs
**Answer**: AWS Cost and Usage Reports (CUR)

### Scenario 10: Reduce Lambda costs for steady high-volume
**Answer**: Consider containers (Fargate) or EC2 for constant high volume

### Scenario 11: Reduce NAT Gateway data transfer costs
**Answer**: VPC Gateway Endpoints for S3/DynamoDB (free)

### Scenario 12: Share Reserved Instances across accounts
**Answer**: AWS Organizations Consolidated Billing

---

## 🎯 Key Takeaways

### EC2 Pricing Priority:
1. **Spot** (90% savings): Interruptible workloads
2. **Savings Plans** (72% savings): Flexible commitment
3. **Reserved Instances** (72% savings): Specific instance
4. **On-Demand**: Short-term, unpredictable

### Storage Optimization:
- **S3**: Use lifecycle policies, choose right storage class
- **EBS**: Use gp3 (cheaper than gp2), delete orphaned volumes
- **Snapshots**: Use Data Lifecycle Manager, archive old snapshots

### Database Optimization:
- **RDS/Aurora**: Reserved Instances, right-sizing, stop when unused
- **DynamoDB**: Right capacity mode, Reserved Capacity, use TTL

### Data Transfer:
- **Free**: Inbound, same AZ, S3→CloudFront
- **Reduce**: Use VPC endpoints, CloudFront, compression

### Cost Management Tools:
- **Cost Explorer**: Analyze past costs
- **Budgets**: Set alerts and limits
- **Compute Optimizer**: Right-size recommendations
- **Trusted Advisor**: Best practice checks

---

*Use these decision trees for cost optimization questions on the exam!*
