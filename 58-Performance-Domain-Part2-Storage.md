# AWS SAA-C03: DESIGN HIGH-PERFORMING ARCHITECTURES - PART 2

## 📋 STORAGE PERFORMANCE & OPTIMIZATION

---

# 💾 EBS (Elastic Block Store) PERFORMANCE

## Definition
**Amazon EBS** provides block-level storage volumes for use with EC2 instances. Performance varies by volume type.

---

## EBS Volume Types Comparison

| Type | Category | Max IOPS | Max Throughput | Size | Use Case |
|------|----------|----------|----------------|------|----------|
| **gp3** | SSD | 16,000 | 1,000 MB/s | 1 GiB - 16 TiB | General purpose |
| **gp2** | SSD | 16,000 | 250 MB/s | 1 GiB - 16 TiB | General (legacy) |
| **io2 Block Express** | SSD | 256,000 | 4,000 MB/s | 4 GiB - 64 TiB | Critical I/O |
| **io2** | SSD | 64,000 | 1,000 MB/s | 4 GiB - 16 TiB | High IOPS |
| **io1** | SSD | 64,000 | 1,000 MB/s | 4 GiB - 16 TiB | High IOPS (legacy) |
| **st1** | HDD | 500 | 500 MB/s | 125 GiB - 16 TiB | Throughput |
| **sc1** | HDD | 250 | 250 MB/s | 125 GiB - 16 TiB | Cold data |

---

## Volume Type Details

### GP3 (General Purpose SSD)

```
┌─────────────────────────────────────────────────────────────────────┐
│                    GP3 VOLUME                                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   BASELINE PERFORMANCE (included):                                   │
│   • 3,000 IOPS                                                       │
│   • 125 MB/s throughput                                              │
│                                                                      │
│   PROVISIONED PERFORMANCE (additional cost):                         │
│   • Up to 16,000 IOPS                                                │
│   • Up to 1,000 MB/s throughput                                      │
│   • IOPS and throughput can be provisioned INDEPENDENTLY            │
│                                                                      │
│   COMPARISON WITH GP2:                                               │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │ GP2: IOPS = 3 × Size (GiB), min 100, max 16,000             │   │
│   │      Need 5,334 GiB volume for 16,000 IOPS                  │   │
│   │                                                              │   │
│   │ GP3: 16,000 IOPS available on ANY size (1 GiB+)             │   │
│   │      20% cheaper baseline than GP2                          │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│   KEY EXAM POINT: GP3 is preferred over GP2 for new workloads       │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### IO2 / IO2 Block Express (Provisioned IOPS SSD)

| Feature | io2 | io2 Block Express |
|---------|-----|-------------------|
| **Max IOPS** | 64,000 | 256,000 |
| **Max Throughput** | 1,000 MB/s | 4,000 MB/s |
| **Max Size** | 16 TiB | 64 TiB |
| **IOPS:GiB Ratio** | 500:1 | 1,000:1 |
| **Durability** | 99.999% | 99.999% |
| **Multi-Attach** | Yes | Yes |
| **Use Case** | High IOPS databases | Extreme performance |

### ST1 / SC1 (HDD)

| Feature | st1 (Throughput) | sc1 (Cold) |
|---------|------------------|------------|
| **Max IOPS** | 500 | 250 |
| **Max Throughput** | 500 MB/s | 250 MB/s |
| **Baseline** | 40 MB/s per TiB | 12 MB/s per TiB |
| **Burst** | 250 MB/s per TiB | 80 MB/s per TiB |
| **Boot Volume** | No | No |
| **Use Case** | Big data, log processing | Infrequent access |

---

## EBS Performance Optimization

### EBS-Optimized Instances

| Feature | Description |
|---------|-------------|
| **Definition** | Dedicated bandwidth for EBS |
| **Benefit** | Consistent EBS performance |
| **Default** | Enabled by default on most current gen |
| **Bandwidth** | Up to 19,000 Mbps (instance dependent) |

### RAID Configurations

| RAID | Description | Use Case |
|------|-------------|----------|
| **RAID 0** | Striping (aggregate performance) | Max IOPS/throughput |
| **RAID 1** | Mirroring (redundancy) | Not recommended (use Multi-AZ) |

```
RAID 0 Example:
4 × io2 volumes (64,000 IOPS each) = 256,000 IOPS total
```

### EBS Multi-Attach

| Feature | Details |
|---------|---------|
| **Supported** | io1, io2 volumes only |
| **Max Instances** | 16 in same AZ |
| **Use Case** | Clustered applications |
| **Requirement** | Cluster-aware file system |

---

## EBS Selection Guide

| Requirement | Volume Type | Reason |
|-------------|-------------|--------|
| General purpose | **gp3** | Best price/performance |
| Boot volume (simple) | **gp3** | Cost-effective |
| Database (general) | **gp3** (up to 16K IOPS) | Sufficient for most DBs |
| Database (high IOPS) | **io2** | Up to 64K IOPS |
| Database (extreme) | **io2 Block Express** | Up to 256K IOPS |
| Big data throughput | **st1** | 500 MB/s, low cost |
| Archive/cold data | **sc1** | Lowest cost |
| Multi-attach cluster | **io2** | Only SSD with multi-attach |

---

## Exam Heuristics

| Keyword/Scenario | Answer |
|------------------|--------|
| General purpose storage | **gp3** |
| Cost-effective SSD | **gp3** |
| High IOPS (up to 64K) | **io2** |
| Extreme IOPS (256K) | **io2 Block Express** |
| Big data throughput | **st1** |
| Infrequent access, lowest cost | **sc1** |
| Database requiring consistent IOPS | **io2** |
| Share volume between instances | **io2 Multi-Attach** |
| Migrate gp2 to gp3 | **Modify volume type (no downtime)** |
| Increase IOPS without larger volume | **gp3 (provision independently)** |

---

# 📦 S3 PERFORMANCE

## Definition
**Amazon S3** provides object storage with high durability (99.999999999%) and varying performance based on access patterns.

---

## S3 Performance Baseline

| Metric | Performance |
|--------|-------------|
| **PUT/COPY/POST/DELETE** | 3,500 requests/second per prefix |
| **GET/HEAD** | 5,500 requests/second per prefix |
| **Object Size** | 0 bytes - 5 TB |
| **Multipart Upload** | Required for >5 GB |

---

## S3 Performance Optimization

### Prefix Distribution

```
┌─────────────────────────────────────────────────────────────────────┐
│                    S3 PREFIX OPTIMIZATION                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   POOR PERFORMANCE (single prefix):                                  │
│   s3://bucket/images/image1.jpg                                     │
│   s3://bucket/images/image2.jpg                                     │
│   s3://bucket/images/image3.jpg                                     │
│   → Limited to 3,500 writes + 5,500 reads per second                │
│                                                                      │
│   OPTIMIZED (multiple prefixes):                                     │
│   s3://bucket/images/2024/01/image1.jpg  → 3,500 + 5,500            │
│   s3://bucket/images/2024/02/image2.jpg  → 3,500 + 5,500            │
│   s3://bucket/images/2024/03/image3.jpg  → 3,500 + 5,500            │
│   s3://bucket/images/2024/04/image4.jpg  → 3,500 + 5,500            │
│   → Each prefix gets full performance allocation                     │
│                                                                      │
│   TOTAL: 4 prefixes × (3,500 + 5,500) = 36,000 req/sec             │
│                                                                      │
│   PREFIX = everything between bucket name and object key            │
│   s3://bucket/ [PREFIX] /object.ext                                 │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Multipart Upload

| Feature | Details |
|---------|---------|
| **Minimum Size** | >100 MB recommended |
| **Required** | >5 GB |
| **Part Size** | 5 MB - 5 GB |
| **Max Parts** | 10,000 |
| **Benefit** | Parallel uploads, resume on failure |

```
┌─────────────────────────────────────────────────────────────────────┐
│                    MULTIPART UPLOAD                                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   SINGLE UPLOAD:                                                     │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │         5 GB File → Single stream → S3                       │   │
│   │         If fails at 4.9 GB, start over                       │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│   MULTIPART UPLOAD:                                                  │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │  5 GB File split into parts:                                 │   │
│   │                                                              │   │
│   │  Part 1 (100 MB) ──────┐                                    │   │
│   │  Part 2 (100 MB) ──────┤                                    │   │
│   │  Part 3 (100 MB) ──────┼──────▶ S3 (parallel upload)       │   │
│   │  Part 4 (100 MB) ──────┤                                    │   │
│   │  ...                   │                                    │   │
│   │  Part N (100 MB) ──────┘                                    │   │
│   │                                                              │   │
│   │  If Part 3 fails, only retry Part 3                         │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### S3 Transfer Acceleration

| Feature | Details |
|---------|---------|
| **How** | Uses CloudFront edge locations |
| **Benefit** | Faster long-distance transfers |
| **Use Case** | Upload from distant locations |
| **Cost** | Additional per GB |
| **Endpoint** | bucketname.s3-accelerate.amazonaws.com |

```
┌─────────────────────────────────────────────────────────────────────┐
│                    S3 TRANSFER ACCELERATION                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   WITHOUT TRANSFER ACCELERATION:                                     │
│                                                                      │
│   User (Sydney) ─────── Internet (slow) ─────▶ S3 (us-east-1)      │
│                                                                      │
│   WITH TRANSFER ACCELERATION:                                        │
│                                                                      │
│   User (Sydney) ──▶ CloudFront Edge ══════▶ S3 (us-east-1)         │
│                     (Sydney)        AWS Backbone                     │
│                                     (fast, optimized)               │
│                                                                      │
│   SPEED IMPROVEMENT: 50-500% for distant uploads                    │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### S3 Byte-Range Fetches

| Feature | Details |
|---------|---------|
| **How** | Request specific byte ranges |
| **Benefit** | Parallel downloads, partial retrieval |
| **Use Case** | Large files, partial processing |

---

## S3 Select & Glacier Select

| Feature | S3 Select | Glacier Select |
|---------|-----------|----------------|
| **Purpose** | Query data in-place | Query archived data |
| **Format** | CSV, JSON, Parquet | CSV, JSON |
| **Benefit** | Reduce data transfer | Query without restore |
| **Cost Saving** | Up to 400% | Avoid full retrieval |

---

## Exam Heuristics

| Keyword/Scenario | Answer |
|------------------|--------|
| S3 high request rate | **Spread across prefixes** |
| Upload large file (>5GB) | **Multipart Upload** |
| Upload from distant location | **S3 Transfer Acceleration** |
| Download large file faster | **Byte-Range Fetches (parallel)** |
| Query S3 data without download | **S3 Select** |
| Query Glacier without restore | **Glacier Select** |
| >100 MB uploads | **Multipart Upload recommended** |

---

# 📁 INSTANCE STORE

## Definition
**Instance Store** provides temporary block-level storage physically attached to the host computer. Data is lost when instance stops/terminates.

---

## Instance Store vs EBS

| Feature | Instance Store | EBS |
|---------|----------------|-----|
| **Persistence** | Ephemeral (lost on stop) | Persistent |
| **Performance** | Very high (local NVMe) | High (network) |
| **Latency** | Lowest | Low |
| **Use Case** | Temporary data, cache, buffer | Persistent data |
| **Cost** | Included with instance | Per GB/month + IOPS |
| **Backup** | Manual (to S3/EBS) | Snapshots |

---

## Instance Store Performance

| Instance | Store Type | IOPS | Throughput |
|----------|------------|------|------------|
| **i3.16xlarge** | NVMe SSD | 3.3M random read | 16 GB/s |
| **i3en.24xlarge** | NVMe SSD | 2M random read | 16 GB/s |
| **d3en.12xlarge** | HDD | Sequential only | 6.2 GB/s |
| **i4i.32xlarge** | NVMe SSD | 2.5M random read | 20 GB/s |

---

## Exam Heuristics

| Keyword/Scenario | Answer |
|------------------|--------|
| Highest IOPS storage | **Instance Store (NVMe)** |
| Temporary/cache data | **Instance Store** |
| Data must persist | **EBS (not instance store)** |
| Buffer for data processing | **Instance Store** |
| Lowest latency storage | **Instance Store** |

---

# 📂 FILE SYSTEM PERFORMANCE

## EFS (Elastic File System)

### Definition
**Amazon EFS** is a fully managed, elastic, shared file system for Linux workloads.

### Performance Modes

| Mode | Latency | Throughput | Use Case |
|------|---------|------------|----------|
| **General Purpose** | < 1 ms | Up to 3 GB/s | Most workloads |
| **Max I/O** | Slightly higher | Unlimited | Thousands of clients |

### Throughput Modes

| Mode | How It Works | Use Case |
|------|--------------|----------|
| **Bursting** | Scales with size (50 MB/s per TB) | Variable workloads |
| **Provisioned** | Set explicit throughput | Consistent high throughput |
| **Elastic** | Auto-scales throughput | Unpredictable workloads |

```
┌─────────────────────────────────────────────────────────────────────┐
│                    EFS THROUGHPUT MODES                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   BURSTING:                                                          │
│   • Baseline: 50 KB/s per GiB of storage                            │
│   • Burst: Up to 100 MB/s per TiB (using burst credits)             │
│   • 1 TiB file system → 50 MB/s baseline, burst to 100 MB/s        │
│                                                                      │
│   PROVISIONED:                                                       │
│   • Set throughput independent of size                               │
│   • Up to 3 GB/s (General Purpose) or 10+ GB/s (Max I/O)           │
│   • Use when throughput > storage size allows                        │
│                                                                      │
│   ELASTIC (NEW):                                                     │
│   • Automatically scales based on workload                           │
│   • Up to 3 GB/s read, 1 GB/s write                                 │
│   • Pay for what you use                                             │
│   • Best for spiky/unpredictable workloads                          │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### EFS Storage Classes

| Class | Description | Use Case |
|-------|-------------|----------|
| **Standard** | Frequently accessed | Active data |
| **Standard-IA** | Infrequent access | Older files |
| **One Zone** | Single AZ (20% cheaper) | Dev/test, backups |
| **One Zone-IA** | Single AZ + IA | Cost-optimized cold data |

---

## FSx Performance

### FSx for Lustre

| Feature | Details |
|---------|---------|
| **Purpose** | HPC, ML, media processing |
| **Performance** | Hundreds of GB/s, millions of IOPS |
| **Integration** | Direct S3 integration |
| **Deployment** | Scratch (temporary) or Persistent |

| Type | Performance | Durability | Use Case |
|------|-------------|------------|----------|
| **Scratch** | 200 MB/s per TiB | Not replicated | Short-term processing |
| **Persistent** | 50-200 MB/s per TiB | Replicated in AZ | Long-term HPC |

### FSx for Windows

| Feature | Details |
|---------|---------|
| **Purpose** | Windows workloads |
| **Protocol** | SMB |
| **Performance** | Up to 2 GB/s, 80K IOPS |
| **Features** | DFS, AD integration |

### FSx for NetApp ONTAP

| Feature | Details |
|---------|---------|
| **Purpose** | Enterprise workloads |
| **Protocols** | NFS, SMB, iSCSI |
| **Features** | Snapshots, clones, compression |
| **Multi-AZ** | Supported |

### FSx for OpenZFS

| Feature | Details |
|---------|---------|
| **Purpose** | Linux workloads |
| **Performance** | Up to 12.5 GB/s, 1M IOPS |
| **Features** | Snapshots, clones, compression |
| **Migration** | From on-prem ZFS |

---

## File System Selection

| Requirement | Service | Reason |
|-------------|---------|--------|
| Linux shared storage | **EFS** | Managed NFS |
| Windows shared storage | **FSx for Windows** | SMB protocol |
| HPC/ML high performance | **FSx for Lustre** | Highest throughput |
| Multi-protocol (NFS+SMB) | **FSx for ONTAP** | Enterprise features |
| ZFS migration | **FSx for OpenZFS** | ZFS compatibility |

---

## Exam Heuristics

| Keyword/Scenario | Answer |
|------------------|--------|
| Shared Linux file system | **EFS** |
| Shared Windows file system | **FSx for Windows** |
| HPC file system | **FSx for Lustre** |
| ML training data | **FSx for Lustre + S3** |
| Thousands of EC2 clients | **EFS Max I/O mode** |
| Variable file system throughput | **EFS Elastic throughput** |
| Cost-optimized file system | **EFS One Zone-IA** |
| SMB + AD integration | **FSx for Windows** |

---

# 🚀 CACHING STRATEGIES

## CloudFront (CDN)

### Definition
**Amazon CloudFront** is a content delivery network (CDN) that securely delivers data, videos, applications, and APIs to customers globally with low latency.

### Performance Features

| Feature | Description | Benefit |
|---------|-------------|---------|
| **Edge Locations** | 400+ worldwide | Low latency |
| **Regional Edge Caches** | Larger caches near regions | Longer cache retention |
| **Origin Shield** | Additional caching layer | Reduce origin load |
| **Lambda@Edge** | Run code at edge | Custom processing |
| **CloudFront Functions** | Lightweight edge functions | Sub-ms latency |

### Cache Optimization

```
┌─────────────────────────────────────────────────────────────────────┐
│                    CLOUDFRONT CACHING                                │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   User ──▶ Edge Location ──▶ Regional Edge ──▶ Origin              │
│                │                   │                │                │
│            Cache HIT          Cache HIT       Cache MISS            │
│            (fastest)          (fast)          (slowest)             │
│                                                                      │
│   CACHE KEY (what makes a unique cached object):                    │
│   • URL path                                                        │
│   • Query strings (configurable)                                    │
│   • Headers (configurable)                                          │
│   • Cookies (configurable)                                          │
│                                                                      │
│   OPTIMIZATION:                                                      │
│   • Minimize cache key variations                                   │
│   • Forward only necessary headers/cookies                          │
│   • Use Origin Shield for multiple regions                          │
│   • Set appropriate TTLs                                            │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### TTL Settings

| Setting | Description | Default |
|---------|-------------|---------|
| **Minimum TTL** | Lowest allowed TTL | 0 |
| **Maximum TTL** | Highest allowed TTL | 31,536,000 (1 year) |
| **Default TTL** | When origin doesn't specify | 86,400 (24 hours) |

---

## ElastiCache

### Definition
**Amazon ElastiCache** is a fully managed in-memory caching service supporting Redis and Memcached.

### Redis vs Memcached

| Feature | Redis | Memcached |
|---------|-------|-----------|
| **Data Structures** | Complex (lists, sets, hashes) | Simple (strings) |
| **Persistence** | Yes | No |
| **Replication** | Yes (Multi-AZ) | No |
| **Clustering** | Yes (up to 500 nodes) | Yes |
| **Pub/Sub** | Yes | No |
| **Backup/Restore** | Yes | No |
| **Transactions** | Yes | No |
| **Lua Scripting** | Yes | No |
| **Multi-threaded** | No (single-threaded) | Yes |

### Redis Cluster Mode

| Mode | Description | Use Case |
|------|-------------|----------|
| **Cluster Mode Disabled** | Single shard, up to 5 replicas | Simple caching |
| **Cluster Mode Enabled** | Multiple shards, data partitioned | Large datasets, high throughput |

```
┌─────────────────────────────────────────────────────────────────────┐
│                    REDIS CLUSTER MODE                                │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   CLUSTER MODE DISABLED:                                             │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │                    SINGLE SHARD                              │   │
│   │   ┌────────┐     ┌────────┐     ┌────────┐                  │   │
│   │   │Primary │────▶│Replica │────▶│Replica │                  │   │
│   │   │        │     │   1    │     │   2    │                  │   │
│   │   └────────┘     └────────┘     └────────┘                  │   │
│   │   All data on one shard, max ~500 nodes across replicas     │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│   CLUSTER MODE ENABLED:                                              │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │   Shard 1           Shard 2           Shard 3               │   │
│   │   ┌────────┐        ┌────────┐        ┌────────┐           │   │
│   │   │Primary │        │Primary │        │Primary │           │   │
│   │   │ Data A │        │ Data B │        │ Data C │           │   │
│   │   └───┬────┘        └───┬────┘        └───┬────┘           │   │
│   │       │                 │                 │                 │   │
│   │   ┌───▼────┐        ┌───▼────┐        ┌───▼────┐           │   │
│   │   │Replica │        │Replica │        │Replica │           │   │
│   │   └────────┘        └────────┘        └────────┘           │   │
│   │   Data partitioned across shards, scales writes            │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Caching Strategies

| Strategy | Description | Use Case |
|----------|-------------|----------|
| **Lazy Loading** | Load data on cache miss | Read-heavy, stale data OK |
| **Write-Through** | Update cache on write | Data consistency important |
| **TTL** | Expire cached data | Balance freshness/performance |

```
┌─────────────────────────────────────────────────────────────────────┐
│                    CACHING STRATEGIES                                │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   LAZY LOADING (Cache-Aside):                                        │
│                                                                      │
│   App ──▶ Check Cache ──▶ HIT? ──▶ Return cached data              │
│               │                                                      │
│               ▼ MISS                                                 │
│           Query DB ──▶ Store in Cache ──▶ Return data              │
│                                                                      │
│   ✓ Only requested data cached                                      │
│   ✓ Cache failures not critical                                     │
│   ✗ Initial request slow (cache miss)                               │
│   ✗ Stale data possible                                             │
│                                                                      │
│   WRITE-THROUGH:                                                     │
│                                                                      │
│   App ──▶ Write to Cache ──▶ Write to DB                           │
│                                                                      │
│   ✓ Cache always consistent with DB                                 │
│   ✓ Read latency always low                                         │
│   ✗ Write latency higher                                            │
│   ✗ Unused data may be cached                                       │
│                                                                      │
│   BEST PRACTICE: Lazy Loading + TTL + Write-Through for updates    │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Exam Heuristics

| Keyword/Scenario | Answer |
|------------------|--------|
| Static content delivery | **CloudFront** |
| Global content distribution | **CloudFront** |
| Reduce origin load | **CloudFront Origin Shield** |
| Edge computing | **Lambda@Edge or CloudFront Functions** |
| Database caching | **ElastiCache** |
| Session storage | **ElastiCache Redis** |
| Simple caching, multi-threaded | **ElastiCache Memcached** |
| Complex data structures cache | **ElastiCache Redis** |
| Cache with persistence | **ElastiCache Redis** |
| Large cache dataset | **ElastiCache Redis Cluster Mode** |
| Leaderboards, sorted data | **ElastiCache Redis (sorted sets)** |
| Pub/sub messaging | **ElastiCache Redis** |

---

# ✅ STORAGE PERFORMANCE EXAM HEURISTICS

| Keyword/Scenario | Answer |
|------------------|--------|
| General purpose SSD | **gp3** |
| High IOPS database | **io2 or io2 Block Express** |
| Big data throughput | **st1** |
| Cold data, lowest cost | **sc1** |
| Shared volume (cluster) | **io2 Multi-Attach** |
| Highest possible IOPS | **Instance Store (NVMe)** |
| Temporary/cache storage | **Instance Store** |
| S3 high throughput | **Multipart Upload + Transfer Acceleration** |
| S3 many requests | **Spread across prefixes** |
| Linux shared file system | **EFS** |
| Windows shared file system | **FSx for Windows** |
| HPC file system | **FSx for Lustre** |
| Global content delivery | **CloudFront** |
| Database caching | **ElastiCache** |
| Session management | **ElastiCache Redis** |

---

*Continued in Part 3: Database & Network Performance*

---

*Last Updated: December 2025*
*Exam Version: SAA-C03*
*Domain: Design High-Performing Architectures (24%)*
