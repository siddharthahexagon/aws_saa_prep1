# AWS SAA-C03: Detailed Exam Scenarios & Comprehensive Answers

## 📋 Table of Contents
- [Compute Scenarios](#compute-scenarios)
- [Storage Scenarios](#storage-scenarios)
- [Database Scenarios](#database-scenarios)
- [Networking Scenarios](#networking-scenarios)
- [Security Scenarios](#security-scenarios)
- [High Availability & Disaster Recovery](#high-availability--disaster-recovery)
- [Cost Optimization Scenarios](#cost-optimization-scenarios)
- [Integration & Messaging Scenarios](#integration--messaging-scenarios)
- [Analytics & Big Data Scenarios](#analytics--big-data-scenarios)
- [Migration Scenarios](#migration-scenarios)

---

## Compute Scenarios

### Scenario 1: Web Application with Variable Traffic
**Question:** A company has a web application that experiences unpredictable traffic patterns throughout the day. The application sometimes receives zero requests for hours, then suddenly spikes to thousands of requests. The company wants a solution that minimizes costs during low traffic and automatically scales during high traffic without server management. What should the solutions architect recommend?

**Detailed Answer:**
**Service:** AWS Lambda + Amazon API Gateway + Amazon DynamoDB

**Complete Solution:**
1. **Frontend:** Host static content (HTML, CSS, JS) on Amazon S3 + CloudFront
2. **API Layer:** Amazon API Gateway to expose RESTful APIs
3. **Backend Logic:** AWS Lambda functions (Node.js, Python, etc.)
4. **Database:** Amazon DynamoDB (serverless NoSQL)

**Why This Solution:**
- **Lambda Benefits:**
  - Pay only for execution time (millisecond billing)
  - Automatic scaling from 0 to thousands of concurrent executions
  - No server management or patching
  - Built-in high availability across multiple AZs
  
- **Cost Optimization:**
  - No charges when traffic is zero
  - Lambda Free Tier: 1M requests/month + 400,000 GB-seconds
  - DynamoDB On-Demand: Pay per request, no idle capacity charges
  
- **Scaling:**
  - API Gateway scales automatically
  - Lambda concurrent execution limit: 1000 (soft limit, can be increased)
  - DynamoDB auto-scales with On-Demand capacity mode

**Architecture:**
```
Users → CloudFront (CDN) → S3 (static content)
Users → API Gateway → Lambda → DynamoDB
                    ↓
                CloudWatch (logging/monitoring)
```

**Alternative Solutions (Why They're Not Best):**
- ❌ EC2 with Auto Scaling: Still pays for minimum instances during zero traffic
- ❌ Elastic Beanstalk: PaaS but still runs EC2 instances continuously
- ✅ Fargate: Good alternative but Lambda is more cost-effective for sporadic traffic

**Exam Keywords:** "Unpredictable traffic", "minimize costs", "zero traffic", "automatic scaling", "no server management"

---

### Scenario 2: Video Transcoding Service
**Question:** A media company needs to transcode uploaded videos into multiple formats (HD, SD, mobile). Each video takes 30-60 minutes to process. The workload is highly variable, with 100 videos one day and 5 the next. The solution must be cost-effective and handle variable workloads. What should be implemented?

**Detailed Answer:**
**Service:** Amazon S3 + AWS Batch + Spot Instances + Amazon SNS

**Complete Solution:**
1. **Upload:** Videos uploaded to S3 bucket (input bucket)
2. **Trigger:** S3 Event Notification → Lambda → Submit job to AWS Batch
3. **Processing:** AWS Batch job queue with Spot Instances
4. **Output:** Transcoded videos stored in S3 (output bucket)
5. **Notification:** SNS notification when complete

**Why This Solution:**
- **AWS Batch:**
  - Manages job queues and compute resources
  - Automatically scales compute resources based on job queue
  - Supports job dependencies and priorities
  - No time limit (unlike Lambda's 15 minutes)
  
- **Spot Instances:**
  - Up to 90% cost savings vs On-Demand
  - Suitable because transcoding can be interrupted and resumed
  - Batch automatically handles Spot interruptions and retries
  
- **S3 for Storage:**
  - Unlimited storage capacity
  - S3 Lifecycle: Transition old videos to Glacier after 30 days
  - S3 Event Notifications for automatic triggering

**Architecture:**
```
Upload → S3 (input) → Event Notification → Lambda → AWS Batch Job Queue
                                                           ↓
                                                    Spot Instances (workers)
                                                           ↓
                                                    S3 (output) → SNS notification
```

**Implementation Details:**
1. **Batch Compute Environment:**
   - Type: EC2 (with Spot instances)
   - Instance types: c5.4xlarge (compute-optimized)
   - Allocation Strategy: SPOT_CAPACITY_OPTIMIZED
   - Min vCPUs: 0, Max vCPUs: 256 (scales to zero when no jobs)

2. **Job Definition:**
   - Docker container with FFmpeg
   - vCPUs: 8, Memory: 16 GB
   - Retry strategy: 3 attempts

3. **Job Queue:**
   - Priority: 1 (higher for urgent jobs)
   - State: ENABLED

**Cost Optimization:**
- Spot instances: ~90% savings
- Scale to zero when no jobs
- S3 Intelligent-Tiering for storage
- CloudWatch Logs retention: 7 days

**Alternative Solutions:**
- ❌ Lambda: 15-minute timeout too short
- ❌ EC2 with Auto Scaling: More complex, less cost-effective
- ✅ ECS with Fargate: Good alternative but more expensive than Spot

**Exam Keywords:** "30-60 minutes", "variable workload", "cost-effective", "processing jobs", "can be interrupted"

---

### Scenario 3: Legacy Application Requiring Specific OS Configuration
**Question:** A company needs to migrate a legacy Java application to AWS. The application requires specific OS-level configurations, custom kernel modules, and runs a proprietary monitoring agent that only works on CentOS 7. The application must run 24/7 with predictable performance. What compute solution should be used?

**Detailed Answer:**
**Service:** Amazon EC2 with Reserved Instances

**Complete Solution:**
1. **Compute:** EC2 instances (t3.large or m5.large)
2. **AMI:** Custom AMI with CentOS 7 and pre-installed software
3. **Pricing:** 3-year Reserved Instances (All Upfront for maximum savings)
4. **High Availability:** Multi-AZ deployment with Auto Scaling (min=max=2)
5. **Load Balancing:** Application Load Balancer

**Why This Solution:**
- **EC2 Full Control:**
  - Install custom kernel modules
  - Specific OS version (CentOS 7)
  - Proprietary monitoring agent
  - Root access for system-level changes
  
- **Reserved Instances:**
  - 24/7 predictable workload = perfect for RI
  - 3-year All Upfront = ~60-65% savings vs On-Demand
  - Guaranteed capacity reservation
  
- **Custom AMI:**
  - Pre-bake all configurations
  - Faster launch times
  - Consistent environment
  - Easy disaster recovery

**Architecture:**
```
Route 53 → ALB (Multi-AZ)
            ↓
    [AZ-A: EC2 Instance] [AZ-B: EC2 Instance]
            ↓                     ↓
        Custom AMI           Custom AMI
     (CentOS 7 + Config)  (CentOS 7 + Config)
```

**Implementation Details:**

1. **Create Custom AMI:**
```bash
# Launch base CentOS 7 AMI
# Install required software
sudo yum install -y java-1.8.0-openjdk
# Install custom kernel modules
# Configure monitoring agent
# Create AMI from configured instance
```

2. **Auto Scaling Configuration:**
- Launch Template with custom AMI
- Min capacity: 2, Max capacity: 4, Desired: 2
- Health check: ELB health check
- Scaling policies: CPU > 70%

3. **Reserved Instance Purchase:**
- Instance type: m5.large
- Term: 3 years
- Payment: All Upfront
- Platform: Linux/UNIX
- Tenancy: Default

4. **High Availability:**
- Deploy in 2 AZs minimum
- ALB health checks every 30 seconds
- Auto Scaling replaces unhealthy instances

**Cost Breakdown (example):**
- On-Demand m5.large: ~$0.096/hr × 2 instances × 730 hrs = ~$140/month
- 3-year RI All Upfront: ~$0.038/hr equivalent = ~$55/month (~60% savings)
- Total 3-year cost: ~$1,980 vs ~$5,040 (saves ~$3,060)

**Alternative Solutions:**
- ❌ Lambda: Cannot install custom OS modules
- ❌ Elastic Beanstalk: Limited OS customization
- ❌ Fargate: Cannot install kernel modules
- ❌ Lightsail: Less control over networking and scalability

**Exam Keywords:** "Legacy application", "specific OS", "custom configuration", "kernel modules", "24/7", "predictable"

---

### Scenario 4: Microservices with Kubernetes
**Question:** A company is migrating a microservices application currently running on-premises Kubernetes to AWS. The development team wants to continue using Kubernetes with minimal changes. The solution must be highly available, scalable, and the company wants AWS to manage the Kubernetes control plane. What should be recommended?

**Detailed Answer:**
**Service:** Amazon EKS (Elastic Kubernetes Service) + Fargate

**Complete Solution:**
1. **Container Orchestration:** Amazon EKS (managed control plane)
2. **Compute Options:**
   - **Fargate:** For stateless microservices (serverless)
   - **EC2 Node Groups:** For stateful services or persistent storage needs
3. **Container Registry:** Amazon ECR (Elastic Container Registry)
4. **Load Balancing:** AWS Load Balancer Controller (ALB Ingress)
5. **Service Mesh:** AWS App Mesh (optional, for advanced routing)
6. **Observability:** CloudWatch Container Insights + X-Ray

**Why This Solution:**
- **EKS Benefits:**
  - AWS manages control plane (3 masters across 3 AZs)
  - Automatic version upgrades and patching
  - Native Kubernetes API compatibility
  - Certified Kubernetes conformant
  - No changes to existing K8s manifests
  
- **Fargate for EKS:**
  - Serverless compute for pods
  - No node management
  - Pay per pod execution time
  - Automatic scaling
  - Ideal for stateless microservices
  
- **Hybrid Approach:**
  - Fargate: Stateless microservices, APIs
  - EC2: Stateful services, databases, caching

**Architecture:**
```
Users → Route 53 → ALB
                    ↓
        EKS Cluster (Managed Control Plane)
                    ↓
    ┌───────────────┴───────────────┐
    ↓                               ↓
Fargate Pods                  EC2 Node Group
(Stateless services)          (Stateful services)
    ↓                               ↓
API, Web, Workers            Database, Cache, Message Queue
```

**Implementation Steps:**

1. **Create EKS Cluster:**
```bash
eksctl create cluster \
  --name production-cluster \
  --region us-east-1 \
  --version 1.28 \
  --fargate \
  --nodegroup-name standard-workers \
  --node-type m5.large \
  --nodes 3 \
  --nodes-min 2 \
  --nodes-max 10
```

2. **Fargate Profile:**
```yaml
apiVersion: v1
kind: FargateProfile
metadata:
  name: microservices
  namespace: default
spec:
  selectors:
  - namespace: default
    labels:
      compute-type: fargate
```

3. **Deploy Microservices:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-service
spec:
  replicas: 3
  selector:
    matchLabels:
      app: api
      compute-type: fargate
  template:
    metadata:
      labels:
        app: api
        compute-type: fargate
    spec:
      containers:
      - name: api
        image: 123456789.dkr.ecr.us-east-1.amazonaws.com/api:v1
        resources:
          requests:
            cpu: 500m
            memory: 1Gi
```

4. **ALB Ingress Controller:**
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: api-ingress
  annotations:
    kubernetes.io/ingress.class: alb
    alb.ingress.kubernetes.io/scheme: internet-facing
    alb.ingress.kubernetes.io/target-type: ip
spec:
  rules:
  - host: api.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 80
```

**High Availability Configuration:**
- EKS control plane: 3 masters across 3 AZs (automatic)
- Worker nodes: Spread across 3 AZs (node groups)
- Pod disruption budgets
- Horizontal Pod Autoscaler (HPA)
- Cluster Autoscaler for EC2 nodes

**Security Best Practices:**
- IAM Roles for Service Accounts (IRSA)
- Pod Security Standards
- Network Policies (Calico or AWS VPC CNI)
- ECR image scanning
- Secrets stored in AWS Secrets Manager

**Cost Optimization:**
- Fargate for variable workloads
- Spot Instances for EC2 node groups (non-critical pods)
- Cluster Autoscaler to scale down
- Compute Savings Plans for steady workloads

**Monitoring:**
- CloudWatch Container Insights (metrics)
- CloudWatch Logs (application logs)
- X-Ray (distributed tracing)
- AWS Distro for OpenTelemetry

**Alternative Solutions:**
- ✅ ECS: Good if no Kubernetes requirement (simpler, AWS-native)
- ❌ Self-managed K8s on EC2: High operational overhead
- ❌ EKS on Outposts: For on-premises, not cloud migration

**Exam Keywords:** "Kubernetes", "microservices", "minimal changes", "managed control plane", "highly available"

---

## Storage Scenarios

### Scenario 5: Multi-Tier Storage for Media Files
**Question:** A media company stores millions of video files in S3. Files are accessed frequently for the first 30 days (streaming), occasionally between 30-90 days (occasional views), and rarely after 90 days but must be retained for 7 years for compliance. The company wants to minimize storage costs while maintaining access when needed. What storage strategy should be implemented?

**Detailed Answer:**
**Service:** Amazon S3 with Lifecycle Policies + Intelligent-Tiering

**Complete Solution:**

**Option 1: Manual Lifecycle Policy (More Control)**
1. **Days 0-30:** S3 Standard (frequent access)
2. **Days 31-90:** S3 Standard-IA (infrequent access)
3. **Days 91-2555:** S3 Glacier Flexible Retrieval (archive)
4. **After 2555 days (7 years):** Expire/Delete

**Option 2: Automatic (Recommended)**
1. **All files:** S3 Intelligent-Tiering
   - Frequent Access tier (< 30 days)
   - Infrequent Access tier (30-90 days)
   - Archive Instant Access (90-180 days)
   - Archive Access (180-365 days)
   - Deep Archive Access (365+ days)

**Why This Solution:**

**S3 Lifecycle Policy Approach:**
- **Cost Savings:**
  - S3 Standard: $0.023/GB/month
  - S3 Standard-IA: $0.0125/GB/month (45% cheaper)
  - S3 Glacier Flexible: $0.0036/GB/month (84% cheaper)
  - Total savings: ~70-80% over 7 years

- **Access Patterns:**
  - Standard: Instant access
  - Standard-IA: Instant access, $0.01/GB retrieval
  - Glacier Flexible: 1-5 minutes retrieval, $0.02/GB retrieval

**S3 Intelligent-Tiering Approach:**
- **Benefits:**
  - No retrieval fees
  - Automatic optimization
  - No overhead of monitoring access patterns
  - Small monitoring fee: $0.0025/1000 objects

- **When to Use:**
  - Unknown or changing access patterns
  - Don't want to manage lifecycle policies
  - Want automatic optimization

**Implementation:**

**Lifecycle Policy Configuration:**
```json
{
  "Rules": [
    {
      "Id": "MediaFilesLifecycle",
      "Status": "Enabled",
      "Filter": {
        "Prefix": "videos/"
      },
      "Transitions": [
        {
          "Days": 30,
          "StorageClass": "STANDARD_IA"
        },
        {
          "Days": 90,
          "StorageClass": "GLACIER_FLEXIBLE_RETRIEVAL"
        }
      ],
      "Expiration": {
        "Days": 2555
      }
    }
  ]
}
```

**Architecture:**
```
Upload → S3 Standard (Days 0-30)
            ↓ (Lifecycle Transition)
        S3 Standard-IA (Days 31-90)
            ↓ (Lifecycle Transition)
        S3 Glacier Flexible (Days 91-2555)
            ↓ (Lifecycle Expiration)
        Deleted (After 7 years)
```

**Additional Features:**

1. **S3 Event Notifications:**
```json
{
  "LambdaFunctionConfigurations": [
    {
      "LambdaFunctionArn": "arn:aws:lambda:us-east-1:123456789:function:ProcessVideo",
      "Events": ["s3:ObjectCreated:*"],
      "Filter": {
        "Key": {
          "FilterRules": [
            {
              "Name": "prefix",
              "Value": "videos/"
            }
          ]
        }
      }
    }
  ]
}
```

2. **S3 Inventory:**
   - Daily reports on object counts, sizes, storage classes
   - Helps monitor lifecycle transitions
   - Stored in separate S3 bucket

3. **CloudWatch Metrics:**
   - Monitor GET requests by storage class
   - Track retrieval costs
   - Alert on unusual access patterns

**Cost Comparison (1 PB for 7 years):**

| Storage Class | Monthly Cost | 7-Year Cost | Retrieval Cost |
|--------------|--------------|-------------|----------------|
| S3 Standard (all) | $23,000 | $1,932,000 | $0 |
| With Lifecycle | ~$6,000 avg | ~$504,000 | ~$50,000 |
| **Total Savings** | **$17,000/mo** | **$1,428,000** | - |

**Retrieval Strategy for Glacier:**
- **Expedited:** 1-5 minutes, $0.03/GB (urgent requests)
- **Standard:** 3-5 hours, $0.01/GB (normal requests)
- **Bulk:** 5-12 hours, $0.0025/GB (large batch jobs)

**Best Practices:**
1. Enable S3 Versioning (protect against accidental deletion)
2. Use S3 Object Lock for compliance (WORM - Write Once Read Many)
3. Enable S3 Server Access Logging
4. Tag objects for cost allocation
5. Use S3 Analytics to verify access patterns before setting lifecycle

**Alternative Solutions:**
- ❌ S3 One Zone-IA: Risky for only 20% savings (single AZ)
- ❌ Manual migration: Operational overhead, prone to errors
- ✅ S3 Glacier Deep Archive: Good for > 7 year retention ($0.00099/GB/month)

**Exam Keywords:** "Compliance retention", "7 years", "access patterns change", "minimize costs", "lifecycle"

---

### Scenario 6: Shared File System for Linux Servers
**Question:** A company has 50 EC2 Linux instances running a content management system that needs to share files. All instances must read and write to the same file system simultaneously. The file system must scale automatically and be highly available across multiple Availability Zones. What storage solution should be used?

**Detailed Answer:**
**Service:** Amazon EFS (Elastic File System)

**Complete Solution:**
1. **File System:** Amazon EFS (Regional service)
2. **Performance Mode:** General Purpose (or Max I/O for > 50 instances)
3. **Throughput Mode:** Elastic (or Provisioned if predictable)
4. **Storage Classes:** EFS Standard + EFS IA (with Lifecycle Management)
5. **Availability:** Multi-AZ by default (regional)
6. **Encryption:** Encryption at rest (KMS) + in transit (TLS)

**Why This Solution:**
- **EFS Features:**
  - POSIX-compliant file system (standard Linux file operations)
  - Concurrent access from thousands of instances
  - Automatic scaling (elastic capacity, grows/shrinks automatically)
  - Multi-AZ replication (high availability and durability)
  - No capacity planning required
  
- **Use Cases:**
  - Content management systems
  - Web serving
  - Data analytics
  - Development environments
  - Home directories

**Architecture:**
```
VPC (us-east-1)
├── AZ-A
│   ├── EC2 Instance 1 ─┐
│   ├── EC2 Instance 2 ─┤
│   └── EFS Mount Target ├─→ EFS File System (Regional)
├── AZ-B                 │
│   ├── EC2 Instance 3 ─┤
│   ├── EC2 Instance 4 ─┤
│   └── EFS Mount Target ├─→ (Replicated across AZs)
└── AZ-C                 │
    ├── EC2 Instance 5 ─┤
    └── EFS Mount Target ┘
```

**Implementation Steps:**

1. **Create EFS File System:**
```bash
aws efs create-file-system \
  --performance-mode generalPurpose \
  --throughput-mode elastic \
  --encrypted \
  --kms-key-id arn:aws:kms:us-east-1:123456789:key/12345 \
  --tags Key=Name,Value=CMSFileSystem
```

2. **Create Mount Targets (one per AZ):**
```bash
# AZ-A
aws efs create-mount-target \
  --file-system-id fs-12345678 \
  --subnet-id subnet-abcd1234 \
  --security-groups sg-12345678

# AZ-B
aws efs create-mount-target \
  --file-system-id fs-12345678 \
  --subnet-id subnet-efgh5678 \
  --security-groups sg-12345678

# AZ-C
aws efs create-mount-target \
  --file-system-id fs-12345678 \
  --subnet-id subnet-ijkl9012 \
  --security-groups sg-12345678
```

3. **Mount on EC2 Instances:**
```bash
# Install EFS mount helper
sudo yum install -y amazon-efs-utils

# Create mount point
sudo mkdir /mnt/efs

# Mount using EFS helper (recommended - handles TLS)
sudo mount -t efs -o tls fs-12345678:/ /mnt/efs

# Or using NFS
sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-12345678.efs.us-east-1.amazonaws.com:/ /mnt/efs

# Add to /etc/fstab for automatic mounting
echo "fs-12345678:/ /mnt/efs efs _netdev,tls 0 0" | sudo tee -a /etc/fstab
```

4. **Security Group Configuration:**
```
Inbound Rules:
- Type: NFS
- Protocol: TCP
- Port: 2049
- Source: Security Group of EC2 instances (sg-ec2-12345)
```

**Performance Modes:**

1. **General Purpose (Recommended):**
   - Latency: Single-digit milliseconds
   - Max 7,000 file system operations/sec
   - Suitable for: Most workloads, CMS, web serving

2. **Max I/O:**
   - Latency: Slightly higher (double-digit milliseconds)
   - Max > 500,000 ops/sec
   - Suitable for: Big data, media processing, > 50 instances

**Throughput Modes:**

1. **Elastic (Recommended):**
   - Automatically scales throughput
   - Spikes up to 3 GiB/s reads, 1 GiB/s writes
   - Pay only for throughput used
   - Best for: Unpredictable workloads

2. **Bursting:**
   - Throughput scales with file system size
   - 50 MiB/s per TiB stored
   - Burst to 100 MiB/s
   - Free tier eligible

3. **Provisioned:**
   - Specify throughput independent of storage
   - Up to 1 GiB/s per TiB
   - Best for: Predictable, high-throughput needs

**Storage Classes & Lifecycle:**

1. **EFS Standard:**
   - Frequently accessed files
   - Multi-AZ redundancy
   - $0.30/GB/month

2. **EFS Infrequent Access (IA):**
   - Not accessed for 7, 14, 30, 60, or 90 days
   - Multi-AZ redundancy
   - $0.025/GB/month (92% cheaper)
   - $0.01/GB retrieval fee

**Lifecycle Configuration:**
```json
{
  "TransitionToIA": "AFTER_30_DAYS",
  "TransitionToPrimaryStorageClass": "AFTER_1_ACCESS"
}
```

**Cost Optimization:**
- Enable Lifecycle Management (automatic IA transition)
- Use Elastic throughput (pay for what you use)
- Monitor with CloudWatch (track storage used, throughput)
- Clean up unused files regularly

**Monitoring & Alerts:**
```yaml
CloudWatch Metrics:
  - TotalIOBytes: Total bytes for all I/O operations
  - ClientConnections: Number of client connections
  - DataReadIOBytes: Bytes read
  - DataWriteIOBytes: Bytes written
  - PercentIOLimit: % of I/O limit used
  
CloudWatch Alarms:
  - Alert when PercentIOLimit > 80% (consider Max I/O mode)
  - Alert when ClientConnections > 1000
```

**High Availability:**
- EFS is regional service (automatically replicated across AZs)
- Mount targets in each AZ
- If one AZ fails, instances in other AZs continue working
- No single point of failure

**Backup:**
- AWS Backup: Automated backups with retention policies
- EFS-to-EFS Backup: Use AWS DataSync
- Point-in-time recovery

**Alternative Solutions:**
- ❌ EBS: Cannot be shared across multiple instances
- ❌ FSx for Lustre: Overkill for CMS, designed for HPC
- ❌ S3: Not a file system (object storage, no POSIX)
- ✅ FSx for Windows: If Windows servers instead of Linux

**Exam Keywords:** "Shared file system", "Linux", "multiple instances", "concurrent access", "POSIX", "automatically scale"

---

### Scenario 7: High-Performance Database Storage
**Question:** A company runs a database on EC2 that requires 100,000 IOPS and 2,000 MB/s throughput with sub-millisecond latency. The database is mission-critical and requires 99.999% durability. What EBS volume type should be used?

**Detailed Answer:**
**Service:** Amazon EBS io2 Block Express volumes

**Complete Solution:**
1. **Volume Type:** io2 Block Express
2. **Size:** 2 TB (minimum for Block Express)
3. **Provisioned IOPS:** 100,000 IOPS
4. **Throughput:** 2,000 MB/s (4,000 MB/s max available)
5. **Multi-Attach:** Enabled (if clustering required)
6. **Encryption:** KMS encryption enabled
7. **Snapshots:** Automated daily snapshots

**Why This Solution:**

**io2 Block Express Specifications:**
- **IOPS:** Up to 256,000 IOPS per volume (100,000 needed ✓)
- **Throughput:** Up to 4,000 MB/s (2,000 MB/s needed ✓)
- **Durability:** 99.999% (five 9's) annual durability
- **Latency:** Sub-millisecond
- **Size:** 4 GiB to 64 TiB
- **IOPS:GiB Ratio:** 1000:1 (vs 50:1 for io2, 500:1 for io1)

**Architecture:**
```
EC2 Instance (r5.4xlarge or larger)
        ↓
EBS io2 Block Express Volume
  - 2 TB size
  - 100,000 IOPS provisioned
  - 2,000 MB/s throughput
  - Encryption: KMS
  - Multi-AZ: Snapshots to S3
        ↓
Daily Snapshots (stored in S3)
Cross-Region Snapshot Copy (DR)
```

**Implementation:**

1. **Create Volume:**
```bash
aws ec2 create-volume \
  --size 2048 \
  --volume-type io2 \
  --iops 100000 \
  --throughput 2000 \
  --availability-zone us-east-1a \
  --encrypted \
  --kms-key-id arn:aws:kms:us-east-1:123456789:key/12345 \
  --tag-specifications 'ResourceType=volume,Tags=[{Key=Name,Value=DatabaseVolume}]'
```

2. **Attach to EC2:**
```bash
aws ec2 attach-volume \
  --volume-id vol-12345678 \
  --instance-id i-abcd1234 \
  --device /dev/sdf
```

3. **Format and Mount:**
```bash
# Check the device
lsblk

# Create filesystem (XFS recommended for databases)
sudo mkfs.xfs /dev/nvme1n1

# Create mount point
sudo mkdir /data

# Mount with optimal options for databases
sudo mount -o noatime,nodiratime /dev/nvme1n1 /data

# Add to /etc/fstab
echo "/dev/nvme1n1 /data xfs noatime,nodiratime 0 2" | sudo tee -a /etc/fstab
```

4. **Configure Database:**
```bash
# For PostgreSQL
postgresql.conf:
  shared_buffers = 8GB
  effective_cache_size = 24GB
  maintenance_work_mem = 2GB
  random_page_cost = 1.1  # For SSD
  effective_io_concurrency = 200

# For MySQL
my.cnf:
  innodb_buffer_pool_size = 8G
  innodb_log_file_size = 512M
  innodb_flush_method = O_DIRECT
  innodb_io_capacity = 10000
  innodb_io_capacity_max = 20000
```

**Instance Requirements:**
- **Minimum:** r5b.4xlarge or larger (EBS-optimized with 10 Gbps)
- **Recommended:** r5b.8xlarge or larger (EBS-optimized with 20 Gbps)
- **Why r5b:** Specifically designed for EBS-optimized performance

**High Availability Setup:**

1. **Automated Snapshots:**
```bash
# Create snapshot
aws ec2 create-snapshot \
  --volume-id vol-12345678 \
  --description "Daily database backup"

# Lifecycle Manager for automated snapshots
Create DLM Policy:
  - Target: Volumes with tag Name=DatabaseVolume
  - Schedule: Daily at 02:00 UTC
  - Retention: 7 daily, 4 weekly, 12 monthly
```

2. **Cross-Region Replication:**
```bash
# Copy snapshot to DR region
aws ec2 copy-snapshot \
  --source-region us-east-1 \
  --source-snapshot-id snap-12345678 \
  --destination-region us-west-2 \
  --encrypted \
  --kms-key-id arn:aws:kms:us-west-2:123456789:key/67890
```

3. **Fast Snapshot Restore:**
```bash
# Enable FSR for instant recovery
aws ec2 enable-fast-snapshot-restores \
  --availability-zones us-east-1a \
  --source-snapshot-ids snap-12345678
```

**Monitoring:**
```yaml
CloudWatch Metrics:
  - VolumeReadOps, VolumeWriteOps: Monitor IOPS usage
  - VolumeReadBytes, VolumeWriteBytes: Monitor throughput
  - VolumeThroughputPercentage: % of provisioned throughput used
  - VolumeConsumedReadWriteOps: Actual IOPS consumed
  
CloudWatch Alarms:
  - Alert when VolumeThroughputPercentage > 90%
  - Alert when VolumeQueueLength > 5 (I/O waiting)
```

**Cost Analysis:**
```
io2 Block Express:
  - IOPS: 100,000 × $0.065/IOPS/month = $6,500/month
  - Storage: 2,048 GB × $0.125/GB/month = $256/month
  - Total: ~$6,756/month

Snapshots:
  - First snapshot: 2 TB × $0.05/GB/month = $100/month
  - Incremental: ~$20/month (only changes)
  - Fast Snapshot Restore: $0.75/snapshot/AZ/month

Total Monthly Cost: ~$6,876/month
```

**Performance Testing:**
```bash
# Install fio
sudo yum install -y fio

# Test IOPS
sudo fio --name=randread --ioengine=libaio --iodepth=128 --rw=randread --bs=4k --direct=1 --size=10G --numjobs=4 --runtime=60 --group_reporting --filename=/data/testfile

# Test Throughput
sudo fio --name=seqread --ioengine=libaio --iodepth=128 --rw=read --bs=128k --direct=1 --size=10G --numjobs=4 --runtime=60 --group_reporting --filename=/data/testfile
```

**Alternative Volume Types (Why Not):**

| Volume Type | Max IOPS | Max Throughput | Why Not Suitable |
|------------|----------|----------------|------------------|
| gp3 | 16,000 | 1,000 MB/s | ❌ Insufficient IOPS |
| io2 | 64,000 | 1,000 MB/s | ❌ Insufficient IOPS and throughput |
| io1 | 64,000 | 1,000 MB/s | ❌ Insufficient, use io2 instead |
| st1 | 500 | 500 MB/s | ❌ HDD, too slow |

**Only io2 Block Express meets requirements!**

**Disaster Recovery Strategy:**
1. **Snapshots:** Daily automated snapshots
2. **Cross-Region:** Copy snapshots to DR region
3. **Fast Restore:** Enable FSR for RTO < 1 hour
4. **Testing:** Monthly DR drills

**Best Practices:**
1. Use EBS-optimized instances (r5b family)
2. Enable encryption at rest
3. Use XFS filesystem for databases
4. Monitor CloudWatch metrics
5. Test restore procedures
6. Use Multi-AZ for database (RDS) if possible

**Exam Keywords:** "100,000 IOPS", "sub-millisecond", "mission-critical", "99.999% durability", "high throughput"

---

## Database Scenarios

### Scenario 8: E-commerce Application Database
**Question:** An e-commerce company needs a database for their application with the following requirements: handles 50,000 transactions per second, provides single-digit millisecond response times, automatically scales, requires no database administration, and is highly available across multiple regions for global customers. What database solution should be used?

**Detailed Answer:**
**Service:** Amazon DynamoDB with Global Tables

**Complete Solution:**
1. **Database:** Amazon DynamoDB (NoSQL)
2. **Capacity Mode:** On-Demand (auto-scaling for variable traffic)
3. **Global Distribution:** DynamoDB Global Tables (multi-region)
4. **Caching:** DynamoDB Accelerator (DAX) for microsecond latency
5. **Backup:** Point-in-time recovery enabled
6. **Encryption:** Encryption at rest with KMS

**Why This Solution:**

**DynamoDB Features:**
- **Performance:**
  - Single-digit millisecond latency at any scale
  - Microsecond latency with DAX
  - Supports millions of requests per second
  - No performance degradation as data grows
  
- **Scalability:**
  - Automatic scaling (with On-Demand mode)
  - No capacity planning required
  - Scales from zero to any level
  
- **High Availability:**
  - Replicates across 3 AZs automatically
  - 99.99% SLA (99.999% with Global Tables)
  - Multi-region active-active with Global Tables
  
- **Serverless:**
  - No servers to manage
  - No patching or maintenance
  - Fully managed by AWS

**Architecture:**
```
Global Setup:
┌─────────────────────┐     ┌─────────────────────┐     ┌─────────────────────┐
│  us-east-1 Region  │     │  eu-west-1 Region  │     │  ap-south-1 Region │
│                     │     │                     │     │                     │
│  ┌──────────────┐  │     │  ┌──────────────┐  │     │  ┌──────────────┐  │
│  │  DynamoDB    │◄─┼─────┼─►│  DynamoDB    │◄─┼─────┼─►│  DynamoDB    │  │
│  │  Global Table│  │     │  │  Global Table│  │     │  │  Global Table│  │
│  └──────┬───────┘  │     │  └──────┬───────┘  │     │  └──────┬───────┘  │
│         │          │     │         │          │     │         │          │
│    ┌────▼────┐     │     │    ┌────▼────┐     │     │    ┌────▼────┐     │
│    │   DAX   │     │     │    │   DAX   │     │     │    │   DAX   │     │
│    │  Cache  │     │     │    │  Cache  │     │     │    │  Cache  │     │
│    └────┬────┘     │     │    └────┬────┘     │     │    └────┬────┘     │
│         │          │     │         │          │     │         │          │
│    Application     │     │    Application     │     │    Application     │
└─────────────────────┘     └─────────────────────┘     └─────────────────────┘
    (US Customers)             (EU Customers)             (Asia Customers)
```

**Table Design:**

**1. Products Table:**
```json
{
  "TableName": "Products",
  "KeySchema": [
    {
      "AttributeName": "ProductID",
      "KeyType": "HASH"  // Partition key
    }
  ],
  "AttributeDefinitions": [
    {
      "AttributeName": "ProductID",
      "AttributeType": "S"
    },
    {
      "AttributeName": "Category",
      "AttributeType": "S"
    }
  ],
  "GlobalSecondaryIndexes": [
    {
      "IndexName": "CategoryIndex",
      "KeySchema": [
        {
          "AttributeName": "Category",
          "KeyType": "HASH"
        }
      ],
      "Projection": {
        "ProjectionType": "ALL"
      }
    }
  ],
  "BillingMode": "PAY_PER_REQUEST",
  "StreamSpecification": {
    "StreamEnabled": true,
    "StreamViewType": "NEW_AND_OLD_IMAGES"
  },
  "PointInTimeRecoverySpecification": {
    "PointInTimeRecoveryEnabled": true
  },
  "SSESpecification": {
    "Enabled": true,
    "SSEType": "KMS"
  }
}
```

**2. Orders Table:**
```json
{
  "TableName": "Orders",
  "KeySchema": [
    {
      "AttributeName": "CustomerID",
      "KeyType": "HASH"  // Partition key
    },
    {
      "AttributeName": "OrderTimestamp",
      "KeyType": "RANGE"  // Sort key
    }
  ],
  "AttributeDefinitions": [
    {
      "AttributeName": "CustomerID",
      "AttributeType": "S"
    },
    {
      "AttributeName": "OrderTimestamp",
      "AttributeType": "N"
    },
    {
      "AttributeName": "OrderStatus",
      "AttributeType": "S"
    }
  ],
  "GlobalSecondaryIndexes": [
    {
      "IndexName": "StatusIndex",
      "KeySchema": [
        {
          "AttributeName": "OrderStatus",
          "KeyType": "HASH"
        },
        {
          "AttributeName": "OrderTimestamp",
          "KeyType": "RANGE"
        }
      ],
      "Projection": {
        "ProjectionType": "ALL"
      }
    }
  ],
  "BillingMode": "PAY_PER_REQUEST"
}
```

**3. Shopping Cart Table:**
```json
{
  "TableName": "ShoppingCart",
  "KeySchema": [
    {
      "AttributeName": "SessionID",
      "KeyType": "HASH"
    }
  ],
  "TimeToLiveSpecification": {
    "Enabled": true,
    "AttributeName": "ExpirationTime"  // Auto-delete after 24 hours
  },
  "BillingMode": "PAY_PER_REQUEST"
}
```

**Implementation:**

**1. Create Global Table:**
```bash
# Create table in primary region
aws dynamodb create-table --cli-input-json file://products-table.json --region us-east-1

# Wait for table to be active
aws dynamodb wait table-exists --table-name Products --region us-east-1

# Create global table
aws dynamodb create-global-table \
  --global-table-name Products \
  --replication-group RegionName=us-east-1 RegionName=eu-west-1 RegionName=ap-south-1 \
  --region us-east-1
```

**2. Set up DAX Cluster:**
```bash
aws dax create-cluster \
  --cluster-name products-dax-cluster \
  --node-type dax.r5.large \
  --replication-factor 3 \
  --iam-role-arn arn:aws:iam::123456789:role/DAXRole \
  --subnet-group-name dax-subnet-group \
  --security-group-ids sg-12345678 \
  --region us-east-1
```

**3. Application Code (Node.js):**
```javascript
const AWS = require('aws-sdk');
const AmazonDaxClient = require('amazon-dax-client');

// DynamoDB client (direct)
const dynamodb = new AWS.DynamoDB.DocumentClient();

// DAX client (cached)
const dax = new AmazonDaxClient({
  endpoints: ['products-dax-cluster.abcd123.dax-clusters.us-east-1.amazonaws.com:8111'],
  region: 'us-east-1'
});

// Get product (with DAX caching)
async function getProduct(productId) {
  const params = {
    TableName: 'Products',
    Key: { ProductID: productId }
  };
  
  try {
    const result = await dax.get(params).promise();
    return result.Item;
  } catch (error) {
    console.error('Error:', error);
    throw error;
  }
}

// Create order (write to DynamoDB)
async function createOrder(customerId, orderData) {
  const params = {
    TableName: 'Orders',
    Item: {
      CustomerID: customerId,
      OrderTimestamp: Date.now(),
      OrderStatus: 'PENDING',
      ...orderData
    }
  };
  
  try {
    await dynamodb.put(params).promise();
    return { success: true };
  } catch (error) {
    console.error('Error:', error);
    throw error;
  }
}

// Query customer orders
async function getCustomerOrders(customerId) {
  const params = {
    TableName: 'Orders',
    KeyConditionExpression: 'CustomerID = :customerId',
    ExpressionAttributeValues: {
      ':customerId': customerId
    },
    ScanIndexForward: false,  // Descending order
    Limit: 20
  };
  
  try {
    const result = await dax.query(params).promise();
    return result.Items;
  } catch (error) {
    console.error('Error:', error);
    throw error;
  }
}

// Update order status with optimistic locking
async function updateOrderStatus(customerId, orderTimestamp, newStatus) {
  const params = {
    TableName: 'Orders',
    Key: {
      CustomerID: customerId,
      OrderTimestamp: orderTimestamp
    },
    UpdateExpression: 'SET OrderStatus = :newStatus, UpdatedAt = :updatedAt',
    ConditionExpression: 'OrderStatus = :oldStatus',  // Optimistic lock
    ExpressionAttributeValues: {
      ':newStatus': newStatus,
      ':oldStatus': 'PENDING',
      ':updatedAt': Date.now()
    },
    ReturnValues: 'ALL_NEW'
  };
  
  try {
    const result = await dynamodb.update(params).promise();
    return result.Attributes;
  } catch (error) {
    if (error.code === 'ConditionalCheckFailedException') {
      console.error('Order status already changed');
    }
    throw error;
  }
}
```

**4. DynamoDB Streams for Event Processing:**
```javascript
// Lambda function triggered by DynamoDB Streams
exports.handler = async (event) => {
  for (const record of event.Records) {
    if (record.eventName === 'INSERT') {
      const newOrder = AWS.DynamoDB.Converter.unmarshall(record.dynamodb.NewImage);
      
      // Send order confirmation email
      await sendOrderConfirmation(newOrder);
      
      // Update inventory
      await updateInventory(newOrder.Items);
      
      // Trigger fulfillment
      await triggerFulfillment(newOrder);
    }
  }
};
```

**Capacity Planning:**

**On-Demand Mode (Recommended):**
- **Reads:** $0.25 per million read request units
- **Writes:** $1.25 per million write request units
- **No planning needed:** Automatically scales
- **Best for:** Unpredictable traffic, new applications

**Example Cost (50,000 TPS):**
```
Assumptions:
- 50,000 transactions/sec
- Average 2 reads + 1 write per transaction
- Reads: 100,000/sec × 3,600 × 24 × 30 = 259 billion/month
- Writes: 50,000/sec × 3,600 × 24 × 30 = 129 billion/month

Cost:
- Reads: 259,000 million × $0.25 / 1000 = $64,750/month
- Writes: 129,000 million × $1.25 / 1000 = $161,250/month
- Storage: 1 TB × $0.25/GB = $250/month
- Global Tables: 2× replication cost
- Total: ~$450,000/month (with 3 regions)

With DAX (90% cache hit rate):
- DynamoDB reads reduced to 25.9 billion = $6,475/month
- DAX cost: 3 × dax.r5.large = $912/month
- Savings: ~$57,000/month
```

**DAX Configuration:**
```yaml
DAX Cluster:
  Node Type: dax.r5.large (13.5 GB cache)
  Nodes: 3 (primary + 2 replicas)
  TTL: 300 seconds (5 minutes)
  Item Cache: Enabled
  Query Cache: Enabled
  
Benefits:
  - Microsecond latency (vs milliseconds)
  - Reduces DynamoDB costs
  - Write-through cache
  - Transparent to application
```

**Monitoring:**
```yaml
CloudWatch Metrics:
  - UserErrors: 4xx errors
  - SystemErrors: 5xx errors
  - ConsumedReadCapacityUnits
  - ConsumedWriteCapacityUnits
  - ThrottledRequests (should be 0)
  - ReplicationLatency (for Global Tables)
  
CloudWatch Alarms:
  - Alert when UserErrors > 100/min
  - Alert when ThrottledRequests > 0
  - Alert when ReplicationLatency > 1000 ms
  
DynamoDB Contributor Insights:
  - Most accessed items
  - Most throttled keys
  - Top contributors to traffic
```

**Best Practices:**
1. **Partition Key Design:**
   - Use high-cardinality attributes (CustomerID, ProductID)
   - Avoid hot partitions
   - Use composite keys when needed

2. **Query Optimization:**
   - Use Query instead of Scan
   - Create GSIs for access patterns
   - Use projection in GSIs (only needed attributes)

3. **Caching:**
   - Use DAX for read-heavy workloads
   - Cache at application layer for less critical data

4. **Backup:**
   - Enable Point-in-Time Recovery (35 days)
   - Create on-demand backups for long-term retention

5. **Security:**
   - Use IAM roles for application access
   - Enable encryption at rest (KMS)
   - Use VPC endpoints for private access

**Alternative Solutions:**
- ❌ RDS/Aurora: Cannot handle 50,000 TPS easily, needs manual scaling
- ❌ MongoDB on EC2: Requires database administration
- ✅ Amazon Keyspaces: Alternative for Cassandra workloads

**Exam Keywords:** "50,000 TPS", "single-digit millisecond", "no administration", "automatically scales", "global", "highly available"

---

I'll continue with more detailed scenarios. Would you like me to continue with more scenarios covering all the remaining categories (Networking, Security, HA/DR, Cost Optimization, Integration, Analytics, and Migration)?
