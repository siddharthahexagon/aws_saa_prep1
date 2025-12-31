# AWS SAA-C03: DESIGN HIGH-PERFORMING ARCHITECTURES - PART 4

## 📋 CACHING, ARCHITECTURE PATTERNS & EXAM SCENARIOS

---

# 🚀 CACHING STRATEGIES

## ElastiCache

### Definition
**Amazon ElastiCache** is a fully managed in-memory caching service supporting Redis and Memcached.

### Redis vs Memcached

| Feature | Redis | Memcached |
|---------|-------|-----------|
| **Data Structures** | Complex (lists, sets, hashes) | Simple (strings) |
| **Persistence** | Yes (RDB, AOF) | No |
| **Replication** | Yes (Multi-AZ) | No |
| **Cluster Mode** | Yes | Yes |
| **Backup/Restore** | Yes | No |
| **Pub/Sub** | Yes | No |
| **Lua Scripting** | Yes | No |
| **Geospatial** | Yes | No |
| **Use Case** | Complex caching, sessions | Simple caching |

### Caching Patterns

```
┌─────────────────────────────────────────────────────────────────────┐
│                    CACHING PATTERNS                                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   LAZY LOADING (Cache-Aside):                                        │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │                                                              │   │
│   │   Application                                                │   │
│   │       │                                                      │   │
│   │       ├──1. GET──▶ Cache                                    │   │
│   │       │            │                                         │   │
│   │       │        Cache MISS                                    │   │
│   │       │                                                      │   │
│   │       ├──2. GET──▶ Database                                 │   │
│   │       │            │                                         │   │
│   │       │        Get Data                                      │   │
│   │       │                                                      │   │
│   │       └──3. SET──▶ Cache (store for next time)              │   │
│   │                                                              │   │
│   │   ✓ Only requested data is cached                           │   │
│   │   ✓ Cache failures don't break app                          │   │
│   │   ✗ Cache miss = 3 round trips (slow first request)         │   │
│   │   ✗ Stale data possible                                     │   │
│   │                                                              │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│   WRITE-THROUGH:                                                     │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │                                                              │   │
│   │   Application                                                │   │
│   │       │                                                      │   │
│   │       ├──1. WRITE──▶ Cache ──2. WRITE──▶ Database          │   │
│   │       │                                                      │   │
│   │   ✓ Data is never stale                                     │   │
│   │   ✓ Read performance improved                               │   │
│   │   ✗ Write penalty (2 writes every time)                     │   │
│   │   ✗ Unused data may be cached                               │   │
│   │                                                              │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│   TTL (Time To Live):                                                │
│   • Add TTL to all cache entries                                    │
│   • Balance freshness vs cache hit rate                             │
│   • Lazy loading + TTL = best combo for most cases                  │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### ElastiCache Cluster Modes

```
┌─────────────────────────────────────────────────────────────────────┐
│                    REDIS CLUSTER MODES                               │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   CLUSTER MODE DISABLED:                                             │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │                                                              │   │
│   │   ┌─────────┐     ┌─────────┐     ┌─────────┐              │   │
│   │   │ Primary │────▶│ Replica │     │ Replica │              │   │
│   │   │         │     │   1     │     │   2     │              │   │
│   │   └─────────┘     └─────────┘     └─────────┘              │   │
│   │                                                              │   │
│   │   • Single shard                                             │   │
│   │   • Up to 5 replicas                                         │   │
│   │   • Multi-AZ failover                                        │   │
│   │   • Max ~340 GB cache                                        │   │
│   │                                                              │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│   CLUSTER MODE ENABLED:                                              │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │                                                              │   │
│   │   Shard 1          Shard 2          Shard 3                 │   │
│   │   ┌────────┐       ┌────────┐       ┌────────┐             │   │
│   │   │Primary │       │Primary │       │Primary │             │   │
│   │   │Replica │       │Replica │       │Replica │             │   │
│   │   │Replica │       │Replica │       │Replica │             │   │
│   │   └────────┘       └────────┘       └────────┘             │   │
│   │                                                              │   │
│   │   • Data partitioned across shards                          │   │
│   │   • Up to 500 shards                                        │   │
│   │   • Scale horizontally                                       │   │
│   │   • Max ~340 GB x 500 = 170 TB                              │   │
│   │                                                              │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Exam Heuristics - ElastiCache

| Keyword/Scenario | Answer |
|------------------|--------|
| In-memory caching with persistence | **Redis** |
| Simple key-value caching | **Memcached** |
| Session store | **Redis** |
| Leaderboards, sorted data | **Redis** |
| Pub/Sub messaging | **Redis** |
| Scale beyond single node | **Cluster Mode Enabled** |
| Multi-AZ caching | **Redis with Multi-AZ** |

---

# 📐 HIGH-PERFORMANCE ARCHITECTURE PATTERNS

## Pattern 1: Static Website with CloudFront

```
┌─────────────────────────────────────────────────────────────────────┐
│                    HIGH-PERFORMANCE STATIC WEBSITE                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   Global Users                                                       │
│       │                                                              │
│       ▼                                                              │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │              CLOUDFRONT (400+ Edge Locations)                │   │
│   │                                                              │   │
│   │   • SSL/TLS termination                                      │   │
│   │   • HTTP/2 & HTTP/3 (QUIC)                                  │   │
│   │   • Brotli compression                                       │   │
│   │   • Cache headers optimization                               │   │
│   │   • Origin Shield enabled                                    │   │
│   │                                                              │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                        │                                             │
│                  Cache Miss                                          │
│                        │                                             │
│                        ▼                                             │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │              S3 BUCKET (Origin)                              │   │
│   │                                                              │   │
│   │   • Static website hosting                                   │   │
│   │   • OAC (Origin Access Control)                             │   │
│   │   • Versioning enabled                                       │   │
│   │   • Cross-region replication (optional)                      │   │
│   │                                                              │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│   PERFORMANCE TIPS:                                                  │
│   • Set long TTLs for static assets (1 year for versioned)          │
│   • Use cache invalidation only when needed                         │
│   • Enable compression for text-based content                       │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

## Pattern 2: High-Performance API

```
┌─────────────────────────────────────────────────────────────────────┐
│                    HIGH-PERFORMANCE API                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   Mobile/Web Clients                                                 │
│       │                                                              │
│       ▼                                                              │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │              AMAZON CLOUDFRONT                               │   │
│   │   • Edge caching for GET requests                           │   │
│   │   • TLS termination at edge                                 │   │
│   │   • WAF integration                                         │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                        │                                             │
│                        ▼                                             │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │              API GATEWAY (REST or HTTP API)                  │   │
│   │   • Request throttling                                       │   │
│   │   • Response caching                                         │   │
│   │   • Request validation                                       │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                        │                                             │
│         ┌──────────────┴──────────────┐                             │
│         ▼                              ▼                             │
│   ┌────────────┐                ┌────────────────┐                  │
│   │   Lambda   │                │   ALB + ECS/   │                  │
│   │ Functions  │                │     Fargate    │                  │
│   └─────┬──────┘                └───────┬────────┘                  │
│         │                                │                           │
│         └──────────────┬─────────────────┘                          │
│                        │                                             │
│                        ▼                                             │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │              DATA LAYER                                      │   │
│   │                                                              │   │
│   │   ┌──────────────┐    ┌──────────────┐                      │   │
│   │   │ ElastiCache  │    │   DynamoDB   │                      │   │
│   │   │   (Redis)    │    │   + DAX      │                      │   │
│   │   └──────────────┘    └──────────────┘                      │   │
│   │         │                    │                               │   │
│   │   Session/Cache        Primary Data                          │   │
│   │                                                              │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

## Pattern 3: High-Performance Data Analytics

```
┌─────────────────────────────────────────────────────────────────────┐
│                    HIGH-PERFORMANCE ANALYTICS                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   Data Sources                                                       │
│   ┌─────────┐ ┌─────────┐ ┌─────────┐                              │
│   │ IoT     │ │ Apps    │ │ Logs    │                              │
│   └────┬────┘ └────┬────┘ └────┬────┘                              │
│        │           │           │                                     │
│        └───────────┴───────────┘                                     │
│                    │                                                 │
│                    ▼                                                 │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │              KINESIS DATA STREAMS                            │   │
│   │   • Multiple shards for parallelism                          │   │
│   │   • Enhanced fan-out (2 MB/sec per consumer per shard)      │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                    │                                                 │
│         ┌─────────┴─────────┐                                       │
│         ▼                   ▼                                       │
│   ┌────────────────┐  ┌────────────────┐                           │
│   │ Kinesis Data   │  │    Lambda      │                           │
│   │   Firehose     │  │ (Real-time)    │                           │
│   │ (Batch to S3)  │  │                │                           │
│   └───────┬────────┘  └────────────────┘                           │
│           │                                                          │
│           ▼                                                          │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │              S3 DATA LAKE                                    │   │
│   │   • Parquet format (columnar)                               │   │
│   │   • Partitioned by date/key                                 │   │
│   │   • S3 Intelligent-Tiering                                  │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                    │                                                 │
│         ┌─────────┴─────────┐                                       │
│         ▼                   ▼                                       │
│   ┌────────────────┐  ┌────────────────┐                           │
│   │    Athena      │  │   Redshift     │                           │
│   │ (Serverless    │  │ (Data Warehouse│                           │
│   │  Queries)      │  │  - Complex)    │                           │
│   └────────────────┘  └────────────────┘                           │
│                                                                      │
│   PERFORMANCE OPTIMIZATIONS:                                         │
│   • Kinesis: More shards = more throughput                          │
│   • S3: Columnar format, compression, partitioning                  │
│   • Athena: Partition pruning, columnar scan                        │
│   • Redshift: Distribution style, sort keys, compression            │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

## Pattern 4: Global High-Performance Application

```
┌─────────────────────────────────────────────────────────────────────┐
│                    GLOBAL HIGH-PERFORMANCE APP                       │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   Global Users                                                       │
│       │                                                              │
│       ▼                                                              │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │              ROUTE 53 (Latency-Based Routing)                │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                        │                                             │
│         ┌──────────────┼──────────────┐                             │
│         ▼              ▼              ▼                             │
│   ┌──────────┐   ┌──────────┐   ┌──────────┐                       │
│   │ us-east-1│   │ eu-west-1│   │ap-south-1│                       │
│   │   ALB    │   │   ALB    │   │   ALB    │                       │
│   └────┬─────┘   └────┬─────┘   └────┬─────┘                       │
│        │              │              │                               │
│        ▼              ▼              ▼                               │
│   ┌──────────┐   ┌──────────┐   ┌──────────┐                       │
│   │ ECS/EKS  │   │ ECS/EKS  │   │ ECS/EKS  │                       │
│   │ Fargate  │   │ Fargate  │   │ Fargate  │                       │
│   └────┬─────┘   └────┬─────┘   └────┬─────┘                       │
│        │              │              │                               │
│        ▼              ▼              ▼                               │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │           AURORA GLOBAL DATABASE                             │   │
│   │                                                              │   │
│   │   us-east-1        eu-west-1        ap-south-1              │   │
│   │   ┌────────┐       ┌────────┐       ┌────────┐              │   │
│   │   │PRIMARY │ ────▶ │REPLICA │ ────▶ │REPLICA │              │   │
│   │   │        │<1 sec │        │<1 sec │        │              │   │
│   │   └────────┘  lag  └────────┘  lag  └────────┘              │   │
│   │                                                              │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│   OR                                                                 │
│                                                                      │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │           DYNAMODB GLOBAL TABLES                             │   │
│   │                                                              │   │
│   │   Active-Active replication across all regions              │   │
│   │   Sub-second replication                                    │   │
│   │   + DAX in each region for microsecond reads                │   │
│   │                                                              │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

# 📝 EXAM SCENARIOS

## Scenario 1: Web Application Performance

**Question:** A company's web application is experiencing slow response times during peak hours. The application runs on EC2 instances behind an ALB, with an RDS MySQL database. Which solutions would improve performance?

**Answer Options:**
1. Add Read Replicas for RDS and route read queries to them ✅
2. Implement ElastiCache Redis for session and query caching ✅
3. Use CloudFront for static content delivery ✅
4. Move to Aurora with Serverless v2 ✅
5. Increase EC2 instance sizes only ❌ (Partial solution)

---

## Scenario 2: DynamoDB Performance Issues

**Question:** A gaming application uses DynamoDB with Provisioned capacity. Users report intermittent throttling errors. What should the team investigate?

**Answer Options:**
1. Hot partition key design (most writes to few partitions) ✅
2. Switch to On-Demand capacity mode ✅
3. Add DAX for read caching ✅
4. Increase RCU/WCU uniformly ❌ (Doesn't fix hot partitions)

**Key Insight:** Throttling is often due to hot partitions, not overall capacity.

---

## Scenario 3: Lambda Cold Starts

**Question:** A company's serverless API (Lambda + API Gateway) experiences occasional high latency due to cold starts. What can reduce cold start impact?

**Answer Options:**
1. Use Provisioned Concurrency ✅
2. Keep Lambda functions warm with CloudWatch Events ✅
3. Reduce deployment package size ✅
4. Increase Lambda memory allocation ✅
5. Move to containers only ❌ (Containers have warm-up too)

---

## Scenario 4: Global Application Latency

**Question:** A company wants to serve users globally with the lowest latency for a TCP-based application. What should they use?

**Answer Options:**
1. AWS Global Accelerator ✅ (TCP/UDP, not HTTP-only)
2. CloudFront ❌ (Better for HTTP/HTTPS content)
3. Route 53 Latency Routing alone ❌ (Still uses public internet)

**Key Insight:** Global Accelerator uses AWS backbone for non-HTTP protocols.

---

## Scenario 5: Database Selection

**Question:** A company needs a database for their e-commerce platform that can:
- Handle 100,000+ transactions per second
- Provide single-digit millisecond latency
- Scale globally

**Answer:** DynamoDB with Global Tables

- DynamoDB: Handles massive scale with consistent performance
- Global Tables: Multi-region active-active replication
- Add DAX: For microsecond reads (product catalog caching)

---

## Scenario 6: Storage Performance

**Question:** A video processing application needs storage that can deliver 500,000 IOPS. What storage solution should they use?

**Answer Options:**
1. io2 Block Express EBS volume ✅ (Up to 256,000 IOPS per volume)
2. Multiple io2 volumes with RAID 0 ✅ (Aggregate IOPS)
3. Instance store on storage-optimized instances ✅ (Very high IOPS)
4. gp3 volumes ❌ (Max 16,000 IOPS per volume)

---

## Scenario 7: Analytics Performance

**Question:** A company runs SQL queries on 10TB of data stored in S3. Queries are slow (30+ minutes). How can they improve query performance?

**Answer Options:**
1. Convert data to Parquet (columnar) format ✅
2. Partition data by commonly queried fields ✅
3. Use Athena federated query ❌ (Not for large analytics)
4. Move to Redshift Spectrum for heavy queries ✅
5. Compress the data files ✅

---

# ⚡ INSTANT ANSWER TABLE - PERFORMANCE

| Scenario | Instant Answer |
|----------|----------------|
| Scale database reads | **Read Replicas** |
| Lambda + RDS connection pooling | **RDS Proxy** |
| DynamoDB microsecond reads | **DAX** |
| In-memory caching | **ElastiCache Redis** |
| Session store | **ElastiCache Redis** |
| Global content delivery | **CloudFront** |
| Global TCP/UDP acceleration | **Global Accelerator** |
| High IOPS storage (>64K) | **io2 Block Express** |
| Highest storage throughput | **Instance Store** |
| S3 upload large files | **Multipart Upload** |
| S3 fast global upload | **Transfer Acceleration** |
| Variable database workload | **Aurora Serverless v2** |
| Cross-region database | **Aurora Global Database** |
| Global DynamoDB | **Global Tables** |
| Reduce cold starts | **Provisioned Concurrency** |
| Edge computing (simple) | **CloudFront Functions** |
| Edge computing (complex) | **Lambda@Edge** |
| EFS high throughput | **Provisioned Throughput** or **Max I/O** |
| Analytics on S3 data | **Athena with Parquet + Partitions** |
| Real-time streaming | **Kinesis Data Streams** |

---

# 📋 PERFORMANCE OPTIMIZATION CHECKLIST

## Compute
- [ ] Right-sized instances for workload
- [ ] Placement groups for network-intensive
- [ ] Enhanced networking enabled
- [ ] Auto Scaling configured
- [ ] Provisioned Concurrency for Lambda (if needed)

## Storage
- [ ] Correct EBS volume type for IOPS/throughput
- [ ] EBS-optimized instances enabled
- [ ] S3 multipart upload for large objects
- [ ] S3 Transfer Acceleration for global
- [ ] CloudFront for static content

## Database
- [ ] Read replicas for read scaling
- [ ] RDS Proxy for connection pooling
- [ ] ElastiCache for caching
- [ ] DAX for DynamoDB caching
- [ ] Proper partition key design (DynamoDB)

## Network
- [ ] CloudFront for content delivery
- [ ] Global Accelerator for non-HTTP
- [ ] VPC endpoints for AWS services
- [ ] Route 53 latency routing

---

# 🎯 FINAL EXAM TIPS - PERFORMANCE DOMAIN

1. **Read the question carefully** - identify if it's about compute, storage, database, or network performance

2. **Look for keywords:**
   - "Latency" → Caching, CDN, or edge
   - "Throughput" → Scaling, instance types, storage types
   - "IOPS" → EBS volume types, instance store
   - "Global" → CloudFront, Global Accelerator, Global Tables

3. **Caching is often the answer:**
   - Web content → CloudFront
   - Database queries → ElastiCache
   - DynamoDB → DAX
   - API responses → API Gateway caching

4. **Know the limits:**
   - gp3: 16,000 IOPS
   - io2: 64,000 IOPS
   - io2 Block Express: 256,000 IOPS
   - S3: 3,500 PUT, 5,500 GET per prefix

5. **Aurora vs RDS:**
   - Need > 5 read replicas → Aurora
   - Need serverless → Aurora Serverless v2
   - Standard workload → RDS is fine

6. **DynamoDB performance:**
   - Throttling → Check partition key design first
   - Read-heavy → Add DAX
   - Global → Global Tables

---

*Last Updated: December 2025*
*Exam Version: SAA-C03*
*Domain: Design High-Performing Architectures (24%)*
