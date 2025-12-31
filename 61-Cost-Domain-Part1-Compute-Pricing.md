# AWS SAA-C03: DESIGN COST-OPTIMIZED ARCHITECTURES - PART 1

## 📋 COMPUTE PRICING & COST OPTIMIZATION

---

# 💰 DOMAIN OVERVIEW

## Design Cost-Optimized Architectures (20% of Exam)

### Focus Areas
1. **Cost-effective storage solutions**
2. **Cost-effective compute and database services**
3. **Cost-effective network architectures**

### Key Principle
> **Choose the MOST cost-effective solution that MEETS the requirements**
> - Don't over-provision
> - Don't under-provision (causing performance issues)
> - Consider operational overhead costs

---

# 🖥️ EC2 PRICING MODELS

## Complete Pricing Model Reference

### Definition
**EC2 Pricing Models** determine how you pay for compute capacity. Choosing the right model can reduce costs by up to 90%.

### Pricing Model Comparison

| Model | Discount | Commitment | Best For |
|-------|----------|------------|----------|
| **On-Demand** | 0% | None | Unpredictable, short-term, spiky |
| **Reserved (Standard)** | Up to 72% | 1 or 3 years | Steady-state, predictable |
| **Reserved (Convertible)** | Up to 66% | 1 or 3 years | Steady-state, may change type |
| **Savings Plans (Compute)** | Up to 66% | 1 or 3 years | Flexible compute (EC2, Lambda, Fargate) |
| **Savings Plans (EC2)** | Up to 72% | 1 or 3 years | Specific EC2 family/region |
| **Spot Instances** | Up to 90% | None (can be interrupted) | Fault-tolerant, flexible |
| **Dedicated Hosts** | Varies | On-Demand or Reserved | Licensing, compliance |
| **Dedicated Instances** | ~2% premium | None | Compliance, isolation |

### EC2 Pricing Decision Tree

```
┌─────────────────────────────────────────────────────────────────────┐
│                    EC2 PRICING DECISION TREE                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   Is workload INTERRUPTIBLE?                                         │
│       │                                                              │
│       ├── YES ──▶ Can handle 2-minute warning?                      │
│       │              │                                               │
│       │              ├── YES ──▶ SPOT INSTANCES (up to 90% off)     │
│       │              │                                               │
│       │              └── NO ───▶ Consider ON-DEMAND                 │
│       │                                                              │
│       └── NO ───▶ Is workload STEADY-STATE (24/7)?                  │
│                      │                                               │
│                      ├── YES ──▶ Do you know exact instance type?   │
│                      │              │                                │
│                      │              ├── YES ──▶ Need flexibility?   │
│                      │              │              │                 │
│                      │              │              ├── NO ──▶        │
│                      │              │              │   RESERVED      │
│                      │              │              │   STANDARD      │
│                      │              │              │   (72% off)     │
│                      │              │              │                 │
│                      │              │              └── YES ─▶        │
│                      │              │                  RESERVED      │
│                      │              │                  CONVERTIBLE   │
│                      │              │                  (66% off)     │
│                      │              │                                │
│                      │              └── NO ───▶ SAVINGS PLANS       │
│                      │                          (Compute: 66%)       │
│                      │                          (EC2: 72%)           │
│                      │                                               │
│                      └── NO ───▶ ON-DEMAND                          │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## On-Demand Instances

### Definition
**On-Demand Instances** let you pay for compute capacity by the hour or second with no long-term commitments.

### Key Characteristics

| Feature | Details |
|---------|---------|
| **Billing** | Per second (Linux), per hour (Windows/RHEL) |
| **Minimum** | 60 seconds |
| **Commitment** | None |
| **Availability** | Always available |
| **Use Case** | Short-term, spiky, unpredictable |

### When to Use On-Demand

```
✓ Development and testing
✓ Applications with unpredictable workloads
✓ Short-term projects
✓ First-time workloads (before understanding patterns)
✓ Applications that cannot be interrupted

✗ NOT for steady-state workloads (use Reserved/Savings Plans)
✗ NOT for fault-tolerant batch jobs (use Spot)
```

---

## Reserved Instances

### Definition
**Reserved Instances (RIs)** provide a significant discount (up to 72%) compared to On-Demand pricing in exchange for a 1 or 3-year commitment.

### Reserved Instance Types

| Type | Discount | Flexibility | Marketplace |
|------|----------|-------------|-------------|
| **Standard RI** | Up to 72% | Cannot change instance family | Can sell |
| **Convertible RI** | Up to 66% | Can change instance family, OS, tenancy | Cannot sell |

### Payment Options

| Payment | Discount | Cash Flow |
|---------|----------|-----------|
| **All Upfront** | Highest (~72%) | Pay all now |
| **Partial Upfront** | Medium (~66%) | Pay some now, rest monthly |
| **No Upfront** | Lowest (~54%) | Pay monthly |

### RI Scope

| Scope | Description | Flexibility |
|-------|-------------|-------------|
| **Regional** | Applies to any AZ in region | More flexible |
| **Zonal** | Applies to specific AZ | Capacity reservation |

### Reserved Instance Attributes

```
┌─────────────────────────────────────────────────────────────────────┐
│                    RESERVED INSTANCE ATTRIBUTES                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   STANDARD RI - CANNOT CHANGE:                                       │
│   • Instance family (m5 → c5 ✗)                                     │
│   • Operating system                                                 │
│   • Tenancy                                                          │
│                                                                      │
│   STANDARD RI - CAN CHANGE:                                          │
│   • Availability Zone (within region)                               │
│   • Instance size (within same family)                              │
│   • Network type (EC2-Classic → VPC)                                │
│                                                                      │
│   CONVERTIBLE RI - CAN CHANGE:                                       │
│   • Instance family (m5 → c5 ✓)                                     │
│   • Instance size                                                    │
│   • Operating system                                                 │
│   • Tenancy                                                          │
│   • Payment option                                                   │
│   (Must be equal or greater value)                                  │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Exam Heuristics - Reserved Instances

| Keyword/Scenario | Answer |
|------------------|--------|
| Steady-state, predictable workload | **Reserved Instances** |
| 1-3 year commitment, maximum savings | **Standard RI, All Upfront, 3-year** |
| Need to change instance family later | **Convertible RI** |
| Sell unused capacity | **Standard RI (Marketplace)** |
| Capacity guaranteed in specific AZ | **Zonal RI** |

---

## Savings Plans

### Definition
**Savings Plans** offer significant savings (up to 72%) over On-Demand in exchange for a commitment to a consistent amount of usage ($/hour) for 1 or 3 years.

### Savings Plan Types

| Type | Discount | Applies To | Flexibility |
|------|----------|------------|-------------|
| **Compute Savings Plans** | Up to 66% | EC2, Fargate, Lambda | Any region, family, size, OS, tenancy |
| **EC2 Instance Savings Plans** | Up to 72% | EC2 only | Specific family + region |
| **SageMaker Savings Plans** | Up to 64% | SageMaker | ML workloads |

### Savings Plans vs Reserved Instances

```
┌─────────────────────────────────────────────────────────────────────┐
│              SAVINGS PLANS vs RESERVED INSTANCES                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   SAVINGS PLANS:                                                     │
│   ✓ Commit to $/hour (e.g., $10/hour)                               │
│   ✓ Automatically applies to any matching usage                     │
│   ✓ Compute SP: Works across EC2, Lambda, Fargate                   │
│   ✓ No capacity reservation                                         │
│   ✓ Simpler to manage                                               │
│                                                                      │
│   RESERVED INSTANCES:                                                │
│   ✓ Commit to specific instance configuration                       │
│   ✓ Zonal RIs provide capacity reservation                          │
│   ✓ Can sell unused on Marketplace (Standard only)                  │
│   ✓ Slightly higher discount potential                              │
│                                                                      │
│   RECOMMENDATION:                                                    │
│   • New to commitments → Start with Compute Savings Plans           │
│   • Need capacity reservation → Zonal Reserved Instances            │
│   • Maximum savings, known config → Standard RI                     │
│   • Multi-service (EC2 + Lambda + Fargate) → Compute Savings Plans  │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Exam Heuristics - Savings Plans

| Keyword/Scenario | Answer |
|------------------|--------|
| Flexible compute commitment | **Compute Savings Plans** |
| EC2 + Lambda + Fargate savings | **Compute Savings Plans** |
| Specific EC2 family, max savings | **EC2 Instance Savings Plans** |
| Don't know exact instance needs | **Compute Savings Plans** |
| Simpler than RIs | **Savings Plans** |

---

## Spot Instances

### Definition
**Spot Instances** let you use spare EC2 capacity at up to 90% discount compared to On-Demand prices, but can be interrupted with 2-minute warning.

### Key Characteristics

| Feature | Details |
|---------|---------|
| **Discount** | Up to 90% off On-Demand |
| **Interruption** | 2-minute warning before termination |
| **Pricing** | Varies based on supply/demand |
| **Availability** | Not guaranteed |

### Spot Instance Strategies

```
┌─────────────────────────────────────────────────────────────────────┐
│                    SPOT INSTANCE STRATEGIES                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   1. SPOT FLEET:                                                     │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │   Define target capacity (instances or vCPUs)                │   │
│   │   Specify multiple instance types and AZs                    │   │
│   │   Fleet automatically launches lowest-price combination      │   │
│   │                                                              │   │
│   │   Allocation Strategies:                                     │   │
│   │   • lowestPrice - Launch lowest price pools                 │   │
│   │   • diversified - Spread across pools (recommended)         │   │
│   │   • capacityOptimized - Highest available capacity          │   │
│   │   • priceCapacityOptimized - Balance price + capacity       │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│   2. EC2 AUTO SCALING WITH MIXED INSTANCES:                          │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │   Auto Scaling Group with:                                   │   │
│   │   • On-Demand base capacity (reliable baseline)             │   │
│   │   • Spot instances for scaling (cost-effective)             │   │
│   │                                                              │   │
│   │   Example:                                                   │   │
│   │   - Min: 2 On-Demand (baseline)                             │   │
│   │   - Desired: 10 (2 On-Demand + 8 Spot)                      │   │
│   │   - Max: 20 (2 On-Demand + 18 Spot)                         │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│   3. SPOT BLOCKS (DEPRECATED):                                       │
│   • Previously allowed 1-6 hour uninterrupted blocks                │
│   • No longer available for new requests                            │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Spot Instance Use Cases

```
✓ GOOD FOR SPOT:
  • Big data analytics (EMR, Spark)
  • Batch processing jobs
  • CI/CD build servers
  • Web servers behind load balancers (with multiple instances)
  • Image/video rendering
  • Machine learning training
  • High-performance computing (HPC)
  • Containerized workloads
  • Testing environments

✗ NOT GOOD FOR SPOT:
  • Databases (data consistency)
  • Single-instance applications
  • Workloads that cannot checkpoint
  • Time-sensitive jobs with strict deadlines
```

### Spot Interruption Handling

| Action | Description |
|--------|-------------|
| **Terminate** | Instance terminated (default) |
| **Stop** | Instance stopped (EBS-backed only) |
| **Hibernate** | Instance hibernated (resume later) |

### Exam Heuristics - Spot Instances

| Keyword/Scenario | Answer |
|------------------|--------|
| Maximum cost savings, fault-tolerant | **Spot Instances** |
| Batch processing, interruptible | **Spot Instances** |
| Big data analytics | **Spot Instances (EMR)** |
| CI/CD builds | **Spot Instances** |
| Mixed On-Demand + Spot | **Auto Scaling Mixed Instances** |
| Spread across capacity pools | **Spot Fleet (diversified)** |

---

## Dedicated Options

### Dedicated Hosts vs Dedicated Instances

| Feature | Dedicated Hosts | Dedicated Instances |
|---------|-----------------|---------------------|
| **Definition** | Physical server dedicated to you | Instance on dedicated hardware |
| **Visibility** | See sockets, cores, host ID | No visibility |
| **Licensing** | BYOL (per-socket, per-core) | No BYOL |
| **Pricing** | Per host | Per instance + ~$2/hour/region |
| **Affinity** | Instance placement control | None |
| **Use Case** | BYOL, compliance | Compliance only |

### When to Use Dedicated

```
DEDICATED HOSTS:
✓ Bring Your Own License (BYOL) - Windows Server, SQL Server, Oracle
✓ Regulatory requirements needing physical server
✓ Need to track sockets/cores for licensing
✓ Consistent server placement

DEDICATED INSTANCES:
✓ Compliance requiring hardware isolation
✓ Don't need BYOL or visibility
✓ Simpler than Dedicated Hosts
```

---

# 📊 COMPUTE OPTIMIZER

## Definition
**AWS Compute Optimizer** analyzes configuration and utilization metrics to recommend optimal AWS resources to reduce costs and improve performance.

### Supported Resources

| Resource | Recommendations |
|----------|-----------------|
| **EC2** | Instance type, size |
| **Auto Scaling Groups** | Instance type configuration |
| **EBS Volumes** | Volume type, size, IOPS |
| **Lambda** | Memory size |
| **ECS on Fargate** | CPU and memory |

### How It Works

```
┌─────────────────────────────────────────────────────────────────────┐
│                    COMPUTE OPTIMIZER FLOW                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   1. COLLECT                                                         │
│      │                                                               │
│      ▼                                                               │
│   CloudWatch Metrics (14 days minimum)                               │
│   • CPU utilization                                                  │
│   • Memory utilization (requires agent)                              │
│   • Network I/O                                                      │
│   • Disk I/O                                                         │
│      │                                                               │
│   2. ANALYZE                                                         │
│      │                                                               │
│      ▼                                                               │
│   Machine Learning analysis of usage patterns                        │
│      │                                                               │
│   3. RECOMMEND                                                       │
│      │                                                               │
│      ▼                                                               │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │   RECOMMENDATIONS:                                           │   │
│   │                                                              │   │
│   │   Current: m5.xlarge ($0.192/hr)                            │   │
│   │                                                              │   │
│   │   Recommended Options:                                       │   │
│   │   1. m5.large  - Savings: $50/month (Under-provisioned)     │   │
│   │   2. m5a.large - Savings: $55/month (AMD alternative)       │   │
│   │   3. t3.large  - Savings: $60/month (Burstable)             │   │
│   │                                                              │   │
│   │   Risk Level: Low | Medium | High                           │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Exam Heuristics - Compute Optimizer

| Keyword/Scenario | Answer |
|------------------|--------|
| Right-size EC2 instances | **Compute Optimizer** |
| Optimize Lambda memory | **Compute Optimizer** |
| EBS volume recommendations | **Compute Optimizer** |
| ML-based resource analysis | **Compute Optimizer** |
| Reduce over-provisioning | **Compute Optimizer** |

---

# ⚡ SERVERLESS COST OPTIMIZATION

## Lambda Pricing

### Definition
**AWS Lambda** pricing is based on the number of requests and duration of code execution.

### Pricing Components

| Component | Pricing |
|-----------|---------|
| **Requests** | $0.20 per 1M requests |
| **Duration** | $0.0000166667 per GB-second |
| **Free Tier** | 1M requests + 400,000 GB-seconds/month |

### Lambda Cost Optimization

```
┌─────────────────────────────────────────────────────────────────────┐
│                    LAMBDA COST OPTIMIZATION                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   1. RIGHT-SIZE MEMORY:                                              │
│      • More memory = more CPU = faster execution                    │
│      • Sometimes higher memory = LOWER cost (faster completion)     │
│      • Use AWS Lambda Power Tuning tool                             │
│                                                                      │
│   2. OPTIMIZE CODE:                                                  │
│      • Minimize cold starts                                         │
│      • Use connection pooling                                       │
│      • Avoid unnecessary SDK initializations                        │
│                                                                      │
│   3. PROVISIONED CONCURRENCY:                                        │
│      • Reduces cold starts but ADDS cost                            │
│      • Use only if cold starts impact user experience               │
│      • Use Auto Scaling for provisioned concurrency                 │
│                                                                      │
│   4. ARM-BASED (Graviton2):                                          │
│      • 20% cheaper than x86                                         │
│      • 34% better price-performance                                 │
│      • Use arm64 architecture                                       │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

## Fargate Pricing

### Pricing Model

| Component | Pricing (approx) |
|-----------|------------------|
| **vCPU** | $0.04048 per vCPU per hour |
| **Memory** | $0.004445 per GB per hour |
| **Spot** | Up to 70% off Fargate On-Demand |

### Fargate Cost Optimization

| Strategy | Savings |
|----------|---------|
| **Fargate Spot** | Up to 70% off |
| **Right-size tasks** | Variable |
| **ARM64 (Graviton)** | ~20% cheaper |
| **Compute Savings Plans** | Up to 50% |

---

# ✅ COMPUTE COST HEURISTICS

## Instant Answer Table

| Keyword/Scenario | Answer |
|------------------|--------|
| Steady-state, 24/7 workload | **Reserved Instances or Savings Plans** |
| Unpredictable workload | **On-Demand** |
| Fault-tolerant, interruptible | **Spot Instances** |
| Flexible commitment (EC2, Lambda, Fargate) | **Compute Savings Plans** |
| Maximum EC2 savings, known family | **Standard RI or EC2 Savings Plan** |
| Need to change instance family | **Convertible RI** |
| Batch processing | **Spot Instances** |
| Big data analytics (EMR) | **Spot Instances** |
| BYOL licensing | **Dedicated Hosts** |
| Right-sizing recommendations | **Compute Optimizer** |
| Lambda cost optimization | **Right-size memory, ARM64** |
| Fargate cost optimization | **Fargate Spot, Savings Plans** |

## Cost Optimization Rules

```
┌─────────────────────────────────────────────────────────────────────┐
│                    COMPUTE COST RULES                                │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   RULE 1: Match pricing model to workload pattern                   │
│   • Steady → Reserved/Savings Plans                                 │
│   • Variable → On-Demand                                            │
│   • Interruptible → Spot                                            │
│                                                                      │
│   RULE 2: Use Auto Scaling to match capacity to demand              │
│   • Don't pay for idle capacity                                     │
│   • Scale out during peaks, scale in during lows                    │
│                                                                      │
│   RULE 3: Right-size before committing                              │
│   • Use Compute Optimizer                                           │
│   • Analyze CloudWatch metrics                                      │
│   • Then buy Reserved Instances                                     │
│                                                                      │
│   RULE 4: Use Spot for fault-tolerant workloads                     │
│   • Up to 90% savings                                               │
│   • Must handle interruptions                                       │
│                                                                      │
│   RULE 5: Consider serverless                                       │
│   • Pay only for execution time                                     │
│   • No idle capacity costs                                          │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

*Continued in Part 2: Storage Cost Optimization*

---

*Last Updated: December 2025*
*Exam Version: SAA-C03*
*Domain: Design Cost-Optimized Architectures (20%)*
