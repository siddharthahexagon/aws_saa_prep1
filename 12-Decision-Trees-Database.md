# AWS SAA-C03: Database Services Decision Trees

## 🌳 Quick Navigation
- [Main Database Decision Tree](#main-database-decision-tree)
- [Relational Database Selection](#relational-database-selection)
- [NoSQL Database Selection](#nosql-database-selection)
- [Caching Strategy Selection](#caching-strategy-selection)
- [Analytics & Data Warehouse Selection](#analytics--data-warehouse-selection)

---

## Main Database Decision Tree

```
START: Need a database?
│
├─→ Data structure?
│   │
│   ├─→ Structured, relational (SQL, ACID)?
│   │   └─→ Go to "Relational Database Selection"
│   │
│   ├─→ Semi-structured or unstructured (NoSQL)?
│   │   └─→ Go to "NoSQL Database Selection"
│   │
│   ├─→ Graph relationships?
│   │   └─→ **Amazon Neptune**
│   │       ├─→ Graph database (Gremlin, SPARQL)
│   │       ├─→ Use case: Social networks, fraud detection, knowledge graphs
│   │       └─→ High availability, up to 15 read replicas
│   │
│   ├─→ Ledger, immutable, cryptographically verifiable?
│   │   └─→ **Amazon QLDB** (Quantum Ledger Database)
│   │       ├─→ Immutable, transparent, cryptographically verifiable
│   │       ├─→ Use case: Financial transactions, supply chain, audit logs
│   │       └─→ Serverless, fully managed
│   │
│   └─→ Time-series data?
│       └─→ **Amazon Timestream**
│           ├─→ Purpose-built for time-series data
│           ├─→ 1000x faster, 1/10th cost vs relational
│           ├─→ Use case: IoT, operational metrics, analytics
│           └─→ Automatic data lifecycle management
│
├─→ Need in-memory caching?
│   └─→ Go to "Caching Strategy Selection"
│
├─→ Need data warehouse (analytics)?
│   └─→ Go to "Analytics & Data Warehouse Selection"
│
└─→ Migration from existing database?
    └─→ **AWS Database Migration Service (DMS)**
        ├─→ Homogeneous: Same engine (Oracle → Oracle)
        ├─→ Heterogeneous: Different engine (Oracle → Aurora)
        ├─→ Continuous replication (Change Data Capture)
        └─→ Minimal downtime migration
```

---

## Relational Database Selection

```
START: Need relational database?
│
├─→ Do you want to manage the database?
│   ├─→ NO → Use AWS managed services (recommended)
│   └─→ YES → Self-manage on EC2 (not recommended for exam)
│
├─→ Which managed service?
│   │
│   ├─→ Need MySQL or PostgreSQL with highest performance?
│   │   └─→ **Amazon Aurora**
│   │       ├─→ MySQL or PostgreSQL compatible
│   │       ├─→ 5x faster than MySQL, 3x faster than PostgreSQL
│   │       ├─→ Storage auto-scales (10GB to 128TB)
│   │       ├─→ Up to 15 read replicas
│   │       ├─→ Multi-AZ by default (6 copies across 3 AZs)
│   │       ├─→ Continuous backup to S3, point-in-time recovery
│   │       ├─→ Aurora Global Database (cross-region, < 1 sec replication)
│   │       ├─→ Aurora Serverless (auto-scaling, pay per use)
│   │       └─→ Use case: Enterprise apps, SaaS, high availability needs
│   │
│   ├─→ Standard MySQL, PostgreSQL, MariaDB, Oracle, SQL Server?
│   │   └─→ **Amazon RDS** (Relational Database Service)
│   │       ├─→ Engines: MySQL, PostgreSQL, MariaDB, Oracle, SQL Server
│   │       ├─→ Automated backups, patching, monitoring
│   │       ├─→ Multi-AZ for high availability (standby replica)
│   │       ├─→ Read replicas for scaling reads (up to 5 for most engines)
│   │       ├─→ Storage types: gp2, gp3, io1, io2
│   │       └─→ Use case: Standard RDBMS needs, lift-and-shift migrations
│   │
│   └─→ Compare Aurora vs RDS:
│       ├─→ Need highest performance → Aurora
│       ├─→ Need specific Oracle/SQL Server features → RDS
│       ├─→ Need cost-effective MySQL/PostgreSQL → RDS
│       └─→ Need global distribution → Aurora Global Database
│
├─→ High Availability requirements?
│   ├─→ Automatic failover needed?
│   │   └─→ **RDS Multi-AZ** or **Aurora** (Multi-AZ by default)
│   │       ├─→ RDS Multi-AZ: Synchronous replication to standby
│   │       │   └─→ Failover in 60-120 seconds
│   │       └─→ Aurora: 6 copies across 3 AZs automatically
│   │           └─→ Failover in < 30 seconds
│   │
│   └─→ Read scaling needed?
│       └─→ **Read Replicas**
│           ├─→ RDS: Up to 5 read replicas (15 for Aurora)
│           ├─→ Asynchronous replication
│           ├─→ Can be in different AZs or regions
│           └─→ Use case: Read-heavy workloads, reporting, analytics
│
├─→ Serverless requirements?
│   └─→ **Aurora Serverless**
│       ├─→ Auto-scaling based on demand
│       ├─→ Pay per second of database capacity
│       ├─→ Automatic start/stop (scale to zero)
│       └─→ Use case: Infrequent/intermittent/unpredictable workloads
│
└─→ Backup & recovery?
    ├─→ Automated backups (all RDS/Aurora)
    ├─→ Retention: 0-35 days (RDS), 1-35 days (Aurora)
    ├─→ Manual snapshots (retained indefinitely)
    └─→ Point-in-time recovery (up to retention period)
```

### RDS vs Aurora Comparison

| Feature | RDS | Aurora | Aurora Serverless |
|---------|-----|--------|-------------------|
| **Engines** | MySQL, PostgreSQL, MariaDB, Oracle, SQL Server | MySQL, PostgreSQL compatible | MySQL, PostgreSQL compatible |
| **Performance** | Standard | 5x MySQL, 3x PostgreSQL | Same as Aurora |
| **Storage** | Up to 64 TB | Up to 128 TB (auto-scales) | Up to 128 TB |
| **Read Replicas** | Up to 5 (15 PostgreSQL) | Up to 15 | N/A |
| **Failover Time** | 60-120 sec | < 30 sec | < 30 sec |
| **Availability** | Multi-AZ optional | Multi-AZ by default | Multi-AZ by default |
| **Backups** | Automated + manual | Continuous to S3 | Continuous to S3 |
| **Global** | Cross-region read replicas | Global Database | N/A |
| **Cost** | Lower | Higher | Pay per use |
| **Use Case** | Standard needs | High performance | Variable workloads |

---

## NoSQL Database Selection

```
START: Need NoSQL database?
│
├─→ Key-value or document store?
│   └─→ **Amazon DynamoDB**
│       ├─→ Fully managed NoSQL (key-value, document)
│       ├─→ Single-digit millisecond latency (microsecond with DAX)
│       ├─→ Scales to 10 trillion requests/day
│       ├─→ Multi-AZ, multi-region (Global Tables)
│       ├─→ Capacity modes:
│       │   ├─→ On-Demand: Pay per request, automatic scaling
│       │   └─→ Provisioned: Specify RCU/WCU, predictable cost
│       ├─→ Features:
│       │   ├─→ DynamoDB Streams (change data capture)
│       │   ├─→ Global Tables (multi-region, active-active)
│       │   ├─→ Point-in-time recovery (continuous backups)
│       │   ├─→ Encryption at rest
│       │   └─→ DAX (DynamoDB Accelerator) for caching
│       ├─→ Indexes:
│       │   ├─→ GSI (Global Secondary Index): Different partition + sort key
│       │   └─→ LSI (Local Secondary Index): Same partition, different sort key
│       └─→ Use case: Mobile/web/gaming backends, IoT, real-time bidding
│
├─→ MongoDB-compatible?
│   └─→ **Amazon DocumentDB**
│       ├─→ MongoDB compatible (3.6, 4.0, 5.0)
│       ├─→ Document database (JSON-like)
│       ├─→ Storage auto-scales (up to 64 TB)
│       ├─→ Up to 15 read replicas
│       ├─→ Multi-AZ, continuous backup to S3
│       └─→ Use case: Content management, catalogs, user profiles
│
├─→ In-memory key-value store?
│   └─→ Go to "Caching Strategy Selection"
│
└─→ Wide-column store (Cassandra)?
│   └─→ **Amazon Keyspaces** (for Apache Cassandra)
│       ├─→ Cassandra-compatible
│       ├─→ Serverless, fully managed
│       ├─→ Single-digit millisecond latency
│       └─→ Use case: IoT, time-series, high-scale applications
```

### DynamoDB Capacity Modes Decision

```
Workload pattern?
│
├─→ Predictable, steady traffic?
│   └─→ **Provisioned Capacity**
│       ├─→ Specify Read Capacity Units (RCU) and Write Capacity Units (WCU)
│       ├─→ Lower cost for steady workloads
│       ├─→ Can enable Auto Scaling
│       └─→ Reserved Capacity available (additional savings)
│
├─→ Unpredictable, variable traffic?
│   └─→ **On-Demand Capacity**
│       ├─→ Pay per request
│       ├─→ Automatic, instant scaling
│       ├─→ No capacity planning
│       └─→ Higher cost per request, but no idle capacity cost
│
└─→ New application, unknown traffic?
    └─→ **On-Demand** (start) → Switch to Provisioned later if steady
```

### DynamoDB Index Selection

```
Need additional query patterns?
│
├─→ Need different partition key?
│   └─→ **Global Secondary Index (GSI)**
│       ├─→ Different partition key + optional sort key
│       ├─→ Can be created anytime (before or after table creation)
│       ├─→ Has its own RCU/WCU
│       ├─→ Eventually consistent
│       └─→ Up to 20 GSIs per table
│
└─→ Same partition key, different sort key?
    └─→ **Local Secondary Index (LSI)**
        ├─→ Same partition key, different sort key
        ├─→ Must be created at table creation time
        ├─→ Shares RCU/WCU with base table
        ├─→ Strongly consistent or eventually consistent
        └─→ Up to 5 LSIs per table
```

---

## Caching Strategy Selection

```
START: Need caching?
│
├─→ What to cache?
│   │
│   ├─→ DynamoDB queries?
│   │   └─→ **DynamoDB Accelerator (DAX)**
│   │       ├─→ In-memory cache for DynamoDB
│   │       ├─→ Microsecond latency (10x performance improvement)
│   │       ├─→ Fully managed, highly available (Multi-AZ)
│   │       ├─→ No application code changes (compatible with DynamoDB API)
│   │       ├─→ Cache size: 1.3 GB to 400+ GB per node
│   │       └─→ Use case: Read-heavy DynamoDB workloads, real-time bidding
│   │
│   ├─→ Database queries, objects, session data?
│   │   └─→ **Amazon ElastiCache**
│   │       ├─→ Choose engine: Redis or Memcached
│   │       └─→ Go to "Redis vs Memcached"
│   │
│   └─→ Static content, web pages, APIs?
│       └─→ **Amazon CloudFront** (CDN)
│           └─→ Caches at edge locations globally
│
└─→ Redis vs Memcached?
    │
    ├─→ Need advanced features?
    │   └─→ **ElastiCache for Redis**
    │       ├─→ Data persistence (snapshot, AOF)
    │       ├─→ Replication (Multi-AZ, read replicas)
    │       ├─→ Backup and restore
    │       ├─→ Pub/sub messaging
    │       ├─→ Complex data types (lists, sets, sorted sets)
    │       ├─→ Geospatial data
    │       ├─→ Lua scripting
    │       ├─→ Transactions
    │       └─→ Use case: Complex caching, leaderboards, session store, pub/sub
    │
    └─→ Need simple, distributed caching?
        └─→ **ElastiCache for Memcached**
            ├─→ Simple key-value store
            ├─→ Multi-threaded (better for multi-core)
            ├─→ No persistence
            ├─→ No replication (but can scale horizontally)
            ├─→ Lower memory overhead
            └─→ Use case: Simple caching, object caching, session store
```

### Redis vs Memcached Comparison

| Feature | Redis | Memcached |
|---------|-------|-----------|
| **Data Persistence** | Yes (snapshots, AOF) | No |
| **Replication** | Yes (Multi-AZ) | No |
| **Backup & Restore** | Yes | No |
| **Data Types** | Complex (lists, sets, hashes) | Simple (strings) |
| **Pub/Sub** | Yes | No |
| **Multi-threading** | Single-threaded | Multi-threaded |
| **Transactions** | Yes | No |
| **Geospatial** | Yes | No |
| **Lua Scripting** | Yes | No |
| **Use Case** | Advanced caching, session store | Simple caching, high throughput |

---

## Analytics & Data Warehouse Selection

```
START: Need analytics or data warehousing?
│
├─→ Data warehouse (OLAP, BI)?
│   └─→ **Amazon Redshift**
│       ├─→ Petabyte-scale data warehouse
│       ├─→ Columnar storage, massively parallel processing (MPP)
│       ├─→ 10x faster than traditional data warehouses
│       ├─→ Node types:
│       │   ├─→ RA3: Managed storage, compute/storage independent
│       │   ├─→ DC2: Compute-intensive, local SSD
│       │   └─→ DS2: Storage-intensive (legacy)
│       ├─→ Redshift Spectrum: Query S3 directly (no loading)
│       ├─→ Redshift Serverless: Auto-scaling, pay per use
│       ├─→ Integrates with: S3, Kinesis, DynamoDB, EMR
│       └─→ Use case: Business intelligence, analytics, reporting
│
├─→ Query data in S3 (without loading)?
│   └─→ **Amazon Athena**
│       ├─→ Serverless, interactive query service
│       ├─→ SQL queries on S3 data
│       ├─→ Pay per query (per TB scanned)
│       ├─→ Supports: CSV, JSON, Parquet, ORC, Avro
│       ├─→ Integrates with AWS Glue (data catalog)
│       └─→ Use case: Ad-hoc queries, log analysis, quick insights
│
├─→ Big data processing (Hadoop, Spark)?
│   └─→ **Amazon EMR** (Elastic MapReduce)
│       ├─→ Managed Hadoop, Spark, HBase, Presto, Flink
│       ├─→ Scales from single instance to thousands
│       ├─→ Spot instances for cost savings
│       ├─→ Integrates with: S3, DynamoDB, Redshift
│       └─→ Use case: Big data processing, ML, ETL at scale
│
├─→ ETL (Extract, Transform, Load)?
│   └─→ **AWS Glue**
│       ├─→ Serverless ETL service
│       ├─→ Data catalog (metadata repository)
│       ├─→ Crawlers (auto-discover schema)
│       ├─→ Job authoring (Python or Scala)
│       ├─→ Integrates with: S3, RDS, Redshift, Athena
│       └─→ Use case: Data preparation, ETL pipelines, data lake
│
├─→ Real-time streaming analytics?
│   └─→ **Amazon Kinesis Data Analytics**
│       ├─→ SQL or Apache Flink for streaming data
│       ├─→ Process Kinesis Data Streams or Firehose
│       └─→ Use case: Real-time dashboards, metrics, alerts
│
├─→ Business intelligence dashboards?
│   └─→ **Amazon QuickSight**
│       ├─→ Serverless BI service
│       ├─→ Interactive dashboards, visualizations
│       ├─→ ML-powered insights (anomaly detection, forecasting)
│       ├─→ Integrates with: RDS, Aurora, Redshift, Athena, S3
│       └─→ Pay per session pricing
│
└─→ Data pipeline orchestration?
    └─→ **AWS Data Pipeline** or **Step Functions**
        ├─→ Data Pipeline: ETL workflows, data movement
        └─→ Step Functions: General workflow orchestration
```

### Analytics Service Selection Matrix

| Use Case | Service | Key Feature |
|----------|---------|-------------|
| **Data Warehouse** | Redshift | Petabyte-scale, columnar, MPP |
| **Query S3** | Athena | Serverless SQL, pay per query |
| **Big Data Processing** | EMR | Hadoop, Spark, scalable |
| **ETL** | Glue | Serverless, data catalog, crawlers |
| **Real-time Streaming** | Kinesis Data Analytics | SQL/Flink on streams |
| **BI Dashboards** | QuickSight | Serverless, ML insights |
| **Data Orchestration** | Data Pipeline, Step Functions | Workflow automation |

---

## Database Migration Decision

```
START: Migrating a database?
│
├─→ Same database engine (homogeneous)?
│   └─→ **AWS Database Migration Service (DMS)**
│       ├─→ Example: MySQL → MySQL, Oracle → Oracle
│       ├─→ Simpler, faster migration
│       ├─→ Continuous replication (CDC - Change Data Capture)
│       ├─→ Minimal downtime
│       └─→ Source can be: On-prem, EC2, RDS
│
├─→ Different database engine (heterogeneous)?
│   └─→ **AWS DMS + AWS Schema Conversion Tool (SCT)**
│       ├─→ Example: Oracle → Aurora, SQL Server → PostgreSQL
│       ├─→ SCT: Convert schema and code
│       ├─→ DMS: Migrate data with CDC
│       └─→ Target: RDS, Aurora, Redshift, DynamoDB, S3
│
├─→ Large-scale migration (> 10 TB)?
│   └─→ **AWS DMS + Snowball Edge**
│       ├─→ Stage 1: Initial load to Snowball
│       ├─→ Stage 2: Ship Snowball, load to S3
│       ├─→ Stage 3: DMS replicates from S3 to target
│       └─→ Stage 4: Ongoing CDC via DMS
│
└─→ Testing migration?
    └─→ **AWS DMS with test endpoints**
        ├─→ Test without affecting production
        └─→ Validate before cutover
```

---

## Exam Scenarios - Quick Answers

### Scenario 1: E-commerce site, need lowest latency for product catalog
**Answer**: DynamoDB (single-digit ms latency)

### Scenario 2: Financial application, need ACID compliance, relational
**Answer**: RDS or Aurora (Aurora for highest performance)

### Scenario 3: Gaming leaderboard, need sub-millisecond reads
**Answer**: DynamoDB with DAX

### Scenario 4: Analytics on large datasets (petabytes), BI queries
**Answer**: Amazon Redshift

### Scenario 5: Mobile app, variable traffic, NoSQL
**Answer**: DynamoDB with On-Demand capacity

### Scenario 6: Session management for web application, caching
**Answer**: ElastiCache (Redis if need persistence, Memcached if simple)

### Scenario 7: Migrate Oracle database to AWS with minimal downtime
**Answer**: AWS DMS (+ SCT if changing engine)

### Scenario 8: Social network, need graph relationships
**Answer**: Amazon Neptune

### Scenario 9: Ad-hoc SQL queries on S3 logs
**Answer**: Amazon Athena

### Scenario 10: MongoDB application, want managed service
**Answer**: Amazon DocumentDB

### Scenario 11: IoT time-series data, millions of events/second
**Answer**: Amazon Timestream or DynamoDB

### Scenario 12: Need highest availability for MySQL database
**Answer**: Aurora (6 copies across 3 AZs) or RDS Multi-AZ

### Scenario 13: Read-heavy workload, need to scale reads
**Answer**: RDS/Aurora with Read Replicas

### Scenario 14: Infrequent database usage, want cost-effective
**Answer**: Aurora Serverless

### Scenario 15: Need immutable audit log for financial transactions
**Answer**: Amazon QLDB

---

## 🎯 Key Takeaways

### Relational Databases:
- **Aurora**: Highest performance MySQL/PostgreSQL, auto-scaling, Multi-AZ
- **RDS**: Standard RDBMS, Multi-AZ optional, read replicas
- **RDS Multi-AZ**: High availability, automatic failover
- **Read Replicas**: Scale read workloads, asynchronous replication

### NoSQL Databases:
- **DynamoDB**: Key-value/document, single-digit ms, fully managed
- **DocumentDB**: MongoDB-compatible, document store
- **Neptune**: Graph database, relationships
- **DAX**: DynamoDB caching, microsecond latency

### Caching:
- **DAX**: DynamoDB-specific, microsecond reads
- **ElastiCache Redis**: Advanced features, persistence, replication
- **ElastiCache Memcached**: Simple caching, multi-threaded

### Analytics:
- **Redshift**: Data warehouse, OLAP, BI
- **Athena**: Query S3 with SQL, serverless
- **EMR**: Big data, Hadoop, Spark
- **Glue**: Serverless ETL, data catalog

### Special Purpose:
- **QLDB**: Immutable ledger, audit logs
- **Timestream**: Time-series data, IoT
- **Keyspaces**: Cassandra-compatible

### Migration:
- **DMS**: Database migration, continuous replication
- **SCT**: Schema conversion for heterogeneous migrations

---

## Quick Selection Guide

```
SCENARIO → DATABASE

Relational, high performance → Aurora
Relational, standard → RDS
NoSQL, key-value, low latency → DynamoDB
MongoDB compatible → DocumentDB
Graph relationships → Neptune
Caching DynamoDB → DAX
Caching general → ElastiCache (Redis/Memcached)
Data warehouse → Redshift
Query S3 → Athena
Big data → EMR
ETL → Glue
Immutable ledger → QLDB
Time-series → Timestream
Cassandra → Keyspaces
```

---

*Use these decision trees for quick database service selection during the exam!*
