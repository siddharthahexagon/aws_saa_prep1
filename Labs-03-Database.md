# AWS SAA-C03 Hands-On Labs: Database Services

## 📋 Overview
These labs cover RDS, Aurora, DynamoDB, ElastiCache, and other database services.

**Estimated Time:** 4-5 hours total  
**Cost Estimate:** $3-8 (RDS can be expensive - cleanup promptly!)  
**Free Tier Eligible:** Partially (some limitations)

---

## Lab 3.1: Amazon RDS - MySQL

### Objectives
- Create RDS MySQL instance
- Configure Multi-AZ deployment
- Create read replicas
- Implement automated backups

### Steps

#### Part A: Create RDS Instance

**Step 1: Create Security Group**
```
1. Go to EC2 → Security Groups
2. Create security group
3. Name: "lab-rds-sg"
4. Description: "Allow MySQL traffic"
5. VPC: Default
6. Inbound rules:
   - Type: MySQL/Aurora (3306)
   - Source: 0.0.0.0/0 (for lab only; use specific IPs in production)
7. Create
```

**Step 2: Create DB Subnet Group**
```
1. Go to RDS → Subnet groups
2. Create DB subnet group
3. Name: "lab-db-subnet-group"
4. Description: "Lab subnet group"
5. VPC: Default VPC
6. Add subnets:
   - Select at least 2 AZs
   - Select all default subnets
7. Create
```

**Step 3: Create RDS Instance**
```
1. Go to RDS → Databases → Create database
2. Standard create
3. Engine: MySQL
4. Version: MySQL 8.0.x (latest)
5. Templates: Free tier
```

**Step 4: Configure Settings**
```
1. DB instance identifier: "lab-mysql-db"
2. Master username: admin
3. Credentials management: Self managed
4. Master password: YourSecurePassword123!
5. Confirm password
```

**Step 5: Instance Configuration**
```
1. DB instance class: db.t3.micro (Free tier)
2. Storage type: General Purpose SSD (gp2)
3. Allocated storage: 20 GiB
4. Storage autoscaling: Enable
5. Maximum storage threshold: 100 GiB
```

**Step 6: Connectivity**
```
1. VPC: Default VPC
2. Subnet group: lab-db-subnet-group
3. Public access: Yes (for lab; No in production)
4. VPC security group: Choose existing → lab-rds-sg
5. Availability Zone: No preference
```

**Step 7: Additional Configuration**
```
1. Initial database name: labdb
2. DB parameter group: default
3. Option group: default
4. Backup:
   - Enable automated backups: Yes
   - Backup retention period: 7 days
   - Backup window: No preference
5. Encryption: Enable
6. Monitoring: Disable enhanced monitoring (Free tier)
7. Log exports: None
8. Maintenance window: No preference
9. Deletion protection: Disable (for lab)
10. Create database
```

**Step 8: Wait for Creation**
```
1. Wait 5-10 minutes for database to be available
2. Note the endpoint address
```

#### Part B: Connect to RDS

**Step 9: Connect Using MySQL Client**
```bash
# Install MySQL client if needed
# Amazon Linux:
sudo yum install -y mysql

# Or use an EC2 instance in the same VPC

# Connect
mysql -h lab-mysql-db.xxxxxxxxxxxx.us-east-1.rds.amazonaws.com -P 3306 -u admin -p

# Enter password when prompted
```

**Step 10: Create Sample Data**
```sql
-- Select database
USE labdb;

-- Create table
CREATE TABLE employees (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    department VARCHAR(50),
    salary DECIMAL(10,2),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Insert data
INSERT INTO employees (name, department, salary) VALUES
    ('John Doe', 'Engineering', 75000),
    ('Jane Smith', 'Marketing', 65000),
    ('Bob Johnson', 'Sales', 60000),
    ('Alice Brown', 'Engineering', 80000);

-- Query data
SELECT * FROM employees;

-- Exit
EXIT;
```

#### Part C: Create Read Replica

**Step 11: Create Read Replica**
```
1. Go to RDS → Databases
2. Select lab-mysql-db
3. Actions → Create read replica
4. Settings:
   - DB instance identifier: "lab-mysql-replica"
   - Instance class: db.t3.micro
   - Storage type: gp2
   - Destination region: Same region
   - Availability Zone: Different from primary
5. Create read replica
```

**Step 12: Test Read Replica**
```bash
# Connect to replica (read-only)
mysql -h lab-mysql-replica.xxxxxxxxxxxx.us-east-1.rds.amazonaws.com -P 3306 -u admin -p

USE labdb;
SELECT * FROM employees;

# Try to insert (should fail)
INSERT INTO employees (name, department, salary) VALUES ('Test', 'Test', 50000);
# Error: Read-only mode
```

#### Part D: Snapshots and Restore

**Step 13: Create Manual Snapshot**
```
1. Select primary database
2. Actions → Take snapshot
3. Snapshot name: "lab-mysql-snapshot"
4. Take snapshot
5. Wait for snapshot to complete
```

**Step 14: Restore from Snapshot**
```
1. Go to Snapshots
2. Select lab-mysql-snapshot
3. Actions → Restore snapshot
4. DB instance identifier: "lab-mysql-restored"
5. Keep other defaults
6. Restore DB instance
```

#### Part E: Multi-AZ Deployment

**Step 15: Convert to Multi-AZ**
```
1. Select primary database
2. Click "Modify"
3. Multi-AZ deployment: Yes
4. Apply immediately: Yes
5. Modify DB instance
6. Wait for modification (can take 10-15 minutes)
```

### Cleanup for Lab 3.1
```
1. Delete read replica (no snapshot needed)
2. Delete restored instance (no snapshot)
3. Delete primary database (skip final snapshot for lab)
4. Delete manual snapshots
5. Delete DB subnet group
6. Delete security group
```

---

## Lab 3.2: Amazon DynamoDB

### Objectives
- Create DynamoDB tables
- Perform CRUD operations
- Configure Global Secondary Indexes (GSI)
- Implement DynamoDB Streams

### Steps

#### Part A: Create DynamoDB Table

**Step 1: Create Table**
```
1. Go to DynamoDB → Tables → Create table
2. Table name: "lab-orders"
3. Partition key: order_id (String)
4. Sort key: order_date (String)
5. Settings: Customize settings
```

**Step 2: Configure Capacity**
```
1. Capacity mode: On-demand (recommended for unpredictable workloads)
   OR
   Provisioned:
   - Read capacity units: 5
   - Write capacity units: 5
   - Auto scaling: Enable
2. Encryption: AWS owned key
3. Create table
```

#### Part B: Add Items

**Step 3: Add Items via Console**
```
1. Go to table → Explore table items
2. Click "Create item"
3. Add attributes:
   order_id: "ORD001"
   order_date: "2024-01-15"
   customer_name: "John Doe"
   total_amount: 150.50
   status: "completed"
4. Create item
```

**Step 4: Add Multiple Items (JSON)**
```json
// Create several items with these values:

{
    "order_id": {"S": "ORD002"},
    "order_date": {"S": "2024-01-16"},
    "customer_name": {"S": "Jane Smith"},
    "total_amount": {"N": "250.00"},
    "status": {"S": "pending"}
}

{
    "order_id": {"S": "ORD003"},
    "order_date": {"S": "2024-01-16"},
    "customer_name": {"S": "Bob Johnson"},
    "total_amount": {"N": "75.25"},
    "status": {"S": "shipped"}
}
```

#### Part C: Query and Scan

**Step 5: Query Items**
```
1. Go to Explore table items
2. Query tab
3. Partition key: ORD001
4. Run query
5. Results show items with that partition key
```

**Step 6: Scan with Filters**
```
1. Scan tab
2. Add filter:
   - Attribute name: status
   - Type: String
   - Condition: Equals
   - Value: pending
3. Run scan
```

#### Part D: Global Secondary Index

**Step 7: Create GSI**
```
1. Go to table → Indexes tab
2. Create index
3. Index name: "status-index"
4. Partition key: status (String)
5. Sort key: order_date (String)
6. Attribute projections: All
7. Create index
8. Wait for index to become Active
```

**Step 8: Query Using GSI**
```
1. Explore table items → Query
2. Select "status-index" from dropdown
3. Partition key: completed
4. Run query
5. Returns all completed orders
```

#### Part E: DynamoDB Streams

**Step 9: Enable Streams**
```
1. Go to table → Exports and streams
2. DynamoDB stream details → Enable
3. View type: New and old images
4. Enable stream
```

**Step 10: Create Lambda Trigger**
```
1. Go to Lambda → Create function
2. Name: "lab-dynamodb-processor"
3. Runtime: Python 3.12
4. Create function
```

**Step 11: Add Stream Trigger**
```python
# Lambda code:
import json

def lambda_handler(event, context):
    for record in event['Records']:
        print(f"Event ID: {record['eventID']}")
        print(f"Event Name: {record['eventName']}")
        
        if record['eventName'] == 'INSERT':
            new_image = record['dynamodb']['NewImage']
            print(f"New Item: {json.dumps(new_image)}")
        elif record['eventName'] == 'MODIFY':
            print(f"Old: {record['dynamodb']['OldImage']}")
            print(f"New: {record['dynamodb']['NewImage']}")
        elif record['eventName'] == 'REMOVE':
            print(f"Deleted: {record['dynamodb']['OldImage']}")
    
    return {'statusCode': 200}
```

```
1. Add trigger → DynamoDB
2. Select lab-orders table
3. Batch size: 100
4. Starting position: Latest
5. Enable trigger
```

**Step 12: Test Stream**
```
1. Go to DynamoDB → Add new item
2. Check Lambda CloudWatch logs
3. Verify event was processed
```

#### Part F: DynamoDB Backup

**Step 13: Create On-Demand Backup**
```
1. Go to table → Backups
2. Create backup
3. Backup name: "lab-orders-backup"
4. Create backup
```

**Step 14: Enable Point-in-Time Recovery**
```
1. Go to Backups tab
2. Point-in-time recovery → Edit
3. Enable
4. Save changes
```

### Cleanup for Lab 3.2
```
1. Delete Lambda trigger
2. Delete Lambda function
3. Delete DynamoDB backups
4. Delete DynamoDB table
```

---

## Lab 3.3: Amazon ElastiCache (Redis)

### Objectives
- Create Redis cluster
- Connect from EC2
- Implement caching patterns

### Steps

#### Part A: Create ElastiCache Cluster

**Step 1: Create Security Group**
```
1. Create security group
2. Name: "lab-redis-sg"
3. Inbound rule:
   - Type: Custom TCP
   - Port: 6379
   - Source: Your EC2 security group or 0.0.0.0/0
```

**Step 2: Create Subnet Group**
```
1. Go to ElastiCache → Subnet groups
2. Create subnet group
3. Name: "lab-redis-subnet-group"
4. VPC: Default
5. Add all subnets
6. Create
```

**Step 3: Create Redis Cluster**
```
1. Go to ElastiCache → Redis clusters
2. Create Redis cluster
3. Configure cluster settings:
   - Cluster mode: Disabled
   - Name: "lab-redis"
   - Location: AWS Cloud
   - Multi-AZ: Disabled (for cost savings in lab)
```

**Step 4: Cluster Settings**
```
1. Node type: cache.t3.micro
2. Number of replicas: 0 (for lab)
3. Subnet group: lab-redis-subnet-group
4. Security groups: lab-redis-sg
5. Encryption in-transit: No (for lab simplicity)
6. Encryption at-rest: No
7. Automatic backups: Disabled
8. Create
9. Wait for status: available (5-10 minutes)
```

#### Part B: Connect to Redis

**Step 5: Launch EC2 Instance**
```
1. Launch t2.micro Amazon Linux 2023
2. Same VPC as Redis
3. Security group allowing SSH
```

**Step 6: Install Redis CLI**
```bash
# Connect to EC2

# Install Redis CLI
sudo yum install -y gcc
wget http://download.redis.io/redis-stable.tar.gz
tar xzf redis-stable.tar.gz
cd redis-stable
make redis-cli
sudo cp src/redis-cli /usr/local/bin/

# Get Redis endpoint from console
# Connect
redis-cli -h lab-redis.xxxxxx.cache.amazonaws.com -p 6379
```

**Step 7: Test Redis Operations**
```redis
# Set values
SET user:1:name "John Doe"
SET user:1:email "john@example.com"
SET counter 0

# Get values
GET user:1:name
GET user:1:email

# Increment counter
INCR counter
INCR counter
GET counter

# Set with expiration (60 seconds)
SETEX session:abc123 60 "user-data-here"
TTL session:abc123

# Hash operations
HSET user:2 name "Jane Smith" email "jane@example.com" age 30
HGETALL user:2

# List operations
LPUSH recent:orders "ORD001" "ORD002" "ORD003"
LRANGE recent:orders 0 -1

# Exit
QUIT
```

#### Part C: Caching with Application

**Step 8: Create Python Caching Example**
```python
# Save as cache_example.py

import redis
import time
import json

# Connect to Redis
r = redis.Redis(
    host='lab-redis.xxxxxx.cache.amazonaws.com',
    port=6379,
    decode_responses=True
)

# Simulate database lookup
def get_user_from_db(user_id):
    print(f"Fetching user {user_id} from database...")
    time.sleep(1)  # Simulate slow DB query
    return {
        'id': user_id,
        'name': f'User {user_id}',
        'email': f'user{user_id}@example.com'
    }

# Cache-aside pattern
def get_user(user_id):
    cache_key = f"user:{user_id}"
    
    # Try cache first
    cached = r.get(cache_key)
    if cached:
        print("Cache HIT!")
        return json.loads(cached)
    
    # Cache miss - get from DB
    print("Cache MISS!")
    user = get_user_from_db(user_id)
    
    # Store in cache for 5 minutes
    r.setex(cache_key, 300, json.dumps(user))
    return user

# Test
print("First call (cache miss):")
user = get_user(123)
print(f"Result: {user}\n")

print("Second call (cache hit):")
user = get_user(123)
print(f"Result: {user}")
```

```bash
# Install redis-py
pip3 install redis

# Run example
python3 cache_example.py
```

### Cleanup for Lab 3.3
```
1. Delete ElastiCache cluster
2. Delete subnet group
3. Delete security group
4. Terminate EC2 instance
```

---

## Lab 3.4: Amazon Aurora

### Objectives
- Create Aurora MySQL cluster
- Understand Aurora architecture
- Test failover behavior

### Steps

#### Part A: Create Aurora Cluster

**Step 1: Create Aurora Cluster**
```
1. Go to RDS → Create database
2. Choose Aurora
3. Edition: Amazon Aurora MySQL-Compatible
4. Capacity type: Provisioned
5. Engine version: Latest
```

**Step 2: Configure Templates**
```
1. Templates: Dev/Test (for cost savings)
2. DB cluster identifier: "lab-aurora-cluster"
3. Master username: admin
4. Master password: YourPassword123!
```

**Step 3: Instance Configuration**
```
1. DB instance class: db.t3.medium (minimum for Aurora)
2. Multi-AZ deployment: Don't create replica (save costs)
```

**Step 4: Connectivity**
```
1. VPC: Default
2. Subnet group: Create new or use existing
3. Public access: Yes (for lab)
4. Security group: Use lab-rds-sg or create new
5. Port: 3306
```

**Step 5: Additional Configuration**
```
1. Initial database: auroradb
2. Backup retention: 1 day
3. Encryption: Yes
4. Create database
```

#### Part B: Connect and Test

**Step 6: Connect to Writer Endpoint**
```bash
mysql -h lab-aurora-cluster.cluster-xxxx.us-east-1.rds.amazonaws.com -u admin -p

USE auroradb;

CREATE TABLE products (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    price DECIMAL(10,2)
);

INSERT INTO products (name, price) VALUES 
    ('Widget A', 19.99),
    ('Widget B', 29.99);

SELECT * FROM products;
```

**Step 7: Add Reader Instance**
```
1. Select Aurora cluster
2. Actions → Add reader
3. Instance identifier: "lab-aurora-reader"
4. Instance class: db.t3.medium
5. Add reader
```

**Step 8: Test Reader Endpoint**
```bash
# Connect to reader endpoint (different URL)
mysql -h lab-aurora-cluster.cluster-ro-xxxx.us-east-1.rds.amazonaws.com -u admin -p

USE auroradb;
SELECT * FROM products;

# Try to write (should fail)
INSERT INTO products (name, price) VALUES ('Widget C', 39.99);
# Error: Read-only mode
```

#### Part C: Aurora Features

**Step 9: Enable Backtrack (if available)**
```
1. Modify cluster
2. Enable backtrack
3. Target backtrack window: 24 hours
4. Apply changes
```

**Step 10: Clone Cluster**
```
1. Select cluster
2. Actions → Create clone
3. Clone identifier: "lab-aurora-clone"
4. Create clone
5. Note: Clone shares storage until data diverges
```

### Cleanup for Lab 3.4
```
1. Delete clone cluster
2. Delete reader instance
3. Delete writer instance
4. Delete cluster (skip final snapshot)
```

---

## Lab 3.5: Database Migration with DMS

### Objectives
- Set up AWS Database Migration Service
- Migrate data between databases

### Steps

**Step 1: Create Source RDS (if not exists)**
```
Create MySQL RDS instance with sample data (as in Lab 3.1)
```

**Step 2: Create Target DynamoDB Table**
```
1. Create DynamoDB table
2. Name: "migrated-employees"
3. Partition key: id (Number)
```

**Step 3: Create Replication Instance**
```
1. Go to DMS → Replication instances
2. Create replication instance
3. Name: "lab-dms-instance"
4. Instance class: dms.t3.micro
5. VPC: Default
6. Multi-AZ: No
7. Public: Yes
8. Create
```

**Step 4: Create Source Endpoint**
```
1. Go to DMS → Endpoints
2. Create endpoint
3. Endpoint type: Source
4. Identifier: "mysql-source"
5. Source engine: MySQL
6. Server name: RDS endpoint
7. Port: 3306
8. Username: admin
9. Password: Your password
10. Test connection
11. Create
```

**Step 5: Create Target Endpoint**
```
1. Create endpoint
2. Endpoint type: Target
3. Identifier: "dynamodb-target"
4. Target engine: Amazon DynamoDB
5. Service access role ARN: Create new role with DynamoDB access
6. Test connection
7. Create
```

**Step 6: Create Migration Task**
```
1. Go to DMS → Database migration tasks
2. Create task
3. Task identifier: "mysql-to-dynamodb"
4. Replication instance: lab-dms-instance
5. Source endpoint: mysql-source
6. Target endpoint: dynamodb-target
7. Migration type: Migrate existing data
8. Table mappings: Add selection rule for employees table
9. Create task
10. Start task
```

### Cleanup for Lab 3.5
```
1. Delete migration task
2. Delete endpoints
3. Delete replication instance
4. Delete DynamoDB table
5. Delete RDS instance (if no longer needed)
```

---

## 📝 Lab Summary & Key Takeaways

### What You Learned:
1. **RDS**: Instance creation, Multi-AZ, read replicas, snapshots
2. **DynamoDB**: Tables, GSI, streams, backups
3. **ElastiCache**: Redis setup, caching patterns
4. **Aurora**: Cluster architecture, reader/writer endpoints
5. **DMS**: Database migration between services

### Exam-Relevant Points:
- RDS Multi-AZ: Synchronous replication for HA
- RDS Read Replica: Asynchronous, can be cross-region
- DynamoDB: Partition key design is critical
- GSI: Different partition key from base table
- Aurora: 6 copies across 3 AZs automatically
- ElastiCache: Redis for complex data types, Memcached for simple

---

**Next:** [Labs-04-Networking](./Labs-04-Networking.md)
