# AWS Database Services - Complete Definitions (SAA-C03)

## Table of Contents
- [Amazon RDS](#amazon-rds)
- [Amazon Aurora](#amazon-aurora)
- [Amazon DynamoDB](#amazon-dynamodb)
- [Amazon ElastiCache](#amazon-elasticache)
- [Amazon Redshift](#amazon-redshift)
- [Amazon Neptune](#amazon-neptune)
- [Amazon DocumentDB](#amazon-documentdb)
- [Amazon Keyspaces](#amazon-keyspaces)
- [Amazon QLDB](#amazon-qldb)
- [Amazon Timestream](#amazon-timestream)
- [AWS Database Migration Service](#aws-database-migration-service)

---

## Amazon RDS (Relational Database Service)

**Definition**: Managed relational database service that makes it easy to set up, operate, and scale relational databases in the cloud. Supports multiple database engines.

**Supported Engines**:
- Amazon Aurora (MySQL and PostgreSQL compatible)
- MySQL
- PostgreSQL
- MariaDB
- Oracle
- Microsoft SQL Server

**Key Features**:
- Automated provisioning and patching
- Automatic backups with point-in-time recovery
- Multi-AZ deployments for high availability
- Read replicas for read scaling
- Encryption at rest and in transit
- Automated failover
- Monitoring via CloudWatch
- Manual scaling (vertical and horizontal)

**Deployment Options**:

### Single-AZ Deployment
- One DB instance in single Availability Zone
- Lower cost
- No automatic failover
- Use for dev/test environments

### Multi-AZ Deployment
- Primary instance in one AZ, standby replica in another AZ
- Synchronous replication
- Automatic failover (60-120 seconds)
- RDS automatically switches to standby during:
  - AZ outage
  - Primary DB instance failure
  - DB instance server type change
  - OS patching
- No performance benefit (standby is passive)
- Use for production workloads

**Read Replicas**:
- Asynchronous replication from primary
- Up to 5 read replicas per DB instance (15 for Aurora)
- Can be in different AZ or different region
- Can be promoted to standalone DB
- Read-heavy workloads
- Reporting and analytics
- Cross-region disaster recovery

**Backup and Recovery**:
- **Automated Backups**:
  - Daily full backup during backup window
  - Transaction logs backed up every 5 minutes
  - Point-in-time recovery (PITR) to any second
  - Retention: 0-35 days (default 7 days)
  - Stored in S3
- **Manual Snapshots**:
  - User-initiated backups
  - Kept until explicitly deleted
  - Can be copied to other regions

**Scaling**:
- **Vertical**: Change instance type (requires downtime)
- **Horizontal**: Add read replicas
- **Storage**: Auto-scaling for storage (no downtime)

**Encryption**:
- At rest: AES-256 using KMS
- In transit: SSL/TLS
- Cannot encrypt existing unencrypted database (must create snapshot, copy with encryption, restore)

**Use Cases**:
- Web and mobile applications
- E-commerce applications
- Online gaming
- Enterprise applications requiring ACID compliance

**Exam Tips**:
- Multi-AZ for high availability (automatic failover)
- Read replicas for read scaling and DR
- Cannot SSH into RDS instances
- Backups happen during backup window (may cause latency)
- Retention period 0 disables automated backups
- Multi-AZ is synchronous, Read Replicas are asynchronous
- RDS manages maintenance, patching, backups
- Use Parameter Groups for engine configuration
- Use Option Groups for engine-specific features

---

## Amazon Aurora

**Definition**: MySQL and PostgreSQL-compatible relational database engine that combines performance and availability of commercial databases with simplicity and cost-effectiveness of open source.

**Key Features**:
- 5x performance of MySQL, 3x performance of PostgreSQL
- Up to 128 TB storage per database instance
- Storage auto-scales in 10 GB increments
- Up to 15 read replicas
- Continuous backup to S3
- Point-in-time recovery
- Fast database cloning
- Global database (cross-region)

**Architecture**:
- Shared storage architecture
- 6 copies of data across 3 AZs
- 4 out of 6 copies needed for writes
- 3 out of 6 copies needed for reads
- Self-healing with peer-to-peer replication
- Storage automatically striped across hundreds of volumes

**High Availability**:
- **Aurora Replicas** (up to 15):
  - Within same region
  - Automated failover in <30 seconds
  - Priority tiers for failover
  - Can be in different AZs
- **Aurora Multi-Master**: Multiple write nodes (all Active)
- **Global Database**: 
  - Primary region + up to 5 secondary regions
  - < 1 second replication lag
  - Disaster recovery with RTO < 1 minute

**Endpoints**:
- **Cluster Endpoint**: Writer endpoint (points to primary)
- **Reader Endpoint**: Load balance across read replicas
- **Custom Endpoint**: User-defined subset of instances
- **Instance Endpoint**: Specific instance

**Aurora Serverless**:
- On-demand, auto-scaling configuration
- Automatically starts up, shuts down, scales capacity
- Pay per second of database capacity used
- Good for infrequent, intermittent, or unpredictable workloads
- v2 supports instant scaling

**Aurora Serverless v2**:
- Instant scaling (fraction of a second)
- Fine-grained scaling increments
- Scales down to 0.5 ACU
- Supports all Aurora features

**Backtrack**:
- Rewind database to specific point in time
- No need to restore from backup
- Available for Aurora MySQL only

**Database Cloning**:
- Create new cluster from existing one
- Fast (uses copy-on-write protocol)
- No impact on source database
- Good for testing

**Use Cases**:
- Enterprise applications
- SaaS applications
- Web and mobile games
- High-performance applications requiring < 30 second failover
- Applications requiring global footprint (Global Database)

**Exam Tips**:
- Aurora is AWS's proprietary database (MySQL/PostgreSQL compatible)
- 5x performance improvement over standard MySQL
- Storage auto-scales from 10 GB to 128 TB
- Up to 15 read replicas with sub-10ms replica lag
- Failover is automatic and faster than RDS (<30 seconds)
- Aurora Serverless for variable, unpredictable workloads
- Global Database for cross-region disaster recovery
- Backtrack for quick point-in-time recovery without backup restore
- More expensive than RDS but better performance and features
- Cannot encrypt existing unencrypted Aurora database

---

## Amazon DynamoDB

**Definition**: Fully managed NoSQL database service providing fast and predictable performance with seamless scalability. Key-value and document database.

**Key Features**:
- Fully managed (serverless)
- Single-digit millisecond latency at any scale
- Flexible schema
- Built-in security, backup, restore
- In-memory caching with DAX
- Global tables (multi-region, multi-master)
- Event-driven programming with streams
- ACID transactions

**Data Model**:
- **Tables**: Collection of items
- **Items**: Collection of attributes (similar to row)
- **Attributes**: Fundamental data element (similar to field)
- **Primary Key**: 
  - Partition Key (simple primary key)
  - Partition Key + Sort Key (composite primary key)

**Capacity Modes**:

### On-Demand
- Pay per request
- No capacity planning required
- Automatic scaling
- Suitable for unpredictable workloads
- More expensive per request

### Provisioned
- Specify read/write capacity units
- Auto Scaling available
- Reserved capacity for cost savings
- Suitable for predictable workloads
- Lower cost with consistent traffic

**Read Consistency**:
- **Eventually Consistent Reads** (default):
  - Lower latency
  - May not reflect recent write
  - Uses 0.5 RCU
- **Strongly Consistent Reads**:
  - Returns most up-to-date data
  - Higher latency
  - Uses 1 RCU
  - Not supported on global secondary indexes

**Indexes**:

### Local Secondary Index (LSI)
- Same partition key, different sort key
- Must be created at table creation time
- Maximum 5 per table
- Shares throughput with base table
- Supports strongly consistent reads

### Global Secondary Index (GSI)
- Different partition key and/or sort key
- Can be created at any time
- Maximum 20 per table
- Has its own throughput settings
- Only eventually consistent reads

**DynamoDB Accelerator (DAX)**:
- In-memory cache for DynamoDB
- Microsecond latency
- No code changes required
- Write-through caching
- Reduces read load on DynamoDB tables

**DynamoDB Streams**:
- Ordered stream of item-level modifications
- 24-hour retention
- Trigger Lambda functions
- Real-time processing
- Cross-region replication

**Global Tables**:
- Multi-region, multi-master replication
- Active-active configuration
- Sub-second replication
- Requires DynamoDB Streams enabled
- Disaster recovery and low latency for global users

**Backup and Restore**:
- **On-Demand Backup**: Full backups at any time
- **Point-in-Time Recovery (PITR)**: 
  - Continuous backups (last 35 days)
  - Restore to any second within retention
  - No performance impact
- Backups stored until deleted

**DynamoDB Transactions**:
- ACID compliance
- All-or-nothing operations
- Coordinated writes across multiple tables
- Up to 25 items or 4 MB per transaction

**Time to Live (TTL)**:
- Automatically delete items after expiry
- No additional cost
- Reduces storage costs
- Good for session data, temporary data

**Use Cases**:
- Mobile and web applications
- Gaming applications
- IoT applications
- Real-time bidding
- Shopping carts
- Session management

**Exam Tips**:
- DynamoDB is fully managed NoSQL (serverless)
- Single-digit millisecond latency
- Choose Partition Key with high cardinality
- On-Demand for unpredictable workloads, Provisioned for predictable
- Use DAX for microsecond latency (caching)
- Global Tables for multi-region applications
- DynamoDB Streams for event-driven architectures
- Use TTL to automatically delete old items
- Eventually consistent by default (strongly consistent optional)
- Maximum item size: 400 KB
- Transactions for ACID requirements

---

## Amazon ElastiCache

**Definition**: Fully managed in-memory data store and cache service supporting Redis and Memcached engines. Improves application performance by retrieving data from fast, managed, in-memory caches.

**Supported Engines**:

### Redis
- Data structures (strings, lists, sets, sorted sets, hashes)
- Persistence (snapshots and AOF)
- Replication (Multi-AZ with automatic failover)
- Backup and restore
- Sorted sets for leaderboards
- Pub/sub messaging
- Geospatial data
- Lua scripting

### Memcached
- Simple key-value store
- Multi-threaded architecture
- No persistence
- No replication
- No backup and restore
- Horizontal scaling (sharding)
- Simpler data structures

**Comparison**:

| Feature | Redis | Memcached |
|---------|-------|-----------|
| **Data Types** | Complex (lists, sets, hashes) | Simple (strings, objects) |
| **Persistence** | Yes | No |
| **Replication** | Yes (Multi-AZ) | No |
| **Backup/Restore** | Yes | No |
| **Multi-threaded** | No | Yes |
| **Pub/Sub** | Yes | No |
| **Sorted Sets** | Yes | No |
| **Transactions** | Yes | No |

**Redis Cluster Mode**:
- **Disabled** (Cluster Mode OFF):
  - Single shard (node group)
  - 0-5 read replicas
  - Scale vertically
  - Multi-AZ for failover
- **Enabled** (Cluster Mode ON):
  - Multiple shards (up to 500)
  - Each shard has primary + 0-5 replicas
  - Scale horizontally
  - Data partitioned across shards

**Security**:
- VPC for network isolation
- Security groups for access control
- Encryption at rest (Redis only)
- Encryption in transit (Redis only)
- Redis AUTH for password protection
- IAM authentication (Redis 6+)

**Use Cases**:
- Session store
- Database caching
- Leaderboards and gaming
- Real-time analytics
- Message queues (Redis pub/sub)
- Rate limiting
- Job and queue management

**Caching Strategies**:
- **Lazy Loading**: Load data into cache only when necessary
  - Cache miss: Read from database, write to cache
  - Cache hit: Read from cache
  - Stale data possible
- **Write-Through**: Add or update cache when database is updated
  - No stale data
  - Write penalty (slower writes)
- **Session Store**: Store temporary session data
- **Time-To-Live (TTL)**: Expire keys after certain time

**Exam Tips**:
- Use Redis for complex data structures and persistence
- Use Memcached for simple, scalable caching with multi-threading
- Redis supports Multi-AZ with automatic failover
- ElastiCache is used for caching, not primary data store
- Lazy loading is cache-aside pattern
- Redis Sorted Sets for real-time leaderboards
- Redis Pub/Sub for messaging
- Put ElastiCache in front of RDS/DynamoDB to reduce load
- Cluster Mode Enabled for horizontal scaling (Redis)

---

## Amazon Redshift

**Definition**: Fast, fully managed, petabyte-scale data warehouse service that makes it simple and cost-effective to analyze data using standard SQL and existing BI tools.

**Key Features**:
- Columnar storage
- Massively parallel processing (MPP)
- Compression
- Query optimization
- Result caching
- Petabyte scale
- SQL-based interface
- Integration with BI tools

**Architecture**:
- **Leader Node**: Receives queries, develops execution plans
- **Compute Nodes**: Execute queries, store data
- **Node Slices**: Parallel processing units within compute nodes

**Node Types**:
- **RA3 Nodes**: Latest generation, managed storage
  - Storage and compute scaling independent
  - Uses RMS (Redshift Managed Storage) on S3
- **DC2 Nodes**: Dense compute, SSD storage
  - For compute-intensive workloads
- **DS2 Nodes**: Dense storage, HDD (previous generation)

**Redshift Spectrum**:
- Query data directly in S3 without loading
- Extends Redshift to exabyte scale
- Pay only for queries run
- No data movement required
- Use with partitioned data for best performance

**Performance Optimization**:
- **Columnar Storage**: Store data by column
- **Compression**: Automatic compression
- **Distribution Styles**: 
  - KEY: Distribute based on column values
  - ALL: Copy entire table to all nodes
  - EVEN: Round-robin distribution
  - AUTO: Redshift decides
- **Sort Keys**: Define data order
- **Materialized Views**: Pre-computed query results
- **Result Caching**: Cache query results

**Backup and Disaster Recovery**:
- Automatic snapshots (1-35 days retention)
- Manual snapshots (until deleted)
- Snapshots stored in S3
- Cross-region snapshot copy
- Restore to any snapshot

**Security**:
- VPC for network isolation
- Encryption at rest (KMS or HSM)
- Encryption in transit (SSL)
- IAM for authentication
- Audit logging with CloudTrail
- Database audit logging

**Concurrency Scaling**:
- Automatically adds cluster capacity for concurrent queries
- Pay per second when active
- Handles burst of user activity

**Workload Management (WLM)**:
- Manage query priorities and resource allocation
- Define query queues
- Automatic or manual WLM

**Use Cases**:
- Business intelligence
- Reporting and analytics
- Data warehousing
- Log analysis
- Large-scale data analysis

**Exam Tips**:
- Redshift is for OLAP (Online Analytical Processing), not OLTP
- Single-AZ by default (can restore snapshot to another AZ)
- Columnar storage for better compression and performance
- Use Redshift Spectrum to query S3 data without loading
- Snapshot to S3 for backup (automatic and manual)
- Copy snapshots to another region for DR
- Enhanced VPC routing for VPC-only traffic
- Not a replacement for RDS (different use cases)
- Leader node is free (only pay for compute nodes)

---

## Amazon Neptune

**Definition**: Fast, reliable, fully managed graph database service that makes it easy to build and run applications that work with highly connected datasets.

**Key Features**:
- Purpose-built for graph data
- Supports two graph models:
  - Property Graph (Apache TinkerPop Gremlin)
  - RDF (SPARQL)
- ACID transactions
- High availability with read replicas
- Point-in-time recovery
- Continuous backup to S3
- Encryption at rest and in transit

**Architecture**:
- Primary instance for writes
- Up to 15 read replicas
- Multi-AZ deployment
- Automatic failover

**Use Cases**:
- Social networking
- Recommendation engines
- Fraud detection
- Knowledge graphs
- Network and IT operations
- Life sciences (drug discovery)

**Exam Tips**:
- Neptune is for graph databases (relationships)
- Use for highly connected data
- Supports both property graph and RDF
- Similar HA features to Aurora (read replicas, Multi-AZ)
- Good for recommendation engines and fraud detection

---

## Amazon DocumentDB

**Definition**: Fast, scalable, highly available, fully managed document database service that supports MongoDB workloads. MongoDB-compatible.

**Key Features**:
- MongoDB 3.6 and 4.0 compatible
- Fully managed
- Storage auto-scales up to 64 TB
- Up to 15 read replicas
- Continuous backup with PITR
- Multi-AZ high availability
- Encryption at rest and in transit

**Architecture**:
- Primary instance for writes
- Read replicas (up to 15) across AZs
- Shared storage architecture (similar to Aurora)
- Automatic failover

**Use Cases**:
- Content management
- Catalogs
- User profiles
- Mobile and web applications
- MongoDB migrations to AWS

**Exam Tips**:
- DocumentDB is MongoDB-compatible
- Fully managed document database
- Similar architecture to Aurora
- Use when migrating from MongoDB
- Multi-AZ with automatic failover
- Storage auto-scales

---

## Amazon Keyspaces

**Definition**: Scalable, highly available, managed Apache Cassandra-compatible database service.

**Key Features**:
- Cassandra-compatible (CQL API)
- Fully managed (serverless)
- Automatic scaling
- Single-digit millisecond latency
- Encryption at rest and in transit
- Point-in-time recovery
- Continuous backup

**Use Cases**:
- High-volume applications
- IoT device data
- Time-series data
- Cassandra migrations to AWS

**Exam Tips**:
- Keyspaces is Cassandra-compatible
- Serverless (no capacity planning)
- Use for Cassandra workloads on AWS

---

## Amazon QLDB (Quantum Ledger Database)

**Definition**: Fully managed ledger database providing transparent, immutable, and cryptographically verifiable transaction log.

**Key Features**:
- Immutable and transparent
- Cryptographic verification
- Serverless
- Centralized (unlike blockchain)
- SQL-like query language
- ACID transactions
- Streaming to Kinesis

**Components**:
- **Journal**: Immutable transaction log
- **PartiQL**: SQL-compatible query language
- **Digest**: Cryptographic hash of transaction history

**Use Cases**:
- Financial transactions
- Supply chain tracking
- Systems of record
- HR and payroll
- Regulatory compliance
- Immutable audit logs

**Exam Tips**:
- QLDB for immutable, cryptographically verifiable ledger
- Not decentralized (use Amazon Managed Blockchain for decentralized)
- Good for audit trails and compliance
- Cannot delete or modify records
- Serverless

---

## Amazon Timestream

**Definition**: Fast, scalable, fully managed time series database service for IoT and operational applications.

**Key Features**:
- Purpose-built for time series data
- Serverless
- Automatic scaling
- Data lifecycle management
- Built-in analytics functions
- SQL-compatible query language
- 1000x faster and 1/10th cost of relational databases

**Architecture**:
- **Memory Store**: Recent data (hours to days)
- **Magnetic Store**: Historical data (days to years)
- Automatic tiering between stores

**Use Cases**:
- IoT applications
- DevOps monitoring
- Application metrics
- Industrial telemetry
- Real-time analytics

**Exam Tips**:
- Timestream for time series data
- Serverless with automatic scaling
- Memory store for recent data, magnetic store for historical
- Good for IoT and operational metrics

---

## AWS Database Migration Service (DMS)

**Definition**: Managed migration and replication service that helps move databases to AWS quickly and securely with minimal downtime.

**Key Features**:
- Minimal downtime migrations
- Continuous data replication
- Support for homogeneous and heterogeneous migrations
- One-time migration or ongoing replication
- Schema conversion with AWS SCT (Schema Conversion Tool)

**Migration Types**:

### Homogeneous
- Same database engines (Oracle to Oracle, MySQL to MySQL)
- Direct migration
- No schema conversion needed

### Heterogeneous
- Different database engines (Oracle to Aurora, SQL Server to MySQL)
- Requires AWS Schema Conversion Tool (SCT)
- Two-step process: schema conversion then data migration

**Sources (On-Premises and AWS)**:
- Oracle, SQL Server, MySQL, PostgreSQL, MongoDB, SAP, DB2
- Azure SQL Database
- Amazon RDS, Aurora, S3, DocumentDB

**Targets (AWS)**:
- RDS, Aurora, Redshift, DynamoDB
- S3, OpenSearch, Kinesis, DocumentDB, Neptune
- Kafka

**Replication Instance**:
- EC2 instance that runs replication tasks
- Connects to source and target
- Performs data migration and replication

**Replication Tasks**:
- **Full Load**: One-time migration of existing data
- **Full Load + CDC**: Initial load plus ongoing changes
- **CDC Only**: Replicate only ongoing changes

**Schema Conversion Tool (SCT)**:
- Convert schema between different engines
- Identifies incompatibilities
- Converts stored procedures, functions, views
- Assessment report for migration complexity

**Use Cases**:
- Database migration to AWS
- Database consolidation
- Continuous data replication
- Development and test copy
- Database replication for analytics

**Exam Tips**:
- DMS for database migrations with minimal downtime
- Use SCT for heterogeneous migrations (different engines)
- Can migrate from on-premises to AWS, AWS to AWS, AWS to on-premises
- Ongoing replication with CDC (Change Data Capture)
- Snowball Edge can be used with DMS for large migrations
- Replication instance size affects performance
- Use DMS for moving data to Redshift, S3, DynamoDB
- Multi-AZ for high availability of replication instance

---

## Summary Comparison Table

| Service | Type | Use Case | Scaling |
|---------|------|----------|---------|
| **RDS** | Relational (SQL) | OLTP, ACID transactions | Vertical + Read Replicas |
| **Aurora** | Relational (SQL) | High-performance OLTP | Vertical + 15 Read Replicas |
| **DynamoDB** | NoSQL (Key-Value) | Low-latency at scale | Automatic |
| **ElastiCache** | In-Memory Cache | Caching, session store | Horizontal |
| **Redshift** | Data Warehouse | OLAP, analytics | Horizontal (add nodes) |
| **Neptune** | Graph Database | Relationships, social | Vertical + Read Replicas |
| **DocumentDB** | Document Database | MongoDB workloads | Vertical + Read Replicas |
| **Keyspaces** | Wide-Column | Cassandra workloads | Automatic |
| **QLDB** | Ledger Database | Immutable audit log | Automatic |
| **Timestream** | Time Series | IoT, metrics | Automatic |

---

## Database Selection Decision Tree

**Need SQL?**
- **Yes** → OLTP or OLAP?
  - **OLTP** → Standard or High Performance?
    - Standard → **RDS**
    - High Performance → **Aurora**
  - **OLAP** → **Redshift**
- **No** → What data model?
  - **Key-Value** → **DynamoDB**
  - **Document** → **DocumentDB**
  - **Graph** → **Neptune**
  - **Wide-Column** → **Keyspaces**
  - **Time Series** → **Timestream**
  - **Ledger** → **QLDB**
  - **In-Memory Cache** → **ElastiCache**

---

## Exam Focus Areas

1. **RDS vs Aurora**: When to use each
2. **Multi-AZ vs Read Replicas**: High availability vs read scaling
3. **DynamoDB Capacity Modes**: On-Demand vs Provisioned
4. **ElastiCache Engines**: Redis vs Memcached features
5. **Redshift**: OLAP use cases and Redshift Spectrum
6. **Database Migration**: DMS for migrations with minimal downtime
7. **Graph Data**: Use Neptune for relationship-heavy data
8. **Caching**: When to use ElastiCache, DAX
9. **Global Databases**: Aurora Global, DynamoDB Global Tables
10. **Backup and Recovery**: Automated backups, snapshots, PITR

---

*Last Updated: January 2026 - SAA-C03 Exam*
