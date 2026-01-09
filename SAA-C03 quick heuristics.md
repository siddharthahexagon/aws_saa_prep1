# AWS Certified Solutions Architect – Associate (SAA-C03)
## Complete exam-style decision guidance (quick heuristics)

---

## 1. Compute Services

### EC2 (Elastic Compute Cloud)
**Definition**: Virtual servers in the cloud for running applications

**Key Features**:
- **Instance Types**: 
  - General Purpose (T3, T4g, M5, M6i, M6g) - Balanced compute, memory, networking
  - Compute Optimized (C5, C6i, C6g) - High-performance processors
  - Memory Optimized (R5, R6i, X2, High Memory) - Large in-memory databases
  - Storage Optimized (I3, I4i, D2, H1) - High sequential read/write to local storage
  - Accelerated Computing (P4, P3, G5, Inf1, VT1) - GPU, FPGA, video transcoding
- **Pricing Models**:
  - On-Demand: Pay by second, no commitment
  - Reserved (1-3 years): Up to 72% discount, Standard/Convertible
  - Spot: Up to 90% discount, can be interrupted with 2-min notice
  - Dedicated Hosts: Physical server dedicated to your use
  - Savings Plans: Flexible pricing, commitment to consistent usage
- **Placement Groups**:
  - Cluster: Low latency, high throughput within single AZ
  - Partition: Spread across logical partitions (Hadoop, Cassandra)
  - Spread: Each instance on separate hardware (max 7 per AZ)
- **Elastic IP**: Static IPv4 address that can be remapped
- **ENI (Elastic Network Interface)**: Virtual network card
- **Instance Metadata**: Access instance info at http://169.254.169.254/latest/meta-data/
- **User Data**: Bootstrap scripts run at launch
- **AMI (Amazon Machine Image)**: Template for EC2 instances
- **EBS-optimized**: Dedicated bandwidth for EBS
- **Instance Store**: Ephemeral storage physically attached to host

**Supported Integrations**:
- Auto Scaling, ELB, CloudWatch, Systems Manager, IAM Roles
- VPC, Security Groups, EBS, EFS, S3
- CloudFormation, Elastic Beanstalk, ECS

**Exam Tips**
- **Pick when**: Full OS control, custom software, persistent workloads, lift-and-shift, GPU/specialized hardware
- **Instance types**: T (burstable), M (balanced), C (compute), R (memory), P/G (GPU), I (storage)
- **Pricing**: On-Demand (flexible), Reserved (1-3yr predictable), Spot (90% off, interruptible), Savings Plans
- **HA pattern**: Auto Scaling Group + ALB + Multi-AZ deployment
- **Eliminate**: "No servers to manage", "event-driven", "minimal ops"

### Lambda
**Definition**: Serverless compute service - run code without managing servers

**Key Features**:
- **Execution Limits**:
  - Memory: 128MB to 10GB (64MB increments)
  - Timeout: 1 second to 15 minutes
  - Deployment package: 50MB zipped, 250MB unzipped
  - /tmp storage: 512MB to 10GB
  - Concurrent executions: 1000 (soft limit, can be increased)
  - Payload: 6MB synchronous, 256KB asynchronous
- **Supported Runtimes**: Python, Node.js, Java, Go, .NET (C#/PowerShell), Ruby, Custom Runtime (Rust, C++)
- **Invocation Types**:
  - Synchronous: Wait for response (API Gateway, ALB, SDK)
  - Asynchronous: Return immediately (S3, SNS, EventBridge)
  - Event Source Mapping: Poll-based (SQS, Kinesis, DynamoDB Streams)
- **Environment Variables**: Store configuration, KMS encryption
- **Layers**: Share code, libraries across functions (max 5 layers)
- **Versions & Aliases**: Version control, routing (weighted, canary)
- **VPC Access**: Access VPC resources (RDS, ElastiCache, internal APIs)
- **Reserved Concurrency**: Guarantee capacity, throttle functions
- **Provisioned Concurrency**: Pre-warmed instances for low latency
- **Destinations**: Route success/failure to SNS, SQS, Lambda, EventBridge

**Event Sources**:
- API Gateway, ALB, S3, DynamoDB Streams, Kinesis Data Streams
- SNS, SQS, EventBridge, CloudWatch Events/Logs, IoT, Cognito
- Alexa, Lex, CloudFormation, CodeCommit, Step Functions

**Supported Integrations**:
- All AWS services via SDK, VPC resources, Internet via NAT Gateway
- X-Ray for tracing, CloudWatch for logs/metrics
- Secrets Manager, Parameter Store, KMS

**Exam Tips**
- **Pick when**: Event-driven, <15min execution, pay-per-invoke, serverless, microservices
- **Limits**: 15min max, 10GB memory, 512MB /tmp, 6MB sync payload
- **Triggers**: API Gateway, S3, DynamoDB Streams, SNS, SQS, EventBridge, CloudWatch Events
- **Eliminate**: Long-running processes, need persistent state, heavy compute/GPU

### ECS (Elastic Container Service)
**Definition**: Container orchestration service for Docker containers

**Key Features**:
- **Launch Types**:
  - **Fargate**: Serverless, AWS manages infrastructure, pay per vCPU/memory
  - **EC2**: You manage EC2 instances, more control, GPU support, lower cost at scale
  - **External**: Run on-premises or other clouds
- **Task Definitions**: JSON template defining containers, CPU, memory, networking, IAM roles
- **Services**: Maintain desired count of tasks, integrate with ELB, auto-scaling
- **Clusters**: Logical grouping of tasks or services
- **Task Placement**: Strategies (binpack, random, spread) and constraints
- **Service Auto Scaling**: Target tracking, step scaling, scheduled scaling
- **Service Discovery**: AWS Cloud Map integration for DNS-based discovery
- **Secrets**: Integration with Secrets Manager and SSM Parameter Store
- **Networking Modes**:
  - awsvpc: Each task gets ENI and private IP (Fargate only mode)
  - bridge: Docker's virtual network
  - host: Use host's network
  - none: No external connectivity
- **Load Balancing**: ALB, NLB integration with dynamic port mapping
- **Logging**: CloudWatch Logs, Fluentd, Splunk, FireLens for log routing
- **Monitoring**: CloudWatch Container Insights, AWS X-Ray

**Supported Integrations**:
- ECR (Elastic Container Registry), Docker Hub, private registries
- ALB, NLB, EFS, FSx, Secrets Manager, Parameter Store
- CloudWatch, X-Ray, EventBridge, Step Functions, CodePipeline
- IAM for task roles, Security Groups, VPC

**Exam Tips**
- **Fargate**: Serverless containers, no cluster management, pay per task
- **EC2 launch type**: More control, custom AMIs, GPU, lower cost at scale
- **Pick when**: Docker containers, microservices, want simpler than Kubernetes
- **Eliminate**: Need Kubernetes features, prefer serverless functions

### EKS (Elastic Kubernetes Service)
**Definition**: Managed Kubernetes service for running containerized applications

**Key Features**:
- Fully managed Kubernetes control plane
- Multi-AZ control plane for HA
- Integration with AWS services (IAM, VPC, ELB)
- EKS on Fargate for serverless pods
- EKS Anywhere for on-premises

**Exam Tips**:
- **Pick when**: Kubernetes required, multi-cloud portability, existing K8s investment, need K8s ecosystem tools
- **Fargate for EKS**: Serverless K8s pods, no node management
- **vs ECS**: EKS when K8s is mandatory, ECS for simpler AWS-native containerization
- **Eliminate**: Simple containerization needs (use ECS/Fargate instead), no K8s expertise

### Elastic Beanstalk
**Definition**: Platform-as-a-Service (PaaS) for deploying and scaling web applications

**Key Features**:
- Automatic infrastructure provisioning (EC2, ALB, Auto Scaling, RDS)
- Supports multiple platforms (.NET, Java, PHP, Node.js, Python, Ruby, Go, Docker)
- Blue/green deployments
- Integrated monitoring and health dashboards
- Retains full control of underlying resources

**Exam Tips**:
- **Pick when**: Quick PaaS deployment, standard web apps, developer-focused, "deploy code quickly"
- **Handles**: Provisioning, load balancing, auto-scaling, health monitoring, patching
- **Use cases**: Simple web apps, APIs, time-to-market priority
- **Eliminate**: Need fine-grained infrastructure control, complex microservices orchestration, container-native workflows

### AWS Batch
**Definition**: Fully managed batch processing service for running jobs at any scale

**Key Features**:
- Dynamic provisioning of compute resources
- Job queues and scheduling
- Supports EC2, Spot, and Fargate compute environments
- Job dependencies and priorities
- Integration with CloudWatch for monitoring

**Exam Tips**:
- **Pick when**: Batch jobs, HPC workloads, dependency management, cost optimization with Spot, large-scale data processing
- **Use cases**: Image/video processing, genomics, financial modeling, ETL jobs
- **Integrates**: EC2, Fargate, Spot instances, S3, DynamoDB
- **Eliminate**: Real-time processing, interactive workloads, sub-second response needs

### Lightsail
**Definition**: Simplified cloud platform for simple web applications and websites

**Key Features**:
- Predictable monthly pricing
- Pre-configured application stacks (WordPress, LAMP, Node.js)
- Virtual private servers, managed databases, load balancers
- Automatic backups and snapshots
- Easy integration with AWS services

**Exam Tips**:
- **Pick when**: Simple VPS, predictable pricing, small websites/blogs, startups, dev/test environments
- **Use cases**: Personal websites, small business apps, simple web apps
- **Eliminate**: Enterprise scale, complex networking, multi-region architectures, advanced AWS integrations, need for specific AWS services

---

## 2. Storage Services

### S3 (Simple Storage Service)
**Definition**: Scalable object storage for any type of data

**Key Features**:
- **Durability & Availability**: 99.999999999% (11 9's) durability across multiple AZs
- **Storage Capacity**: Unlimited total storage, object size 0 bytes to 5TB
- **Bucket Features**:
  - Bucket policies (resource-based permissions)
  - Access Control Lists (ACLs) - legacy, use policies instead
  - Block Public Access settings (account and bucket level)
  - Bucket versioning (keep multiple versions of objects)
  - MFA Delete (require MFA to delete versions)
  - Object Lock (WORM - write once read many, Compliance/Governance modes)
  - Default encryption (SSE-S3, SSE-KMS, SSE-C)
  - Requester Pays (requester pays for data transfer)
- **Lifecycle Policies**: Automate transitions between storage classes and expiration
- **Replication**:
  - Cross-Region Replication (CRR): Compliance, lower latency, cross-account
  - Same-Region Replication (SRR): Log aggregation, prod/dev replication
  - Replication Time Control (RTC): 99.99% replicated in 15 minutes
- **Event Notifications**: Trigger Lambda, SNS, SQS on object events
- **S3 Select**: Query object content using SQL without retrieving full object
- **S3 Batch Operations**: Perform operations on billions of objects
- **Transfer Acceleration**: Use CloudFront edge locations for faster uploads (50-500% faster)
- **Multi-part Upload**: Upload large objects in parts (required for >5GB, recommended for >100MB)
- **Byte-range Fetches**: Download specific byte ranges (parallelize, resume downloads)
- **Presigned URLs**: Temporary access to private objects (expiration time)
- **Access Points**: Simplify access management for shared datasets
- **Object Lambda**: Transform objects on retrieval using Lambda
- **Inventory**: Generate reports of objects and metadata
- **Analytics**: Storage class analysis for lifecycle recommendations
- **Intelligent-Tiering**: Automatic cost optimization (frequent/infrequent/archive tiers)

**Storage Classes**:
- **S3 Standard**: 3+ AZ, frequent access, millisecond latency, 99.99% availability
- **S3 Standard-IA**: 3+ AZ, infrequent access, retrieval fee, 99.9% availability, 30-day minimum
- **S3 One Zone-IA**: Single AZ, 20% cheaper than Standard-IA, 99.5% availability
- **S3 Intelligent-Tiering**: 3+ AZ, auto-moves between frequent/infrequent/archive tiers, monitoring fee
- **S3 Glacier Instant Retrieval**: 3+ AZ, archive, millisecond retrieval, 90-day minimum, 99.9% availability
- **S3 Glacier Flexible Retrieval**: 3+ AZ, archive, minutes-hours retrieval (Expedited 1-5min, Standard 3-5hr, Bulk 5-12hr), 90-day minimum
- **S3 Glacier Deep Archive**: 3+ AZ, lowest cost, 12-48hr retrieval (Standard 12hr, Bulk 48hr), 180-day minimum, 99.99% durability

**Security**:
- Encryption at rest (SSE-S3, SSE-KMS, SSE-C, client-side)
- Encryption in transit (HTTPS/TLS)
- VPC Endpoints (Gateway Endpoint for private access)
- Access Analyzer for S3 (detect public/shared buckets)
- CloudTrail for API logging, CloudWatch for metrics
- IAM policies, bucket policies, ACLs, Access Points

**Supported Integrations**:
- CloudFront, Route 53, Lambda, EventBridge, SNS, SQS
- Athena, Redshift Spectrum, EMR, Glue, QuickSight
- CloudFormation, CloudTrail, Config, Macie
- DataSync, Transfer Family, Snow Family, Storage Gateway
- EC2, ECS, EKS, Lambda for compute access

**Exam Tips**:
- **Pick when**: Object storage, static websites, data lakes, backups, logs, archives, media files, big data analytics
- **Storage classes**:
  - **S3 Standard**: Frequent access, millisecond latency, 3+ AZ
  - **S3 IA (Infrequent Access)**: Monthly access, lower cost, retrieval fee
  - **S3 One Zone-IA**: Non-critical data, single AZ, 20% cheaper than S3 IA
  - **S3 Intelligent-Tiering**: Unknown/changing patterns, auto-moves objects between tiers
  - **S3 Glacier Instant**: Archive with instant (ms) retrieval
  - **S3 Glacier Flexible**: Minutes-hours retrieval, long-term archive
  - **S3 Glacier Deep Archive**: 12hr retrieval, lowest cost, 7-10 year retention
- **Features**: Versioning, MFA delete, lifecycle policies, replication (CRR/SRR), encryption (SSE-S3, SSE-KMS, SSE-C), presigned URLs, S3 Select
- **Security**: Bucket policies, ACLs, Block Public Access, VPC Endpoints
- **Performance**: Multi-part upload for >100MB, Transfer Acceleration, byte-range fetches
- **Eliminate**: Block storage needs, POSIX file system, frequent small updates, sub-10ms latency for small objects

### EBS (Elastic Block Store)
**Definition**: Block-level storage volumes for EC2 instances

**Key Features**:
- Persistent block storage
- Automatically replicated within AZ
- Point-in-time snapshots to S3
- Encryption at rest and in transit
- Can be detached and reattached to instances
- Multi-Attach for io2 volumes (up to 16 instances)

**Exam Tips**:
- **Pick when**: EC2 block storage, databases, boot volumes, persistent storage, low-latency access
- **Types**:
  - **gp3/gp2**: General purpose SSD, balanced price/performance, boot volumes, 3000-16000 IOPS
  - **io2/io1**: Provisioned IOPS SSD, critical DBs, >16000 IOPS, 99.9% durability
  - **st1**: Throughput-optimized HDD for big data, data warehouses, log processing
  - **sc1**: Cold HDD for infrequent access, lowest cost
- **Features**: Snapshots to S3, encryption (KMS), Multi-Attach (io2 only), volume resizing, volume types can be changed
- **Performance**: IOPS and throughput scale with volume size
- **Eliminate**: Shared file system across instances (use EFS), object storage (use S3), cross-AZ redundancy (single AZ only)

### EFS (Elastic File System)
**Definition**: Fully managed, scalable NFS file system for Linux workloads

**Key Features**:
- Shared file system across multiple EC2 instances
- Automatically scales to petabytes
- Multi-AZ redundancy within region
- POSIX-compliant
- Lifecycle management to move files to IA storage class
- Supports NFSv4 protocol

**Exam Tips**:
- **Pick when**: Shared POSIX file system, Linux, NFS, concurrent access across multiple EC2s, content management, web serving, data sharing
- **Performance modes**: 
  - **General Purpose**: Latency-sensitive, web serving, CMS
  - **Max I/O**: Higher latency, higher throughput, big data, media processing
- **Throughput modes**: 
  - **Bursting**: Throughput scales with file system size
  - **Provisioned**: Fixed throughput regardless of size
  - **Elastic**: Automatically scales throughput up/down
- **Storage classes**: Standard, Infrequent Access (EFS IA)
- **Use cases**: Home directories, content management, shared workflows, development environments
- **Eliminate**: Windows file shares (use FSx for Windows), single instance access (use EBS), object storage (use S3), ultra-low latency

### FSx
**Definition**: Family of fully managed third-party file systems

**Key Features**:
- Multiple file system types for different use cases
- Fully managed with automatic backups
- High performance and scalability
- Integration with on-premises and cloud workloads

**Exam Tips**:
- **FSx for Windows File Server**: 
  - Windows file shares, SMB protocol, Active Directory integration
  - **Pick when**: Windows apps, Active Directory, NTFS, DFS, user quotas
  - **Use cases**: Home directories, SharePoint, SQL Server, IIS web servers
- **FSx for Lustre**: 
  - HPC, ML training, high-performance parallel file system, S3 integration
  - **Pick when**: Sub-millisecond latency, 100s GB/s throughput, HPC, video processing, financial modeling
  - **Use cases**: Machine learning, genomics research, video rendering
- **FSx for NetApp ONTAP**: 
  - Multi-protocol (NFS, SMB, iSCSI), advanced data management
  - **Pick when**: Migrate NetApp workloads, need snapshots, clones, replication
- **FSx for OpenZFS**: 
  - High-performance, snapshots, compression, low-latency
  - **Pick when**: Migrate ZFS workloads, need point-in-time snapshots
- **Eliminate**: Object storage (use S3), simple Linux NFS (use EFS), block storage (use EBS)

### Storage Gateway
**Definition**: Hybrid cloud storage service connecting on-premises to AWS storage

**Key Features**:
- On-premises cache with cloud-backed storage
- Multiple gateway types for different protocols
- Local caching for low-latency access
- Integration with S3, EBS, Glacier

**Exam Tips**:
- **File Gateway**: 
  - NFS/SMB to S3, local cache
  - **Pick when**: File shares backed by S3, migrate file data to cloud
- **Volume Gateway**: 
  - iSCSI block volumes (Cached/Stored modes)
  - **Cached**: Frequently accessed data on-premises, full dataset in S3
  - **Stored**: Full dataset on-premises, async backup to S3
  - **Pick when**: Block storage, backup volumes to AWS, disaster recovery
- **Tape Gateway**: 
  - Virtual tape library (VTL) for backup applications
  - **Pick when**: Replace physical tapes, integrate with existing backup software (Veeam, NetBackup)
- **Pick when**: Hybrid cloud, on-prem to AWS storage integration, gradual cloud migration, DR scenarios
- **Eliminate**: Pure cloud workloads (use S3/EBS/EFS directly), real-time sync needs (use DataSync)

---

## 3. Database Services

### RDS (Relational Database Service)
**Definition**: Managed relational database service supporting multiple database engines

**Key Features**:
- **Supported Engines**:
  - MySQL (5.7, 8.0) - Open-source
  - PostgreSQL (10-15) - Open-source with advanced features
  - MariaDB (10.3-10.6) - MySQL fork
  - Oracle (12c, 19c, 21c) - Enterprise, BYOL or License Included
  - SQL Server (2016-2019) - Web, Express, Standard, Enterprise
  - Amazon Aurora (MySQL/PostgreSQL compatible)
- **Deployment Options**:
  - **Single-AZ**: One DB instance in one AZ
  - **Multi-AZ**: Synchronous standby in different AZ, automatic failover 60-120s
  - **Multi-AZ Cluster**: 2 readable standbys, MySQL/PostgreSQL only, <1s failover
- **Read Replicas**:
  - Async replication, eventual consistency
  - Up to 5 per source (15 for Aurora)
  - Same region or cross-region
  - Can be promoted to standalone DB
  - Use for read scaling, reporting, analytics
- **Storage**:
  - General Purpose SSD (gp2/gp3): 20GB to 64TB, burst IOPS
  - Provisioned IOPS SSD (io1): 100GB to 64TB, up to 80,000 IOPS
  - Magnetic (deprecated): 20GB to 3TB, backward compatibility
  - Auto-scaling storage (automatically increase when low)
- **Backup & Recovery**:
  - Automated backups: Daily snapshots, transaction logs every 5 min, retention 0-35 days
  - Manual snapshots: User-initiated, retained until deleted
  - Point-in-Time Recovery: Restore to any second within retention period
  - Backup window: Specify maintenance window
  - Copy snapshots across regions
- **Security**:
  - Network isolation in VPC
  - Security Groups for firewall rules
  - IAM authentication for MySQL/PostgreSQL
  - Encryption at rest (KMS), in transit (SSL/TLS)
  - Database authentication (password, IAM, Kerberos for Oracle/SQL Server)
  - Secrets Manager integration for password rotation
- **Monitoring & Maintenance**:
  - CloudWatch metrics (CPU, connections, IOPS, storage)
  - Enhanced Monitoring (OS-level metrics)
  - Performance Insights (database performance analysis)
  - Event notifications (SNS)
  - Automatic minor version patching
  - Maintenance window for patches
- **High Availability Features**:
  - Multi-AZ with automatic failover
  - DNS name stays same after failover
  - Read replicas for read availability
  - Automatic backup to S3 across regions
- **Performance Features**:
  - Read replicas for read scaling
  - Provisioned IOPS for predictable performance
  - Enhanced networking (SR-IOV)
  - Storage auto-scaling
- **Parameter Groups**: Configure database settings
- **Option Groups**: Enable additional features (Oracle, SQL Server)
- **Subnet Groups**: Define which subnets RDS can use

**Supported Integrations**:
- VPC, Security Groups, KMS, IAM, Secrets Manager
- CloudWatch, CloudTrail, SNS, EventBridge
- Lambda (via triggers in Aurora), S3 (import/export)
- DMS, Database Migration Service, Schema Conversion Tool
- Systems Manager Parameter Store

**Exam Tips**:
- **Engines**: MySQL, PostgreSQL, MariaDB, Oracle, SQL Server, Amazon Aurora
- **Pick when**: Managed relational DB, SQL, ACID transactions, existing DB migrations, complex queries/joins
- **Multi-AZ**: 
  - Synchronous replication to standby in different AZ
  - Automatic failover (60-120 seconds)
  - For HA/DR, not for read scaling
  - DNS name stays same after failover
- **Read Replicas**: 
  - Async replication, eventual consistency
  - Scale reads, offload reporting/analytics
  - Can be in different region (cross-region)
  - Can be promoted to standalone DB
  - Up to 5 read replicas per source
- **Backup**: Automated daily backups, retention 0-35 days, transaction logs every 5 minutes
- **Performance**: Use read replicas, choose appropriate instance size, use Provisioned IOPS
- **Eliminate**: NoSQL needs, extreme scale (millions of requests/sec), sub-10ms latency, serverless preferences (use Aurora Serverless)

### Aurora
**Definition**: AWS-built cloud-native relational database compatible with MySQL and PostgreSQL

**Key Features**:
- 5x faster than MySQL, 3x faster than PostgreSQL
- Auto-scaling storage (10GB to 128TB)
- Up to 15 read replicas with <10ms lag
- 6 copies of data across 3 AZs
- Continuous backup to S3
- Point-in-time recovery
- Self-healing storage

**Exam Tips**:
- **Pick when**: High-performance MySQL/PostgreSQL, cloud-native, auto-scaling storage, need better HA than RDS, performance-critical applications
- **Aurora Serverless**: 
  - Auto-scales compute capacity based on load
  - Pay per second for DB capacity used
  - **Pick when**: Variable/unpredictable workloads, dev/test, infrequent use, new applications
- **Aurora Global Database**: 
  - Cross-region replication <1s lag
  - Up to 5 secondary regions
  - <1 minute RTO for disaster recovery
  - **Pick when**: Global applications, disaster recovery, low-latency global reads
- **Aurora Replicas**: Up to 15, auto-failover, <30s failover time
- **Cost**: Higher than RDS (20-30%), but better performance and availability, cost-effective at scale
- **Use cases**: Enterprise apps, SaaS applications, gaming, e-commerce
- **Eliminate**: Non-MySQL/PostgreSQL engines, budget-constrained small workloads, simple use cases where standard RDS suffices

### DynamoDB
**Definition**: Fully managed NoSQL database service for key-value and document data

**Key Features**:
- **Data Model**:
  - Tables: Collection of items
  - Items: Collection of attributes (like rows)
  - Attributes: Key-value pairs (like columns)
  - Primary Key: Partition key (required) or Partition key + Sort key (composite)
  - Max item size: 400KB
- **Capacity Modes**:
  - **On-demand**: Pay per request, auto-scales instantly, unpredictable workloads
  - **Provisioned**: Specify RCU/WCU, auto-scaling available, predictable workloads, cheaper at scale
  - Can switch between modes once per 24 hours
- **Read/Write Capacity**:
  - RCU (Read Capacity Unit): 1 strongly consistent read/sec or 2 eventually consistent reads/sec for 4KB
  - WCU (Write Capacity Unit): 1 write/sec for 1KB
  - Burst capacity: 5 minutes of unused capacity
- **Indexes**:
  - **Global Secondary Index (GSI)**: Different partition/sort key, can be created anytime, has own RCU/WCU
  - **Local Secondary Index (LSI)**: Same partition key, different sort key, created at table creation, shares RCU/WCU
  - Max 20 GSI, 5 LSI per table
- **DynamoDB Streams**:
  - Ordered flow of item-level changes (create, update, delete)
  - 24-hour retention
  - Exactly once processing with Kinesis adapter
  - Trigger Lambda functions for real-time processing
  - Stream views: KEYS_ONLY, NEW_IMAGE, OLD_IMAGE, NEW_AND_OLD_IMAGES
- **Global Tables**:
  - Multi-region, multi-master replication
  - Active-active: Write to any region
  - <1 second replication lag
  - Requires DynamoDB Streams enabled
  - Automatic conflict resolution (last writer wins)
- **Consistency Models**:
  - **Eventually Consistent Reads**: Default, may not reflect recent writes, cheaper (1 RCU)
  - **Strongly Consistent Reads**: Always reflects successful writes, higher cost (2 RCU)
- **Transactions**:
  - ACID support across multiple items/tables
  - All-or-nothing operations (TransactWriteItems, TransactGetItems)
  - Up to 25 items or 4MB per transaction
- **Time To Live (TTL)**:
  - Automatically delete expired items
  - No cost for deletions
  - Specify epoch timestamp attribute
- **Point-in-Time Recovery (PITR)**:
  - Continuous backups for 35 days
  - Restore to any second within retention
- **On-Demand Backup**:
  - Full table backup
  - Retained until manually deleted
  - No performance impact
  - Cross-region/cross-account copy
- **DAX (DynamoDB Accelerator)**:
  - In-memory cache, microsecond read latency
  - Write-through cache
  - Highly available (Multi-AZ)
  - Compatible with existing DynamoDB API calls
  - Cache TTL (default 5 minutes)
- **PartiQL Support**: SQL-compatible query language
- **DynamoDB Exports**: Export to S3 in DynamoDB JSON or Ion format
- **DynamoDB Imports**: Import from S3 CSV, JSON, or Ion

**Security**:
- Encryption at rest (KMS) - default enabled
- Encryption in transit (TLS)
- VPC Endpoints (Gateway Endpoint)
- IAM for authentication and authorization
- Fine-grained access control (item/attribute level)
- CloudTrail for audit logging
- Backup encryption

**Supported Integrations**:
- Lambda (DynamoDB Streams triggers), API Gateway
- Kinesis Data Streams, Kinesis Data Firehose
- S3 (export/import), Glue, Athena, EMR
- CloudWatch, X-Ray, EventBridge
- DMS, AppSync, Amplify
- Step Functions, ECS, EKS

**Exam Tips**:
- **Pick when**: Key-value/document NoSQL, single-digit ms latency, massive scale (millions of requests/sec), serverless, mobile/web/gaming/IoT apps
- **Capacity modes**:
  - **On-demand**: Unpredictable traffic, pay-per-request, auto-scaling
  - **Provisioned**: Predictable traffic, reserve capacity, cheaper at scale, can use auto-scaling
- **Features**: 
  - **GSI/LSI**: Global/Local Secondary Indexes for flexible queries
  - **DynamoDB Streams**: Capture changes, trigger Lambda, real-time processing
  - **Point-in-Time Recovery**: Restore to any second in last 35 days
  - **Global Tables**: Multi-region, multi-master, <1s replication
  - **TTL**: Automatic item expiration
- **DAX (DynamoDB Accelerator)**: 
  - In-memory cache for microsecond reads
  - **Pick when**: Read-heavy workloads, sub-millisecond response needed
- **Transactions**: ACID support for multiple items
- **Use cases**: Session state, user profiles, shopping carts, real-time bidding, gaming leaderboards, IoT
- **Eliminate**: Complex joins, SQL queries required, OLAP/analytics, strong consistency across partitions, relational data with many relationships

### ElastiCache
**Definition**: Fully managed in-memory caching service (Redis or Memcached)

**Key Features**:
- Sub-millisecond latency
- Reduce database load
- Support for Redis and Memcached engines
- Automatic failover and recovery
- Backup and restore capabilities

**Exam Tips**:
- **Redis**: 
  - Advanced data structures (sorted sets, lists, hashes)
  - Pub/sub messaging
  - Persistence (snapshots/AOF)
  - Multi-AZ with automatic failover
  - Read replicas for read scaling
  - Transactions, Lua scripting
  - **Pick when**: Complex data structures, persistence needed, HA required, pub/sub, leaderboards, real-time analytics
- **Memcached**: 
  - Simple key-value cache
  - Multi-threaded, horizontal scaling (sharding)
  - No persistence
  - **Pick when**: Simple caching, multi-threaded performance, horizontal scaling
- **Pick when**: Sub-millisecond reads, reduce DB load, session state, caching, real-time leaderboards, rate limiting
- **Use cases**: Session store, database query caching, API response caching, gaming leaderboards, real-time analytics
- **Pattern**: Cache-aside (lazy loading), Write-through, Session store
- **Eliminate**: Persistent data storage, need durable storage, object storage needs

### Redshift
**Definition**: Fully managed petabyte-scale data warehouse service

**Key Features**:
- Columnar storage and parallel query execution
- Massively parallel processing (MPP)
- SQL-based queries
- Integration with BI tools (Tableau, PowerBI, QuickSight)
- Automated backups and snapshots
- Encryption at rest and in transit
- Result caching

**Exam Tips**:
- **Pick when**: Data warehouse, OLAP, petabyte-scale analytics, BI tools, complex queries on large datasets, historical data analysis
- **Redshift Spectrum**: 
  - Query exabytes of data in S3 directly without loading
  - **Pick when**: Query data lakes, combine S3 and Redshift data, reduce ETL
- **Performance**: Columnar storage, compression, distribution keys, sort keys
- **Cost**: Reserved instances available, pay for compute + storage
- **Use cases**: Business intelligence, historical analytics, data aggregation, reporting
- **Cluster types**: Dense compute (DC2) for high performance, Dense storage (DS2) for large datasets
- **Eliminate**: OLTP workloads, real-time single-record lookups, operational databases, transactional systems, sub-second queries on small datasets

### DocumentDB
**Definition**: Fully managed MongoDB-compatible document database

**Key Features**:
- MongoDB 3.6 and 4.0 compatibility
- Automatic scaling of storage (up to 64TB)
- 6 copies across 3 AZs
- Up to 15 read replicas
- Continuous backup to S3
- Point-in-time recovery

**Exam Tips**:
- **Pick when**: MongoDB compatibility required, migrate from MongoDB, document database, JSON documents, managed NoSQL
- **Use cases**: Content management, catalogs, user profiles, mobile/web apps
- **vs DynamoDB**: Use DocumentDB when migrating from MongoDB or need MongoDB compatibility; DynamoDB is simpler/cheaper for key-value
- **Eliminate**: Key-value needs (use DynamoDB), need actual MongoDB (use MongoDB Atlas or self-managed)

### Neptune
**Definition**: Fully managed graph database service

**Key Features**:
- Supports property graph (Gremlin) and RDF (SPARQL)
- High availability with 6 copies across 3 AZs
- Up to 15 read replicas
- Continuous backup to S3
- Point-in-time recovery
- Fast query performance on billions of relationships

**Exam Tips**:
- **Pick when**: Graph database, social networks, fraud detection, recommendation engines, knowledge graphs, network/IT operations
- **Use cases**: Social networking (friend relationships), fraud detection (pattern analysis), recommendation engines, network topology, identity management
- **Query languages**: Gremlin (property graph), SPARQL (RDF)
- **Eliminate**: Relational data (use RDS), simple key-value (use DynamoDB), document storage (use DocumentDB)

### QLDB (Quantum Ledger Database)
**Definition**: Fully managed ledger database with immutable, cryptographically verifiable transaction log

**Key Features**:
- Immutable and transparent
- Cryptographically verifiable using SHA-256
- Serverless, automatic scaling
- SQL-like query language (PartiQL)
- ACID transactions
- Complete and verifiable history of changes

**Exam Tips**:
- **Pick when**: Immutable transaction log, cryptographically verifiable records, audit trail, regulatory compliance, supply chain, financial transactions
- **Use cases**: Banking transactions, supply chain tracking, vehicle registration, HR/payroll, claims history
- **vs Blockchain**: QLDB is centralized (AWS controlled), blockchain is decentralized; use QLDB when you trust central authority
- **Eliminate**: Need decentralized verification (use Amazon Managed Blockchain), standard database with updates/deletes

### Timestream
**Definition**: Fully managed time-series database for IoT and operational applications

**Key Features**:
- Purpose-built for time-series data
- Automatic data tiering (memory to magnetic storage)
- 1000x faster and 1/10th cost of relational databases for time-series
- Built-in time-series analytics functions
- Serverless, auto-scaling
- SQL-compatible query language

**Exam Tips**:
- **Pick when**: Time-series data, IoT sensor data, operational metrics, application monitoring, DevOps metrics, clickstream data
- **Use cases**: IoT applications, industrial telemetry, application monitoring, real-time analytics on time-stamped data
- **Storage tiers**: Memory (recent data, fast queries), Magnetic (historical data, cost-optimized)
- **Eliminate**: Non-time-series data, general-purpose database needs, need for complex joins across non-temporal data

---

## 4. Networking & Content Delivery

### VPC (Virtual Private Cloud)
**Definition**: Isolated virtual network within AWS cloud for launching AWS resources

**Key Features**:
- **CIDR Blocks**: 
  - Primary CIDR: /16 to /28 (65,536 to 16 IPs)
  - Secondary CIDR: Up to 4 additional CIDR blocks
  - Cannot overlap with existing CIDR blocks
  - Reserved IPs: First 4 and last 1 in each subnet (.0, .1, .2, .3, .255)
- **Subnets**:
  - Public subnet: Has route to Internet Gateway
  - Private subnet: No direct route to Internet Gateway
  - One subnet = One AZ (subnet cannot span AZs)
  - Can have multiple subnets per AZ
  - Auto-assign public IPv4/IPv6 option
- **Route Tables**:
  - Controls traffic routing in VPC
  - Each subnet must be associated with one route table
  - Main route table: Default for VPC
  - Custom route tables for specific routing needs
  - Routes: Destination CIDR + Target (IGW, NAT, VPC Peering, TGW, VPN)
- **Internet Gateway (IGW)**:
  - Horizontally scaled, redundant, highly available
  - One IGW per VPC
  - Provides route to internet for public subnets
  - Performs NAT for instances with public IPs
- **NAT Gateway**:
  - Managed NAT service for private subnets
  - Deployed in public subnet, requires Elastic IP
  - 5 Gbps to 45 Gbps bandwidth
  - Supports up to 55,000 simultaneous connections
  - Automatic failover within AZ (deploy in each AZ for HA)
  - Charged per hour + data processed
- **NAT Instance** (legacy):
  - EC2 instance with NAT AMI
  - Deployed in public subnet
  - Must disable source/destination check
  - Cheaper but requires management
  - Can use as bastion host
- **Security Groups**:
  - Virtual firewall at instance/ENI level
  - Stateful (return traffic automatically allowed)
  - Allow rules only (no deny rules)
  - Evaluate all rules before allowing traffic
  - Can reference other security groups
  - Default: Outbound all allowed, inbound all denied
- **Network ACLs (NACLs)**:
  - Firewall at subnet level
  - Stateless (must allow return traffic explicitly)
  - Allow and deny rules
  - Rules evaluated in number order (lowest first)
  - Default NACL: All inbound/outbound allowed
  - Custom NACL: All traffic denied by default
  - One NACL per subnet, but NACL can be associated with multiple subnets
- **VPC Peering**:
  - Private connection between two VPCs
  - Non-transitive (A-B-C requires A-C peering)
  - Can peer across regions and accounts
  - No overlapping CIDR blocks
  - Update route tables for peering
  - No single point of failure
- **VPC Endpoints**:
  - **Gateway Endpoints**: S3, DynamoDB, free, route table entry
  - **Interface Endpoints**: All other services, ENI with private IP, hourly + data charges, powered by PrivateLink
- **VPC Flow Logs**:
  - Capture IP traffic info (accepted/rejected)
  - Can be created at VPC, subnet, or ENI level
  - Publish to CloudWatch Logs or S3
  - Does not capture: DHCP, DNS (Route 53), metadata, Windows license activation
- **Elastic Network Interface (ENI)**:
  - Virtual network card
  - Has private IP, Elastic IP, MAC address, security groups
  - Can attach/detach from instances
  - Bound to specific AZ
- **Elastic IP (EIP)**:
  - Static public IPv4 address
  - Associated with AWS account, not instance
  - Can be remapped to another instance quickly
  - Charged when not associated with running instance
- **VPC Sharing**:
  - Share subnets with other AWS accounts (same organization)
  - Centralized VPC management
  - Owner manages VPC, participants create resources
- **DHCP Options Sets**:
  - Configure domain name servers, NTP servers, NetBIOS servers
  - Cannot modify, must create new and associate
- **IPv6 Support**:
  - /56 CIDR block for VPC
  - /64 CIDR block for each subnet
  - All IPv6 addresses are public
  - Egress-Only Internet Gateway for IPv6 (like NAT for IPv4)
- **DNS in VPC**:
  - Amazon-provided DNS at VPC base + 2 (e.g., 10.0.0.2)
  - enableDnsHostnames: Assign public DNS hostname
  - enableDnsSupport: Enable DNS resolution

**Supported Integrations**:
- EC2, ECS, EKS, Lambda, RDS, ElastiCache, Redshift
- ALB, NLB, NAT Gateway, VPN Gateway, Transit Gateway
- Direct Connect, PrivateLink, VPC Endpoints
- Route 53 (Private Hosted Zones), CloudFront (via origin)
- Security Groups, NACLs, WAF, Shield, GuardDuty
- CloudWatch, VPC Flow Logs, CloudTrail

**Exam Tips**:
- **Components**: 
  - Subnets (public/private), Route Tables, Internet Gateway (IGW), NAT Gateway/Instance
  - VPC Endpoints (Gateway for S3/DynamoDB, Interface for other services)
  - VPN Gateway, Direct Connect Gateway, Transit Gateway
- **Design pattern**: 
  - Public subnets: ALB, NAT Gateway, bastion hosts (route to IGW)
  - Private subnets: Application servers, databases (route to NAT Gateway for outbound)
- **Security**: 
  - **Security Groups**: Stateful, instance-level, allow rules only, return traffic automatically allowed
  - **NACLs**: Stateless, subnet-level, allow and deny rules, separate inbound/outbound rules
- **CIDR blocks**: /16 to /28, can add secondary CIDR blocks
- **VPC Peering**: Connect 2 VPCs, non-transitive, can peer across regions
- **Use cases**: Isolate resources, multi-tier applications, hybrid cloud connectivity
- **Eliminate**: Public internet without control (use CloudFront, Global Accelerator)

### Load Balancers
**Definition**: Distribute incoming application traffic across multiple targets

**Key Features**:
- High availability across multiple AZs
- Health checks and automatic failover
- SSL/TLS termination
- Integration with Auto Scaling
- Access logs to S3

**Exam Tips**:
- **ALB (Application Load Balancer)**: 
  - HTTP/HTTPS, layer 7 (application layer)
  - Path-based and host-based routing
  - Lambda targets, ECS/EKS integration
  - WebSocket support
  - **Pick when**: Web applications, microservices, container-based apps, need content-based routing, Lambda targets
- **NLB (Network Load Balancer)**: 
  - TCP/UDP/TLS, layer 4 (transport layer)
  - Ultra-low latency (<100μs), millions of requests/sec
  - Static IP addresses per AZ, Elastic IPs
  - Preserve source IP
  - **Pick when**: Extreme performance, static IPs needed, non-HTTP protocols, gaming, IoT
- **GWLB (Gateway Load Balancer)**: 
  - Layer 3 (network layer)
  - Deploy, scale, and manage third-party virtual appliances
  - **Pick when**: Firewalls, intrusion detection, deep packet inspection, traffic inspection appliances
- **CLB (Classic Load Balancer)**: 
  - Legacy, layer 4 and 7
  - **Eliminate**: Avoid for new architectures, use ALB/NLB instead
- **Cross-Zone Load Balancing**: Distribute traffic evenly across all targets in all enabled AZs
- **Sticky Sessions**: Route requests from same client to same target

### Route 53
**Definition**: Scalable and highly available Domain Name System (DNS) web service

**Key Features**:
- **Domain Registration**: Register new domains, transfer existing domains
- **Hosted Zones**:
  - **Public Hosted Zone**: DNS for internet-facing resources ($0.50/month)
  - **Private Hosted Zone**: DNS for VPC resources ($0.50/month)
  - Can associate private zone with multiple VPCs (even cross-account)
- **Record Types**:
  - **A**: IPv4 address
  - **AAAA**: IPv6 address
  - **CNAME**: Canonical name (alias for another domain, can't use for zone apex)
  - **Alias**: AWS-specific, like CNAME but works for zone apex, free queries
  - **MX**: Mail exchange servers
  - **TXT**: Text records (SPF, DKIM, verification)
  - **NS**: Name server records
  - **SOA**: Start of authority
  - **CAA**: Certificate Authority Authorization
  - **SRV**: Service locator
- **Routing Policies**:
  - **Simple**: Single resource, no health checks, multiple values returned randomly
  - **Weighted**: Distribute traffic by percentages (0-255), supports health checks
    - Use case: A/B testing, gradual migration, load distribution
  - **Latency**: Route to region with lowest latency for user
    - Based on AWS region, requires health checks for failover
  - **Failover**: Active-passive, primary and secondary resources
    - Requires health checks on primary, evaluates health before routing
  - **Geolocation**: Route based on user's geographic location (continent, country, state)
    - Default location for unmatched queries, supports health checks
  - **Geoproximity**: Route based on geographic location with bias (-99 to +99)
    - Requires Route 53 Traffic Flow, can shift traffic to/away from resources
  - **Multi-value**: Return multiple IP addresses (up to 8), basic load balancing
    - Each record can have health check, only healthy records returned
  - **IP-based**: Route based on client's IP address (CIDR blocks)
- **Health Checks**:
  - Monitor endpoint health (HTTP, HTTPS, TCP)
  - 15 or 30-second intervals (10-second option for higher cost)
  - Can monitor CloudWatch alarms (calculated health check)
  - Can monitor other health checks (composite health checks)
  - Health checkers from ~15 global locations
  - Pass threshold: 18% of health checkers must report healthy
  - Can trigger CloudWatch alarms and SNS notifications
  - String matching: Check for specific string in response
- **Traffic Flow**: Visual editor for complex routing configurations
- **Traffic Policies**: Reusable routing configurations, versioned
- **Resolver**:
  - Conditional forwarding rules (forward DNS queries to on-premises)
  - Inbound endpoints: On-premises can query Route 53 private zones
  - Outbound endpoints: VPC can query on-premises DNS
  - Resolver rules can be shared across accounts (RAM)
- **DNSSEC**: Protect against DNS spoofing, validate DNS responses
- **Query Logging**: Log DNS queries to CloudWatch Logs
- **Service Discovery**: Register instances for microservices (Cloud Map integration)

**Alias Records vs CNAME**:
- **Alias**: Free, works for zone apex, only AWS resources, automatic updates
- **CNAME**: Paid queries, doesn't work for zone apex, any domain, manual updates

**Supported Target Resources (Alias Records)**:
- ELB (ALB, NLB, CLB), CloudFront distributions, API Gateway
- S3 website endpoints, VPC interface endpoints
- Global Accelerator, Elastic Beanstalk, Route 53 records in same hosted zone

**Supported Integrations**:
- All AWS services for DNS resolution
- CloudWatch for monitoring and alarms
- CloudTrail for API logging
- IAM for access control, Cost allocation tags
- AWS Cloud Map for service discovery

**Exam Tips**:
- **Pick when**: DNS management, health checks, traffic policies, domain registration, global traffic routing
- **Routing policies**: 
  - **Simple**: Single resource, no health checks
  - **Weighted**: Distribute traffic by percentages, A/B testing
  - **Latency**: Route to region with lowest latency
  - **Failover**: Active-passive failover, requires health checks
  - **Geolocation**: Route based on user location
  - **Geoproximity**: Route based on geographic location of resources, bias adjustment
  - **Multi-value**: Return multiple IP addresses, basic load balancing with health checks
- **Health checks**: Monitor endpoints, trigger failover
- **Use cases**: Global traffic management, DR failover, multi-region active-active, blue/green deployments
- **Alias records**: Free, AWS resource targets (ELB, CloudFront, S3, API Gateway)
- **Private hosted zones**: DNS for VPC resources
- **Eliminate**: Content delivery (use CloudFront), load balancing within region (use ELB)

### CloudFront
**Definition**: Global content delivery network (CDN) for fast delivery of web content

**Key Features**:
- **Edge Locations**: 400+ points of presence in 90+ cities across 48 countries
- **Regional Edge Caches**: Larger caches between origin and edge locations (13 locations)
- **Distribution Types**:
  - **Web Distribution**: Static and dynamic content, HTTP/HTTPS
  - **RTMP Distribution**: Media streaming (deprecated, use Web distribution)
- **Origins**:
  - **S3 Bucket**: Static content, Origin Access Control (OAC) for private access
  - **Custom Origin**: ALB, NLB, EC2, S3 website endpoint, HTTP servers, MediaStore, MediaPackage
  - **Origin Groups**: Failover between primary and secondary origins
  - Multiple origins per distribution with path patterns
- **Caching**:
  - **TTL (Time To Live)**: Min 0s, Max 31536000s (1 year), Default 86400s (24 hours)
  - **Cache key**: Combination of URL, query strings, headers, cookies
  - **Cache behaviors**: Different settings for different URL path patterns
  - **Invalidation**: Remove objects from cache before TTL ($0.005 per path for >1000 paths/month)
  - **Versioning**: Use versioned filenames instead of invalidation (free)
- **Origin Access Control (OAC)**: 
  - Restrict S3 bucket access to CloudFront only
  - Works with S3 SSE-KMS encryption
  - Supports all S3 buckets (including in other accounts)
  - Replaces Origin Access Identity (OAI)
- **Signed URLs & Signed Cookies**:
  - Restrict access to content
  - Signed URLs: Individual files, use when client doesn't support cookies
  - Signed Cookies: Multiple files, use when client supports cookies
  - Can specify IP ranges, expiration time, trusted signers
- **Field-Level Encryption**: Encrypt specific form fields at edge, decrypt at origin
- **Lambda@Edge**: Run Lambda functions at edge locations
  - **Viewer Request**: After CloudFront receives request from viewer
  - **Origin Request**: Before CloudFront forwards request to origin
  - **Origin Response**: After CloudFront receives response from origin
  - **Viewer Response**: Before CloudFront returns response to viewer
  - Use cases: A/B testing, auth, SEO, intelligent redirects, image manipulation
- **CloudFront Functions**: Lightweight JavaScript for viewer request/response
  - Faster and cheaper than Lambda@Edge
  - Sub-millisecond execution
  - Use cases: Cache key normalization, header manipulation, URL rewrites
- **Geo Restriction**:
  - Whitelist: Allow only specified countries
  - Blacklist: Block specified countries
  - Based on third-party GeoIP database
- **Price Classes**: Control which edge locations to use (All, 200, 100)
  - All: Best performance, highest cost
  - 200: Exclude most expensive regions
  - 100: Only least expensive regions
- **HTTPS**:
  - Viewer Protocol Policy: HTTP/HTTPS, Redirect HTTP to HTTPS, HTTPS only
  - Origin Protocol Policy: HTTP only, HTTPS only, Match viewer
  - SNI (Server Name Indication): Serve multiple domains from same IP (free)
  - Dedicated IP: Custom SSL certificate on every edge location ($600/month)
- **Compression**: Automatic gzip/brotli compression for text files
- **Real-Time Logs**: Stream logs to Kinesis Data Streams within seconds
- **Standard Logs**: Detailed logs to S3 (free, but S3 storage charged)
- **Monitoring**: CloudWatch metrics (requests, bytes, error rates)
- **WebSocket**: Full-duplex communication over single TCP connection
- **HTTP/2 and HTTP/3**: Supported for faster page loads
- **Origin Shield**: Additional caching layer to reduce origin load

**Security Features**:
- AWS Shield Standard (free DDoS protection)
- AWS WAF integration for application-level protection
- SSL/TLS encryption (Certificate Manager integration)
- Geo restriction
- Signed URLs and cookies for content protection
- Origin access control for S3
- Field-level encryption

**Supported Origins**:
- S3 buckets (with OAC), S3 website endpoints
- EC2 instances, ECS containers
- ALB, NLB (must be internet-facing)
- API Gateway, Lambda Function URLs
- On-premises servers (any HTTP server)
- MediaStore, MediaPackage, Elemental MediaPackage

**Supported Integrations**:
- S3, EC2, ALB, NLB, API Gateway, Lambda
- Route 53 (Alias records), ACM (SSL certificates)
- WAF, Shield, CloudWatch, CloudTrail
- Lambda@Edge, CloudFront Functions
- AWS Certificate Manager, IAM

**Exam Tips**:
- **Pick when**: CDN, global content delivery, reduce latency, static/dynamic content caching, DDoS protection, improve website performance
- **Origins**: S3 buckets, ALB, EC2, custom HTTP servers, MediaStore, MediaPackage
- **Features**: 
  - Edge locations for caching (read)
  - Regional edge caches (larger cache)
  - OAI (Origin Access Identity) for private S3 access
  - Signed URLs/cookies for restricted content
  - Lambda@Edge for customization at edge
  - Field-level encryption
- **Use cases**: Website acceleration, video streaming, software distribution, API acceleration, security
- **Cache behavior**: TTL, invalidation requests
- **Geo restriction**: Whitelist/blacklist countries
- **vs S3 Transfer Acceleration**: CloudFront for content delivery, S3 TA for uploads
- **Eliminate**: Origin is only in single location and users are nearby (direct S3/ELB may be cheaper)

### API Gateway
**Definition**: Fully managed service for creating, publishing, and managing APIs

**Key Features**:
- RESTful APIs, HTTP APIs, WebSocket APIs
- Request/response transformation
- Throttling and quota management
- API versioning and stages
- Integration with Lambda, HTTP endpoints, AWS services
- Built-in caching

**Exam Tips**:
- **Pick when**: REST/HTTP/WebSocket APIs, serverless backends, API management, mobile/web app backends
- **Types**: 
  - **REST API**: Feature-rich, API keys, request validation, caching, usage plans
  - **HTTP API**: Cheaper (70% less), faster, simpler, OIDC/OAuth 2.0
  - **WebSocket API**: Two-way real-time communication, chat apps, streaming
- **Features**: 
  - Throttling (rate limiting per client)
  - Caching (reduce backend calls, TTL 0-3600s)
  - Authorization: IAM, Cognito User Pools, Lambda authorizers, API keys
  - Request/response transformation
  - Mock integrations for testing
- **Integration types**: Lambda, HTTP endpoints, AWS services (DynamoDB, S3, Step Functions)
- **Stages**: Dev, test, prod with different configurations
- **Use cases**: Serverless backends, microservices APIs, mobile backends, legacy API modernization
- **Eliminate**: Pure static content (use CloudFront + S3), no API management needed

### Direct Connect
**Definition**: Dedicated private network connection from on-premises to AWS

**Key Features**:
- Dedicated network connection (1 Gbps to 100 Gbps)
- Consistent network performance
- Reduced bandwidth costs
- Private connectivity (bypasses internet)
- Can work with VPN for IPsec encryption
- Multiple virtual interfaces on single connection

**Exam Tips**:
- **Pick when**: Dedicated network connection, consistent bandwidth, large data transfer, hybrid cloud, low latency requirements, regulatory compliance
- **Virtual Interfaces**: 
  - **Private VIF**: Access VPC via private IPs
  - **Public VIF**: Access AWS public services (S3, DynamoDB) via public IPs
  - **Transit VIF**: Connect to Transit Gateway for multiple VPCs
- **Connection types**: Dedicated (single customer) vs Hosted (shared, via AWS partners)
- **LAG (Link Aggregation Groups)**: Combine multiple connections for higher throughput
- **Use cases**: Large dataset transfers, real-time data feeds, hybrid cloud architectures
- **Setup time**: Weeks to months (physical connection)
- **Resilience**: Use multiple Direct Connect locations + VPN backup
- **vs VPN**: Direct Connect = dedicated, expensive, consistent; VPN = over internet, quick, variable performance
- **Eliminate**: Immediate connectivity needed (use VPN), small data transfer, budget-constrained

### VPN
**Definition**: Secure encrypted connection over the internet to AWS

**Key Features**:
- IPsec VPN tunnels
- Quick to set up (minutes)
- Encrypted traffic
- Integration with Route 53 and Transit Gateway
- Redundant tunnels for HA

**Exam Tips**:
- **Site-to-Site VPN**: 
  - Connect on-premises data center to AWS
  - Two tunnels for HA (active/passive or active/active)
  - Customer Gateway (on-premises) + Virtual Private Gateway (AWS side)
  - **Pick when**: Quick connectivity, secure hybrid cloud, backup for Direct Connect
- **Client VPN**: 
  - Remote users to VPC
  - Based on OpenVPN
  - Authentication via Active Directory, SAML, certificate-based
  - **Pick when**: Remote workforce access, secure client access to VPC resources
- **Use cases**: Hybrid connectivity, disaster recovery, remote access, temporary connections
- **VPN CloudHub**: Multiple site-to-site VPN connections, hub-and-spoke model
- **Accelerated Site-to-Site VPN**: Uses Global Accelerator for improved performance
- **vs Direct Connect**: VPN faster to set up, lower cost, variable performance; Direct Connect more consistent, higher bandwidth
- **Eliminate**: Need dedicated connection (use Direct Connect), need consistent low latency

### Transit Gateway
**Definition**: Network hub to connect VPCs and on-premises networks

**Key Features**:
- Hub-and-spoke network topology
- Connect thousands of VPCs and on-premises networks
- Supports multicast
- Inter-region peering
- Route table control for traffic flow
- Integration with Direct Connect and VPN

**Exam Tips**:
- **Pick when**: Hub-and-spoke architecture, many VPCs (>10), centralized routing, on-prem integration, complex network topologies
- **Benefits**: Simplifies network architecture, reduces connections needed (n connections vs n² for VPC peering)
- **Features**:
  - Multiple route tables for traffic segmentation
  - Multicast support (only AWS service with native multicast)
  - Appliance mode for stateful inspection
  - Cross-region peering
- **Use cases**: Multi-VPC architectures, centralized network management, hybrid cloud, shared services VPC
- **Transit Gateway attachments**: VPCs, VPN connections, Direct Connect Gateways, peering connections
- **vs VPC Peering**: Transit Gateway for many VPCs and central management, VPC Peering for simple 1-to-1
- **Eliminate**: Simple 1-to-1 VPC connectivity (use VPC Peering), only 2-3 VPCs

### VPC Endpoints
**Definition**: Private connection between VPC and AWS services without internet gateway

**Key Features**:
- Private connectivity to AWS services
- Traffic stays within AWS network
- No data transfer charges (for Gateway Endpoints)
- Powered by AWS PrivateLink

**Exam Tips**:
- **Gateway Endpoints**: 
  - S3 and DynamoDB only
  - Free
  - Route table entry
  - Highly available within region
  - **Pick when**: Private S3/DynamoDB access, reduce NAT Gateway costs
- **Interface Endpoints** (PrivateLink): 
  - All other AWS services
  - ENI-based with private IP
  - Hourly + data transfer charges
  - Security group control
  - **Pick when**: Private access to AWS services from VPC, VPN, or Direct Connect
- **Benefits**: 
  - No internet gateway, NAT device, VPN, or Direct Connect needed
  - Reduced data transfer costs
  - Better security (private connectivity)
  - Improved performance (lower latency)
- **Use cases**: Keep traffic within AWS network, access S3 from private subnet, compliance requirements
- **Eliminate**: Need public internet access, cross-region access (endpoints are regional)

### Global Accelerator
**Definition**: Network service that improves global application availability and performance using AWS global network

**Key Features**:
- **Anycast IP Addresses**: 
  - 2 static anycast IPs provided per accelerator
  - IPs announced from multiple edge locations simultaneously
  - Traffic routed to optimal AWS edge location
  - No need to update DNS or client-facing IPs
- **Global Network**: 
  - Uses AWS private backbone network (not internet)
  - Routes traffic through AWS edge locations to endpoints
  - 60% performance improvement for some applications
  - Consistent performance regardless of user location
- **Endpoint Types**:
  - Network Load Balancers (NLB)
  - Application Load Balancers (ALB)
  - EC2 instances (Elastic IP)
  - Elastic IP addresses
  - Can be in multiple regions
- **Traffic Dial**: Control percentage of traffic to endpoint group (0-100%)
- **Endpoint Weights**: Distribute traffic across endpoints within group
- **Health Checks**:
  - Automatic health checking of endpoints
  - 10 or 30-second intervals
  - Automatic failover to healthy endpoints (<30 seconds)
  - Can override health check for maintenance
- **Client Affinity**: 
  - Route requests from same client to same endpoint
  - Based on source IP address
  - 5-tuple hash (source IP, source port, destination IP, destination port, protocol)
- **Listeners**: 
  - TCP, UDP, or both
  - Port ranges (single port or multiple ports)
  - Up to 10 listeners per accelerator
- **Security**:
  - AWS Shield Standard (DDoS protection) included
  - AWS Shield Advanced compatible
  - TLS termination at NLB/ALB (not at edge)
  - Integration with AWS WAF (via ALB)
- **Custom Routing Accelerator**: 
  - Deterministic routing to specific EC2 instances
  - Map user to specific destination IP:port
  - Use case: Gaming, VoIP, custom routing logic
- **Monitoring**: CloudWatch metrics (processed bytes, new/active connections)
- **Flow Logs**: Capture information about IP traffic

**Supported Integrations**:
- ALB, NLB, EC2, Elastic IP addresses
- CloudWatch for monitoring, CloudTrail for API logging
- AWS Shield Standard/Advanced for DDoS protection
- Route 53 (can use Alias records), ACM for certificates
- AWS WAF (via ALB integration)
- VPC, Security Groups, EventBridge

**Exam Tips**:
- **Pick when**: Static anycast IPs, improved global availability/performance for TCP/UDP apps, health checks, instant regional failover, gaming/IoT/VoIP
- **How it works**: Traffic enters AWS network at nearest edge location, travels over AWS backbone
- **Benefits**:
  - 2 static IPs for all endpoints (vs dynamic ALB IPs)
  - Fast regional failover (<30s)
  - Deterministic routing (no DNS caching issues)
  - DDoS protection
  - 60% performance improvement for multi-region apps
- **Use cases**: Gaming (UDP), IoT, VoIP, global applications, blue/green deployments, disaster recovery
- **Endpoints**: ALB, NLB, EC2 instances, Elastic IPs
- **vs CloudFront**: 
  - Global Accelerator = TCP/UDP apps, static IPs, non-HTTP use cases, no caching
  - CloudFront = HTTP/HTTPS content, caching at edge locations
- **Eliminate**: HTTP content delivery with caching (use CloudFront), single-region apps

---

## 5. Security, Identity & Compliance

### IAM (Identity and Access Management)
**Definition**: Manage access to AWS services and resources securely through authentication and authorization

**Key Features**:
- **Users**:
  - Individual identity with permanent long-term credentials
  - Username + password for console access
  - Access keys (access key ID + secret) for programmatic access
  - Up to 2 access keys per user (rotation)
  - MFA (Multi-Factor Authentication) support (virtual/hardware MFA, U2F)
  - Max 5000 users per account
- **Groups**:
  - Collection of users
  - Attach policies to groups (users inherit permissions)
  - User can belong to multiple groups (max 10)
  - No nesting (groups cannot contain other groups)
  - Max 300 groups per account
- **Roles**:
  - Temporary credentials for AWS services or federated users
  - No long-term credentials (access keys)
  - Assumed by users, services, or applications
  - Trust policy (who can assume) + permissions policy (what they can do)
  - Session duration: 15 min to 12 hours (default 1 hour)
  - Types:
    - **AWS Service Role**: For EC2, Lambda, ECS, etc.
    - **Cross-Account Role**: Access resources in another AWS account
    - **Identity Provider Role**: For SAML 2.0, OIDC, Web Identity Federation
- **Policies**:
  - **Identity-based**: Attach to users, groups, roles
  - **Resource-based**: Attach to resources (S3 buckets, SQS queues, Lambda)
  - **Managed Policies**: 
    - AWS Managed (created by AWS, e.g., AdministratorAccess)
    - Customer Managed (you create and manage)
    - Max 6144 characters, version limit 5
  - **Inline Policies**: Embedded directly in user, group, or role
  - JSON structure: Version, Statement (Effect, Action, Resource, Condition)
  - Evaluation logic: Explicit Deny > Explicit Allow > Implicit Deny (default)
- **Policy Elements**:
  - **Effect**: Allow or Deny
  - **Action**: Service actions (e.g., s3:GetObject, ec2:StartInstances)
  - **Resource**: ARN of resources (e.g., arn:aws:s3:::bucket/*)
  - **Condition**: Optional conditions (IP address, time, MFA, tags, etc.)
  - **Principal**: Who (for resource-based policies)
- **Permission Boundaries**: 
  - Maximum permissions an identity can have
  - Does not grant permissions, only limits
  - Use case: Delegate permission management without giving full admin
- **Service Control Policies (SCPs)**:
  - Part of AWS Organizations
  - Restrict permissions across accounts
  - Affect all users/roles (including root)
  - Do not grant permissions, only restrict
- **Access Analyzer**: 
  - Identify resources shared with external entities
  - Analyze policies for public/cross-account access
  - Generate least-privilege policies based on activity
- **Credential Report**: 
  - Account-level report of all users and credential status
  - CSV format, password age, MFA status, access key rotation
- **Access Advisor**: 
  - User-level, shows service permissions and last accessed time
  - Helps identify unused permissions for least privilege
- **IAM Identity Center (AWS SSO)**: 
  - Centralized access management for multiple AWS accounts
  - Single sign-on to AWS accounts and applications
  - Integration with Microsoft AD, SAML 2.0 IdPs
- **Password Policy**: 
  - Minimum length, character requirements
  - Password expiration, reuse prevention
  - User self-service password reset
- **Security Token Service (STS)**:
  - Generate temporary credentials
  - AssumeRole, AssumeRoleWithSAML, AssumeRoleWithWebIdentity
  - GetSessionToken (MFA), GetFederationToken

**Supported Integrations**:
- All AWS services (authentication and authorization)
- CloudTrail (audit), CloudWatch (monitoring)
- AWS Organizations (SCPs), SAML 2.0, OIDC providers
- Secrets Manager, Parameter Store, KMS

**Exam Tips**:
- **Users**: Long-term credentials (username/password, access keys), for individuals
  - **Avoid**: Using access keys in applications, hardcoding credentials
- **Groups**: Collection of users, attach policies to groups
- **Roles**: Temporary credentials, no password/access keys, assumable by users/services
  - **Use for**: EC2 instances, Lambda functions, cross-account access, federated users
- **Policies**: JSON documents defining permissions
  - Identity-based (attached to users/groups/roles)
  - Resource-based (attached to resources like S3 buckets)
  - Permissions boundaries, SCPs (in Organizations)
- **Best practices**: 
  - Use roles over access keys for applications
  - MFA for privileged users/root account
  - Least privilege principle
  - Password policies and rotation
  - Never use root account for daily tasks
  - Enable CloudTrail for API auditing
- **Policy Evaluation**: Explicit deny > Explicit allow > Implicit deny
- **Cross-account access**: Use roles, not access keys
- **Eliminate**: Sharing credentials, embedding keys in code, using root account regularly

### Cognito
**Definition**: Fully managed user identity and authentication service for web and mobile applications

**Key Features**:
- **User Pools**:
  - User directory for sign-up and sign-in
  - Customizable hosted UI for authentication
  - Username/password, email/phone number verification
  - MFA support (SMS, TOTP with authenticator apps)
  - Password policies (length, complexity, expiration)
  - Account recovery (email/SMS)
  - Email/SMS verification workflows
  - Lambda triggers for custom workflows (pre-signup, post-confirmation, pre-authentication, etc.)
  - OAuth 2.0, SAML 2.0, OpenID Connect support
  - Social identity providers (Facebook, Google, Amazon, Apple)
  - Enterprise identity providers (SAML 2.0: Active Directory, Okta, etc.)
  - JWT tokens (ID token, access token, refresh token)
  - Max 40 million users per user pool
  - Advanced security features (compromised credentials detection, adaptive authentication)
- **Identity Pools (Federated Identities)**:
  - Provide temporary AWS credentials to users
  - Exchange identity provider tokens for AWS credentials (STS)
  - Support multiple identity providers:
    - Cognito User Pools, Social (Facebook, Google, Amazon, Apple)
    - SAML 2.0, OpenID Connect, Custom developer-authenticated identities
    - Unauthenticated (guest) access
  - Role-based access control (authenticated vs unauthenticated roles)
  - Fine-grained access control with policy variables
  - Direct access to AWS resources (S3, DynamoDB, etc.)
- **Token Management**:
  - ID token: User identity information (claims)
  - Access token: Authorize API access
  - Refresh token: Obtain new ID/access tokens (validity 1-3650 days)
  - Token expiration configurable (5 min to 1 day for ID/access)
- **Hosted UI**: Fully managed, customizable sign-in/sign-up pages
- **Custom Domain**: Use your own domain for hosted UI (requires ACM certificate)

**Supported Integrations**:
- API Gateway (authorizer), ALB (authentication), AppSync, Amplify
- Lambda (triggers), SNS (SMS/email), SES (email)
- CloudWatch Logs, CloudTrail
- Social IdPs, SAML 2.0, OpenID Connect providers
- All AWS services (via Identity Pool credentials)

**Exam Tips**:
- **User Pools**: 
  - User directory for authentication (sign-up/sign-in)
  - Provides JWT tokens
  - MFA, password policies, account recovery
  - Social logins (Facebook, Google, Amazon)
  - SAML/OIDC integration
  - **Pick when**: Need user authentication, user directory, mobile/web app sign-in
- **Identity Pools** (Federated Identities): 
  - Provide AWS credentials to users
  - Authorization (access AWS services directly)
  - Exchange tokens for temporary AWS credentials
  - Guest/unauthenticated access support
  - **Pick when**: Users need direct AWS resource access (S3, DynamoDB), mobile apps accessing AWS
- **Use together**: User Pools for authentication → Identity Pools for authorization
- **Use cases**: Mobile apps, web apps, API authentication, social login, federated access
- **vs IAM**: Cognito for customer-facing apps (millions of users), IAM for internal AWS users/services
- **Eliminate**: Internal employee access (use IAM, SSO), need custom authentication system

### KMS (Key Management Service)
**Definition**: Managed service for creating and controlling encryption keys used to encrypt data

**Key Features**:
- **KMS Keys (Customer Master Keys - CMK)**:
  - **AWS Managed Keys**: Created automatically by AWS services (free), auto-rotation every year, can't manage
  - **Customer Managed Keys**: You create and manage, $1/month, control rotation, can disable/enable
  - **AWS Owned Keys**: Used by AWS services, not in your account, no cost, no visibility
  - **CloudHSM Keys**: Generated from CloudHSM cluster, FIPS 140-2 Level 3
- **Key Types**:
  - **Symmetric (AES-256)**: Single encryption key, used for encrypt/decrypt, never leaves KMS unencrypted
  - **Asymmetric (RSA, ECC)**: Public/private key pair, public key downloadable, used for encrypt/decrypt or sign/verify
- **Key Policies**:
  - Primary access control mechanism (like resource-based policies)
  - Default key policy: Allow root user full access
  - Can combine with IAM policies
  - Required for cross-account access
- **Key Rotation**:
  - Automatic rotation: Once per year for customer managed keys
  - Manual rotation: Create new key and update applications
  - AWS managed keys: Automatically rotated every year (can't disable)
  - Rotation doesn't change key ID, old key material kept for decryption
- **Envelope Encryption**:
  - Encrypt data with Data Encryption Key (DEK)
  - Encrypt DEK with KMS key (envelope)
  - AWS services use envelope encryption for large data (>4KB)
  - Reduces network load (only DEK sent to KMS)
- **Key Operations**:
  - Encrypt/Decrypt (up to 4KB data directly)
  - GenerateDataKey (get DEK for envelope encryption)
  - GenerateDataKeyWithoutPlaintext
  - ReEncrypt (change key without exposing data)
- **API Request Limits**:
  - Shared quota across symmetric and asymmetric keys
  - 5500, 10000, or 30000 requests/second (varies by region)
  - Can request quota increase
  - Use data key caching to reduce KMS calls
- **Multi-Region Keys**:
  - Replicate keys across regions
  - Same key ID, key material in multiple regions
  - Encrypt in one region, decrypt in another
  - Use case: Global applications, disaster recovery
- **Key Import**:
  - Bring Your Own Key (BYOK)
  - Import 256-bit symmetric key
  - Manual rotation required
  - Can set expiration
- **Key Deletion**:
  - Waiting period: 7-30 days
  - Prevents accidental deletion
  - Key can't be used during waiting period
  - Can cancel deletion during waiting period
- **Grants**:
  - Alternative to key policies
  - Programmatic delegation of permissions
  - Can be created/revoked programmatically
  - Use case: Temporary permissions, service integrations

**Compliance & Security**:
- FIPS 140-2 Level 2 validated (Level 3 with CloudHSM)
- CloudTrail integration for audit logs
- VPC Endpoints for private connectivity
- Never export symmetric keys (asymmetric public keys can be downloaded)
- Automatic key backups

**Supported Integrations**:
- S3, EBS, RDS, Aurora, DynamoDB, Redshift, ElastiCache
- Lambda, CloudWatch Logs, Systems Manager, Secrets Manager
- SQS, SNS, Kinesis, EFS, FSx
- CloudTrail, Config, CodeBuild, CodePipeline
- All services that support encryption at rest

**Exam Tips**:
- **CMK (Customer Master Keys)**: 
  - AWS managed (free, auto-rotation)
  - Customer managed (monthly fee, control rotation)
  - Can't export CMK, all operations done within KMS
- **Pick when**: Encryption at rest, centralized key management, compliance requirements (HIPAA, PCI-DSS), audit key usage
- **Integration**: S3, EBS, RDS, DynamoDB, Lambda, SSM Parameter Store, Secrets Manager, etc.
- **Key policies**: Control who can use/manage keys
- **Multi-region keys**: Use same key across multiple regions
- **Envelope encryption**: Encrypt data with data key, encrypt data key with CMK
- **Use cases**: Encrypt data at rest, secure secrets, digital signatures
- **Limits**: 5500 req/sec (varies by region), use data key caching for high volume
- **vs CloudHSM**: KMS = managed, multi-tenant; CloudHSM = dedicated hardware, single-tenant, full control
- **Eliminate**: Need to export keys, need FIPS 140-2 Level 3 always (use CloudHSM)

### Secrets Manager
**Definition**: Securely store, retrieve, and manage secrets with automatic rotation

**Key Features**:
- **Secret Types**:
  - Database credentials (RDS, Aurora, Redshift, DocumentDB, etc.)
  - API keys and tokens
  - OAuth tokens
  - SSH keys
  - Arbitrary key-value pairs
  - Binary secrets (up to 64KB)
- **Automatic Rotation**:
  - Built-in rotation for RDS, Aurora, Redshift, DocumentDB
  - Rotation period: 1-365 days
  - Lambda function executes rotation (provided by AWS or custom)
  - Versioning: AWSCURRENT, AWSPENDING, AWSPREVIOUS stages
  - Automatic rollback if rotation fails
- **Encryption**:
  - Encryption at rest with KMS
  - Can use AWS managed key (free) or customer managed key
  - Encryption in transit (TLS)
- **Access Control**:
  - IAM policies for fine-grained access
  - Resource-based policies on secrets
  - VPC endpoints for private access
  - Tag-based access control
- **Cross-Region Replication**:
  - Replicate secrets to multiple regions
  - Read replicas for disaster recovery
  - Automatic sync when primary secret changes
  - Each replica encrypted with regional KMS key
- **Versioning**:
  - Automatic versioning on rotation
  - Staging labels: AWSCURRENT (active), AWSPENDING (rotating), AWSPREVIOUS (old)
  - Can retrieve specific versions
  - Version ID unique identifier
- **Integration**:
  - Native integration with RDS, Aurora, Redshift, DocumentDB, Neptune
  - Lambda for custom rotation logic
  - CloudFormation dynamic references
  - ECS task secrets injection
  - CodeBuild, CodePipeline for CI/CD secrets
  - Systems Manager Parameter Store integration
- **Monitoring & Audit**:
  - CloudTrail logs all API calls
  - CloudWatch metrics for API usage
  - EventBridge events for rotation success/failure
  - Automatic alerts via SNS
- **Pricing**: $0.40/secret/month + $0.05 per 10,000 API calls
- **Secret Retrieval Caching**: Use SDK caching to reduce API calls and costs

**Supported Integrations**:
- RDS, Aurora, Redshift, DocumentDB, Neptune (automatic rotation)
- Lambda (custom rotation, secret retrieval)
- ECS (inject secrets as environment variables)
- EKS (Secrets Store CSI Driver)
- CloudFormation (dynamic references)
- CodeBuild, CodePipeline, CodeDeploy
- Systems Manager Parameter Store, KMS
- CloudWatch, CloudTrail, EventBridge

**Exam Tips**:
- **Pick when**: Secret rotation needed, RDS password integration, compliance requirements, need automatic rotation for DB credentials
- **Secrets**: Database credentials, API keys, tokens, passwords
- **Rotation**: Automatic rotation for RDS, Redshift, DocumentDB; custom Lambda for others
- **Use cases**: Database credentials, API keys, application secrets, OAuth tokens
- **vs Parameter Store**: 
  - Secrets Manager = automatic rotation, RDS integration, higher cost ($0.40/secret/month)
  - Parameter Store = simpler, cheaper (free standard tier), no automatic rotation, manual updates
- **Best practice**: Use Secrets Manager for rotating secrets, Parameter Store for config values
- **Eliminate**: Simple config data (use Parameter Store), cost-sensitive non-rotating secrets

### Certificate Manager (ACM)
**Definition**: Provision, manage, and deploy SSL/TLS certificates for AWS services

**Key Features**:
- **Public Certificates**:
  - Free SSL/TLS certificates for AWS services
  - Domain validation (DV) certificates
  - Wildcard certificates (*.example.com)
  - Subject Alternative Names (SANs) - multiple domains in one cert
  - RSA 2048-bit or EC (P-256, P-384) keys
- **Certificate Validation**:
  - **DNS Validation**: Add CNAME record to DNS (recommended, automated renewal)
  - **Email Validation**: Email to domain registrant, admin@, administrator@, etc.
  - Validation status: Pending, Success, Failed
- **Automatic Renewal**:
  - ACM automatically renews certificates before expiration
  - Renewal attempt starts 60 days before expiry
  - Requires DNS validation records to remain in place
  - Email notification if renewal fails
  - No manual intervention required for DNS-validated certs
- **Private Certificate Authority (Private CA)**:
  - Create private CA hierarchy
  - Issue private certificates for internal resources
  - $400/month per private CA
  - Additional charges per private certificate issued
  - Use cases: Internal APIs, IoT devices, microservices
- **Certificate Import**:
  - Import third-party certificates
  - Must provide certificate, private key, certificate chain
  - Manual renewal required (no automatic renewal)
  - Can export imported certificates
- **Regional Service**:
  - Certificates are regional resources
  - Must request certificate in same region as resource
  - **Exception**: CloudFront requires certificates in us-east-1
  - Can have multiple certificates across regions
- **Integration**:
  - **ALB**: Multiple certificates per ALB (SNI support)
  - **NLB**: TLS termination with ACM certificates
  - **CloudFront**: Use certificates from us-east-1 region
  - **API Gateway**: Custom domain names with ACM certificates
  - **Elastic Beanstalk**: Automatic HTTPS configuration
  - **CloudFormation**: Reference ACM certificates in templates
- **Certificate Transparency Logging**:
  - All public certificates logged to CT logs
  - Cannot be disabled for public certificates
  - Helps detect mis-issuance
- **Monitoring**:
  - CloudWatch metrics (days to expiry)
  - EventBridge events for certificate status
  - CloudTrail for API activity
  - Expiration alerts via CloudWatch alarms
- **Certificate Pinning**: Not recommended with ACM (automatic rotation changes cert)

**Supported Integrations**:
- ALB, NLB, CloudFront, API Gateway
- Elastic Beanstalk, Cognito, App Runner
- Amplify, CloudFormation, Terraform
- CloudWatch, CloudTrail, EventBridge

**Exam Tips**:
- **Pick when**: Free SSL/TLS certificates for ALB, CloudFront, API Gateway, Elastic Beanstalk, need automatic renewal
- **Public certificates**: Free, automatic validation (DNS or email), auto-renewal
- **Private certificates**: For internal resources, requires AWS Private CA (additional cost)
- **Auto-renewal**: Automatic for certificates issued by ACM
- **Integration**: ALB, NLB, CloudFront, API Gateway, Elastic Beanstalk, Cognito
- **Regions**: Certificate must be in same region as resource (except CloudFront = us-east-1)
- **Use cases**: HTTPS for web applications, secure API endpoints, encrypted connections
- **Cannot**: Export private key for certificates issued by ACM (unless imported)
- **Eliminate**: Need to export certificate, need certificate for EC2 directly (use imported cert or ELB)

### WAF (Web Application Firewall)
**Definition**: Protect web applications from common web exploits

**Key Features**:
- Layer 7 (HTTP/HTTPS) protection
- Managed rules and custom rules
- Real-time metrics and logging
- Rate-based rules for DDoS mitigation
- IP reputation lists
- Integration with CloudFront, ALB, API Gateway

**Exam Tips**:
- **Pick when**: SQL injection protection, XSS protection, rate limiting, geo-blocking, bot mitigation, protect web apps from OWASP Top 10
- **Integration**: ALB, CloudFront, API Gateway, AppSync
- **Web ACLs**: Rules to allow, block, or count requests
- **Rule types**:
  - Managed rules (AWS Managed, AWS Marketplace)
  - Custom rules (IP sets, regex, size constraints, geo match, rate-based)
  - Rate-based rules (e.g., block IP exceeding 2000 req/5min)
- **Actions**: Allow, Block, Count (for testing)
- **Use cases**: Block SQL injection, prevent XSS, geo-restrictions, rate limiting, bot protection
- **AWS Managed Rules**: Pre-configured rule sets for common threats (CRS, Known Bad Inputs, SQL Injection, etc.)
- **Logging**: Send to CloudWatch Logs, S3, Kinesis Firehose
- **Eliminate**: Network layer attacks (use Shield), need host-based firewall

### Shield
**Definition**: Managed DDoS protection service

**Key Features**:
- Always-on detection and mitigation
- Protection against SYN floods, UDP floods, reflection attacks
- Integration with CloudFront, Route 53, ELB
- Real-time attack notifications

**Exam Tips**:
- **Shield Standard**: 
  - Free, automatic for all AWS customers
  - Protection for network and transport layer (L3/L4) attacks
  - Integration with CloudFront and Route 53
  - **Pick when**: Basic DDoS protection (always enabled)
- **Shield Advanced**: 
  - $3,000/month per organization
  - Enhanced protection for L3/L4/L7 attacks
  - 24/7 DDoS Response Team (DRT)
  - Cost protection (credits for scaling during attack)
  - Advanced attack analytics and reports
  - Integration with WAF (free WAF charges during attack)
  - **Pick when**: Critical apps needing advanced DDoS mitigation, financial protection from scaling costs, need DRT support
- **Protection resources**: CloudFront, Route 53, ELB, EC2 Elastic IPs, Global Accelerator
- **Use cases**: Protect against DDoS, ensure availability during attacks, gaming, e-commerce, financial services
- **vs WAF**: Shield for DDoS (L3/L4/L7), WAF for application attacks (L7 only)
- **Eliminate**: Budget-constrained (use Shield Standard + WAF), don't need DRT support

### GuardDuty
**Definition**: Intelligent threat detection service using machine learning

**Key Features**:
- Continuous monitoring for malicious activity
- Analyzes VPC Flow Logs, CloudTrail, DNS logs, EKS logs
- Machine learning and threat intelligence
- 30-day free trial
- No agents required

**Exam Tips**:
- **Pick when**: Threat detection, ML-based anomaly detection, identify compromised instances, detect unauthorized access, cryptocurrency mining detection
- **Analyzes**: 
  - CloudTrail events (unusual API calls, unauthorized deployments)
  - VPC Flow Logs (unusual network traffic)
  - DNS logs (C&C activity, DNS tunneling)
  - EKS audit logs (suspicious K8s activities)
- **Findings**: Prioritized security findings with severity levels
- **Actions**: EventBridge integration to trigger automated responses (Lambda, SNS)
- **Use cases**: Security monitoring, compliance, threat detection, anomaly detection, account compromise detection
- **Trusted IP lists**: Whitelist IPs that should be allowed
- **Threat lists**: Blacklist known malicious IPs
- **Multi-account**: Can be managed via AWS Organizations for centralized security
- **Eliminate**: Need to scan for vulnerabilities in code (use Inspector), content filtering

### Macie
**Definition**: Data security service using ML to discover and protect sensitive data in S3

**Key Features**:
- Automated sensitive data discovery
- Machine learning and pattern matching
- PII, PHI, financial data detection
- Data classification and inventory
- Security and access control monitoring

**Exam Tips**:
- **Pick when**: Discover/protect sensitive data in S3, find PII/PHI, data classification, compliance (GDPR, HIPAA), detect S3 security risks
- **Discovers**: PII (Personally Identifiable Information), financial data, credentials, health data
- **Capabilities**:
  - Automated sensitive data discovery jobs
  - Data classification (sensitive vs non-sensitive)
  - Inventory of S3 buckets
  - Security posture evaluation (encryption, public access, replication)
  - Findings with severity levels
- **Integration**: EventBridge for automated responses, Security Hub for centralized findings
- **Use cases**: Data compliance, find unencrypted sensitive data, detect overly permissive S3 buckets, data governance
- **Managed data identifiers**: Built-in patterns for 80+ sensitive data types
- **Custom data identifiers**: Regex patterns for proprietary data
- **Eliminate**: General security monitoring (use GuardDuty), non-S3 data scanning, EC2 scanning (use Inspector)

### Inspector
**Definition**: Automated vulnerability assessment service

**Key Features**:
- Automated security assessments
- Continuous scanning
- Network and host assessments
- CVE vulnerability detection
- Risk scoring and prioritization

**Exam Tips**:
- **Pick when**: Vulnerability assessment for EC2, ECR container images, Lambda functions, identify software vulnerabilities, network reachability analysis
- **Scans**:
  - **EC2**: Operating system and application vulnerabilities, network reachability
  - **ECR**: Container image vulnerabilities
  - **Lambda**: Package vulnerabilities in function code and layers
- **Agent-based**: SSM agent required for EC2 (pre-installed on many AMIs)
- **Findings**: CVE vulnerabilities with severity scores, remediation recommendations
- **Assessment types**:
  - Network reachability (network configuration issues)
  - Host assessments (vulnerable software, CVEs)
- **Integration**: Security Hub, EventBridge for automated responses
- **Use cases**: Security assessments, compliance, vulnerability management, container security, DevSecOps
- **Continuous**: Automatically re-scans when new CVEs are published
- **Eliminate**: Data classification (use Macie), threat detection (use GuardDuty), application code scanning (use CodeGuru)

### CloudHSM
**Definition**: Hardware Security Module for cryptographic key storage

**Key Features**:
- FIPS 140-2 Level 3 validated
- Single-tenant hardware security module
- You manage the keys
- Tamper-resistant hardware
- Deploy in cluster for HA across AZs

**Exam Tips**:
- **Pick when**: FIPS 140-2 Level 3 compliance required, dedicated hardware, full key control, regulatory requirements (government, finance), need to export keys
- **vs KMS**: 
  - CloudHSM = dedicated hardware, single-tenant, FIPS 140-2 Level 3, you manage keys, can export keys, higher cost
  - KMS = multi-tenant, AWS managed, FIPS 140-2 Level 2, AWS manages infrastructure, cannot export keys, lower cost
- **Use cases**: SSL/TLS offloading, Oracle TDE, storing private keys, digital rights management, PKI
- **Cluster**: Deploy across AZs for HA, automatic synchronization
- **Integration**: Custom applications via industry-standard APIs (PKCS#11, JCE, CNG)
- **Management**: You manage keys and users, AWS manages hardware
- **Eliminate**: Simple encryption needs (use KMS), need fully managed solution, cost-sensitive

### Directory Service
**Definition**: Managed Microsoft Active Directory in AWS cloud

**Key Features**:
- Microsoft AD compatibility
- Seamless domain join for Windows instances
- SSO to AWS applications
- MFA support
- Trust relationships with on-premises AD

**Exam Tips**:
- **AWS Managed Microsoft AD**: 
  - Full Active Directory features
  - Runs on Windows Server
  - Trust relationships with on-premises AD
  - Multi-AZ deployment for HA
  - **Pick when**: Need full AD features, Windows workloads, SSO, trust with on-prem AD, SharePoint, SQL Server
- **AD Connector**: 
  - Proxy/directory gateway to on-premises AD
  - Does not cache credentials
  - Users managed in on-premises AD
  - **Pick when**: Use existing on-prem AD, MFA via on-prem RADIUS, avoid data replication
- **Simple AD**: 
  - Standalone, Samba-based directory
  - Subset of AD features
  - Up to 500-5000 users
  - **Pick when**: Small workloads, basic LDAP, simple user directory, cost-sensitive, <5000 users
- **Use cases**: Windows applications, Linux domain join, AWS Management Console SSO, EC2 Windows domain join
- **Integration**: RDS for SQL Server, WorkSpaces, WorkDocs, QuickSight, Chime
- **Eliminate**: Non-Windows workloads without AD needs, simple authentication (use Cognito)

---

## 6. Application Integration

### SQS (Simple Queue Service)
**Definition**: Fully managed message queuing service for decoupling applications

**Key Features**:
- **Queue Types**:
  - **Standard Queue**:
    - Nearly unlimited throughput (unlimited TPS)
    - At-least-once delivery (duplicates possible)
    - Best-effort ordering (messages may arrive out of order)
    - Lower cost than FIFO
  - **FIFO Queue** (First-In-First-Out):
    - Exactly-once processing (no duplicates)
    - Strict ordering (preserved message order)
    - 300 messages/sec (3000 with batching, up to 10 messages per batch)
    - Queue name must end with .fifo
    - Message group ID for ordering within groups
    - Message deduplication ID (5-min deduplication interval)
- **Message Attributes**:
  - Message body: Up to 256KB of text (any format)
  - Message attributes: Custom metadata (up to 10 attributes)
  - System attributes: AWS-specific metadata
  - Extended Client Library: Use S3 for messages >256KB
- **Visibility Timeout**:
  - Duration message is invisible after being received (0s to 12 hours)
  - Default: 30 seconds
  - Consumer must delete message or it becomes visible again
  - ChangeMessageVisibility API to extend timeout
  - Pattern: Set timeout > max processing time
- **Message Retention**:
  - 1 minute to 14 days
  - Default: 4 days
  - Messages deleted after retention period expires
- **Delay Queues**:
  - Postpone delivery of new messages (0 to 15 minutes)
  - Default: 0 seconds
  - Applies to entire queue (Standard) or per-message (Message Timers)
- **Message Timers** (Standard only):
  - Per-message delay (0 to 15 minutes)
  - Overrides delay queue setting
  - Not available for FIFO queues
- **Dead-Letter Queues (DLQ)**:
  - Separate queue for messages that can't be processed
  - After maxReceiveCount attempts, message sent to DLQ
  - Use for debugging, forensic analysis
  - Must be same type (Standard DLQ for Standard queue, FIFO for FIFO)
  - Redrive policy: Define source queue and maxReceiveCount (1-1000)
  - Redrive allow policy: Control which queues can use this DLQ
- **Long Polling**:
  - Wait for messages to arrive (1 to 20 seconds)
  - Reduces empty responses, lowers cost, decreases API calls
  - Enabled by setting ReceiveMessageWaitTimeSeconds > 0
  - More efficient than short polling (default 0s)
- **Short Polling**:
  - Returns immediately (even if queue empty)
  - May not return all messages
  - Higher cost due to more API calls
- **Batch Operations**:
  - Send, receive, delete up to 10 messages per batch
  - Reduces cost and latency
  - FIFO: 3000 messages/sec with batching (vs 300 without)
- **Security**:
  - Encryption in transit (HTTPS)
  - Encryption at rest (KMS) - optional, can use AWS managed or customer managed keys
  - Access control via IAM policies and SQS policies
  - VPC Endpoints (Interface Endpoint) for private access
  - SQS Access Policy: Resource-based policy (like S3 bucket policy)
- **Monitoring**:
  - CloudWatch metrics: ApproximateNumberOfMessages, ApproximateAgeOfOldestMessage, etc.
  - CloudTrail for API logging
  - Can trigger alarms based on queue depth
- **Message Lifecycle**:
  1. Producer sends message
  2. Message stored redundantly across multiple AZs
  3. Consumer polls for messages (receives message)
  4. Message becomes invisible (visibility timeout starts)
  5. Consumer processes and deletes message
  6. If not deleted, message becomes visible again after timeout
- **Throughput**:
  - Standard: Unlimited transactions per second
  - FIFO: 300 TPS (3000 with batching)
  - Can request higher FIFO throughput (up to 30,000 TPS)

**Supported Integrations**:
- Lambda (event source trigger, async invocation)
- SNS (fan-out pattern: SNS → multiple SQS queues)
- S3 (event notifications via SNS → SQS)
- EC2, ECS, EKS (consumers), Auto Scaling (based on queue depth)
- EventBridge (send events to SQS)
- Step Functions (task integration)
- CloudWatch, CloudTrail, X-Ray
- API Gateway (via Lambda)

**Exam Tips**:
- **Pick when**: Decouple components, buffering, async processing, distribute workload, handle traffic spikes
- **Standard queues**: 
  - At-least-once delivery (duplicates possible)
  - Best-effort ordering
  - Nearly unlimited throughput
  - **Pick when**: High throughput needed, order not critical, can handle duplicates
- **FIFO queues**: 
  - Exactly-once processing
  - Strict message ordering
  - 300 msgs/sec (3000 with batching)
  - **Pick when**: Order matters, no duplicates allowed, transactions
- **Features**: 
  - **Visibility timeout**: Hide message while processing (0s-12hrs, default 30s)
  - **Dead-letter queues**: Handle failed messages after max receives
  - **Long polling**: Reduce empty responses, save costs, wait up to 20s
  - **Delay queues**: Postpone delivery up to 15 minutes
  - **Message timers**: Per-message delay (Standard only)
- **Message size**: Up to 256KB (use S3 for larger with Extended Client Library)
- **Use cases**: Microservices decoupling, job queues, order processing, buffering writes
- **Pattern**: Producer → SQS → Consumer (poll-based)
- **Eliminate**: Need push notifications (use SNS), pub/sub with multiple consumers (use SNS → SQS)

### SNS (Simple Notification Service)
**Definition**: Fully managed pub/sub messaging service for application-to-application and application-to-person notifications

**Key Features**:
- **Topic Types**:
  - **Standard Topics**:
    - Best-effort message ordering
    - At-least-once delivery
    - High throughput (unlimited)
    - Supports all subscription protocols
  - **FIFO Topics**:
    - Strict message ordering
    - Exactly-once message delivery
    - Up to 300 messages/sec (3000 with batching)
    - Must subscribe with FIFO SQS queues
    - Topic name must end with .fifo
    - Message deduplication and grouping
- **Subscription Protocols**:
  - **SQS**: Standard or FIFO queues
  - **Lambda**: Invoke functions with message
  - **HTTP/HTTPS**: Webhook endpoints
  - **Email**: Plain text email (email address)
  - **Email-JSON**: JSON-formatted email
  - **SMS**: Text messages to phone numbers
  - **Mobile Push**: iOS (APNS), Android (FCM), Amazon (ADM), etc.
  - **Kinesis Data Firehose**: Stream to data lakes
- **Message Structure**:
  - Message body: Up to 256KB
  - Message attributes: Metadata (up to 10 attributes)
  - Message ID: Unique identifier
  - Subject: Optional for email subscriptions
- **Message Filtering**:
  - Filter policies on subscriptions
  - JSON-based filtering on message attributes
  - Reduce unnecessary message deliveries
  - Each subscriber can have different filter
  - Supports: String matching, numeric matching, existence checking, prefix matching
- **Message Delivery**:
  - Push-based delivery
  - Delivery retry policy (configurable per protocol)
  - Dead-letter queue (DLQ) for failed deliveries
  - Delivery status logging (CloudWatch Logs)
- **Fan-Out Pattern**:
  - Publish once to SNS, deliver to multiple SQS queues
  - Each service gets independent queue
  - Parallel asynchronous processing
  - No message loss if one subscriber fails
  - Use case: Process same message differently by multiple services
- **Message Attributes**:
  - Custom metadata attached to message
  - Used for message filtering
  - Name-value pairs (String, Number, Binary, String.Array)
- **Access Control**:
  - IAM policies for topic access
  - Topic policies (resource-based)
  - Encryption at rest (KMS)
  - Encryption in transit (HTTPS)
  - VPC Endpoints for private access
- **Delivery Policies**:
  - Retry attempts and backoff
  - Protocol-specific (HTTP, SQS, Lambda, etc.)
  - Configurable per subscription
  - Default: 3 retries with exponential backoff
- **Message Encryption**:
  - Server-side encryption with KMS
  - At rest and in transit
  - Can use AWS managed or customer managed keys
- **Large Message Handling**:
  - Extended Client Library for messages >256KB
  - Store payload in S3, send reference via SNS
  - Similar to SQS Extended Client Library
- **Mobile Push Notifications**:
  - Platform endpoints (device tokens)
  - Platform applications (iOS, Android, etc.)
  - Direct publish to mobile devices
  - Supports APNS, FCM, ADM, Baidu, WNS, MPNS
- **Monitoring**:
  - CloudWatch metrics (NumberOfMessages, NumberOfNotifications)
  - Delivery status for SMS
  - Failed delivery tracking
  - CloudTrail for API logging

**Supported Integrations**:
- Lambda, SQS (fan-out pattern), Kinesis Data Firehose
- EventBridge, CloudWatch Alarms, S3 (event notifications)
- EC2 Auto Scaling, RDS (event notifications)
- CloudFormation, CodePipeline, CodeCommit
- Mobile apps (iOS, Android), Email, SMS
- HTTP/HTTPS webhooks, third-party services

**Exam Tips**:
- **Pick when**: Pub/sub pattern, fan-out to multiple subscribers, push notifications, broadcast messages, alerts/alarms
- **Subscribers**: SQS, Lambda, HTTP/HTTPS endpoints, email, SMS, mobile push notifications
- **Standard topics**: 
  - Best-effort ordering
  - At-least-once delivery
  - High throughput
- **FIFO topics**: 
  - Strict ordering
  - Exactly-once delivery
  - Must subscribe with FIFO SQS queues
  - 300 msgs/sec (3000 with batching)
- **Message filtering**: Subscribers receive only messages they want based on filter policies
- **Fan-out pattern**: SNS → multiple SQS queues (each service gets its own queue)
  - **Pick when**: Send same message to multiple systems, decouple producer from consumers
- **Use cases**: Application alerts, push notifications, mobile apps, email notifications, fan-out to multiple services
- **vs SQS**: SNS = push (pub/sub), SQS = pull (queue); often used together (SNS → SQS)
- **Eliminate**: Need queuing (use SQS), single consumer (use SQS directly), need message persistence beyond delivery

### EventBridge (CloudWatch Events)
**Definition**: Serverless event bus for building event-driven applications

**Key Features**:
- **Event Buses**:
  - **Default Event Bus**: Receives events from AWS services
  - **Custom Event Buses**: For your applications or third-party SaaS
  - **Partner Event Buses**: Pre-configured for SaaS partners (Zendesk, Datadog, Auth0, etc.)
  - Cross-account event delivery
  - Archive and replay capability
- **Event Sources**:
  - AWS services (90+ services: EC2, S3, CloudTrail, RDS, ECS, etc.)
  - SaaS partners (Zendesk, Datadog, PagerDuty, Auth0, Shopify, etc.)
  - Custom applications (PutEvents API)
  - Schedule expressions (cron or rate)
- **Event Targets** (20+ targets):
  - Lambda functions, Step Functions, SQS, SNS, Kinesis Data Streams, Kinesis Data Firehose
  - ECS tasks, Batch jobs, CodePipeline, CodeBuild
  - Systems Manager Run Command, Automation
  - API Gateway, API destinations (HTTP endpoints)
  - EC2 actions (start, stop, terminate, reboot)
  - CloudWatch Log Groups, EventBridge in another account/region
- **Event Rules**:
  - Event pattern: Match JSON structure of events (content-based filtering)
  - Schedule: Cron or rate expressions (cron(0 12 * * ? *) or rate(5 minutes))
  - Multiple targets per rule (up to 5)
  - Input transformer to customize event sent to target
  - Dead-letter queue for failed invocations
- **Event Patterns** (Content-Based Filtering):
  - Match on event source, detail-type, account, region, resources
  - Exact match, prefix match, suffix match, anything-but, exists, numeric matching
  - Complex boolean logic (AND, OR)
  - More advanced than SNS filtering
- **Schema Registry**:
  - Discover event schemas automatically
  - Generate code bindings for Java, Python, TypeScript
  - Version management for schemas
  - OpenAPI schema format
  - IDE integration for development
- **Archive and Replay**:
  - Archive events for compliance or testing
  - Replay archived events for debugging/recovery
  - Configure retention period (indefinite or specific duration)
  - Filter events to archive
  - Useful for bug fixes, testing, disaster recovery
- **API Destinations**:
  - Send events to external HTTP endpoints
  - OAuth, API key, or basic authentication
  - Retry with exponential backoff
  - Invocation rate limiting
  - Use case: Integrate with third-party services outside AWS
- **Input Transformation**:
  - Modify event JSON before sending to target
  - Extract specific fields
  - Add static values
  - Use JSONPath expressions
- **Cross-Account Events**:
  - Send/receive events across AWS accounts
  - Requires event bus policy and rule in target account
  - Use case: Centralized event management, multi-account architectures
- **Dead-Letter Queue (DLQ)**:
  - SQS queue for failed target invocations
  - Configure per rule
  - Helps with debugging and retry logic
- **Scheduling**:
  - Cron expressions: Specific times (e.g., 9 AM every Monday)
  - Rate expressions: Regular intervals (e.g., every 5 minutes)
  - More flexible than CloudWatch Events
  - Can trigger any supported target
- **Event Throttling**:
  - PutEvents API: 2400 requests/sec (soft limit)
  - Can request increase
  - Batch up to 10 events per request
- **Multi-Region Events**:
  - Global endpoints for HA (automatic failover)
  - Custom event buses only
  - Secondary region processes events if primary fails

**Supported Integrations**:
- All major AWS services (EC2, S3, RDS, Lambda, ECS, etc.)
- SaaS partners (90+ partners including Zendesk, Datadog, MongoDB, New Relic)
- Step Functions, CodePipeline, SNS, SQS, Kinesis
- Systems Manager, CloudWatch Logs, API Gateway
- Third-party HTTP endpoints (API Destinations)

**Exam Tips**:
- **Pick when**: Event bus for event-driven architecture, SaaS integrations, rule-based routing, scheduled events (cron), complex event filtering
- **Event sources**: 
  - AWS services (EC2, S3, CloudTrail, etc.)
  - SaaS partners (Zendesk, Datadog, Auth0, etc.)
  - Custom applications
- **Targets**: Lambda, Step Functions, SQS, SNS, Kinesis, ECS tasks, Batch jobs, SSM, API Gateway, etc.
- **Rules**: Pattern matching on event content, schedule expressions
- **Event buses**: 
  - Default (AWS services)
  - Partner event buses (SaaS)
  - Custom event buses
- **Features**:
  - Content filtering (filter on event attributes)
  - Input transformation
  - Archive and replay for testing
  - Schema registry for event discovery
- **vs SNS**: 
  - EventBridge = advanced filtering/routing/archive, SaaS integrations, 100+ AWS targets
  - SNS = simple pub/sub, higher throughput, mobile push
- **Use cases**: Serverless workflows, security automation, multi-account event routing, scheduled tasks
- **Eliminate**: Simple pub/sub (use SNS), need microsecond latency, very high throughput (millions/sec) 
  - EventBridge = advanced filtering/routing/archive, SaaS integrations, 100+ AWS targets
  - SNS = simple pub/sub, higher throughput, mobile push
- **Use cases**: Serverless workflows, security automation, multi-account event routing, scheduled tasks
- **Eliminate**: Simple pub/sub (use SNS), need microsecond latency, very high throughput (millions/sec)

### Step Functions
**Definition**: Serverless orchestration service for coordinating distributed applications and microservices using visual workflows

**Key Features**:
- **Workflow Types**:
  - **Standard Workflows**:
    - Long-running (up to 1 year / 365 days)
    - Exactly-once execution
    - At-most-once state transitions
    - Full execution history (90 days)
    - 2000 executions/sec (soft limit)
    - Priced per state transition
    - **Pick when**: Auditing needed, long-running processes, need execution history, human approval workflows
  - **Express Workflows**:
    - Short-duration (up to 5 minutes)
    - At-least-once execution
    - High-volume (100,000+ executions/sec)
    - Minimal execution history (CloudWatch Logs)
    - 5x cheaper than Standard
    - Two sub-types:
      - **Synchronous**: Wait for workflow completion, return result (up to 5 min)
      - **Asynchronous**: Fire-and-forget, doesn't wait for completion
    - **Pick when**: High-volume event processing, IoT data processing, streaming data transformation, mobile backends
- **State Types**:
  - **Task**: Single unit of work (Lambda, ECS, Batch, SNS, SQS, DynamoDB, etc.)
  - **Choice**: Branch based on input/output values (if-then-else logic)
  - **Parallel**: Execute multiple branches in parallel, wait for all to complete
  - **Wait**: Delay execution (seconds, timestamp, timestamp path, seconds path)
  - **Map**: Dynamically iterate over array of items, process in parallel (max 40 iterations)
  - **Succeed**: Successful termination
  - **Fail**: Failed termination with error and cause
  - **Pass**: Pass input to output, no work performed, can transform data
- **Service Integrations** (200+ AWS services):
  - **Optimized integrations**: Lambda, ECS, Fargate, Batch, DynamoDB, SNS, SQS, Glue, SageMaker, EMR, EventBridge, Step Functions (nested), API Gateway, Athena
  - **SDK integrations**: Any AWS service via AWS SDK
  - **Request/Response**: Synchronous, wait for result
  - **Run a Job**: Async, wait for job completion (.sync pattern)
  - **Wait for Callback**: Pause until callback with task token (.waitForTaskToken)
- **Error Handling**:
  - **Retry**: Automatic retry with exponential backoff
    - Configure: ErrorEquals, IntervalSeconds, MaxAttempts, BackoffRate
    - Default retry for Lambda: 2 retries with 2x backoff
  - **Catch**: Handle errors and transition to another state
    - Define fallback state for specific error types
    - Can catch all errors or specific errors
  - **Timeout**: Max duration for state execution
  - **Heartbeat**: Periodic check for long-running tasks
  - **Error Types**: States.ALL, States.Timeout, States.TaskFailed, States.Permissions, custom errors
- **Input/Output Processing**:
  - **InputPath**: Filter input before state execution
  - **OutputPath**: Filter output after state execution
  - **ResultPath**: Where to place task result in output
  - **Parameters**: Construct input for state from workflow data
  - **ResultSelector**: Process service output before sending to next state
- **Execution Features**:
  - Visual workflow designer in console
  - Execution history with step-by-step details
  - Execution ARN for tracking
  - Start execution via API, EventBridge, Lambda, SDK
  - Pass input data (JSON, up to 256KB)
- **State Machine Definition**:
  - JSON-based Amazon States Language (ASL)
  - Version control via definition updates
  - Can import/export definitions
  - Validate syntax in console
- **Monitoring & Logging**:
  - CloudWatch metrics (ExecutionsFailed, ExecutionsSucceeded, ExecutionTime, etc.)
  - CloudWatch Logs integration (Express workflows)
  - X-Ray tracing for debugging
  - EventBridge events for execution status changes
  - Step Functions API for describing executions
- **Activity Tasks**:
  - Long-running worker tasks hosted anywhere
  - Workers poll for work using GetActivityTask
  - Send heartbeat, report success/failure
  - Use case: Workers on EC2, ECS, on-premises
- **Nested Workflows**:
  - Start Step Functions workflow from another workflow
  - Modular, reusable workflows
  - Synchronous (.sync) or fire-and-forget
- **Standard Connectors**:
  - Direct service integration without Lambda
  - Example: DynamoDB PutItem, SQS SendMessage, ECS RunTask
  - Reduces cost and latency
- **Map State (Distributed Map)**:
  - Process large-scale parallel workloads
  - S3 inventory as input (millions of items)
  - Up to 10,000 parallel executions
  - Standard workflows only

**Supported Integrations**:
- Lambda, ECS, Fargate, Batch, Glue, SageMaker, EMR
- DynamoDB, SNS, SQS, EventBridge, Kinesis
- API Gateway, Athena, CodeBuild, Systems Manager
- Step Functions (nested workflows)
- 200+ AWS services via SDK integration

**Exam Tips**:
- **Pick when**: Orchestrate serverless workflows, coordinate multiple Lambda functions, state machines, complex business logic, error handling/retry logic
- **Workflow types**:
  - **Standard**: 
    - Long-running (up to 1 year)
    - Exactly-once execution
    - Full execution history
    - **Pick when**: Auditing needed, long-running processes, need execution history
  - **Express**: 
    - Short-duration (up to 5 minutes)
    - At-least-once execution
    - High-volume (100k+ executions/sec)
    - **Pick when**: High-volume, event processing, IoT data processing, streaming data transformation
- **States**: Task, Choice, Parallel, Wait, Map, Succeed, Fail, Pass
- **Integrations**: Lambda, ECS, Fargate, DynamoDB, SNS, SQS, Batch, Glue, SageMaker, EMR, API Gateway
- **Error handling**: Retry, Catch, timeout, heartbeat
- **Use cases**: ETL workflows, order processing, video processing, microservices orchestration, human approval workflows
- **vs SWF**: Step Functions = newer, simpler, serverless; SWF = legacy, external workers
- **Eliminate**: Simple sequential Lambda (use Lambda destinations), single task
- **Integrations**: Lambda, ECS, Fargate, DynamoDB, SNS, SQS, Batch, Glue, SageMaker, EMR, API Gateway
- **Error handling**: Retry, Catch, timeout, heartbeat
- **Use cases**: ETL workflows, order processing, video processing, microservices orchestration, human approval workflows
- **vs SWF**: Step Functions = newer, simpler, serverless; SWF = legacy, external workers
- **Eliminate**: Simple sequential Lambda (use Lambda destinations), single task

### AppSync
**Definition**: Managed GraphQL service for building scalable APIs

**Key Features**:
- GraphQL and REST API support
- Real-time subscriptions via WebSocket
- Offline data synchronization
- Fine-grained authorization
- Resolver pipelines
- Integration with DynamoDB, Lambda, RDS, HTTP, OpenSearch

**Exam Tips**:
- **Pick when**: GraphQL APIs, real-time subscriptions, offline sync, mobile/web apps, need flexible data querying, consolidate multiple data sources
- **Data sources**: DynamoDB, Lambda, RDS Aurora, HTTP APIs, OpenSearch
- **Real-time**: WebSocket-based subscriptions for real-time updates
- **Offline**: Automatic conflict resolution, local data caching
- **Security**: API keys, IAM, Cognito User Pools, OIDC, Lambda authorizers
- **Resolvers**: VTL templates or JavaScript to fetch data from data sources
- **Use cases**: Mobile apps, real-time collaborative apps, social media feeds, chat applications, dashboards
- **vs API Gateway**: AppSync = GraphQL, real-time, offline sync; API Gateway = REST/HTTP, broader integrations
- **Eliminate**: Simple REST APIs (use API Gateway), no real-time needs, not using GraphQL

### Amazon MQ
**Definition**: Managed message broker service for Apache ActiveMQ and RabbitMQ

**Key Features**:
- Managed ActiveMQ and RabbitMQ
- Support for industry-standard protocols (JMS, AMQP, MQTT, STOMP, WebSocket)
- High availability with active/standby or clustered deployments
- Automatic failover
- Encryption and authentication

**Exam Tips**:
- **Pick when**: Managed message broker (ActiveMQ, RabbitMQ), lift-and-shift legacy apps using JMS/AMQP, need protocol compatibility, migrate from on-premises message brokers
- **Protocols**: JMS, NMS, AMQP 0.9.1, AMQP 1.0, MQTT, STOMP, WebSocket
- **Engines**: 
  - ActiveMQ (classic, single-instance or HA pair)
  - RabbitMQ (cluster for HA and scalability)
- **Use cases**: Migrate existing message broker, legacy apps requiring JMS, microservices with complex routing
- **High availability**: Active/standby (ActiveMQ) or cluster (RabbitMQ)
- **vs SQS/SNS**: 
  - Amazon MQ = when need protocol compatibility, migrating existing apps
  - SQS/SNS = cloud-native, serverless, better AWS integration, simpler
- **Eliminate**: New cloud-native apps (use SQS/SNS), serverless requirements, need unlimited scaling

---

## 7. Analytics

### Kinesis Data Streams
**Definition**: Real-time streaming data service for collecting and processing large streams of data records in real time

**Key Features**:
- **Shards** (Throughput Units):
  - Basic capacity unit of stream
  - **Write**: 1 MB/sec or 1,000 records/sec per shard
  - **Read**: 2 MB/sec per shard (shared among all consumers with standard, dedicated with enhanced fan-out)
  - **Shard count**: Manually provisioned (can auto-scale via API/Lambda/Application Auto Scaling)
  - **Shard splitting**: Increase capacity by splitting shards
  - **Shard merging**: Reduce capacity and cost by merging shards
  - **Pricing**: $0.015 per shard-hour + $0.014 per 1M PUT payload units
- **Data Retention**:
  - Default: 24 hours
  - Can increase to 365 days (8760 hours)
  - Replay capability (reprocess data within retention period)
  - Long-term retention for compliance, debugging, reprocessing
- **Data Records**:
  - Partition key (determines shard, max 256 bytes)
  - Sequence number (unique per partition key, assigned by Kinesis)
  - Data blob (max 1 MB per record)
  - Records immutable once written
- **Ordering**:
  - Guaranteed ordering within shard (via partition key)
  - Same partition key → same shard → ordered
  - Different shards → no ordering guarantee across shards
  - Use partition key strategically (user ID, device ID, etc.)
- **Consumers**:
  - **Shared Throughput (Standard)**:
    - 2 MB/sec read per shard (shared among all consumers)
    - Max 5 consumers per shard with GetRecords
    - Pull model (consumers poll using GetRecords)
    - 200ms propagation delay
  - **Enhanced Fan-Out**:
    - Dedicated 2 MB/sec read per consumer per shard
    - Unlimited consumers (no 5 consumer limit)
    - Push model (Kinesis pushes to consumers via HTTP/2)
    - 70ms propagation delay (faster)
    - Higher cost ($0.015 per consumer-shard-hour + $0.013 per GB)
    - **Pick when**: Multiple consumers need full throughput, need low latency
- **Consumer Types**:
  - AWS Lambda (event source mapping, batch processing)
  - Kinesis Data Analytics (SQL/Flink on streaming data)
  - Kinesis Data Firehose (load to S3/Redshift/OpenSearch)
  - EC2, ECS, EKS (custom apps with KCL)
  - EMR (big data processing)
- **Kinesis Client Library (KCL)**:
  - Simplifies consumer development
  - Handles load balancing across consumers
  - Checkpointing with DynamoDB
  - Automatic shard discovery and assignment
  - Supports Java, Python, Node.js, .NET, Ruby
- **Producer Types**:
  - **Kinesis Producer Library (KPL)**: High-performance, batching, compression, retries (C++, Java)
  - **Kinesis Agent**: Log file collection from servers
  - **AWS SDK**: PutRecord (single), PutRecords (batch up to 500 records or 5 MB)
  - **Kinesis Data Streams API**: Direct API calls
  - **CloudWatch Logs**: Stream logs to Kinesis
  - **IoT Core**: Device data to Kinesis
- **Capacity Modes**:
  - **Provisioned Mode**: 
    - Manually provision shards
    - Pay per shard-hour
    - Can scale up/down via API
    - **Pick when**: Predictable traffic, cost optimization
  - **On-Demand Mode**:
    - Automatic scaling (4 MB/sec write, 8 MB/sec read default)
    - Pay per GB ingested and retrieved
    - No shard management
    - **Pick when**: Unpredictable traffic, spiky workloads, less management
- **Security**:
  - Encryption at rest (KMS, optional)
  - Encryption in transit (TLS/HTTPS)
  - IAM for access control
  - VPC endpoints (Interface Endpoint) for private access
  - CloudWatch for monitoring
  - CloudTrail for API auditing
- **Monitoring**:
  - CloudWatch metrics: IncomingBytes, IncomingRecords, WriteProvisionedThroughputExceeded, ReadProvisionedThroughputExceeded, GetRecords.IteratorAgeMilliseconds, etc.
  - Shard-level metrics (detailed monitoring)
  - Consumer-level metrics (enhanced fan-out)
- **Error Handling**:
  - ProvisionedThroughputExceededException: Exceed shard limits (retry with exponential backoff or add shards)
  - Use batching to optimize throughput
  - Use random partition keys to distribute load evenly
- **Data Processing Patterns**:
  - Fan-out: Multiple consumers process same stream
  - Aggregation: Combine multiple streams
  - Windowing: Process data in time windows
  - Real-time analytics: Immediate insights from streaming data

**Supported Integrations**:
- Lambda (event source), Kinesis Data Analytics (SQL/Flink)
- Kinesis Data Firehose (deliver to S3/Redshift/OpenSearch)
- EC2, ECS, EKS (KCL consumers)
- EMR (big data processing), Spark Streaming
- CloudWatch Logs (log streaming), IoT Core
- AWS Glue, SageMaker, EventBridge

**Exam Tips**:
- **Pick when**: Real-time streaming data collection/processing (<1s latency), custom consumers, need replay capability, clickstream analysis, log aggregation, IoT telemetry, real-time analytics
- **Shards**: 
  - Write: 1 MB/sec or 1,000 records/sec per shard
  - Read: 2 MB/sec per shard (shared) or 2 MB/sec per consumer (enhanced fan-out)
  - Manually provisioned (or On-Demand for auto-scaling)
  - Pay per shard-hour
- **Retention**: 24 hours default, up to 365 days (replay data within retention)
- **Ordering**: Guaranteed within shard (use same partition key for related records)
- **Consumers**: Lambda, Kinesis Data Analytics, EC2/ECS (KCL), Firehose, EMR
- **Enhanced fan-out**: Dedicated 2 MB/sec per consumer, unlimited consumers, push model, 70ms latency
  - **Pick when**: Multiple consumers need full throughput, low latency required
- **Capacity modes**:
  - **Provisioned**: Manual shard management, predictable cost
  - **On-Demand**: Auto-scaling, unpredictable traffic, no management
- **Use cases**: Real-time analytics, log/event ingestion, clickstream analysis, IoT data processing, gaming leaderboards, fraud detection, anomaly detection
- **vs Firehose**: 
  - Data Streams = real-time (<1s), custom processing, replay, manual/on-demand shards
  - Firehose = near real-time (60s+), direct to destinations, fully managed, no replay
- **vs SQS**: 
  - Kinesis = ordered streaming, multiple consumers, replay, real-time
  - SQS = message queue, single consumer (deleted after read), no ordering (except FIFO), no replay
- **Eliminate**: Batch processing (use Batch/EMR), need fully managed loading to S3/Redshift (use Firehose), simple messaging (use SQS)

### Kinesis Data Firehose
**Definition**: Fully managed service for loading streaming data into data stores and analytics tools with near real-time delivery

**Key Features**:
- **Fully Managed & Serverless**:
  - No shard management or capacity planning
  - Automatic scaling based on data throughput
  - Pay only for data ingested (per GB)
  - No minimum fees or upfront costs
- **Delivery Destinations**:
  - **AWS Destinations**:
    - **S3**: Direct delivery (most common), supports compression and partitioning
    - **Redshift**: Via S3 (COPY command from S3 to Redshift)
    - **OpenSearch Service/OpenSearch Serverless**: Direct delivery with index rotation
    - **Splunk**: Direct delivery to Splunk HEC endpoint
  - **Third-Party Destinations**:
    - Datadog, MongoDB, New Relic, Dynatrace, Sumo Logic, LogicMonitor, Coralogix
  - **HTTP Endpoints**: Custom HTTP endpoints (webhooks, APIs)
  - **Generic HTTP Endpoint**: Any HTTPS endpoint with authentication
- **Buffering**:
  - **Buffer Size**: 1 MB to 128 MB (whichever comes first triggers delivery)
  - **Buffer Interval**: 60 seconds to 900 seconds (15 minutes)
  - Lower buffer = more frequent delivery = higher cost
  - Pattern: Choose based on latency requirements vs cost
  - Near real-time: Minimum 60 seconds latency
- **Data Transformation**:
  - **Lambda Transformation**: Transform records before delivery (max 3 retries)
  - Use cases: Format conversion, enrichment, filtering, aggregation
  - Can add metadata, parse logs, compress, encrypt
  - Failed records sent to S3 error bucket
- **Data Format Conversion**:
  - Convert JSON to Parquet or ORC (columnar formats)
  - Requires Glue Data Catalog for schema
  - Reduces storage costs and improves query performance in Athena/Redshift Spectrum
  - Automatic schema evolution detection
- **Compression**:
  - Supports GZIP, Snappy, Zip, Hadoop-compatible Snappy
  - S3 destination: GZIP, Snappy, Zip (not for Redshift)
  - Redshift destination: GZIP only
  - Reduces storage costs
- **Encryption**:
  - At rest: S3 with SSE-S3 or SSE-KMS
  - In transit: HTTPS/TLS
  - Source encryption: Can receive encrypted data from Kinesis Data Streams
- **Source Types**:
  - **Direct PUT**: Application calls Firehose PutRecord/PutRecordBatch API
  - **Kinesis Data Streams**: Read from Data Streams (common pattern)
  - **CloudWatch Logs**: Stream log groups to Firehose
  - **AWS IoT**: IoT device data
  - **CloudWatch Events/EventBridge**: Event data
  - **Kinesis Agent**: Server log files
- **Backup & Error Handling**:
  - All source records can be backed up to S3 (optional)
  - Failed data (transformation failures, delivery failures) automatically sent to S3 error bucket
  - Retry duration: 0-7200 seconds (2 hours)
  - After retries, data sent to error bucket with error details
- **Delivery Patterns**:
  - **S3**: Most common, raw or transformed data, compressed, partitioned by time
  - **Redshift**: S3 → COPY command → Redshift (automatic)
  - **OpenSearch**: Direct indexing with index rotation (daily, weekly, monthly)
  - **Splunk**: HEC (HTTP Event Collector) endpoint
- **S3 Prefix & Partitioning**:
  - Dynamic partitioning based on keys in records (year/month/day/hour)
  - Custom prefixes with inline parsing or Lambda
  - Pattern: `bucket/year=2026/month=01/day=09/hour=14/`
  - Optimizes Athena queries and S3 organization
- **Monitoring & Logging**:
  - CloudWatch metrics: IncomingBytes, IncomingRecords, DeliveryToS3.Success, DeliveryToS3.DataFreshness
  - CloudWatch Logs for delivery errors
  - Data delivery failure captured with error details
  - CloudTrail for API auditing
- **Throughput & Limits**:
  - No explicit throughput limit (auto-scales)
  - PutRecord: Single record (max 1000 KB)
  - PutRecordBatch: Batch of up to 500 records or 4 MB (whichever is smaller)
  - 5000 records/sec per delivery stream (soft limit, can increase)
- **VPC Support**:
  - Can deliver to VPC resources via private connectivity
  - Requires VPC endpoint for S3, Redshift, or other VPC-based destinations
- **MSK Integration**:
  - Read from Amazon MSK (Managed Streaming for Kafka)
  - Consume Kafka topics and deliver to destinations

**Supported Integrations**:
- **Sources**: Kinesis Data Streams, CloudWatch Logs, IoT Core, EventBridge, Kinesis Agent, Direct PUT API
- **Destinations**: S3, Redshift, OpenSearch, Splunk, Datadog, MongoDB, New Relic, HTTP endpoints
- **Transformation**: Lambda, Glue Data Catalog (schema)
- **Monitoring**: CloudWatch, CloudTrail
- **Storage**: S3 (backup, errors, destination)

**Exam Tips**:
- **Pick when**: Load streaming data to S3, Redshift, OpenSearch, Splunk, HTTP endpoints; near real-time delivery (NOT sub-second); no custom processing; fully managed; no shard management
- **Fully managed**: No shards, automatic scaling, serverless
- **Destinations**: 
  - **AWS**: S3 (most common), Redshift (via S3), OpenSearch, Splunk
  - **Third-party**: Datadog, MongoDB, New Relic, Sumo Logic
  - **HTTP**: Custom endpoints
- **Buffering**: 
  - 60-900 seconds OR 1-128 MB (whichever comes first)
  - Minimum 60 seconds latency (near real-time, NOT real-time)
  - Lower buffer = more frequent = higher cost
- **Transformation**: Lambda function to transform records (format, enrich, filter, aggregate)
- **Format conversion**: JSON → Parquet/ORC (requires Glue Data Catalog), reduces costs, faster queries
- **Compression**: GZIP, Snappy, Zip (S3), GZIP only (Redshift)
- **Source patterns**:
  - **Direct PUT**: Apps call Firehose API
  - **Kinesis Data Streams → Firehose**: Process in Data Streams, then load to destination
  - **CloudWatch Logs → Firehose**: Stream logs to S3/OpenSearch
- **Use cases**: ETL to data lake (S3), log/event delivery, real-time analytics ingestion, stream to Redshift/OpenSearch, backup streaming data
- **vs Data Streams**: 
  - **Firehose**: Near real-time (60s+), fully managed, direct to destinations, no replay, no shards, simpler
  - **Data Streams**: Real-time (<1s), custom consumers, replay capability, manual/on-demand shards, more control
- **Pattern**: Data Streams → Firehose → S3/Redshift (complex processing in Data Streams, then load to storage)
- **Eliminate**: Need sub-second latency (use Data Streams), custom real-time processing (use Data Streams + Lambda/Analytics), need to replay data (use Data Streams)

### Kinesis Data Analytics
**Definition**: Real-time analytics on streaming data using SQL or Apache Flink

**Key Features**:
- SQL queries on streaming data
- Apache Flink for advanced processing
- Serverless, auto-scaling
- Real-time dashboards and alerts
- Input from Kinesis Data Streams or Firehose

**Exam Tips**:
- **Pick when**: SQL queries on streaming data, real-time analytics, transform/aggregate streaming data, time-series analytics, real-time dashboards
- **Input sources**: Kinesis Data Streams, Kinesis Data Firehose
- **Output destinations**: Kinesis Data Streams, Firehose, Lambda
- **Two runtime options**:
  - SQL (simpler, standard SQL queries)
  - Apache Flink (advanced Java/Scala, complex stateful processing)
- **Use cases**: Real-time metrics, streaming ETL, real-time leaderboards, anomaly detection, clickstream analysis
- **Pre-built functions**: Aggregate, filter, window functions, anomaly detection
- **Eliminate**: Batch analytics (use Athena, Redshift, EMR), non-streaming data

### MSK (Managed Streaming for Apache Kafka)
**Definition**: Fully managed Apache Kafka service

**Key Features**:
- Fully managed Kafka clusters
- Multi-AZ deployment
- Automatic patching and updates
- MSK Serverless option
- Integration with AWS services

**Exam Tips**:
- **Pick when**: Apache Kafka required, existing Kafka workloads, migrate from self-managed Kafka, need Kafka ecosystem tools (Kafka Connect, Kafka Streams)
- **Deployment**: 
  - MSK Provisioned (choose instance types, manual scaling)
  - MSK Serverless (auto-scaling, pay per throughput)
- **Features**: Multi-AZ for HA, automatic recovery, monitoring via CloudWatch, encryption in transit and at rest
- **Integration**: Kinesis Data Analytics, Lambda, Glue, S3 (via Kafka Connect)
- **Use cases**: Real-time streaming, event-driven architectures, log aggregation, migrate existing Kafka apps
- **vs Kinesis Data Streams**: 
  - MSK = when need Kafka specifically, Kafka ecosystem, more control
  - Kinesis = simpler, tighter AWS integration, less management
- **Eliminate**: No Kafka requirement (use Kinesis), simple messaging (use SQS/SNS)

### Athena
**Definition**: Serverless interactive query service to analyze data in S3 using SQL

**Key Features**:
- Serverless, no infrastructure
- Standard SQL (Presto-based)
- Pay per query (data scanned)
- Query results stored in S3
- Integration with Glue Data Catalog

**Exam Tips**:
- **Pick when**: Serverless SQL queries on S3, ad-hoc analysis, pay per query, query logs/CloudTrail, data lake analytics, infrequent queries
- **Data formats**: CSV, JSON, Parquet, ORC, Avro, etc.
- **Integration**: Glue Data Catalog for schema metadata, QuickSight for visualization
- **Cost optimization**: 
  - Use columnar formats (Parquet, ORC) for 30-90% cost savings
  - Partition data
  - Compress data
- **Federated Query**: Query data in relational, non-relational, object, and custom sources (RDS, DynamoDB, Redshift)
- **Use cases**: Log analysis, business intelligence, query S3 data lake, CloudTrail analysis, VPC Flow Logs analysis
- **No ETL needed**: Query data directly in S3
- **vs Redshift**: Athena = serverless, pay per query, infrequent queries; Redshift = provisioned, predictable workload, frequent complex queries
- **Eliminate**: Real-time queries, high-frequency queries (use Redshift), need data warehouse features

### AWS Glue
**Definition**: Fully managed ETL (Extract, Transform, Load) service

**Key Features**:
- Serverless ETL
- Data catalog with crawler
- Automatic schema discovery
- Job scheduling
- Apache Spark and Python shell jobs
- Visual ETL designer (Glue Studio)

**Exam Tips**:
- **Pick when**: ETL workloads, data catalog, crawlers for schema discovery, serverless Spark jobs, prepare data for analytics
- **Components**:
  - **Data Catalog**: Centralized metadata repository, schema versioning
  - **Crawlers**: Auto-discover schemas by scanning data sources
  - **ETL Jobs**: Serverless Spark/Python for data transformation
  - **Glue Studio**: Visual ETL job designer
  - **DataBrew**: Visual data preparation tool
- **Data sources**: S3, RDS, Redshift, DynamoDB, on-premises databases (via JDBC)
- **Glue Job types**: Spark (PySpark/Scala), Python Shell, Streaming ETL
- **Integration**: Athena, Redshift Spectrum, EMR, QuickSight use Glue Data Catalog
- **Use cases**: ETL pipelines, prepare data lakes, schema discovery, data cataloging, serverless data preparation
- **vs EMR**: Glue = serverless, simpler, managed ETL; EMR = complex big data, custom frameworks, more control
- **vs Data Pipeline**: Glue = serverless, Spark-based; Data Pipeline = legacy, orchestration-focused
- **Eliminate**: Real-time processing (use Kinesis), simple data copy (use DataSync)

### EMR (Elastic MapReduce)
**Definition**: Managed big data platform for processing vast amounts of data using open-source frameworks (Hadoop, Spark, HBase, Presto, Flink)

**Key Features**:
- **Supported Frameworks**:
  - **Hadoop**: MapReduce, HDFS, YARN
  - **Spark**: In-memory processing, 100x faster than MapReduce
  - **HBase**: NoSQL database on Hadoop
  - **Presto**: Fast SQL query engine
  - **Flink**: Stream processing framework
  - **Hive**: SQL-like interface to Hadoop
  - **Pig**: Scripting for data transformation
  - **Hudi, Iceberg, Delta Lake**: Data lake table formats
  - **Zeppelin, Jupyter**: Interactive notebooks
  - **Mahout**: Machine learning library
- **Deployment Options**:
  - **EMR on EC2**: Traditional cluster deployment, full control
  - **EMR on EKS**: Run Spark jobs on EKS, share compute across applications
  - **EMR on Outposts**: On-premises EMR clusters
  - **EMR Serverless**: Serverless Spark/Hive, auto-scaling, pay per use
- **Cluster Types**:
  - **Transient (Temporary)**: Start cluster, run job, terminate (cost-effective)
  - **Long-Running (Persistent)**: Keep cluster running for interactive queries, batch jobs
- **Node Types**:
  - **Master Node**: Manages cluster, coordinates tasks, tracks job status (1 or 3 for HA)
  - **Core Nodes**: Run tasks and store data in HDFS (can scale up/down, persist HDFS data)
  - **Task Nodes**: Run tasks only, no HDFS storage (can use Spot, scale dynamically)
- **Storage Options**:
  - **HDFS**: Distributed file system on Core/Master nodes (ephemeral, lost if cluster terminates)
  - **EMRFS**: S3-backed storage, persistent, decouples compute and storage (recommended)
  - **Local File System**: Instance store (ephemeral, very fast)
  - **EBS for HDFS**: EBS volumes attached to Core/Master nodes
- **Auto-Scaling**:
  - Automatic scaling based on CloudWatch metrics (YARN memory, HDFS utilization)
  - Scale Task nodes in/out dynamically
  - Scale Core nodes in/out (with shrinking protection for HDFS)
  - Custom auto-scaling policies
- **Instance Fleet vs Uniform Instance Groups**:
  - **Instance Fleet**: Mix multiple instance types, Spot/On-Demand mix, allocation strategy
  - **Uniform Instance Groups**: Single instance type per group (Master, Core, Task)
- **Cost Optimization**:
  - Use Spot instances for Task nodes (up to 90% savings)
  - Use Spot for Core nodes (with caution, risk of data loss if terminated)
  - Transient clusters for batch jobs
  - S3 for persistent storage (EMRFS) instead of HDFS
  - Reserved Instances for long-running clusters
  - EMR Serverless for variable workloads
- **Security**:
  - **Encryption**: At rest (EBS, S3 with SSE or CSE), in transit (TLS)
  - **IAM roles**: Service role, EC2 instance profile, Auto Scaling role
  - **Kerberos**: Authentication for cluster users
  - **Security groups**: Control network access
  - **Lake Formation**: Fine-grained access control
  - **Apache Ranger**: Authorization plugin
- **Monitoring & Logging**:
  - CloudWatch metrics (cluster, node, YARN, HDFS metrics)
  - CloudWatch Events for cluster state changes
  - Log aggregation to S3
  - Ganglia for cluster monitoring
  - EMR console for job tracking
- **High Availability**:
  - Multi-master (3 master nodes) for HA
  - HDFS replication (default 3 replicas)
  - Automatic replacement of failed nodes
  - Use S3 (EMRFS) for durable storage
- **Notebook Integration**:
  - EMR Notebooks (Jupyter-based, serverless)
  - Zeppelin notebooks
  - SageMaker notebooks
- **Step Execution**:
  - Submit steps (jobs) to cluster
  - Steps run sequentially or in parallel
  - Can add steps to running cluster
  - Auto-terminate after last step completes
- **Bootstrap Actions**:
  - Scripts run on all nodes before applications start
  - Install custom software, configure environment
  - Up to 16 bootstrap actions per cluster
- **Data Sources/Destinations**:
  - S3, DynamoDB, RDS, Redshift
  - HDFS, HBase, Cassandra
  - On-premises data via Direct Connect/VPN
  - Kinesis for streaming data

**Supported Integrations**:
- S3 (EMRFS), DynamoDB, RDS, Redshift, Kinesis
- Glue Data Catalog (as Hive metastore)
- Athena, QuickSight, SageMaker
- CloudWatch, CloudTrail, Lake Formation
- Step Functions (orchestration), Lambda
- IAM, KMS, Secrets Manager

**Exam Tips**:
- **Pick when**: Big data processing (Hadoop, Spark, Hive, Presto, Flink), complex data pipelines, machine learning at scale, petabyte-scale analytics, need custom frameworks, existing Hadoop workloads
- **Frameworks**: Hadoop (MapReduce), Spark (in-memory, 100x faster), HBase (NoSQL), Presto (fast SQL), Flink (streaming), Hive (SQL on Hadoop), Pig, Hudi/Iceberg/Delta Lake
- **Deployment options**:
  - **EMR on EC2**: Traditional, full control
  - **EMR Serverless**: No cluster management, auto-scaling, pay per use
  - **EMR on EKS**: Share EKS clusters
  - **EMR on Outposts**: On-premises
- **Cluster types**:
  - **Transient**: Start, run job, terminate (cost-effective for batch)
  - **Long-running**: Keep alive for interactive queries
- **Node types**: 
  - **Master**: Manage cluster (1 or 3 for HA)
  - **Core**: Run tasks + HDFS storage
  - **Task**: Run tasks only, no HDFS (best for Spot)
- **Storage**: 
  - **HDFS**: Ephemeral, on Core nodes
  - **EMRFS**: S3-backed, persistent, decouples storage/compute (recommended)
- **Cost optimization**: 
  - Spot for Task nodes (up to 90% savings)
  - Transient clusters for batch jobs
  - S3 (EMRFS) for storage (cheaper than HDFS)
  - EMR Serverless for variable workloads
- **Auto-scaling**: Based on YARN/HDFS metrics, scale Task/Core nodes
- **Use cases**: Log analysis, genomics, financial modeling, machine learning, clickstream analysis, data transformation, ETL at scale
- **vs Glue**: EMR = complex big data, custom frameworks, more control, full Spark/Hadoop; Glue = serverless, simpler ETL, managed, Spark-based
- **vs Redshift**: EMR = data processing/transformation/analytics; Redshift = data warehouse/BI queries
- **vs Athena**: EMR = complex processing, Spark/Hadoop needed; Athena = simple SQL queries on S3
- **Eliminate**: Simple ETL (use Glue), small datasets (<TB), serverless requirements (use Glue/Athena), no big data expertise

### QuickSight
**Definition**: Cloud-native serverless business intelligence service for creating interactive visualizations, dashboards, and ML-powered insights

**Key Features**:
- **SPICE Engine** (Super-fast, Parallel, In-memory Calculation Engine):
  - In-memory calculation engine for fast queries
  - Automatically replicates data across AZs for HA
  - Capacity: 10GB per user (Standard), 500GB per account (Enterprise)
  - Import modes: Full refresh or incremental
  - Direct query option (no SPICE, query source directly)
- **Data Sources**:
  - **AWS**: RDS (MySQL, PostgreSQL, SQL Server, Aurora), Redshift, Athena, S3, OpenSearch, DynamoDB (via Athena), Timestream
  - **SaaS**: Salesforce, Jira, ServiceNow, Adobe Analytics, GitHub, Twitter
  - **On-Premises**: SQL Server, MySQL, PostgreSQL, Oracle, Teradata, Presto (via JDBC)
  - **Files**: CSV, TSV, ELF, XLSX, JSON
  - **Data preparation**: Built-in data prep (joins, filters, calculated fields)
- **User Types & Pricing**:
  - **Reader**: View dashboards only, pay-per-session ($0.30/session, max $5/month)
  - **Author**: Create datasets, analyses, dashboards (Standard $9/month, Enterprise $18/month)
  - **Admin**: Manage QuickSight account
- **ML Insights**:
  - **Anomaly Detection**: Automatically identify outliers in data
  - **Forecasting**: Time-series forecasting (up to future periods)
  - **Auto-Narratives**: Natural language summaries of visualizations
  - **ML-powered Insights**: Discover hidden insights and trends
  - **What-if Analysis**: Scenario planning (Enterprise only)
  - **Q (Natural Language)**: Ask questions in plain English, get visualizations
- **Visualizations**:
  - 30+ chart types (bar, line, pie, scatter, heat map, tree map, waterfall, KPI, pivot table, etc.)
  - Interactive drill-downs
  - Parameters and controls for user interaction
  - Custom color themes and branding
  - Mobile-responsive layouts
- **Dashboards & Sharing**:
  - Publish analyses as dashboards
  - Share dashboards with users or groups
  - Email reports (PDF, Excel) on schedule
  - Subscribe to dashboard updates
  - Threshold alerts (email notifications)
- **Embedded Analytics**:
  - Embed dashboards in web applications (iframe)
  - SDK for programmatic embedding
  - Row-Level Security (RLS) for multi-tenant apps
  - Anonymous embedding (public access)
  - Registered user embedding (authenticated users)
  - Use case: White-label BI in SaaS applications
- **Security**:
  - IAM integration, SSO via SAML 2.0, Active Directory
  - Row-Level Security (RLS) for data access control
  - Column-Level Security (CLS) (Enterprise)
  - Encryption at rest (SPICE with KMS)
  - Encryption in transit (TLS)
  - VPC connectivity for private data sources
  - Private VPC access (Enterprise)
- **Standard vs Enterprise Edition**:
  - **Standard**: Basic BI features, 10GB SPICE/user, email support
  - **Enterprise**: 
    - ML insights (Q, forecasting, anomaly detection)
    - Hourly SPICE refresh (vs daily)
    - Encryption at rest
    - VPC connectivity, Active Directory integration
    - Column-Level Security (CLS)
    - 24/7 support, $18/author/month
- **Data Refresh**:
  - SPICE: Schedule refresh (hourly/daily/weekly/monthly)
  - Direct query: Always live data
  - Incremental refresh for large datasets
- **QuickSight Q**:
  - Natural language queries ("Show me sales by region")
  - ML-powered question suggestions
  - Answers with auto-generated visualizations
  - Enterprise edition only
- **Monitoring & Auditing**:
  - CloudTrail for API logging
  - Usage reports for dashboards and users
  - Dashboard performance metrics
  - Email alerts on scheduled refresh failures

**Supported Integrations**:
- RDS, Aurora, Redshift, Athena, S3, OpenSearch
- DynamoDB (via Athena), Timestream
- Salesforce, Jira, ServiceNow, Adobe Analytics
- On-premises databases (MySQL, PostgreSQL, Oracle, SQL Server)
- IAM, Active Directory, SAML 2.0 IdPs
- CloudTrail, KMS

**Exam Tips**:
- **Pick when**: BI dashboards, data visualizations, ML insights (anomaly detection, forecasting), interactive analytics, embed analytics in apps, ad-hoc analysis, serverless BI
- **Data sources**: RDS, Aurora, Redshift, Athena, S3, OpenSearch, SaaS apps (Salesforce), on-premises databases
- **SPICE**: Super-fast in-memory engine for fast queries, automatically HA across AZs
  - **Pick when**: Need fast performance, large datasets, multiple concurrent users
  - **Direct query**: Skip SPICE, query live data (slower, always current)
- **ML Insights**: Anomaly detection, forecasting, auto-narratives, Q (natural language queries)
- **Q (natural language)**: Ask questions in plain English, get visualizations (Enterprise only)
- **Embedded analytics**: Embed dashboards in web apps, Row-Level Security for multi-tenancy
- **Editions**:
  - **Standard**: Basic BI, $9/author, $0.30/reader session
  - **Enterprise**: ML insights, hourly refresh, encryption, AD integration, $18/author
- **Pricing**: Pay-per-session for readers ($0.30/session, max $5/month), monthly for authors
- **Use cases**: Business intelligence, executive dashboards, operational reporting, embedded analytics in SaaS apps, ad-hoc analysis, sales/marketing analytics
- **vs Traditional BI (Tableau, Power BI)**: QuickSight = serverless, cloud-native, pay-per-session, faster setup, tighter AWS integration
- **vs Athena**: QuickSight = visualizations/dashboards; Athena = SQL queries (often used together)
- **Eliminate**: Custom visualization requirements (use open-source like Grafana), need open-source BI tools, real-time streaming dashboards (use Kinesis Data Analytics with Flink), on-premises only

### Data Pipeline
**Definition**: Web service for orchestrating and automating data movement and transformation (legacy service)

**Key Features**:
- Scheduled data processing
- Data movement between AWS services
- Fault tolerance and retries
- On-premises data source support

**Exam Tips**:
- **Pick when**: Scheduled ETL (legacy scenarios), move data between AWS services, complex dependencies (legacy, prefer Glue)
- **Activities**: EMR, Hive, Copy, SQL, scripts
- **Data nodes**: S3, RDS, DynamoDB, Redshift
- **Use cases**: Regular ETL jobs, data archival, log processing (legacy)
- **vs Glue**: Data Pipeline = legacy, orchestration-focused; Glue = serverless, modern ETL, better choice for new projects
- **vs Step Functions**: Data Pipeline = data-centric; Step Functions = general-purpose workflow
- **Eliminate**: New projects (use Glue), real-time processing, serverless requirements

---

## 8. Management & Governance

### CloudWatch
**Definition**: Monitoring and observability service for AWS resources and applications

**Key Features**:
- **Metrics**:
  - **Basic Monitoring**: 5-minute intervals (free for many services)
  - **Detailed Monitoring**: 1-minute intervals (additional cost for EC2)
  - **High-Resolution Metrics**: Down to 1-second granularity (custom metrics)
  - **Standard Resolution**: 60-second granularity
  - **Retention**: 1-min data for 15 days, 5-min for 63 days, 1-hour for 455 days
  - **Custom Metrics**: Application-specific metrics via PutMetricData API
  - **Dimensions**: Name-value pairs to uniquely identify metrics (e.g., InstanceId, InstanceType)
  - **Statistics**: Sum, Average, Min, Max, SampleCount, pNN (percentile)
  - **Metric Math**: Perform calculations on metrics
- **Alarms**:
  - Monitor metrics and trigger actions
  - States: OK, ALARM, INSUFFICIENT_DATA
  - Actions: SNS, Auto Scaling, EC2 actions (stop, terminate, reboot, recover), Systems Manager actions
  - Evaluation periods and datapoints to alarm
  - Composite alarms: Combine multiple alarms with AND/OR logic
  - Alarm actions: Can execute Lambda via SNS
- **Logs**:
  - **Log Groups**: Container for log streams (e.g., /aws/lambda/function-name)
  - **Log Streams**: Sequence of log events from same source
  - **Log Events**: Record of activity with timestamp and message
  - **Retention**: 1 day to 10 years or never expire (default never)
  - **CloudWatch Logs Agent**: Send logs from EC2/on-premises
  - **Unified CloudWatch Agent**: Metrics + logs, system-level metrics (disk, memory)
  - **Log Insights**: Interactive query and analysis (SQL-like queries)
  - **Metric Filters**: Create metrics from log patterns, trigger alarms
  - **Subscriptions**: Real-time feed to Lambda, Kinesis Data Streams, Kinesis Data Firehose
  - **Log Groups Encryption**: KMS encryption
  - **Export to S3**: Batch export, can take up to 12 hours
- **Dashboards**:
  - Customizable views of metrics and logs
  - Can display metrics from multiple regions
  - Can be shared via link
  - Automatic dashboards for AWS services
  - Cross-account cross-region dashboards
- **Events/EventBridge**:
  - Schedule: Cron or rate expressions
  - Event patterns: React to AWS service state changes
  - Targets: Lambda, SNS, SQS, Kinesis, Step Functions, ECS tasks, etc.
- **CloudWatch Insights**:
  - **Container Insights**: Metrics and logs from containers (ECS, EKS, K8s)
  - **Lambda Insights**: Metrics and logs from Lambda functions
  - **Contributor Insights**: Top talkers analysis (top IPs, URLs, etc.)
  - **Application Insights**: Automated dashboards for applications (.NET, SQL Server, etc.)
- **ServiceLens**: Visualize and analyze distributed applications (X-Ray + CloudWatch)
- **Synthetics**: Create canaries to monitor endpoints and APIs
- **RUM (Real User Monitoring)**: Collect client-side performance data
- **Anomaly Detection**: ML-powered anomaly detection for metrics
- **Metric Streams**: Near real-time streaming to Kinesis Data Firehose

**Default Metrics by Service**:
- **EC2**: CPU, Network, Disk, Status Checks (NOT memory/disk space - need agent)
- **EBS**: Volume read/write ops, throughput, queue length, idle time
- **S3**: BucketSizeBytes, NumberOfObjects (daily metrics)
- **RDS**: CPU, connections, free storage, read/write latency, IOPS
- **Lambda**: Invocations, duration, errors, throttles, concurrent executions
- **ELB**: Request count, target response time, healthy/unhealthy host count
- **DynamoDB**: User errors, system errors, throttled requests, consumed RCU/WCU

**Supported Integrations**:
- All AWS services, EC2, Lambda, ECS, EKS, RDS, DynamoDB
- S3, Kinesis, SNS, SQS, Step Functions
- Auto Scaling, Systems Manager, CloudFormation
- X-Ray, ServiceLens, EventBridge
- Third-party: Datadog, Splunk, Sumo Logic via Metric Streams

**Exam Tips**:
- **Metrics**: 
  - Default metrics (CPU, Network, Disk) - 5 minute intervals
  - Detailed monitoring - 1 minute intervals (extra cost)
  - Custom metrics - application-specific data
  - Metric resolution: Standard (1 min) or High (1 sec)
- **Logs**: 
  - Centralized logging, retention (never expire to 10 years)
  - Log Groups and Log Streams
  - Filter patterns for searching
  - Metric filters to create metrics from logs
  - Insights for interactive log analytics
  - Export to S3, stream to Lambda/Kinesis
- **Alarms**: Trigger actions based on metric thresholds, composite alarms
- **Dashboards**: Custom visualizations, cross-region/cross-account
- **Events/EventBridge**: Scheduled/event-driven automation
- **Use cases**: Performance monitoring, log analysis, alerting, auto-scaling triggers, operational insights
- **Agent**: Install on EC2/on-premises for custom metrics and logs
- **Eliminate**: Long-term log storage (export to S3 Glacier), API call auditing (use CloudTrail)

### CloudTrail
**Definition**: Service for governance, compliance, and audit of AWS account activity

**Key Features**:
- Records AWS API calls
- Log file delivery to S3
- Integration with CloudWatch Logs
- Event history (90 days free)
- Multi-region and multi-account support
- Log file integrity validation

**Exam Tips**:
- **Pick when**: Audit API calls, compliance, governance, security analysis, troubleshoot operational issues, detect unauthorized access
- **Events**: Management events (control plane), Data events (data plane - S3/Lambda), Insights events (unusual activity)
- **Management events**: Create/delete/modify resources (enabled by default)
- **Data events**: S3 object-level API, Lambda executions (higher volume, extra cost)
- **Insights**: Automatically detect unusual API activity using ML
- **Best practices**: 
  - Enable in all regions
  - Log to S3 with encryption
  - Enable log file validation
  - Integrate with CloudWatch Logs for alerts
  - Multi-account logging with Organizations
- **Use cases**: Security analysis, compliance auditing, resource change tracking, troubleshooting
- **Event history**: 90 days free view in console
- **Trails**: For longer retention, deliver to S3
- **Eliminate**: Application logging (use CloudWatch Logs), performance monitoring (use CloudWatch Metrics)

### AWS Config
**Definition**: Service for assessing, auditing, and evaluating configurations of AWS resources

**Key Features**:
- Continuous configuration monitoring
- Configuration history
- Change notifications
- Compliance auditing with rules
- Remediation actions
- Multi-account and multi-region aggregation

**Exam Tips**:
- **Pick when**: Resource inventory, compliance rules, configuration drift detection, change history, track resource relationships, audit configurations
- **Config Rules**: Evaluate resource configurations for compliance (AWS managed or custom Lambda)
- **Configuration recorder**: Records resource configurations
- **Configuration snapshots**: Point-in-time view of configurations
- **Configuration history**: Track changes over time
- **Remediation**: Automatic remediation via SSM Automation documents
- **Use cases**: 
  - Security analysis (unrestricted SSH access, S3 public buckets)
  - Change management (who changed what when)
  - Compliance auditing (PCI-DSS, HIPAA)
  - Troubleshooting (configuration drift)
  - Resource relationships tracking
- **Aggregators**: Multi-account and multi-region data aggregation
- **vs CloudTrail**: Config = configuration changes (what changed); CloudTrail = API activity (who did what)
- **Eliminate**: Real-time enforcement (use SCPs), API activity logging (use CloudTrail)

### Systems Manager
**Definition**: Unified interface for managing AWS resources and on-premises infrastructure

**Key Features**:
- Operational insights and automation
- Patch management
- Configuration management
- Secure access to instances
- Parameter and secret storage

**Exam Tips**:
- **Session Manager**: 
  - Browser-based or CLI shell access to EC2/on-premises
  - No SSH keys, bastion hosts, or open inbound ports needed
  - Audit via CloudTrail, log sessions to S3/CloudWatch
  - **Pick when**: Bastion-less SSH/RDP, secure access, audit requirements
- **Parameter Store**: 
  - Store config data and secrets (free standard, $0.05/advanced parameter)
  - Hierarchical structure, versioning, TTL
  - Integration with other services (Lambda, ECS, CloudFormation)
  - **Pick when**: Simple config/secrets storage, cost-sensitive, no automatic rotation
- **Run Command**: Execute scripts on EC2 fleet, no SSH needed
- **Patch Manager**: Automated patching for OS and applications
- **Automation**: Runbooks for common tasks (AMI creation, snapshot management)
- **Inventory**: Collect metadata from managed instances
- **State Manager**: Maintain consistent configuration state
- **Use cases**: Patch management, configuration compliance, remote command execution, parameter storage
- **Requires**: SSM Agent (pre-installed on Amazon Linux 2, Windows, Ubuntu)
- **Eliminate**: Secret rotation needed (use Secrets Manager), app-level monitoring (use CloudWatch)

### CloudFormation
**Definition**: Infrastructure as Code (IaC) service for provisioning AWS resources

**Key Features**:
- Declarative templates (JSON/YAML)
- Automate resource provisioning
- Stack management (create, update, delete)
- Drift detection
- StackSets for multi-account/region
- Change sets for preview

**Exam Tips**:
- **Pick when**: Infrastructure as code, repeatable deployments, stack management, automate provisioning, version control infrastructure
- **Templates**: JSON or YAML, define resources and dependencies
- **Stacks**: Collection of AWS resources managed as single unit
- **StackSets**: Deploy stacks across multiple accounts/regions (AWS Organizations integration)
- **Features**:
  - Change sets: Preview changes before applying
  - Drift detection: Identify manual changes to resources
  - Rollback on failure
  - Stack policies: Prevent updates to critical resources
  - Cross-stack references: Export/import values between stacks
  - Nested stacks: Reusable templates
- **Helper scripts**: cfn-init, cfn-signal, cfn-hup for EC2 configuration
- **Use cases**: Environment replication (dev/test/prod), disaster recovery, compliance, multi-region deployment
- **vs Terraform**: CloudFormation = AWS-native, free, deep AWS integration; Terraform = multi-cloud, larger ecosystem
- **Eliminate**: Simple manual resources, one-off deployments, need multi-cloud (use Terraform)

### CDK (Cloud Development Kit)
- **Pick when**: Define infra in programming languages (TypeScript, Python, Java, C#)

### SAM (Serverless Application Model)
- **Pick when**: Simplified CloudFormation for serverless apps

### Service Catalog
- **Pick when**: Govern approved resources, self-service IT

### Control Tower
- **Pick when**: Multi-account setup, landing zone, guardrails

### Organizations
- **Pick when**: Consolidated billing, SCPs (Service Control Policies), OU management

### Trusted Advisor
- **Pick when**: Cost optimization, security, performance, fault tolerance checks

### AWS Compute Optimizer
- **Pick when**: Right-sizing recommendations for EC2, Lambda, EBS

---

## 9. Migration & Transfer

### DMS (Database Migration Service)
**Definition**: Managed service for migrating databases to AWS with minimal downtime

**Key Features**:
- Continuous data replication
- Homogeneous and heterogeneous migrations
- Near-zero downtime migrations
- One-time migration or continuous replication
- Schema conversion with SCT

**Exam Tips**:
- **Pick when**: Database migration (homogeneous/heterogeneous), continuous replication, minimal downtime, consolidate databases, disaster recovery
- **Migration types**:
  - Homogeneous (Oracle → Oracle, MySQL → RDS MySQL)
  - Heterogeneous (Oracle → Aurora PostgreSQL, SQL Server → MySQL)
- **Schema Conversion Tool (SCT)**: Convert database schemas and code for heterogeneous migrations
- **Replication instance**: EC2 instance running DMS software
- **Sources**: Oracle, SQL Server, MySQL, PostgreSQL, MongoDB, SAP, DB2, Azure SQL, on-premises and cloud databases
- **Targets**: RDS, Aurora, Redshift, DynamoDB, S3, OpenSearch, Kinesis, DocumentDB, Neptune
- **Use cases**: Database migration to AWS, database consolidation, dev/test environments, continuous replication
- **CDC (Change Data Capture)**: Ongoing replication after initial migration
- **Eliminate**: Large data transfers without database (use DataSync/Snow Family), ETL workloads (use Glue)

### DataSync
**Definition**: Automated data transfer service for moving data between on-premises and AWS

**Key Features**:
- Automated data transfer
- Up to 10x faster than open-source tools
- Built-in data validation
- Bandwidth throttling
- Scheduling support

**Exam Tips**:
- **Pick when**: Automate data transfer on-prem ↔ AWS, migrate to cloud, ongoing data replication, NFS/SMB file shares, sync large datasets
- **Protocols**: NFS, SMB, HDFS, object storage
- **Targets**: S3 (all classes), EFS, FSx for Windows, FSx for Lustre, FSx for OpenZFS, FSx for NetApp ONTAP
- **Agent**: Install on-premises (VMware, KVM, Hyper-V) or EC2
- **Features**:
  - Data encryption in transit (TLS)
  - Data validation and integrity checking
  - Bandwidth throttling
  - Scheduling and automation
  - CloudWatch metrics and logs
- **Use cases**: Cloud migration, data processing workflows, data archival, data replication for DR
- **vs Storage Gateway**: DataSync = one-time/scheduled transfers, faster; Storage Gateway = continuous hybrid storage
- **vs Snow Family**: DataSync = over network, ongoing; Snow = offline transfer, one-time large migrations
- **Eliminate**: Database migration (use DMS), need offline transfer (use Snow Family), continuous hybrid storage (use Storage Gateway)

### Snow Family
**Definition**: Physical devices for data migration and edge computing when network transfer is impractical

**Key Features**:
- Offline data transfer
- Edge computing capabilities
- Ruggedized, secure devices
- Encryption (256-bit)
- Tamper-resistant

**Exam Tips**:
- **Snowcone**: 
  - 8TB HDD or 14TB SSD
  - Smallest device, portable (4.5 lbs)
  - Edge computing with IoT
  - **Pick when**: Space/weight constrained, edge locations, small datasets
- **Snowball Edge**: 
  - Storage Optimized: 80TB, 40 vCPUs, 80 GB RAM
  - Compute Optimized: 42TB, 52 vCPUs, 208 GB RAM, optional GPU
  - **Pick when**: TB to PB migrations, edge computing, local processing
- **Snowmobile**: 
  - 100PB per truck
  - Exabyte-scale transfers
  - **Pick when**: 10PB+ data, datacenter migration
- **Pick when**: Network bandwidth insufficient, petabyte+ migrations, limited connectivity, high network costs, weeks/months over network
- **Features**: 
  - Local compute (EC2, Lambda)
  - S3-compatible storage
  - AWS OpsHub GUI for management
  - DataSync agent pre-installed (Snowcone)
- **Use cases**: Large-scale cloud migration, disaster recovery, content distribution, remote/offline locations
- **Process**: Order device → transfer data locally → ship back to AWS → data uploaded to S3
- **Rule of thumb**: If transfer takes >1 week over network, consider Snow Family
- **Eliminate**: Good network connectivity, small datasets (<10TB), ongoing sync (use DataSync)

### Transfer Family
- **Pick when**: SFTP/FTPS/FTP to S3/EFS

### Application Migration Service (MGN)
- **Pick when**: Lift-and-shift replication for servers

---

## 10. Developer Tools

### CodeCommit
- **Pick when**: Managed Git repositories

### CodeBuild
- **Pick when**: Managed build service, compile/test/package

### CodeDeploy
- **Pick when**: Automated deployment to EC2, Lambda, on-prem

### CodePipeline
- **Pick when**: CI/CD orchestration, integrate with Jenkins, GitHub, etc.

### CodeArtifact
- **Pick when**: Managed artifact repository (npm, Maven, pip)

### CodeStar
- **Pick when**: Unified project dashboard for CI/CD

### Cloud9
- **Pick when**: Browser-based IDE

---

## 11. Machine Learning & AI

### SageMaker
- **Pick when**: Build, train, deploy ML models at scale
- **Features**: Notebooks, training jobs, endpoints, AutoML, Ground Truth

### Rekognition
- **Pick when**: Image/video analysis, facial recognition, content moderation

### Comprehend
- **Pick when**: NLP, sentiment analysis, entity extraction

### Textract
- **Pick when**: Extract text/data from documents, forms

### Translate
- **Pick when**: Language translation

### Polly
- **Pick when**: Text-to-speech

### Transcribe
- **Pick when**: Speech-to-text

### Lex
- **Pick when**: Chatbots, conversational interfaces

### Personalize
- **Pick when**: Recommendation engines

### Forecast
- **Pick when**: Time-series forecasting

---

## 12. Media Services

### MediaConvert
- **Pick when**: VOD transcoding

### MediaLive / MediaPackage
- **Pick when**: Live video streaming

### Elastic Transcoder
- **Pick when**: Legacy media transcoding (prefer MediaConvert)

---

## 13. IoT

### IoT Core
- **Pick when**: Device connectivity, MQTT, rules engine, device shadows

### IoT Greengrass
- **Pick when**: Edge computing, local Lambda execution

---

## 15. Additional Important Services

### AWS Backup
**Definition**: Centralized backup service across AWS services

**Key Features**:
- Centralized backup management
- Automated backup schedules
- Backup policies and plans
- Cross-region and cross-account backup
- Compliance reporting

**Exam Tips**:
- **Pick when**: Centralized backup management, automate backups across services, compliance requirements, lifecycle policies
- **Supported services**: EC2, EBS, RDS, Aurora, DynamoDB, DocumentDB, Neptune, EFS, FSx, Storage Gateway
- **Features**: Backup plans, retention policies, lifecycle to cold storage, point-in-time recovery, incremental backups
- **Backup Vault**: Storage location with encryption and access policies
- **Use cases**: Centralized backup, compliance, disaster recovery, cross-region backup
- **Eliminate**: Simple single-service backups (use native service features), real-time replication

### Elastic Load Balancing (ELB)
**Definition**: Automatically distribute traffic across multiple targets

**Key Features**:
- High availability across AZs
- Health checks
- SSL/TLS termination
- Auto Scaling integration
- Cross-zone load balancing

**Exam Tips**:
- **Connection draining/deregistration delay**: Complete in-flight requests before removing target (1-3600s)
- **Sticky sessions**: Route requests from same client to same target (ALB/CLB)
- **Cross-zone load balancing**: Distribute evenly across all targets in all AZs (ALB always, NLB/CLB optional)
- **Health checks**: Determine target availability, protocol-specific (HTTP, HTTPS, TCP)
- **SSL/TLS**: Offload encryption, use ACM certificates, SNI for multiple certificates (ALB/NLB)
- **Monitoring**: CloudWatch metrics, access logs to S3
- **Pick patterns**: ALB for HTTP/S microservices, NLB for performance, GWLB for appliances

### Auto Scaling
**Definition**: Automatically adjust compute capacity to maintain performance and optimize costs

**Key Features**:
- Automatic scaling based on demand
- Integration with ELB
- Health checks and replacement
- Multiple scaling policies

**Exam Tips**:
- **Auto Scaling Groups (ASG)**: Min/max/desired capacity, launch template/configuration
- **Scaling policies**:
  - **Target tracking**: Maintain specific metric (CPU 50%, request count)
  - **Step scaling**: Scale based on alarm severity
  - **Simple scaling**: Single scaling adjustment
  - **Scheduled**: Scale based on predictable patterns
  - **Predictive**: ML-based forecasting
- **Health checks**: EC2 status + ELB health checks
- **Cooldown**: Wait period after scaling activity (default 300s)
- **Lifecycle hooks**: Custom actions during launch/termination
- **Termination policies**: Which instance to terminate first (OldestInstance, NewestInstance, etc.)
- **Use cases**: Handle variable traffic, maintain availability, cost optimization
- **Integration**: Launch with ELB, use CloudWatch alarms, multiple AZs for HA

### AWS Organizations
**Definition**: Centrally manage and govern multiple AWS accounts

**Key Features**:
- Consolidated billing
- Hierarchical account organization (OUs)
- Service Control Policies (SCPs)
- Automated account creation
- Centralized logging and monitoring

**Exam Tips**:
- **Pick when**: Multi-account strategy, consolidated billing, centralized governance, apply policies across accounts
- **Organizational Units (OUs)**: Group accounts hierarchically (e.g., Production, Development, Finance)
- **Service Control Policies (SCPs)**: 
  - Maximum permissions for accounts/OUs
  - Does not grant permissions, only restricts
  - Applied to accounts and OUs
  - **Pick when**: Enforce compliance, prevent service usage, meet regulatory requirements
- **Consolidated billing**: Single payment, volume discounts, Reserved Instance sharing
- **AWS Control Tower integration**: Automated landing zone setup
- **Use cases**: Multi-account governance, cost management, security boundaries, environment isolation
- **Eliminate**: Single account scenarios, no policy enforcement needed

### AWS Well-Architected Framework
**Definition**: Best practices and design principles for cloud architectures

**Five Pillars**:
1. **Operational Excellence**: Run and monitor systems, continuous improvement
2. **Security**: Protect information, systems, and assets
3. **Reliability**: Recover from failures, meet demand, mitigate disruptions
4. **Performance Efficiency**: Use resources efficiently, adapt to changing requirements
5. **Cost Optimization**: Avoid unnecessary costs, optimize spending

**Exam Tips**:
- **Design principles**:
  - Stop guessing capacity (use Auto Scaling)
  - Test at production scale
  - Automate architecture experimentation
  - Allow for evolutionary architectures
  - Drive architectures using data
  - Improve through game days
- **Well-Architected Tool**: Review workloads against best practices
- **Use in exam**: Recognize which pillar each service/pattern addresses

---

## 14. Cost Management

### Cost Explorer
- **Pick when**: Visualize/analyze spending patterns

### Budgets
- **Pick when**: Set cost/usage budgets, alerts

### Cost and Usage Reports
- **Pick when**: Detailed billing data export

---

## Exam quick-decision flowchart

```
START
  ↓
What's the constraint?
  ├─ "No servers to manage" → Lambda, Fargate, Aurora Serverless, DynamoDB
  ├─ "Relational + SQL" → RDS/Aurora (not DynamoDB)
  ├─ "Massive scale + <10ms" → DynamoDB
  ├─ "Shared file system" → EFS (Linux) / FSx (Windows)
  ├─ "Archive/long-term" → S3 Glacier
  ├─ "Cross-region DR" → Multi-region (S3 CRR, Aurora Global, Route 53)
  ├─ "Private S3 access" → VPC Gateway Endpoint
  ├─ "API audit" → CloudTrail
  ├─ "Secret rotation" → Secrets Manager
  ├─ "GPU/ML" → EC2 P/G instances, SageMaker
  ├─ "Real-time stream" → Kinesis Data Streams
  ├─ "Batch analytics" → Redshift, Athena, EMR
  └─ "Cost optimization" → Spot, S3 lifecycle, right-sizing
```

---

## Exam-taking principles

1. **Read constraints first**: RTO/RPO, cost, compliance, latency, throughput, operational overhead
2. **Eliminate**: Remove options requiring more management than stated
3. **Prefer managed**: Unless question needs specific control
4. **Match keywords**: "least operational overhead", "fully managed", "real-time", "multi-region", "archive"
5. **Security default**: Least privilege, encryption at rest/transit, private subnets, VPC endpoints
6. **HA pattern**: Multi-AZ for regional HA, Multi-Region for DR

---

Focus on constraints → service mapping and elimination based on keywords.