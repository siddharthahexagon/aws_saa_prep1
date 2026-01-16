# AWS SAA-C03 Hands-On Labs: Migration & Transfer Services

## 📋 Overview
These labs cover database migration, data transfer, and migration strategies.

**Estimated Time:** 3-4 hours total  
**Cost Estimate:** $2-5 (DMS replication instance costs)  
**Free Tier Eligible:** Partially

---

## Lab 10.1: AWS Database Migration Service (DMS)

### Objectives
- Set up DMS replication instance
- Migrate database between engines
- Implement continuous replication

### Steps

#### Part A: Create Source and Target Databases

**Step 1: Create Source MySQL Database**
```
1. RDS → Create database
2. Engine: MySQL
3. Templates: Free tier
4. DB instance identifier: "lab-source-mysql"
5. Master username: admin
6. Password: SourcePassword123!
7. Public access: Yes (for lab)
8. Initial database: sourcedb
9. Create database
```

**Step 2: Populate Source Database**
```sql
-- Connect to source database
mysql -h lab-source-mysql.xxx.us-east-1.rds.amazonaws.com -u admin -p

-- Create schema and data
USE sourcedb;

CREATE TABLE customers (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE orders (
    id INT AUTO_INCREMENT PRIMARY KEY,
    customer_id INT,
    total DECIMAL(10,2),
    status VARCHAR(20),
    order_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (customer_id) REFERENCES customers(id)
);

INSERT INTO customers (name, email) VALUES
    ('John Doe', 'john@example.com'),
    ('Jane Smith', 'jane@example.com'),
    ('Bob Johnson', 'bob@example.com');

INSERT INTO orders (customer_id, total, status) VALUES
    (1, 150.00, 'completed'),
    (1, 200.00, 'pending'),
    (2, 75.50, 'completed'),
    (3, 300.00, 'processing');
```

**Step 3: Create Target PostgreSQL Database**
```
1. RDS → Create database
2. Engine: PostgreSQL
3. Templates: Free tier
4. DB instance identifier: "lab-target-postgres"
5. Master username: admin
6. Password: TargetPassword123!
7. Public access: Yes
8. Initial database: targetdb
9. Create database
```

#### Part B: Set Up DMS

**Step 4: Create Replication Instance**
```
1. DMS → Replication instances → Create
2. Name: "lab-replication-instance"
3. Instance class: dms.t3.micro
4. Engine version: Latest
5. Storage: 50 GB
6. VPC: Default
7. Multi-AZ: No (for lab)
8. Public access: Yes
9. Create
10. Wait for status: Available
```

**Step 5: Create Source Endpoint**
```
1. DMS → Endpoints → Create endpoint
2. Endpoint type: Source
3. Endpoint identifier: "mysql-source"
4. Source engine: MySQL
5. Access to endpoint database: Provide access manually
6. Server name: lab-source-mysql.xxx.rds.amazonaws.com
7. Port: 3306
8. Username: admin
9. Password: SourcePassword123!
10. Test endpoint connection
11. Create endpoint
```

**Step 6: Create Target Endpoint**
```
1. Create endpoint
2. Endpoint type: Target
3. Endpoint identifier: "postgres-target"
4. Target engine: PostgreSQL
5. Server name: lab-target-postgres.xxx.rds.amazonaws.com
6. Port: 5432
7. Username: admin
8. Password: TargetPassword123!
9. Database name: targetdb
10. Test connection
11. Create endpoint
```

#### Part C: Create Migration Task

**Step 7: Create Task**
```
1. DMS → Database migration tasks → Create
2. Task identifier: "mysql-to-postgres"
3. Replication instance: lab-replication-instance
4. Source endpoint: mysql-source
5. Target endpoint: postgres-target
6. Migration type: Migrate existing data and replicate ongoing changes
```

**Step 8: Configure Task Settings**
```
1. Target table preparation mode: Drop tables on target
2. Include LOB columns: Limited LOB mode
3. Max LOB size: 32 KB
4. Enable validation: Yes
5. Enable CloudWatch logs: Yes
```

**Step 9: Configure Table Mappings**
```json
{
  "rules": [
    {
      "rule-type": "selection",
      "rule-id": "1",
      "rule-name": "include-all",
      "object-locator": {
        "schema-name": "sourcedb",
        "table-name": "%"
      },
      "rule-action": "include"
    },
    {
      "rule-type": "transformation",
      "rule-id": "2",
      "rule-name": "rename-schema",
      "rule-action": "rename",
      "rule-target": "schema",
      "object-locator": {
        "schema-name": "sourcedb"
      },
      "value": "public"
    }
  ]
}
```

**Step 10: Start Migration**
```
1. Review and create task
2. Start task: Automatically on create
3. Monitor progress in Table statistics tab
```

#### Part D: Verify Migration

**Step 11: Check Target Database**
```sql
-- Connect to PostgreSQL
psql -h lab-target-postgres.xxx.rds.amazonaws.com -U admin -d targetdb

-- Verify tables
\dt

-- Check data
SELECT * FROM customers;
SELECT * FROM orders;
```

**Step 12: Test Ongoing Replication**
```sql
-- In MySQL source, add new data
INSERT INTO customers (name, email) VALUES ('New Customer', 'new@example.com');
INSERT INTO orders (customer_id, total, status) VALUES (4, 500.00, 'new');

-- In PostgreSQL target, verify replication (wait a few seconds)
SELECT * FROM customers;
SELECT * FROM orders;
```

### Cleanup for Lab 10.1
```
1. Stop and delete DMS task
2. Delete endpoints
3. Delete replication instance
4. Delete RDS databases
5. Delete security groups
```

---

## Lab 10.2: AWS DataSync

### Objectives
- Transfer data between on-premises and S3
- Configure DataSync agents
- Schedule data transfers

### Steps

#### Part A: Simulate On-Premises with EC2

**Step 1: Create Source EC2 Instance**
```
1. Launch EC2 instance
2. Name: "lab-datasync-source"
3. Instance type: t2.micro
4. Storage: 20 GB
5. User data:
#!/bin/bash
yum update -y
yum install -y nfs-utils

# Create sample data directory
mkdir -p /data/files
for i in {1..100}; do
  dd if=/dev/urandom of=/data/files/file_$i.dat bs=1K count=$((RANDOM % 100 + 1))
done

# Set up NFS share
yum install -y nfs-utils
echo "/data *(rw,sync,no_root_squash)" >> /etc/exports
systemctl enable nfs-server
systemctl start nfs-server
exportfs -a
```

**Step 2: Create Destination S3 Bucket**
```
1. Create S3 bucket: "lab-datasync-destination-[account-id]"
2. Enable versioning
3. Create
```

#### Part B: Configure DataSync

**Step 3: Create DataSync Agent**
```
1. DataSync → Agents → Create agent
2. Agent deployment: Amazon EC2
3. Follow wizard to create agent EC2 instance
4. Activation key: Get from agent console
5. Agent name: "lab-datasync-agent"
6. Create
```

**Step 4: Create Source Location**
```
1. DataSync → Locations → Create location
2. Location type: NFS
3. Agents: lab-datasync-agent
4. NFS Server: lab-datasync-source private IP
5. Mount path: /data
6. Create location
```

**Step 5: Create Destination Location**
```
1. Create location
2. Location type: Amazon S3
3. S3 bucket: lab-datasync-destination-[account-id]
4. S3 storage class: Standard
5. Folder: /synced-data
6. IAM role: Create new or select existing
7. Create location
```

#### Part C: Create and Run Task

**Step 6: Create DataSync Task**
```
1. DataSync → Tasks → Create task
2. Source location: lab-datasync-source (NFS)
3. Destination location: S3 bucket
4. Next
```

**Step 7: Configure Task**
```
1. Task name: "lab-sync-task"
2. Data transfer mode: Transfer all data
3. Verify data: Verify only data transferred
4. Preserve metadata: Default
5. Schedule: Run on demand
   OR
   Schedule: Hourly
6. Next
7. Create task
```

**Step 8: Run Task**
```
1. Select task → Start
2. Start with defaults
3. Monitor progress:
   - View task execution
   - Check files transferred
   - Check bytes transferred
```

**Step 9: Verify Transfer**
```bash
# Check S3 bucket
aws s3 ls s3://lab-datasync-destination-[account-id]/synced-data/ --recursive

# Count files
aws s3 ls s3://lab-datasync-destination-[account-id]/synced-data/ --recursive | wc -l
```

### Cleanup for Lab 10.2
```
1. Delete DataSync task
2. Delete locations
3. Delete agent
4. Empty and delete S3 bucket
5. Terminate EC2 instances
```

---

## Lab 10.3: AWS Transfer Family (SFTP)

### Objectives
- Create SFTP server
- Configure user access
- Transfer files securely

### Steps

#### Part A: Create Transfer Server

**Step 1: Create SFTP Server**
```
1. AWS Transfer Family → Create server
2. Protocol: SFTP
3. Identity provider: Service managed
4. Endpoint type: Public
5. Logging: CloudWatch
6. Next
7. Create server
```

**Step 2: Create S3 Bucket for SFTP**
```
1. Create bucket: "lab-sftp-bucket-[account-id]"
2. Create folders:
   - /home/user1
   - /home/user2
```

#### Part B: Configure Users

**Step 3: Create IAM Role for Users**
```json
// Trust policy
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "transfer.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}

// Permissions policy
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowS3Access",
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket",
        "s3:GetBucketLocation"
      ],
      "Resource": "arn:aws:s3:::lab-sftp-bucket-[account-id]"
    },
    {
      "Sid": "AllowS3ObjectAccess",
      "Effect": "Allow",
      "Action": [
        "s3:PutObject",
        "s3:GetObject",
        "s3:DeleteObject"
      ],
      "Resource": "arn:aws:s3:::lab-sftp-bucket-[account-id]/*"
    }
  ]
}
```

**Step 4: Generate SSH Key**
```bash
# Generate SSH key pair
ssh-keygen -t rsa -b 4096 -f lab-sftp-key -N ""

# Output:
# lab-sftp-key (private key)
# lab-sftp-key.pub (public key)

# Get public key content
cat lab-sftp-key.pub
```

**Step 5: Create SFTP User**
```
1. Transfer Family → Select server → Add user
2. Username: user1
3. Role: Created IAM role
4. Policy: None
5. Home directory:
   - Bucket: lab-sftp-bucket-[account-id]
   - Optional folder: /home/user1
6. Restricted: Yes
7. SSH public key: Paste public key content
8. Add
```

#### Part C: Test SFTP Connection

**Step 6: Connect via SFTP**
```bash
# Get server endpoint from console
# Format: server-id.server.transfer.us-east-1.amazonaws.com

# Connect
sftp -i lab-sftp-key user1@server-id.server.transfer.us-east-1.amazonaws.com

# List files
ls

# Upload file
put local-file.txt

# Download file
get remote-file.txt

# Exit
bye
```

**Step 7: Verify in S3**
```
1. Go to S3 bucket
2. Navigate to /home/user1
3. Verify uploaded file exists
```

### Cleanup for Lab 10.3
```
1. Delete SFTP users
2. Delete/stop SFTP server
3. Empty and delete S3 bucket
4. Delete IAM role
```

---

## Lab 10.4: Migration Strategies

### Objectives
- Understand 7 R's of migration
- Plan application migration
- Execute lift-and-shift

### Steps

#### Part A: Assessment

**Step 1: Inventory Current Resources**
```markdown
# Migration Assessment Template

## Application: Sample Web App

### Current Architecture
- Web server: On-premises VM (4 vCPU, 8 GB RAM)
- Database: MySQL on VM (2 vCPU, 4 GB RAM)
- Storage: NAS share (500 GB)
- Users: 100 concurrent

### Dependencies
- External API endpoints
- LDAP authentication
- File share access

### Data Volume
- Database: 50 GB
- Files: 200 GB
- Daily change rate: 2 GB
```

**Step 2: Choose Migration Strategy**
```markdown
## 7 Rs Evaluation

1. **Rehost (Lift & Shift)**
   - Move as-is to EC2
   - Fastest, minimal changes
   - Good for: Quick migration

2. **Replatform (Lift & Reshape)**
   - Move to RDS instead of EC2 MySQL
   - Some optimization, low risk
   - Good for: Database workloads

3. **Repurchase**
   - Replace with SaaS
   - Example: On-prem CRM → Salesforce
   - Good for: Commercial software

4. **Refactor (Re-architect)**
   - Redesign for cloud-native
   - Microservices, serverless
   - Good for: Strategic applications

5. **Retire**
   - Decommission application
   - Good for: Redundant systems

6. **Retain**
   - Keep on-premises
   - Good for: Complex dependencies

7. **Relocate**
   - VMware Cloud on AWS
   - Good for: Minimal disruption
```

#### Part B: Lift-and-Shift Migration

**Step 3: Create Target Environment**
```
1. Create VPC with public/private subnets
2. Set up security groups
3. Create RDS instance (replatform database)
4. Create EC2 instance (rehost web server)
```

**Step 4: Migrate Database**
```
1. Use DMS for database migration
2. Or export/import for smaller databases:

# On source:
mysqldump -u user -p sourcedb > backup.sql

# On target:
mysql -h rds-endpoint -u admin -p targetdb < backup.sql
```

**Step 5: Migrate Application**
```
1. Create AMI of on-premises server
   (using VM Import/Export)
   OR
2. Fresh install on EC2 with configuration:
   - Install runtime dependencies
   - Deploy application code
   - Update database connection strings
   - Configure logging to CloudWatch
```

**Step 6: Migrate Files**
```
1. Use DataSync for file transfer
2. Or S3 sync for smaller volumes:

aws s3 sync /nas/share s3://bucket-name/files/
```

#### Part C: Cutover Planning

**Step 7: Create Cutover Plan**
```markdown
# Cutover Checklist

## Pre-Cutover (T-1 week)
- [ ] Final data sync
- [ ] DNS TTL reduction
- [ ] Team notifications
- [ ] Rollback plan documented

## Cutover Window (T-0)
- [ ] Stop writes to source
- [ ] Final delta sync
- [ ] Verify data integrity
- [ ] Update DNS records
- [ ] Test application
- [ ] Monitor for issues

## Post-Cutover (T+1)
- [ ] Verify all functionality
- [ ] Performance monitoring
- [ ] User acceptance testing
- [ ] Decommission source (after soak period)
```

**Step 8: Execute Cutover**
```bash
# 1. Final sync
aws datasync start-task-execution --task-arn arn:aws:datasync:...

# 2. Update Route 53 (or external DNS)
aws route53 change-resource-record-sets --hosted-zone-id ZONE_ID --change-batch '{
  "Changes": [{
    "Action": "UPSERT",
    "ResourceRecordSet": {
      "Name": "app.example.com",
      "Type": "A",
      "TTL": 60,
      "ResourceRecords": [{"Value": "NEW_IP"}]
    }
  }]
}'

# 3. Monitor
aws cloudwatch get-metric-statistics ...
```

### Cleanup for Lab 10.4
```
1. Document what was created
2. Delete in reverse order:
   - Application layer
   - Database layer
   - Networking
```

---

## Lab 10.5: AWS Application Migration Service

### Objectives
- Set up Application Migration Service
- Replicate server
- Launch test instance

### Steps

#### Part A: Set Up MGN

**Step 1: Initialize MGN**
```
1. Go to Application Migration Service
2. Get started
3. Create replication template:
   - Staging area subnet: Default
   - Replication server instance type: t3.small
   - EBS encryption: Yes
4. Save template
```

**Step 2: Install MGN Agent (Simulated)**
```bash
# On source server (simulated with EC2)

# Download agent
wget -O ./aws-replication-installer-init.py \
  https://aws-application-migration-service-us-east-1.s3.amazonaws.com/latest/linux/aws-replication-installer-init.py

# Install agent
sudo python3 aws-replication-installer-init.py \
  --region us-east-1 \
  --aws-access-key-id YOUR_KEY \
  --aws-secret-access-key YOUR_SECRET
```

**Step 3: Monitor Replication**
```
1. MGN Console → Source servers
2. View server status
3. Wait for "Ready for testing"
4. Monitor data replication progress
```

#### Part B: Test and Cutover

**Step 4: Launch Test Instance**
```
1. Select source server
2. Test and cutover → Launch test instances
3. Configure launch settings:
   - Instance type: t2.micro
   - Subnet: Target subnet
   - Security groups: Select appropriate
4. Launch test
5. Wait for launch completion
```

**Step 5: Validate Test Instance**
```
1. Go to EC2
2. Find launched test instance
3. Connect and verify:
   - Application works
   - Data is present
   - Network connectivity
```

**Step 6: Mark Ready for Cutover**
```
1. If test successful
2. MGN → Select server → Mark as ready for cutover
```

**Step 7: Perform Cutover**
```
1. Test and cutover → Launch cutover instances
2. Confirm cutover
3. This creates final production instances
4. Update DNS/load balancers
5. Finalize cutover
```

### Cleanup for Lab 10.5
```
1. Terminate cutover/test instances
2. Disconnect source server
3. Delete source server from MGN
4. Clean up staging area resources
```

---

## 📝 Lab Summary & Key Takeaways

### What You Learned:
1. **DMS**: Database migration between engines
2. **DataSync**: Large-scale data transfer
3. **Transfer Family**: Managed SFTP/FTP
4. **Migration Strategies**: 7 R's evaluation
5. **Application Migration Service**: Server replication

### Exam-Relevant Points:

#### DMS
- Supports homogeneous and heterogeneous migrations
- Schema Conversion Tool (SCT) for different engines
- Change Data Capture (CDC) for ongoing replication

#### DataSync
- Up to 10x faster than open-source tools
- Automatic validation and encryption
- Bandwidth throttling available

#### Transfer Family
- Managed SFTP, FTPS, FTP
- Integrates with S3 and EFS
- Uses IAM for authorization

#### Migration Strategies
- 7 R's: Rehost, Replatform, Repurchase, Refactor, Retire, Retain, Relocate
- Choose based on business requirements and timeline

#### Snow Family
- Snowcone: 8 TB, portable
- Snowball Edge: 80 TB, compute capable
- Snowmobile: 100 PB, exabyte-scale

### Cost Optimization Tips:
- Use DMS smaller instances for dev/test
- DataSync pricing based on data transferred
- MGN: Pay for replication storage and test instances

---

## 🎓 Final Lab Completion Checklist

After completing all labs:
- [ ] Labs-00-Setup completed
- [ ] Labs-01-Compute completed
- [ ] Labs-02-Storage completed
- [ ] Labs-03-Database completed
- [ ] Labs-04-Networking completed
- [ ] Labs-05-Security completed
- [ ] Labs-06-Monitoring completed
- [ ] Labs-07-Serverless completed
- [ ] Labs-08-Containers completed
- [ ] Labs-09-HA-DR completed
- [ ] Labs-10-Migration completed

**Total Estimated Time:** 35-45 hours  
**Total Estimated Cost:** $25-50 (if cleaned up promptly)

---

**Congratulations!** You have completed all hands-on labs for AWS SAA-C03 preparation.

**Next Steps:**
1. Review exam tips in [09-Exam-Strategy-Tips.md](./09-Exam-Strategy-Tips.md)
2. Practice with sample questions
3. Take practice exams
4. Schedule your certification exam!
