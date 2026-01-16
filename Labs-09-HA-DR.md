# AWS SAA-C03 Hands-On Labs: High Availability & Disaster Recovery

## 📋 Overview
These labs cover multi-AZ deployments, backup strategies, and disaster recovery patterns.

**Estimated Time:** 3-4 hours total  
**Cost Estimate:** $3-6 (some HA features cost more)  
**Free Tier Eligible:** Partially

---

## Lab 9.1: Multi-AZ Architecture

### Objectives
- Deploy highly available web application
- Implement Multi-AZ database
- Configure health checks and failover

### Steps

#### Part A: Create Multi-AZ VPC

**Step 1: Create VPC with Multiple AZs**
```
1. VPC → Create VPC
2. VPC and more (use wizard)
3. Name: "lab-ha-vpc"
4. CIDR: 10.0.0.0/16
5. Number of AZs: 2
6. Number of public subnets: 2
7. Number of private subnets: 2
8. NAT gateways: 1 per AZ (for HA)
9. Create
```

#### Part B: Deploy Web Tier

**Step 2: Create Launch Template**
```
1. EC2 → Launch Templates → Create
2. Name: "lab-ha-web-template"
3. AMI: Amazon Linux 2023
4. Instance type: t2.micro
5. Security group: Create new
   - Allow HTTP (80) from ALB security group
   - Allow SSH from your IP
6. User data:
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
INSTANCE_ID=$(curl -s http://169.254.169.254/latest/meta-data/instance-id)
AZ=$(curl -s http://169.254.169.254/latest/meta-data/placement/availability-zone)
echo "<h1>Instance: $INSTANCE_ID</h1><p>AZ: $AZ</p>" > /var/www/html/index.html
```

**Step 3: Create Auto Scaling Group**
```
1. EC2 → Auto Scaling Groups → Create
2. Name: "lab-ha-asg"
3. Launch template: lab-ha-web-template
4. VPC: lab-ha-vpc
5. Subnets: Select both public subnets
6. Load balancing: Attach to new ALB
7. Health check: ELB
8. Health check grace period: 120 seconds
9. Desired: 2, Minimum: 2, Maximum: 4
10. Scaling policies: Target tracking - CPU 70%
```

**Step 4: Create Application Load Balancer**
```
1. EC2 → Load Balancers → Create ALB
2. Name: "lab-ha-alb"
3. Scheme: Internet-facing
4. Subnets: Both public subnets
5. Security group: Allow HTTP from 0.0.0.0/0
6. Target group: lab-ha-tg
   - Health check: /
   - Interval: 10 seconds
   - Healthy threshold: 2
```

**Step 5: Verify High Availability**
```bash
# Test load balancing
for i in {1..10}; do curl http://[ALB-DNS]; done
# Should see alternating instance IDs and AZs

# Terminate one instance
# ASG should launch replacement
# ALB should route only to healthy instances
```

#### Part C: Multi-AZ RDS

**Step 6: Create Multi-AZ RDS**
```
1. RDS → Create database
2. Engine: MySQL
3. Templates: Production
4. Multi-AZ: Yes
5. Instance: db.t3.micro
6. Storage: 20 GB gp2
7. VPC: lab-ha-vpc
8. Create new subnet group with private subnets
9. Public access: No
10. Create database
```

**Step 7: Test RDS Failover**
```
1. Note primary AZ
2. Actions → Reboot → Reboot with failover
3. Monitor failover process
4. Note new primary AZ
5. Connection should resume automatically
```

#### Part D: Cross-AZ Application Integration

**Step 8: Connect Web Tier to Database**
```
1. Update web servers to use RDS endpoint
2. RDS endpoint remains same after failover
3. Application automatically reconnects
```

### Cleanup for Lab 9.1
```
1. Delete Auto Scaling Group
2. Delete ALB and Target Group
3. Delete RDS instance
4. Delete Launch Template
5. Delete VPC and all components
```

---

## Lab 9.2: AWS Backup

### Objectives
- Create backup plans
- Configure backup vaults
- Perform restore operations

### Steps

#### Part A: Create Backup Vault

**Step 1: Create Vault**
```
1. AWS Backup → Backup vaults → Create
2. Name: "lab-backup-vault"
3. Encryption key: Use default
4. Create
```

#### Part B: Create Backup Plan

**Step 2: Create Backup Plan**
```
1. AWS Backup → Backup plans → Create
2. Start from template: Daily-Monthly-1yr-Retention
   Or build new plan:
3. Plan name: "lab-backup-plan"
```

**Step 3: Configure Backup Rules**
```
Rule 1 - Daily:
1. Rule name: "daily-backup"
2. Backup vault: lab-backup-vault
3. Backup frequency: Daily
4. Backup window: Default
5. Lifecycle:
   - Transition to cold storage: 30 days
   - Expire: 365 days
6. Copy to another region: us-west-2 (optional)

Rule 2 - Monthly:
1. Rule name: "monthly-backup"
2. Frequency: Monthly
3. Lifecycle: Expire after 1 year
```

**Step 4: Assign Resources**
```
1. Resource assignment name: "lab-resources"
2. IAM role: Default
3. Resource selection:
   - By tags:
     - Key: Backup
     - Value: true
   OR
   - By resource type:
     - EC2, EBS, RDS
4. Create plan
```

#### Part C: Tag Resources for Backup

**Step 5: Tag Resources**
```
# Add tag to resources:
Key: Backup
Value: true

# Apply to:
- EC2 instances
- EBS volumes
- RDS databases
```

#### Part D: On-Demand Backup

**Step 6: Create On-Demand Backup**
```
1. AWS Backup → Create on-demand backup
2. Resource type: EC2
3. Instance ID: Select instance
4. Backup vault: lab-backup-vault
5. IAM role: Default
6. Create backup
```

**Step 7: Monitor Backup Job**
```
1. Go to Jobs → Backup jobs
2. View status: Running → Completed
3. View recovery points in vault
```

#### Part E: Restore from Backup

**Step 8: Restore EC2 Instance**
```
1. Go to Backup vaults → lab-backup-vault
2. Select recovery point
3. Actions → Restore
4. Restore options:
   - Instance type: Same as original or different
   - VPC: Select VPC
   - Subnet: Select subnet
   - Security groups: Select
   - IAM role: Default
5. Restore backup
```

**Step 9: Verify Restoration**
```
1. Go to EC2 → Instances
2. Find restored instance
3. Verify data integrity
4. Compare with original
```

### Cleanup for Lab 9.2
```
1. Delete backup plan
2. Delete recovery points
3. Delete backup vault
4. Remove backup tags from resources
```

---

## Lab 9.3: Route 53 Failover Routing

### Objectives
- Configure health checks
- Implement DNS failover
- Test failover scenarios

### Steps

#### Part A: Create Health Checks

**Step 1: Create Primary Health Check**
```
1. Route 53 → Health checks → Create
2. Name: "primary-health-check"
3. Endpoint:
   - IP address or domain: Primary ALB DNS
   - Port: 80
   - Path: /health
4. Advanced:
   - Request interval: Fast (10 seconds)
   - Failure threshold: 2
5. Create
```

**Step 2: Create Secondary Health Check**
```
1. Create health check
2. Name: "secondary-health-check"
3. Endpoint: Secondary region ALB DNS
4. Same configuration as primary
```

#### Part B: Create Failover Records

**Step 3: Create Hosted Zone** (if not exists)
```
1. Route 53 → Hosted zones → Create
2. Domain name: Your domain or test domain
3. Type: Public hosted zone
4. Create
```

**Step 4: Create Failover Records**
```
Primary Record:
1. Create record
2. Record name: app
3. Record type: A
4. Routing policy: Failover
5. Failover record type: Primary
6. Value: Primary ALB IP or Alias to ALB
7. Health check: primary-health-check
8. Record ID: primary
9. Create

Secondary Record:
1. Create record
2. Record name: app (same as primary)
3. Failover record type: Secondary
4. Value: Secondary ALB IP or Alias
5. Health check: secondary-health-check
6. Record ID: secondary
7. Create
```

#### Part C: Test Failover

**Step 5: Verify Normal Operation**
```bash
# Query DNS
nslookup app.yourdomain.com
# Should resolve to primary

# Access application
curl app.yourdomain.com
# Should hit primary region
```

**Step 6: Simulate Failure**
```
1. Stop all instances behind primary ALB
   OR
2. Modify security group to block health check
3. Wait for health check to fail (2-3 minutes)
```

**Step 7: Verify Failover**
```bash
# Query DNS again
nslookup app.yourdomain.com
# Should now resolve to secondary

# Access application
curl app.yourdomain.com
# Should hit secondary region
```

**Step 8: Restore Primary**
```
1. Start instances or restore security group
2. Wait for health check to pass
3. DNS will automatically return to primary
```

### Cleanup for Lab 9.3
```
1. Delete DNS records
2. Delete health checks
3. Delete hosted zone (if created for lab)
```

---

## Lab 9.4: S3 Cross-Region Replication

### Objectives
- Configure cross-region replication
- Understand replication rules
- Test replication functionality

### Steps

#### Part A: Create Source and Destination Buckets

**Step 1: Create Source Bucket**
```
1. S3 → Create bucket
2. Name: "lab-crr-source-[account-id]"
3. Region: us-east-1
4. Enable versioning (required for CRR)
5. Create
```

**Step 2: Create Destination Bucket**
```
1. Create bucket
2. Name: "lab-crr-destination-[account-id]"
3. Region: us-west-2 (different region)
4. Enable versioning
5. Create
```

#### Part B: Configure Replication

**Step 3: Create Replication Rule**
```
1. Source bucket → Management → Replication rules
2. Create replication rule
3. Rule name: "lab-crr-rule"
4. Status: Enabled
5. Source bucket:
   - Apply to all objects
   OR
   - Filter by prefix or tags
6. Destination:
   - Choose bucket in this account
   - Bucket: lab-crr-destination-[account-id]
   - Change storage class: Optional (Glacier for cost savings)
7. IAM role: Create new role
8. Additional options:
   - Replication Time Control (RTC): Optional (guarantees 15-minute SLA)
   - Replica modification sync: Optional
   - Delete marker replication: Optional
9. Save
```

**Step 4: Choose Existing Objects**
```
When prompted:
- No (replicate new objects only)
- Yes (creates batch replication job for existing)
```

#### Part C: Test Replication

**Step 5: Upload Objects**
```bash
# Upload to source bucket
aws s3 cp test-file.txt s3://lab-crr-source-[account-id]/

# Wait 1-2 minutes

# Check destination bucket
aws s3 ls s3://lab-crr-destination-[account-id]/
# File should appear
```

**Step 6: Verify Replication Status**
```
1. Source bucket → Objects
2. Select object
3. Properties → Replication status
4. Should show: COMPLETED
```

**Step 7: Test Version Replication**
```bash
# Upload new version
echo "Version 2" > test-file.txt
aws s3 cp test-file.txt s3://lab-crr-source-[account-id]/

# Both versions should replicate
aws s3api list-object-versions --bucket lab-crr-destination-[account-id]
```

### Cleanup for Lab 9.4
```
1. Delete replication rule
2. Delete all versions in both buckets
3. Delete both buckets
4. Delete IAM replication role
```

---

## Lab 9.5: Disaster Recovery Strategies

### Objectives
- Understand DR strategies
- Implement pilot light
- Test DR procedures

### Steps

#### Part A: Document Current Architecture

**Step 1: Create DR Documentation**
```markdown
# DR Plan Template

## Primary Site (us-east-1)
- VPC: lab-ha-vpc
- Web tier: Auto Scaling Group
- Database: RDS Multi-AZ
- Storage: S3 with versioning

## Recovery Site (us-west-2)
- VPC: lab-dr-vpc (to be created)
- Web tier: AMI ready, minimal capacity
- Database: RDS Read Replica
- Storage: S3 CRR destination

## RTO: 1 hour
## RPO: 15 minutes
```

#### Part B: Implement Pilot Light

**Step 2: Create DR VPC**
```
1. Switch to DR region (us-west-2)
2. Create VPC: lab-dr-vpc
3. Create subnets (public and private)
4. Create Internet Gateway
5. Configure route tables
```

**Step 3: Create RDS Read Replica**
```
1. Go to RDS in primary region
2. Select database
3. Actions → Create read replica
4. Destination region: us-west-2
5. Instance class: db.t3.micro (smaller for cost)
6. Create
```

**Step 4: Prepare AMI in DR Region**
```
1. Create AMI from primary web server
2. Copy AMI to DR region:
   - EC2 → AMIs → Select → Actions → Copy AMI
   - Destination region: us-west-2
3. Create Launch Template in DR region using copied AMI
```

**Step 5: Create Minimal ASG in DR**
```
1. In DR region, create ASG
2. Desired: 0, Minimum: 0, Maximum: 4
3. This is "pilot light" - ready but not running
```

#### Part C: DR Failover Procedure

**Step 6: Document Failover Steps**
```markdown
# Failover Procedure

1. Detect failure (CloudWatch alarm or manual)
2. Promote RDS read replica to standalone
3. Update ASG desired capacity to 2
4. Verify instances healthy
5. Update Route 53 to point to DR ALB
6. Verify application functionality
7. Communicate to stakeholders
```

**Step 7: Test Failover (Non-Production)**
```bash
# Step 1: Promote read replica
aws rds promote-read-replica --db-instance-identifier lab-db-replica

# Step 2: Scale up DR ASG
aws autoscaling set-desired-capacity \
  --auto-scaling-group-name lab-dr-asg \
  --desired-capacity 2

# Step 3: Update Route 53 (manual or script)
# Change app.domain.com to point to DR ALB

# Step 4: Verify
curl app.domain.com
```

#### Part D: DR Testing Schedule

**Step 8: Create DR Test Plan**
```markdown
# DR Test Schedule

## Quarterly Tests
- Full failover to DR site
- Application functionality verification
- Performance testing
- Failback procedure

## Monthly Tests
- RDS replica promotion test
- AMI validation
- Network connectivity

## Weekly Tests
- Health check verification
- Backup restoration
- Documentation review
```

### Cleanup for Lab 9.5
```
1. Delete DR ASG
2. Delete RDS replica (or promoted instance)
3. Delete Launch Template
4. Delete copied AMI
5. Delete DR VPC
```

---

## Lab 9.6: Global Accelerator

### Objectives
- Create Global Accelerator
- Configure endpoints
- Test global routing

### Steps

#### Part A: Create Global Accelerator

**Step 1: Create Accelerator**
```
1. Go to Global Accelerator
2. Create accelerator
3. Name: "lab-global-accelerator"
4. Type: Standard
5. Create
```

**Step 2: Add Listeners**
```
1. Add listener
2. Ports: 80
3. Protocol: TCP
4. Client affinity: None
5. Create listener
```

**Step 3: Add Endpoint Groups**
```
Region 1 (us-east-1):
1. Add endpoint group
2. Region: us-east-1
3. Traffic dial: 100%
4. Health check: HTTP, port 80, path /
5. Add endpoint:
   - Type: ALB
   - Endpoint: lab-ha-alb
   - Weight: 100

Region 2 (us-west-2):
1. Add endpoint group
2. Region: us-west-2
3. Traffic dial: 100%
4. Add endpoint (if ALB exists in DR)
```

#### Part B: Test Global Accelerator

**Step 4: Get Static IPs**
```
1. View accelerator details
2. Note the two static IP addresses
3. Note the DNS name (xxxx.awsglobalaccelerator.com)
```

**Step 5: Test Routing**
```bash
# Access via Global Accelerator DNS
curl xxxx.awsglobalaccelerator.com

# Access via static IP
curl http://[static-ip-1]
curl http://[static-ip-2]
```

**Step 6: Test Failover**
```
1. Disable endpoint group in us-east-1
2. Traffic should route to us-west-2
3. Re-enable us-east-1
4. Traffic should return
```

### Cleanup for Lab 9.6
```
1. Delete endpoint groups
2. Delete listener
3. Delete accelerator
```

---

## 📝 Lab Summary & Key Takeaways

### What You Learned:
1. **Multi-AZ**: High availability within a region
2. **AWS Backup**: Centralized backup management
3. **Route 53 Failover**: DNS-based failover
4. **S3 CRR**: Cross-region data replication
5. **DR Strategies**: Pilot light, warm standby
6. **Global Accelerator**: Global traffic management

### Exam-Relevant Points:
- Multi-AZ: Automatic failover, synchronous replication
- AWS Backup: Cross-region, cross-account support
- Route 53: Active-passive failover with health checks
- S3 CRR: Requires versioning, asynchronous replication
- RTO vs RPO:
  - RTO: Recovery Time Objective (how long to recover)
  - RPO: Recovery Point Objective (data loss tolerance)
- DR Strategies (cost/complexity order):
  1. Backup & Restore: Lowest cost, highest RTO
  2. Pilot Light: Core components ready
  3. Warm Standby: Scaled-down replica running
  4. Multi-site: Full active-active

---

**Next:** [Labs-10-Migration](./Labs-10-Migration.md)
