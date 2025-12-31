# AWS SAA-C03 CHEATSHEET: DATABASE SERVICES

## 📋 PRINT-READY EXAM REFERENCE

---

# 🗃️ AMAZON RDS (RELATIONAL DATABASE SERVICE)

## Definition
Managed relational database service supporting multiple database engines.

## Supported Engines

| Engine | Description | Use Case |
|--------|-------------|----------|
| **MySQL** | Open-source RDBMS | Web applications |
| **PostgreSQL** | Advanced open-source | Complex queries, GIS |
| **MariaDB** | MySQL fork | MySQL alternative |
| **Oracle** | Enterprise DB | Legacy enterprise apps |
| **SQL Server** | Microsoft DB | Windows/.NET apps |
| **Aurora** | AWS cloud-native | High performance |

## RDS Features

| Feature | Description |
|---------|-------------|
| **Multi-AZ** | Synchronous standby in another AZ |
| **Read Replicas** | Async replication for reads |
| **Automated backups** | Daily snapshots + transaction logs |
| **Manual snapshots** | User-initiated, persist until deleted |
| **Encryption** | At-rest (KMS), in-transit (SSL) |
| **IAM DB Auth** | MySQL, PostgreSQL authentication |

## Multi-AZ Deployments

| Type | Description | Failover |
|------|-------------|----------|
| **Multi-AZ Instance** | Single standby | Auto failover (1-2 min) |
| **Multi-AZ Cluster** | 2 readable standbys | Faster failover (< 35 sec) |

### Multi-AZ Key Points
- **Synchronous replication**
- **Automatic failover** on primary failure
- **Same DNS endpoint** - no application changes
- **Standby NOT for reads** (use Read Replicas)
- **Use case**: High availability, disaster recovery

## Read Replicas

| Feature | Description |
|---------|-------------|
| **Replication** | Asynchronous |
| **Max replicas** | 15 (Aurora), 5 (others) |
| **Cross-region** | Supported |
| **Promotion** | Can promote to standalone DB |
| **Use case** | Read scaling, analytics |

### Read Replica Key Points
- **Network cost**: Free within same region, charged cross-region
- **Can create replica of replica** (increases lag)
- **Replica can be Multi-AZ**
- **Not for write scaling**

## RDS Storage

| Type | Description | Use Case |
|------|-------------|----------|
| **gp2** | General purpose SSD | Most workloads |
| **gp3** | Configurable IOPS/throughput | Flexible performance |
| **io1** | Provisioned IOPS | High performance |
| **Magnetic** | Legacy | Legacy only |

### Storage Auto Scaling
- Automatically increases storage
- Set **maximum storage threshold**
- Triggers when storage < 10% free
- 6+ hours since last modification
- 5 minutes since last scaling

## RDS Backups

| Type | Description | Retention |
|------|-------------|-----------|
| **Automated** | Daily + transaction logs | 1-35 days |
| **Manual Snapshots** | User-initiated | Until deleted |

### Backup Key Points
- **PITR**: Restore to any point (5-min granularity)
- **Snapshot restore**: Creates new DB instance
- **Copy snapshots**: Cross-region for DR
- **Share snapshots**: With other accounts

## RDS Security

| Layer | Options |
|-------|---------|
| **Network** | VPC, Security Groups, Private subnets |
| **Encryption at rest** | KMS (enable at creation) |
| **Encryption in transit** | SSL/TLS |
| **Authentication** | Password, IAM, Kerberos |
| **Audit** | CloudWatch, RDS Event Notifications |

### Encryption Key Points
- Enable at **creation only** (cannot enable later)
- **Unencrypted to encrypted**: Snapshot → Copy with encryption → Restore
- Read replicas encrypted if master encrypted
- Cross-region replicas use different KMS key

## RDS Proxy

| Feature | Description |
|---------|-------------|
| **Purpose** | Connection pooling |
| **Benefit** | Reduce DB connections by 66% |
| **Failover** | 66% faster failover |
| **IAM auth** | Enforce IAM authentication |
| **Use case** | Lambda, serverless apps |

---

# ✨ AMAZON AURORA

## Definition
AWS cloud-native database compatible with MySQL and PostgreSQL, 5x faster than MySQL.

## Aurora Architecture

| Component | Description |
|-----------|-------------|
| **Cluster volume** | 6 copies across 3 AZs |
| **Primary instance** | Read/write |
| **Replica instances** | Read-only (up to 15) |
| **Writer endpoint** | Points to primary |
| **Reader endpoint** | Load balances across replicas |
| **Custom endpoint** | User-defined instance groups |

## Aurora Features

| Feature | Description |
|---------|-------------|
| **Auto-scaling storage** | 10 GB to 128 TB |
| **Self-healing** | Auto repairs data blocks |
| **Failover** | < 30 seconds |
| **Backtrack** | Rewind DB without restore |
| **Clone** | Fast copy using copy-on-write |
| **Parallel Query** | Speed up analytics |

## Aurora High Availability

| Feature | Description |
|---------|-------------|
| **6 copies of data** | Across 3 AZs |
| **4/6 writes** | Need 4 copies for write |
| **3/6 reads** | Need 3 copies for read |
| **Auto failover** | Promotes replica in < 30 sec |
| **Self-healing** | Peer-to-peer replication |

## Aurora Replicas vs RDS Read Replicas

| Feature | Aurora | RDS |
|---------|--------|-----|
| **Max replicas** | 15 | 5 |
| **Replication** | Storage-level (fast) | DB-level (slower) |
| **Failover** | Automatic | Manual |
| **Lag** | Milliseconds | Seconds |

## Aurora Serverless

| Feature | Description |
|---------|-------------|
| **v1** | On-demand, auto-scaling |
| **v2** | More granular, instant scaling |
| **ACU** | Aurora Capacity Units (2 GB each) |
| **Use case** | Variable workloads, dev/test |

### Aurora Serverless v2
- Scale from **0.5 to 128 ACUs**
- Sub-second scaling
- Same endpoint as provisioned
- **Instant scaling** (no warm-up)

## Aurora Global Database

| Feature | Description |
|---------|-------------|
| **Replication** | < 1 second cross-region |
| **Secondary regions** | Up to 5 |
| **Promotion** | RTO < 1 minute |
| **Use case** | DR, global reads |

### Global Database Key Points
- **Primary region**: Read/write
- **Secondary regions**: Read-only
- **RPO**: < 1 second
- **RTO**: < 1 minute (promoted manually)

## Aurora Machine Learning

| Integration | Description |
|-------------|-------------|
| **SageMaker** | Call ML models via SQL |
| **Comprehend** | Sentiment analysis |
| **Use case** | Fraud detection, recommendations |

---

# 🔥 AMAZON DYNAMODB

## Definition
Fully managed NoSQL database with single-digit millisecond performance at any scale.

## DynamoDB Concepts

| Concept | Description |
|---------|-------------|
| **Table** | Collection of items |
| **Item** | Collection of attributes (row) |
| **Attribute** | Key-value pair (column) |
| **Partition Key** | Primary key hash |
| **Sort Key** | Optional range key |
| **Secondary Index** | Alternate query patterns |

## Primary Key Types

| Type | Description | Example |
|------|-------------|---------|
| **Partition Key** | Single attribute | user_id |
| **Composite** | Partition + Sort | user_id + timestamp |

## Capacity Modes

| Mode | Description | Use Case |
|------|-------------|----------|
| **Provisioned** | Specify RCU/WCU | Predictable workloads |
| **On-Demand** | Pay per request | Variable, unpredictable |

### Capacity Units

| Unit | Description |
|------|-------------|
| **RCU** | 1 strongly consistent read (4 KB) per second |
| **RCU** | 2 eventually consistent reads (4 KB) per second |
| **WCU** | 1 write (1 KB) per second |

### Provisioned Capacity
- **Auto Scaling**: Scale RCU/WCU automatically
- **Reserved Capacity**: Commit for discounts
- **Burst capacity**: 300 seconds of unused capacity

## DynamoDB Indexes

| Type | Description | Limit |
|------|-------------|-------|
| **LSI** | Local Secondary Index | 5 per table |
| **GSI** | Global Secondary Index | 20 per table |

### Index Differences

| Feature | LSI | GSI |
|---------|-----|-----|
| **When created** | At table creation | Anytime |
| **Partition key** | Same as table | Different allowed |
| **Sort key** | Different | Different allowed |
| **Consistency** | Strong/eventual | Eventual only |
| **Capacity** | Shares table capacity | Own capacity |

## DynamoDB Streams

| Feature | Description |
|---------|-------------|
| **Purpose** | Capture item-level changes |
| **Retention** | 24 hours |
| **Integration** | Lambda, Kinesis |
| **Use case** | Replication, triggers, analytics |

### Stream View Types
- `KEYS_ONLY` - Only keys
- `NEW_IMAGE` - New item
- `OLD_IMAGE` - Old item
- `NEW_AND_OLD_IMAGES` - Both

## DynamoDB Global Tables

| Feature | Description |
|---------|-------------|
| **Type** | Multi-region, multi-master |
| **Replication** | Automatic, < 1 second |
| **Conflict** | Last writer wins |
| **Requirement** | Streams must be enabled |
| **Use case** | Global apps, low latency |

## DynamoDB TTL

| Feature | Description |
|---------|-------------|
| **Purpose** | Auto-delete expired items |
| **Cost** | Free |
| **Latency** | ~48 hours after expiry |
| **Use case** | Session data, temp data |

## DynamoDB Backup

| Type | Description |
|------|-------------|
| **On-demand** | Manual, full backups |
| **PITR** | 35-day continuous backup |
| **Restore** | Always to new table |

## DynamoDB DAX (Accelerator)

| Feature | Description |
|---------|-------------|
| **Type** | In-memory cache |
| **Latency** | Microseconds |
| **Compatibility** | API compatible |
| **Use case** | Read-heavy, repeated reads |

### DAX vs ElastiCache

| Feature | DAX | ElastiCache |
|---------|-----|-------------|
| **For** | DynamoDB only | Any application |
| **API changes** | None | Requires code changes |
| **Managed** | Fully | Fully |

## DynamoDB Security

| Feature | Description |
|---------|-------------|
| **Encryption** | Always encrypted (KMS) |
| **IAM** | Fine-grained access control |
| **VPC Endpoints** | Private access |
| **Audit** | CloudTrail |

---

# 💾 AMAZON ELASTICACHE

## Definition
Managed in-memory caching service (Redis or Memcached).

## Engine Comparison

| Feature | Redis | Memcached |
|---------|-------|-----------|
| **Data structures** | Complex (lists, sets, hashes) | Simple (strings) |
| **Persistence** | Yes | No |
| **Replication** | Yes | No |
| **Multi-AZ** | Yes | No |
| **Backup** | Yes | No |
| **Cluster mode** | Yes | Yes |
| **Multi-threaded** | No | Yes |
| **Use case** | HA, complex data | Simple caching |

## Redis Features

| Feature | Description |
|---------|-------------|
| **Cluster Mode Disabled** | 1 shard, up to 5 replicas |
| **Cluster Mode Enabled** | Up to 500 shards |
| **Multi-AZ** | Automatic failover |
| **Global Datastore** | Cross-region replication |
| **Auth** | Redis AUTH + IAM |

## Caching Strategies

| Strategy | Description | Use Case |
|----------|-------------|----------|
| **Lazy Loading** | Load on cache miss | Read-heavy |
| **Write-Through** | Write to cache and DB | Data consistency |
| **TTL** | Expire cached data | Prevent stale data |

## ElastiCache Use Cases

| Use Case | Description |
|----------|-------------|
| **Session store** | Store user sessions |
| **DB cache** | Cache frequent queries |
| **Leaderboards** | Redis sorted sets |
| **Message queues** | Redis pub/sub |

---

# 📊 AMAZON REDSHIFT

## Definition
Fully managed petabyte-scale data warehouse for analytics.

## Redshift Architecture

| Component | Description |
|-----------|-------------|
| **Leader Node** | Query planning, result aggregation |
| **Compute Nodes** | Execute queries, store data |
| **Node Slices** | Partitions of compute node |

## Node Types

| Type | Description | Use Case |
|------|-------------|----------|
| **RA3** | Managed storage (S3) | Variable storage needs |
| **DC2** | Dense compute (SSD) | < 500 GB hot data |
| **DS2** | Dense storage (HDD) | Legacy, avoid |

## Redshift Features

| Feature | Description |
|---------|-------------|
| **Columnar storage** | Efficient analytics |
| **Compression** | Automatic |
| **MPP** | Massively Parallel Processing |
| **Spectrum** | Query S3 directly |
| **Concurrency Scaling** | Auto-add clusters |
| **Snapshots** | Automated + manual |

## Redshift Serverless
- No cluster management
- Pay per query
- Auto-scales
- Good for sporadic workloads

## Redshift vs Athena vs EMR

| Feature | Redshift | Athena | EMR |
|---------|----------|--------|-----|
| **Type** | Data warehouse | Query service | Cluster |
| **Data location** | Loaded | S3 (in-place) | S3/HDFS |
| **Best for** | Complex analytics | Ad-hoc queries | Big data processing |
| **Pricing** | Per cluster/query | Per query | Per cluster |

---

# 📑 OTHER DATABASE SERVICES

## DocumentDB

| Feature | Description |
|---------|-------------|
| **Type** | Document database |
| **Compatible** | MongoDB API |
| **Use case** | MongoDB workloads on AWS |
| **Scaling** | Storage auto-scales to 64 TB |

## Neptune

| Feature | Description |
|---------|-------------|
| **Type** | Graph database |
| **APIs** | Gremlin, SPARQL |
| **Use case** | Social networks, fraud detection |
| **HA** | 6 copies across 3 AZs |

## Keyspaces

| Feature | Description |
|---------|-------------|
| **Type** | Wide-column |
| **Compatible** | Apache Cassandra |
| **Use case** | Cassandra workloads on AWS |
| **Scaling** | Serverless, on-demand |

## QLDB (Quantum Ledger)

| Feature | Description |
|---------|-------------|
| **Type** | Ledger database |
| **Feature** | Immutable, cryptographic |
| **Use case** | Financial transactions, audit |
| **Verification** | SHA-256 hash chain |

## Timestream

| Feature | Description |
|---------|-------------|
| **Type** | Time-series database |
| **Use case** | IoT, DevOps, analytics |
| **Feature** | Auto-tiering hot/cold |

## MemoryDB for Redis

| Feature | Description |
|---------|-------------|
| **Type** | Durable in-memory database |
| **Compatible** | Redis API |
| **Use case** | Primary database with Redis |
| **Durability** | Multi-AZ transactional log |

---

# 🎯 DATABASE QUICK EXAM HEURISTICS

| Keyword/Scenario | Answer |
|------------------|--------|
| Relational, managed | **RDS** |
| 5x MySQL performance | **Aurora** |
| High availability, sync | **Multi-AZ** |
| Read scaling | **Read Replicas** |
| < 1 sec cross-region replication | **Aurora Global Database** |
| Variable workloads, auto-scale | **Aurora Serverless v2** |
| NoSQL, key-value | **DynamoDB** |
| NoSQL, millisecond latency | **DynamoDB** |
| NoSQL, microsecond latency | **DynamoDB + DAX** |
| Multi-region NoSQL | **DynamoDB Global Tables** |
| In-memory caching | **ElastiCache** |
| Session storage | **ElastiCache Redis** |
| Simple caching, multi-threaded | **ElastiCache Memcached** |
| Complex data structures cache | **ElastiCache Redis** |
| Data warehouse, analytics | **Redshift** |
| Query S3 with SQL | **Athena** |
| Query S3 from Redshift | **Redshift Spectrum** |
| MongoDB compatibility | **DocumentDB** |
| Graph database | **Neptune** |
| Cassandra compatibility | **Keyspaces** |
| Immutable ledger | **QLDB** |
| Time-series data | **Timestream** |
| Lambda + database | **RDS Proxy** |
| Encrypt existing RDS | **Snapshot → Copy encrypted → Restore** |

---

# 📊 DATABASE SELECTION MATRIX

| Requirement | Service |
|-------------|---------|
| Relational, multi-engine | RDS |
| High-performance relational | Aurora |
| Serverless relational | Aurora Serverless |
| Key-value NoSQL | DynamoDB |
| Document NoSQL | DocumentDB |
| Graph | Neptune |
| Wide-column | Keyspaces |
| Time-series | Timestream |
| Ledger | QLDB |
| In-memory | ElastiCache / MemoryDB |
| Data warehouse | Redshift |

---

# ✅ EXAM DAY QUICK CHECKS

```
□ Know RDS Multi-AZ vs Read Replicas
□ Know Aurora architecture (6 copies, 3 AZs)
□ Know Aurora Global Database RPO/RTO
□ Know DynamoDB capacity modes (Provisioned vs On-Demand)
□ Know DynamoDB indexes (LSI vs GSI)
□ Know ElastiCache engines (Redis vs Memcached)
□ Know Redshift node types
□ Know when to use each database type
□ Know how to encrypt unencrypted RDS
```

---

*Last Updated: December 2025*
*Exam Version: SAA-C03*
