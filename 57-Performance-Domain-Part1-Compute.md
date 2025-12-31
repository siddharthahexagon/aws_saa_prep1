# AWS SAA-C03: DESIGN HIGH-PERFORMING ARCHITECTURES - PART 1

## 📋 COMPUTE PERFORMANCE & OPTIMIZATION

---

# 📊 DOMAIN OVERVIEW

| Aspect | Details |
|--------|---------|
| **Exam Weight** | **24%** (~16 questions out of 65) |
| **Focus Areas** | Scalable compute, storage, database, network performance |
| **Key Services** | EC2, Lambda, ECS/EKS, EBS, S3, RDS, Aurora, DynamoDB, CloudFront, ElastiCache |
| **Primary Goal** | Design architectures that deliver optimal performance for workload requirements |

---

## 🎯 FOUR FOCUS AREAS

```
┌─────────────────────────────────────────────────────────────────────┐
│                 DESIGN HIGH-PERFORMING ARCHITECTURES                 │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  1. SCALABLE COMPUTE                 2. HIGH-PERFORMING STORAGE     │
│  ┌─────────────────────────────┐   ┌─────────────────────────────┐  │
│  │ • EC2 instance selection    │   │ • EBS volume optimization   │  │
│  │ • Lambda optimization       │   │ • S3 performance            │  │
│  │ • Container performance     │   │ • Instance store            │  │
│  │ • Auto Scaling              │   │ • File systems (EFS, FSx)   │  │
│  │ • Placement groups          │   │ • Caching strategies        │  │
│  └─────────────────────────────┘   └─────────────────────────────┘  │
│                                                                      │
│  3. DATABASE PERFORMANCE             4. NETWORK OPTIMIZATION        │
│  ┌─────────────────────────────┐   ┌─────────────────────────────┐  │
│  │ • RDS/Aurora optimization   │   │ • CloudFront CDN            │  │
│  │ • DynamoDB performance      │   │ • Global Accelerator        │  │
│  │ • Caching (ElastiCache)     │   │ • VPC optimization          │  │
│  │ • Read replicas             │   │ • Enhanced networking       │  │
│  │ • Connection management     │   │ • Transfer Acceleration     │  │
│  └─────────────────────────────┘   └─────────────────────────────┘  │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

# 💻 EC2 INSTANCE PERFORMANCE

## Instance Type Selection

### Instance Family Overview

| Family | Optimized For | Use Cases | Example Types |
|--------|---------------|-----------|---------------|
| **M** | General Purpose | Web servers, app servers, dev/test | M5, M6i, M6g, M7g |
| **C** | Compute | Batch processing, gaming, ML inference | C5, C6i, C6g, C7g |
| **R** | Memory | In-memory databases, real-time analytics | R5, R6i, R6g |
| **X** | Memory Intensive | SAP HANA, large in-memory databases | X1, X1e, X2idn |
| **I** | Storage (NVMe) | NoSQL databases, data warehousing | I3, I3en, I4i |
| **D** | Dense Storage | Hadoop, data lakes, distributed file systems | D2, D3, D3en |
| **G/P** | GPU | ML training, graphics rendering | G4dn, G5, P4d, P5 |
| **Inf** | ML Inference | Cost-effective ML inference | Inf1, Inf2 |
| **Trn** | ML Training | Deep learning training | Trn1 |
| **Hpc** | HPC | Tightly coupled HPC workloads | Hpc6a, Hpc7g |
| **T** | Burstable | Variable workloads, dev/test | T3, T3a, T4g |

---

### Instance Naming Convention

```
┌─────────────────────────────────────────────────────────────────────┐
│                    INSTANCE NAMING CONVENTION                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   Example: c5n.xlarge                                                │
│                                                                      │
│   c    5    n    .    xlarge                                        │
│   │    │    │         │                                              │
│   │    │    │         └── Size (nano, micro, small, medium,         │
│   │    │    │              large, xlarge, 2xlarge, etc.)            │
│   │    │    │                                                        │
│   │    │    └── Additional capabilities:                            │
│   │    │         n = network optimized                               │
│   │    │         d = NVMe instance store                             │
│   │    │         a = AMD processor                                   │
│   │    │         g = AWS Graviton (ARM)                              │
│   │    │         e = extra memory or storage                         │
│   │    │         z = high frequency                                  │
│   │    │                                                             │
│   │    └── Generation (higher = newer)                               │
│   │                                                                  │
│   └── Instance family (c = compute, m = general, r = memory, etc.)  │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

### Performance Selection Guide

| Requirement | Instance Type | Reason |
|-------------|---------------|--------|
| Balanced workload | **M6i, M7g** | Equal compute, memory, network |
| CPU-intensive | **C6i, C7g** | Higher compute-to-memory ratio |
| Memory-intensive | **R6i, R7g** | Higher memory-to-compute ratio |
| Large memory (>1TB) | **X2idn** | Up to 4TB memory |
| High IOPS storage | **I4i** | NVMe SSD, high random IOPS |
| High throughput storage | **D3en** | Dense HDD, high sequential throughput |
| GPU compute | **P4d, P5** | NVIDIA GPUs for ML training |
| GPU graphics | **G5** | NVIDIA GPUs for graphics/gaming |
| ML inference | **Inf2** | AWS Inferentia chips |
| Cost-effective ARM | **M7g, C7g, R7g** | Graviton3 processors |
| Variable/bursty | **T3, T4g** | CPU credits, lowest cost |
| HPC (MPI) | **Hpc7g** | EFA enabled, tightly coupled |

---

## Placement Groups

### Definition
**Placement Groups** are logical groupings of instances that influence how instances are placed on underlying hardware.

### Types Comparison

| Type | Placement | Use Case | Limits |
|------|-----------|----------|--------|
| **Cluster** | Same rack, same AZ | HPC, low-latency | 1 AZ, same instance type recommended |
| **Spread** | Different racks | HA for critical instances | Max 7 instances per AZ |
| **Partition** | Different partitions | Large distributed systems (HDFS, Cassandra) | Max 7 partitions per AZ |

### Placement Group Details

```
┌─────────────────────────────────────────────────────────────────────┐
│                    PLACEMENT GROUPS                                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   CLUSTER PLACEMENT GROUP                                           │
│   ┌─────────────────────────────────────────────┐                   │
│   │              Same Rack (AZ-a)               │                   │
│   │   ┌───┐ ┌───┐ ┌───┐ ┌───┐ ┌───┐ ┌───┐    │                   │
│   │   │EC2│ │EC2│ │EC2│ │EC2│ │EC2│ │EC2│     │                   │
│   │   └───┘ └───┘ └───┘ └───┘ └───┘ └───┘    │                   │
│   │   10 Gbps between instances                 │                   │
│   │   Lowest latency (microseconds)             │                   │
│   └─────────────────────────────────────────────┘                   │
│   USE: HPC, tightly coupled applications                            │
│                                                                      │
│   SPREAD PLACEMENT GROUP                                            │
│   ┌───────────┐  ┌───────────┐  ┌───────────┐                      │
│   │  Rack 1   │  │  Rack 2   │  │  Rack 3   │                      │
│   │  ┌───┐   │  │  ┌───┐   │  │  ┌───┐   │                      │
│   │  │EC2│   │  │  │EC2│   │  │  │EC2│   │                      │
│   │  └───┘   │  │  └───┘   │  │  └───┘   │                      │
│   └───────────┘  └───────────┘  └───────────┘                      │
│   Each instance on separate hardware                                │
│   USE: Critical applications, max 7 per AZ                          │
│                                                                      │
│   PARTITION PLACEMENT GROUP                                         │
│   ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐    │
│   │  Partition 1    │  │  Partition 2    │  │  Partition 3    │    │
│   │  ┌───┐ ┌───┐   │  │  ┌───┐ ┌───┐   │  │  ┌───┐ ┌───┐   │    │
│   │  │EC2│ │EC2│   │  │  │EC2│ │EC2│   │  │  │EC2│ │EC2│   │    │
│   │  └───┘ └───┘   │  │  └───┘ └───┘   │  │  └───┘ └───┘   │    │
│   │  ┌───┐ ┌───┐   │  │  ┌───┐ ┌───┐   │  │  ┌───┐ ┌───┐   │    │
│   │  │EC2│ │EC2│   │  │  │EC2│ │EC2│   │  │  │EC2│ │EC2│   │    │
│   │  └───┘ └───┘   │  │  └───┘ └───┘   │  │  └───┘ └───┘   │    │
│   └─────────────────┘  └─────────────────┘  └─────────────────┘    │
│   Partitions have separate racks                                    │
│   USE: Hadoop, Cassandra, Kafka (partition-aware)                   │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Exam Heuristics

| Keyword/Scenario | Answer |
|------------------|--------|
| Lowest latency between instances | **Cluster Placement Group** |
| HPC workload | **Cluster Placement Group** |
| 10 Gbps between instances | **Cluster Placement Group + Enhanced Networking** |
| Isolate failure for critical instances | **Spread Placement Group** |
| Max 7 instances per AZ isolation | **Spread Placement Group** |
| Large distributed workload (HDFS) | **Partition Placement Group** |
| Hadoop, Cassandra, Kafka | **Partition Placement Group** |

---

## Enhanced Networking

### Definition
**Enhanced Networking** provides higher bandwidth, higher packet per second (PPS), and lower latency using SR-IOV (Single Root I/O Virtualization).

### Types

| Type | Description | Bandwidth | Instances |
|------|-------------|-----------|-----------|
| **ENA** | Elastic Network Adapter | Up to 100 Gbps | Most current gen |
| **Intel 82599 VF** | Intel Virtual Function | Up to 10 Gbps | Legacy (C3, R3) |

### EFA (Elastic Fabric Adapter)

| Feature | Description |
|---------|-------------|
| **Purpose** | HPC and ML workloads |
| **Protocol** | OS-bypass (direct hardware access) |
| **Benefit** | Lower latency, higher throughput |
| **Use Case** | MPI applications, distributed training |
| **Requirement** | Supported instance types + Linux |

### Exam Heuristics

| Keyword/Scenario | Answer |
|------------------|--------|
| High network throughput | **Enhanced Networking (ENA)** |
| HPC inter-node communication | **EFA + Cluster Placement Group** |
| MPI applications | **EFA** |
| 100 Gbps networking | **ENA on supported instances** |
| Distributed ML training | **EFA** |

---

# ⚡ AWS LAMBDA PERFORMANCE

## Definition
**AWS Lambda** is a serverless compute service that runs code in response to events and automatically manages compute resources.

## Performance Configuration

### Memory and CPU

```
┌─────────────────────────────────────────────────────────────────────┐
│                    LAMBDA MEMORY & CPU RELATIONSHIP                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   Memory          CPU Power        vCPUs                             │
│   ────────        ─────────        ─────                             │
│   128 MB          Fractional       < 1                               │
│   512 MB          Fractional       < 1                               │
│   1,769 MB        1 vCPU           1                                 │
│   3,538 MB        2 vCPUs          2                                 │
│   5,307 MB        3 vCPUs          3                                 │
│   7,076 MB        4 vCPUs          4                                 │
│   8,845 MB        5 vCPUs          5                                 │
│   10,240 MB       6 vCPUs          6                                 │
│                                                                      │
│   KEY INSIGHT: More memory = More CPU = Faster execution            │
│                                                                      │
│   COST OPTIMIZATION:                                                 │
│   Sometimes MORE memory = LOWER cost (faster execution)              │
│   Test with different memory settings to find optimal               │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Lambda Limits

| Limit | Value | Notes |
|-------|-------|-------|
| **Memory** | 128 MB - 10,240 MB | 1 MB increments |
| **Timeout** | 1 second - 15 minutes | Max execution time |
| **Ephemeral Storage** | 512 MB - 10,240 MB | /tmp directory |
| **Deployment Package** | 50 MB (zipped), 250 MB (unzipped) | Layers up to 250 MB |
| **Environment Variables** | 4 KB total | All variables combined |
| **Concurrent Executions** | 1,000 default | Can request increase |
| **Burst Concurrency** | 500-3,000 (region dependent) | Initial burst |

### Cold Start Optimization

```
┌─────────────────────────────────────────────────────────────────────┐
│                    LAMBDA COLD START                                 │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   COLD START                           WARM START                    │
│                                                                      │
│   ┌──────────────────┐                ┌──────────────────┐          │
│   │ Download Code    │                │                  │          │
│   │ (from S3)        │                │                  │          │
│   └────────┬─────────┘                │                  │          │
│            ▼                          │                  │          │
│   ┌──────────────────┐                │                  │          │
│   │ Create Container │                │                  │          │
│   │                  │                │                  │          │
│   └────────┬─────────┘                │                  │          │
│            ▼                          │                  │          │
│   ┌──────────────────┐                │                  │          │
│   │ Initialize       │                │                  │          │
│   │ Runtime          │                │                  │          │
│   └────────┬─────────┘                │   Execute        │          │
│            ▼                          │   Handler        │          │
│   ┌──────────────────┐                │                  │          │
│   │ Run Init Code    │                │   (Reuse        │          │
│   │ (outside handler)│                │    existing     │          │
│   └────────┬─────────┘                │    container)   │          │
│            ▼                          │                  │          │
│   ┌──────────────────┐                │                  │          │
│   │ Execute Handler  │                │                  │          │
│   │                  │                │                  │          │
│   └──────────────────┘                └──────────────────┘          │
│                                                                      │
│   COLD START: 100ms - 10+ seconds                                   │
│   WARM START: Milliseconds                                          │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Cold Start Mitigation

| Strategy | Description | Use Case |
|----------|-------------|----------|
| **Provisioned Concurrency** | Pre-initialized instances | Predictable latency required |
| **Keep Warm** | Scheduled invocations | Lower cost than provisioned |
| **Smaller Package** | Reduce deployment size | Faster download |
| **Optimize Init Code** | Minimize outside-handler code | Faster initialization |
| **Use ARM (Graviton2)** | Faster cold start | Cost + performance |
| **SnapStart** | Snapshot initialized state | Java only |

### Lambda SnapStart (Java)

| Feature | Description |
|---------|-------------|
| **Purpose** | Eliminate cold start for Java |
| **How** | Snapshots initialized state |
| **Benefit** | Up to 10x faster startup |
| **Limitation** | Java managed runtimes only |

### Provisioned Concurrency

```
┌─────────────────────────────────────────────────────────────────────┐
│                    PROVISIONED CONCURRENCY                           │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   WITHOUT PROVISIONED CONCURRENCY:                                   │
│                                                                      │
│   Request 1 → Cold Start → Execute (slow)                           │
│   Request 2 → Warm Start → Execute (fast)                           │
│   Request 3 → Warm Start → Execute (fast)                           │
│   ...                                                                │
│   Request N → Cold Start → Execute (slow) ← Container recycled      │
│                                                                      │
│   WITH PROVISIONED CONCURRENCY (PC=5):                              │
│                                                                      │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │  5 Pre-initialized instances always ready                    │   │
│   │                                                              │   │
│   │  ┌────┐ ┌────┐ ┌────┐ ┌────┐ ┌────┐                        │   │
│   │  │Warm│ │Warm│ │Warm│ │Warm│ │Warm│                         │   │
│   │  └────┘ └────┘ └────┘ └────┘ └────┘                        │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│   Request 1-5 → Immediate execution (no cold start)                 │
│   Request 6+  → Standard concurrency (may cold start)               │
│                                                                      │
│   USE CASE: APIs requiring consistent low latency                   │
│   COST: Pay for provisioned + invocations                           │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Lambda VPC Performance

| Scenario | Performance Impact |
|----------|-------------------|
| **No VPC** | Fastest (no ENI) |
| **VPC (Hyperplane)** | Minimal impact (since 2019) |
| **VPC + NAT Gateway** | Additional latency for internet |

### Exam Heuristics

| Keyword/Scenario | Answer |
|------------------|--------|
| Reduce cold start | **Provisioned Concurrency or SnapStart (Java)** |
| Consistent low latency | **Provisioned Concurrency** |
| Lambda too slow | **Increase memory (also increases CPU)** |
| Lambda timeout | **Increase timeout (max 15 min)** |
| Large deployment package | **Use Lambda Layers** |
| Cost-effective ARM | **Lambda with Graviton2 (arm64)** |
| Java cold start | **SnapStart** |
| Real-time API | **Provisioned Concurrency** |

---

# 🐳 CONTAINER PERFORMANCE

## ECS vs EKS Selection

| Feature | ECS | EKS |
|---------|-----|-----|
| **Orchestrator** | AWS proprietary | Kubernetes |
| **Learning Curve** | Lower | Higher |
| **Portability** | AWS only | Multi-cloud |
| **Control Plane** | Free | $0.10/hour (~$73/month) |
| **Use Case** | AWS-native apps | K8s expertise, multi-cloud |

## Launch Type Performance

### Fargate vs EC2

| Aspect | Fargate | EC2 |
|--------|---------|-----|
| **Management** | Serverless | You manage |
| **Scaling** | Automatic | Configure ASG |
| **GPU Support** | No | Yes |
| **Instance Store** | No | Yes |
| **Persistent Storage** | EFS only | EBS + EFS |
| **Cost Model** | Per vCPU/memory/second | Per instance |
| **Startup Time** | ~30-60 seconds | Depends on AMI |
| **Best For** | Variable workloads | Predictable, GPU, storage |

### ECS Task Placement

| Strategy | Description | Use Case |
|----------|-------------|----------|
| **binpack** | Minimize instances used | Cost optimization |
| **spread** | Distribute across AZs/instances | High availability |
| **random** | Random placement | Default |

### Exam Heuristics

| Keyword/Scenario | Answer |
|------------------|--------|
| Kubernetes expertise | **EKS** |
| Multi-cloud containers | **EKS** |
| Simpler container orchestration | **ECS** |
| Serverless containers | **Fargate** |
| GPU containers | **ECS/EKS on EC2** |
| Cost-optimize container placement | **ECS binpack strategy** |
| HA container placement | **ECS spread strategy** |

---

# 🔄 AUTO SCALING PERFORMANCE

## Scaling Speed Optimization

### Scaling Cooldown

| Setting | Purpose | Default |
|---------|---------|---------|
| **Default Cooldown** | Prevent rapid scaling | 300 seconds |
| **Scaling Specific** | Override for specific policies | Configurable |
| **Warm-up Time** | Time for instance to contribute | Configurable |

### Faster Scaling Strategies

| Strategy | How | Benefit |
|----------|-----|---------|
| **Reduce Cooldown** | Lower cooldown period | Faster response |
| **Step Scaling** | Multiple steps | Proportional response |
| **Target Tracking** | Automatic adjustment | Optimal scaling |
| **Predictive Scaling** | ML-based prediction | Proactive scaling |
| **Warm Pools** | Pre-initialized instances | Faster scale-out |

### Warm Pools

```
┌─────────────────────────────────────────────────────────────────────┐
│                    AUTO SCALING WARM POOLS                           │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   STANDARD AUTO SCALING:                                             │
│                                                                      │
│   Scale-out request → Launch instance → Boot → Configure → Ready    │
│                       │←──────────── 3-10 minutes ─────────────→│   │
│                                                                      │
│   WITH WARM POOL:                                                    │
│                                                                      │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │                    WARM POOL                                 │   │
│   │   ┌────────┐ ┌────────┐ ┌────────┐                          │   │
│   │   │Stopped │ │Stopped │ │Running │  (Pre-configured)        │   │
│   │   │Instance│ │Instance│ │Instance│                          │   │
│   │   └────────┘ └────────┘ └────────┘                          │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                              │                                       │
│   Scale-out request ─────────┘                                      │
│                              ▼                                       │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │                    AUTO SCALING GROUP                        │   │
│   │   ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐              │   │
│   │   │Running │ │Running │ │Running │ │Running │  (from pool) │   │
│   │   │Instance│ │Instance│ │Instance│ │Instance│              │   │
│   │   └────────┘ └────────┘ └────────┘ └────────┘              │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│   Scale-out time: Seconds (vs minutes)                              │
│                                                                      │
│   WARM POOL STATES:                                                  │
│   • Stopped: Lowest cost, slower to start                           │
│   • Running: Higher cost, instant availability                      │
│   • Hibernated: Middle ground (for supported instances)             │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Exam Heuristics

| Keyword/Scenario | Answer |
|------------------|--------|
| Faster scaling response | **Reduce cooldown, use step/target tracking** |
| Proactive scaling | **Predictive Scaling** |
| Pre-warmed instances | **Warm Pools** |
| Scale based on custom metric | **Target Tracking with custom metric** |
| Scale proportionally | **Step Scaling** |

---

# ✅ COMPUTE PERFORMANCE EXAM HEURISTICS

| Keyword/Scenario | Answer |
|------------------|--------|
| CPU-intensive workload | **C-family instances (C6i, C7g)** |
| Memory-intensive workload | **R-family instances (R6i, R7g)** |
| High IOPS storage | **I-family instances (I4i)** |
| High throughput storage | **D-family instances (D3en)** |
| GPU training | **P-family instances (P4d, P5)** |
| GPU inference | **Inf-family (Inf2) or G-family** |
| Cost-effective compute | **Graviton instances (M7g, C7g, R7g)** |
| Variable workload | **T-family (T3, T4g) burstable** |
| Lowest latency between instances | **Cluster Placement Group** |
| Isolate hardware failure | **Spread Placement Group** |
| Distributed systems (Hadoop) | **Partition Placement Group** |
| HPC networking | **EFA + Cluster Placement Group** |
| Lambda cold start | **Provisioned Concurrency or SnapStart** |
| Lambda slow execution | **Increase memory** |
| Serverless containers | **Fargate** |
| GPU containers | **ECS/EKS on EC2** |
| Fast Auto Scaling | **Warm Pools + reduced cooldown** |

---

*Continued in Part 2: Storage Performance*

---

*Last Updated: December 2025*
*Exam Version: SAA-C03*
*Domain: Design High-Performing Architectures (24%)*
