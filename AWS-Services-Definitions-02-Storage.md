# AWS Storage Services - Complete Definitions (SAA-C03)

## Table of Contents
- [Amazon S3](#amazon-s3)
- [Amazon EBS](#amazon-ebs)
- [Amazon EFS](#amazon-efs)
- [Amazon FSx](#amazon-fsx)
- [AWS Storage Gateway](#aws-storage-gateway)
- [AWS Backup](#aws-backup)
- [AWS Snow Family](#aws-snow-family)
- [Amazon S3 Glacier](#amazon-s3-glacier)

---

## Amazon S3 (Simple Storage Service)

**Definition**: Object storage service that offers industry-leading scalability, data availability, security, and performance. Store and retrieve any amount of data from anywhere on the web.

**Key Features**:
- Object-based storage (not block or file-based)
- Virtually unlimited storage capacity
- Objects up to 5 TB in size
- 99.999999999% (11 9's) durability
- Multiple storage classes for cost optimization
- Versioning, lifecycle policies, replication
- Strong consistency for all operations

**Storage Classes**:

| Class | Use Case | Availability | Durability | Min Storage | Retrieval |
|-------|----------|--------------|------------|-------------|-----------|
| **S3 Standard** | Frequently accessed | 99.99% | 11 9's | None | Instant |
| **S3 Intelligent-Tiering** | Unknown/changing access | 99.9% | 11 9's | None | Instant |
| **S3 Standard-IA** | Infrequent access | 99.9% | 11 9's | 30 days | Instant |
| **S3 One Zone-IA** | Infrequent, non-critical | 99.5% | 11 9's (1 AZ) | 30 days | Instant |
| **S3 Glacier Instant** | Archive, instant access | 99.9% | 11 9's | 90 days | Instant |
| **S3 Glacier Flexible** | Archive, async access | 99.99% | 11 9's | 90 days | Min-hours |
| **S3 Glacier Deep Archive** | Long-term archive | 99.99% | 11 9's | 180 days | 12 hours |

**Key Concepts**:
- **Bucket**: Container for objects (globally unique name)
- **Object**: Files stored in buckets (key-value pairs)
- **Key**: Unique identifier for object within bucket
- **Version ID**: Identifier for specific version of object
- **Metadata**: Name-value pairs about object
- **Region**: AWS Region where bucket resides

**Security Features**:
- **Bucket Policies**: JSON-based resource policies
- **IAM Policies**: User/role-based permissions
- **Access Control Lists (ACLs)**: Legacy access control
- **Encryption at Rest**: SSE-S3, SSE-KMS, SSE-C, Client-side
- **Encryption in Transit**: HTTPS/TLS
- **Block Public Access**: Account and bucket-level controls
- **MFA Delete**: Require MFA for object deletion
- **Pre-signed URLs**: Temporary access to objects

**Data Management**:
- **Versioning**: Keep multiple versions of objects
- **Lifecycle Policies**: Automate transitions and expiration
- **Replication**: CRR (Cross-Region) and SRR (Same-Region)
- **Object Lock**: WORM (Write Once Read Many) compliance
- **Event Notifications**: Trigger workflows (Lambda, SNS, SQS)

**Performance**:
- 3,500 PUT/COPY/POST/DELETE requests per second per prefix
- 5,500 GET/HEAD requests per second per prefix
- No limit on number of prefixes
- Multipart upload for objects > 100 MB
- Transfer Acceleration for faster uploads
- S3 Select for retrieving subsets of object data

**Use Cases**:
- Static website hosting
- Data lakes and big data analytics
- Backup and disaster recovery
- Application data storage
- Media hosting and distribution
- Software delivery

**Exam Tips**:
- S3 Standard for frequently accessed data
- Intelligent-Tiering when access patterns unknown
- Glacier for long-term archival
- Enable versioning for compliance and accidental deletion protection
- Use lifecycle policies to automatically transition objects
- CRR requires versioning enabled on both source and destination
- Use S3 Transfer Acceleration for global uploads
- S3 is eventually consistent for overwrite PUTS and DELETES (older behavior - now strongly consistent)
- Actually, S3 now provides strong read-after-write consistency for all operations

---

## Amazon EBS (Elastic Block Store)

**Definition**: Block-level storage volumes for use with EC2 instances. EBS volumes behave like raw, unformatted block devices that can be mounted as devices on instances.

**Key Features**:
- Persistent block storage
- Automatically replicated within Availability Zone
- Snapshots stored in S3
- Encryption support
- Resizable volumes
- Multiple volumes per instance
- Attaches to single EC2 instance (except io2 multi-attach)

**Volume Types**:

### SSD-Based Volumes

| Type | Name | IOPS | Throughput | Use Case |
|------|------|------|------------|----------|
| **gp3** | General Purpose SSD | 3,000-16,000 | 125-1,000 MB/s | Boot volumes, dev/test |
| **gp2** | General Purpose SSD | 3-16,000 (burst) | Up to 250 MB/s | Boot volumes, general workloads |
| **io2** | Provisioned IOPS SSD | 100-64,000 | Up to 1,000 MB/s | I/O intensive, critical workloads |
| **io2 Block Express** | Highest Performance SSD | 256,000 | 4,000 MB/s | Largest, most critical workloads |
| **io1** | Provisioned IOPS SSD | 100-64,000 | Up to 1,000 MB/s | Legacy, use io2 instead |

### HDD-Based Volumes

| Type | Name | Throughput | Use Case |
|------|------|------------|----------|
| **st1** | Throughput Optimized HDD | 500 MB/s | Big data, data warehouses |
| **sc1** | Cold HDD | 250 MB/s | Infrequently accessed data |

**Snapshots**:
- Point-in-time backups stored in S3
- Incremental backups (only changed blocks)
- Can be copied across regions
- Can create AMIs from snapshots
- Encrypted snapshots from encrypted volumes
- Fast Snapshot Restore (FSR) for instant volume restoration

**Encryption**:
- AES-256 encryption using AWS KMS
- Encrypt volumes at creation
- Encrypted volumes produce encrypted snapshots
- Data encrypted at rest, in transit, and all backups
- Minimal performance impact

**Multi-Attach** (io2 only):
- Attach single volume to multiple EC2 instances
- Up to 16 Nitro-based instances
- All instances in same AZ
- Requires cluster-aware file system

**Use Cases**:
- Boot volumes for EC2 instances
- Database storage (IO-intensive)
- Enterprise applications
- File systems
- Development and test environments

**Exam Tips**:
- EBS volumes are AZ-specific
- Snapshots are stored in S3 and are regional
- gp3 offers better price/performance than gp2
- io2 for mission-critical, high-performance databases
- st1 for big data, data warehouses (throughput-intensive)
- sc1 for cold data, lowest cost
- Cannot attach EBS volume to multiple instances (except io2 Multi-Attach)
- Use snapshots to migrate volumes across AZs
- EBS-optimized instances for dedicated bandwidth

---

## Amazon EFS (Elastic File System)

**Definition**: Fully managed, elastic, shared file storage system that provides a simple, scalable file storage for use with AWS Cloud services and on-premises resources.

**Key Features**:
- NFSv4 protocol
- Elastic storage (grows and shrinks automatically)
- Concurrent access from multiple instances
- Supports thousands of connections
- Regional service (spans multiple AZs)
- Petabyte scale
- Strong consistency

**Performance Modes**:
- **General Purpose**: Latency-sensitive use cases (default)
  - Max 7,000 file operations per second
- **Max I/O**: Higher aggregate throughput and operations per second
  - Slightly higher latencies
  - Parallel workloads

**Throughput Modes**:
- **Bursting**: Throughput scales with file system size
  - 50 MB/s per TB, burst to 100 MB/s
- **Provisioned**: Specify throughput independent of storage
  - For applications requiring higher throughput-to-storage ratio
- **Elastic**: Automatically scales throughput with workload

**Storage Classes**:
- **EFS Standard**: Frequently accessed files
- **EFS Infrequent Access (IA)**: Lower cost for infrequently accessed files
- **Lifecycle Management**: Automatically move files to IA

**Access Control**:
- POSIX permissions
- IAM for API access
- Security groups for network access
- EFS Access Points for application-specific access
- VPC for network isolation

**Encryption**:
- Encryption at rest using KMS
- Encryption in transit using TLS
- Transparent encryption

**Use Cases**:
- Shared file storage for EC2 instances
- Container storage
- Content management systems
- Web serving
- Home directories
- Big data analytics
- Application development and testing

**Exam Tips**:
- EFS is for shared file storage (multiple instances)
- Works across multiple AZs in a region
- Pay only for storage used (no pre-provisioning)
- More expensive than EBS and S3
- Linux-based instances only (NFSv4)
- Use lifecycle management to move to IA for cost savings
- General Purpose mode for most use cases
- Max I/O for highly parallelized applications
- Can mount on-premises servers via Direct Connect or VPN

---

## Amazon FSx

**Definition**: Family of fully managed third-party file systems with native compatibility and feature sets.

### FSx for Windows File Server

**Definition**: Fully managed native Windows file system with full SMB support and Windows NTFS features.

**Key Features**:
- Built on Windows Server
- SMB protocol support (2.0 - 3.1.1)
- Windows NTFS file system
- Active Directory integration
- Distributed File System (DFS)
- Data deduplication
- SSD and HDD storage options
- Automated backups
- Encryption at rest and in transit

**Performance**:
- Up to 2 GB/s throughput
- Millions of IOPS
- Sub-millisecond latencies

**Use Cases**:
- Windows-based applications
- Home directories
- Microsoft SQL Server storage
- Media workflows
- Content management

**Exam Tips**:
- Use FSx for Windows when you need SMB protocol
- Native Windows compatibility (Active Directory)
- Multi-AZ for high availability
- Can access from on-premises via VPN/Direct Connect

### FSx for Lustre

**Definition**: High-performance file system optimized for fast processing of workloads such as machine learning, HPC, video processing, and financial modeling.

**Key Features**:
- POSIX-compliant file system
- Hundreds of GB/s throughput
- Millions of IOPS
- Sub-millisecond latencies
- Seamless S3 integration
- Scratch and Persistent deployment options

**Deployment Options**:
- **Scratch**: Temporary storage, data not replicated
  - Higher throughput (200 MB/s per TiB)
  - No replication, data loss if server fails
- **Persistent**: Long-term storage, data replicated within AZ
  - 50, 100, or 200 MB/s per TiB throughput

**S3 Integration**:
- Link file system to S3 bucket
- Read data from S3 as files
- Write results back to S3

**Use Cases**:
- Machine learning
- High-performance computing (HPC)
- Video processing
- Financial modeling
- Electronic design automation (EDA)
- Big data analytics

**Exam Tips**:
- FSx for Lustre for high-performance computing
- Integrates seamlessly with S3
- Use Scratch for temporary, high-performance storage
- Use Persistent for long-term storage with replication
- Sub-millisecond latencies
- Linux-based applications

### FSx for NetApp ONTAP

**Definition**: Fully managed shared storage with NetApp ONTAP features and performance.

**Key Features**:
- Multi-protocol access (NFS, SMB, iSCSI)
- Data deduplication and compression
- Snapshots and replication
- Storage efficiency features
- FlexClone technology
- High availability and durability

**Use Cases**:
- Enterprise applications
- VMware on AWS
- Database workloads
- DevOps workflows

### FSx for OpenZFS

**Definition**: Fully managed file storage built on the OpenZFS file system.

**Key Features**:
- Point-in-time snapshots
- Data compression
- Up to 1 million IOPS
- Sub-millisecond latencies
- Z-Standard compression

**Use Cases**:
- Linux-based workloads
- Data migration from on-premises ZFS

**Exam Tips for FSx Family**:
- **Windows**: Use FSx for Windows File Server
- **High Performance/HPC**: Use FSx for Lustre
- **NetApp Features**: Use FSx for NetApp ONTAP
- **OpenZFS**: Use FSx for OpenZFS

---

## AWS Storage Gateway

**Definition**: Hybrid cloud storage service that provides on-premises access to virtually unlimited cloud storage. Connects on-premises environments with cloud storage.

**Types**:

### File Gateway (S3 File Gateway)

**Definition**: Provides file interface to S3 with local caching for low-latency access.

**Key Features**:
- NFS and SMB protocols
- Files stored as S3 objects
- Local cache for frequently accessed data
- Integrates with Active Directory
- Supports all S3 features (lifecycle, versioning, etc.)

**Use Cases**:
- File sharing
- Backup and archival
- Tiering to cloud
- Disaster recovery

### Volume Gateway

**Definition**: Block storage volumes backed by S3 with local caching.

**Types**:
- **Cached Volumes**: Store primary data in S3, cache frequently accessed data locally
  - 1 GB - 32 TB per volume
- **Stored Volumes**: Store entire dataset locally, asynchronously backup to S3
  - 1 GB - 16 TB per volume
  - Point-in-time snapshots stored as EBS snapshots

**Use Cases**:
- Backup and disaster recovery
- Migration to cloud
- Disaster recovery

### Tape Gateway (VTL)

**Definition**: Virtual tape library backed by S3 and Glacier.

**Key Features**:
- iSCSI-based virtual tape library
- Integrates with existing backup applications
- Virtual tapes stored in S3 (Virtual Tape Library)
- Archive tapes to Glacier (Virtual Tape Shelf)
- Supports leading backup applications (Veeam, Veritas, etc.)

**Use Cases**:
- Replace physical tape infrastructure
- Long-term archival
- Compliance

**Exam Tips**:
- File Gateway for file-based access to S3
- Volume Gateway for block storage with cloud backup
- Tape Gateway for replacing physical tape backups
- All types use local cache for low latency
- Data stored in AWS is durable (S3/Glacier)
- Requires on-premises VM or hardware appliance

---

## AWS Backup

**Definition**: Fully managed backup service that centralizes and automates data protection across AWS services.

**Key Features**:
- Centralized backup management
- Policy-based backup
- Automated backup scheduling
- Lifecycle management to cold storage
- Cross-region and cross-account backups
- Compliance and auditing
- Encryption at rest and in transit

**Supported Services**:
- EC2 instances
- EBS volumes
- RDS databases
- DynamoDB tables
- EFS file systems
- FSx file systems
- Storage Gateway volumes
- S3 buckets
- VMware workloads on-premises

**Components**:
- **Backup Plans**: Policies defining when and how to backup
- **Backup Vault**: Container for organizing backups
- **Resource Assignments**: Resources to include in backup plan
- **Recovery Points**: Snapshots/backups created by AWS Backup

**Backup Plan Options**:
- Frequency (hourly, daily, weekly, monthly)
- Retention period
- Transition to cold storage
- Copy to another region
- Lifecycle policies

**Use Cases**:
- Centralized backup across AWS accounts
- Compliance and regulatory requirements
- Disaster recovery
- Data protection automation

**Exam Tips**:
- AWS Backup for centralized backup management
- Supports multiple AWS services in one console
- Policy-based backup with lifecycle management
- Cross-region backups for disaster recovery
- Integrates with AWS Organizations for multi-account
- Use backup plans to define backup policies
- Backup vaults for organizing and securing backups

---

## AWS Snow Family

**Definition**: Family of physical devices for data migration and edge computing when network transfer is not feasible or cost-effective.

### AWS Snowcone

**Definition**: Smallest member of AWS Snow Family for edge computing and data transfer.

**Specifications**:
- 8 TB usable storage (HDD)
- 14 TB usable (SSD) for Snowcone SSD
- 2 CPUs, 4 GB memory
- Lightweight (4.5 lbs / 2.1 kg)
- Rugged and portable

**Use Cases**:
- Edge computing
- IoT data collection
- Content distribution
- Small data migrations

### AWS Snowball

**Definition**: Petabyte-scale data transfer device with on-board storage and compute.

**Types**:

**Snowball Edge Storage Optimized**:
- 80 TB usable storage
- 40 vCPUs, 80 GB memory
- 1 TB SSD for block volumes
- S3-compatible storage
- EC2 compute capabilities

**Snowball Edge Compute Optimized**:
- 42 TB usable storage
- 52 vCPUs, 208 GB memory
- Optional GPU
- EC2 and Lambda at the edge

**Use Cases**:
- Large-scale data migrations (10 TB+)
- Data center decommissioning
- Disaster recovery
- Edge computing

### AWS Snowmobile

**Definition**: Exabyte-scale data transfer service for massive data migrations.

**Specifications**:
- Up to 100 PB per Snowmobile
- 45-foot long ruggedized shipping container
- Pulled by semi-trailer truck
- GPS tracking and security

**Use Cases**:
- Data center migration
- Complete data center shutdown
- Exabyte-scale migrations

**Exam Tips**:
- **Snowcone**: < 8 TB, edge computing, portable
- **Snowball**: 10 TB - 80 TB, petabyte migrations
- **Snowmobile**: > 10 PB, exabyte migrations
- All Snow devices provide encryption
- Use when network transfer is slow or expensive
- Can run EC2 instances and Lambda functions on Snowball Edge
- Data import to S3, EBS, Glacier
- Consider data transfer time and network costs

---

## Amazon S3 Glacier

**Definition**: Secure, durable, and extremely low-cost storage service for data archiving and long-term backup. (Now part of S3 as storage classes)

**Storage Classes**:

### S3 Glacier Instant Retrieval
- Millisecond retrieval
- 68% lower cost than S3 Standard-IA
- Minimum storage duration: 90 days
- Archive data rarely accessed but needs instant access

### S3 Glacier Flexible Retrieval (formerly Glacier)
- Retrieval options:
  - **Expedited**: 1-5 minutes
  - **Standard**: 3-5 hours
  - **Bulk**: 5-12 hours
- Minimum storage duration: 90 days
- Archive data with retrieval times of minutes to hours

### S3 Glacier Deep Archive
- Lowest cost storage
- Retrieval options:
  - **Standard**: 12 hours
  - **Bulk**: 48 hours
- Minimum storage duration: 180 days
- Long-term retention (7-10 years)
- Compliance and regulatory archives

**Key Features**:
- 99.999999999% (11 9's) durability
- Encryption by default
- Vault Lock for compliance
- Lifecycle policies for automatic transitions
- Query-in-place with S3 Select and Glacier Select

**Use Cases**:
- Regulatory archives
- Media asset archiving
- Healthcare information archiving
- Financial records
- Scientific data

**Exam Tips**:
- Glacier Instant Retrieval: archive data with instant access
- Glacier Flexible Retrieval: retrieval in minutes to hours
- Glacier Deep Archive: cheapest, retrieval in hours
- Minimum storage duration charges apply
- Use lifecycle policies to automatically archive
- Vault Lock for compliance (WORM model)
- Cannot access via S3 URL until restored

---

## Summary Comparison Table

| Service | Type | Access | Durability | Use Case |
|---------|------|--------|------------|----------|
| **S3** | Object | Multiple instances, internet | 11 9's | General storage, static content |
| **EBS** | Block | Single EC2 instance | Within AZ | Boot volumes, databases |
| **EFS** | File (NFS) | Multiple EC2 instances | 11 9's, Multi-AZ | Shared file storage |
| **FSx Windows** | File (SMB) | Multiple instances | Multi-AZ | Windows applications |
| **FSx Lustre** | File (POSIX) | Multiple instances | Single/Multi-AZ | HPC, ML workloads |
| **Storage Gateway** | Hybrid | On-premises to cloud | 11 9's (in AWS) | Hybrid cloud storage |
| **Snow Family** | Physical | Offline data transfer | In transit | Large data migrations |

---

## Exam Focus Areas

1. **S3 Storage Classes**: When to use each class based on access patterns
2. **EBS Volume Types**: Match workload requirements to volume type
3. **EFS vs FSx**: Shared storage options and protocol support
4. **Storage Gateway Types**: File vs Volume vs Tape Gateway
5. **Data Transfer**: When to use Snow Family vs network transfer
6. **Backup Strategies**: AWS Backup for centralized management
7. **Performance**: IOPS, throughput, latency requirements
8. **Cost Optimization**: Lifecycle policies, storage class selection
9. **Encryption**: At rest and in transit options
10. **High Availability**: Multi-AZ storage options

---

*Last Updated: January 2026 - SAA-C03 Exam*
