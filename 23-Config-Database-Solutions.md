# Configuration Questionnaire: Database Solutions
## Step-by-Step Database Selection and Configuration

---

## 📋 Table of Contents
1. [Relational Database Migration](#relational-database-migration)
2. [NoSQL Database Configuration](#nosql-database-configuration)
3. [Multi-Region Database Setup](#multi-region-database-setup)
4. [Caching Strategies](#caching-strategies)
5. [Database Disaster Recovery](#database-disaster-recovery)
6. [Hybrid Database Architectures](#hybrid-database-architectures)

---

## 🗄️ SCENARIO 1: Relational Database Migration

### Initial Questions:
```
Q1: What is the source database?
    ├─ MySQL → RDS MySQL or Aurora MySQL
    ├─ PostgreSQL → RDS PostgreSQL or Aurora PostgreSQL
    ├─ Oracle → RDS Oracle or Aurora PostgreSQL (with Babelfish)
    ├─ SQL Server → RDS SQL Server
    └─ Other → Consider DMS for migration

Q2: What is the database size?
    ├─ < 100 GB → RDS (any instance size)
    ├─ 100 GB - 1 TB → RDS or Aurora
    └─ > 1 TB → Aurora (better performance/cost)

Q3: What is the read:write ratio?
    ├─ Mostly reads → Add Read Replicas
    ├─ Balanced → Standard Multi-AZ
    └─ Write-heavy → Aurora with multiple writers

Q4: What are the availability requirements?
    ├─ 99% → Single-AZ RDS
    ├─ 99.9% → Multi-AZ RDS
    └─ 99.99%+ → Multi-AZ Aurora or Global Database
```

### Configuration Matrix:

#### **MySQL/PostgreSQL Migration to Aurora**
```
Services Required:
├─ DMS (Database Migration Service)
├─ Aurora Cluster (Target)
├─ SCT (Schema Conversion Tool - if needed)
├─ Secrets Manager (Credentials)
├─ CloudWatch (Monitoring)
└─ Lambda (Automation)

Configuration Steps:

[Step 1: Pre-Migration Assessment]
1. Schema Conversion Tool (SCT):
   - Download and install SCT
   - Connect to source database
   - Generate assessment report:
     • Automatic conversion: ~95%
     • Manual intervention needed: ~5%
     • Incompatibilities highlighted
   
2. Review Assessment:
   - Note unsupported features
   - Plan for code changes
   - Estimate migration time

[Step 2: Target Aurora Setup]
3. Create Aurora Cluster:
   - Engine: Aurora MySQL 3.x or Aurora PostgreSQL 14.x
   - Template: Production
   - DB cluster identifier: "myapp-aurora-cluster"
   - Credentials:
     • Master username: admin
     • Store in Secrets Manager
     • Enable automatic rotation
   
4. Instance Configuration:
   - DB instance class: db.r6g.xlarge (start here)
   - Multi-AZ: YES (3 AZs recommended)
   - First instance: Writer
   - Additional instances: 1-2 Readers
   
5. Storage:
   - Aurora automatically manages storage
   - Auto-scales from 10GB to 128TB
   - I/O-Optimized or Standard (choose based on cost)
   
6. Network & Security:
   - VPC: Existing or new
   - Subnet group: Private subnets in multiple AZs
   - Security group: Allow 3306/5432 from app tier only
   - Public accessibility: NO
   - Encryption: YES (KMS key)
   
7. Advanced Settings:
   - Backup retention: 7 days (35 max for compliance)
   - Backup window: 03:00-04:00 UTC
   - Maintenance window: Sun 04:00-05:00 UTC
   - Enable enhanced monitoring (60-second granularity)
   - Enable Performance Insights (7 days retention)
   - Enable deletion protection
   - Parameter group: Custom (optimize based on workload)
   - Option group: Default (unless specific features needed)

[Step 3: DMS Replication Instance]
8. Create DMS Replication Instance:
   - Name: "mysql-to-aurora-replication"
   - Instance class: dms.c5.xlarge
   - Engine version: 3.4.7 (latest)
   - Allocated storage: 100 GB (auto-scales to 6TB)
   - VPC: Same as Aurora
   - Multi-AZ: YES (for production)
   - Public accessibility: NO
   
9. Replication Instance Settings:
   - Subnet group: Private subnets
   - Security group: Allow from source and target
   - KMS encryption: Enabled
   - Maintenance window: Sun 05:00-06:00 UTC

[Step 4: DMS Endpoints]
10. Source Endpoint (On-premises/EC2 MySQL):
    - Endpoint type: Source
    - Engine: MySQL
    - Server: source-db.company.com
    - Port: 3306
    - SSL mode: Require (if supported)
    - Username/Password: From Secrets Manager
    - Test connection
    
11. Target Endpoint (Aurora):
    - Endpoint type: Target
    - Engine: Aurora MySQL
    - Server: aurora-cluster-endpoint
    - Port: 3306
    - SSL mode: Require
    - Username/Password: From Secrets Manager
    - Extra connection attributes:
      • initstmt=SET FOREIGN_KEY_CHECKS=0
    - Test connection

[Step 5: DMS Migration Task]
12. Create Database Migration Task:
    - Task identifier: "full-load-and-cdc"
    - Replication instance: Select above
    - Source endpoint: MySQL source
    - Target endpoint: Aurora target
    - Migration type: Full load + CDC (ongoing replication)
    
13. Task Settings:
    - Target table preparation: Drop tables on target
    - LOB mode: Limited LOB mode (max 32 KB)
    - Enable validation: YES (for critical data)
    - Enable CloudWatch logs: YES
    - Task recovery: Enable recovery for task errors
    
14. Table Mappings:
    - Selection rules:
      ```json
      {
        "rules": [
          {
            "rule-type": "selection",
            "rule-id": "1",
            "rule-name": "include-all-tables",
            "object-locator": {
              "schema-name": "mydb",
              "table-name": "%"
            },
            "rule-action": "include"
          },
          {
            "rule-type": "transformation",
            "rule-id": "2",
            "rule-name": "convert-lowercase",
            "rule-action": "convert-lowercase",
            "rule-target": "schema",
            "object-locator": {
              "schema-name": "%"
            }
          }
        ]
      }
      ```
    
15. Start Migration Task:
    - Monitor in DMS console
    - Check CloudWatch logs
    - Watch table statistics

[Step 6: Validation & Cutover]
16. Validation Phase:
    - Compare row counts: Source vs Target
    - Run test queries on Aurora
    - Performance testing with read replicas
    - Validate data integrity
    - Test application with Aurora endpoint
    
17. Pre-Cutover Checklist:
    ✅ Full load completed (100%)
    ✅ CDC lag < 10 seconds
    ✅ All validations passed
    ✅ Application tested with Aurora
    ✅ Rollback plan documented
    ✅ Team notified
    ✅ Maintenance window scheduled
    
18. Cutover Process:
    a. Stop source database writes (maintenance mode)
    b. Wait for CDC to catch up (lag = 0)
    c. Verify final row counts match
    d. Update application connection strings
    e. Point DNS/Load balancer to Aurora endpoint
    f. Enable writes on Aurora
    g. Monitor for errors (15-30 minutes)
    h. If successful: Keep running
       If issues: Rollback to source

[Step 7: Post-Migration]
19. Optimize Aurora:
    - Add Read Replicas (1-15 readers)
    - Configure Auto Scaling for readers:
      • Min replicas: 1
      • Max replicas: 5
      • Target CPU: 70%
      • Target connections: 700 (per db.r6g.xlarge)
    - Enable Query Cache (if applicable)
    - Review slow query logs
    - Optimize indexes
    
20. Setup Monitoring:
    - CloudWatch Dashboards:
      • Database connections
      • CPU utilization
      • Read/Write IOPS
      • Replication lag
      • Freeable memory
    - Alarms:
      • High CPU (> 80%)
      • High connections (> 1000)
      • Low freeable memory (< 1 GB)
      • Replication lag (> 1000 ms)
    
21. Backup Strategy:
    - Automated backups: Enabled (7-35 days)
    - Manual snapshots: Weekly
    - Export to S3: Monthly (for compliance)
    - Cross-region backup: For DR

[Step 8: Decommission Source]
22. After Successful Migration (1-2 weeks):
    - Stop DMS replication task
    - Delete DMS resources (instance, endpoints, tasks)
    - Archive source database
    - Update documentation
    - Train team on Aurora features

Architecture Diagram:
Source MySQL → DMS Replication Instance → Aurora Cluster
                                            ├─ Writer Instance (AZ-1)
                                            ├─ Reader Instance (AZ-2)
                                            └─ Reader Instance (AZ-3)

Migration Timeline:
- Assessment: 1-2 days
- Setup: 1 day
- Full load: Hours to days (depends on size)
- CDC sync: Continuous
- Testing: 3-7 days
- Cutover: 1-2 hours
- Total: 1-2 weeks

Cost Estimate:
- DMS replication instance: ~$300/month (during migration)
- Aurora db.r6g.xlarge writer: ~$350/month
- Aurora readers (2x): ~$700/month
- Storage: ~$0.10/GB/month
- Backups: ~$0.021/GB/month
- Total: ~$1,400-2,000/month

Performance Benefits:
✅ 5x better performance than MySQL
✅ Automatic failover (< 30 seconds)
✅ Read scaling with replicas
✅ Storage auto-scales to 128 TB
✅ Point-in-time recovery
```

#### **Oracle to PostgreSQL Migration (Heterogeneous)**
```
Additional Services:
├─ SCT (Schema Conversion Tool - required)
├─ Aurora PostgreSQL with Babelfish (SQL Server compat)
└─ Application code refactoring

Key Differences:
1. SCT is mandatory (different SQL dialects)
2. Manual code conversion needed (~20-30%)
3. Stored procedures require rewriting
4. Oracle-specific features need alternatives
5. Extended testing phase (2-4 weeks)

Configuration:
[Same as above, but with these additions]

1. SCT Data Extraction Agents:
   - For large databases (> 1 TB)
   - Parallel data extraction
   - Speeds up migration 5-10x

2. Code Conversion:
   - PL/SQL → PL/pgSQL
   - Oracle packages → PostgreSQL schemas
   - Sequences, triggers, functions

3. Extended Validation:
   - Query result comparison
   - Performance benchmarking
   - Load testing

Migration Timeline: 2-3 months (vs 1-2 weeks for homogeneous)
```

---

## 📊 SCENARIO 2: NoSQL Database Configuration

### Initial Questions:
```
Q1: What is the data access pattern?
    ├─ Key-value lookups → DynamoDB
    ├─ Document queries → DocumentDB or DynamoDB
    ├─ Graph relationships → Neptune
    └─ In-memory cache → ElastiCache

Q2: What is the consistency requirement?
    ├─ Eventual consistency OK → DynamoDB (default)
    ├─ Strong consistency required → DynamoDB (strong reads) or DocumentDB
    └─ ACID transactions → DocumentDB or Aurora

Q3: What is the traffic pattern?
    ├─ Unpredictable → DynamoDB On-Demand
    ├─ Predictable → DynamoDB Provisioned
    └─ Steady growth → Provisioned with Auto Scaling
```

### Configuration Matrix:

#### **DynamoDB for High-Scale Application**
```
Services Required:
├─ DynamoDB (Database)
├─ DynamoDB Streams (Change capture)
├─ Lambda (Stream processing)
├─ DAX (Caching layer)
├─ CloudWatch (Monitoring)
└─ Backup (Point-in-time recovery)

Configuration Steps:

[Step 1: Table Design]
1. Design Primary Key:
   - Choose partition key (high cardinality):
     • Good: user_id, product_id, order_id
     • Bad: status (low cardinality), date (hot partitions)
   - Add sort key if needed:
     • For range queries
     • For composite primary key
   
   Example 1: User Orders Table
   - Partition Key: user_id
   - Sort Key: order_timestamp
   - Allows: Get all orders for a user, sorted by time
   
   Example 2: E-commerce Products
   - Partition Key: category
   - Sort Key: product_id
   - Allows: Get all products in a category

2. Create Table:
   - Table name: "UserOrders"
   - Partition key: user_id (String)
   - Sort key: order_timestamp (Number)
   - Table settings:
     • Default settings OR Customize
   
3. Capacity Mode:
   Option A: On-Demand
   - Best for: Unpredictable traffic, new applications
   - Pricing: $1.25 per million write requests
   - No capacity planning needed
   - Instantly scales to any workload
   
   Option B: Provisioned
   - Best for: Predictable traffic, cost optimization
   - Set read/write capacity units:
     • Read Capacity Units (RCU): 1 RCU = 4 KB/sec strongly consistent
     • Write Capacity Units (WCU): 1 WCU = 1 KB/sec
   - Example calculation:
     • 1000 items/sec × 2 KB each = 2,000 KB/sec
     • WCU needed = 2,000 (round up)
   
4. Auto Scaling (for Provisioned):
   - Target utilization: 70%
   - Min capacity: 5 RCU, 5 WCU
   - Max capacity: 1000 RCU, 1000 WCU
   - Scales up: When utilization > 70%
   - Scales down: After sustained low usage

[Step 2: Secondary Indexes]
5. Global Secondary Index (GSI):
   - Use case: Query by different attributes
   - Example: Query orders by status
     • Index name: StatusIndex
     • Partition key: order_status
     • Sort key: order_timestamp
     • Projected attributes: ALL (or specific)
   - Capacity: Separate from table (set independently)
   - Limitations: Eventually consistent only
   
6. Local Secondary Index (LSI):
   - Use case: Alternative sort key
   - Must be created at table creation time
   - Shares capacity with table
   - Example:
     • Table: user_id (PK), order_timestamp (SK)
     • LSI: user_id (PK), order_amount (SK)
     • Allows: Get most expensive orders for a user
   - Maximum: 5 LSIs per table

[Step 3: DynamoDB Streams]
7. Enable Streams:
   - View type: NEW_AND_OLD_IMAGES
   - Captures: All create/update/delete operations
   - Retention: 24 hours
   - Use cases:
     • Trigger Lambda for processing
     • Replicate to another table
     • Analytics pipeline
     • Audit logging
   
8. Lambda Stream Processor:
   - Trigger: DynamoDB Stream
   - Batch size: 100 records
   - Starting position: LATEST
   - Error handling: Retry 3 times, then DLQ
   - Example use case:
     • Send email when order status changes
     • Update search index (OpenSearch)
     • Replicate to data warehouse

[Step 4: DAX (DynamoDB Accelerator)]
9. When to Use DAX:
   ✅ Read-heavy workloads (10:1 read:write)
   ✅ Need microsecond latency
   ✅ Cost: Better than reading directly (at scale)
   ❌ Write-heavy workloads
   ❌ Strong consistency required for all reads
   
10. Create DAX Cluster:
    - Cluster name: "user-orders-cache"
    - Node type: dax.r5.large (2 vCPUs, 13.3 GB RAM)
    - Cluster size: 3 nodes (1 primary, 2 replicas)
    - Subnet group: Private subnets (multi-AZ)
    - Security group: Allow 8111 from application tier
    - Parameter group:
      • query-ttl-millis: 300000 (5 minutes)
      • record-ttl-millis: 300000
    
11. Update Application Code:
    ```python
    import boto3
    from botocore.exceptions import ClientError
    
    # Without DAX (direct DynamoDB)
    dynamodb = boto3.resource('dynamodb', region_name='us-east-1')
    table = dynamodb.Table('UserOrders')
    
    # With DAX
    import amazondax
    endpoint = 'user-orders-cache.abcdef.dax-clusters.us-east-1.amazonaws.com'
    dax = amazondax.AmazonDaxClient(endpoint=endpoint)
    
    # Queries automatically cached
    response = dax.get_item(
        TableName='UserOrders',
        Key={'user_id': 'U12345'}
    )
    ```

[Step 5: Backup & Recovery]
12. Point-in-Time Recovery (PITR):
    - Enable PITR: YES
    - Recovery window: 35 days
    - Restore to any second within window
    - Cost: Continuous backups (~$0.20/GB/month)
    
13. On-Demand Backups:
    - Create manual snapshots
    - Retained until deleted
    - Use for compliance, long-term retention
    - Can restore to different region
    
14. AWS Backup Integration:
    - Centralized backup management
    - Backup plans with schedules
    - Cross-region copy
    - Lifecycle management

[Step 6: Global Tables (Multi-Region)]
15. Enable Global Tables:
    - Requires: Streams enabled, empty table
    - Add replica regions:
      • us-east-1 (primary)
      • eu-west-1 (replica)
      • ap-southeast-1 (replica)
    - Replication lag: Typically < 1 second
    - Conflict resolution: Last-writer-wins
    
16. Benefits:
    ✅ Multi-region, multi-active
    ✅ Low latency reads/writes globally
    ✅ Automatic replication
    ✅ 99.999% availability SLA

[Step 7: Advanced Features]
17. Time To Live (TTL):
    - Automatically delete expired items
    - TTL attribute: expiration_time (epoch timestamp)
    - No additional cost
    - Use cases:
      • Session data
      • Temporary data
      • Log retention
    
18. Transactions:
    - ACID transactions across multiple items
    - Up to 25 items per transaction
    - All-or-nothing semantics
    - Example:
      ```python
      dynamodb = boto3.client('dynamodb')
      
      response = dynamodb.transact_write_items(
          TransactItems=[
              {
                  'Put': {
                      'TableName': 'Orders',
                      'Item': {'order_id': {'S': 'O123'}, ...}
                  }
              },
              {
                  'Update': {
                      'TableName': 'Inventory',
                      'Key': {'product_id': {'S': 'P456'}},
                      'UpdateExpression': 'SET stock = stock - :val',
                      'ExpressionAttributeValues': {':val': {'N': '1'}}
                  }
              }
          ]
      )
      ```

[Step 8: Monitoring & Optimization]
19. CloudWatch Metrics:
    - UserErrors (client-side errors)
    - SystemErrors (server-side errors)
    - ConsumedReadCapacityUnits
    - ConsumedWriteCapacityUnits
    - ThrottledRequests (critical!)
    
20. CloudWatch Alarms:
    - Throttled requests > 0
    - User errors > 10/minute
    - Consumed capacity > 80% of provisioned
    
21. Performance Optimization:
    - Use batch operations (BatchGetItem, BatchWriteItem)
    - Use eventual consistent reads when possible (50% cheaper)
    - Avoid hot partitions (distribute keys evenly)
    - Use sparse indexes (don't index every item)
    - Compress large attributes (> 10 KB)
    
22. Cost Optimization:
    - Use On-Demand for variable workloads
    - Use Provisioned + Reserved Capacity for steady loads
    - Evaluate Infrequent Access (IA) table class:
      • 60% cheaper storage
      • 25% more expensive requests
      • Good for: Data accessed < once per month
    - Enable TTL to auto-delete old data
    - Use S3 for large objects (store references in DynamoDB)

Architecture Diagram:
Application → DAX Cluster → DynamoDB Table
                              ├─ GSI (StatusIndex)
                              ├─ LSI (AmountIndex)
                              └─ Streams → Lambda → [OpenSearch/SNS/S3]

Global Architecture:
us-east-1 (DynamoDB) ←→ eu-west-1 (Replica)
                    ↓
              ap-southeast-1 (Replica)

Cost Estimate (Provisioned - 1000 RCU, 1000 WCU):
- Base capacity: ~$500/month
- Storage (100 GB): ~$25/month
- DAX cluster (3 nodes): ~$700/month
- Backups (PITR): ~$20/month
- Total: ~$1,245/month

Performance:
- Latency: Single-digit milliseconds (without DAX)
- Latency with DAX: Microseconds for cached reads
- Throughput: Virtually unlimited (with proper key design)
- Availability: 99.99% (single region), 99.999% (global tables)

Best Practices:
✅ Design partition key for even distribution
✅ Use sort keys for range queries
✅ Keep item size < 400 KB
✅ Use batch operations for multiple items
✅ Enable PITR for production tables
✅ Monitor throttling and adjust capacity
✅ Use sparse GSIs to save cost
✅ Implement caching (DAX or application-level)
```

#### **DocumentDB for MongoDB Workloads**
```
Services Required:
├─ DocumentDB Cluster
├─ DMS (Migration from MongoDB)
├─ Lambda (Application integration)
└─ CloudWatch (Monitoring)

Configuration:

1. Create DocumentDB Cluster:
   - Engine: DocDB 5.0
   - Instance class: db.r6g.large
   - Instances: 1 writer + 2 readers
   - Multi-AZ: YES
   
2. Network:
   - VPC: Private subnets
   - Security group: Port 27017
   - TLS required: YES
   
3. Authentication:
   - Username/password in Secrets Manager
   - Enable IAM authentication
   
4. Migrate from MongoDB:
   - Use DMS
   - Or: mongodump/mongorestore
   - Test compatibility (95%+ compatible)

Cost: $500-1,500/month
```

---

## 🌍 SCENARIO 3: Multi-Region Database Setup

### Configuration Matrix:

#### **Aurora Global Database**
```
Services Required:
├─ Aurora Global Database
├─ Route 53 (DNS failover)
├─ Lambda (Automated failover)
└─ CloudWatch (Monitoring)

Configuration Steps:

[Primary Region: us-east-1]
1. Create Aurora Cluster:
   - db.r6g.2xlarge (1 writer + 2 readers)
   - Multi-AZ enabled
   - Enhanced monitoring
   
2. Enable Global Database:
   - Global cluster identifier: "myapp-global"
   - Primary region: us-east-1
   
[Secondary Region: eu-west-1]
3. Add Secondary Region:
   - Same instance class
   - 1 writer (read-only) + 1 reader
   - Replication lag: < 1 second
   
[Application Configuration]
4. Connection Management:
   - Write endpoint: Primary region cluster endpoint
   - Read endpoint: Reader endpoint (either region)
   - Route 53 health checks for failover
   
5. Failover Process:
   - Manual: Promote secondary to primary (< 1 min)
   - Automated: Lambda + CloudWatch Events
   
RTO: < 1 minute
RPO: < 1 second (typical replication lag)
Cost: 2x primary region cost + data transfer
```

---

## ⚡ SCENARIO 4: Caching Strategies

### Configuration Matrix:

#### **Multi-Layer Caching**
```
Caching Layers:
1. CloudFront (Edge cache) - Static content
2. ElastiCache (Application cache) - Database queries
3. DAX (DynamoDB cache) - DynamoDB reads

ElastiCache Redis Configuration:

[Step 1: Cluster Mode Choice]
Option A: Cluster Mode Disabled
- Simple setup
- Max 5 replicas
- Single shard
- Good for: < 250 GB data

Option B: Cluster Mode Enabled
- Horizontal scaling
- Up to 500 shards
- Automatic sharding
- Good for: > 250 GB data

[Step 2: Create Redis Cluster]
1. Cluster Mode Enabled Setup:
   - Engine: Redis 7.0
   - Cluster name: "app-cache-cluster"
   - Shards: 3
   - Replicas per shard: 2
   - Node type: cache.r6g.large
   - Multi-AZ: YES
   - Auto-failover: Enabled
   
2. Network:
   - Subnet group: Private subnets (3 AZs)
   - Security group: Port 6379
   
3. Backup:
   - Automatic backups: Enabled
   - Backup retention: 7 days
   - Backup window: 03:00-04:00 UTC
   
4. Maintenance:
   - Auto-upgrade minor versions: YES
   - SNS notifications: Enabled

[Step 3: Caching Patterns]
5. Cache-Aside Pattern:
   ```python
   import redis
   import json
   
   redis_client = redis.Redis(
       host='app-cache-cluster.abcdef.cache.amazonaws.com',
       port=6379,
       decode_responses=True
   )
   
   def get_user(user_id):
       # Try cache first
       cache_key = f"user:{user_id}"
       cached = redis_client.get(cache_key)
       
       if cached:
           return json.loads(cached)
       
       # Cache miss - get from database
       user = get_from_database(user_id)
       
       # Store in cache (TTL: 1 hour)
       redis_client.setex(
           cache_key,
           3600,
           json.dumps(user)
       )
       
       return user
   ```
   
6. Write-Through Pattern:
   - Write to cache and database simultaneously
   - Ensures cache is always fresh
   - Higher write latency
   
7. TTL Strategy:
   - Hot data: 5-15 minutes
   - Warm data: 1 hour
   - Cold data: 24 hours
   - Session data: Session duration

[Step 4: Monitoring]
8. CloudWatch Metrics:
   - CacheHitRate (target > 80%)
   - CPUUtilization (< 70%)
   - NetworkBytesIn/Out
   - Evictions (should be minimal)
   
9. Alarms:
   - Cache hit rate < 70%
   - High CPU > 75%
   - High memory usage > 90%

Cost: $400-1,200/month (3-shard cluster)
```

---

## 🔄 SCENARIO 5: Database Disaster Recovery

### Configuration Matrix:

#### **Comprehensive DR Strategy**
```
RTO/RPO Requirements:
- Tier 1 (Critical): RTO < 1 hr, RPO < 15 min
- Tier 2 (Important): RTO < 4 hrs, RPO < 1 hr
- Tier 3 (Normal): RTO < 24 hrs, RPO < 24 hrs

Tier 1 Configuration:

Services Required:
├─ Aurora Global Database (Multi-region)
├─ Automated backups (Continuous)
├─ Cross-region snapshots
├─ Lambda (Failover automation)
├─ Route 53 (Health checks)
└─ CloudFormation (Infrastructure as Code)

Configuration:

1. Primary Region (us-east-1):
   - Aurora Multi-AZ cluster
   - Point-in-time recovery (35 days)
   - Automated backups (daily)
   
2. Secondary Region (us-west-2):
   - Aurora Global Database replica
   - Replication lag < 1 second
   - Promoted to primary in DR event
   
3. Backup Strategy:
   - Continuous (PITR): Every 5 minutes
   - Automated snapshots: Daily
   - Cross-region copy: Daily
   - Long-term retention: Monthly to S3
   
4. Failover Process:
   a. Detection:
      - Route 53 health checks fail
      - CloudWatch alarm triggers
   b. Automation:
      - Lambda promotes secondary
      - Updates Route 53 records
      - Notifies team via SNS
   c. Timeline:
      - Detection: < 2 minutes
      - Promotion: < 1 minute
      - DNS propagation: 1-5 minutes
      - Total RTO: < 10 minutes
   
5. Testing:
   - DR drill: Quarterly
   - Failover test: Monthly
   - Backup restore test: Weekly

Cost: 2.5x primary region (replica + backups)
```

---

## 🔗 SCENARIO 6: Hybrid Database Architectures

### Configuration Matrix:

#### **On-Premises + Cloud Database**
```
Services Required:
├─ DMS (Replication)
├─ Direct Connect or VPN
├─ RDS/Aurora (Cloud database)
├─ Database Migration Service
└─ DataSync (For files)

Configuration:

1. Connectivity:
   - AWS Direct Connect: 1 Gbps
   - Or: VPN connection
   - Private connectivity to VPC
   
2. DMS Continuous Replication:
   - Source: On-premises Oracle
   - Target: Aurora PostgreSQL
   - Mode: Full load + CDC
   - Latency: 5-30 seconds
   
3. Use Cases:
   - Read replica in cloud
   - Gradual migration
   - Disaster recovery
   - Cloud analytics on on-prem data

Cost: $500-2,000/month + network costs
```

---

## 📊 Quick Decision Matrix

| **Requirement** | **Solution** | **RTO** | **RPO** | **Cost** |
|-----------------|--------------|---------|---------|----------|
| Simple website DB | RDS Single-AZ | Hours | 5-15 min | $ |
| Production app | RDS Multi-AZ | < 2 min | 0 | $$ |
| High-perf relational | Aurora Multi-AZ | < 30 sec | 0 | $$$ |
| Global app | Aurora Global | < 1 min | < 1 sec | $$$$ |
| Key-value store | DynamoDB | N/A | < 1 sec | $-$$ |
| Document store | DocumentDB | < 30 sec | 0 | $$ |
| In-memory cache | ElastiCache | < 1 min | N/A | $$ |
| Data warehouse | Redshift | Hours | N/A | $$$ |

---

**Next**: See `24-Config-Microservices-Containers.md` for microservices configurations
