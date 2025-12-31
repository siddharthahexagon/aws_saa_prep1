# AWS SAA-C03: Migration, Transfer & Additional Services Cheat Sheet

## 📋 Table of Contents
- [Migration Hub](#migration-hub)
- [Application Discovery Service](#application-discovery-service)
- [Application Migration Service (MGN)](#application-migration-service)
- [Database Migration Service (DMS)](#database-migration-service)
- [DataSync](#datasync)
- [Transfer Family](#transfer-family)
- [Snow Family](#snow-family)
- [Disaster Recovery Strategies](#disaster-recovery-strategies)

---

## Migration Hub

### Definition
Central location to track application migrations across multiple AWS and partner solutions.

### Key Features
- **Centralized Tracking**: Single dashboard for migrations
- **Integrations**: Application Discovery Service, Server Migration Service, DMS
- **Grouping**: Organize servers by application
- **Status Tracking**: Monitor migration progress
- **Migration Plans**: Create and track plans

### Exam Tips & Heuristics
✅ **When to Use Migration Hub:**
- Track multiple migrations
- Central visibility
- Plan large-scale migrations

🎯 **Exam Heuristics:**
- "Track multiple migrations" = **Migration Hub**
- "Migration visibility" = **Migration Hub**

---

## Application Discovery Service

### Definition
Plan migration projects by gathering information about on-premises data centers.

### Key Features
- **Discovery Types**:
  - **Agentless**: VMware vCenter integration
  - **Agent-based**: Detailed system performance and dependencies
- **Data Collected**: Configuration, utilization, network connections
- **Integration**: Migration Hub
- **Export**: Data to S3 for analysis

### Exam Tips & Heuristics
✅ **When to Use Application Discovery Service:**
- Discover on-premises environment
- Plan migrations
- Understand dependencies
- Size AWS resources

🎯 **Exam Heuristics:**
- "Discover on-premises servers" = **Application Discovery Service**
- "Plan migration" = **Application Discovery Service**
- "Server dependencies" = **Application Discovery Service** (agent-based)

---

## Application Migration Service (MGN)

### Definition
Lift-and-shift migration service (formerly CloudEndure Migration).

### Key Features
- **Replication**: Continuous block-level replication
- **Minimal Downtime**: Cutover in minutes
- **Supported Sources**: Physical, virtual, cloud servers
- **Target**: EC2 instances
- **Testing**: Non-disruptive testing
- **Automated Conversion**: Match AWS instance types
- **Free**: No additional charge for the service

### Exam Tips & Heuristics
✅ **When to Use MGN:**
- Lift-and-shift migrations
- Minimal downtime required
- Large-scale server migrations
- Disaster recovery

⚠️ **Common Pitfalls:**
- Not for application refactoring
- Requires replication agent
- Target is always EC2

🎯 **Exam Heuristics:**
- "Lift-and-shift servers" = **Application Migration Service (MGN)**
- "Minimal downtime migration" = **MGN**
- "Rehost servers to AWS" = **MGN**
- "CloudEndure Migration" = Now called **MGN**
- "Physical servers to AWS" = **MGN**

---

## Database Migration Service (DMS)

### Definition
Migrate databases to AWS with minimal downtime (covered in Database section, repeated here for migration context).

### Key Features
- **Continuous Replication**: CDC for ongoing replication
- **Homogeneous & Heterogeneous**: Same or different engines
- **Schema Conversion Tool (SCT)**: For heterogeneous migrations
- **Snowball Integration**: Large database migrations

### Exam Tips & Heuristics
✅ **When to Use DMS:**
- Database migrations
- Continuous replication
- Consolidate databases
- Database modernization

🎯 **Exam Heuristics:**
- "Migrate database" = **DMS**
- "Oracle to Aurora" = **DMS + SCT**
- "Continuous database replication" = **DMS**
- "Large database + limited bandwidth" = **DMS + Snowball**

---

## DataSync

### Definition
Online data transfer service to move data between on-premises and AWS, or between AWS services.

### Key Features
- **Transfer Speed**: Up to 10Gbps
- **Protocols**: NFS, SMB
- **Locations**: 
  - **Source**: On-premises (NFS, SMB, HDFS), AWS (S3, EFS, FSx)
  - **Destination**: S3, EFS, FSx
- **Scheduling**: Automated transfer schedules
- **Verification**: Data integrity checks
- **Bandwidth Throttling**: Control network usage
- **Encryption**: In transit and at rest
- **DataSync Agent**: VM deployed on-premises

### Exam Tips & Heuristics
✅ **When to Use DataSync:**
- Online data migration (TB to PB)
- Recurring data transfers
- Data replication for DR
- Sync on-premises to AWS
- Transfer between AWS services

⚠️ **Common Pitfalls:**
- Requires network connectivity (online)
- Use Snow Family for offline or limited bandwidth
- Agent needed for on-premises sources

🎯 **Exam Heuristics:**
- "Online data transfer" = **DataSync**
- "NFS/SMB to S3/EFS" = **DataSync**
- "Scheduled data sync" = **DataSync**
- "Continuous sync to AWS" = **DataSync**
- "<10TB + good bandwidth" = **DataSync**
- ">10TB + limited bandwidth" = **Snow Family**
- "One-time large transfer + limited bandwidth" = **Snowball**
- "Ongoing sync" = **DataSync**

---

## Transfer Family

### Definition
Fully managed support for SFTP, FTPS, and FTP directly to S3 or EFS.

### Key Features
- **Protocols**: SFTP, FTPS, FTP
- **Storage**: S3 or EFS
- **Authentication**: Service-managed, Active Directory, custom (Lambda)
- **High Availability**: Multi-AZ
- **Managed Infrastructure**: No servers to maintain
- **DNS**: Custom domain support (Route 53)
- **Logging**: CloudWatch and CloudTrail
- **Workflows**: Automate post-upload processing

### Exam Tips & Heuristics
✅ **When to Use Transfer Family:**
- Migrate file transfer workflows to AWS
- SFTP/FTP access to S3
- Replace on-premises FTP servers
- B2B file transfers

⚠️ **Common Pitfalls:**
- Charged per hour + data transfer
- Not for internal AWS data transfers
- Endpoints are public by default (can use VPC)

🎯 **Exam Heuristics:**
- "SFTP to S3" = **Transfer Family**
- "FTP server" = **Transfer Family**
- "Migrate FTP workflows" = **Transfer Family**
- "Third-party file uploads to S3" = **Transfer Family**
- "Active Directory authentication for FTP" = **Transfer Family**

---

## Snow Family

### Definition
Physical devices for data migration and edge computing (covered in Storage section, repeated for migration context).

### Key Features
- **Snowcone**: 8-14TB, portable, edge computing
- **Snowball Edge**: 80TB, edge computing, clustering
- **Snowmobile**: 100PB, exabyte-scale

### Exam Tips & Heuristics
✅ **When to Use Snow Family:**
- Limited bandwidth
- Offline data transfer
- Large-scale migrations (TB to EB)
- Edge computing

🎯 **Exam Heuristics:**
- "<10TB + good bandwidth" = **DataSync** or **Direct Connect**
- "10TB-10PB + offline" = **Snowball Edge**
- ">10PB" = **Snowmobile**
- "Limited bandwidth" = **Snow Family**
- "Remote edge computing" = **Snowcone/Snowball Edge**

---

## Disaster Recovery Strategies

### Overview
AWS provides multiple DR strategies with different RTO (Recovery Time Objective) and RPO (Recovery Point Objective).

### DR Strategies (Fastest to Slowest)

#### 1. Multi-Site Active/Active
- **Description**: Fully operational in multiple regions
- **RTO/RPO**: Minutes/Real-time
- **Cost**: Highest
- **Services**: Route 53, Global Accelerator, Aurora Global Database
- **Use Case**: Zero downtime requirement

#### 2. Warm Standby
- **Description**: Scaled-down version running in DR region
- **RTO/RPO**: Minutes to hours / Minutes
- **Cost**: Medium-High
- **Services**: EC2 (smaller instances), RDS Multi-AZ, ASG (min capacity)
- **Use Case**: Quick recovery needed

#### 3. Pilot Light
- **Description**: Minimal version running (core components only)
- **RTO/RPO**: Hours / Minutes
- **Cost**: Medium
- **Services**: RDS replicas, AMIs ready, data replicated
- **Use Case**: Cost-effective with acceptable recovery time

#### 4. Backup and Restore
- **Description**: Regular backups, restore when needed
- **RTO/RPO**: Hours to days / Hours
- **Cost**: Lowest
- **Services**: S3, AWS Backup, snapshots
- **Use Case**: Non-critical systems, cost optimization

### Key DR Services
- **Route 53**: Failover routing
- **S3**: Cross-region replication
- **EBS/RDS**: Snapshots to another region
- **Aurora**: Global Database
- **DynamoDB**: Global Tables
- **CloudFormation**: Infrastructure automation
- **AWS Backup**: Centralized backup, cross-region copy
- **Elastic Disaster Recovery (DRS)**: Continuous replication (CloudEndure DR)

### Exam Tips & Heuristics
🎯 **Exam Heuristics:**
- "Zero downtime" = **Multi-Site Active/Active**
- "Minutes RTO" = **Warm Standby** or **Multi-Site**
- "Cost-effective DR" = **Pilot Light** or **Backup and Restore**
- "Hours RTO acceptable" = **Pilot Light** or **Backup and Restore**
- "Database DR" = **RDS Read Replica** (different region) or **Aurora Global Database**
- "Application DR" = **Elastic Disaster Recovery (DRS)**
- "File DR" = **S3 CRR**, **EFS replication**, **DataSync**

### DR Strategy Comparison
| Strategy | RTO | RPO | Cost | Complexity |
|----------|-----|-----|------|------------|
| Multi-Site | Minutes | Real-time | Highest | High |
| Warm Standby | Minutes-Hours | Minutes | Medium-High | Medium |
| Pilot Light | Hours | Minutes | Medium | Medium |
| Backup/Restore | Hours-Days | Hours | Lowest | Low |

---

## 🎓 Migration Service Selection Guide

| Requirement | Service |
|------------|---------|
| Track migrations | Migration Hub |
| Discover on-premises | Application Discovery Service |
| Server migration | Application Migration Service (MGN) |
| Database migration | Database Migration Service (DMS) |
| Online data transfer | DataSync |
| SFTP/FTP to S3 | Transfer Family |
| Offline data transfer | Snow Family |
| Disaster recovery | Multi-Site, Pilot Light, Backup/Restore |

---

## 💡 Key Migration Patterns

### 6 R's of Migration
1. **Rehost (Lift-and-Shift)**: MGN, VM Import/Export
2. **Replatform (Lift-Tinker-Shift)**: RDS instead of EC2 database
3. **Repurchase (Drop-and-Shop)**: SaaS (Salesforce, Workday)
4. **Refactor/Re-architect**: Serverless, containers
5. **Retire**: Decommission unnecessary systems
6. **Retain**: Keep on-premises (for now)

### Migration Best Practices
- Start with non-critical applications
- Use Application Discovery Service for inventory
- Test migrations thoroughly
- Plan for network bandwidth
- Consider hybrid period
- Automate with CloudFormation
- Implement monitoring early

### Data Transfer Selection
| Data Size | Bandwidth | Service |
|-----------|-----------|---------|
| <10TB | Good | DataSync, Direct Connect |
| <10TB | Limited | Snowcone |
| 10TB-10PB | Limited | Snowball Edge |
| >10PB | Limited | Snowmobile |
| Ongoing | Any | DataSync, Direct Connect |

### Database Migration Strategy
1. **Assess**: DMS + SCT for assessment
2. **Convert Schema**: SCT for heterogeneous
3. **Migrate**: DMS with CDC
4. **Validate**: Test thoroughly
5. **Cutover**: Minimize downtime

