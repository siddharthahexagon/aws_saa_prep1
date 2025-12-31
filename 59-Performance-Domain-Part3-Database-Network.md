# AWS SAA-C03: DESIGN HIGH-PERFORMING ARCHITECTURES - PART 3

## 📋 DATABASE & NETWORK PERFORMANCE

---

# 🗄️ DATABASE PERFORMANCE

## RDS Performance Optimization

### Definition
**Amazon RDS** is a managed relational database service supporting multiple engines with built-in optimization features.

### RDS Instance Classes

| Class | Type | Use Case |
|-------|------|----------|
| **db.m** | General Purpose | Balanced workloads |
| **db.r** | Memory Optimized | Memory-intensive queries |
| **db.x** | Memory Optimized (Large) | Very large in-memory workloads |
| **db.t** | Burstable | Dev/test, variable workloads |

### Read Replicas

```
┌─────────────────────────────────────────────────────────────────────┐
│                    RDS READ REPLICAS                                 │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│                    APPLICATION                                       │
│                        │                                             │
│            ┌──────────┴──────────┐                                  │
│            │                     │                                   │
│         Writes                 Reads                                 │
│            │                     │                                   │
│            ▼                     ▼                                   │
│   ┌─────────────────┐   ┌─────────────────────────────────────┐    │
│   │   RDS Primary   │   │         READ REPLICAS               │    │
│   │                 │   │                                     │    │
│   │   (Read/Write)  │   │  ┌──────┐  ┌──────┐  ┌──────┐     │    │
│   │                 │──▶│  │ RR 1 │  │ RR 2 │  │ RR 3 │     │    │
│   │                 │   │  └──────┘  └──────┘  └──────┘     │    │
│   │                 │   │                                     │    │
│   └─────────────────┘   │  (Read Only - Async Replication)   │    │
│                          └─────────────────────────────────────┘    │
│                                                                      │
│   SCALING READS:                                                     │
│   • Up to 5 read replicas (15 for Aurora)                           │
│   • Same region or cross-region                                      │
│   • Async replication (slight lag)                                   │
│   • Can be promoted to standalone                                    │
│                                                                      │
│   PERFORMANCE BENEFIT:                                               │
│   • Offload SELECT queries from primary                             │
│   • Scale read capacity horizontally                                 │
│   • Use for reporting/analytics                                      │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### RDS Performance Features

| Feature | Description | Benefit |
|---------|-------------|---------|
| **Read Replicas** | Async copies for read scaling | Scale reads |
| **Storage Auto Scaling** | Automatically increase storage | Prevent running out |
| **Enhanced Monitoring** | 50+ metrics at 1-second granularity | Detailed insights |
| **Performance Insights** | Database performance analysis | Identify bottlenecks |
| **Proxy (RDS Proxy)** | Connection pooling | Handle many connections |

### RDS Proxy

```
┌─────────────────────────────────────────────────────────────────────┐
│                    RDS PROXY                                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   WITHOUT PROXY:                                                     │
│                                                                      │
│   Lambda (1000 concurrent) ─────▶ RDS (max ~2000 connections)       │
│   │ │ │ │ │ │ │ │ │ │                                               │
│   Each Lambda = 1 connection                                         │
│   ⚠️ Connection exhaustion                                           │
│                                                                      │
│   WITH RDS PROXY:                                                    │
│                                                                      │
│   Lambda (1000 concurrent) ─────▶ RDS Proxy ─────▶ RDS              │
│   │ │ │ │ │ │ │ │ │ │               │                               │
│   │ │ │ │ │ │ │ │ │ │           Connection                          │
│   └─┴─┴─┴─┴─┴─┴─┴─┴─┘           Pooling                             │
│   Connections shared                  │                              │
│                                    ~100 connections                  │
│                                    to database                       │
│                                                                      │
│   BENEFITS:                                                          │
│   • Connection pooling (reduce connections by 90%+)                 │
│   • Failover 66% faster                                             │
│   • IAM authentication                                               │
│   • Secrets Manager integration                                      │
│                                                                      │
│   USE CASES:                                                         │
│   • Lambda + RDS                                                     │
│   • Microservices with many connections                             │
│   • Unpredictable workloads                                         │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Exam Heuristics - RDS

| Keyword/Scenario | Answer |
|------------------|--------|
| Scale read queries | **Read Replicas** |
| Reporting without affecting production | **Read Replica** |
| Lambda + RDS connection issues | **RDS Proxy** |
| Too many database connections | **RDS Proxy** |
| Identify slow queries | **Performance Insights** |
| Detailed database metrics | **Enhanced Monitoring** |
| Cross-region read scaling | **Cross-Region Read Replica** |

---

## Aurora Performance

### Definition
**Amazon Aurora** is a MySQL and PostgreSQL-compatible relational database with up to 5x the throughput of MySQL and 3x the throughput of PostgreSQL.

### Aurora Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                    AURORA ARCHITECTURE                               │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│                    ┌─────────────────────────────┐                  │
│                    │      Aurora Cluster         │                  │
│                    │                             │                  │
│                    │   ┌─────────┐              │                  │
│                    │   │ Primary │ (Read/Write) │                  │
│                    │   │ Instance│              │                  │
│                    │   └────┬────┘              │                  │
│                    │        │                    │                  │
│                    │   ┌────┴────┬────────┬────────┐              │
│                    │   │         │        │        │              │
│                    │   ▼         ▼        ▼        ▼              │
│                    │ ┌────┐   ┌────┐   ┌────┐   ┌────┐          │
│                    │ │ RR │   │ RR │   │ RR │   │ RR │           │
│                    │ │ 1  │   │ 2  │   │ 3  │   │...15│          │
│                    │ └────┘   └────┘   └────┘   └────┘          │
│                    │ (Up to 15 Read Replicas)                    │
│                    └─────────────────┬───────────────────────────┘  │
│                                      │                               │
│                                      ▼                               │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │              SHARED CLUSTER STORAGE                          │   │
│   │                                                              │   │
│   │   ┌──────┐  ┌──────┐  ┌──────┐  ┌──────┐  ┌──────┐  ┌──────┐│
│   │   │Copy 1│  │Copy 2│  │Copy 3│  │Copy 4│  │Copy 5│  │Copy 6││
│   │   │ AZ-a │  │ AZ-a │  │ AZ-b │  │ AZ-b │  │ AZ-c │  │ AZ-c ││
│   │   └──────┘  └──────┘  └──────┘  └──────┘  └──────┘  └──────┘│
│   │                                                              │
│   │   6 copies across 3 AZs, auto-healing, auto-expanding       │
│   │   Up to 128 TiB per database                                │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│   PERFORMANCE:                                                       │
│   • 5x MySQL throughput, 3x PostgreSQL                              │
│   • Storage auto-scales in 10 GB increments                         │
│   • Sub-10ms replica lag                                             │
│   • Failover in < 30 seconds                                         │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Aurora Performance Features

| Feature | Description | Benefit |
|---------|-------------|---------|
| **Up to 15 Replicas** | More than RDS (5) | Better read scaling |
| **Replica Lag < 10ms** | Shared storage | Near real-time reads |
| **Auto-scaling Storage** | 10 GB to 128 TiB | No pre-provisioning |
| **Parallel Query** | Push query to storage | Faster analytics |
| **Aurora Serverless v2** | Auto-scaling compute | Variable workloads |
| **Global Database** | Cross-region replication | < 1 second lag |

### Aurora Serverless v2

```
┌─────────────────────────────────────────────────────────────────────┐
│                    AURORA SERVERLESS V2                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   CAPACITY (ACU - Aurora Capacity Units):                            │
│                                                                      │
│   Minimum: 0.5 ACU (can scale to zero with v2)                      │
│   Maximum: 128 ACU                                                   │
│                                                                      │
│   1 ACU ≈ 2 GB RAM                                                   │
│                                                                      │
│   SCALING:                                                           │
│                                                                      │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │                                                              │   │
│   │  Capacity                                                    │   │
│   │      ▲                                                       │   │
│   │      │    ┌──────┐                                          │   │
│   │  Max │    │      │     Traffic spike                        │   │
│   │      │    │      │                                          │   │
│   │      │  ┌─┘      └─┐                                        │   │
│   │      │ ┌┘          └┐                                       │   │
│   │  Min │─┘            └───────                                │   │
│   │      └─────────────────────────────────────────────▶ Time   │   │
│   │                                                              │   │
│   │  Scales in seconds (not minutes like v1)                    │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│   USE CASES:                                                         │
│   • Variable/unpredictable workloads                                │
│   • Dev/test environments                                           │
│   • Multi-tenant applications                                        │
│   • New applications with unknown demand                            │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Aurora Global Database

| Feature | Details |
|---------|---------|
| **Replication Lag** | < 1 second |
| **Secondary Regions** | Up to 5 |
| **Promotion Time** | < 1 minute |
| **Use Case** | Disaster recovery, global reads |

### Exam Heuristics - Aurora

| Keyword/Scenario | Answer |
|------------------|--------|
| High-performance MySQL/PostgreSQL | **Aurora** |
| More than 5 read replicas | **Aurora (up to 15)** |
| Variable database workload | **Aurora Serverless v2** |
| Cross-region database | **Aurora Global Database** |
| Fastest failover | **Aurora (< 30 seconds)** |
| Analytics on OLTP database | **Aurora Parallel Query** |

---

## DynamoDB Performance

### Definition
**Amazon DynamoDB** is a fully managed NoSQL database providing single-digit millisecond performance at any scale.

### Capacity Modes

| Mode | Description | Use Case |
|------|-------------|----------|
| **Provisioned** | Set RCU/WCU | Predictable traffic |
| **On-Demand** | Pay per request | Variable/unknown traffic |

### Read/Write Capacity Units

```
┌─────────────────────────────────────────────────────────────────────┐
│                    DYNAMODB CAPACITY UNITS                           │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   WRITE CAPACITY UNIT (WCU):                                         │
│   • 1 WCU = 1 write/second for item up to 1 KB                      │
│   • Larger items: Round up to nearest KB                             │
│                                                                      │
│   Example: 5 KB item = 5 WCU per write                              │
│                                                                      │
│   READ CAPACITY UNIT (RCU):                                          │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │                                                              │   │
│   │  STRONGLY CONSISTENT:                                        │   │
│   │  • 1 RCU = 1 read/second for item up to 4 KB                │   │
│   │                                                              │   │
│   │  EVENTUALLY CONSISTENT:                                      │   │
│   │  • 1 RCU = 2 reads/second for item up to 4 KB               │   │
│   │  • DEFAULT - 2x efficiency                                   │   │
│   │                                                              │   │
│   │  TRANSACTIONAL:                                              │   │
│   │  • 2 RCU = 1 transactional read for item up to 4 KB        │   │
│   │                                                              │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│   Example: 8 KB item                                                 │
│   • Strongly consistent read: 2 RCU                                 │
│   • Eventually consistent read: 1 RCU                               │
│   • Transactional read: 4 RCU                                       │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### DynamoDB Performance Features

| Feature | Description | Benefit |
|---------|-------------|---------|
| **DAX (DynamoDB Accelerator)** | In-memory cache | Microsecond reads |
| **Global Tables** | Multi-region active-active | Global low latency |
| **Auto Scaling** | Automatic capacity adjustment | Cost optimization |
| **Adaptive Capacity** | Handle uneven traffic | Prevent throttling |

### DAX (DynamoDB Accelerator)

```
┌─────────────────────────────────────────────────────────────────────┐
│                    DAX (DYNAMODB ACCELERATOR)                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   WITHOUT DAX:                                                       │
│                                                                      │
│   Application ─────────────────▶ DynamoDB                           │
│                                 (single-digit ms)                    │
│                                                                      │
│   WITH DAX:                                                          │
│                                                                      │
│   Application ─────▶ DAX Cluster ─────▶ DynamoDB                    │
│                      (microseconds)     (cache miss)                 │
│                           │                                          │
│                       Cache HIT                                      │
│                      (microseconds)                                  │
│                                                                      │
│   DAX CLUSTER:                                                       │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │   ┌─────────┐     ┌─────────┐     ┌─────────┐              │   │
│   │   │  DAX    │     │  DAX    │     │  DAX    │              │   │
│   │   │ Primary │     │ Replica │     │ Replica │              │   │
│   │   └─────────┘     └─────────┘     └─────────┘              │   │
│   │                                                              │   │
│   │   • In-VPC deployment                                        │   │
│   │   • Write-through cache                                      │   │
│   │   • 5-minute default TTL                                     │   │
│   │   • Up to 10 nodes per cluster                               │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│   USE CASES:                                                         │
│   • Read-heavy workloads                                            │
│   • Repeated reads (same data)                                      │
│   • Latency-sensitive applications                                  │
│                                                                      │
│   NOT SUITABLE FOR:                                                  │
│   • Write-heavy workloads                                           │
│   • Strongly consistent reads required                              │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### DynamoDB Partition Keys

```
┌─────────────────────────────────────────────────────────────────────┐
│                    PARTITION KEY DESIGN                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   HOT PARTITION (POOR DESIGN):                                       │
│                                                                      │
│   Partition Key: date                                                │
│                                                                      │
│   ┌─────────────┬─────────────┬─────────────┐                       │
│   │  2024-12-29 │  2024-12-30 │  2024-12-31 │                       │
│   │   (cold)    │   (cold)    │ ████████████│ ◀── ALL traffic       │
│   │   1%        │   1%        │    98%      │                       │
│   └─────────────┴─────────────┴─────────────┘                       │
│                                                                      │
│   ⚠️ 98% of traffic hits single partition → throttling              │
│                                                                      │
│   DISTRIBUTED (GOOD DESIGN):                                         │
│                                                                      │
│   Partition Key: user_id                                             │
│                                                                      │
│   ┌─────────────┬─────────────┬─────────────┬─────────────┐         │
│   │  user_001   │  user_002   │  user_003   │  user_004   │         │
│   │ ████████    │ █████████   │ ████████    │ ████████    │         │
│   │   25%       │    25%      │   25%       │   25%       │         │
│   └─────────────┴─────────────┴─────────────┴─────────────┘         │
│                                                                      │
│   ✓ Traffic distributed evenly → no throttling                      │
│                                                                      │
│   BEST PRACTICES:                                                    │
│   • High cardinality partition keys                                 │
│   • Use composite keys if needed                                    │
│   • Add random suffix if single key dominates                       │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Exam Heuristics - DynamoDB

| Keyword/Scenario | Answer |
|------------------|--------|
| Microsecond read latency | **DAX** |
| DynamoDB caching | **DAX** |
| Variable DynamoDB traffic | **On-Demand capacity** |
| Global low-latency database | **DynamoDB Global Tables** |
| Hot partition / throttling | **Better partition key design** |
| Large item storage (>400KB) | **Store pointer in DynamoDB, data in S3** |
| Time-series data | **Partition key with time prefix + write sharding** |

---

## Database Selection Guide

| Requirement | Service | Reason |
|-------------|---------|--------|
| Relational, high performance | **Aurora** | 5x MySQL throughput |
| Relational, standard | **RDS** | Managed, familiar |
| Key-value, any scale | **DynamoDB** | Serverless, consistent |
| Key-value, microsecond | **DynamoDB + DAX** | In-memory cache |
| Document database | **DocumentDB** | MongoDB compatible |
| Graph database | **Neptune** | Relationships |
| Time-series | **Timestream** | Purpose-built |
| Ledger/immutable | **QLDB** | Cryptographic verification |
| In-memory | **ElastiCache** | Sub-ms latency |
| Data warehouse | **Redshift** | Analytics |

---

# 🌐 NETWORK PERFORMANCE

## CloudFront Performance

### Definition
**Amazon CloudFront** is a global CDN that accelerates content delivery by caching at edge locations.

### Performance Optimization

| Feature | Description | Use Case |
|---------|-------------|----------|
| **Edge Locations** | 400+ POPs globally | Lowest latency |
| **Regional Edge Caches** | 13 locations | Longer cache retention |
| **Origin Shield** | Single cache layer | Reduce origin load |
| **HTTP/3 (QUIC)** | Faster connections | Mobile, lossy networks |
| **Brotli Compression** | Better than gzip | Smaller transfers |

### Cache Behaviors

```
┌─────────────────────────────────────────────────────────────────────┐
│                    CLOUDFRONT CACHE BEHAVIORS                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   Distribution: d123.cloudfront.net                                  │
│                                                                      │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │                    CACHE BEHAVIORS                           │   │
│   │                                                              │   │
│   │   Path Pattern    │  Origin      │  TTL    │  Headers       │   │
│   │   ─────────────────────────────────────────────────────────  │   │
│   │   /api/*          │  ALB         │  0      │  All           │   │
│   │   /images/*       │  S3          │  1 year │  None          │   │
│   │   /static/*       │  S3          │  1 week │  None          │   │
│   │   Default (*)     │  ALB         │  1 day  │  Host, Accept  │   │
│   │                                                              │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│   OPTIMIZATION:                                                      │
│   • Static content: Long TTL, S3 origin                             │
│   • Dynamic content: Short/no cache, forward necessary headers      │
│   • API: No cache, forward all headers                              │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Lambda@Edge vs CloudFront Functions

| Feature | Lambda@Edge | CloudFront Functions |
|---------|-------------|---------------------|
| **Runtime** | Node.js, Python | JavaScript |
| **Execution Time** | Up to 30 seconds | < 1 ms |
| **Memory** | 128 MB - 10 GB | 2 MB |
| **Network Access** | Yes | No |
| **Event Types** | All 4 | Viewer only |
| **Use Case** | Complex processing | Simple transforms |
| **Price** | Higher | Lower |

---

## Global Accelerator

### Definition
**AWS Global Accelerator** improves performance by routing traffic through AWS global network instead of public internet.

### Global Accelerator vs CloudFront

| Feature | Global Accelerator | CloudFront |
|---------|-------------------|------------|
| **Type** | Network layer (TCP/UDP) | Content delivery (HTTP/HTTPS) |
| **IP Addresses** | 2 static Anycast IPs | Dynamic edge IPs |
| **Caching** | No | Yes |
| **Use Case** | Non-HTTP, gaming, IoT | Web content |
| **Protocol** | TCP, UDP | HTTP, HTTPS, WebSocket |

```
┌─────────────────────────────────────────────────────────────────────┐
│                    GLOBAL ACCELERATOR                                │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   WITHOUT GLOBAL ACCELERATOR:                                        │
│                                                                      │
│   User (Sydney) ══════════════════════════════════▶ ALB (us-east)  │
│                    Public Internet (variable latency)               │
│                                                                      │
│   WITH GLOBAL ACCELERATOR:                                           │
│                                                                      │
│   User (Sydney) ──▶ Edge Location ════════════════▶ ALB (us-east)  │
│                      (Sydney)     AWS Global Network                │
│                                   (optimized, consistent)            │
│                                                                      │
│   FEATURES:                                                          │
│   • 2 static Anycast IP addresses                                   │
│   • Health checks and automatic failover                            │
│   • Traffic dials (weighted routing)                                │
│   • Endpoint groups per region                                      │
│                                                                      │
│   USE CASES:                                                         │
│   • Gaming (UDP)                                                    │
│   • IoT (MQTT over UDP)                                             │
│   • VoIP                                                            │
│   • Static IP requirement                                           │
│   • Non-HTTP applications                                           │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Exam Heuristics - Network

| Keyword/Scenario | Answer |
|------------------|--------|
| Global content delivery | **CloudFront** |
| Static IP for global app | **Global Accelerator** |
| Gaming (UDP) acceleration | **Global Accelerator** |
| Reduce origin load | **CloudFront Origin Shield** |
| Edge computing (complex) | **Lambda@Edge** |
| Edge computing (simple) | **CloudFront Functions** |
| Non-HTTP protocol | **Global Accelerator** |

---

## VPC Performance

### Enhanced Networking

| Feature | Description | Benefit |
|---------|-------------|---------|
| **ENA** | Elastic Network Adapter | Up to 100 Gbps |
| **EFA** | Elastic Fabric Adapter | HPC, ML training |
| **SR-IOV** | Hardware virtualization | Lower latency |

### VPC Endpoints Performance

| Type | Description | Use Case |
|------|-------------|----------|
| **Gateway Endpoint** | S3, DynamoDB (free) | Most S3/DynamoDB access |
| **Interface Endpoint** | Other services (PrivateLink) | Private API access |

### Exam Heuristics - VPC

| Keyword/Scenario | Answer |
|------------------|--------|
| High bandwidth EC2 networking | **Enhanced Networking (ENA)** |
| HPC inter-node communication | **EFA + Cluster Placement** |
| Private access to S3 | **Gateway VPC Endpoint** |
| Private access to other services | **Interface VPC Endpoint** |
| 100 Gbps networking | **ENA on supported instances** |

---

# ✅ DATABASE & NETWORK PERFORMANCE HEURISTICS

| Keyword/Scenario | Answer |
|------------------|--------|
| RDS read scaling | **Read Replicas** |
| RDS connection pooling | **RDS Proxy** |
| Lambda + RDS | **RDS Proxy** |
| High-performance MySQL | **Aurora** |
| Variable database workload | **Aurora Serverless v2** |
| DynamoDB caching | **DAX** |
| Microsecond DynamoDB reads | **DAX** |
| Global DynamoDB | **Global Tables** |
| DynamoDB throttling | **Improve partition key design** |
| Global static content | **CloudFront** |
| Global non-HTTP | **Global Accelerator** |
| Static IP global routing | **Global Accelerator** |
| Edge computing | **Lambda@Edge / CloudFront Functions** |

---

*Continued in Part 4: Architecture Patterns & Exam Scenarios*

---

*Last Updated: December 2025*
*Exam Version: SAA-C03*
*Domain: Design High-Performing Architectures (24%)*
