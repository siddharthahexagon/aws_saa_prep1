# AWS SAA-C03 CHEATSHEET: S3 & STORAGE SERVICES

## 📋 PRINT-READY EXAM REFERENCE

---

# 🪣 AMAZON S3 (SIMPLE STORAGE SERVICE)

## Definition
Object storage service with unlimited storage, high durability (11 9's), and rich feature set.

## S3 Storage Classes - MEMORIZE THIS TABLE!

| Class | Durability | Availability | Min Duration | Min Size | Retrieval | Use Case |
|-------|------------|--------------|--------------|----------|-----------|----------|
| **S3 Standard** | 99.999999999% | 99.99% | None | None | Instant | Frequently accessed |
| **S3 Intelligent-Tiering** | 99.999999999% | 99.9% | None | None | Instant | Unknown patterns |
| **S3 Standard-IA** | 99.999999999% | 99.9% | 30 days | 128 KB | Instant | Infrequent access |
| **S3 One Zone-IA** | 99.999999999% | 99.5% | 30 days | 128 KB | Instant | Recreatable data |
| **S3 Glacier Instant** | 99.999999999% | 99.9% | 90 days | 128 KB | Milliseconds | Archive, instant access |
| **S3 Glacier Flexible** | 99.999999999% | 99.99% | 90 days | 40 KB | 1 min - 12 hrs | Archive |
| **S3 Glacier Deep Archive** | 99.999999999% | 99.99% | 180 days | 40 KB | 12 - 48 hrs | Long-term archive |

## S3 Glacier Retrieval Options

| Tier | Glacier Flexible | Deep Archive | Cost |
|------|-----------------|--------------|------|
| **Expedited** | 1-5 minutes | N/A | $$$ |
| **Standard** | 3-5 hours | 12 hours | $$ |
| **Bulk** | 5-12 hours | 48 hours | $ |

## S3 Intelligent-Tiering Tiers

| Tier | Access Pattern | Cost |
|------|----------------|------|
| **Frequent Access** | Active data | Standard pricing |
| **Infrequent Access** | 30 days no access | 40% savings |
| **Archive Instant** | 90 days no access | 68% savings |
| **Archive Access** | 90+ days (optional) | Similar to Glacier |
| **Deep Archive** | 180+ days (optional) | Similar to Deep Archive |

## S3 Bucket Properties

| Property | Description |
|----------|-------------|
| **Bucket name** | Globally unique, 3-63 chars, lowercase |
| **Region-specific** | Buckets are created in a specific region |
| **Flat structure** | No real folders, just key prefixes |
| **Object size** | 0 bytes to 5 TB |
| **PUT limit** | Single PUT up to 5 GB |
| **Multipart** | Required for > 100 MB, up to 10,000 parts |

## S3 Versioning

| State | Description |
|-------|-------------|
| **Unversioned** | Default state |
| **Enabled** | All object versions stored |
| **Suspended** | Stops new versions, keeps existing |

### Versioning Rules
- Cannot disable, only suspend
- Delete marker added when deleting versioned object
- Permanently delete by specifying version ID
- MFA Delete requires MFA for permanent deletes

## S3 Encryption - CRITICAL FOR EXAM!

| Type | Key Management | Key Storage | Use Case |
|------|----------------|-------------|----------|
| **SSE-S3** | AWS managed | AWS | Default, simplest |
| **SSE-KMS** | AWS KMS | KMS | Audit trail, key rotation |
| **SSE-C** | Customer provided | Customer | Full control |
| **Client-Side** | Customer | Customer | Encrypt before upload |

### Encryption Rules
- **Default encryption**: Can set at bucket level
- **SSE-S3**: `x-amz-server-side-encryption: AES256`
- **SSE-KMS**: `x-amz-server-side-encryption: aws:kms`
- **SSE-C**: Customer provides key in every request
- **Bucket key**: Reduces KMS API calls, saves cost

## S3 Security

### Block Public Access (Account/Bucket Level)
| Setting | Description |
|---------|-------------|
| **BlockPublicAcls** | Block new public ACLs |
| **IgnorePublicAcls** | Ignore existing public ACLs |
| **BlockPublicPolicy** | Block new public bucket policies |
| **RestrictPublicBuckets** | Restrict access to authorized users only |

### Bucket Policies
```json
{
  "Effect": "Allow/Deny",
  "Principal": "*",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::bucket/*",
  "Condition": {}
}
```

### Access Control
| Method | Description | Use Case |
|--------|-------------|----------|
| **Bucket Policy** | Resource-based, JSON | Cross-account, public access |
| **ACLs** | Legacy, object-level | Avoid for new apps |
| **IAM Policies** | User-based | User/role permissions |
| **Access Points** | Simplified access | Large datasets, many users |
| **Object Ownership** | Control ACL behavior | Enforce bucket owner full control |

## S3 Replication

| Type | Description | Use Case |
|------|-------------|----------|
| **CRR** | Cross-Region Replication | DR, compliance, latency |
| **SRR** | Same-Region Replication | Log aggregation, dev/prod sync |

### Replication Requirements
- Versioning enabled on source and destination
- Proper IAM permissions
- Can replicate to different account
- Can replicate to different storage class

### Replication Rules
- Not retroactive (only new objects)
- Delete markers NOT replicated by default (can enable)
- Version ID deletions NOT replicated
- No chaining (A→B→C won't work)
- S3 Batch Replication for existing objects

## S3 Lifecycle Rules

| Action | Description |
|--------|-------------|
| **Transition** | Move to different storage class |
| **Expiration** | Delete objects after time |
| **NoncurrentVersionTransition** | Move old versions |
| **NoncurrentVersionExpiration** | Delete old versions |
| **AbortIncompleteMultipartUpload** | Clean up incomplete uploads |
| **ExpiredObjectDeleteMarker** | Remove delete markers |

### Transition Waterfall (Can only move down)
```
Standard → Standard-IA → Intelligent-Tiering → One Zone-IA → Glacier Instant → Glacier Flexible → Deep Archive
```

## S3 Performance

| Feature | Description |
|---------|-------------|
| **Baseline** | 3,500 PUT/s, 5,500 GET/s per prefix |
| **Multipart Upload** | Parallelize uploads, recommended > 100 MB |
| **S3 Transfer Acceleration** | Use CloudFront edge for faster uploads |
| **Byte-range fetches** | Parallelize downloads |
| **S3 Select** | Query subset of object using SQL |
| **Glacier Select** | Query archived data |

## S3 Event Notifications

| Destination | Use Case |
|-------------|----------|
| **SNS** | Fan-out to multiple subscribers |
| **SQS** | Queue for processing |
| **Lambda** | Serverless processing |
| **EventBridge** | Advanced filtering, multiple targets |

### Event Types
- `s3:ObjectCreated:*`
- `s3:ObjectRemoved:*`
- `s3:ObjectRestore:*`
- `s3:Replication:*`

## S3 Access Logs & Analytics

| Feature | Description |
|---------|-------------|
| **Server Access Logging** | Log all requests to target bucket |
| **Storage Class Analysis** | Recommendations for lifecycle rules |
| **S3 Inventory** | List of objects and metadata |
| **S3 Storage Lens** | Organization-wide storage insights |

## S3 Object Lock & Glacier Vault Lock

| Feature | Description | Use Case |
|---------|-------------|----------|
| **Object Lock** | WORM (Write Once Read Many) | Compliance, prevent deletion |
| **Governance Mode** | Special users can delete | Soft protection |
| **Compliance Mode** | Nobody can delete, even root | Regulatory compliance |
| **Legal Hold** | Indefinite protection | Litigation |
| **Retention Period** | Fixed time protection | Document retention |
| **Glacier Vault Lock** | Vault-level WORM | Archive compliance |

## S3 Pre-signed URLs

| Feature | Description |
|---------|-------------|
| **Purpose** | Temporary access to private objects |
| **Duration** | 1 second to 7 days (console: 12 hours) |
| **Permissions** | Inherit from creator's permissions |
| **Use case** | Temporary downloads, uploads |

---

# 📁 AMAZON EFS (ELASTIC FILE SYSTEM)

## Definition
Managed NFS file system for Linux instances, automatically scales.

## EFS Features

| Feature | Description |
|---------|-------------|
| **Protocol** | NFSv4.1 |
| **Supported** | Linux only (not Windows) |
| **Multi-AZ** | Automatically replicated |
| **Scaling** | Automatic, petabyte scale |
| **Concurrent** | 1000s of connections |
| **Mount targets** | One per AZ |

## EFS Storage Classes

| Class | Description | Use Case |
|-------|-------------|----------|
| **Standard** | Frequently accessed | Active files |
| **Standard-IA** | Infrequent access | Cost savings |
| **One Zone** | Single AZ | Dev/test, non-critical |
| **One Zone-IA** | Single AZ + IA | Maximum savings |

## EFS Performance Modes

| Mode | Description | Use Case |
|------|-------------|----------|
| **General Purpose** | Low latency | Web serving, CMS |
| **Max I/O** | Higher throughput, higher latency | Big data, media processing |

## EFS Throughput Modes

| Mode | Description |
|------|-------------|
| **Bursting** | Throughput scales with storage |
| **Provisioned** | Fixed throughput regardless of size |
| **Elastic** | Automatically scales throughput |

## EFS vs EBS vs S3

| Feature | EFS | EBS | S3 |
|---------|-----|-----|-----|
| **Type** | File | Block | Object |
| **Protocol** | NFS | N/A | HTTP |
| **Multi-attach** | Yes (Linux) | Limited (io1/io2) | N/A |
| **Multi-AZ** | Yes | No (AZ-locked) | Yes |
| **OS Support** | Linux | Any | Any |
| **Use case** | Shared files | EC2 storage | Static files, backup |

---

# 🗄️ AWS FSx

## FSx Types

| Type | Description | Use Case |
|------|-------------|----------|
| **FSx for Windows** | Windows native file system | Windows shares, AD integration |
| **FSx for Lustre** | High-performance parallel FS | HPC, ML, video processing |
| **FSx for NetApp ONTAP** | NetApp features | Hybrid cloud, snapshots |
| **FSx for OpenZFS** | ZFS features | Linux workloads, snapshots |

## FSx for Windows File Server

| Feature | Description |
|---------|-------------|
| **Protocol** | SMB, NFS |
| **AD Integration** | Yes, required |
| **Multi-AZ** | Available |
| **Storage** | SSD or HDD |
| **Deduplication** | Supported |
| **Use case** | Windows file shares, SharePoint |

## FSx for Lustre

| Feature | Description |
|---------|-------------|
| **Performance** | Millions of IOPS, 100s GB/s |
| **S3 Integration** | Read/write directly to S3 |
| **Deployment** | Scratch (temp) or Persistent |
| **Use case** | HPC, ML training, video rendering |

### Lustre Deployment Types
| Type | Description | Use Case |
|------|-------------|----------|
| **Scratch** | Temporary, high burst | Short-term processing |
| **Persistent** | Replicated, long-term | Sensitive data |

---

# 🔄 AWS STORAGE GATEWAY

## Definition
Hybrid cloud storage connecting on-premises to AWS cloud storage.

## Gateway Types

| Type | Protocol | Cache | Backend | Use Case |
|------|----------|-------|---------|----------|
| **S3 File Gateway** | NFS, SMB | Yes | S3 | File shares backed by S3 |
| **FSx File Gateway** | SMB | Yes | FSx for Windows | Windows file shares |
| **Volume Gateway (Cached)** | iSCSI | Yes | S3 + EBS snapshots | Primary data in cloud |
| **Volume Gateway (Stored)** | iSCSI | Full data | S3 snapshots | Primary data on-prem |
| **Tape Gateway** | iSCSI VTL | No | S3, Glacier | Backup to virtual tapes |

## Storage Gateway Key Points

| Point | Description |
|-------|-------------|
| **Hardware appliance** | Available if no VM capacity |
| **Local cache** | Frequently accessed data stored locally |
| **Bandwidth** | Can throttle to manage network usage |
| **Active Directory** | SMB shares can integrate with AD |

---

# ❄️ AWS SNOW FAMILY

## Definition
Physical devices for edge computing and data transfer.

## Snow Devices

| Device | Storage | Compute | Use Case |
|--------|---------|---------|----------|
| **Snowcone** | 8-14 TB | 2 vCPUs, 4 GB | Small, portable, edge |
| **Snowball Edge Storage** | 80 TB | 40 vCPUs | Large data transfer |
| **Snowball Edge Compute** | 42 TB | 52 vCPUs, GPU | Edge computing |
| **Snowmobile** | 100 PB | N/A | Exabyte-scale transfer |

## Snow Family Key Points

| Point | Description |
|-------|-------------|
| **Data transfer** | Physical shipping, not network |
| **Encryption** | 256-bit encryption |
| **E2E tracking** | GPS, tamper-proof |
| **OpsHub** | GUI to manage devices |
| **Edge computing** | Run EC2, Lambda on device |

### When to Use Snow

| Data Size | Network Speed | Time to Transfer | Recommendation |
|-----------|---------------|------------------|----------------|
| 10 TB | 1 Gbps | 1 day | Network OK |
| 100 TB | 1 Gbps | 12 days | Consider Snowball |
| 1 PB | 1 Gbps | 100+ days | Use Snowball |
| 10+ PB | Any | Long | Snowmobile |

---

# 🔐 AWS BACKUP

## Definition
Centralized backup service across AWS services.

## Supported Services
- EC2, EBS
- S3
- RDS, Aurora, DynamoDB
- EFS, FSx
- Storage Gateway
- DocumentDB, Neptune

## Backup Features

| Feature | Description |
|---------|-------------|
| **Backup Plans** | Automated schedules, retention |
| **Backup Vault** | Encrypted container for backups |
| **Vault Lock** | WORM protection for compliance |
| **Cross-region** | Copy backups to other regions |
| **Cross-account** | Copy backups to other accounts |
| **PITR** | Point-in-time recovery support |

---

# 🎯 STORAGE QUICK EXAM HEURISTICS

| Keyword/Scenario | Answer |
|------------------|--------|
| Frequently accessed objects | **S3 Standard** |
| Unknown access patterns | **S3 Intelligent-Tiering** |
| Infrequent access, millisecond retrieval | **S3 Standard-IA** |
| Archive, instant retrieval | **S3 Glacier Instant Retrieval** |
| Archive, flexible retrieval | **S3 Glacier Flexible Retrieval** |
| Long-term archive, cheapest | **S3 Glacier Deep Archive** |
| WORM compliance, can't delete | **S3 Object Lock Compliance Mode** |
| Cross-region replication | **Enable versioning + CRR** |
| Shared Linux file system | **EFS** |
| Shared Windows file system | **FSx for Windows** |
| HPC, ML, high-performance | **FSx for Lustre** |
| Hybrid cloud file shares | **Storage Gateway** |
| Tape backup replacement | **Tape Gateway** |
| Petabyte data migration | **Snowball / Snowmobile** |
| Edge computing, remote | **Snowball Edge Compute** |
| Encrypt S3 with audit trail | **SSE-KMS** |
| Customer-managed encryption keys | **SSE-C or Client-side** |
| Faster S3 uploads globally | **S3 Transfer Acceleration** |
| Query data in S3 | **S3 Select or Athena** |
| Centralized backup | **AWS Backup** |

---

# 📊 STORAGE SELECTION MATRIX

| Requirement | Service |
|-------------|---------|
| Object storage | S3 |
| Block storage for EC2 | EBS |
| Shared file system (Linux) | EFS |
| Shared file system (Windows) | FSx for Windows |
| High-performance parallel | FSx for Lustre |
| Hybrid cloud storage | Storage Gateway |
| Physical data transfer | Snow Family |
| Centralized backup | AWS Backup |

---

# ✅ EXAM DAY QUICK CHECKS

```
□ Know all S3 storage classes and retrieval times
□ Know S3 encryption types (SSE-S3, SSE-KMS, SSE-C)
□ Know S3 replication requirements (versioning!)
□ Know EFS vs EBS vs S3 differences
□ Know FSx types and use cases
□ Know Storage Gateway types
□ Know Snow family devices and capacities
□ Know Object Lock modes (Governance vs Compliance)
```

---

*Last Updated: December 2025*
*Exam Version: SAA-C03*
