# AWS SAA-C03: Migration & Data Transfer Decision Trees

## 🌳 Quick Navigation
- [Migration Strategy Selection (6 Rs)](#migration-strategy-selection-6-rs)
- [Data Transfer Decision](#data-transfer-decision)
- [Database Migration](#database-migration)
- [Application Migration](#application-migration)
- [Hybrid Architecture](#hybrid-architecture)

---

## Migration Strategy Selection (6 Rs)

```
START: Which migration strategy?
│
├─→ Want to eliminate the workload?
│   └─→ **Retire**
│       ├─→ Turn off applications no longer needed
│       ├─→ Reduce cost immediately
│       └─→ Use case: Redundant, unused, or obsolete applications
│
├─→ Keep on-premises (not ready to migrate)?
│   └─→ **Retain** (Revisit)
│       ├─→ Keep in current environment
│       ├─→ Migrate later
│       └─→ Use case: Complex apps, recently upgraded, no business case
│
├─→ Move to SaaS instead?
│   └─→ **Repurchase** (Drop and Shop)
│       ├─→ Move to different product (usually SaaS)
│       ├─→ Examples:
│       │   ├─→ On-prem CRM → Salesforce
│       │   ├─→ On-prem email → Microsoft 365
│       │   └─→ On-prem HR → Workday
│       └─→ Use case: Replace with cloud-native SaaS
│
├─→ Lift-and-shift (quick migration)?
│   └─→ **Rehost** (Lift and Shift)
│       ├─→ Move to cloud with minimal changes
│       ├─→ Tools:
│       │   ├─→ AWS Application Migration Service (MGN)
│       │   ├─→ AWS Server Migration Service (SMS) - legacy
│       │   └─→ VMware Cloud on AWS
│       ├─→ Benefits:
│       │   ├─→ Fastest migration
│       │   ├─→ Lower risk
│       │   ├─→ Learn cloud operations
│       │   └─→ Optimize later
│       └─→ Use case: Quick migration, legacy apps, time-sensitive
│
├─→ Lift-tinker-and-shift (some optimization)?
│   └─→ **Replatform** (Lift, Tinker, and Shift)
│       ├─→ Make few cloud optimizations
│       ├─→ No core architecture changes
│       ├─→ Examples:
│       │   ├─→ Self-managed database → RDS
│       │   ├─→ On-prem MySQL → Aurora MySQL
│       │   ├─→ App server → Elastic Beanstalk
│       │   └─→ .NET Framework → .NET Core on Elastic Beanstalk
│       ├─→ Benefits:
│       │   ├─→ Some cloud benefits
│       │   ├─→ Reduced operations
│       │   └─→ Moderate effort
│       └─→ Use case: Quick wins with some cloud optimization
│
└─→ Full re-architecture for cloud?
    └─→ **Refactor/Re-architect**
        ├─→ Redesign application for cloud-native
        ├─→ Examples:
        │   ├─→ Monolith → Microservices
        │   ├─→ On-prem app → Serverless (Lambda, API Gateway)
        │   ├─→ Traditional DB → DynamoDB
        │   └─→ Custom queue → SQS/SNS
        ├─→ Benefits:
        │   ├─→ Full cloud-native benefits
        │   ├─→ Best scalability and agility
        │   └─→ Long-term cost optimization
        ├─→ Challenges:
        │   ├─→ Highest effort
        │   ├─→ Requires development skills
        │   └─→ Longer timeline
        └─→ Use case: Strategic applications, cloud-native transformation

### 6 Rs Decision Matrix

| Strategy | Effort | Time | Risk | Cloud Benefits | Use Case |
|----------|--------|------|------|----------------|----------|
| **Retire** | None | Immediate | None | N/A | Decommission |
| **Retain** | None | N/A | None | None | Not ready |
| **Repurchase** | Low | Short | Low | SaaS benefits | Replace with SaaS |
| **Rehost** | Low | Short | Low | Infrastructure | Quick migration |
| **Replatform** | Medium | Medium | Medium | Some | Managed services |
| **Refactor** | High | Long | High | Full | Cloud-native |
```

---

## Data Transfer Decision

```
START: Need to transfer data to AWS?
│
├─→ How much data?
│   │
│   ├─→ Small data (< 10 GB)?
│   │   └─→ **Direct Upload**
│   │       ├─→ AWS CLI / SDK
│   │       ├─→ S3 Console
│   │       └─→ Use case: Small files, configuration data
│   │
│   ├─→ Medium data (10 GB - 10 TB)?
│   │   └─→ **Network Transfer**
│   │       ├─→ Direct Connect (if available)
│   │       ├─→ S3 Transfer Acceleration
│   │       ├─→ AWS DataSync
│   │       └─→ Use case: Moderate data, good internet
│   │
│   ├─→ Large data (10 TB - 80 TB)?
│   │   └─→ **AWS Snowball Edge**
│   │       ├─→ 80 TB usable storage
│   │       ├─→ Compute capabilities (optional)
│   │       ├─→ Encryption at rest
│   │       ├─→ Shipping time: ~1 week
│   │       └─→ Use case: Large one-time transfers, limited bandwidth
│   │
│   ├─→ Very large data (80 TB - 1 PB)?
│   │   └─→ **Multiple Snowball Edge devices**
│   │       ├─→ Order multiple devices
│   │       ├─→ Or use Snowball Edge Storage Optimized
│   │       └─→ Use case: Data center migration
│   │
│   └─→ Massive data (> 1 PB)?
│       └─→ **AWS Snowmobile**
│           ├─→ 100 PB per Snowmobile
│           ├─→ Shipping container on truck
│           ├─→ 45-foot ruggedized container
│           └─→ Use case: Exabyte-scale migration, data center shutdown
│
├─→ Transfer Method by Speed
│   │
│   │ Data Size    │ Internet    │ Snow Device   │ Faster Choice │
│   │──────────────│─────────────│───────────────│───────────────│
│   │ 10 TB        │ ~1 day      │ ~1 week       │ Internet      │
│   │ 100 TB       │ ~10 days    │ ~1 week       │ Snowball      │
│   │ 1 PB         │ ~100 days   │ ~2 weeks      │ Snow devices  │
│   │ 10 PB        │ ~3 years    │ ~2 months     │ Snowmobile    │
│
├─→ Ongoing vs One-Time?
│   │
│   ├─→ One-time large transfer?
│   │   └─→ **Snow Family** (Snowcone, Snowball, Snowmobile)
│   │
│   ├─→ Ongoing/continuous sync?
│   │   └─→ **AWS DataSync**
│   │       ├─→ Automated data transfer
│   │       ├─→ On-prem ↔ AWS (NFS, SMB, HDFS)
│   │       ├─→ AWS ↔ AWS (S3, EFS, FSx)
│   │       ├─→ Scheduling and bandwidth control
│   │       ├─→ Data validation
│   │       └─→ Use case: Recurring transfers, data replication
│   │
│   └─→ Hybrid file access (on-prem + cloud)?
│       └─→ **AWS Storage Gateway**
│           ├─→ File Gateway: NFS/SMB → S3
│           ├─→ Volume Gateway: iSCSI → S3/EBS
│           ├─→ Tape Gateway: VTL → S3 Glacier
│           └─→ Use case: Hybrid storage, backup to cloud
│
├─→ Transfer Acceleration Needed?
│   │
│   ├─→ Global uploads to S3?
│   │   └─→ **S3 Transfer Acceleration**
│   │       ├─→ Uses CloudFront edge locations
│   │       ├─→ 50-500% speed improvement
│   │       └─→ Use case: Global uploads, large files
│   │
│   └─→ Accelerate any TCP traffic?
│       └─→ **AWS Global Accelerator**
│           ├─→ Uses AWS global network
│           ├─→ Static anycast IPs
│           └─→ Use case: Application acceleration
│
└─→ Snow Family Comparison
    │
    │ Device          │ Storage    │ Compute │ Use Case              │
    │─────────────────│────────────│─────────│───────────────────────│
    │ Snowcone        │ 8-14 TB    │ Optional│ Edge, small transfers │
    │ Snowball Edge   │ 80 TB      │ Optional│ Large transfers       │
    │ Snowmobile      │ 100 PB     │ No      │ Exabyte-scale        │
```

---

## Database Migration

```
START: Migrate database to AWS?
│
├─→ Same database engine?
│   └─→ **Homogeneous Migration**
│       ├─→ Example: MySQL → RDS MySQL, Oracle → RDS Oracle
│       ├─→ Tool: **AWS DMS** (Database Migration Service)
│       ├─→ Simple, schema-compatible
│       ├─→ Minimal downtime with CDC (Change Data Capture)
│       └─→ Steps:
│           1. Create target database in AWS
│           2. Create DMS replication instance
│           3. Create source and target endpoints
│           4. Create and run migration task
│           5. Cutover when caught up
│
├─→ Different database engine?
│   └─→ **Heterogeneous Migration**
│       ├─→ Example: Oracle → Aurora PostgreSQL, SQL Server → RDS MySQL
│       ├─→ Tools:
│       │   ├─→ **AWS SCT** (Schema Conversion Tool)
│       │   │   └─→ Convert schema, stored procedures, functions
│       │   └─→ **AWS DMS** (migrate data)
│       ├─→ Steps:
│       │   1. Use SCT to convert schema
│       │   2. Review and fix conversion issues
│       │   3. Apply schema to target database
│       │   4. Use DMS to migrate data
│       │   5. Validate and cutover
│       └─→ Challenges:
│           ├─→ Schema incompatibilities
│           ├─→ Application code changes
│           └─→ Testing required
│
├─→ Database Migration Service (DMS) Details
│   │
│   ├─→ Replication Instance:
│   │   ├─→ EC2 instance running replication software
│   │   ├─→ Size based on data volume and change rate
│   │   └─→ Multi-AZ for high availability
│   │
│   ├─→ Migration Types:
│   │   ├─→ Full Load: One-time migration
│   │   ├─→ Full Load + CDC: Initial load + ongoing changes
│   │   └─→ CDC Only: Continuous replication
│   │
│   ├─→ Supported Sources:
│   │   ├─→ On-premises: Oracle, SQL Server, MySQL, PostgreSQL, MongoDB
│   │   ├─→ AWS: RDS, Aurora, S3
│   │   └─→ Azure SQL Database
│   │
│   └─→ Supported Targets:
│       ├─→ RDS, Aurora, Redshift, DynamoDB
│       ├─→ S3, OpenSearch, Kinesis
│       └─→ DocumentDB, Neptune
│
├─→ Specific Migration Scenarios
│   │
│   ├─→ Oracle → AWS?
│   │   ├─→ License included: RDS for Oracle
│   │   ├─→ Reduce cost: Aurora PostgreSQL (refactor)
│   │   └─→ Same features: RDS for Oracle with BYOL
│   │
│   ├─→ SQL Server → AWS?
│   │   ├─→ License included: RDS for SQL Server
│   │   ├─→ Reduce cost: Aurora MySQL/PostgreSQL (refactor)
│   │   └─→ High availability: RDS Multi-AZ or Aurora
│   │
│   ├─→ MongoDB → AWS?
│   │   └─→ DocumentDB (MongoDB compatible)
│   │
│   ├─→ Cassandra → AWS?
│   │   └─→ Amazon Keyspaces
│   │
│   └─→ Need data warehouse?
│       └─→ Migrate to Amazon Redshift
│           └─→ Use DMS + Redshift as target
│
└─→ Migration Best Practices
    ├─→ 1. Assess and plan (AWS Migration Evaluator)
    ├─→ 2. Test migration in non-production
    ├─→ 3. Use CDC for minimal downtime
    ├─→ 4. Validate data after migration
    ├─→ 5. Have rollback plan
    └─→ 6. Monitor DMS tasks and performance
```

---

## Application Migration

```
START: Migrate applications to AWS?
│
├─→ Virtual Machines (EC2)?
│   └─→ **AWS Application Migration Service (MGN)**
│       ├─→ Replaces Server Migration Service (SMS)
│       ├─→ Block-level replication
│       ├─→ Continuous replication (minimal downtime)
│       ├─→ Supports:
│       │   ├─→ VMware, Hyper-V, physical servers
│       │   ├─→ Windows, Linux
│       │   └─→ Azure VMs
│       ├─→ Process:
│       │   1. Install replication agent on source
│       │   2. Data replicates to AWS staging area
│       │   3. Test instances launched for validation
│       │   4. Cutover to production
│       └─→ Use case: Lift-and-shift VM migration
│
├─→ VMware workloads?
│   ├─→ Keep VMware stack?
│   │   └─→ **VMware Cloud on AWS**
│   │       ├─→ VMware SDDC on AWS bare metal
│   │       ├─→ vSphere, vSAN, NSX
│   │       ├─→ Hybrid cloud operations
│   │       └─→ Use case: Extend on-prem VMware to cloud
│   │
│   └─→ Migrate off VMware?
│       └─→ **AWS Application Migration Service**
│
├─→ Containers?
│   └─→ **Containerize and Deploy**
│       ├─→ AWS App2Container:
│       │   ├─→ Containerize .NET and Java apps
│       │   ├─→ Generates Dockerfile and deployment artifacts
│       │   └─→ Deploy to ECS or EKS
│       ├─→ Migration path:
│       │   1. Containerize with App2Container
│       │   2. Push images to ECR
│       │   3. Deploy to ECS/EKS
│       │   4. Configure load balancing and scaling
│       └─→ Use case: Modernize legacy apps
│
├─→ Mainframe?
│   └─→ **AWS Mainframe Modernization**
│       ├─→ Replatform: Automated refactoring
│       ├─→ Rehost: Managed runtime
│       └─→ Use case: COBOL, PL/I applications
│
├─→ SAP workloads?
│   └─→ **SAP on AWS**
│       ├─→ AWS Launch Wizard for SAP
│       ├─→ Certified SAP instances
│       └─→ Use case: SAP HANA, S/4HANA, NetWeaver
│
├─→ Windows workloads?
│   └─→ **AWS Migration Options**
│       ├─→ Application Migration Service (MGN)
│       ├─→ FSx for Windows File Server
│       ├─→ AWS Directory Service
│       └─→ Use case: .NET apps, Active Directory
│
└─→ Migration Assessment Tools
    │
    ├─→ **AWS Migration Hub**
    │   ├─→ Central tracking of migrations
    │   ├─→ Integrates with multiple migration tools
    │   └─→ Progress visualization
    │
    ├─→ **AWS Application Discovery Service**
    │   ├─→ Discover on-premises applications
    │   ├─→ Agent-based or agentless
    │   ├─→ Collect configuration and usage data
    │   └─→ Create migration plan
    │
    └─→ **AWS Migration Evaluator**
        ├─→ Business case for migration
        ├─→ Cost projections
        └─→ Right-sizing recommendations
```

---

## Hybrid Architecture

```
START: Hybrid cloud architecture?
│
├─→ Network Connectivity?
│   │
│   ├─→ Quick setup, encrypted?
│   │   └─→ **Site-to-Site VPN**
│   │       ├─→ Over public internet
│   │       ├─→ Setup: Hours to days
│   │       ├─→ Bandwidth: Up to 1.25 Gbps per tunnel
│   │       └─→ Use case: Quick hybrid setup, backup connectivity
│   │
│   ├─→ Dedicated, consistent?
│   │   └─→ **AWS Direct Connect**
│   │       ├─→ Dedicated physical connection
│   │       ├─→ Setup: Weeks to months
│   │       ├─→ Bandwidth: 1 Gbps - 100 Gbps
│   │       ├─→ Private VIF: Connect to VPCs
│   │       ├─→ Public VIF: Connect to public AWS services
│   │       └─→ Use case: Large data transfers, consistent performance
│   │
│   ├─→ Need both dedicated + encrypted?
│   │   └─→ **Direct Connect + VPN**
│   │       ├─→ VPN over Direct Connect
│   │       └─→ Use case: Compliance requiring encryption
│   │
│   └─→ Connect to multiple VPCs?
│       └─→ **AWS Transit Gateway**
│           ├─→ Hub for VPCs and on-premises
│           ├─→ Single VPN/Direct Connect to multiple VPCs
│           └─→ Use case: Large hybrid networks
│
├─→ Hybrid Storage?
│   │
│   ├─→ File Gateway (NFS/SMB)?
│   │   └─→ **Storage Gateway - File Gateway**
│   │       ├─→ NFS or SMB access to S3
│   │       ├─→ Local cache for low latency
│   │       ├─→ On-premises apps write to local, sync to S3
│   │       └─→ Use case: Extend storage to cloud, backups
│   │
│   ├─→ Block Storage (iSCSI)?
│   │   └─→ **Storage Gateway - Volume Gateway**
│   │       ├─→ Cached Volumes: S3-backed, local cache
│   │       ├─→ Stored Volumes: Local storage, backup to S3
│   │       └─→ Use case: iSCSI applications, DR
│   │
│   └─→ Tape Backup?
│       └─→ **Storage Gateway - Tape Gateway**
│           ├─→ Virtual Tape Library (VTL)
│           ├─→ Backup to S3 and Glacier
│           └─→ Use case: Replace physical tape, archive
│
├─→ Hybrid Identity?
│   │
│   ├─→ Use on-premises AD?
│   │   ├─→ Proxy to on-prem AD?
│   │   │   └─→ **AD Connector**
│   │   │       ├─→ Proxy requests to on-prem AD
│   │   │       ├─→ No data stored in AWS
│   │   │       └─→ Use case: Use existing on-prem AD
│   │   │
│   │   └─→ Extend AD to AWS?
│   │       └─→ **AWS Managed Microsoft AD**
│   │           ├─→ Trust relationship with on-prem AD
│   │           ├─→ Full AD features in AWS
│   │           └─→ Use case: AD-dependent workloads in AWS
│   │
│   └─→ Federated access?
│       └─→ **IAM Identity Center** (SSO)
│           ├─→ Connect to on-prem IdP (SAML)
│           ├─→ Single sign-on to AWS accounts
│           └─→ Use case: Enterprise SSO
│
├─→ Hybrid DNS?
│   └─→ **Route 53 Resolver**
│       ├─→ Inbound Endpoint: On-prem → AWS DNS
│       ├─→ Outbound Endpoint: AWS → On-prem DNS
│       └─→ Use case: Cross-environment name resolution
│
└─→ Edge Computing (Hybrid)?
    │
    ├─→ AWS at edge location?
    │   └─→ **AWS Outposts**
    │       ├─→ AWS infrastructure on-premises
    │       ├─→ Same APIs and tools as AWS
    │       ├─→ Full racks or servers
    │       └─→ Use case: Low latency, data residency
    │
    ├─→ Limited connectivity?
    │   └─→ **Snow Family (Snowball Edge)**
    │       ├─→ Compute at edge
    │       ├─→ Disconnected operation
    │       └─→ Use case: Edge ML, remote locations
    │
    └─→ 5G edge?
        └─→ **AWS Wavelength**
            ├─→ AWS compute in telecom 5G networks
            ├─→ Ultra-low latency
            └─→ Use case: Mobile edge computing, gaming
```

---

## Exam Scenarios - Quick Answers

### Scenario 1: Migrate 50 TB to S3, limited bandwidth
**Answer**: AWS Snowball Edge

### Scenario 2: Migrate Oracle to Aurora PostgreSQL
**Answer**: AWS SCT (schema conversion) + AWS DMS (data migration)

### Scenario 3: Lift-and-shift VMs to EC2 quickly
**Answer**: AWS Application Migration Service (MGN)

### Scenario 4: Continuous file sync from on-prem to S3
**Answer**: AWS DataSync

### Scenario 5: On-prem application needs S3 access via NFS
**Answer**: Storage Gateway - File Gateway

### Scenario 6: Track migration progress across multiple tools
**Answer**: AWS Migration Hub

### Scenario 7: Oracle database with minimal downtime migration
**Answer**: AWS DMS with CDC (Change Data Capture)

### Scenario 8: Discover on-prem applications before migration
**Answer**: AWS Application Discovery Service

### Scenario 9: Containerize legacy Java application
**Answer**: AWS App2Container → Deploy to ECS/EKS

### Scenario 10: Need AWS services on-premises
**Answer**: AWS Outposts

### Scenario 11: Hybrid DNS resolution
**Answer**: Route 53 Resolver (Inbound + Outbound endpoints)

### Scenario 12: Migrate 5 PB of data to AWS
**Answer**: AWS Snowmobile (or multiple Snowball devices)

---

## 🎯 Key Takeaways

### 6 Rs of Migration:
1. **Retire**: Turn off
2. **Retain**: Keep on-prem
3. **Repurchase**: Move to SaaS
4. **Rehost**: Lift-and-shift
5. **Replatform**: Lift-tinker-and-shift
6. **Refactor**: Re-architect for cloud

### Data Transfer Tools:
- **Small data**: Direct upload, CLI
- **Medium data**: DataSync, Transfer Acceleration
- **Large data**: Snowball Edge (80 TB)
- **Massive data**: Snowmobile (100 PB)
- **Ongoing sync**: DataSync
- **Hybrid access**: Storage Gateway

### Database Migration:
- **Same engine**: DMS only
- **Different engine**: SCT + DMS
- **Minimal downtime**: Use CDC

### Application Migration:
- **VMs to EC2**: Application Migration Service (MGN)
- **VMware**: VMware Cloud on AWS or MGN
- **Containers**: App2Container → ECS/EKS

### Hybrid Architecture:
- **Quick connectivity**: Site-to-Site VPN
- **Dedicated**: Direct Connect
- **Hybrid storage**: Storage Gateway
- **Hybrid identity**: AD Connector or Managed AD
- **AWS on-prem**: Outposts

---

*Use these decision trees for migration and transfer questions on the exam!*
