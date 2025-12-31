# AWS SAA-C03: Storage Services Cheat Sheet

## 📋 Table of Contents
- [S3 (Simple Storage Service)](#s3)
- [EBS (Elastic Block Store)](#ebs)
- [EFS (Elastic File System)](#efs)
- [FSx](#fsx)
- [Storage Gateway](#storage-gateway)
- [Snow Family](#snow-family)
- [Backup](#backup)

---

## S3 (Simple Storage Service)

### Definition
Object storage service offering industry-leading scalability, durability (11 9's), and availability.

### Key Features
- **Storage Classes**:
  - **S3 Standard**: Frequent access, 99.99% availability
  - **S3 Intelligent-Tiering**: Auto-moves objects between tiers
  - **S3 Standard-IA**: Infrequent access, lower cost
  - **S3 One Zone-IA**: Single AZ, 20% cheaper than Standard-IA
  - **S3 Glacier Instant Retrieval**: Archive, millisecond retrieval
  - **S3 Glacier Flexible Retrieval**: Minutes to hours retrieval
  - **S3 Glacier Deep Archive**: Lowest cost, 12-48 hours retrieval
- **Versioning**: Keep multiple versions of objects
- **Lifecycle Policies**: Automate transitions between storage classes
- **Replication**: CRR (Cross-Region) and SRR (Same-Region)
- **Encryption**: SSE-S3, SSE-KMS, SSE-C, Client-Side
- **Object Lock**: WORM (Write Once Read Many) compliance
- **MFA Delete**: Extra protection for object deletion
- **Access Points**: Simplify data access for applications
- **S3 Select**: Query data with SQL without retrieving entire object
- **Event Notifications**: Trigger Lambda, SQS, SNS on object events
- **Transfer Acceleration**: Speed up uploads using CloudFront edge locations
- **Presigned URLs**: Temporary access to private objects
- **Multipart Upload**: Required for objects >5GB, recommended >100MB

### Exam Tips & Heuristics
✅ **When to Use S3:**
- Static website hosting
- Data lakes and big data analytics
- Backup and archival
- Application data storage
- Media hosting

⚠️ **Common Pitfalls:**
- S3 is eventually consistent for overwrite PUTS and DELETES (now strong consistency as of Dec 2020)
- Bucket names are globally unique
- 5TB max object size, 5GB max single PUT
- Deleting versioned objects needs extra care
- Public access must be explicitly configured

🎯 **Exam Heuristics:**
- "Store objects/files" = **S3**
- "Cost optimization" + "infrequent access" = **S3 IA** or **Intelligent-Tiering**
- "Archive, rarely accessed" = **Glacier**
- "Compliance, immutability" = **S3 Object Lock**
- "Cross-region backup" = **S3 CRR**
- "Fastest upload from distant location" = **S3 Transfer Acceleration**
- "Query CSV/JSON without downloading" = **S3 Select**
- "Static website" = **S3 + CloudFront**
- "Unknown access patterns" = **S3 Intelligent-Tiering**
- "Minimum storage duration" → IA: 30 days, Glacier: 90 days, Deep Archive: 180 days

### S3 Storage Class Comparison
| Class | Durability | Availability | AZs | Use Case | Retrieval |
|-------|-----------|--------------|-----|----------|-----------|
| Standard | 11 9's | 99.99% | ≥3 | Frequent access | Immediate |
| Intelligent-Tiering | 11 9's | 99.9% | ≥3 | Unknown patterns | Immediate |
| Standard-IA | 11 9's | 99.9% | ≥3 | Infrequent access | Immediate |
| One Zone-IA | 11 9's | 99.5% | 1 | Non-critical | Immediate |
| Glacier Instant | 11 9's | 99.9% | ≥3 | Archive, instant | Milliseconds |
| Glacier Flexible | 11 9's | 99.99% | ≥3 | Archive | 1-5 min to 12 hrs |
| Glacier Deep | 11 9's | 99.99% | ≥3 | Long-term archive | 12-48 hours |

### S3 Security Best Practices
- **Bucket Policies**: Resource-based policies (JSON)
- **IAM Policies**: User/role-based access control
- **ACLs**: Legacy, use bucket policies instead
- **Block Public Access**: Should be enabled by default
- **VPC Endpoints**: Access S3 without internet gateway
- **Access Analyzer**: Identify public/shared access

---

## EBS (Elastic Block Store)

### Definition
Block-level storage volumes for use with EC2 instances, persistent and network-attached.

### Key Features
- **Volume Types**:
  - **gp3**: General Purpose SSD, 3000-16000 IOPS, 125-1000 MB/s (cost-effective)
  - **gp2**: General Purpose SSD, 3-16000 IOPS, burstable
  - **io2 Block Express**: Highest performance, 256,000 IOPS, 4000 MB/s
  - **io2**: Provisioned IOPS, 64,000 IOPS, 99.999% durability
  - **io1**: Provisioned IOPS, 64,000 IOPS
  - **st1**: Throughput Optimized HDD, 500 IOPS, 500 MB/s (big data)
  - **sc1**: Cold HDD, 250 IOPS, 250 MB/s (lowest cost)
- **Snapshots**: Point-in-time backups to S3, incremental
- **Fast Snapshot Restore (FSR)**: No latency on first use (costs extra)
- **Snapshot Archive**: 75% cheaper, 24-72 hours restore
- **Recycle Bin**: Recover deleted snapshots
- **Multi-Attach**: Attach io1/io2 to multiple EC2 in same AZ (16 instances max)
- **Encryption**: At rest using KMS, in-transit between instance and volume
- **Lifecycle Manager**: Automate snapshot creation and retention

### Exam Tips & Heuristics
✅ **When to Use EBS:**
- Database storage
- Boot volumes for EC2
- Applications needing persistent block storage
- High-performance computing

⚠️ **Common Pitfalls:**
- EBS volumes are AZ-specific (can't directly attach to instance in different AZ)
- Snapshots are stored in S3 but not directly accessible
- io1/io2 needed for >16,000 IOPS
- Can only attach to one instance (except Multi-Attach io1/io2)
- Termination protection must be enabled separately

🎯 **Exam Heuristics:**
- "Block storage for EC2" = **EBS**
- "High performance database" = **io2/io2 Block Express**
- "Boot volume" = **gp3** (most cost-effective) or **gp2**
- "Big data, throughput-focused" = **st1**
- "Cost-sensitive, infrequent access" = **sc1**
- "Need >16,000 IOPS" = **io1/io2** or **io2 Block Express**
- "Migrate volume to another AZ" = Create **snapshot**, restore in new AZ
- "Multiple instances need access" = **io1/io2 Multi-Attach** or use **EFS**
- "Sub-millisecond latency" = **Instance Store** (not EBS)

### EBS Volume Type Selection
| Volume Type | IOPS | Throughput | Use Case |
|------------|------|------------|----------|
| gp3 | 3,000-16,000 | 125-1,000 MB/s | General purpose, cost-effective |
| gp2 | 3-16,000 | Up to 250 MB/s | General purpose, burst |
| io2 Block Express | Up to 256,000 | Up to 4,000 MB/s | Mission-critical, highest perf |
| io2/io1 | Up to 64,000 | Up to 1,000 MB/s | Databases, sustained IOPS |
| st1 | Up to 500 | Up to 500 MB/s | Big data, data warehouses |
| sc1 | Up to 250 | Up to 250 MB/s | Cold data, lowest cost |

---

## EFS (Elastic File System)

### Definition
Managed NFS (Network File System) that can be mounted on multiple EC2 instances simultaneously.

### Key Features
- **Shared Storage**: Multiple instances can access concurrently
- **Performance Modes**:
  - **General Purpose**: Low latency (web serving, CMS)
  - **Max I/O**: Higher latency, higher throughput (big data, media processing)
- **Throughput Modes**:
  - **Bursting**: Scales with file system size
  - **Provisioned**: Fixed throughput regardless of size
  - **Elastic**: Automatically scales
- **Storage Classes**:
  - **Standard**: Frequent access
  - **Infrequent Access (EFS-IA)**: Cost-optimized for rarely accessed files
- **Lifecycle Management**: Auto-move files to IA
- **Encryption**: At rest and in transit
- **Access Points**: Application-specific entry points
- **Regional & One Zone**: Multi-AZ or single AZ
- **Backup**: AWS Backup integration

### Exam Tips & Heuristics
✅ **When to Use EFS:**
- Multiple EC2 instances need shared storage
- Content management systems
- Web serving shared files
- Big data analytics
- Lift-and-shift applications using NFS

⚠️ **Common Pitfalls:**
- More expensive than EBS
- Linux only (uses NFS protocol)
- Performance can vary with file system size (in bursting mode)
- Need security group rules for NFS (port 2049)
- Cross-region access not supported natively

🎯 **Exam Heuristics:**
- "Multiple instances" + "shared file storage" = **EFS**
- "Windows file share" = **FSx for Windows** (not EFS)
- "Linux shared storage" = **EFS**
- "Content management" = **EFS**
- "Cost optimization for infrequently accessed files" = **EFS-IA** + Lifecycle
- "Single AZ for cost savings" = **EFS One Zone**
- "Low latency required" = **General Purpose** mode
- "Maximum throughput for big data" = **Max I/O** mode

### EFS vs EBS vs Instance Store
| Feature | EFS | EBS | Instance Store |
|---------|-----|-----|----------------|
| Type | Network file system | Block storage | Ephemeral block |
| Attachment | Many instances | One instance (mostly) | Physically attached |
| Availability | Multi-AZ | Single AZ | Instance-specific |
| Persistence | Yes | Yes | No (lost on stop) |
| Use Case | Shared storage | Boot/data volumes | Cache, temp data |

---

## FSx

### Definition
Managed file systems for specialized workloads (Windows, Lustre, NetApp ONTAP, OpenZFS).

### Key Features

#### FSx for Windows File Server
- **Windows Native**: SMB protocol, Active Directory integration
- **Features**: DFS, shadow copies, ACLs, user quotas
- **Deployment**: Single-AZ or Multi-AZ
- **Backup**: Daily automatic backups to S3
- **Performance**: SSD and HDD storage options

#### FSx for Lustre
- **High Performance**: ML, HPC, video processing, financial modeling
- **S3 Integration**: Can read/write directly to S3
- **Deployment Types**:
  - **Scratch**: Temporary, 6x faster, no replication
  - **Persistent**: Long-term, replicated within AZ
- **Performance**: Sub-millisecond latencies, up to 1TB/s throughput

#### FSx for NetApp ONTAP
- **Enterprise Features**: Point-in-time snapshots, cloning, compression
- **Multi-Protocol**: NFS, SMB, iSCSI
- **Storage Efficiency**: Deduplication, compression
- **SnapMirror**: Replication to other ONTAP systems

#### FSx for OpenZFS
- **High Performance**: Up to 1 million IOPS
- **Linux Compatible**: NFS protocol
- **Snapshots**: Point-in-time snapshots
- **Cost-Effective**: Compression and data deduplication

### Exam Tips & Heuristics
✅ **When to Use FSx:**
- Windows applications requiring SMB → FSx for Windows
- High-performance computing → FSx for Lustre
- Enterprise NAS migration → FSx for NetApp ONTAP
- Linux workloads needing high IOPS → FSx for OpenZFS

⚠️ **Common Pitfalls:**
- FSx for Windows ≠ EFS (Windows vs Linux)
- FSx for Lustre is for HPC, not general-purpose storage
- Different FSx types have different protocols and use cases

🎯 **Exam Heuristics:**
- "Windows" + "SMB" + "Active Directory" = **FSx for Windows**
- "Machine learning" + "HPC" + "high throughput" = **FSx for Lustre**
- "Process data in S3" + "HPC" = **FSx for Lustre**
- "Enterprise NAS" + "NetApp" = **FSx for NetApp ONTAP**
- "Linux" + "high IOPS" + "ZFS" = **FSx for OpenZFS**
- "Shared Windows storage" = **FSx for Windows** (not EFS)

---

## Storage Gateway

### Definition
Hybrid cloud storage service connecting on-premises environments with AWS cloud storage.

### Key Features

#### File Gateway
- **Protocol**: NFS, SMB
- **Backend**: S3 Standard, S3-IA, S3 One Zone-IA
- **Use Case**: File shares, backups, cloud-tiering
- **Caching**: Local cache for recently accessed files
- **Active Directory**: Integration for SMB

#### Volume Gateway
- **Stored Volumes**: Entire dataset on-premises, async backup to S3
- **Cached Volumes**: Frequently accessed data cached locally, full dataset in S3
- **Protocol**: iSCSI
- **Use Case**: Block storage, backups, disaster recovery
- **EBS Snapshots**: Volume backups stored as EBS snapshots

#### Tape Gateway
- **Virtual Tape Library (VTL)**: iSCSI interface
- **Backend**: S3 and Glacier
- **Use Case**: Replace physical tape infrastructure
- **Backup Software**: Compatible with major backup applications

### Exam Tips & Heuristics
✅ **When to Use Storage Gateway:**
- Hybrid cloud storage
- On-premises to cloud migration
- Backup and disaster recovery
- Cloud-tiered storage

⚠️ **Common Pitfalls:**
- Requires local VM or hardware appliance
- Bandwidth considerations for data transfer
- Different gateway types for different use cases
- Cached data vs full data on-premises

🎯 **Exam Heuristics:**
- "Hybrid cloud" + "on-premises" = **Storage Gateway**
- "File shares" + "NFS/SMB" = **File Gateway**
- "Replace tape backup" = **Tape Gateway**
- "Block storage" + "on-premises" = **Volume Gateway**
- "Entire dataset on-premises" = **Stored Volumes**
- "Most data in cloud" = **Cached Volumes**
- "Backup to cloud" = Any Storage Gateway type (depending on protocol)

---

## Snow Family

### Definition
Physical devices for data migration, edge computing, and offline data transfer.

### Key Features

#### Snowcone
- **Capacity**: 8TB HDD or 14TB SSD
- **Size**: Smallest, portable, ruggedized
- **Use Case**: Edge computing, data collection in remote locations
- **DataSync**: Pre-installed agent for online transfer
- **Power**: Battery-powered option

#### Snowball Edge
- **Storage Optimized**: 80TB usable, 40 vCPUs, 1TB SSD
- **Compute Optimized**: 42TB usable, 52 vCPUs, optional GPU
- **Clustering**: Up to 15 devices
- **Use Case**: Large migrations (10TB-petabytes), edge computing
- **EC2 & Lambda**: Run instances locally

#### Snowmobile
- **Capacity**: 100PB per truck
- **Use Case**: Exabyte-scale data transfer
- **Security**: GPS tracking, 24/7 video surveillance, escort vehicle
- **Timeline**: Weeks for setup and data transfer

### Exam Tips & Heuristics
✅ **When to Use Snow Family:**
- Limited bandwidth or connectivity
- Large-scale data migration
- Edge computing in remote locations
- Petabyte to exabyte-scale transfers

⚠️ **Common Pitfalls:**
- Not for real-time data transfer
- Physical shipping time must be considered
- Requires planning and logistics
- Different devices for different scales

🎯 **Exam Heuristics:**
- "<10TB" = **AWS DataSync** or **Direct Connect** (not Snow)
- "10TB-10PB" + "offline transfer" = **Snowball Edge**
- "Small site" + "edge computing" = **Snowcone**
- ">10PB" or "exabyte scale" = **Snowmobile**
- "Limited bandwidth" + "TB-PB data" = **Snowball**
- "Edge processing" + "remote location" = **Snowcone/Snowball Edge**
- "Need to run EC2 on device" = **Snowball Edge Compute**

### Snow Family Comparison
| Device | Capacity | Use Case | Edge Compute |
|--------|----------|----------|--------------|
| Snowcone | 8-14TB | Small transfers, IoT | Yes (2 CPUs) |
| Snowball Edge Storage | 80TB | Large migrations | Yes (40 vCPUs) |
| Snowball Edge Compute | 42TB+28TB NVMe | Edge processing | Yes (52 vCPUs, GPU) |
| Snowmobile | 100PB | Exabyte-scale | No |

---

## AWS Backup

### Definition
Centralized backup service for AWS resources across services and regions.

### Key Features
- **Supported Services**: EC2, EBS, EFS, FSx, S3, RDS, Aurora, DynamoDB, DocumentDB, Neptune, Storage Gateway
- **Backup Plans**: Schedule, retention rules, lifecycle policies
- **Backup Vaults**: Logical containers for backups
- **Cross-Region Backup**: Disaster recovery
- **Cross-Account Backup**: Centralized backup management
- **WORM**: Backup Vault Lock for compliance
- **Restore**: Point-in-time or snapshot-based
- **Monitoring**: CloudWatch, EventBridge, SNS integration
- **Backup Policies**: AWS Organizations integration

### Exam Tips & Heuristics
✅ **When to Use AWS Backup:**
- Centralized backup management
- Compliance and governance requirements
- Multi-service backup strategy
- Cross-region disaster recovery

⚠️ **Common Pitfalls:**
- Not all AWS services supported
- Costs for backup storage and data transfer
- Restore time varies by service
- Requires IAM permissions setup

🎯 **Exam Heuristics:**
- "Centralized backup" + "multiple services" = **AWS Backup**
- "Compliance" + "WORM" = **Backup Vault Lock**
- "Disaster recovery" + "cross-region" = **AWS Backup** with cross-region copy
- "Automated backup retention" = **AWS Backup** plans
- "Single service backup" = Use service-native backup (may be simpler)

---

## 🎓 Storage Service Selection Guide

| Requirement | Service |
|------------|---------|
| Object storage, static content | S3 |
| Block storage for EC2 | EBS |
| Shared file system (Linux) | EFS |
| Shared file system (Windows) | FSx for Windows |
| High-performance computing | FSx for Lustre |
| Hybrid cloud storage | Storage Gateway |
| Large-scale offline data transfer | Snow Family |
| Centralized backup | AWS Backup |

---

## 💡 Key Exam Patterns

### Cost Optimization Patterns
- Infrequent access → S3-IA, EFS-IA
- Archive → S3 Glacier
- Unknown patterns → S3 Intelligent-Tiering
- Lifecycle policies for automatic transitions
- Single AZ for non-critical data (EFS One Zone, S3 One Zone-IA)

### Durability & Availability Patterns
- Multi-AZ for critical data (S3 Standard, EFS Standard)
- Regular snapshots/backups
- Cross-region replication for DR
- Versioning for data protection

### Performance Patterns
- High IOPS → io2, io2 Block Express, Instance Store
- High throughput → st1, FSx for Lustre
- Low latency → EBS, Instance Store
- Shared storage → EFS, FSx

