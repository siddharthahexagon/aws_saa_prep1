# AWS SAA-C03: Database Services Cheat Sheet

## 📋 Table of Contents
- [RDS (Relational Database Service)](#rds)
- [Aurora](#aurora)
- [DynamoDB](#dynamodb)
- [ElastiCache](#elasticache)
- [Redshift](#redshift)
- [DocumentDB](#documentdb)
- [Neptune](#neptune)
- [QLDB](#qldb)
- [Timestream](#timestream)
- [Database Migration Service (DMS)](#dms)

---

## RDS (Relational Database Service)

### Definition
Managed relational database service supporting multiple database engines.

### Key Features
- **Supported Engines**: MySQL, PostgreSQL, MariaDB, Oracle, SQL Server, Aurora
- **Deployment**: Single-AZ or Multi-AZ (automatic failover)
- **Read Replicas**: Up to 15 (Aurora), 5 (others), async replication
- **Storage**: gp2, gp3, io1, magnetic
- **Automated Backups**: Point-in-time recovery (1-35 days retention)
- **Snapshots**: Manual backups stored in S3, can be shared/copied
- **Encryption**: At rest (KMS), in transit (SSL/TLS)
- **Maintenance Windows**: For patches and updates
- **Enhanced Monitoring**: CloudWatch with 1-second granularity
- **Performance Insights**: Database performance monitoring
- **IAM Authentication**: MySQL, PostgreSQL, Aurora
- **Storage Auto Scaling**: Automatically increase storage

### Exam Tips & Heuristics
✅ **When to Use RDS:**
- Need ACID compliance
- Relational data model
- Complex queries and joins
- Existing applications using SQL databases
- Don't want to manage database infrastructure

⚠️ **Common Pitfalls:**
- Multi-AZ is for disaster recovery, NOT read scaling (use Read Replicas)
- Automated backups deleted when DB deleted (manual snapshots persist)
- Read Replicas can be in different regions (cross-region)
- Cannot SSH into RDS instances
- Must enable backups for Read Replicas

🎯 **Exam Heuristics:**
- "Relational database" + "managed" = **RDS**
- "High availability" + "automatic failover" = **Multi-AZ**
- "Read-heavy workload" = **Read Replicas**
- "Disaster recovery in another region" = **Cross-region Read Replica** or **snapshot copy**
- "Point-in-time recovery" = **Automated backups** (must be enabled)
- "Free up resources during maintenance" = **Maintenance window**
- "MySQL/PostgreSQL" + "serverless" = **Aurora Serverless**
- "Need highest performance" = **Aurora** (not standard RDS)
- "Cost optimization for dev/test" = Single-AZ, stop/start instances

### Multi-AZ vs Read Replicas
| Feature | Multi-AZ | Read Replicas |
|---------|----------|---------------|
| Purpose | High availability, DR | Read scaling, DR |
| Replication | Synchronous | Asynchronous |
| Automatic Failover | Yes | No (manual promotion) |
| Traffic | Only on failover | Active read traffic |
| Number | 1 standby | Up to 15 (Aurora), 5 (others) |
| Cross-Region | Yes | Yes |

---

## Aurora

### Definition
AWS proprietary, cloud-optimized relational database compatible with MySQL and PostgreSQL.

### Key Features
- **Performance**: 5x faster than MySQL, 3x faster than PostgreSQL
- **Storage**: Auto-scaling from 10GB to 128TB in 10GB increments
- **High Availability**: 6 copies across 3 AZs, self-healing
- **Read Replicas**: Up to 15, <10ms replica lag
- **Endpoints**:
  - **Writer Endpoint**: Points to primary instance
  - **Reader Endpoint**: Load-balanced across read replicas
  - **Custom Endpoints**: Subset of instances for specific workloads
- **Aurora Serverless**: Auto-scaling, pay per second
- **Aurora Global Database**: Cross-region replication (<1 second), up to 5 secondary regions
- **Aurora Multi-Master**: Multiple write instances
- **Backtrack**: Rewind DB to any point in time without restoring from backup
- **Cloning**: Fast, cost-effective copy of database
- **Parallel Query**: Faster analytics on current data
- **Database Activity Streams**: Real-time monitoring

### Exam Tips & Heuristics
✅ **When to Use Aurora:**
- Need highest performance for MySQL/PostgreSQL
- High availability is critical
- Large-scale applications
- Read-heavy workloads
- Global applications

⚠️ **Common Pitfalls:**
- More expensive than RDS MySQL/PostgreSQL
- Aurora Serverless v1 has cold start delays
- Not compatible with all MySQL/PostgreSQL features
- Backtrack only available for MySQL-compatible

🎯 **Exam Heuristics:**
- "MySQL/PostgreSQL" + "highest performance" = **Aurora**
- "Global database" + "low latency worldwide" = **Aurora Global Database**
- "Variable workload" + "cost optimization" = **Aurora Serverless**
- "Read scaling" = **Aurora Read Replicas** (up to 15)
- "Disaster recovery" + "<1 second RPO" = **Aurora Global Database**
- "Multiple writers" = **Aurora Multi-Master**
- "Undo changes without backup restore" = **Aurora Backtrack**
- "Fast database copy for dev/test" = **Aurora Cloning**
- "Analytics on production data" = **Aurora Parallel Query**

### Aurora vs RDS
| Feature | Aurora | RDS |
|---------|--------|-----|
| Performance | 5x MySQL, 3x PostgreSQL | Standard engine performance |
| Storage | Auto-scale to 128TB | Manual scaling |
| Replicas | Up to 15 | Up to 5 |
| Failover | <30 seconds | 60-120 seconds |
| Backtrack | Yes (MySQL) | No |
| Global Database | Yes | Manual setup |
| Cost | Higher | Lower |

---

## DynamoDB

### Definition
Fully managed NoSQL database service with single-digit millisecond latency at any scale.

### Key Features
- **Data Model**: Key-value and document
- **Capacity Modes**:
  - **Provisioned**: Specify RCU/WCU, auto-scaling available
  - **On-Demand**: Pay per request, automatic scaling
- **Indexes**:
  - **Local Secondary Index (LSI)**: Same partition key, different sort key (created at table creation)
  - **Global Secondary Index (GSI)**: Different partition key and sort key (can be added anytime)
- **Streams**: Change data capture, 24-hour retention
- **Global Tables**: Multi-region, multi-active replication
- **Transactions**: ACID across multiple items/tables
- **Point-in-Time Recovery (PITR)**: Continuous backups for 35 days
- **On-Demand Backup**: Manual full backups
- **DAX (DynamoDB Accelerator)**: In-memory cache, microsecond latency
- **TTL (Time To Live)**: Automatically delete expired items
- **Encryption**: At rest by default, in transit with SSL

### Exam Tips & Heuristics
✅ **When to Use DynamoDB:**
- Need single-digit millisecond latency
- Key-value access patterns
- Massive scale (serverless)
- Gaming, IoT, mobile backends
- Real-time bidding
- Shopping carts

⚠️ **Common Pitfalls:**
- No complex queries or joins (use indexes or denormalize)
- LSI must be created at table creation (cannot add later)
- GSI has eventual consistency
- Item size limit 400KB
- Scan operations are expensive (use Query instead)
- Hot partitions can throttle requests

🎯 **Exam Heuristics:**
- "NoSQL" + "millisecond latency" = **DynamoDB**
- "Key-value" or "document store" = **DynamoDB**
- "Serverless database" = **DynamoDB**
- "Unpredictable traffic" = **DynamoDB On-Demand**
- "Predictable traffic" = **DynamoDB Provisioned** with auto-scaling
- "Read-heavy" + "microsecond latency" = **DynamoDB + DAX**
- "Multi-region writes" = **DynamoDB Global Tables**
- "Real-time data changes" = **DynamoDB Streams** + Lambda
- "Session store" = **DynamoDB** (with TTL)
- "Need complex queries" = Consider **RDS/Aurora** (not DynamoDB)

### DynamoDB Best Practices
- Use composite keys for flexible queries
- Create GSIs for alternative access patterns
- Enable auto-scaling for provisioned capacity
- Use DAX for read-heavy workloads
- Implement TTL for automatic cleanup
- Use DynamoDB Streams for event-driven architectures
- Avoid scan operations (use Query with indexes)

---

## ElastiCache

### Definition
Fully managed in-memory caching service supporting Redis and Memcached.

### Key Features

#### ElastiCache for Redis
- **Data Structures**: Strings, lists, sets, sorted sets, hashes, bitmaps, hyperloglogs
- **Persistence**: Snapshots (RDB), append-only file (AOF)
- **Replication**: Multi-AZ with automatic failover
- **Cluster Mode**: Partition data across shards, up to 500 nodes
- **Backup & Restore**: Automated and manual snapshots
- **Auth**: Redis AUTH, IAM authentication
- **Encryption**: At rest and in transit
- **Global Datastore**: Cross-region replication

#### ElastiCache for Memcached
- **Multi-threaded**: Better CPU utilization
- **Simple Data Types**: Strings only
- **No Persistence**: Data lost on restart
- **No Replication**: No backup/snapshot capability
- **Sharding**: Horizontal scaling via partitioning
- **Multi-AZ**: Not supported

### Exam Tips & Heuristics
✅ **When to Use ElastiCache:**
- Reduce database load
- Session store
- Real-time analytics
- Leaderboards and gaming
- Message queues (Redis)
- Pub/sub messaging (Redis)

⚠️ **Common Pitfalls:**
- Redis for advanced features, Memcached for simplicity
- ElastiCache is not a database (data can be lost)
- Application must implement cache invalidation logic
- Redis Cluster Mode vs Non-Cluster Mode has different features

🎯 **Exam Heuristics:**
- "Caching" = **ElastiCache**
- "Session store" = **ElastiCache Redis** (persistent) or **DynamoDB**
- "Leaderboard" + "sorting" = **ElastiCache Redis** (sorted sets)
- "Pub/sub" = **ElastiCache Redis** or **SNS**
- "Need persistence" = **Redis** (not Memcached)
- "Need multi-AZ" = **Redis** (not Memcached)
- "Simple caching" + "multi-threaded" = **Memcached**
- "Reduce RDS/Aurora load" = **ElastiCache** in front of database
- "Complex data structures" = **Redis**

### Redis vs Memcached
| Feature | Redis | Memcached |
|---------|-------|-----------|
| Data Structures | Complex (lists, sets, sorted sets) | Simple (strings only) |
| Persistence | Yes (RDB, AOF) | No |
| Replication | Yes, Multi-AZ | No |
| Backup/Restore | Yes | No |
| Multi-threaded | No (single-threaded) | Yes |
| Use Case | Advanced caching, session store | Simple caching |

---

## Redshift

### Definition
Fully managed, petabyte-scale data warehouse service designed for OLAP (Online Analytical Processing).

### Key Features
- **Architecture**: Columnar storage, massively parallel processing (MPP)
- **Node Types**:
  - **Leader Node**: Query planning and aggregation
  - **Compute Nodes**: Execute queries and store data
- **Node Sizes**: Dense Compute (DC2), Dense Storage (DS2), RA3 (managed storage)
- **Snapshots**: Automated (retained 1-35 days) and manual (indefinite)
- **Redshift Spectrum**: Query S3 data without loading
- **Concurrency Scaling**: Auto-scale for concurrent queries
- **Result Caching**: Cache query results
- **Enhanced VPC Routing**: Force through VPC
- **Distribution Styles**: EVEN, KEY, ALL
- **Sort Keys**: Optimize query performance
- **Compression**: Automatic column compression
- **Redshift Serverless**: No infrastructure management

### Exam Tips & Heuristics
✅ **When to Use Redshift:**
- Data warehousing and OLAP
- Business intelligence
- Complex analytics queries
- Historical data analysis
- Petabyte-scale datasets

⚠️ **Common Pitfalls:**
- Not for OLTP (use RDS/Aurora)
- Not for real-time queries (latency in seconds)
- Single-AZ only (use snapshots for DR)
- Requires data loading/transformation
- Query performance depends on proper design (distribution keys, sort keys)

🎯 **Exam Heuristics:**
- "Data warehouse" = **Redshift**
- "OLAP" or "analytics" = **Redshift**
- "Business intelligence" = **Redshift**
- "Petabyte scale analytics" = **Redshift**
- "Query data in S3 without loading" = **Redshift Spectrum**
- "Variable analytical workload" = **Redshift Serverless**
- "Real-time transactional" = **RDS/Aurora/DynamoDB** (not Redshift)
- "Disaster recovery" = **Cross-region snapshot copy**

### Redshift vs RDS
| Feature | Redshift | RDS |
|---------|----------|-----|
| Purpose | OLAP (analytics) | OLTP (transactions) |
| Architecture | Columnar, MPP | Row-based |
| Scale | Petabyte | Terabyte |
| Query Speed | Complex queries on large datasets | Fast transactions |
| Cost | Lower for analytics workloads | Lower for transactional |

---

## DocumentDB

### Definition
Fully managed MongoDB-compatible document database service.

### Key Features
- **MongoDB Compatibility**: Supports MongoDB 3.6, 4.0, 5.0 APIs
- **Storage**: Auto-scales from 10GB to 64TB
- **High Availability**: 6 copies across 3 AZs
- **Read Replicas**: Up to 15
- **Backup**: Automated, continuous with PITR
- **Encryption**: At rest and in transit
- **Global Clusters**: Cross-region replication

### Exam Tips & Heuristics
✅ **When to Use DocumentDB:**
- Migrating from MongoDB to AWS
- Need managed MongoDB-compatible service
- JSON document storage
- Content management, user profiles

⚠️ **Common Pitfalls:**
- Not 100% MongoDB compatible (some features missing)
- More expensive than self-managed MongoDB
- Runs in VPC (not publicly accessible by default)

🎯 **Exam Heuristics:**
- "MongoDB" = **DocumentDB**
- "Document database" + "managed" = **DocumentDB**
- "JSON documents" = **DocumentDB** or **DynamoDB**
- "Migrate MongoDB to AWS" = **DocumentDB**

---

## Neptune

### Definition
Fully managed graph database service supporting property graph and RDF graph models.

### Key Features
- **Query Languages**: Gremlin (property graphs), SPARQL (RDF graphs)
- **High Availability**: 6 copies across 3 AZs
- **Read Replicas**: Up to 15
- **Backup**: Continuous, automated, PITR
- **Global Database**: Cross-region replication
- **ACID Compliance**: Full transactions

### Exam Tips & Heuristics
✅ **When to Use Neptune:**
- Social networks and relationships
- Fraud detection
- Recommendation engines
- Knowledge graphs
- Network/IT operations

⚠️ **Common Pitfalls:**
- Not for relational data (use RDS)
- Not for document data (use DocumentDB/DynamoDB)
- Requires understanding of graph query languages

🎯 **Exam Heuristics:**
- "Graph database" = **Neptune**
- "Social network" + "relationships" = **Neptune**
- "Fraud detection" + "connected data" = **Neptune**
- "Recommendation engine" = **Neptune** or **Personalize**
- "Knowledge graph" = **Neptune**

---

## QLDB (Quantum Ledger Database)

### Definition
Fully managed ledger database with immutable, cryptographically verifiable transaction log.

### Key Features
- **Immutable**: Cannot delete or modify records
- **Transparent**: Complete verifiable history
- **Cryptographic Verification**: SHA-256 hash chaining
- **SQL-like**: PartiQL query language
- **Serverless**: Automatic scaling
- **ACID**: Full transaction support
- **Streaming**: Stream data to Kinesis

### Exam Tips & Heuristics
✅ **When to Use QLDB:**
- Financial transaction history
- Supply chain tracking
- Regulatory compliance
- Audit trail requirements
- Immutable record-keeping

⚠️ **Common Pitfalls:**
- Not a blockchain (centralized ledger)
- Cannot delete data (immutable by design)
- Not for general-purpose database needs

🎯 **Exam Heuristics:**
- "Immutable" + "audit trail" = **QLDB**
- "Ledger" or "financial transactions" = **QLDB**
- "Cryptographically verifiable" = **QLDB**
- "Supply chain tracking" = **QLDB**
- "Need blockchain" = **Managed Blockchain** (not QLDB)

---

## Timestream

### Definition
Fully managed time series database for IoT and operational applications.

### Key Features
- **Purpose-Built**: Optimized for time series data
- **Serverless**: Auto-scaling
- **Storage Tiers**: Memory store (recent data), magnetic store (historical)
- **Built-in Functions**: Time series analytics functions
- **Adaptive Query Processing**: Optimizes queries automatically
- **Encryption**: At rest and in transit

### Exam Tips & Heuristics
✅ **When to Use Timestream:**
- IoT sensor data
- Application monitoring
- DevOps metrics
- Time series analytics

⚠️ **Common Pitfalls:**
- Specialized for time series only
- Not for relational or document data

🎯 **Exam Heuristics:**
- "Time series" = **Timestream**
- "IoT data" + "time-based analytics" = **Timestream**
- "DevOps metrics over time" = **Timestream** or **CloudWatch**
- "Application monitoring" = **Timestream** or **CloudWatch**

---

## Database Migration Service (DMS)

### Definition
Managed service for migrating databases to AWS with minimal downtime.

### Key Features
- **Homogeneous Migrations**: Same engine (e.g., Oracle to Oracle)
- **Heterogeneous Migrations**: Different engines (e.g., Oracle to Aurora), requires **Schema Conversion Tool (SCT)**
- **Continuous Replication**: Change Data Capture (CDC)
- **Supported Sources**: Oracle, SQL Server, MySQL, PostgreSQL, MongoDB, SAP, DB2, Azure SQL, S3
- **Supported Targets**: RDS, Aurora, Redshift, DynamoDB, S3, DocumentDB, Neptune, Kinesis
- **Replication Instance**: EC2 instance running replication tasks
- **Snowball Integration**: For large migrations with limited bandwidth

### Exam Tips & Heuristics
✅ **When to Use DMS:**
- Migrate databases to AWS
- Continuous replication for DR
- Database consolidation
- Development/test database refresh

⚠️ **Common Pitfalls:**
- Heterogeneous migrations more complex (need SCT)
- Replication instance sizing important for performance
- Source database must have binary logging enabled
- Not for data transformation (basic only)

🎯 **Exam Heuristics:**
- "Database migration" = **DMS**
- "Migrate with minimal downtime" = **DMS** with CDC
- "Oracle to Aurora" = **DMS + SCT**
- "Same engine migration" = **DMS** only
- "Different engine migration" = **DMS + SCT**
- "Large database + limited bandwidth" = **DMS + Snowball**
- "Continuous replication" = **DMS** (CDC mode)

---

## 🎓 Database Service Selection Guide

| Requirement | Service |
|------------|---------|
| RDBMS, ACID, SQL | RDS, Aurora |
| Highest performance MySQL/PostgreSQL | Aurora |
| NoSQL, key-value, millisecond latency | DynamoDB |
| In-memory caching | ElastiCache |
| Data warehouse, OLAP | Redshift |
| MongoDB-compatible | DocumentDB |
| Graph database | Neptune |
| Immutable ledger | QLDB |
| Time series data | Timestream |
| Database migration | DMS |

---

## 💡 Key Exam Patterns

### High Availability Patterns
- RDS Multi-AZ for automatic failover
- Aurora with multiple read replicas
- DynamoDB Global Tables for multi-region
- ElastiCache Redis with Multi-AZ

### Performance Optimization Patterns
- Read Replicas for read scaling
- ElastiCache for caching layer
- DynamoDB DAX for microsecond latency
- Aurora Parallel Query for analytics

### Cost Optimization Patterns
- Aurora Serverless for variable workloads
- DynamoDB On-Demand for unpredictable traffic
- RDS Reserved Instances for steady-state
- Stop/start dev/test databases

### Disaster Recovery Patterns
- Automated backups with PITR
- Cross-region Read Replicas
- Snapshot copies to other regions
- DynamoDB Global Tables
- Aurora Global Database

