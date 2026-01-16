# AWS SAA-C03: One-Liner Service Definitions & Features

## 🎯 Quick Reference for All Exam Services

---

## 🖥️ COMPUTE SERVICES

| Service | One-Liner Definition | Key Feature |
|---------|---------------------|-------------|
| **EC2** | Virtual servers in the cloud with full OS control | Choose instance type, AMI, and pricing model |
| **EC2 Auto Scaling** | Automatically adjusts EC2 capacity based on demand | Maintains availability and optimizes costs |
| **Lambda** | Serverless compute that runs code in response to events | Max 15 min execution, pay per invocation |
| **Elastic Beanstalk** | PaaS that deploys and manages web apps automatically | Handles capacity, load balancing, scaling |
| **ECS** | AWS-native container orchestration service | Deep AWS integration, simpler than Kubernetes |
| **EKS** | Managed Kubernetes service on AWS | Use when you need Kubernetes specifically |
| **Fargate** | Serverless compute engine for containers | No server management for ECS/EKS |
| **AWS Batch** | Runs batch computing jobs at any scale | Automatically provisions optimal compute |
| **Lightsail** | Simple VPS for small projects and beginners | Fixed monthly pricing, pre-configured |
| **Outposts** | Run AWS infrastructure on-premises | Hybrid cloud with consistent AWS experience |
| **Wavelength** | Deploy apps at 5G network edge for ultra-low latency | Single-digit millisecond latency to mobile |
| **Local Zones** | Extend AWS regions closer to users | Low-latency apps in specific geographic areas |

---

## 💾 STORAGE SERVICES

| Service | One-Liner Definition | Key Feature |
|---------|---------------------|-------------|
| **S3** | Object storage with unlimited scalability and 11 9s durability | Storage classes for cost optimization |
| **S3 Glacier** | Low-cost archive storage for long-term data retention | Retrieval options: minutes to hours |
| **S3 Glacier Deep Archive** | Lowest cost storage for 7+ year retention | 12-48 hour retrieval time |
| **EBS** | Block storage volumes for EC2 instances | Persistent, can be attached/detached |
| **EBS Snapshots** | Point-in-time backups of EBS volumes stored in S3 | Incremental, only changed blocks stored |
| **Instance Store** | Temporary block storage physically attached to host | Highest performance, data lost on stop |
| **EFS** | Managed NFS file system for Linux workloads | Shared across multiple EC2, auto-scaling |
| **FSx for Windows** | Managed Windows file system with SMB protocol | Active Directory integration, NTFS |
| **FSx for Lustre** | High-performance parallel file system for HPC | Integrates with S3, sub-millisecond latency |
| **FSx for NetApp ONTAP** | Managed NetApp with multi-protocol support | NFS, SMB, iSCSI, snapshots, cloning |
| **FSx for OpenZFS** | Managed OpenZFS with snapshots and cloning | Linux workloads needing ZFS features |
| **Storage Gateway** | Hybrid cloud storage connecting on-prem to AWS | File, Volume, and Tape Gateway modes |
| **AWS Backup** | Centralized backup service across AWS services | Policy-based, automated backup management |

---

## 🗄️ DATABASE SERVICES

| Service | One-Liner Definition | Key Feature |
|---------|---------------------|-------------|
| **RDS** | Managed relational database (MySQL, PostgreSQL, Oracle, SQL Server, MariaDB) | Automated backups, patching, Multi-AZ |
| **Aurora** | AWS-built MySQL/PostgreSQL with 5x performance | Auto-scales storage, 6-way replication |
| **Aurora Serverless** | On-demand auto-scaling Aurora database | Scales to zero, pay per ACU-second |
| **Aurora Global Database** | Aurora with cross-region replication < 1 second | RPO < 1 sec, RTO < 1 min |
| **DynamoDB** | Serverless NoSQL key-value and document database | Single-digit millisecond latency at any scale |
| **DynamoDB Global Tables** | Multi-region, multi-active DynamoDB replication | Active-active across regions |
| **DAX** | In-memory cache for DynamoDB | Microsecond latency for reads |
| **ElastiCache Redis** | Managed Redis with persistence and replication | Caching, sessions, leaderboards, pub/sub |
| **ElastiCache Memcached** | Managed Memcached for simple caching | Multi-threaded, no persistence |
| **Redshift** | Petabyte-scale data warehouse for OLAP | Columnar storage, MPP architecture |
| **Redshift Spectrum** | Query S3 data directly from Redshift | No loading required, extends Redshift |
| **DocumentDB** | Managed MongoDB-compatible document database | MongoDB workloads on AWS |
| **Neptune** | Managed graph database for connected data | Social networks, fraud detection, knowledge graphs |
| **Timestream** | Serverless time-series database for IoT and metrics | Auto-scaling, built-in analytics |
| **QLDB** | Immutable, cryptographically verifiable ledger database | Complete audit trail, no blockchain needed |
| **Keyspaces** | Managed Apache Cassandra-compatible database | Wide-column NoSQL, serverless |
| **MemoryDB** | Redis-compatible durable in-memory database | Durability with Redis speed |

---

## 🌐 NETWORKING SERVICES

| Service | One-Liner Definition | Key Feature |
|---------|---------------------|-------------|
| **VPC** | Isolated virtual network for your AWS resources | Full control over IP ranges, subnets, routing |
| **Subnets** | Segments of VPC IP range in a single AZ | Public (IGW route) or Private (no IGW route) |
| **Internet Gateway** | Enables internet access for VPC resources | Horizontally scaled, redundant |
| **NAT Gateway** | Allows private subnet instances to access internet | Managed, one per AZ for HA |
| **Security Groups** | Stateful instance-level firewall (allow only) | Inbound/outbound rules, no deny rules |
| **NACLs** | Stateless subnet-level firewall (allow and deny) | Numbered rules, evaluated in order |
| **Route 53** | Scalable DNS and domain registration service | Health checks, routing policies |
| **CloudFront** | Global CDN for low-latency content delivery | Edge locations, caching, SSL/TLS |
| **Global Accelerator** | Network layer accelerator using AWS backbone | Static anycast IPs, no caching |
| **API Gateway** | Managed service to create and publish APIs | REST, HTTP, WebSocket APIs |
| **VPC Peering** | Private connection between two VPCs | Non-transitive, no overlapping CIDRs |
| **Transit Gateway** | Hub to connect VPCs and on-prem networks | Transitive routing, scales to thousands |
| **VPC Endpoints (Gateway)** | Private access to S3 and DynamoDB | Free, uses route table entries |
| **VPC Endpoints (Interface)** | Private access to AWS services via ENI | Uses PrivateLink, costs per hour/GB |
| **PrivateLink** | Private connectivity to services without internet | Keeps traffic on AWS network |
| **Direct Connect** | Dedicated private connection from on-prem to AWS | 1/10/100 Gbps, consistent latency |
| **Site-to-Site VPN** | Encrypted tunnel over internet to AWS | Quick setup, IPsec encryption |
| **Client VPN** | Managed VPN for remote user access to AWS | OpenVPN-based, scales automatically |

---

## ⚖️ LOAD BALANCERS

| Service | One-Liner Definition | Key Feature |
|---------|---------------------|-------------|
| **ALB** | Layer 7 load balancer for HTTP/HTTPS traffic | Path/host routing, WebSocket, Lambda targets |
| **NLB** | Layer 4 load balancer for TCP/UDP traffic | Ultra-low latency, static IPs, millions RPS |
| **GWLB** | Load balancer for third-party virtual appliances | Deploy firewalls, IDS/IPS at scale |
| **CLB** | Legacy load balancer (Layer 4 and 7) | Deprecated, use ALB or NLB instead |

---

## 🔒 SECURITY SERVICES

| Service | One-Liner Definition | Key Feature |
|---------|---------------------|-------------|
| **IAM** | Manage users, groups, roles, and permissions | Free, global service, least privilege |
| **IAM Roles** | Temporary credentials for AWS service access | No long-term credentials, assume role |
| **IAM Policies** | JSON documents defining permissions | Identity-based or Resource-based |
| **AWS Organizations** | Centrally manage multiple AWS accounts | SCPs, consolidated billing, OUs |
| **Service Control Policies** | Organization-wide permission guardrails | Restrict maximum permissions |
| **IAM Identity Center** | SSO for AWS accounts and business apps | Centralized access, SAML federation |
| **Cognito User Pools** | User directory for app authentication | Sign-up, sign-in, MFA, social login |
| **Cognito Identity Pools** | Temporary AWS credentials for app users | Access AWS services from apps |
| **Directory Service** | Managed Microsoft Active Directory on AWS | AD Connector, Simple AD, Managed AD |
| **KMS** | Managed encryption key service | Automatic rotation, audit via CloudTrail |
| **CloudHSM** | Dedicated hardware security module | FIPS 140-2 Level 3, you control keys |
| **Secrets Manager** | Store and rotate secrets automatically | Database credential rotation built-in |
| **Parameter Store** | Store configuration and secrets (free tier) | Hierarchical storage, no auto-rotation |
| **ACM** | Free SSL/TLS certificates for AWS services | Auto-renewal, integrates with ALB/CloudFront |
| **WAF** | Web application firewall for Layer 7 attacks | SQL injection, XSS protection, rate limiting |
| **Shield Standard** | Free DDoS protection for all AWS customers | Automatic, always-on protection |
| **Shield Advanced** | Enhanced DDoS protection with 24/7 support | Cost protection, DRT access |
| **Network Firewall** | Managed firewall for VPC traffic filtering | Stateful inspection, IDS/IPS |
| **Firewall Manager** | Centrally manage firewall rules across accounts | WAF, Shield, Security Groups, Network Firewall |
| **GuardDuty** | ML-based threat detection service | Analyzes CloudTrail, VPC Flow Logs, DNS |
| **Inspector** | Automated vulnerability scanning | EC2, Lambda, ECR container images |
| **Macie** | Discover and protect sensitive data in S3 | PII detection using ML |
| **Detective** | Investigate and analyze security findings | Visualize relationships, root cause analysis |
| **Security Hub** | Centralized security findings dashboard | Aggregates GuardDuty, Inspector, Macie |

---

## 📊 MONITORING & MANAGEMENT

| Service | One-Liner Definition | Key Feature |
|---------|---------------------|-------------|
| **CloudWatch Metrics** | Collect and track AWS resource metrics | Built-in and custom metrics |
| **CloudWatch Logs** | Centralized log collection and analysis | Log groups, retention, metric filters |
| **CloudWatch Alarms** | Trigger actions based on metric thresholds | Auto Scaling, SNS notifications |
| **CloudWatch Events/EventBridge** | Event-driven automation and routing | Cron schedules, event patterns |
| **CloudTrail** | Audit log of all API calls in your account | Governance, compliance, security analysis |
| **AWS Config** | Track resource configuration changes | Compliance rules, configuration history |
| **X-Ray** | Distributed tracing for application analysis | Trace requests across services |
| **CloudFormation** | Infrastructure as Code using templates | JSON/YAML, stack management, drift detection |
| **CDK** | Define infrastructure using programming languages | TypeScript, Python, Java, generates CloudFormation |
| **SAM** | Framework for serverless application deployment | Extends CloudFormation for Lambda |
| **Systems Manager** | Operational hub for AWS resource management | Patch Manager, Session Manager, Run Command |
| **Session Manager** | Secure shell access without SSH keys or bastion | Audit logging, no inbound ports needed |
| **Trusted Advisor** | Best practices recommendations | Cost, security, performance, fault tolerance |
| **Compute Optimizer** | Right-sizing recommendations for EC2 | ML-based, analyzes utilization |
| **Service Catalog** | IT-approved product catalog for self-service | Standardized deployments |
| **Control Tower** | Set up and govern multi-account environment | Landing zone, guardrails |
| **License Manager** | Track and manage software licenses | Enforce licensing rules |
| **Personal Health Dashboard** | Personalized view of AWS service health | Proactive notifications |
| **Well-Architected Tool** | Review workloads against best practices | 6 pillars assessment |

---

## 💰 COST MANAGEMENT

| Service | One-Liner Definition | Key Feature |
|---------|---------------------|-------------|
| **Cost Explorer** | Visualize and analyze AWS spending | Forecasting, filtering, grouping |
| **AWS Budgets** | Set custom budgets with alerts | Cost, usage, RI utilization alerts |
| **Cost and Usage Reports** | Most detailed billing data available | Hourly, resource-level data |
| **Savings Plans** | Flexible pricing for consistent compute usage | Up to 72% savings, applies automatically |
| **Reserved Instances** | Commit to 1 or 3 years for discounts | Standard (up to 72%) or Convertible (up to 66%) |
| **Spot Instances** | Spare EC2 capacity at up to 90% discount | Can be interrupted with 2-min notice |

---

## 🔗 APPLICATION INTEGRATION

| Service | One-Liner Definition | Key Feature |
|---------|---------------------|-------------|
| **SQS** | Fully managed message queuing service | Decouple components, Standard or FIFO |
| **SQS Standard** | At-least-once delivery, best-effort ordering | Unlimited throughput |
| **SQS FIFO** | Exactly-once processing, strict ordering | 300 TPS (3000 with batching) |
| **SNS** | Pub/sub messaging for fan-out | Push to multiple subscribers |
| **EventBridge** | Serverless event bus for event-driven apps | Rules, scheduling, SaaS integration |
| **Step Functions** | Visual workflow orchestration service | State machines, error handling |
| **AppSync** | Managed GraphQL API service | Real-time subscriptions, offline sync |
| **Amazon MQ** | Managed ActiveMQ and RabbitMQ | Migrate existing messaging apps |

---

## 📈 ANALYTICS & BIG DATA

| Service | One-Liner Definition | Key Feature |
|---------|---------------------|-------------|
| **Athena** | Serverless SQL queries on S3 data | Pay per query, no infrastructure |
| **EMR** | Managed Hadoop/Spark for big data processing | Petabyte-scale, spot instances |
| **Glue** | Serverless ETL and data catalog service | Auto-generates ETL code |
| **Glue Data Catalog** | Centralized metadata repository | Schema discovery, integrates with Athena |
| **Kinesis Data Streams** | Real-time data streaming at scale | Capture, process, store streaming data |
| **Kinesis Data Firehose** | Load streaming data to destinations | Near real-time to S3, Redshift, OpenSearch |
| **Kinesis Data Analytics** | SQL/Flink on streaming data | Real-time analytics |
| **Kinesis Video Streams** | Stream video from devices to AWS | ML processing, playback |
| **OpenSearch Service** | Managed Elasticsearch for search and analytics | Log analytics, full-text search |
| **QuickSight** | Serverless BI and visualization | ML-powered insights, dashboards |
| **Data Pipeline** | Orchestrate data movement and transformation | Scheduled, on-prem support |
| **MSK** | Managed Apache Kafka for streaming | Compatible with Kafka applications |
| **Lake Formation** | Build and manage data lakes | Security, governance, data sharing |

---

## 🚀 MIGRATION & TRANSFER

| Service | One-Liner Definition | Key Feature |
|---------|---------------------|-------------|
| **Migration Hub** | Central tracking for migration projects | Track progress across services |
| **Application Discovery Service** | Discover on-premises applications | Agentless or agent-based discovery |
| **Application Migration Service (MGN)** | Lift-and-shift servers to AWS | Continuous replication, minimal downtime |
| **Database Migration Service (DMS)** | Migrate databases with minimal downtime | Homogeneous and heterogeneous |
| **Schema Conversion Tool (SCT)** | Convert database schemas between engines | Use with DMS for heterogeneous migrations |
| **DataSync** | Automate data transfer to/from AWS | 10x faster than open-source tools |
| **Transfer Family** | Managed SFTP, FTPS, FTP for S3/EFS | Integrate with existing workflows |
| **Snowcone** | Small, rugged edge device (8 TB) | Remote locations, IoT |
| **Snowball Edge** | Petabyte-scale data transfer device (80 TB) | Storage Optimized or Compute Optimized |
| **Snowmobile** | Exabyte-scale container truck (100 PB) | Data center migrations |

---

## 🤖 MACHINE LEARNING & AI

| Service | One-Liner Definition | Key Feature |
|---------|---------------------|-------------|
| **SageMaker** | Build, train, and deploy ML models | End-to-end ML platform |
| **Rekognition** | Image and video analysis with deep learning | Face detection, object recognition |
| **Textract** | Extract text and data from documents | Forms, tables, handwriting |
| **Comprehend** | Natural language processing service | Sentiment, entities, key phrases |
| **Translate** | Neural machine translation | Real-time and batch translation |
| **Polly** | Text-to-speech service | Lifelike voices, SSML support |
| **Transcribe** | Speech-to-text service | Real-time and batch transcription |
| **Lex** | Build conversational chatbots | Same tech as Alexa |
| **Kendra** | Intelligent enterprise search | ML-powered, natural language queries |
| **Personalize** | Real-time personalization and recommendations | Same tech as Amazon.com |
| **Forecast** | Time-series forecasting service | ML-based demand planning |
| **Fraud Detector** | Detect online fraud using ML | Pre-trained models |

---

## 📱 DEVELOPER TOOLS

| Service | One-Liner Definition | Key Feature |
|---------|---------------------|-------------|
| **CodeCommit** | Managed Git repository service | Private repos, IAM integration |
| **CodeBuild** | Managed build service | Compile, test, produce artifacts |
| **CodeDeploy** | Automated deployment service | EC2, Lambda, on-premises |
| **CodePipeline** | CI/CD pipeline automation | Orchestrate build, test, deploy |
| **CodeArtifact** | Managed artifact repository | npm, PyPI, Maven packages |
| **Cloud9** | Cloud-based IDE | Browser-based development |
| **X-Ray** | Distributed tracing and debugging | Analyze and debug microservices |

---

## 🌍 OTHER SERVICES

| Service | One-Liner Definition | Key Feature |
|---------|---------------------|-------------|
| **Elastic Transcoder** | Convert media files to different formats | Scalable video transcoding |
| **MediaConvert** | File-based video transcoding | Broadcast-grade, more features |
| **SES** | Email sending and receiving service | Transactional and marketing emails |
| **Pinpoint** | Multi-channel user engagement | Push, email, SMS, voice |
| **WorkSpaces** | Managed virtual desktops (DaaS) | Windows or Linux desktops |
| **AppStream 2.0** | Stream desktop applications | Application streaming, not full desktop |
| **IoT Core** | Connect IoT devices to AWS | MQTT, device management |
| **IoT Greengrass** | Run local compute on IoT devices | Edge computing, ML inference |

---

## 🎯 EXAM DAY QUICK MEMORY AIDS

### Storage Duration Keywords
| Keyword | Service |
|---------|---------|
| Temporary/Ephemeral | Instance Store |
| Persistent block | EBS |
| Shared Linux (NFS) | EFS |
| Shared Windows (SMB) | FSx for Windows |
| Object/Unlimited | S3 |
| Archive | Glacier |

### Database Keywords
| Keyword | Service |
|---------|---------|
| Relational + AWS managed | RDS/Aurora |
| Key-value/Document NoSQL | DynamoDB |
| Graph | Neptune |
| Ledger/Immutable | QLDB |
| Time-series | Timestream |
| Data warehouse | Redshift |
| Cache | ElastiCache/DAX |

### Security Keywords
| Keyword | Service |
|---------|---------|
| Who can do what | IAM |
| Encryption keys | KMS |
| Hardware keys | CloudHSM |
| Secrets rotation | Secrets Manager |
| API audit | CloudTrail |
| Config tracking | Config |
| Threat detection | GuardDuty |
| Vulnerability scan | Inspector |
| PII in S3 | Macie |

### Integration Keywords
| Keyword | Service |
|---------|---------|
| Decouple/Queue | SQS |
| Fan-out/Pub-sub | SNS |
| Event routing | EventBridge |
| Workflow | Step Functions |
| Real-time stream | Kinesis |

---

