# AWS SAA-C03: ONE-PAGE EXAM DAY CHEAT SHEET

**Print this. Memorize this. Pass the exam. 🚀**

---

## ⚡ INSTANT SERVICE SELECTION (ALL Services with Constraints)

### 🖥️ COMPUTE (11 Services)

| Service | Use When | Key Constraint | Don't Use When |
|---------|----------|----------------|----------------|
| **EC2** | Full control, custom apps, >15min tasks | Manual scaling, patching | < 15 min tasks, no management |
| **Lambda** | Event-driven, < 15 min, serverless | **15 min max, 10 GB memory, 512 MB /tmp** | Long tasks, stateful, large deps |
| **Elastic Beanstalk** | Deploy app fast, PaaS | Limited customization | Need full infra control |
| **ECS** | Docker containers, AWS-native | Manual cluster management | Need Kubernetes |
| **EKS** | Kubernetes required, multi-cloud | Complex, expensive | Simple containers |
| **Fargate** | Serverless containers, no management | Higher cost | Cost-sensitive, need node control |
| **AWS Batch** | Batch jobs, any scale, auto-scheduling | Job-based only | Real-time processing |
| **Lightsail** | Simple VPS, fixed pricing | **Limited scalability, basic features** | Enterprise, complex architecture |
| **Outposts** | On-premises + AWS, hybrid | **High upfront cost, min 42U rack** | Cloud-only workloads |
| **Wavelength** | 5G ultra-low latency, mobile edge | **Limited locations, 5G only** | Standard latency OK |
| **Local Zones** | Low latency in specific cities | **Limited services, specific metros** | Standard regions work |

### 💾 STORAGE (13 Services)

| Service | Use When | Key Constraint | Don't Use When |
|---------|----------|----------------|----------------|
| **S3** | Object storage, unlimited, web | **5 TB max object, eventual consistency on overwrite** | Block storage, low latency |
| **S3 Standard** | Frequent access, < 1 month | Higher cost | Infrequent access |
| **S3 Intelligent-Tiering** | Unknown/changing patterns | Small monitoring fee | Known pattern |
| **S3 Standard-IA** | Infrequent + instant, > 30 days | **30 day min, retrieval fee** | Frequent access |
| **S3 One Zone-IA** | Infrequent + single AZ + recreatable | **One AZ = no HA** | Need durability |
| **S3 Glacier Instant** | Archive + instant retrieval | **90 day min, higher than IA** | Frequent access |
| **S3 Glacier Flexible** | Archive + 1-5 hour retrieval | **90 day min, retrieval time** | Instant access |
| **S3 Glacier Deep** | 7+ years, 12 hour retrieval | **180 day min, 12 hr retrieval** | < 7 year retention |
| **EBS** | Block storage, single EC2, persistent | **Single AZ, single EC2 attach** | Shared storage, multi-AZ |
| **Instance Store** | Temp storage, highest IOPS | **Ephemeral - lost on stop/terminate** | Need persistence |
| **EFS** | Shared Linux file system (NFS) | Linux only, higher cost | Windows, single instance |
| **FSx for Windows** | Windows SMB, shared | **Windows only, higher cost** | Linux workloads |
| **FSx for Lustre** | HPC, ML, parallel processing | **Linked to S3, specific use case** | General file storage |

### 🗄️ DATABASE (15 Services)

| Service | Use When | Key Constraint | Don't Use When |
|---------|----------|----------------|----------------|
| **RDS** | Relational DB, managed, standard | **Max 64 TB storage** | NoSQL, > 64 TB |
| **Aurora** | Relational + 5x performance | Higher cost than RDS | Cost-sensitive, small DB |
| **Aurora Serverless** | Variable workload, auto-scale | Scaling delay | Consistent load |
| **DynamoDB** | NoSQL, serverless, key-value | **400 KB item max, no complex queries** | Relational, complex joins |
| **ElastiCache Redis** | In-memory cache, DB cache | **In-memory = expensive, volatile** | Persistent primary DB |
| **ElastiCache Memcached** | Simple cache, multi-threaded | No persistence, no HA | Need data persistence |
| **DAX** | DynamoDB cache, microsecond | **DynamoDB only, 1 MB max** | Cache other databases |
| **Redshift** | Petabyte data warehouse, OLAP | **Not for OLTP, batch loading** | Real-time transactional |
| **DocumentDB** | MongoDB compatible, managed | MongoDB API only | SQL, key-value |
| **Neptune** | Graph database, relationships | **Graph queries only, niche** | Relational, document |
| **Timestream** | Time-series data, IoT | **Time-series only** | General purpose DB |
| **QLDB** | Immutable ledger, cryptographic | **Append-only, no delete** | Need data deletion |
| **Keyspaces** | Cassandra compatible | Cassandra API only | SQL, key-value |
| **MemoryDB for Redis** | Redis + durability | **Higher cost than ElastiCache** | Cache only (no durability) |
| **RDS Proxy** | Connection pooling, Lambda → DB | Additional component | Direct connections OK |

### 🌐 NETWORKING (25 Services)

| Service | Use When | Key Constraint | Don't Use When |
|---------|----------|----------------|----------------|
| **VPC** | Private network isolation | **Planning required, CIDR limits** | Serverless-only apps |
| **Security Groups** | Instance firewall, stateful | **Allow only, no deny rules** | Need explicit deny |
| **NACLs** | Subnet firewall, deny rules | **Stateless = both directions** | Instance-level control |
| **Route 53** | DNS, domain management, routing | **TTL delays, propagation time** | Not for DNS |
| **CloudFront** | CDN, global cache, HTTP | **Cache = stale data possible** | Dynamic uncached content |
| **Global Accelerator** | Global routing, Layer 4, no cache | **No caching, static IPs only** | Need content caching |
| **ALB** | HTTP/HTTPS, Layer 7, path routing | **HTTP only, 60 sec idle** | TCP/UDP, static IP |
| **NLB** | TCP/UDP, Layer 4, millions RPS | **No Layer 7 features** | Need path routing |
| **GWLB** | Security appliance LB | **Specific use case: 3rd party FW** | Standard load balancing |
| **API Gateway** | REST/WebSocket APIs | **29 sec timeout, 10 MB payload** | Long-running, large files |
| **VPC Peering** | Connect 2 VPCs privately | **No transitive routing** | Hub-and-spoke (use TGW) |
| **Transit Gateway** | Hub for many VPCs/VPNs | Additional cost, complexity | Only 2-3 VPCs |
| **VPC Endpoints** | Private AWS service access | **Regional, per-service** | Public internet OK |
| **PrivateLink** | Private service exposure | Configuration complexity | Public exposure OK |
| **Direct Connect** | Dedicated 1-10 Gbps to AWS | **Weeks setup, high cost** | VPN sufficient |
| **Site-to-Site VPN** | Encrypted on-prem to AWS | **1.25 Gbps max, internet-based** | Need > 1.25 Gbps |
| **Client VPN** | Remote user access to AWS | Per-user licensing | Site-to-site connection |
| **VPN CloudHub** | Multiple sites to AWS | Basic hub-spoke only | Complex routing |
| **Elastic IP** | Static public IP | **Charged if not attached** | Dynamic IP OK |
| **NAT Gateway** | Private subnet internet access | **Per-AZ, hourly + data cost** | Public subnet |
| **NAT Instance** | Budget NAT (manual) | **Manual management, single point** | Production (use NAT GW) |
| **Internet Gateway** | VPC internet access | One per VPC | Private-only VPC |
| **Egress-Only IGW** | IPv6 outbound only | IPv6 only | IPv4 workloads |
| **AWS PrivateLink** | Share services privately | Additional cost | Public sharing OK |
| **VPC Flow Logs** | Network traffic logging | Storage cost, not real-time | Real-time analysis |

### 🔐 SECURITY (30 Services)

| Service | Use When | Key Constraint | Don't Use When |
|---------|----------|----------------|----------------|
| **IAM** | User/role access control | **Global service, eventual consistency** | Not authentication (use Cognito) |
| **IAM Roles** | Temporary credentials, EC2/Lambda | Must attach to resource | Long-term user access |
| **Organizations** | Multi-account management | **Can't convert standalone → Org** | Single account |
| **SCPs** | Permission boundaries across accounts | Deny only, no allow | Single account |
| **IAM Identity Center** | SSO across AWS accounts | **Requires Organizations** | Single account, federated only |
| **Cognito User Pools** | User authentication for apps | **App users, not AWS resources** | AWS access control |
| **Cognito Identity Pools** | AWS credentials for app users | Temporary only | Long-term access |
| **Directory Service** | Managed Active Directory | **Windows-focused, complex** | Simple auth (use Cognito) |
| **KMS** | Encryption key management | **4 KB data max, API calls** | Encrypt large data directly |
| **CloudHSM** | Dedicated hardware, FIPS 140-2 L3 | **Expensive, complex, manual** | KMS sufficient |
| **Secrets Manager** | Store + auto-rotate secrets | **Cost per secret** | Static values (use Param Store) |
| **Parameter Store** | Store config/secrets, free tier | **4 KB standard, 8 KB advanced** | Complex rotation |
| **ACM** | Free SSL/TLS certificates | **AWS services only, auto-renewal** | Non-AWS servers |
| **WAF** | Web application firewall, L7 | **HTTP/HTTPS only, rules cost** | L4 protection |
| **Shield Standard** | DDoS protection (free) | Basic protection only | Advanced DDoS (use Shield Adv) |
| **Shield Advanced** | Advanced DDoS + 24/7 DRT | **$3K/month minimum** | Basic DDoS OK |
| **Network Firewall** | VPC stateful firewall + IDS/IPS | Additional cost | Security Groups sufficient |
| **Firewall Manager** | Central firewall management | **Requires Organizations** | Single account |
| **GuardDuty** | AI threat detection, ML | **Cost per million events** | Budget-constrained |
| **Inspector** | Vulnerability scanning (EC2/ECR) | Scanning cost | No automated scanning needed |
| **Macie** | Find PII in S3 | **S3 only, cost per GB** | Not PII discovery |
| **Detective** | Investigate security issues | Requires GuardDuty | No investigations needed |
| **Security Hub** | Central security dashboard | **Aggregation only, no prevention** | Single service view |
| **Config** | Track configuration changes | Per-rule cost | No compliance tracking |
| **CloudTrail** | API call audit logs | **90 day free, storage cost** | Not for app logs |
| **Resource Access Manager** | Share resources cross-account | **Specific shareable resources** | Public sharing |
| **Trusted Advisor** | Best practice checks | **Full checks = Business/Enterprise** | Not using Support plan |
| **Artifact** | Compliance reports download | Reporting only | Active compliance mgmt |
| **Certificate Manager** | SSL/TLS cert management | See ACM | - |
| **Single Sign-On (SSO)** | Federated access | See IAM Identity Center | - |

### 📊 MONITORING & MANAGEMENT (15 Services)

| Service | Use When | Key Constraint | Don't Use When |
|---------|----------|----------------|----------------|
| **CloudWatch Metrics** | Monitor AWS resources | **1 min default, 5 min free tier** | Real-time < 1 sec |
| **CloudWatch Logs** | Centralize logs | Storage + ingestion cost | Not collecting logs |
| **CloudWatch Alarms** | Alert on metric thresholds | Per-alarm cost | No alerting needed |
| **CloudWatch Events** | (Old) Event routing | **Deprecated → use EventBridge** | New implementations |
| **EventBridge** | Event-driven routing | Slight delay | Synchronous processing |
| **CloudFormation** | IaC, stack management | **Learning curve, YAML/JSON** | Simple manual setup |
| **CDK** | IaC with programming languages | Generates CloudFormation | Prefer YAML/JSON |
| **Systems Manager** | Fleet management, patching | **Requires agent, permissions** | Single instance |
| **OpsWorks** | Chef/Puppet automation | **Legacy, complex** | Use Systems Manager |
| **Elastic Beanstalk** | (See Compute) | - | - |
| **Service Catalog** | Approved product catalog | Requires setup | No governance needs |
| **Control Tower** | Multi-account governance | **Requires Organizations** | Single account |
| **License Manager** | Track software licenses | License tracking only | No licensed software |
| **Health Dashboard** | AWS service health | Info only, no control | - |
| **Personal Health Dashboard** | Your resource health | Info only | - |

### 🔗 INTEGRATION & MESSAGING (10 Services)

| Service | Use When | Key Constraint | Don't Use When |
|---------|----------|----------------|----------------|
| **SQS Standard** | Decouple, queue, at-least-once | **No ordering guarantee, duplicates** | Need FIFO |
| **SQS FIFO** | Decouple + strict ordering | **300 TPS (3000 batch), one region** | High throughput |
| **SNS** | Pub/Sub, fan-out, push | **Push only, 256 KB message** | Need pull/polling |
| **EventBridge** | Event routing, filtering | Slight delay | Real-time < 1 sec |
| **Step Functions** | Workflow orchestration | **25K events/month free then cost** | Simple sequences |
| **SWF** | Long workflows, external signals | **Legacy, complex** | Use Step Functions |
| **AppSync** | Managed GraphQL | GraphQL only | REST API |
| **Amazon MQ** | Managed ActiveMQ/RabbitMQ | **Migration only, not cloud-native** | Greenfield (use SNS/SQS) |
| **Kinesis Data Streams** | Real-time streaming, ordering | **Manual shard management, 1 MB/sec** | Batch processing |
| **Amazon MQ** | Existing JMS, AMQP apps | Higher cost than SQS/SNS | Cloud-native apps |

### 📈 ANALYTICS & BIG DATA (15 Services)

| Service | Use When | Key Constraint | Don't Use When |
|---------|----------|----------------|----------------|
| **Kinesis Data Streams** | Real-time data streaming, replay | **1 MB/sec/shard, manual scaling** | Batch, simple queue |
| **Kinesis Firehose** | Load streams to S3/Redshift/etc | **Near real-time (60 sec min)** | Real-time < 1 sec |
| **Kinesis Analytics** | SQL on streaming data | SQL only | Complex processing |
| **Kinesis Video Streams** | Video streaming, playback | Video only | Non-video streams |
| **MSK** | Managed Kafka, open-source | Kafka expertise required | No Kafka experience |
| **Athena** | SQL query on S3 (serverless) | **Query cost per TB scanned** | Frequent complex queries (use Redshift) |
| **EMR** | Managed Hadoop, Spark, big data | **Cluster management, complex** | Simple ETL (use Glue) |
| **Glue** | Serverless ETL, data catalog | **Python/Scala only** | Real-time processing |
| **Glue DataBrew** | Visual data prep, no-code | Limited transformations | Complex ETL |
| **QuickSight** | BI dashboards, visualizations | **Per-user cost** | No visualization needed |
| **Data Pipeline** | Data workflow orchestration | **Legacy → use Glue** | New implementations |
| **OpenSearch** | Search, logs, analytics (Elasticsearch) | **Complex, cost per node** | Simple search |
| **Lake Formation** | Data lake setup, governance | Additional complexity | Simple S3 storage |
| **Redshift Spectrum** | Query S3 from Redshift | Requires Redshift cluster | No Redshift (use Athena) |
| **Data Exchange** | Subscribe to 3rd party data | Specific use case | No 3rd party data |

### 🚚 MIGRATION & TRANSFER (10 Services)

| Service | Use When | Key Constraint | Don't Use When |
|---------|----------|----------------|----------------|
| **MGN (App Migration)** | Lift-and-shift servers to AWS | **Replication cost, licensing** | Refactor/rearchitect |
| **DMS** | Database migration (homo/hetero) | **Source DB performance impact** | Offline migration OK |
| **SCT** | Schema conversion | Assessment only | Same DB engine |
| **DataSync** | On-prem to AWS data sync | **Agent required, bandwidth** | One-time (use Snow) |
| **Transfer Family** | SFTP/FTPS/FTP to S3 | **Hourly + data cost** | Native S3 access |
| **Snowcone** | 8 TB data + edge compute | **8 TB limit** | > 8 TB (use Snowball) |
| **Snowball Edge** | 80 TB data transfer | **80 TB limit** | > 80 TB (use Snowmobile) |
| **Snowmobile** | Exabyte (100 PB) data transfer | **Months, security, logistics** | < 10 PB |
| **Application Discovery** | Discover on-prem resources | Discovery only | Already documented |
| **Migration Hub** | Track migrations | Tracking only | No migration tracking |

### 🚀 APPLICATION SERVICES (20 Services)

| Service | Use When | Key Constraint | Don't Use When |
|---------|----------|----------------|----------------|
| **SES** | Send transactional emails | **Email only, verification needed** | Marketing (use Pinpoint) |
| **Pinpoint** | Marketing campaigns, SMS, push | Higher cost | Simple transactional |
| **SageMaker** | Build/train/deploy ML models | **ML expertise, cost** | Pre-built AI (use Rekognition) |
| **Rekognition** | Image/video analysis (pre-built) | **Pre-built only, API limits** | Custom models |
| **Polly** | Text-to-speech | **Cost per character** | No TTS needed |
| **Transcribe** | Speech-to-text | **Cost per minute** | No STT needed |
| **Translate** | Language translation | **Cost per character** | No translation needed |
| **Comprehend** | NLP, sentiment, entities | Pre-built only | Custom NLP models |
| **Lex** | Build chatbots (Alexa tech) | **Bot complexity, cost** | Simple Q&A |
| **Kendra** | Intelligent search (ML-powered) | **Expensive, complex** | Simple search |
| **Textract** | Extract text from documents | PDF/image only | Plain text |
| **Forecast** | Time-series ML forecasting | Time-series only | Other ML tasks |
| **Personalize** | ML-powered recommendations | Training time + cost | Rule-based OK |
| **Fraud Detector** | ML fraud detection | Specific use case | No fraud detection |
| **DeviceFarm** | Test mobile apps on real devices | Mobile only | Web testing |
| **IoT Core** | Connect IoT devices | **IoT protocols, MQTT** | Standard HTTP APIs |
| **IoT Greengrass** | IoT edge computing | Edge devices only | Cloud processing |
| **MediaConvert** | Video transcoding | Video only | No video processing |
| **Elastic Transcoder** | (Legacy) Video transcoding | **Use MediaConvert** | New implementations |
| **WorkSpaces** | Virtual desktops (VDI) | **Per-user cost, Windows/Linux** | No VDI needed |

---

## � SCENARIO-BASED INSTANT SERVICE SELECTION

### 💻 Application Architecture Scenarios

| Scenario | Instant Answer | Why |
|----------|----------------|-----|
| Company needs to host a **static website** | **S3 + CloudFront** | Static content, CDN for global delivery |
| Need to run **Docker containers** without managing servers | **Fargate** | Serverless containers, no EC2 management |
| Application needs to process **images uploaded to S3** | **Lambda + S3 Event** | Event-driven, serverless, < 15 min |
| **Microservices** need to communicate asynchronously | **SQS + SNS** | Decouple services, reliable messaging |
| Web app needs **user authentication** (login/signup) | **Cognito User Pools** | User directory for apps, not IAM |
| API needs to handle **millions of requests** with caching | **API Gateway + CloudFront** | API management + edge caching |
| Need to run **batch jobs** that take hours | **AWS Batch** | Auto-scaling batch processing |
| Application has **unpredictable traffic** patterns | **Lambda + DynamoDB + Aurora Serverless** | All serverless, auto-scaling |
| Need **real-time** data processing from IoT devices | **Kinesis Data Streams + Lambda** | Real-time streaming + processing |
| Long-running **workflow** with multiple steps | **Step Functions** | Orchestrate Lambda/services |

### 💾 Data & Storage Scenarios

| Scenario | Instant Answer | Why |
|----------|----------------|-----|
| Store **petabytes** of log files, rarely accessed | **S3 Glacier Deep Archive** | Cheapest, 12 hr retrieval OK |
| **Shared file system** for Linux EC2 instances | **EFS** | NFS, multiple EC2 attach |
| **Shared file system** for Windows servers | **FSx for Windows** | SMB, Active Directory integration |
| Database for **shopping cart** (fast reads/writes) | **DynamoDB** | NoSQL, serverless, low latency |
| **Relational database** with highest performance | **Aurora** | 5x MySQL, 3x PostgreSQL |
| Store **data for 7 years** for compliance | **S3 Glacier Deep Archive** | 7+ years = Deep Archive |
| **Cache** frequently accessed database queries | **ElastiCache Redis** | In-memory, sub-millisecond |
| Cache **DynamoDB** queries specifically | **DAX** | DynamoDB-specific cache |
| Store **backup files** for 30-90 days | **S3 Standard-IA** | Infrequent access, instant retrieval |
| **Data warehouse** for business analytics | **Redshift** | Petabyte-scale, OLAP |
| Run **SQL queries** on S3 data | **Athena** | Serverless SQL on S3 |
| **Video/image** storage with CDN delivery | **S3 + CloudFront** | Object storage + global CDN |

### 🔒 Security Scenarios

| Scenario | Instant Answer | Why |
|----------|----------------|-----|
| Need to **block specific IP addresses** | **NACL** | Subnet-level, supports deny rules |
| Encrypt data with **customer-managed keys** | **KMS Customer Managed Key** | Full control over key rotation |
| Store **database passwords** with auto-rotation | **Secrets Manager** | Auto-rotation built-in |
| **Detect threats** using AI/ML | **GuardDuty** | AI-powered threat detection |
| Find **credit card numbers** in S3 buckets | **Macie** | PII/sensitive data discovery |
| **DDoS protection** for website | **Shield Standard + CloudFront** | Free DDoS + CDN |
| Need **web application firewall** rules | **WAF** | L7 firewall, custom rules |
| **Audit all API calls** across AWS account | **CloudTrail** | API logging, compliance |
| EC2 needs to access S3 **without credentials** | **IAM Role** | Temporary credentials |
| **Multi-account** permission management | **SCPs (Service Control Policies)** | Org-wide permission boundaries |
| Users need **MFA** to access sensitive resources | **IAM Policy with MFA condition** | Enforce MFA in policy |
| Track **configuration changes** for compliance | **AWS Config** | Configuration tracking + rules |

### 🌐 Networking & Connectivity Scenarios

| Scenario | Instant Answer | Why |
|----------|----------------|-----|
| **Private subnet** needs internet access | **NAT Gateway** | Outbound only, managed |
| **On-premises** needs secure connection to AWS | **Site-to-Site VPN** | Encrypted, quick setup |
| Need **dedicated 10 Gbps** connection to AWS | **Direct Connect** | Private, high bandwidth |
| **Distribute traffic** across EC2 instances (HTTP) | **ALB** | Layer 7, HTTP/HTTPS |
| Distribute traffic for **gaming app** (UDP) | **NLB** | Layer 4, TCP/UDP, low latency |
| **Global** users need low latency | **CloudFront + Global Accelerator** | CloudFront for HTTP, GA for TCP/UDP |
| Connect **multiple VPCs** in hub-and-spoke | **Transit Gateway** | Central hub for VPC connectivity |
| Access S3 **without internet** gateway | **VPC Endpoint (Gateway)** | Private S3 access |
| Access **RDS** from Lambda in VPC | **VPC + Security Groups** | Lambda in VPC, SG rules |
| **Route traffic** based on geolocation | **Route 53 Geolocation Routing** | DNS-based geo routing |
| Need **static IP** for NLB | **NLB (built-in)** | NLB provides static IPs |
| **Failover** to secondary region | **Route 53 Failover Routing** | DNS-based failover |

### 🔄 High Availability & DR Scenarios

| Scenario | Instant Answer | Why |
|----------|----------------|-----|
| **RDS** needs automatic failover | **Multi-AZ** | Sync replication, auto-failover |
| Need **DR** in another region | **Multi-Region + S3 Cross-Region Replication** | Geographic redundancy |
| **Scale** EC2 based on CPU usage | **Auto Scaling Group** | Automatic capacity adjustment |
| Ensure **zero downtime** deployments | **Blue/Green with ELB** | Traffic switch after validation |
| **Read-heavy** database workload | **Read Replicas** | Offload reads from primary |
| Application needs **99.99% availability** | **Multi-AZ + Multi-region + Auto Scaling** | Eliminate single points of failure |
| **Backup** across all services centrally | **AWS Backup** | Centralized backup management |
| **RPO = 1 hour, RTO = 1 hour** | **Pilot Light** | Minimal always-on, scale on DR |
| **RPO = minutes, RTO = minutes** | **Warm Standby** | Scaled-down production running |
| **RPO = real-time, RTO = none** | **Multi-Site Active-Active** | Full production in multiple regions |

### 💰 Cost Optimization Scenarios

| Scenario | Instant Answer | Why |
|----------|----------------|-----|
| **Predictable** workload for 1-3 years | **Reserved Instances** | Up to 75% discount |
| **Flexible** workload, can handle interruptions | **Spot Instances** | Up to 90% discount |
| **Unknown** data access patterns | **S3 Intelligent-Tiering** | Auto-moves between tiers |
| Only need EC2 **during business hours** | **Auto Scaling (scheduled)** | Start/stop automatically |
| Lambda running **constantly** | **Migrate to Fargate/EC2** | Lambda expensive for 24/7 |
| S3 data **not accessed for 90+ days** | **Lifecycle Policy → Glacier** | Automatic archiving |
| Want to **analyze** spending patterns | **Cost Explorer** | Visualize and analyze costs |
| Set **budget alerts** | **AWS Budgets** | Alert when exceeding thresholds |
| Need **cost recommendations** | **Compute Optimizer** | ML-powered recommendations |
| **Dev/Test** environments running 24/7 | **Stop/Start on schedule** | Only run when needed |

### 📊 Analytics & Migration Scenarios

| Scenario | Instant Answer | Why |
|----------|----------------|-----|
| **Migrate** on-premises servers to AWS | **MGN (Application Migration Service)** | Lift-and-shift, automated |
| Migrate **Oracle to PostgreSQL** | **DMS + SCT** | Schema conversion + data migration |
| **Real-time** clickstream analysis | **Kinesis Data Streams + Analytics** | Real-time streaming analytics |
| Process **TB of data** overnight (batch) | **EMR or AWS Batch** | Large-scale batch processing |
| **ETL** jobs for data warehouse | **Glue** | Serverless ETL |
| Transfer **100 TB** from on-premises | **Snowball Edge** | Physical device, 80-100 TB |
| **Visualize** business data with dashboards | **QuickSight** | BI tool, ML insights |
| **Search** logs across multiple sources | **OpenSearch** | Elasticsearch, log analytics |
| Run **Hadoop** workloads in cloud | **EMR** | Managed Hadoop/Spark |
| **Transform** data before loading to S3 | **Glue or Lambda** | Serverless transformation |

---

## ⚡ INSTANT DECISION RULES (2-Second Answers)

### IF-THEN Rules (Use First Match)

```
IF "serverless" + < 15 min → THEN Lambda
IF "serverless" + > 15 min → THEN Fargate
IF "cache" + DynamoDB → THEN DAX
IF "cache" + any database → THEN ElastiCache Redis
IF "cache" + web content → THEN CloudFront
IF "NoSQL" → THEN DynamoDB
IF "relational" + "5x" → THEN Aurora
IF "relational" + standard → THEN RDS
IF "warehouse" → THEN Redshift
IF "queue" → THEN SQS
IF "pub/sub" or "fan-out" → THEN SNS
IF "block IP" → THEN NACL
IF "encrypt" → THEN KMS
IF "rotate secrets" → THEN Secrets Manager
IF "shared Linux" → THEN EFS
IF "shared Windows" → THEN FSx for Windows
IF "HTTP load balancer" → THEN ALB
IF "TCP/UDP load balancer" → THEN NLB
IF "CDN" or "global delivery" → THEN CloudFront
IF "static website" → THEN S3 + CloudFront
IF "Docker" + "no servers" → THEN Fargate
IF "Kubernetes" → THEN EKS
IF "archive" + "7+ years" → THEN Glacier Deep Archive
IF "archive" + "instant" → THEN Glacier Instant Retrieval
IF "infrequent" + "instant" → THEN S3 Standard-IA
IF "real-time stream" → THEN Kinesis Data Streams
IF "SQL on S3" → THEN Athena
IF "ETL" → THEN Glue
IF "migrate server" → THEN MGN
IF "migrate database" → THEN DMS
IF "on-prem to AWS sync" → THEN DataSync
IF "100+ TB transfer" → THEN Snowball/Snowmobile
IF "DR" + "cross-region" → THEN Multi-Region
IF "HA" + "same region" → THEN Multi-AZ
IF "read scaling" → THEN Read Replicas
IF "workflow" → THEN Step Functions
IF "event routing" → THEN EventBridge
IF "API" → THEN API Gateway
IF "GraphQL" → THEN AppSync
IF "threat detection" → THEN GuardDuty
IF "PII detection" → THEN Macie
IF "API audit" → THEN CloudTrail
IF "config tracking" → THEN Config
IF "DDoS" → THEN Shield
IF "web firewall" → THEN WAF
IF "VPN" → THEN Site-to-Site VPN
IF "dedicated connection" → THEN Direct Connect
IF "private AWS service" → THEN VPC Endpoint
IF "DNS" → THEN Route 53
IF "user auth" → THEN Cognito
IF "AWS access" → THEN IAM
```

### Keyword Triggers (Auto-Select Service)

| Keyword in Question | Service | Confidence |
|---------------------|---------|------------|
| **Serverless** | Lambda/DynamoDB/Fargate/Aurora Serverless | 95% |
| **5x faster** or **5x performance** | Aurora | 99% |
| **Cache** | CloudFront/ElastiCache/DAX | 90% |
| **Queue** or **decouple** | SQS | 95% |
| **Fan-out** or **pub/sub** | SNS | 95% |
| **Block IP** or **deny rule** | NACL | 99% |
| **NoSQL** | DynamoDB | 90% |
| **Warehouse** or **petabyte** | Redshift | 95% |
| **Real-time stream** | Kinesis | 90% |
| **SQL on S3** | Athena | 99% |
| **ETL** | Glue | 85% |
| **Shared file** + **Linux** | EFS | 95% |
| **Shared file** + **Windows** | FSx for Windows | 99% |
| **7 years** or **compliance archive** | Glacier Deep Archive | 95% |
| **Threat detection** | GuardDuty | 90% |
| **PII** or **credit card** | Macie | 99% |
| **Rotate secrets** | Secrets Manager | 95% |
| **Web firewall** or **SQL injection** | WAF | 95% |
| **DDoS** | Shield | 90% |
| **Migrate servers** | MGN | 90% |
| **Migrate database** | DMS | 90% |
| **100 TB transfer** | Snowball | 95% |
| **GraphQL** | AppSync | 99% |
| **Kubernetes** | EKS | 99% |
| **Docker** + **serverless** | Fargate | 95% |
| **Batch jobs** | AWS Batch | 90% |
| **Workflow** or **orchestrate** | Step Functions | 90% |
| **User authentication** (app) | Cognito | 90% |
| **API audit** | CloudTrail | 95% |
| **Config changes** | Config | 95% |

---

## 🏆 DOMAIN QUICK WINS (Guaranteed Points)

### 🔐 Security Domain (30% - ~20 questions)

**Easy Points (Memorize These):**

1. **Block IP address** → Always NACL (not Security Group)
2. **Encrypt at rest** → Enable encryption + KMS
3. **Rotate secrets** → Secrets Manager (not Parameter Store)
4. **Find PII/sensitive data** → Macie
5. **Threat detection/ML** → GuardDuty
6. **API audit logs** → CloudTrail
7. **Web firewall** → WAF
8. **DDoS protection** → Shield
9. **EC2 access to S3** → IAM Role (never credentials)
10. **User auth for apps** → Cognito (not IAM)
11. **Least privilege** → Minimal permissions
12. **MFA** → Always enable for sensitive ops
13. **Track config changes** → Config
14. **Multi-account permissions** → SCPs
15. **Free SSL certificates** → ACM

**Pattern Recognition:**
- ❌ Security Group **CANNOT** deny → Use NACL
- ✅ Encrypt → KMS (almost always)
- ✅ Private subnet internet → NAT Gateway
- ✅ Credentials in code → **WRONG**, use IAM Role/Secrets Manager

**Instant Answers:**
```
Security Group = Stateful + allow only + instance
NACL = Stateless + allow/deny + subnet
Encrypt = KMS
Secrets = Secrets Manager
Audit = CloudTrail
Detect = GuardDuty
PII = Macie
WAF = Layer 7 web attacks
Shield = DDoS
```

### 🏗️ Resilient Architecture (26% - ~17 questions)

**Easy Points (Memorize These):**

1. **High Availability** → Multi-AZ + Auto Scaling + ELB
2. **Disaster Recovery** → Multi-Region + backups
3. **Read scaling** → Read Replicas (not Multi-AZ)
4. **Automatic failover** → Multi-AZ RDS
5. **Decouple services** → SQS
6. **Fan-out messages** → SNS
7. **Zero downtime deploy** → Blue/Green
8. **Health checks** → ELB Health Checks
9. **Auto-scaling trigger** → CloudWatch Alarms
10. **Backup all services** → AWS Backup
11. **RPO minutes/RTO minutes** → Warm Standby
12. **RPO real-time/RTO none** → Multi-Site Active-Active
13. **Handle traffic spikes** → Auto Scaling + ELB
14. **Survive AZ failure** → Multi-AZ everything
15. **Message ordering** → SQS FIFO

**DR Cheat Code:**
```
Backup/Restore: Cheapest, Hours recovery
Pilot Light: Minimal running, 10-30 min
Warm Standby: Scaled-down running, Minutes
Multi-Site: Full duplicate, Real-time
```

**Instant Formulas:**
- HA = Multi-AZ + Auto Scaling + Load Balancer
- DR = Multi-Region + Cross-Region Replication + Backups
- Decouple = SQS (queue) or SNS (pub/sub)
- Scale = Auto Scaling + CloudWatch Alarms

### ⚡ High-Performing (24% - ~16 questions)

**Easy Points (Memorize These):**

1. **Cache web content** → CloudFront
2. **Cache database** → ElastiCache Redis
3. **Cache DynamoDB** → DAX
4. **Read-heavy DB** → Read Replicas
5. **Low latency storage** → Instance Store (ephemeral)
6. **High IOPS** → io2 Block Express EBS
7. **Global low latency** → CloudFront + Global Accelerator
8. **Millions of IOPS** → Instance Store
9. **Faster RDS** → Aurora (5x)
10. **Fast queries** → Caching + indexing
11. **Right instance type** → Match workload (compute/memory/storage)
12. **Enhanced networking** → Placement Groups
13. **HPC** → Placement Groups (cluster)
14. **Reduce latency** → Cache closer to users
15. **Parallel processing** → FSx for Lustre + EMR

**Caching Strategy:**
```
User → CloudFront (edge cache)
     → ALB/API Gateway (optional cache)
     → App Layer
     → ElastiCache/DAX (DB cache)
     → Database
```

**Performance Hierarchy:**
```
Fastest → Instance Store (ephemeral)
       → io2 Block Express (SSD)
       → io2/io1 (SSD)
       → gp3 (SSD)
       → st1 (HDD)
Slowest → sc1 (HDD)
```

### 💰 Cost-Optimized (20% - ~13 questions)

**Easy Points (Memorize These):**

1. **Predictable workload** → Reserved Instances (1-3 yr)
2. **Flexible/interruptible** → Spot Instances
3. **Unknown access pattern** → S3 Intelligent-Tiering
4. **90+ days no access** → S3 Lifecycle → Glacier
5. **7+ years retention** → Glacier Deep Archive
6. **Dev/Test 24/7** → Stop/start on schedule
7. **Lambda 24/7** → Consider Fargate/EC2 instead
8. **Right-sizing** → Compute Optimizer
9. **Monitor costs** → Cost Explorer
10. **Budget alerts** → AWS Budgets
11. **Data transfer costs** → Use VPC Endpoints, CloudFront
12. **NAT Gateway costs** → Consider NAT Instance (non-prod)
13. **Idle resources** → Auto Scaling, scheduled stop/start
14. **Storage costs** → Lifecycle policies
15. **Serverless = cheaper** → Lambda, Fargate, Aurora Serverless

**Cost Formula:**
```
Steady 24/7 = Reserved (up to 75% off)
Flexible/interruptible = Spot (up to 90% off)
Variable/unpredictable = On-Demand or Serverless
Short-lived tasks = Lambda
Storage = Lifecycle to cheaper tiers
```

**Instant Cost Decisions:**
- Need it 24/7 for 1 year → Reserved
- Can handle interruption → Spot
- Unpredictable usage → Serverless/On-Demand
- Old data → Lifecycle to Glacier
- Dev/Test → Auto stop/start

---

## �🎯 CRITICAL COMPARISONS

### Multi-AZ vs Multi-Region vs Read Replica
- **Multi-AZ** = HA (failover), same region, sync, automatic
- **Multi-Region** = DR, different regions, async, manual
- **Read Replica** = Read scaling, async, manual promote

### Security Group vs NACL
- **Security Group** = Stateful, instance, allow only
- **NACL** = Stateless, subnet, allow + deny

### ALB vs NLB
- **ALB** = HTTP/HTTPS, Layer 7, path routing
- **NLB** = TCP/UDP, Layer 4, high perf, static IP

### CloudFront vs Global Accelerator
- **CloudFront** = HTTP, caching, Layer 7
- **Global Accelerator** = TCP/UDP, no cache, Layer 4

### S3 vs EBS vs EFS vs Instance Store
- **S3** = Object, unlimited, web access
- **EBS** = Block, single EC2, persistent
- **EFS** = File (NFS), shared, Linux
- **Instance Store** = Block, temp, highest perf

---

## 🔥 DECISION RULES (Use First Match)

### Storage Class
```
Unknown pattern → Intelligent-Tiering
Frequent → Standard
Infrequent + instant → Standard-IA
Infrequent + single AZ → One Zone-IA
Archive + instant → Glacier Instant
Archive + hours → Glacier Flexible
7+ years → Glacier Deep Archive
```

### Database
```
Relational + 5x → Aurora
Relational + standard → RDS
NoSQL + serverless → DynamoDB
MongoDB → DocumentDB
Graph → Neptune
Warehouse → Redshift
Cache DynamoDB → DAX
Cache database → ElastiCache Redis
```

### Compute
```
< 15 min + event → Lambda
> 15 min → EC2/ECS/Fargate
Containers + K8s → EKS
Containers + simple → ECS
Serverless containers → Fargate
Batch → AWS Batch
```

---

## 🚨 EXAM TRAPS - AVOID!

| Trap | Wrong Answer | Correct Answer |
|------|--------------|----------------|
| Block specific IP | Security Group | NACL |
| > 15 min serverless | Lambda | Fargate/EC2 |
| Multi-AZ for DR | Multi-AZ only | Multi-Region |
| Read scaling | Multi-AZ | Read Replica |
| Cache DynamoDB | ElastiCache | DAX |
| Shared Linux FS | EBS | EFS |
| TCP/UDP global | CloudFront | Global Accelerator |
| Cost-effective + steady | Lambda | Reserved Instances |

---

## 📊 CRITICAL LIMITS

| Service | Limit | Impact |
|---------|-------|--------|
| Lambda | 15 min max | Can't use for longer |
| DynamoDB | 400 KB item | Use S3 for large |
| SQS | 14 days retention | Auto-delete after |
| S3 | 5 TB max object | Multipart for larger |
| API Gateway | 29 sec timeout | Long tasks = async |

---

## 🏆 DOMAIN QUICK WINS

### Secure (30%)
- Encryption: KMS + enable on all services
- Least privilege: Minimal IAM permissions
- MFA: Sensitive operations
- Private: Private subnets + NAT Gateway
- Audit: CloudTrail + Config + GuardDuty

### Resilient (26%)
- Multi-AZ: RDS, ALB, NAT Gateway
- Auto Scaling: EC2, DynamoDB
- Decouple: SQS, SNS, EventBridge
- Backup: Automated + cross-region
- Health checks: ELB, Route 53

### Performance (24%)
- Cache: CloudFront + ElastiCache/DAX
- Read replicas: RDS/Aurora
- Right instance: Match workload type
- Placement groups: HPC/cluster
- Enhanced networking: High throughput

### Cost (20%)
- Reserved/Spot: Predictable/flexible
- Lifecycle: S3 → IA → Glacier
- Serverless: Lambda, Fargate, Aurora Serverless
- Right-size: Compute Optimizer
- Monitor: Cost Explorer, Budgets

---

## 🎯 EXAM STRATEGY

### Time Management
- 65 questions / 130 min = 2 min/question
- Flag hard questions, return later
- Don't get stuck

### Approach
1. Read question twice
2. Identify domain & keywords
3. Match to service pattern
4. Eliminate wrong answers
5. Choose most managed/serverless

### When Stuck
- What's the core requirement?
- What domain is it?
- Which keywords are present?
- Choose serverless/managed option

---

## 💡 TOP 10 HEURISTICS

1. **"Serverless"** → Lambda, DynamoDB, Aurora Serverless, Fargate
2. **"Cache"** → CloudFront (web), ElastiCache (DB), DAX (DynamoDB)
3. **"HA"** → Multi-AZ + Auto Scaling + Load Balancer
4. **"DR"** → Multi-Region + Backups
5. **"Cost"** → Right-size + Reserved/Spot + Lifecycle
6. **"Performance"** → Cache + Read Replicas + Right instance
7. **"Security"** → Encrypt + Least privilege + MFA + Private
8. **"Decouple"** → SQS, SNS, EventBridge
9. **"Storage"** → S3 (object), EBS (block), EFS (shared)
10. **"Database"** → Aurora (relational high-perf), DynamoDB (NoSQL), Redshift (warehouse)

---

## ✅ CONFIDENCE CHECK (< 5 sec each)

1. Serverless + event-driven + < 15 min = **Lambda**
2. NoSQL + serverless + key-value = **DynamoDB**
3. Web LB + path routing = **ALB**
4. Object storage + unlimited = **S3**
5. Cache DynamoDB + microsecond = **DAX**
6. Queue + decouple = **SQS**
7. Fan-out + multiple subscribers = **SNS**
8. Relational + 5x performance = **Aurora**
9. Warehouse + petabyte = **Redshift**
10. CDN + global delivery = **CloudFront**

**10/10? You're ready! 🎯**

---

## 🔢 LAST-MINUTE MEMORY DUMP

**Write on scratch paper at exam start:**

```
Lambda: 15 min max
S3: 5 TB max object
SQS: 14 day max
DynamoDB: 400 KB max item
API Gateway: 29 sec timeout

Multi-AZ = HA (same region)
Multi-Region = DR (cross region)
Read Replica = Read scale (async)

SG = Stateful, allow, instance
NACL = Stateless, allow+deny, subnet

ALB = L7 HTTP
NLB = L4 TCP/UDP

CloudFront = L7 + cache
Global Accel = L4 + no cache

S3 Tiers:
Standard → Intelligent → IA → Glacier

DR Tiers:
Backup/Restore: Hours
Pilot Light: Min-Hours
Warm Standby: Minutes
Multi-Site: Real-time
```

---

## 🎓 FINAL MANTRAS

1. **AWS favors:** Managed, serverless, automated, secure
2. **When in doubt:** Choose serverless option
3. **Read carefully:** Keywords matter
4. **Eliminate first:** Often easier than finding right answer

