# AWS SAA-C03 CHEATSHEET: EC2 & COMPUTE SERVICES

## 📋 PRINT-READY EXAM REFERENCE

---

# 🖥️ AMAZON EC2 (ELASTIC COMPUTE CLOUD)

## Definition
Virtual servers in the cloud with configurable CPU, memory, storage, and networking.

## Instance Types - MEMORIZE THIS!

| Family | Optimized For | Use Cases | Memory Hint |
|--------|---------------|-----------|-------------|
| **M** | General Purpose | Web servers, small DBs | **M**ost workloads |
| **C** | Compute | CPU-intensive, batch, gaming | **C**PU |
| **R** | Memory | In-memory DBs, caching | **R**AM |
| **X** | Memory (extreme) | SAP HANA, large in-memory | e**X**treme memory |
| **I** | Storage (IOPS) | High IOPS, NoSQL DBs | **I**OPS |
| **D** | Storage (density) | Data warehouses, HDFS | **D**ense storage |
| **G** | Graphics | 3D rendering, ML inference | **G**raphics |
| **P** | GPU Compute | ML training, HPC | **P**arallel processing |
| **T** | Burstable | Variable workloads, dev/test | **T**urbo burst |
| **z1d** | High frequency | Gaming, EDA | High **z**ingle-thread |
| **Hpc** | High Performance | Tightly coupled HPC | **H**PC |
| **Mac** | macOS | iOS development | **Mac**intosh |

## Instance Naming Convention
```
m5.xlarge
│ │  └── Size (nano, micro, small, medium, large, xlarge, 2xlarge...)
│ └── Generation (higher = newer)
└── Family
```

## EC2 Purchasing Options - CRITICAL FOR EXAM!

| Option | Discount | Commitment | Use Case | Key Point |
|--------|----------|------------|----------|-----------|
| **On-Demand** | 0% | None | Short-term, unpredictable | Pay per second (Linux) or hour (Windows) |
| **Reserved (Standard)** | Up to 72% | 1 or 3 years | Steady-state | Can sell on Marketplace |
| **Reserved (Convertible)** | Up to 66% | 1 or 3 years | Flexible instance type | Can change instance family |
| **Savings Plans (Compute)** | Up to 66% | 1 or 3 years | Flexible across services | Applies to EC2, Fargate, Lambda |
| **Savings Plans (EC2)** | Up to 72% | 1 or 3 years | Committed to EC2 family | Locked to region & family |
| **Spot** | Up to 90% | None | Fault-tolerant, flexible | Can be interrupted with 2-min warning |
| **Dedicated Host** | Varies | None or Reserved | Licensing, compliance | Physical server, BYOL |
| **Dedicated Instance** | Varies | None | Compliance | Hardware isolation, not physical server |
| **Capacity Reservations** | 0% | None | Guaranteed capacity | Reserve in specific AZ |

## Spot Instance Strategies

| Strategy | Description | Best For |
|----------|-------------|----------|
| **Spot Fleet** | Collection of Spot + On-Demand | Cost optimization with availability |
| **Spot Block** | 1-6 hours guaranteed | Batch jobs with defined duration |
| **Capacity-Optimized** | Least likely to be interrupted | Stateful workloads |
| **Lowest-Price** | Cheapest instances | Fault-tolerant batch |
| **Diversified** | Spread across pools | Maximum availability |

## EC2 Placement Groups

| Type | Description | Use Case | Key Rule |
|------|-------------|----------|----------|
| **Cluster** | Same rack, same AZ | Low latency, HPC | Single AZ only, 10Gbps network |
| **Spread** | Different hardware | HA for critical instances | Max 7 instances per AZ per group |
| **Partition** | Separate racks | Hadoop, Cassandra, Kafka | Max 7 partitions per AZ |

## EC2 Instance Store vs EBS

| Feature | Instance Store | EBS |
|---------|----------------|-----|
| **Persistence** | Ephemeral (lost on stop) | Persistent |
| **Performance** | Very high IOPS | Up to 256,000 IOPS (io2 Block Express) |
| **Backup** | Manual only | Snapshots |
| **Use Case** | Cache, temp data, buffer | Databases, boot volumes |
| **Replication** | None | Within AZ |

## EC2 Network Features

| Feature | Description |
|---------|-------------|
| **ENI** | Basic network interface, multiple IPs |
| **ENA** | Enhanced Networking, up to 100 Gbps |
| **EFA** | Elastic Fabric Adapter, HPC/ML, OS-bypass |

## EC2 Hibernate
- **Saves RAM to EBS** - faster boot
- Root volume must be **encrypted EBS**
- Max **60 days** hibernation
- Supported: On-Demand, Reserved, Spot
- RAM must be **< 150 GB**

---

# 📦 AMAZON EBS (ELASTIC BLOCK STORE)

## Definition
Persistent block storage volumes for EC2 instances, replicated within an AZ.

## EBS Volume Types - MEMORIZE THIS TABLE!

| Type | IOPS | Throughput | Size | Use Case | Key Point |
|------|------|------------|------|----------|-----------|
| **gp3** | 16,000 | 1,000 MB/s | 1 GiB - 16 TiB | General purpose | IOPS independent of size |
| **gp2** | 16,000 | 250 MB/s | 1 GiB - 16 TiB | Boot volumes | 3 IOPS per GiB (burst to 3,000) |
| **io2 Block Express** | 256,000 | 4,000 MB/s | 4 GiB - 64 TiB | Critical DBs | 99.999% durability |
| **io2/io1** | 64,000 | 1,000 MB/s | 4 GiB - 16 TiB | Databases | 50:1 IOPS to GiB ratio |
| **st1** | 500 | 500 MB/s | 125 GiB - 16 TiB | Big data, logs | Cannot be boot volume |
| **sc1** | 250 | 250 MB/s | 125 GiB - 16 TiB | Cold data | Lowest cost HDD |

## EBS Quick Rules

| Rule | Detail |
|------|--------|
| **AZ-locked** | EBS volumes are locked to an AZ |
| **Move across AZ** | Snapshot → Restore in new AZ |
| **Move across Region** | Snapshot → Copy to region → Restore |
| **Encryption** | Uses KMS, snapshots of encrypted volumes are encrypted |
| **Encrypt unencrypted** | Snapshot → Copy with encryption → Create volume |
| **Multi-Attach** | io1/io2 only, same AZ, up to 16 instances |
| **Root volume** | Deleted by default when instance terminates (can change) |
| **Data volumes** | NOT deleted by default when instance terminates |

## EBS Snapshots

| Feature | Description |
|---------|-------------|
| **Incremental** | Only changed blocks stored after first |
| **Storage** | Stored in S3 (managed by AWS) |
| **Cross-region** | Copy snapshots to other regions |
| **Fast Snapshot Restore** | Eliminate latency on first access ($$$) |
| **Recycle Bin** | Recover accidentally deleted snapshots (1 day - 1 year) |
| **Archive Tier** | 75% cheaper, 24-72 hours to restore |

## EBS-Optimized Instances
- Dedicated bandwidth for EBS I/O
- Most current generation instances are EBS-optimized by default
- Required for maximum io2 performance

---

# ⚖️ ELASTIC LOAD BALANCING (ELB)

## Load Balancer Types - CRITICAL!

| Type | Layer | Protocol | Use Case | Key Features |
|------|-------|----------|----------|--------------|
| **ALB** | 7 | HTTP, HTTPS, gRPC | Web apps, microservices | Path/host routing, Lambda targets |
| **NLB** | 4 | TCP, UDP, TLS | Extreme performance | Millions of requests/sec, static IP |
| **GLB** | 3 | IP | 3rd party appliances | Firewalls, IDS/IPS |
| **CLB** | 4/7 | TCP, SSL, HTTP, HTTPS | Legacy | Don't use for new apps |

## ALB Features

| Feature | Description |
|---------|-------------|
| **Target Groups** | EC2, ECS, Lambda, IP addresses |
| **Path-based routing** | /api/* → Service A, /web/* → Service B |
| **Host-based routing** | api.example.com → Service A |
| **Query string routing** | ?platform=mobile → Mobile service |
| **Fixed response** | Return custom HTTP response |
| **Redirects** | HTTP → HTTPS redirect |
| **Sticky sessions** | AWSALB cookie, duration-based |
| **Weighted target groups** | Blue/green, canary deployments |
| **Authentication** | OIDC, Cognito integration |

## NLB Features

| Feature | Description |
|---------|-------------|
| **Static IP** | One static IP per AZ |
| **Elastic IP** | Can assign EIP |
| **Preserve source IP** | Client IP visible to targets |
| **Zonal isolation** | Fail-over within AZ |
| **Cross-zone disabled** | By default (can enable) |
| **TLS termination** | Decrypt at NLB |
| **UDP support** | Gaming, IoT, streaming |
| **PrivateLink** | Expose services via VPC endpoints |

## GLB (Gateway Load Balancer)

| Feature | Description |
|---------|-------------|
| **GENEVE protocol** | Port 6081 |
| **Transparent** | Traffic flows through appliances |
| **Use case** | Firewalls, IDS/IPS, deep packet inspection |
| **Single entry/exit** | For VPC traffic |

## Health Checks

| Setting | ALB | NLB |
|---------|-----|-----|
| **Protocol** | HTTP, HTTPS | TCP, HTTP, HTTPS |
| **Path** | /health | /health (if HTTP) |
| **Interval** | 5-300 sec | 10 or 30 sec |
| **Unhealthy threshold** | 2-10 | 2-10 |

## Cross-Zone Load Balancing

| LB Type | Default | Charges |
|---------|---------|---------|
| ALB | Enabled | No charge |
| NLB | Disabled | Charges apply if enabled |
| CLB | Disabled | No charge |

## Connection Draining / Deregistration Delay
- Time to complete in-flight requests before deregistering
- Default: 300 seconds
- Range: 0-3600 seconds
- Set to 0 for immediate deregistration

---

# 📈 AUTO SCALING

## Auto Scaling Components

| Component | Description |
|-----------|-------------|
| **Launch Template** | EC2 configuration (AMI, instance type, etc.) |
| **Launch Configuration** | Legacy, immutable, don't use for new |
| **Auto Scaling Group** | Collection of EC2 instances |
| **Scaling Policy** | When and how to scale |

## Scaling Policies

| Policy Type | Description | Use Case |
|-------------|-------------|----------|
| **Target Tracking** | Maintain metric at target value | CPUUtilization at 50% |
| **Step Scaling** | Scale based on alarm thresholds | Add 2 when CPU > 70%, add 4 when > 90% |
| **Simple Scaling** | Single adjustment, wait for cooldown | Legacy, don't use |
| **Scheduled** | Scale at specific times | Business hours scaling |
| **Predictive** | ML-based forecasting | Predictable patterns |

## Scaling Metrics

| Metric | Description |
|--------|-------------|
| **CPUUtilization** | Average CPU across group |
| **RequestCountPerTarget** | ALB requests per instance |
| **Average Network In/Out** | Network traffic |
| **Custom CloudWatch** | Any custom metric |

## Auto Scaling Settings

| Setting | Description | Default |
|---------|-------------|---------|
| **Minimum** | Minimum instances always running | 1 |
| **Maximum** | Maximum instances allowed | - |
| **Desired** | Target number of instances | - |
| **Cooldown** | Wait period between scaling actions | 300 sec |
| **Health check grace** | Time before health checks start | 300 sec |
| **Warm-up** | Time for new instance to warm up | 300 sec |

## Instance Refresh
- Replace all instances with new configuration
- **Minimum healthy percentage**: instances to keep during refresh
- **Instance warmup**: time before instance is ready
- Use for: AMI updates, launch template changes

## Termination Policies (Order of Priority)
1. AZ with most instances
2. Oldest launch configuration/template
3. Closest to billing hour
4. Random

---

# 🌐 ELASTIC IP & ENI

## Elastic IP

| Feature | Description |
|---------|-------------|
| **Definition** | Static public IPv4 address |
| **Limit** | 5 per region (soft limit) |
| **Charges** | Free when attached to running instance |
| **Remap** | Can quickly remap to another instance |
| **Use case** | Failover, static endpoints |

## Elastic Network Interface (ENI)

| Feature | Description |
|---------|-------------|
| **Definition** | Virtual network card |
| **Attributes** | Private IP, public IP, Elastic IP, MAC, security groups |
| **AZ-bound** | Cannot move across AZs |
| **Multiple ENIs** | Attach multiple to one instance |
| **Use case** | Management network, dual-homed instances |

---

# 🎯 EC2 QUICK EXAM HEURISTICS

| Keyword/Scenario | Answer |
|------------------|--------|
| HPC, tightly coupled | **Cluster placement group + EFA** |
| Critical instances, spread hardware | **Spread placement group** |
| Hadoop, Cassandra, HDFS | **Partition placement group** |
| Lowest cost, can be interrupted | **Spot instances** |
| Steady workload, 1-3 year commitment | **Reserved instances** |
| Compliance, dedicated hardware | **Dedicated Hosts** |
| BYOL (Bring Your Own License) | **Dedicated Hosts** |
| Fast boot, save RAM state | **EC2 Hibernate** |
| High IOPS database | **io2 Block Express** |
| Throughput optimized, big data | **st1** |
| Boot volume, general purpose | **gp3** |
| Move EBS across AZ | **Snapshot → Restore** |
| Encrypt existing volume | **Snapshot → Copy encrypted → New volume** |
| HTTP/HTTPS routing | **ALB** |
| TCP/UDP, extreme performance | **NLB** |
| Static IP for load balancer | **NLB with Elastic IP** |
| Third-party firewall appliances | **Gateway Load Balancer** |
| Blue/green deployment | **ALB weighted target groups** |
| Predictable scaling patterns | **Predictive scaling** |
| Maintain 70% CPU | **Target tracking scaling** |
| Scale at 9 AM daily | **Scheduled scaling** |
| Cross-region DR for EBS | **EBS Snapshots → Copy to DR region** |

---

# 📊 INSTANCE TYPE SELECTION MATRIX

| Workload | Instance Type | Why |
|----------|---------------|-----|
| Web server | M5/M6i | Balanced |
| In-memory cache | R5/R6i | High memory |
| Batch processing | C5/C6i | CPU optimized |
| ML training | P4/P5 | GPU compute |
| Graphics rendering | G4/G5 | GPU graphics |
| SAP HANA | X1e/X2idn | Extreme memory |
| OLTP database | R5/R6i + io2 | Memory + IOPS |
| Data warehouse | D2/D3 | Dense storage |
| High-frequency trading | z1d | Fast single-thread |
| Dev/test | T3 | Burstable, cheap |

---

# ✅ EXAM DAY QUICK CHECKS

```
□ Know all instance families (M, C, R, X, I, D, G, P, T)
□ Know all EBS types and their IOPS limits
□ Know placement group types and constraints
□ Know load balancer types and layers
□ Know scaling policy types
□ Know Spot vs Reserved vs On-Demand use cases
□ Know EBS snapshot operations
□ Know EC2 Hibernate requirements
```

---

*Last Updated: December 2025*
*Exam Version: SAA-C03*
