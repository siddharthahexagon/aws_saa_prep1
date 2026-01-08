# AWS Migration & Transfer Services - Complete Definitions (SAA-C03)

## Table of Contents
- [AWS Migration Hub](#aws-migration-hub)
- [AWS Application Discovery Service](#aws-application-discovery-service)
- [AWS Application Migration Service (MGN)](#aws-application-migration-service)
- [AWS Database Migration Service (DMS)](#aws-database-migration-service)
- [AWS DataSync](#aws-datasync)
- [AWS Transfer Family](#aws-transfer-family)
- [AWS Snow Family](#aws-snow-family)
- [AWS Migration Evaluator](#aws-migration-evaluator)
- [AWS Mainframe Modernization](#aws-mainframe-modernization)

---

## AWS Migration Hub

**Definition**: Central location to track application migrations across multiple AWS and partner solutions.

**Key Features**:
- Centralized tracking
- Multi-tool support
- Progress dashboard
- Migration status
- Integration with migration tools
- No additional cost

**Supported Tools**:
- AWS Application Migration Service (MGN)
- AWS Database Migration Service (DMS)
- AWS Server Migration Service (SMS) - deprecated
- CloudEndure Migration
- Partner tools (ATADATA, RiverMeadow, etc.)

**Components**:

### Application Grouping
- Group servers as applications
- Track as single unit
- Dependency mapping

### Migration Updates
- Status tracking
- Progress monitoring
- Cross-tool visibility

### Strategy Recommendations
- 7 R's of migration
- Cost estimates
- Migration patterns

**7 R's of Migration**:
1. **Rehost (Lift and Shift)**: Move as-is
2. **Replatform (Lift, Tinker, and Shift)**: Minor optimizations
3. **Refactor/Re-architect**: Redesign for cloud
4. **Repurchase**: Move to SaaS
5. **Retire**: Decommission
6. **Retain**: Keep on-premises
7. **Relocate**: Move to AWS (VMware Cloud)

**Use Cases**:
- Track complex migrations
- Multi-tool migrations
- Portfolio-level visibility
- Migration planning

**Exam Tips**:
- Migration Hub for tracking migrations
- Centralized view across tools
- No additional cost
- Integrates with MGN, DMS, partner tools
- 7 R's of migration strategy
- Groups servers into applications

---

## AWS Application Discovery Service

**Definition**: Helps plan migration projects by gathering information about on-premises data centers.

**Key Features**:
- Discover servers and applications
- Dependency mapping
- Performance data collection
- Integration with Migration Hub
- Export data for analysis

**Discovery Types**:

### Agentless Discovery (Connector)
- **VMware vCenter only**
- Deploy OVA virtual appliance
- Collects:
  - Static configuration data
  - Resource utilization
  - Network connections
- Less detailed than agent-based
- No software installation on servers

### Agent-Based Discovery
- Install agent on each server
- Collects:
  - Detailed system information
  - Performance metrics
  - Network dependencies
  - Running processes
- Works on:
  - Physical servers
  - VMware VMs
  - Hyper-V VMs
- More comprehensive data

**Data Collected**:
- System configuration
- System performance
- Running processes
- Network dependencies
- Resource utilization

**Data Analysis**:
- Export to S3
- Analyze with Athena
- Visualize with QuickSight
- Integration with Migration Hub

**Use Cases**:
- Migration planning
- Dependency mapping
- Right-sizing
- TCO analysis
- Migration waves

**Exam Tips**:
- Application Discovery for migration planning
- Agentless: VMware only, less detail
- Agent-based: More detail, all environments
- Exports data to S3 for analysis
- Integrates with Migration Hub
- Use before migration to understand environment

---

## AWS Application Migration Service (MGN)

**Definition**: Automated lift-and-shift solution for migrating applications to AWS. Formerly CloudEndure Migration.

**Key Features**:
- Automated replication
- Minimal downtime (minutes)
- Wide platform support
- Non-disruptive testing
- Automated conversion
- Free for first 90 days per server

**How It Works**:
1. Install replication agent on source
2. Continuous data replication to AWS
3. Test in AWS (non-disruptive)
4. Cutover when ready (minutes of downtime)
5. Replication agents automatically removed

**Replication**:
- Block-level continuous replication
- Low staging area (lightweight instances)
- Replication to EBS volumes
- Minimal performance impact

**Supported Sources**:
- Physical servers
- VMware vSphere
- Microsoft Hyper-V
- Azure VMs
- Amazon EC2
- Other cloud platforms

**Supported Operating Systems**:
- Windows Server 2003+
- Most Linux distributions

**Testing**:
- Launch test instances
- Validate functionality
- No impact on production
- Multiple test cycles

**Cutover**:
- Automated launch of production instances
- Minutes of downtime
- Final data sync
- DNS cutover (manual)

**Post-Migration**:
- Optimization recommendations
- Modernization opportunities
- Cost optimization

**Use Cases**:
- Datacenter migration
- Disaster recovery
- Cross-cloud migration
- Operating system migration
- Platform migration

**Exam Tips**:
- MGN for lift-and-shift migrations (rehost)
- Continuous replication, minimal downtime
- Automated server conversion
- Non-disruptive testing
- Supports physical and virtual servers
- Free for first 90 days per server
- Replaced SMS (Server Migration Service)

---

## AWS Database Migration Service (DMS)

**Definition**: Managed service to migrate databases to AWS with minimal downtime.

(Core details covered in Database section; migration-specific details here)

**Migration Types**:

### Homogeneous Migrations
- Same database engines
- Oracle → Oracle, MySQL → MySQL
- Direct migration
- No schema conversion

### Heterogeneous Migrations
- Different database engines
- Oracle → Aurora, SQL Server → MySQL
- Requires AWS Schema Conversion Tool (SCT)
- Two-step: schema conversion, then data

**Schema Conversion Tool (SCT)**:
- Convert database schemas
- Identify incompatibilities
- Convert stored procedures, functions, views
- Assessment report
- Desktop application

**Replication Methods**:

### Full Load
- One-time migration
- All existing data
- Downtime required

### Full Load + CDC (Change Data Capture)
- Initial full load
- Continuous replication of changes
- Minimal downtime
- Most common

### CDC Only
- Only replicate changes
- Assumes data already migrated
- Ongoing replication

**Replication Instance**:
- EC2 instance running DMS
- Connect source and target
- Performs data migration
- Size based on workload

**Multi-AZ**:
- High availability
- Automatic failover
- Synchronous replication
- Production migrations

**Endpoints**:
- Source and target database connections
- Connection settings
- SSL support
- VPN/Direct Connect for on-premises

**Tasks**:
- Define migration parameters
- Table mappings
- Transformation rules
- Start/stop/monitor

**Supported Sources**:
- On-premises and AWS databases
- Oracle, SQL Server, MySQL, PostgreSQL, MongoDB, SAP, DB2
- Amazon RDS, Aurora, S3
- Azure SQL Database

**Supported Targets**:
- Amazon RDS, Aurora, Redshift
- S3, DynamoDB, OpenSearch
- Kinesis Data Streams
- DocumentDB, Neptune

**Continuous Replication**:
- Ongoing CDC
- Use for:
  - Disaster recovery
  - Database consolidation
  - Read replicas
  - Analytics

**Snowball Integration**:
- For very large databases (> 10 TB)
- Extract to Snowball
- Ship to AWS
- Import to S3
- DMS loads to target

**Use Cases**:
- Database migration to AWS
- Continuous replication
- Database consolidation
- Disaster recovery
- Test/dev database refresh

**Exam Tips**:
- DMS for database migrations
- Minimal downtime with CDC
- SCT for heterogeneous migrations
- Replication instance performs migration
- Multi-AZ for production
- Snowball for very large databases
- Continuous replication for DR
- Source can be on-premises or cloud

---

## AWS DataSync

**Definition**: Online data transfer service that simplifies, automates, and accelerates moving data between on-premises storage and AWS, or between AWS storage services.

**Key Features**:
- Automated data transfer
- Fast transfer (up to 10 Gbps)
- Bandwidth throttling
- Data validation
- Scheduling
- Encryption in transit
- Incremental transfers

**How It Works**:
1. Deploy DataSync agent (on-premises or EC2)
2. Configure source and destination
3. Create task
4. Execute (manual or scheduled)
5. Monitor progress

**DataSync Agent**:
- VM (VMware, KVM, Hyper-V) or EC2 instance
- Connects to on-premises storage
- Transfers data to AWS
- One agent can handle multiple tasks

**Supported Sources**:
- NFS (v3, v4)
- SMB (v2, v3)
- HDFS
- Self-managed object storage
- AWS storage (S3, EFS, FSx)

**Supported Destinations**:
- Amazon S3 (all storage classes)
- Amazon EFS
- Amazon FSx for Windows File Server
- Amazon FSx for Lustre
- Amazon FSx for OpenZFS
- Amazon FSx for NetApp ONTAP

**Transfer Options**:
- Bandwidth throttling
- Schedule transfers
- Task filtering (include/exclude)
- Verify data integrity
- Preserve metadata and permissions

**Locations**:
- **On-premises**: NFS, SMB servers
- **AWS**: S3, EFS, FSx
- **Other cloud**: Self-managed object storage

**Use Cases**:
- Data migration to AWS
- Archive cold data
- Data replication for DR
- Data processing workflows
- Hybrid storage

**DataSync vs Snow Family vs DMS**:

| Service | Type | Use Case |
|---------|------|----------|
| **DataSync** | Online Transfer | Files, ongoing sync |
| **Snow Family** | Offline Transfer | Large data, no network |
| **DMS** | Database Migration | Databases |

**Exam Tips**:
- DataSync for online data transfer
- Fast (up to 10 Gbps)
- Automated and scheduled transfers
- Supports NFS, SMB, HDFS, object storage
- Transfer to S3, EFS, FSx
- Incremental transfers
- Data validation and encryption
- Use for data migration and ongoing sync
- Agent required for on-premises

---

## AWS Transfer Family

**Definition**: Fully managed support for file transfers directly into and out of S3 or EFS using SFTP, FTPS, and FTP protocols.

**Supported Protocols**:
- **SFTP (SSH File Transfer Protocol)**: Secure, over SSH
- **FTPS (File Transfer Protocol over SSL)**: Secure, over TLS
- **FTP (File Transfer Protocol)**: Unencrypted (not recommended)
- **AS2 (Applicability Statement 2)**: EDI data transfer

**Key Features**:
- Fully managed
- Highly available (Multi-AZ)
- No infrastructure management
- Existing workflows supported
- Custom hostname (Route 53)
- Integration with existing authentication

**Storage Backends**:
- **Amazon S3**: Store files as objects
- **Amazon EFS**: Store as files in file system

**Authentication**:
- Service-managed users (stored in Transfer Family)
- Custom identity provider (LDAP, Active Directory, etc.)
- AWS Secrets Manager
- Lambda-based authentication

**Access Control**:
- IAM roles for users
- Restrict access to S3 buckets/EFS directories
- Home directory mapping
- Logical directories (chroot)

**Endpoints**:
- **Public**: Accessible from internet
- **VPC**: Private access from VPC
- **VPC with internet-facing**: Elastic IP

**Use Cases**:
- Migrate FTP workflows to AWS
- Secure file exchange
- Partner file sharing
- Legacy application integration
- EDI transactions (AS2)

**Pricing**:
- Hourly fee per protocol per endpoint
- Data transfer charges

**Exam Tips**:
- Transfer Family for FTP/SFTP to S3/EFS
- Supports SFTP, FTPS, FTP, AS2
- Fully managed, no servers
- Store in S3 or EFS
- Custom authentication (Lambda, AD, Secrets Manager)
- Use for legacy FTP migrations
- High availability (Multi-AZ)

---

## AWS Snow Family

**Definition**: Physical devices for offline data transfer and edge computing when network transfer is not feasible.

(Core details in Storage section; migration specifics here)

**Migration Use Cases**:

### Snowcone
- **Capacity**: 8 TB HDD or 14 TB SSD
- **Use Case**: Small migrations, edge computing
- **Transfer**: Ship back or DataSync online transfer
- **Edge Computing**: EC2 instances, Lambda

### Snowball Edge
- **Storage Optimized**: 80 TB usable
- **Compute Optimized**: 42 TB usable + GPU option
- **Use Case**: Large migrations (10 TB - 10 PB)
- **Transfer**: Ship back to AWS
- **Edge Computing**: EC2, Lambda, IoT Greengrass
- **Clustering**: Combine devices for more capacity

### Snowmobile
- **Capacity**: Up to 100 PB per truck
- **Use Case**: Exabyte-scale migrations, datacenter shutdown
- **Transfer**: Drive to location, load data, ship to AWS
- **Security**: GPS tracking, 24/7 video surveillance

**Data Transfer Process**:
1. Request device(s) from AWS
2. Receive and connect device
3. Copy data to device
4. Ship back to AWS
5. AWS imports data to S3
6. Device securely erased

**Snow Family with DMS**:
- Extract database to Snowball Edge
- Ship to AWS
- DMS imports to target database
- For very large databases

**Snow Family with Storage Gateway**:
- NFS interface to Snowball Edge
- Applications write to "cloud" storage
- Ship device when full
- Continue with next device

**Security**:
- 256-bit encryption
- TPM (Trusted Platform Module)
- Secure erase after import
- Chain of custody tracking

**Use Cases**:
- Datacenter migration
- Disaster recovery
- Content distribution
- Edge computing and storage
- Limited or no internet connectivity

**Exam Tips**:
- Use Snow Family when network transfer too slow/expensive
- **Snowcone**: < 8 TB, portable
- **Snowball**: 10 TB - 10 PB, most common
- **Snowmobile**: > 10 PB, datacenter shutdown
- Edge computing capabilities
- Encrypted by default
- Ship to AWS for import to S3
- Consider data transfer time and network costs
- Can use with DMS for large databases

---

## AWS Migration Evaluator

**Definition**: Helps build data-driven business case for migration to AWS. Formerly TSO Logic.

**Key Features**:
- Quick insights (no agents)
- Cost modeling
- Right-sizing recommendations
- Migration patterns
- Business case report
- No cost

**Data Collection**:
- Agentless (read-only)
- Collects inventory and utilization
- On-premises and other clouds
- 7-day snapshot

**Outputs**:
- Directional business case
- Cost comparison (on-premises vs AWS)
- Migration patterns (7 R's)
- Right-sizing recommendations
- Migration planning

**Use Cases**:
- Build business case for cloud
- Executive presentations
- Migration planning
- Cost comparison
- Quick assessment

**Exam Tips**:
- Migration Evaluator for business case
- Quick insights, no agents
- Cost comparison on-premises vs AWS
- Migration pattern recommendations
- Free service
- Use early in migration journey

---

## AWS Mainframe Modernization

**Definition**: Managed service to migrate and modernize mainframe applications to AWS.

**Key Features**:
- Automated refactoring
- Managed runtime
- Replatforming and refactoring
- Minimal code changes
- Development and testing tools

**Patterns**:
- **Automated Refactoring**: Convert to Java microservices
- **Replatforming**: Run mainframe apps on AWS (managed runtime)

**Use Cases**:
- Mainframe migration
- Mainframe modernization
- Cost reduction
- Agility improvement

**Exam Tips**:
- Mainframe Modernization for mainframe migrations
- Automated refactoring or replatforming
- Managed runtime for mainframe apps
- Specialized service (less common on exam)

---

## Migration Services Summary

| Service | Type | Use Case |
|---------|------|----------|
| **Migration Hub** | Tracking | Central migration tracking |
| **Application Discovery** | Assessment | Discover on-premises servers |
| **MGN** | Server Migration | Lift-and-shift servers |
| **DMS** | Database Migration | Migrate databases |
| **DataSync** | File Transfer | Online file transfer |
| **Transfer Family** | FTP/SFTP | Legacy file transfer |
| **Snow Family** | Offline Transfer | Large offline migrations |
| **Migration Evaluator** | Business Case | Build migration business case |
| **Mainframe Modernization** | Mainframe | Migrate mainframe apps |

---

## Migration Strategy Decision Tree

**Question: What are you migrating?**

### Servers/VMs
- **Lift-and-Shift** → **MGN** (Application Migration Service)
- **Assessment** → **Application Discovery Service**
- **Tracking** → **Migration Hub**

### Databases
- **Same Engine** → **DMS** (homogeneous)
- **Different Engine** → **DMS + SCT** (heterogeneous)
- **Very Large (> 10 TB)** → **DMS + Snowball Edge**

### Files
- **Online Transfer** → **DataSync** (NFS, SMB, HDFS)
- **FTP/SFTP** → **Transfer Family**
- **Offline (Large)** → **Snow Family**

### Data
- **< 10 TB** → **DataSync** or **Direct Connect**
- **10 TB - 10 PB** → **Snowball Edge**
- **> 10 PB** → **Snowmobile**

### Mainframe
- **Mainframe Apps** → **Mainframe Modernization**

### Business Case
- **Cost Analysis** → **Migration Evaluator**

---

## Migration Best Practices

### Planning
1. Discover and assess (Application Discovery Service)
2. Build business case (Migration Evaluator)
3. Choose migration strategy (7 R's)
4. Prioritize applications (Migration Hub)

### Execution
1. **Rehost**: MGN for servers, DMS for databases
2. **Replatform**: Minor optimizations during migration
3. **Refactor**: Rebuild for cloud-native
4. **Repurchase**: Move to SaaS
5. **Retire**: Decommission unnecessary systems
6. **Retain**: Keep on-premises (for now)
7. **Relocate**: VMware Cloud on AWS

### Data Transfer
1. **< 1 TB**: Direct Connect or VPN
2. **1-10 TB**: DataSync
3. **10-80 TB**: Snowball Edge
4. **> 10 PB**: Snowmobile

### Testing
1. Test in AWS before cutover
2. Validate functionality
3. Performance testing
4. Security validation

### Cutover
1. Choose maintenance window
2. Final data sync
3. Switch DNS
4. Validate
5. Monitor

---

## Common Migration Patterns

### Pattern 1: Rehost (Lift-and-Shift)
**Services**: MGN, DMS
**Timeline**: Fast (weeks)
**Cost**: Lower migration cost
**Optimization**: Post-migration

### Pattern 2: Replatform
**Services**: MGN + manual changes, DMS
**Timeline**: Medium (months)
**Cost**: Medium
**Optimization**: During migration

### Pattern 3: Refactor
**Services**: Rewrite application
**Timeline**: Long (months-years)
**Cost**: Highest migration cost
**Optimization**: Full cloud-native

### Pattern 4: Hybrid
**Services**: Direct Connect, Storage Gateway, DataSync
**Timeline**: Ongoing
**Cost**: Varies
**Optimization**: Incremental

---

## Exam Focus Areas

1. **MGN**: Lift-and-shift servers, continuous replication
2. **DMS**: Database migrations, CDC, SCT for heterogeneous
3. **DataSync**: Online file transfer, fast, automated
4. **Snow Family**: Offline transfers, Snowball for 10TB-10PB
5. **Transfer Family**: FTP/SFTP to S3/EFS
6. **Migration Hub**: Central tracking, no cost
7. **Application Discovery**: Assessment, agentless vs agent
8. **7 R's**: Migration strategies
9. **DMS + Snowball**: Very large databases
10. **DataSync vs Snow**: Online vs offline

---

## Migration Decision Scenarios

### Scenario 1: 100 TB of files, good network
**Solution**: DataSync (online transfer)

### Scenario 2: 50 TB database, minimize downtime
**Solution**: DMS with CDC (continuous replication)

### Scenario 3: 500 servers to AWS, lift-and-shift
**Solution**: MGN (Application Migration Service)

### Scenario 4: 5 PB data, limited network
**Solution**: Snowball Edge (multiple devices)

### Scenario 5: Legacy FTP application
**Solution**: Transfer Family (SFTP to S3)

### Scenario 6: Oracle to Aurora migration
**Solution**: DMS + SCT (heterogeneous migration)

### Scenario 7: VMware vCenter assessment
**Solution**: Application Discovery Service (agentless)

### Scenario 8: Track multi-tool migration
**Solution**: Migration Hub

### Scenario 9: 500 TB database, slow network
**Solution**: DMS + Snowball Edge

### Scenario 10: Build migration business case
**Solution**: Migration Evaluator

---

*Last Updated: January 2026 - SAA-C03 Exam*
