# AWS SAA-C03 CHEATSHEET: CONTAINERS, MIGRATION & ANALYTICS

## 📋 PRINT-READY EXAM REFERENCE

---

# 🐳 CONTAINER SERVICES

## Amazon ECS (Elastic Container Service)

### Definition
AWS-native container orchestration service.

### ECS Launch Types

| Type | Description | Use Case |
|------|-------------|----------|
| **EC2** | You manage EC2 instances | Control, GPU, compliance |
| **Fargate** | Serverless containers | Simplicity, pay-per-use |

### ECS Components

| Component | Description |
|-----------|-------------|
| **Cluster** | Logical grouping |
| **Task Definition** | Container config (CPU, memory, image) |
| **Task** | Running container(s) |
| **Service** | Maintains desired task count |
| **Container Instance** | EC2 with ECS agent |

### ECS vs Fargate

| Feature | EC2 Launch | Fargate |
|---------|------------|---------|
| **Management** | You manage EC2 | Serverless |
| **Pricing** | EC2 instance pricing | Per vCPU/memory |
| **Scaling** | Auto Scaling groups | Automatic |
| **GPU** | Yes | Limited |
| **Windows** | Yes | Yes |
| **Spot** | Yes | Yes |

### ECS Task Placement

| Strategy | Description |
|----------|-------------|
| **binpack** | Minimize instances (cost) |
| **spread** | Spread across AZs/instances |
| **random** | Random placement |

### ECS + ALB

| Feature | Description |
|---------|-------------|
| **Dynamic port mapping** | Multiple tasks on same instance |
| **Path-based routing** | Route to different services |
| **Target groups** | ECS service registered |

---

## Amazon EKS (Elastic Kubernetes Service)

### Definition
Managed Kubernetes service.

### EKS Features

| Feature | Description |
|---------|-------------|
| **Managed control plane** | AWS manages masters |
| **Open source** | Compatible with Kubernetes |
| **Node types** | EC2, Fargate, managed node groups |
| **Add-ons** | VPC CNI, CoreDNS, kube-proxy |

### EKS Node Types

| Type | Description |
|------|-------------|
| **Managed Node Groups** | AWS manages EC2 nodes |
| **Self-managed** | You manage EC2 nodes |
| **Fargate** | Serverless pods |

### EKS + Fargate

| Feature | Description |
|---------|-------------|
| **Pod-level** | Each pod runs on Fargate |
| **Fargate Profile** | Define which pods use Fargate |
| **Limitations** | No DaemonSets, limited volumes |

---

## Amazon ECR (Elastic Container Registry)

### Definition
Managed Docker container registry.

### ECR Features

| Feature | Description |
|---------|-------------|
| **Private** | Private repositories |
| **Public** | ECR Public Gallery |
| **Scan** | Vulnerability scanning |
| **Encryption** | KMS encryption |
| **Lifecycle** | Image cleanup policies |
| **Replication** | Cross-region, cross-account |

### ECR Key Points
- Integrated with ECS, EKS
- Use IAM for access control
- Images scanned with Inspector

---

## AWS App Runner

### Definition
Fully managed container application service.

### App Runner Features

| Feature | Description |
|---------|-------------|
| **Source** | Container image or source code |
| **Auto scaling** | Automatic |
| **HTTPS** | Built-in |
| **Custom domain** | Supported |
| **Use case** | Web apps, APIs |

---

# 🚚 MIGRATION SERVICES

## AWS Migration Hub

### Definition
Central location to track migrations.

### Features
- Track migration progress
- Integrate with migration tools
- Application discovery

---

## AWS Application Discovery Service

### Definition
Discover on-premises applications for migration.

### Discovery Types

| Type | Description |
|------|-------------|
| **Agentless** | VM inventory, configuration |
| **Agent-based** | Detailed info, dependencies |

---

## AWS Application Migration Service (MGN)

### Definition
Lift-and-shift migration service (formerly CloudEndure).

### MGN Features

| Feature | Description |
|---------|-------------|
| **Replication** | Continuous block-level |
| **Testing** | Non-disruptive testing |
| **Cutover** | Minutes of downtime |
| **OS Support** | Windows, Linux |

### MGN Process
1. Install replication agent
2. Continuous replication to staging
3. Test migrated instances
4. Cutover (switch to AWS)

---

## AWS Database Migration Service (DMS)

### Definition
Migrate databases to AWS.

### DMS Features

| Feature | Description |
|---------|-------------|
| **Homogeneous** | Same engine (Oracle → Oracle) |
| **Heterogeneous** | Different engine (Oracle → PostgreSQL) |
| **Continuous** | CDC (Change Data Capture) |
| **SCT** | Schema Conversion Tool |

### When to Use SCT

| Migration | Need SCT? |
|-----------|-----------|
| Oracle → Oracle | No |
| SQL Server → RDS SQL Server | No |
| Oracle → PostgreSQL | Yes |
| SQL Server → Aurora | Yes |

### DMS Endpoints

| Endpoint | Description |
|----------|-------------|
| **Source** | Where data comes from |
| **Target** | Where data goes |
| **Task** | Replication configuration |

### DMS Sources
- On-premises databases
- EC2 databases
- RDS
- S3
- Azure SQL

### DMS Targets
- RDS
- Aurora
- DynamoDB
- S3
- Redshift
- Kinesis
- OpenSearch

---

## AWS DataSync

### Definition
Data transfer service for NFS, SMB, S3, EFS, FSx.

### DataSync Features

| Feature | Description |
|---------|-------------|
| **Speed** | Up to 10 Gbps |
| **Agent** | On-premises agent required |
| **Encryption** | In-transit encryption |
| **Scheduling** | Hourly, daily, weekly |
| **Validation** | Data integrity verification |

### DataSync Use Cases

| From | To |
|------|-----|
| On-premises NFS/SMB | S3, EFS, FSx |
| AWS to AWS | S3 ↔ EFS, S3 ↔ FSx |

### DataSync vs Storage Gateway

| Feature | DataSync | Storage Gateway |
|---------|----------|-----------------|
| **Purpose** | Migration/sync | Ongoing hybrid |
| **Direction** | One-time or scheduled | Continuous |
| **Protocol** | NFS/SMB | NFS/SMB/iSCSI |

---

## AWS Transfer Family

### Definition
Managed file transfer to/from S3 and EFS.

### Protocols Supported
- SFTP
- FTPS
- FTP
- AS2

### Transfer Family Features

| Feature | Description |
|---------|-------------|
| **Integration** | AD, LDAP, custom IdP |
| **Storage** | S3, EFS |
| **Endpoint** | Public or VPC |
| **Use case** | B2B file exchange |

---

# 📊 ANALYTICS SERVICES

## Amazon Athena

### Definition
Serverless query service for S3 using SQL.

### Athena Features

| Feature | Description |
|---------|-------------|
| **Serverless** | No infrastructure |
| **Pricing** | $5 per TB scanned |
| **Format** | CSV, JSON, Parquet, ORC |
| **Integration** | Glue Data Catalog |

### Athena Optimization

| Technique | Benefit |
|-----------|---------|
| **Columnar** | Less data scanned |
| **Partitioning** | Query subset of data |
| **Compression** | Less data scanned |
| **Larger files** | Fewer operations |

### Athena Use Cases
- Ad-hoc queries on S3
- Log analysis
- CloudTrail analysis
- Cost and usage reports

---

## AWS Glue

### Definition
Serverless ETL service.

### Glue Components

| Component | Description |
|-----------|-------------|
| **Data Catalog** | Metadata repository |
| **Crawlers** | Discover schema |
| **ETL Jobs** | Transform data |
| **Studio** | Visual ETL |

### Glue Features

| Feature | Description |
|---------|-------------|
| **Serverless** | No infrastructure |
| **Spark/Python** | ETL languages |
| **Bookmarks** | Track processed data |
| **Workflows** | Orchestrate jobs |

### Glue Data Catalog
- Central metadata store
- Used by Athena, Redshift, EMR
- Crawlers auto-detect schema

---

## Amazon EMR (Elastic MapReduce)

### Definition
Managed Hadoop/Spark cluster.

### EMR Features

| Feature | Description |
|---------|-------------|
| **Frameworks** | Hadoop, Spark, Presto, Hive |
| **Storage** | HDFS, EMRFS (S3), EBS |
| **Node types** | Master, Core, Task |
| **Pricing** | On-Demand, Reserved, Spot |

### EMR Node Types

| Node | Description |
|------|-------------|
| **Master** | Manage cluster |
| **Core** | Run tasks + store data |
| **Task** | Run tasks only (optional) |

### EMR Use Cases
- Big data processing
- Machine learning
- Log analysis
- ETL

---

## Amazon QuickSight

### Definition
Serverless BI service for visualizations.

### QuickSight Features

| Feature | Description |
|---------|-------------|
| **SPICE** | In-memory engine |
| **ML Insights** | Anomaly detection |
| **Sources** | RDS, Redshift, S3, Athena |
| **Sharing** | Dashboard sharing |

---

## Amazon OpenSearch (Elasticsearch)

### Definition
Managed search and analytics service.

### OpenSearch Features

| Feature | Description |
|---------|-------------|
| **Full-text search** | Document search |
| **Log analytics** | ELK stack |
| **Visualization** | OpenSearch Dashboards |
| **Serverless** | OpenSearch Serverless |

### OpenSearch Use Cases
- Application search
- Log analytics (CloudWatch, VPC Flow)
- SIEM
- Clickstream analytics

---

## Amazon Kinesis Data Analytics

### Definition
SQL queries on streaming data.

### Features
- Standard SQL
- Apache Flink
- Real-time processing
- Sources: Kinesis Streams, Firehose

---

## AWS Lake Formation

### Definition
Set up secure data lakes easily.

### Lake Formation Features

| Feature | Description |
|---------|-------------|
| **Central catalog** | Unified metadata |
| **Security** | Fine-grained access |
| **Blueprints** | Pre-built ingest |
| **ML** | FindMatches transform |

---

# 🎯 CONTAINER & MIGRATION HEURISTICS

| Keyword/Scenario | Answer |
|------------------|--------|
| Container orchestration, AWS native | **ECS** |
| Kubernetes | **EKS** |
| Serverless containers | **Fargate** |
| Container registry | **ECR** |
| Simple web app containers | **App Runner** |
| Lift-and-shift migration | **Application Migration Service (MGN)** |
| Database migration | **DMS** |
| Different DB engines | **DMS + SCT** |
| File transfer to S3/EFS | **DataSync** |
| SFTP to S3 | **Transfer Family** |
| Query S3 with SQL | **Athena** |
| ETL serverless | **Glue** |
| Big data Hadoop/Spark | **EMR** |
| BI dashboards | **QuickSight** |
| Search engine | **OpenSearch** |
| Log analytics | **OpenSearch** |
| Secure data lake | **Lake Formation** |

---

# 📊 ANALYTICS SERVICE SELECTION

| Use Case | Service |
|----------|---------|
| Query S3 ad-hoc | Athena |
| Complex analytics | Redshift |
| ETL jobs | Glue |
| Big data processing | EMR |
| Real-time streaming | Kinesis |
| Search | OpenSearch |
| Visualization | QuickSight |
| Data lake security | Lake Formation |

---

# ✅ EXAM DAY QUICK CHECKS

```
□ Know ECS launch types (EC2 vs Fargate)
□ Know EKS node types
□ Know DMS homogeneous vs heterogeneous
□ Know when SCT is needed
□ Know DataSync vs Storage Gateway
□ Know Athena use cases and optimization
□ Know Glue components
□ Know EMR node types
□ Know OpenSearch use cases
```

---

*Last Updated: December 2025*
*Exam Version: SAA-C03*
