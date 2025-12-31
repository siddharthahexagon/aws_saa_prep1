# AWS SAA-C03: Compute Services Decision Trees

## 🌳 Quick Navigation
- [Main Compute Decision Tree](#main-compute-decision-tree)
- [EC2 Instance Type Selection](#ec2-instance-type-selection)
- [EC2 Pricing Model Selection](#ec2-pricing-model-selection)
- [Container Service Selection](#container-service-selection)
- [Serverless vs Server-based](#serverless-vs-server-based)

---

## Main Compute Decision Tree

```
START: Need compute resources?
│
├─→ Execution time < 15 minutes?
│   ├─→ YES → Event-driven/stateless?
│   │   ├─→ YES → **AWS Lambda**
│   │   │         └─→ Sub-second billing, automatic scaling
│   │   └─→ NO → Need state management?
│   │             └─→ **Step Functions + Lambda**
│   │
│   └─→ NO → Continue below...
│
├─→ Need full OS control/custom software?
│   ├─→ YES → Long-running applications?
│   │   ├─→ YES → **EC2**
│   │   │         └─→ Go to "EC2 Instance Type Selection"
│   │   └─→ NO → Batch processing?
│   │             └─→ **AWS Batch**
│   │
│   └─→ NO → Continue below...
│
├─→ Using containers (Docker)?
│   ├─→ YES → Go to "Container Service Selection"
│   │
│   └─→ NO → Continue below...
│
├─→ Need quick deployment without infrastructure management?
│   ├─→ YES → Standard web app (Node, Python, Java, .NET)?
│   │   ├─→ YES → **Elastic Beanstalk**
│   │   │         └─→ Automatic scaling, monitoring, patching
│   │   └─→ NO → Static website only?
│   │             └─→ **S3 + CloudFront** or **Amplify**
│   │
│   └─→ NO → Continue below...
│
├─→ Simple WordPress/LAMP stack?
│   └─→ YES → **Lightsail**
│             └─→ Fixed pricing, easy setup
│
└─→ Complex multi-tier application?
    └─→ Mix of: **EC2 + Auto Scaling + ELB + Lambda**
```

---

## EC2 Instance Type Selection

```
START: Which EC2 instance family?
│
├─→ General purpose workload (balanced CPU/memory)?
│   └─→ **T-series** (burstable) or **M-series** (steady-state)
│       ├─→ Variable workload → T3/T3a/T4g (burstable credits)
│       └─→ Steady workload → M5/M6i/M6a/M7g
│
├─→ Compute-intensive (high CPU)?
│   └─→ **C-series** (Compute optimized)
│       ├─→ Examples: Batch processing, HPC, gaming servers
│       └─→ C5/C6i/C6a/C7g (3:1 or 2:1 CPU:memory ratio)
│
├─→ Memory-intensive (high RAM)?
│   └─→ **R-series** (RAM optimized) or **X-series** (extreme memory)
│       ├─→ Examples: Databases, in-memory caching, big data
│       ├─→ R5/R6i/R6a/R7g (1:8 or 1:12 CPU:memory ratio)
│       └─→ Need 3-4 TB RAM? → X1e/X2idn
│
├─→ Storage-intensive (high IOPS/throughput)?
│   └─→ **I-series** (IOPS optimized) or **D-series** (dense storage)
│       ├─→ Need NVMe SSD, low latency → I3/I3en/I4i
│       └─→ Need HDD, high throughput → D2/D3
│
├─→ GPU workloads (ML, graphics, video)?
│   └─→ **P-series** (ML training) or **G-series** (graphics/inference)
│       ├─→ ML training → P3/P4 (NVIDIA A100/V100)
│       ├─→ ML inference → Inf1/Inf2 (AWS Inferentia)
│       └─→ Graphics/video → G4dn/G5 (NVIDIA T4/A10G)
│
└─→ Network-intensive (high bandwidth)?
    └─→ Enhanced networking instances
        └─→ C5n, M5n, R5n (100 Gbps networking)
```

### Quick Instance Selection Matrix

| Workload | Instance Family | Example Types |
|----------|----------------|---------------|
| Web servers, dev/test | T3, M5 | t3.medium, m5.large |
| Batch processing, HPC | C5, C6i | c5.xlarge, c6i.2xlarge |
| Databases, caching | R5, R6i, X1 | r5.2xlarge, x1e.8xlarge |
| Big data (Hadoop, Kafka) | I3, D2 | i3.4xlarge, d2.8xlarge |
| ML training | P3, P4 | p3.8xlarge, p4d.24xlarge |
| ML inference | Inf1, G4dn | inf1.xlarge, g4dn.xlarge |
| ARM-based (cost savings) | T4g, M6g, C7g | t4g.medium, m6g.large |

---

## EC2 Pricing Model Selection

```
START: How to pay for EC2?
│
├─→ Workload is predictable/steady-state?
│   ├─→ YES → Can commit for 1-3 years?
│   │   ├─→ YES (1 year) → **Reserved Instances (1-year)**
│   │   │                  └─→ ~40% savings
│   │   ├─→ YES (3 years) → **Reserved Instances (3-year)**
│   │   │                  └─→ ~60% savings
│   │   └─→ Flexible commitment → **Savings Plans**
│   │                            ├─→ Compute Savings Plan (any instance)
│   │                            └─→ EC2 Instance Savings Plan (specific)
│   │
│   └─→ NO → Continue below...
│
├─→ Workload is flexible/interruptible?
│   ├─→ YES → Can handle interruptions (2-min notice)?
│   │   └─→ **Spot Instances**
│   │       ├─→ ~90% savings
│   │       ├─→ Use cases: Batch, big data, containers, CI/CD
│   │       └─→ Mix with On-Demand for resilience
│   │
│   └─→ NO → Continue below...
│
├─→ Need dedicated hardware (compliance/licensing)?
│   └─→ **Dedicated Hosts** or **Dedicated Instances**
│       ├─→ Dedicated Hosts: Full control, socket/core visibility
│       │   └─→ Use case: BYOL (Bring Your Own License)
│       └─→ Dedicated Instances: Isolated at hardware level
│           └─→ Use case: Compliance requirements
│
└─→ Unpredictable workload, no commitment?
    └─→ **On-Demand Instances**
        ├─→ Pay per second (Linux/Windows)
        ├─→ No upfront cost
        └─→ Most flexible, highest cost
```

### Pricing Strategy Decision

```
SCENARIO → RECOMMENDATION

Development/Testing
├─→ Variable usage → On-Demand or Spot
└─→ Regular hours (9-5) → Scheduled Reserved Instances

Production Baseline
├─→ Steady 24/7 → Reserved Instances (3-year)
└─→ Steady but growing → Savings Plans (more flexible)

Production Burst Capacity
├─→ Predictable peaks → Reserved + On-Demand
└─→ Unpredictable peaks → Reserved + Spot

Batch Processing
└─→ Spot Instances (with Spot Fleet for diversity)

Big Data / Analytics
└─→ Reserved (master nodes) + Spot (worker nodes)

Compliance / Licensing
└─→ Dedicated Hosts (for BYOL)

Cost Optimization Priority
└─→ Reserved/Savings Plans (70%) + Spot (20%) + On-Demand (10%)
```

---

## Container Service Selection

```
START: Running containerized applications?
│
├─→ Need Kubernetes?
│   ├─→ YES → Self-manage control plane?
│   │   ├─→ NO → **Amazon EKS** (Elastic Kubernetes Service)
│   │   │        ├─→ AWS-managed control plane
│   │   │        ├─→ Worker nodes: EC2 or Fargate
│   │   │        └─→ Best for: Existing K8s apps, complex orchestration
│   │   └─→ YES → Install K8s on EC2 (not recommended for exam)
│   │
│   └─→ NO → Continue below...
│
├─→ Want AWS-native orchestration?
│   └─→ YES → **Amazon ECS** (Elastic Container Service)
│             ├─→ Simpler than Kubernetes
│             ├─→ Deep AWS integration
│             └─→ Launch type: EC2 or Fargate
│
├─→ Want serverless containers (no server management)?
│   └─→ YES → **AWS Fargate**
│             ├─→ Works with ECS or EKS
│             ├─→ Pay per vCPU/memory used
│             ├─→ No EC2 instance management
│             └─→ Best for: Microservices, batch jobs, unpredictable workloads
│
├─→ Manage infrastructure (EC2 instances)?
│   └─→ YES → ECS on EC2 or EKS on EC2
│             ├─→ More control, potentially lower cost at scale
│             ├─→ You manage: patching, scaling, security
│             └─→ Best for: Steady workloads, cost optimization
│
└─→ Simple container deployment (single container)?
    └─→ **Elastic Beanstalk** (Docker single container)
        └─→ Simplest option, PaaS experience
```

### Container Service Comparison Matrix

| Feature | ECS | EKS | Fargate | Elastic Beanstalk |
|---------|-----|-----|---------|-------------------|
| **Complexity** | Low | High | Low | Very Low |
| **Kubernetes** | No | Yes | Optional | No |
| **Server Management** | EC2 mode: Yes | EC2 mode: Yes | No | Minimal |
| **Cost** | Free (pay EC2) | $0.10/hr + compute | Per vCPU/GB | Free (pay resources) |
| **Use Case** | AWS-native apps | K8s portability | Serverless containers | Quick deployment |
| **Learning Curve** | Easy | Steep | Easy | Very Easy |

---

## Serverless vs Server-based

```
START: Serverless or server-based?
│
├─→ Execution time?
│   ├─→ < 15 minutes → Serverless possible
│   ├─→ > 15 minutes → Server-based required
│   └─→ Hours/days → EC2 or containers
│
├─→ Traffic pattern?
│   ├─→ Sporadic/event-driven → **Lambda** (serverless)
│   ├─→ Steady 24/7 → **EC2/ECS** (server-based often cheaper)
│   └─→ Unpredictable spikes → **Lambda** or **Fargate**
│
├─→ Operational overhead tolerance?
│   ├─→ Want zero management → **Lambda**
│   ├─→ Want some control → **Fargate**
│   └─→ Want full control → **EC2**
│
├─→ Cold start sensitivity?
│   ├─→ Latency critical → **EC2/ECS** (always warm)
│   ├─→ Can tolerate 1-3 sec → **Lambda** (standard)
│   └─→ Need <1 sec → **Lambda** with provisioned concurrency
│
└─→ Cost consideration?
    ├─→ Low volume → **Lambda** (pay per invocation)
    ├─→ High volume, steady → **EC2/ECS Reserved** (cheaper at scale)
    └─→ High volume, variable → **Fargate** or **Lambda**
```

---

## Batch Processing Decision Tree

```
START: Need batch processing?
│
├─→ Simple batch jobs, no dependencies?
│   └─→ **Lambda** (if < 15 min) or **AWS Batch**
│
├─→ Complex dependencies, job scheduling?
│   └─→ **AWS Batch**
│       ├─→ Automatic job queue management
│       ├─→ Uses Spot, On-Demand, or both
│       └─→ Integrates with EC2, ECS, Fargate
│
├─→ Workflow orchestration needed?
│   └─→ **Step Functions** + Lambda/Batch
│       └─→ Visual workflows, error handling, retries
│
└─→ HPC, tightly-coupled workloads?
    └─→ **AWS Batch** with EC2 placement groups
        └─→ Or **ParallelCluster** for MPI workloads
```

---

## Lambda-Specific Decision Tree

```
START: Is Lambda appropriate?
│
├─→ Check execution time
│   ├─→ < 15 minutes → ✅ Lambda OK
│   └─→ > 15 minutes → ❌ Use Fargate/EC2
│
├─→ Check memory needs
│   ├─→ < 10 GB → ✅ Lambda OK
│   └─→ > 10 GB → ❌ Use Fargate/EC2
│
├─→ Check disk space
│   ├─→ < 10 GB (/tmp) → ✅ Lambda OK
│   └─→ > 10 GB → ❌ Use Fargate/EC2 with EBS
│
├─→ Check invocation pattern
│   ├─→ Event-driven (S3, DynamoDB, SQS, etc.) → ✅ Lambda ideal
│   ├─→ Scheduled (cron) → ✅ Lambda + EventBridge
│   ├─→ API requests → ✅ Lambda + API Gateway
│   └─→ Long-running daemon → ❌ Use EC2/Fargate
│
├─→ Check cold start tolerance
│   ├─→ Can tolerate 1-3 sec → ✅ Lambda standard
│   ├─→ Need < 1 sec → ✅ Lambda with provisioned concurrency
│   └─→ Need instant response 24/7 → ❌ Use EC2/Fargate
│
└─→ Check language runtime
    ├─→ Node.js, Python, Java, .NET, Go, Ruby, Custom → ✅ Lambda
    └─→ Unsupported language → ❌ Use custom runtime or containers
```

---

## EC2 vs ECS vs Lambda Quick Reference

| Criteria | Lambda | Fargate (ECS/EKS) | ECS on EC2 | EC2 |
|----------|--------|-------------------|------------|-----|
| **Execution Time** | < 15 min | Any | Any | Any |
| **Scaling** | Automatic, instant | Automatic, ~60 sec | Manual/Auto Scaling | Manual/Auto Scaling |
| **Cost (low volume)** | Very low | Low | Medium | Medium |
| **Cost (high volume)** | Medium | Medium | Low (with Reserved) | Low (with Reserved) |
| **Management** | Zero | Low | Medium | High |
| **Cold Start** | Yes (1-3 sec) | Yes (~60 sec) | No (if pre-scaled) | No |
| **Customization** | Limited | High | High | Full |
| **Best For** | Event-driven | Microservices | Steady workloads | Full control |

---

## Exam Scenarios - Quick Answers

### Scenario 1: Event-driven image processing (< 5 min per image)
**Answer**: Lambda triggered by S3 event

### Scenario 2: 24/7 web application with Auto Scaling
**Answer**: EC2 with Auto Scaling + ALB (or ECS on EC2)

### Scenario 3: Microservices, containerized, no server management
**Answer**: ECS on Fargate (or EKS on Fargate if need K8s)

### Scenario 4: Data processing job running 3 hours daily
**Answer**: AWS Batch with Spot instances (or Fargate)

### Scenario 5: API backend, sporadic traffic, cost-effective
**Answer**: Lambda + API Gateway

### Scenario 6: Legacy Java application, needs specific OS packages
**Answer**: EC2 (full control needed)

### Scenario 7: Kubernetes workload, managed control plane
**Answer**: Amazon EKS

### Scenario 8: High-performance computing, MPI
**Answer**: EC2 with cluster placement group (or AWS Batch)

### Scenario 9: Video transcoding, takes 30-60 minutes
**Answer**: AWS Batch or ECS/Fargate (Lambda too short)

### Scenario 10: WordPress site, simple setup
**Answer**: Lightsail (or Elastic Beanstalk for more control)

---

## 🎯 Key Takeaways

### Lambda When:
- ✅ Event-driven workloads
- ✅ < 15 minute execution
- ✅ Sporadic/unpredictable traffic
- ✅ Zero operational overhead desired

### EC2 When:
- ✅ Full control needed
- ✅ Long-running applications
- ✅ Custom software/OS requirements
- ✅ High volume, steady workload (with Reserved Instances)

### Fargate When:
- ✅ Containers without server management
- ✅ Microservices architecture
- ✅ Variable workloads
- ✅ Don't want to manage clusters

### ECS When:
- ✅ AWS-native container orchestration
- ✅ Simpler than Kubernetes
- ✅ Deep AWS service integration

### EKS When:
- ✅ Need Kubernetes compatibility
- ✅ Portability important
- ✅ Complex orchestration needs

### Elastic Beanstalk When:
- ✅ Quick deployment, minimal config
- ✅ Standard application stack
- ✅ PaaS experience desired

---

*Use these decision trees during exam to quickly eliminate wrong answers!*
