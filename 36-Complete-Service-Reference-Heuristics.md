# AWS SAA-C03: Complete Service Reference with Exam Heuristics

## 🎯 Definition + Feature + When to Choose (Exam Heuristics)

---

## 🖥️ COMPUTE SERVICES

### EC2 (Elastic Compute Cloud)
| Aspect | Details |
|--------|---------|
| **Definition** | Virtual servers in the cloud with full OS control |
| **Key Feature** | Choose instance type, AMI, pricing model, and placement |
| **Exam Heuristic** | Choose EC2 when: need full control, specific OS, long-running workloads, or custom software installation |

### EC2 Instance Types
| Type | Definition | Feature | Exam Heuristic |
|------|------------|---------|----------------|
| **T-series** | Burstable performance instances | Burst credits for spiky workloads | "Variable workload" or "small apps" → T3/T3a |
| **M-series** | General purpose balanced | Equal compute/memory/network | "General purpose" or "no specific requirement" → M5/M6i |
| **C-series** | Compute optimized | High CPU-to-memory ratio | "CPU-intensive" or "batch processing" → C5/C6i |
| **R-series** | Memory optimized | High memory-to-CPU ratio | "In-memory" or "memory-intensive" → R5/R6i |
| **X-series** | Extreme memory | Largest memory capacity | "SAP HANA" or "extreme memory" → X1/X2 |
| **I-series** | Storage optimized | High sequential read/write | "High I/O" or "NoSQL databases" → I3/I3en |
| **D-series** | Dense storage | High HDD capacity | "Data warehousing" or "distributed file systems" → D2/D3 |
| **P-series** | GPU compute | NVIDIA GPUs for ML training | "Machine learning training" or "GPU" → P3/P4 |
| **G-series** | Graphics intensive | GPU for graphics rendering | "Graphics" or "video encoding" → G4 |
| **Inf-series** | ML inference | AWS Inferentia chips | "ML inference" or "cost-effective inference" → Inf1 |

### EC2 Pricing Models
| Model | Definition | Feature | Exam Heuristic |
|-------|------------|---------|----------------|
| **On-Demand** | Pay by the hour/second, no commitment | Flexibility, no upfront | "Short-term" or "unpredictable" or "testing" → On-Demand |
| **Reserved (Standard)** | 1-3 year commitment, up to 72% off | Cannot change instance family | "Steady-state" + "predictable" + "cost savings" → Reserved |
| **Reserved (Convertible)** | 1-3 year, up to 66% off, can change | Flexibility to change type | "Long-term" but "may need different type" → Convertible RI |
| **Savings Plans** | $/hour commitment, up to 72% off | Applies to EC2, Fargate, Lambda | "Flexible compute commitment" → Savings Plans |
| **Spot** | Spare capacity, up to 90% off | Can be interrupted | "Fault-tolerant" or "flexible" or "batch" → Spot |
| **Dedicated Host** | Physical server for your use | Per-socket licensing | "BYOL" or "compliance" or "licensing" → Dedicated Host |
| **Dedicated Instance** | Hardware isolation | Compliance | "Hardware isolation" but no license needs → Dedicated Instance |

### Lambda
| Aspect | Details |
|--------|---------|
| **Definition** | Serverless compute that runs code in response to events |
| **Key Feature** | Max 15 min execution, 10 GB memory, pay per invocation and duration |
| **Exam Heuristic** | Choose Lambda when: event-driven, < 15 min, no server management needed, variable/unpredictable traffic |
| **Anti-Pattern** | DON'T use for: > 15 min jobs, constant high traffic, need persistent connections |

### Elastic Beanstalk
| Aspect | Details |
|--------|---------|
| **Definition** | PaaS that deploys and manages web applications automatically |
| **Key Feature** | Handles provisioning, load balancing, scaling, monitoring |
| **Exam Heuristic** | Choose when: "quickly deploy web app" or "don't want to manage infrastructure" but still need EC2 access |

### Container Services
| Service | Definition | Feature | Exam Heuristic |
|---------|------------|---------|----------------|
| **ECS** | AWS-native container orchestration | Deep AWS integration, simpler | "Containers" + "AWS native" or "simpler" → ECS |
| **EKS** | Managed Kubernetes | Kubernetes compatibility | "Kubernetes" or "K8s" or "existing K8s" → EKS |
| **Fargate** | Serverless containers | No EC2 management | "Serverless containers" or "don't manage servers" → Fargate |
| **ECR** | Container image registry | Integrated with ECS/EKS | "Store container images" → ECR |

### Other Compute
| Service | Definition | Feature | Exam Heuristic |
|---------|------------|---------|----------------|
| **AWS Batch** | Managed batch computing | Auto-provisions resources | "Batch processing" + "any scale" → Batch |
| **Lightsail** | Simple VPS | Fixed monthly pricing | "Simple" or "beginner" or "WordPress" → Lightsail |
| **Outposts** | AWS on-premises | Consistent hybrid experience | "On-premises" + "same AWS APIs" → Outposts |
| **Wavelength** | 5G edge computing | Ultra-low latency mobile | "5G" or "mobile edge" → Wavelength |
| **Local Zones** | AWS closer to users | Low latency in specific metros | "Low latency" + "specific city" → Local Zones |

---

## 💾 STORAGE SERVICES

### S3 (Simple Storage Service)
| Aspect | Details |
|--------|---------|
| **Definition** | Object storage with unlimited scalability and 11 9s durability |
| **Key Feature** | Storage classes, versioning, lifecycle policies, cross-region replication |
| **Exam Heuristic** | Choose S3 when: objects/files, unlimited storage, web content, backup, data lake |

### S3 Storage Classes
| Class | Definition | Feature | Exam Heuristic |
|-------|------------|---------|----------------|
| **S3 Standard** | Frequently accessed data | 99.99% availability | "Frequently accessed" or "no pattern specified" → Standard |
| **S3 Intelligent-Tiering** | Auto-moves between tiers | Small monitoring fee | "Unknown access pattern" or "changing patterns" → Intelligent-Tiering |
| **S3 Standard-IA** | Infrequent access, instant retrieval | 30-day min, retrieval fee | "Infrequent" but "need immediately" → Standard-IA |
| **S3 One Zone-IA** | Infrequent, single AZ | 20% cheaper than Standard-IA | "Infrequent" + "recreatable" or "secondary backup" → One Zone-IA |
| **S3 Glacier Instant** | Archive with millisecond access | Same latency as Standard | "Archive" but "instant access needed" → Glacier Instant |
| **S3 Glacier Flexible** | Archive, minutes to hours retrieval | Expedited/Standard/Bulk | "Archive" + "hours OK" → Glacier Flexible |
| **S3 Glacier Deep Archive** | Coldest storage | 12-48 hour retrieval | "7+ years" or "compliance archive" or "lowest cost" → Deep Archive |

### S3 Features
| Feature | Definition | Exam Heuristic |
|---------|------------|----------------|
| **Versioning** | Keep multiple versions of objects | "Accidental deletion protection" or "audit trail" → Enable Versioning |
| **MFA Delete** | Require MFA to delete | "Extra deletion protection" → MFA Delete |
| **Object Lock** | WORM (Write Once Read Many) | "Compliance" or "cannot be deleted" → Object Lock |
| **Lifecycle Policy** | Auto-transition/expire objects | "Automate storage class changes" → Lifecycle Policy |
| **CRR** | Cross-Region Replication | "DR" or "compliance in another region" → CRR |
| **SRR** | Same-Region Replication | "Log aggregation" or "same region copy" → SRR |
| **Transfer Acceleration** | Fast uploads using CloudFront | "Global uploads" or "faster upload" → Transfer Acceleration |
| **Presigned URLs** | Temporary access to private objects | "Temporary access" or "download link" → Presigned URL |

### EBS (Elastic Block Store)
| Aspect | Details |
|--------|---------|
| **Definition** | Block storage volumes for EC2 instances |
| **Key Feature** | Persistent, snapshots to S3, encryption, multiple volume types |
| **Exam Heuristic** | Choose EBS when: need block storage, databases on EC2, boot volumes |

### EBS Volume Types
| Type | Definition | Feature | Exam Heuristic |
|------|------------|---------|----------------|
| **gp3** | General purpose SSD | 3000 IOPS baseline, up to 16K | "General purpose" or "boot volume" → gp3 |
| **gp2** | Legacy general purpose SSD | IOPS scales with size | "Bursting IOPS" → gp2 (but prefer gp3) |
| **io2/io2 Block Express** | Highest performance SSD | Up to 256K IOPS, multi-attach | "High IOPS" or "critical database" → io2 |
| **st1** | Throughput optimized HDD | 500 MB/s throughput | "Big data" or "throughput" or "sequential" → st1 |
| **sc1** | Cold HDD | Lowest cost | "Infrequent" + "lowest cost block" → sc1 |

### File Systems
| Service | Definition | Feature | Exam Heuristic |
|---------|------------|---------|----------------|
| **EFS** | Managed NFS for Linux | Multi-AZ, auto-scaling | "Shared Linux" or "NFS" or "multiple EC2" → EFS |
| **FSx for Windows** | Managed Windows file server | SMB, AD integration | "Shared Windows" or "SMB" or "Active Directory" → FSx Windows |
| **FSx for Lustre** | High-performance parallel FS | HPC, ML, integrates with S3 | "HPC" or "high performance" or "ML training data" → FSx Lustre |
| **FSx for NetApp ONTAP** | Managed NetApp | Multi-protocol, enterprise features | "NetApp" or "iSCSI" or "multi-protocol" → FSx ONTAP |
| **FSx for OpenZFS** | Managed OpenZFS | Snapshots, clones | "ZFS" or "Linux file server migration" → FSx OpenZFS |

### Instance Store
| Aspect | Details |
|--------|---------|
| **Definition** | Temporary block storage physically attached to EC2 host |
| **Key Feature** | Highest IOPS/throughput, data lost on stop/terminate |
| **Exam Heuristic** | Choose when: "temporary storage" or "highest performance" + "data can be lost" |

### Storage Gateway
| Type | Definition | Feature | Exam Heuristic |
|------|------------|---------|----------------|
| **File Gateway** | NFS/SMB to S3 | On-prem file access to S3 | "On-prem NFS to S3" → File Gateway |
| **Volume Gateway (Cached)** | iSCSI with S3 backend | Frequently accessed data cached | "iSCSI" + "primary data in cloud" → Cached Volume |
| **Volume Gateway (Stored)** | iSCSI with local storage | Full dataset on-prem, async backup | "iSCSI" + "primary data on-prem" → Stored Volume |
| **Tape Gateway** | Virtual tape library to S3/Glacier | Backup software compatible | "Replace tape" or "VTL" → Tape Gateway |

### Data Transfer
| Service | Definition | Feature | Exam Heuristic |
|---------|------------|---------|----------------|
| **Snowcone** | Small edge device, 8-14 TB | Rugged, battery-powered | "Remote location" or "< 14 TB" + "edge" → Snowcone |
| **Snowball Edge** | Petabyte transfer, 80 TB | Storage or Compute optimized | "Petabytes" or "limited bandwidth" → Snowball Edge |
| **Snowmobile** | Exabyte transfer, 100 PB | Shipping container truck | "Exabytes" or "data center migration" → Snowmobile |
| **DataSync** | Automated data transfer | 10x faster, scheduled | "Ongoing sync" or "NFS/SMB to AWS" → DataSync |
| **Transfer Family** | Managed SFTP/FTPS/FTP | S3 or EFS backend | "SFTP" or "existing file transfer workflows" → Transfer Family |

---

## 🗄️ DATABASE SERVICES

### RDS (Relational Database Service)
| Aspect | Details |
|--------|---------|
| **Definition** | Managed relational database (MySQL, PostgreSQL, MariaDB, Oracle, SQL Server) |
| **Key Feature** | Automated backups, patching, Multi-AZ, Read Replicas |
| **Exam Heuristic** | Choose RDS when: relational data, need managed service, standard SQL workloads |

### Aurora
| Aspect | Details |
|--------|---------|
| **Definition** | AWS-built MySQL/PostgreSQL-compatible with 5x performance |
| **Key Feature** | 6-way replication, auto-scaling storage (128 TB), self-healing |
| **Exam Heuristic** | Choose Aurora when: "highest performance MySQL/PostgreSQL" or "auto-scaling" or "fast failover" |

### Aurora Features
| Feature | Definition | Exam Heuristic |
|---------|------------|----------------|
| **Aurora Serverless v2** | Auto-scales capacity | "Variable workload" + "relational" → Aurora Serverless |
| **Aurora Global Database** | Cross-region < 1 sec replication | "Cross-region DR" + "< 1 min RTO" → Aurora Global |
| **Aurora Replicas** | Up to 15 read replicas | "Scale reads" + "Aurora" → Aurora Replicas |
| **Backtrack** | Rewind database to point in time | "Quick recovery without restore" → Backtrack |

### RDS vs Aurora Decision
| Scenario | Choice | Why |
|----------|--------|-----|
| Standard MySQL workload | RDS MySQL | Simpler, cheaper |
| High-performance MySQL | Aurora MySQL | 5x performance |
| Need Oracle or SQL Server | RDS | Aurora doesn't support |
| Auto-scaling storage | Aurora | Up to 128 TB auto |
| Fast failover (< 30 sec) | Aurora | 10-30 sec vs 60-120 sec |
| Global DR, < 1 sec RPO | Aurora Global | Cross-region replication |

### DynamoDB
| Aspect | Details |
|--------|---------|
| **Definition** | Serverless NoSQL key-value and document database |
| **Key Feature** | Single-digit millisecond latency at any scale, Global Tables |
| **Exam Heuristic** | Choose DynamoDB when: "NoSQL" or "key-value" or "serverless DB" or "any scale" |

### DynamoDB Features
| Feature | Definition | Exam Heuristic |
|---------|------------|----------------|
| **On-Demand Capacity** | Pay per request | "Unpredictable" or "new workload" → On-Demand |
| **Provisioned Capacity** | Set RCU/WCU | "Predictable" or "cost optimization" → Provisioned |
| **Auto Scaling** | Adjust provisioned capacity | "Provisioned" + "varying" → Auto Scaling |
| **Global Tables** | Multi-region active-active | "Multi-region" + "active-active" → Global Tables |
| **DynamoDB Streams** | Capture item changes | "React to changes" or "trigger Lambda" → Streams |
| **TTL** | Auto-delete expired items | "Expire data" or "session data" → TTL |
| **DAX** | In-memory cache | "Microsecond reads" or "cache DynamoDB" → DAX |
| **GSI** | Query by non-key attributes | "Query different attribute" → GSI |
| **LSI** | Alternate sort key | "Different sort key" (create at table creation) → LSI |

### Caching
| Service | Definition | Feature | Exam Heuristic |
|---------|------------|---------|----------------|
| **ElastiCache Redis** | Managed Redis | Persistence, replication, pub/sub | "Cache" + "persistence" or "complex types" → Redis |
| **ElastiCache Memcached** | Managed Memcached | Simple, multi-threaded | "Simple cache" + "multi-threaded" → Memcached |
| **DAX** | DynamoDB cache | Microsecond latency | "Cache DynamoDB specifically" → DAX |

### Other Databases
| Service | Definition | Feature | Exam Heuristic |
|---------|------------|---------|----------------|
| **Redshift** | Petabyte data warehouse | Columnar, OLAP, MPP | "Data warehouse" or "OLAP" or "analytics" → Redshift |
| **DocumentDB** | MongoDB-compatible | Managed document DB | "MongoDB" or "document database" → DocumentDB |
| **Neptune** | Graph database | Social, fraud, knowledge | "Graph" or "relationships" or "social network" → Neptune |
| **Timestream** | Time-series database | IoT, metrics, serverless | "Time-series" or "IoT data" → Timestream |
| **QLDB** | Ledger database | Immutable, verifiable | "Immutable" or "audit trail" or "ledger" → QLDB |
| **Keyspaces** | Cassandra-compatible | Serverless wide-column | "Cassandra" → Keyspaces |

---

## 🌐 NETWORKING SERVICES

### VPC Components
| Component | Definition | Feature | Exam Heuristic |
|-----------|------------|---------|----------------|
| **VPC** | Isolated virtual network | Your own IP space | Foundation for all networking |
| **Subnet** | IP range segment in one AZ | Public or Private | "AZ-level isolation" → Subnets |
| **Internet Gateway** | VPC internet connectivity | Horizontally scaled | "Internet access" → IGW |
| **NAT Gateway** | Private subnet internet access | Managed, per-AZ | "Private instances need internet" → NAT GW |
| **NAT Instance** | EC2-based NAT | Self-managed | "Cost-sensitive NAT" (but prefer NAT GW) |
| **Security Groups** | Stateful instance firewall | Allow rules only | "Instance firewall" → Security Groups |
| **NACLs** | Stateless subnet firewall | Allow + Deny rules | "Block specific IP" or "subnet firewall" → NACL |
| **Route Tables** | Traffic routing rules | Per subnet | Controls traffic flow |
| **Elastic IP** | Static public IPv4 | Remappable | "Static IP needed" → EIP |

### Security Groups vs NACLs
| Aspect | Security Groups | NACLs |
|--------|-----------------|-------|
| **Level** | Instance | Subnet |
| **State** | Stateful | Stateless |
| **Rules** | Allow only | Allow + Deny |
| **Evaluation** | All rules | Numbered order |
| **Exam Heuristic** | Default choice | "Block IP" or "subnet-level" |

### Load Balancers
| Service | Definition | Feature | Exam Heuristic |
|---------|------------|---------|----------------|
| **ALB** | Layer 7 HTTP/HTTPS | Path/host routing, Lambda | "HTTP" or "path routing" or "host routing" → ALB |
| **NLB** | Layer 4 TCP/UDP | Ultra-low latency, static IP | "TCP/UDP" or "static IP" or "millions RPS" → NLB |
| **GWLB** | Layer 3 for appliances | Deploy 3rd-party firewalls | "Firewall appliance" or "IDS/IPS" → GWLB |
| **CLB** | Legacy Layer 4/7 | Deprecated | Never choose for new → Use ALB/NLB |

### Route 53
| Aspect | Details |
|--------|---------|
| **Definition** | Scalable DNS and domain registration service |
| **Key Feature** | Health checks, multiple routing policies, alias records |
| **Exam Heuristic** | Choose Route 53 for: any DNS question, routing policies, health checks |

### Route 53 Routing Policies
| Policy | Definition | Exam Heuristic |
|--------|------------|----------------|
| **Simple** | Single resource | "Basic DNS" → Simple |
| **Weighted** | Distribute by percentage | "A/B testing" or "gradual migration" → Weighted |
| **Latency** | Route to lowest latency | "Lowest latency" or "best performance" → Latency |
| **Failover** | Active-passive | "DR" or "failover" → Failover |
| **Geolocation** | Route by user location | "Country-based" or "content localization" → Geolocation |
| **Geoproximity** | Route by distance + bias | "Route closest" + "shift traffic" → Geoproximity |
| **Multivalue Answer** | Return multiple healthy IPs | "Simple load balancing" + "health checks" → Multivalue |

### Content Delivery & Acceleration
| Service | Definition | Feature | Exam Heuristic |
|---------|------------|---------|----------------|
| **CloudFront** | Global CDN | Caches content at edge | "Cache content" or "static content" → CloudFront |
| **Global Accelerator** | Network acceleration | Static IPs, no caching | "No caching" + "acceleration" or "static IP" → GA |

### CloudFront vs Global Accelerator
| Aspect | CloudFront | Global Accelerator |
|--------|------------|-------------------|
| **Purpose** | Cache & deliver content | Route to optimal endpoint |
| **Caching** | Yes | No |
| **Static IPs** | No (use Lambda@Edge) | Yes (2 anycast) |
| **Exam Heuristic** | "CDN" or "cache" | "Static IP" or "TCP/UDP" or "no cache" |

### Hybrid Connectivity
| Service | Definition | Feature | Exam Heuristic |
|---------|------------|---------|----------------|
| **Site-to-Site VPN** | IPsec tunnel over internet | Quick setup, encrypted | "Quick" or "encrypted over internet" → VPN |
| **Direct Connect** | Dedicated private line | 1/10/100 Gbps, consistent | "Consistent latency" or "high throughput" → DX |
| **Direct Connect Gateway** | Connect DX to multiple VPCs | Multi-region DX | "DX to multiple regions" → DX Gateway |
| **Transit Gateway** | Hub for VPCs and on-prem | Transitive routing | "Connect many VPCs" or "hub-spoke" → TGW |

### VPC Connectivity
| Service | Definition | Feature | Exam Heuristic |
|---------|------------|---------|----------------|
| **VPC Peering** | Connect 2 VPCs | Non-transitive | "Connect 2 VPCs" + "no transit" → Peering |
| **Transit Gateway** | Connect many VPCs | Transitive, scalable | "Many VPCs" or "transitive" → TGW |
| **Gateway Endpoint** | S3/DynamoDB private access | Free, route table | "Private S3 access" → Gateway Endpoint |
| **Interface Endpoint** | Other services private access | ENI, PrivateLink | "Private access to AWS service" → Interface Endpoint |
| **PrivateLink** | Expose service privately | No internet needed | "Expose service to other VPCs" → PrivateLink |

### API Gateway
| Aspect | Details |
|--------|---------|
| **Definition** | Managed service to create, publish, and manage APIs |
| **Key Feature** | REST, HTTP, WebSocket APIs, throttling, caching |
| **Exam Heuristic** | Choose when: "API" + Lambda, "throttling", "managed API" |

### API Gateway Types
| Type | Definition | Exam Heuristic |
|------|------------|----------------|
| **REST API** | Full-featured, regional/edge | "Full features" or "request validation" → REST |
| **HTTP API** | Low-latency, low-cost | "Simple proxy" or "cost-effective" → HTTP |
| **WebSocket API** | Two-way communication | "Real-time" or "chat" → WebSocket |

---

## 🔒 SECURITY SERVICES

### IAM (Identity and Access Management)
| Aspect | Details |
|--------|---------|
| **Definition** | Manage users, groups, roles, and permissions for AWS access |
| **Key Feature** | Free, global, supports federation, least privilege |
| **Exam Heuristic** | IAM is the answer for: any access control, permissions, who can do what |

### IAM Components
| Component | Definition | Exam Heuristic |
|-----------|------------|----------------|
| **Users** | Individual identities | "Individual person" → User |
| **Groups** | Collection of users | "Group of users" + "same permissions" → Group |
| **Roles** | Temporary credentials | "Service access" or "cross-account" or "no permanent keys" → Role |
| **Policies** | Permission definitions | JSON defining allow/deny |
| **Permission Boundary** | Maximum permissions | "Limit role permissions" → Permission Boundary |

### IAM Key Rules
| Rule | Details |
|------|---------|
| **Never use root** | Create IAM users, enable MFA on root |
| **Use roles for services** | EC2, Lambda → IAM Role (never access keys!) |
| **Least privilege** | Only grant needed permissions |
| **Use groups** | Attach policies to groups, not users |

### Authentication Services
| Service | Definition | Feature | Exam Heuristic |
|---------|------------|---------|----------------|
| **Cognito User Pools** | User directory | Sign-up, sign-in, MFA | "App user authentication" → User Pools |
| **Cognito Identity Pools** | AWS credentials for users | Temporary credentials | "App users need AWS access" → Identity Pools |
| **IAM Identity Center** | Centralized SSO | Multi-account access | "SSO" or "multiple AWS accounts" → Identity Center |
| **Directory Service** | Managed Active Directory | AD Connector, Managed AD | "Active Directory" → Directory Service |

### Encryption Services
| Service | Definition | Feature | Exam Heuristic |
|---------|------------|---------|----------------|
| **KMS** | Managed encryption keys | Auto-rotation, CloudTrail audit | "Encryption" or "managed keys" → KMS |
| **CloudHSM** | Dedicated hardware keys | FIPS 140-2 Level 3 | "FIPS 140-2 Level 3" or "dedicated HSM" → CloudHSM |
| **ACM** | Free SSL/TLS certificates | Auto-renewal | "SSL certificate" → ACM |

### KMS Key Types
| Type | Definition | Exam Heuristic |
|------|------------|----------------|
| **AWS Managed** | AWS creates/manages | "Simple" or "default encryption" |
| **Customer Managed** | You create/manage | "Control rotation" or "cross-account" |
| **AWS Owned** | AWS internal | Not visible to you |

### Secrets Management
| Service | Definition | Feature | Exam Heuristic |
|---------|------------|---------|----------------|
| **Secrets Manager** | Store and rotate secrets | Auto-rotation built-in | "Secret rotation" or "database credentials" → Secrets Manager |
| **Parameter Store** | Store config and secrets | Free tier, no rotation | "Config values" or "no rotation needed" → Parameter Store |

### Threat Detection
| Service | Definition | Feature | Exam Heuristic |
|---------|------------|---------|----------------|
| **GuardDuty** | ML threat detection | Analyzes logs automatically | "Threat detection" or "anomaly" → GuardDuty |
| **Inspector** | Vulnerability scanning | EC2, Lambda, ECR | "Vulnerability scan" or "CVE" → Inspector |
| **Macie** | S3 data discovery | Find PII/sensitive data | "PII" or "sensitive data in S3" → Macie |
| **Detective** | Security investigation | Visualize, analyze incidents | "Investigate incident" → Detective |
| **Security Hub** | Central security dashboard | Aggregates findings | "Centralized security view" → Security Hub |

### Network Security
| Service | Definition | Feature | Exam Heuristic |
|---------|------------|---------|----------------|
| **Shield Standard** | Basic DDoS protection | Free, automatic | "DDoS" → Shield (Standard is free) |
| **Shield Advanced** | Enhanced DDoS + support | 24/7 DRT, cost protection | "DDoS" + "support" or "cost protection" → Shield Advanced |
| **WAF** | Web application firewall | SQL injection, XSS, rate limit | "SQLi" or "XSS" or "Layer 7 attack" → WAF |
| **Network Firewall** | VPC traffic filtering | Stateful, IDS/IPS | "VPC firewall" or "IDS/IPS" → Network Firewall |
| **Firewall Manager** | Centralized firewall rules | Multi-account management | "Manage firewalls across accounts" → Firewall Manager |

### Audit & Compliance
| Service | Definition | Feature | Exam Heuristic |
|---------|------------|---------|----------------|
| **CloudTrail** | API call audit log | Who did what when | "Audit API calls" or "who did this" → CloudTrail |
| **Config** | Resource configuration tracking | Compliance rules | "Configuration changes" or "compliance" → Config |
| **Artifact** | Compliance reports | SOC, PCI, ISO docs | "Compliance documents" → Artifact |

### AWS Organizations
| Aspect | Details |
|--------|---------|
| **Definition** | Centrally manage multiple AWS accounts |
| **Key Feature** | SCPs, consolidated billing, organizational units |
| **Exam Heuristic** | Choose when: multi-account, policy enforcement, billing consolidation |

### Organizations Components
| Component | Definition | Exam Heuristic |
|-----------|------------|----------------|
| **SCP** | Permission guardrails | "Restrict all accounts" or "prevent action" → SCP |
| **OU** | Organizational units | "Group accounts" → OU |
| **Consolidated Billing** | Single bill for all accounts | "Volume discounts" or "single bill" |

---

## 📊 MONITORING & MANAGEMENT

### CloudWatch
| Aspect | Details |
|--------|---------|
| **Definition** | Monitoring and observability service for AWS resources |
| **Key Feature** | Metrics, logs, alarms, dashboards, events |
| **Exam Heuristic** | CloudWatch is the answer for: monitoring, metrics, logs, alarms |

### CloudWatch Components
| Component | Definition | Exam Heuristic |
|-----------|------------|----------------|
| **Metrics** | Time-ordered data points | "Monitor resource" → Metrics |
| **Logs** | Log data collection | "Centralize logs" → CloudWatch Logs |
| **Alarms** | Threshold-based alerts | "Alert when threshold" → Alarms |
| **Events/EventBridge** | Event routing | "Trigger on event" → EventBridge |
| **Dashboards** | Visualization | "Visualize metrics" → Dashboards |

### Other Monitoring
| Service | Definition | Feature | Exam Heuristic |
|---------|------------|---------|----------------|
| **X-Ray** | Distributed tracing | Trace requests | "Trace requests" or "debug microservices" → X-Ray |
| **CloudTrail** | API audit | Who did what | "Audit API" or "compliance" → CloudTrail |
| **VPC Flow Logs** | Network traffic logs | Source/dest IP, ports | "Network troubleshooting" → Flow Logs |

### Infrastructure as Code
| Service | Definition | Feature | Exam Heuristic |
|---------|------------|---------|----------------|
| **CloudFormation** | IaC with JSON/YAML | Stacks, drift detection | "Infrastructure as Code" → CloudFormation |
| **CDK** | IaC with programming languages | Generates CloudFormation | "IaC" + "TypeScript/Python" → CDK |
| **SAM** | Serverless IaC | Extends CloudFormation | "Serverless IaC" → SAM |

### Systems Manager
| Aspect | Details |
|--------|---------|
| **Definition** | Operational hub for managing AWS and on-prem resources |
| **Key Feature** | Patch Manager, Session Manager, Run Command, Parameter Store |
| **Exam Heuristic** | Choose SSM when: fleet management, patching, secure access |

### Systems Manager Components
| Component | Definition | Exam Heuristic |
|-----------|------------|----------------|
| **Session Manager** | Secure shell without SSH | "Shell access" + "no SSH keys" → Session Manager |
| **Run Command** | Execute commands remotely | "Run script on many instances" → Run Command |
| **Patch Manager** | Automate patching | "Patch instances" → Patch Manager |
| **Parameter Store** | Store config values | "Store config" or "secrets (no rotation)" → Parameter Store |
| **State Manager** | Maintain instance state | "Keep instances configured" → State Manager |

### Cost Management
| Service | Definition | Feature | Exam Heuristic |
|---------|------------|---------|----------------|
| **Cost Explorer** | Visualize spending | Forecasting, filtering | "Analyze cost" or "visualize spend" → Cost Explorer |
| **Budgets** | Set spending alerts | Cost/usage alerts | "Budget alert" or "spending limit" → Budgets |
| **Cost & Usage Reports** | Detailed billing data | Most granular | "Detailed billing" → CUR |
| **Compute Optimizer** | Right-sizing | ML recommendations | "Right-size EC2" → Compute Optimizer |
| **Trusted Advisor** | Best practices | Cost, security, performance | "Best practices" → Trusted Advisor |

### Governance
| Service | Definition | Feature | Exam Heuristic |
|---------|------------|---------|----------------|
| **Control Tower** | Multi-account setup | Landing zone, guardrails | "Set up multi-account" → Control Tower |
| **Service Catalog** | IT-approved products | Self-service deployment | "Standardized deployments" → Service Catalog |
| **License Manager** | Track licenses | Enforce rules | "Software licensing" → License Manager |
| **Well-Architected Tool** | Architecture review | 6 pillars assessment | "Review architecture" → Well-Architected Tool |

---

## 🔗 APPLICATION INTEGRATION

### Messaging Services
| Service | Definition | Feature | Exam Heuristic |
|---------|------------|---------|----------------|
| **SQS** | Message queue service | Decouple, Standard/FIFO | "Decouple" or "queue" → SQS |
| **SNS** | Pub/sub notifications | Fanout to many | "Fanout" or "notify many" → SNS |
| **EventBridge** | Event bus | Rules, scheduling | "Event-driven" or "cron" → EventBridge |
| **Amazon MQ** | Managed ActiveMQ/RabbitMQ | Migrate existing | "ActiveMQ" or "RabbitMQ" → Amazon MQ |

### SQS Types
| Type | Definition | Exam Heuristic |
|------|------------|----------------|
| **Standard** | At-least-once, best-effort order | "High throughput" → Standard |
| **FIFO** | Exactly-once, strict order | "Order matters" or "no duplicates" → FIFO |

### SNS + SQS Pattern
| Pattern | Description | Exam Heuristic |
|---------|-------------|----------------|
| **Fanout** | SNS → Multiple SQS | "One event, many consumers" → SNS + SQS |

### Step Functions
| Aspect | Details |
|--------|---------|
| **Definition** | Visual workflow orchestration for distributed applications |
| **Key Feature** | State machines, error handling, parallel execution |
| **Exam Heuristic** | Choose when: "workflow" or "orchestrate Lambda" or "state machine" |

### Streaming
| Service | Definition | Feature | Exam Heuristic |
|---------|------------|---------|----------------|
| **Kinesis Data Streams** | Real-time streaming | Custom processing | "Real-time" + "custom processing" → Data Streams |
| **Kinesis Data Firehose** | Load to destinations | Near real-time, managed | "Load to S3/Redshift" → Firehose |
| **Kinesis Data Analytics** | SQL on streams | Real-time analytics | "SQL on stream" → Data Analytics |
| **MSK** | Managed Kafka | Kafka-compatible | "Kafka" → MSK |

### Kinesis Decision
| Need | Choice |
|------|--------|
| Custom processing, order | Data Streams |
| Just load to S3/Redshift | Firehose |
| Real-time SQL | Data Analytics |
| Kafka compatibility | MSK |

---

## 📈 ANALYTICS

| Service | Definition | Feature | Exam Heuristic |
|---------|------------|---------|----------------|
| **Athena** | Serverless SQL on S3 | Pay per query | "Query S3" or "serverless SQL" → Athena |
| **Redshift** | Data warehouse | Columnar, OLAP | "Data warehouse" or "OLAP" → Redshift |
| **Redshift Spectrum** | Query S3 from Redshift | No data loading | "S3 + Redshift" → Spectrum |
| **EMR** | Managed Hadoop/Spark | Big data processing | "Hadoop" or "Spark" or "big data" → EMR |
| **Glue** | Serverless ETL | Auto-generates code | "ETL" or "transform data" → Glue |
| **Glue Data Catalog** | Metadata repository | Schema discovery | "Data catalog" or "metadata" → Glue Catalog |
| **OpenSearch** | Search and analytics | Log analytics, search | "Search" or "log analytics" → OpenSearch |
| **QuickSight** | BI dashboards | ML insights | "Dashboard" or "BI" → QuickSight |
| **Lake Formation** | Data lake management | Security, governance | "Data lake" + "governance" → Lake Formation |

---

## 🚀 MIGRATION

| Service | Definition | Feature | Exam Heuristic |
|---------|------------|---------|----------------|
| **Migration Hub** | Track migrations | Central dashboard | "Track migration progress" → Migration Hub |
| **Application Discovery** | Discover on-prem | Agent/agentless | "Discover applications" → Discovery Service |
| **Application Migration (MGN)** | Lift-and-shift | Continuous replication | "Lift and shift" or "rehost" → MGN |
| **DMS** | Database migration | Minimal downtime | "Migrate database" → DMS |
| **SCT** | Schema conversion | Heterogeneous migration | "Different DB engine" → SCT + DMS |

### 6 Rs of Migration
| Strategy | Definition | Exam Heuristic |
|----------|------------|----------------|
| **Rehost** | Lift and shift | "Quick migration" → MGN |
| **Replatform** | Lift and optimize | "Minor optimization" → RDS, Beanstalk |
| **Repurchase** | Move to SaaS | "Replace with SaaS" |
| **Refactor** | Re-architect | "Cloud-native" → Serverless |
| **Retain** | Keep as-is | "Not ready to migrate" |
| **Retire** | Decommission | "No longer needed" |

---

## 🎯 MASTER EXAM HEURISTICS

### Always True Rules
| Rule | Details |
|------|---------|
| EC2 needs S3 access | Use IAM Role (never access keys) |
| Decouple components | Use SQS |
| Fanout to multiple | Use SNS → SQS |
| Event-driven | Lambda or EventBridge |
| Serverless SQL on S3 | Athena |
| Data warehouse | Redshift |
| NoSQL key-value | DynamoDB |
| Managed MySQL/PostgreSQL | Aurora |
| Cache database | ElastiCache Redis |
| Cache DynamoDB | DAX |
| Threat detection | GuardDuty |
| Vulnerability scan | Inspector |
| PII in S3 | Macie |
| API audit | CloudTrail |
| Compliance/config | Config |
| Secrets with rotation | Secrets Manager |
| Encrypt data | KMS |
| SSL certificates | ACM |

### Cost Optimization Rules
| Scenario | Answer |
|----------|--------|
| Steady-state workload | Reserved Instances / Savings Plans |
| Fault-tolerant batch | Spot Instances |
| Infrequent S3 access | Standard-IA or Glacier |
| Unknown access pattern | Intelligent-Tiering |
| Right-size instances | Compute Optimizer |

### High Availability Rules
| Scenario | Answer |
|----------|--------|
| Database HA | Multi-AZ (RDS) or Aurora |
| Application HA | Multi-AZ + Auto Scaling + ALB |
| Cross-region DR | Aurora Global / S3 CRR / Route 53 Failover |
| Fast DB failover | Aurora (10-30 sec) vs RDS (60-120 sec) |

---

**Good luck on your exam!** 🎉

*Complete reference with 100+ services, features, and exam heuristics for SAA-C03*
