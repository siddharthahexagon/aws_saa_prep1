# AWS SAA-C03: Storage Services Decision Trees

## 🌳 Quick Navigation
- [Main Storage Decision Tree](#main-storage-decision-tree)
- [S3 Storage Class Selection](#s3-storage-class-selection)
- [EBS Volume Type Selection](#ebs-volume-type-selection)
- [File System Selection](#file-system-selection)
- [Data Transfer Method Selection](#data-transfer-method-selection)

---

## Main Storage Decision Tree

```
START: Need storage?
│
├─→ What type of data access?
│   │
│   ├─→ Object storage (files via HTTP/S3 API)?
│   │   └─→ **Amazon S3**
│   │       └─→ Go to "S3 Storage Class Selection"
│   │
│   ├─→ Block storage (attach to EC2)?
│   │   └─→ **Amazon EBS**
│   │       └─→ Go to "EBS Volume Type Selection"
│   │
│   ├─→ File system (shared access, NFS/SMB)?
│   │   └─→ Go to "File System Selection"
│   │
│   └─→ Backup/archive only?
│       └─→ **AWS Backup** or **S3 Glacier**
│
├─→ Performance requirements?
│   ├─→ Sub-millisecond latency → EBS (io2/io2 Block Express)
│   ├─→ Millisecond latency → S3, EFS
│   └─→ Archive (hours to retrieve) → S3 Glacier Deep Archive
│
├─→ Access pattern?
│   ├─→ Single EC2 instance → EBS
│   ├─→ Multiple EC2 instances → EFS or FSx
│   ├─→ Internet/any AWS service → S3
│   └─→ On-premises + AWS → Storage Gateway or FSx
│
└─→ Data location?
    ├─→ Cloud-native → S3, EBS, EFS
    ├─→ Hybrid (on-prem + cloud) → Storage Gateway, DataSync
    └─→ Offline/disconnected → Snow Family
```

---

## S3 Storage Class Selection

```
START: Choosing S3 storage class?
│
├─→ How frequently accessed?
│   │
│   ├─→ Accessed frequently (daily/weekly)?
│   │   └─→ **S3 Standard**
│   │       ├─→ 99.99% availability
│   │       ├─→ 11 9's durability
│   │       ├─→ Use case: Active data, websites, content distribution
│   │       └─→ Cost: Highest storage, lowest retrieval
│   │
│   ├─→ Accessed infrequently (monthly)?
│   │   ├─→ Need multi-AZ redundancy?
│   │   │   ├─→ YES → **S3 Standard-IA** (Infrequent Access)
│   │   │   │         ├─→ 99.9% availability
│   │   │   │         ├─→ 11 9's durability
│   │   │   │         ├─→ Use case: Backups, DR files, long-term storage
│   │   │   │         └─→ Cost: Lower storage, retrieval fee
│   │   │   │
│   │   │   └─→ NO → **S3 One Zone-IA**
│   │   │             ├─→ 99.5% availability (single AZ)
│   │   │             ├─→ 11 9's durability (but AZ loss = data loss)
│   │   │             ├─→ Use case: Reproducible data, non-critical backups
│   │   │             └─→ Cost: 20% cheaper than Standard-IA
│   │   │
│   │   └─→ Need instant access?
│   │       ├─→ YES → Use above IA classes
│   │       └─→ NO → Continue to Glacier...
│   │
│   ├─→ Accessed rarely (quarterly/yearly)?
│   │   ├─→ Need retrieval in minutes?
│   │   │   └─→ **S3 Glacier Instant Retrieval**
│   │   │       ├─→ Millisecond access (like S3 Standard)
│   │   │       ├─→ Use case: Medical images, news archives
│   │   │       ├─→ Minimum 90-day storage
│   │   │       └─→ Cost: Much cheaper storage, retrieval fee
│   │   │
│   │   ├─→ Need retrieval in 1-5 minutes (or hours)?
│   │   │   └─→ **S3 Glacier Flexible Retrieval** (formerly Glacier)
│   │   │       ├─→ Retrieval: 1-5 min (Expedited), 3-5 hrs (Standard), 5-12 hrs (Bulk)
│   │   │       ├─→ Use case: Long-term backups, archives
│   │   │       ├─→ Minimum 90-day storage
│   │   │       └─→ Cost: Very low storage, retrieval fee + time
│   │   │
│   │   └─→ Rarely accessed (7-10 years+)?
│   │       └─→ **S3 Glacier Deep Archive**
│   │           ├─→ Retrieval: 12 hrs (Standard), 48 hrs (Bulk)
│   │           ├─→ Use case: Compliance archives, long-term retention
│   │           ├─→ Minimum 180-day storage
│   │           └─→ Cost: Lowest storage ($1/TB/month), highest retrieval cost
│   │
│   └─→ Access pattern unknown or changing?
│       └─→ **S3 Intelligent-Tiering**
│           ├─→ Automatic tiering based on access patterns
│           ├─→ No retrieval fees
│           ├─→ Small monthly monitoring fee
│           ├─→ Tiers: Frequent, Infrequent (30 days), Archive (90 days), Deep Archive (180 days)
│           └─→ Use case: Unpredictable access, hands-off optimization
│
└─→ Special considerations?
    ├─→ Need versioning → Enable on any class
    ├─→ Need replication → S3 Cross-Region Replication (CRR) or Same-Region (SRR)
    ├─→ Need encryption → SSE-S3, SSE-KMS, or SSE-C
    └─→ Need lifecycle transitions → Configure S3 Lifecycle policies
```

### S3 Storage Class Comparison Matrix

| Storage Class | Availability | Min Duration | Retrieval Time | Use Case | Cost/GB/Month |
|--------------|--------------|--------------|----------------|----------|---------------|
| **Standard** | 99.99% | None | Instant | Active data | ~$0.023 |
| **Standard-IA** | 99.9% | 30 days | Instant | Infrequent access | ~$0.0125 |
| **One Zone-IA** | 99.5% | 30 days | Instant | Reproducible data | ~$0.01 |
| **Glacier Instant** | 99.9% | 90 days | Instant | Archive + instant access | ~$0.004 |
| **Glacier Flexible** | 99.99% | 90 days | 1 min - 12 hrs | Long-term archive | ~$0.0036 |
| **Glacier Deep Archive** | 99.99% | 180 days | 12-48 hrs | Compliance archive | ~$0.00099 |
| **Intelligent-Tiering** | 99.9% | None | Instant (auto) | Unknown patterns | ~$0.023 + fees |

---

## EBS Volume Type Selection

```
START: Choosing EBS volume type?
│
├─→ What's the priority?
│   │
│   ├─→ IOPS (Input/Output Operations Per Second)?
│   │   └─→ High IOPS required?
│   │       │
│   │       ├─→ > 64,000 IOPS or > 4,000 MB/s?
│   │       │   └─→ **io2 Block Express**
│   │       │       ├─→ Up to 256,000 IOPS
│   │       │       ├─→ Up to 4,000 MB/s throughput
│   │       │       ├─→ 99.999% durability
│   │       │       ├─→ Use case: Largest mission-critical databases
│   │       │       └─→ Cost: Highest
│   │       │
│   │       ├─→ Up to 64,000 IOPS?
│   │       │   └─→ **io2**
│   │       │       ├─→ 64,000 IOPS per volume
│   │       │       ├─→ 1,000 MB/s throughput
│   │       │       ├─→ 99.999% durability
│   │       │       ├─→ Use case: I/O-intensive databases (MongoDB, Cassandra)
│   │       │       └─→ Cost: High (pay for provisioned IOPS)
│   │       │
│   │       └─→ Up to 16,000 IOPS?
│   │           └─→ **io1** (older generation)
│   │               ├─→ Consider using io2 instead (same price, better durability)
│   │               └─→ 99.9% durability
│   │
│   ├─→ Throughput (MB/s)?
│   │   └─→ High throughput, sequential I/O?
│   │       └─→ **st1** (Throughput Optimized HDD)
│   │           ├─→ Up to 500 MB/s
│   │           ├─→ 500 IOPS
│   │           ├─→ Use case: Big data, data warehouses, log processing
│   │           ├─→ Cannot be boot volume
│   │           └─→ Cost: Low
│   │
│   ├─→ Cost (cheapest)?
│   │   └─→ **sc1** (Cold HDD)
│   │       ├─→ Up to 250 MB/s
│   │       ├─→ 250 IOPS
│   │       ├─→ Use case: Infrequently accessed data, lowest cost storage
│   │       ├─→ Cannot be boot volume
│   │       └─→ Cost: Lowest
│   │
│   └─→ Balanced (most common)?
│       └─→ **gp3** (General Purpose SSD - latest)
│           ├─→ Baseline: 3,000 IOPS, 125 MB/s (regardless of size)
│           ├─→ Can provision up to 16,000 IOPS, 1,000 MB/s independently
│           ├─→ Use case: Boot volumes, dev/test, low-latency apps
│           ├─→ Cost: Lower than gp2, better performance
│           └─→ **RECOMMENDED for most workloads**
│
└─→ Legacy/existing volumes?
    └─→ **gp2** (General Purpose SSD - older)
        ├─→ IOPS scales with size (3 IOPS/GB)
        ├─→ Up to 16,000 IOPS
        ├─→ Burst to 3,000 IOPS for small volumes
        └─→ Consider migrating to gp3
```

### EBS Volume Type Comparison

| Type | Category | Max IOPS | Max Throughput | Durability | Use Case | Cost |
|------|----------|----------|----------------|------------|----------|------|
| **gp3** | SSD | 16,000 | 1,000 MB/s | 99.8-99.9% | General purpose | $ |
| **gp2** | SSD | 16,000 | 250 MB/s | 99.8-99.9% | General purpose | $$ |
| **io2 Block Express** | SSD | 256,000 | 4,000 MB/s | 99.999% | Mission-critical | $$$$ |
| **io2** | SSD | 64,000 | 1,000 MB/s | 99.999% | I/O intensive | $$$ |
| **io1** | SSD | 64,000 | 1,000 MB/s | 99.9% | I/O intensive (legacy) | $$$ |
| **st1** | HDD | 500 | 500 MB/s | 99.8-99.9% | Throughput optimized | $ |
| **sc1** | HDD | 250 | 250 MB/s | 99.8-99.9% | Cold data | $ |

### Quick Selection Heuristic

```
Boot volume → gp3
Database (transactional) → io2 or gp3
Database (large, critical) → io2 Block Express
Big data, log processing → st1
Archive, infrequent access → sc1
Dev/test, general apps → gp3
```

---

## File System Selection

```
START: Need shared file system?
│
├─→ Operating system?
│   │
│   ├─→ Linux/Unix?
│   │   ├─→ Standard NFS file system?
│   │   │   └─→ **Amazon EFS** (Elastic File System)
│   │   │       ├─→ Fully managed NFS v4
│   │   │       ├─→ Scales automatically (petabytes)
│   │   │       ├─→ Multi-AZ by default (high availability)
│   │   │       ├─→ Performance modes: General Purpose, Max I/O
│   │   │       ├─→ Throughput modes: Bursting, Provisioned, Elastic
│   │   │       ├─→ Storage classes: Standard, IA
│   │   │       └─→ Use case: Content management, web serving, data analytics
│   │   │
│   │   └─→ High-performance computing (HPC)?
│   │       └─→ **FSx for Lustre**
│   │           ├─→ High-performance parallel file system
│   │           ├─→ Sub-millisecond latency, 100s GB/s throughput
│   │           ├─→ Integrates with S3
│   │           ├─→ Deployment: Scratch (temporary), Persistent (durable)
│   │           └─→ Use case: ML, HPC, video processing, financial modeling
│   │
│   ├─→ Windows?
│   │   └─→ **FSx for Windows File Server**
│   │       ├─→ Fully managed Windows native file system
│   │       ├─→ SMB protocol, NTFS
│   │       ├─→ Active Directory integration
│   │       ├─→ DFS namespaces, quotas, encryption
│   │       ├─→ Multi-AZ for HA
│   │       └─→ Use case: Windows applications, SharePoint, SQL Server
│   │
│   ├─→ Need enterprise features (NetApp)?
│   │   └─→ **FSx for NetApp ONTAP**
│   │       ├─→ Fully managed NetApp ONTAP
│   │       ├─→ NFS, SMB, iSCSI protocols
│   │       ├─→ Works with Linux, Windows, macOS
│   │       ├─→ Features: Snapshots, replication, compression, deduplication
│   │       └─→ Use case: Multi-protocol access, data migration from on-prem NetApp
│   │
│   └─→ Need OpenZFS?
│       └─→ **FSx for OpenZFS**
│           ├─→ Fully managed OpenZFS file system
│           ├─→ NFS protocol
│           ├─→ Up to 1M IOPS, sub-millisecond latency
│           ├─→ Features: Snapshots, clones, compression
│           └─→ Use case: Move from on-prem ZFS, data-intensive workloads
│
├─→ Hybrid (on-premises + AWS)?
│   └─→ **AWS Storage Gateway**
│       ├─→ File Gateway (NFS/SMB) → Store in S3
│       ├─→ Volume Gateway (iSCSI) → Store in S3, local cache
│       ├─→ Tape Gateway (VTL) → Store in S3 Glacier
│       └─→ Use case: Hybrid cloud storage, backup, DR
│
└─→ Performance requirements?
    ├─→ Shared access, moderate performance → EFS
    ├─→ High IOPS, low latency → FSx for Lustre or OpenZFS
    ├─→ Windows-native → FSx for Windows
    └─→ Enterprise features → FSx for NetApp ONTAP
```

### File System Comparison Matrix

| File System | Protocol | OS | Performance | HA | Use Case |
|-------------|----------|----|-----------|----|----------|
| **EFS** | NFS v4 | Linux | Moderate-High | Multi-AZ | General purpose Linux file sharing |
| **FSx for Windows** | SMB | Windows | High | Multi-AZ | Windows apps, AD integration |
| **FSx for Lustre** | Lustre | Linux | Very High | Single AZ | HPC, ML, big data processing |
| **FSx for NetApp ONTAP** | NFS, SMB, iSCSI | Any | High | Multi-AZ | Multi-protocol, enterprise features |
| **FSx for OpenZFS** | NFS | Linux | Very High | Single AZ | ZFS migration, high IOPS |
| **Storage Gateway** | NFS, SMB, iSCSI | Any | Moderate | N/A | Hybrid cloud, on-prem to AWS |

---

## Data Transfer Method Selection

```
START: Need to transfer data to AWS?
│
├─→ Data size?
│   │
│   ├─→ < 10 TB?
│   │   ├─→ Good internet bandwidth (> 100 Mbps)?
│   │   │   └─→ **Online transfer**
│   │   │       ├─→ Direct upload to S3
│   │   │       ├─→ AWS DataSync (automated, scheduled)
│   │   │       ├─→ AWS Transfer Family (SFTP/FTPS/FTP)
│   │   │       └─→ S3 Transfer Acceleration (if global, faster upload)
│   │   │
│   │   └─→ Poor internet (< 100 Mbps)?
│   │       └─→ **AWS Snowcone**
│   │           ├─→ 8 TB usable storage (HDD) or 14 TB (SSD)
│   │           ├─→ Small, rugged device
│   │           ├─→ Can run EC2 & Lambda at edge
│   │           └─→ Use case: Edge computing, IoT, small migrations
│   │
│   ├─→ 10 TB - 80 TB?
│   │   └─→ **AWS Snowball Edge**
│   │       ├─→ Storage Optimized: 80 TB usable
│   │       ├─→ Compute Optimized: 42 TB + GPU
│   │       ├─→ Can cluster up to 15 nodes
│   │       ├─→ Use case: Large migrations, edge computing, data processing
│   │       └─→ Transfer time: ~1 week (vs weeks/months over internet)
│   │
│   └─→ > 100 TB (exabyte scale)?
│       └─→ **AWS Snowmobile**
│           ├─→ 100 PB capacity (semi-truck trailer)
│           ├─→ Physical security, GPS tracking
│           ├─→ Use case: Data center migration, exabyte-scale transfers
│           └─→ Transfer time: Weeks (vs years over internet)
│
├─→ Ongoing sync (on-premises ↔ AWS)?
│   └─→ **AWS DataSync**
│       ├─→ Automated data transfer, scheduling
│       ├─→ Bandwidth throttling, encryption
│       ├─→ 10x faster than open-source tools
│       ├─→ Source: NFS, SMB, HDFS, S3, EFS, FSx
│       ├─→ Destination: S3, EFS, FSx
│       └─→ Use case: Hybrid workflows, data replication, migration
│
├─→ Need hybrid file access?
│   └─→ **Storage Gateway**
│       ├─→ File Gateway: Access S3 as local NFS/SMB
│       ├─→ Volume Gateway: iSCSI volumes backed by S3
│       └─→ Tape Gateway: Virtual tape library to S3 Glacier
│
├─→ Need dedicated network connection?
│   └─→ **AWS Direct Connect**
│       ├─→ Private, dedicated network (1-100 Gbps)
│       ├─→ Consistent performance, lower latency
│       ├─→ Use case: Large data transfers, hybrid cloud
│       └─→ Takes weeks to provision
│
└─→ File transfer protocol needed (SFTP)?
    └─→ **AWS Transfer Family**
        ├─→ Managed SFTP, FTPS, FTP service
        ├─→ Direct transfer to/from S3 or EFS
        ├─→ Integration with AD, LDAP, custom auth
        └─→ Use case: B2B file transfers, legacy app integration
```

### Data Transfer Decision Matrix

| Data Size | Internet Speed | Recommended | Transfer Time | Cost |
|-----------|---------------|-------------|---------------|------|
| < 10 TB | Good | Online (S3 upload, DataSync) | Hours-Days | $ |
| < 10 TB | Poor | Snowcone | ~1 week (shipping) | $$ |
| 10-80 TB | Any | Snowball Edge | ~1 week | $$$ |
| > 100 TB | Any | Snowmobile | Weeks | $$$$ |
| Ongoing sync | Any | DataSync | Continuous | $ per GB |
| Hybrid access | Any | Storage Gateway | N/A | $ per GB |
| SFTP/FTP | Any | Transfer Family | N/A | $ per hour + GB |

---

## Storage Gateway Types Decision

```
START: Which Storage Gateway?
│
├─→ File-based access?
│   └─→ **File Gateway**
│       ├─→ Protocol: NFS v3/v4.1, SMB
│       ├─→ Backed by: S3 (with local cache)
│       ├─→ Use case: Backup, archive, file sharing to S3
│       └─→ Cache frequently accessed files locally
│
├─→ Block-based access (volumes)?
│   └─→ **Volume Gateway**
│       ├─→ Protocol: iSCSI
│       ├─→ Two modes:
│       │   ├─→ Cached Volumes: Primary data in S3, cache on-prem
│       │   │   └─→ Use case: Minimize on-prem storage
│       │   └─→ Stored Volumes: Primary data on-prem, async backup to S3
│       │       └─→ Use case: Low-latency access, full dataset on-prem
│       └─→ Point-in-time snapshots (EBS snapshots)
│
└─→ Tape backup (virtual tapes)?
    └─→ **Tape Gateway**
        ├─→ Virtual Tape Library (VTL)
        ├─→ Works with existing backup software (Veeam, NetBackup, etc.)
        ├─→ Backed by: S3, S3 Glacier, S3 Glacier Deep Archive
        └─→ Use case: Replace physical tape infrastructure
```

---

## Backup Strategy Decision

```
START: Backup strategy?
│
├─→ Backup AWS resources?
│   └─→ **AWS Backup**
│       ├─→ Centralized backup management
│       ├─→ Supports: EBS, RDS, DynamoDB, EFS, FSx, Storage Gateway
│       ├─→ Automated backup scheduling
│       ├─→ Cross-region and cross-account backup
│       └─→ Compliance reporting
│
├─→ Backup to S3 with lifecycle?
│   └─→ **S3 + Lifecycle Policies**
│       ├─→ Transition to IA after 30 days
│       ├─→ Transition to Glacier after 90 days
│       └─→ Expire old versions/delete after X days
│
├─→ On-premises backup to AWS?
│   └─→ **Storage Gateway (Tape Gateway)**
│       └─→ Virtual tapes → S3 Glacier
│
└─→ Database backup?
    ├─→ RDS/Aurora → Automated backups, snapshots
    ├─→ DynamoDB → Point-in-time recovery, on-demand backup
    └─→ Consider AWS Backup for centralized management
```

---

## Exam Scenarios - Quick Answers

### Scenario 1: Store website images, accessed frequently
**Answer**: S3 Standard

### Scenario 2: Backup files, accessed once a month
**Answer**: S3 Standard-IA

### Scenario 3: Compliance archives, 7-year retention, rarely accessed
**Answer**: S3 Glacier Deep Archive

### Scenario 4: Unknown access pattern, automatic cost optimization
**Answer**: S3 Intelligent-Tiering

### Scenario 5: High IOPS database (100,000 IOPS)
**Answer**: io2 Block Express

### Scenario 6: Boot volume for web server
**Answer**: gp3

### Scenario 7: Big data processing, high throughput
**Answer**: st1 (HDD) or FSx for Lustre

### Scenario 8: Shared file system for Linux servers
**Answer**: Amazon EFS

### Scenario 9: Windows file server with Active Directory
**Answer**: FSx for Windows File Server

### Scenario 10: Transfer 50 TB from on-prem to AWS, poor internet
**Answer**: AWS Snowball Edge

### Scenario 11: Ongoing sync between on-prem NFS and S3
**Answer**: AWS DataSync

### Scenario 12: Hybrid access, files appear local but stored in S3
**Answer**: Storage Gateway (File Gateway)

### Scenario 13: Machine learning, high-performance file system
**Answer**: FSx for Lustre

### Scenario 14: Centralized backup for all AWS resources
**Answer**: AWS Backup

### Scenario 15: Cost-effective storage for rarely accessed data
**Answer**: S3 One Zone-IA (if reproducible) or Glacier

---

## 🎯 Key Takeaways

### S3 Storage Classes:
- **Standard**: Frequent access, highest availability
- **Standard-IA**: Infrequent access, still need instant retrieval
- **Glacier Instant**: Archive but need instant access
- **Glacier Flexible**: Archive, can wait minutes/hours
- **Glacier Deep Archive**: Long-term compliance, wait 12+ hours
- **Intelligent-Tiering**: Don't know access pattern, automatic optimization

### EBS Volume Types:
- **gp3**: Default choice for most workloads
- **io2/io2 Block Express**: High IOPS databases
- **st1**: Big data, high throughput
- **sc1**: Cold data, lowest cost

### File Systems:
- **EFS**: Linux shared file system
- **FSx for Windows**: Windows shared file system
- **FSx for Lustre**: HPC, ML, high performance
- **FSx for NetApp ONTAP**: Multi-protocol, enterprise features

### Data Transfer:
- **< 10 TB + good internet**: Online transfer (S3, DataSync)
- **10-80 TB or poor internet**: Snowball Edge
- **> 100 TB**: Snowmobile
- **Ongoing sync**: DataSync
- **Hybrid access**: Storage Gateway

---

*Use these decision trees to quickly identify the right storage service!*
