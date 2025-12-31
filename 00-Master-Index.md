# AWS SAA-C03: Master Cheat Sheet Index

## 📚 Complete Study Guide for AWS Solutions Architect Associate Exam

This comprehensive cheat sheet collection covers all services and domains for the AWS SAA-C03 exam. Each file is organized by service category with detailed definitions, features, exam tips, and heuristics.

---

## 📑 Study Guide Files

### 1. [Compute Services](01-Compute-Services.md)
**Services Covered:**
- EC2 (Elastic Compute Cloud)
- Lambda
- Elastic Beanstalk
- ECS (Elastic Container Service)
- EKS (Elastic Kubernetes Service)
- Fargate
- AWS Batch
- Lightsail

**Key Topics:** Instance types, pricing models, serverless computing, container orchestration, batch processing

---

### 2. [Storage Services](02-Storage-Services.md)
**Services Covered:**
- S3 (Simple Storage Service)
- EBS (Elastic Block Store)
- EFS (Elastic File System)
- FSx (Windows, Lustre, NetApp ONTAP, OpenZFS)
- Storage Gateway
- Snow Family (Snowcone, Snowball, Snowmobile)
- AWS Backup

**Key Topics:** Object storage, block storage, file systems, hybrid storage, offline data transfer, backup strategies

---

### 3. [Database Services](03-Database-Services.md)
**Services Covered:**
- RDS (Relational Database Service)
- Aurora
- DynamoDB
- ElastiCache (Redis, Memcached)
- Redshift
- DocumentDB
- Neptune
- QLDB
- Timestream
- DMS (Database Migration Service)

**Key Topics:** Relational databases, NoSQL, caching, data warehousing, specialized databases, database migration

---

### 4. [Networking & Content Delivery](04-Networking-Content-Delivery.md)
**Services Covered:**
- VPC (Virtual Private Cloud)
- Route 53
- CloudFront
- API Gateway
- Direct Connect
- Global Accelerator
- Transit Gateway
- VPN
- Load Balancers (ALB, NLB, GWLB, CLB)

**Key Topics:** Network architecture, DNS, CDN, API management, hybrid connectivity, load balancing

---

### 5. [Security, Identity & Compliance](05-Security-Identity-Compliance.md)
**Services Covered:**
- IAM (Identity and Access Management)
- AWS Organizations
- Cognito
- Directory Service
- KMS (Key Management Service)
- Secrets Manager
- Parameter Store
- ACM (Certificate Manager)
- WAF & Shield
- GuardDuty
- Inspector
- Macie
- Config
- CloudTrail
- Security Hub
- Detective

**Key Topics:** Access control, authentication, encryption, threat detection, compliance, auditing

---

### 6. [Management, Monitoring & Governance](06-Management-Monitoring-Governance.md)
**Services Covered:**
- CloudWatch
- CloudFormation
- Systems Manager
- Trusted Advisor
- Service Catalog
- Control Tower
- Well-Architected Tool
- Personal Health Dashboard
- Cost Management (Cost Explorer, Budgets, Savings Plans)

**Key Topics:** Monitoring, infrastructure as code, automation, governance, cost optimization

---

### 7. [Application Integration & Analytics](07-Application-Integration-Analytics.md)
**Services Covered:**
- SQS (Simple Queue Service)
- SNS (Simple Notification Service)
- EventBridge
- Step Functions
- SWF
- AppSync
- Amazon MQ
- Kinesis (Data Streams, Firehose, Analytics, Video Streams)
- Athena
- EMR (Elastic MapReduce)
- Glue
- QuickSight
- Data Pipeline
- MSK (Managed Streaming for Kafka)

**Key Topics:** Message queuing, pub/sub, event-driven architecture, streaming, ETL, analytics, BI

---

### 8. [Migration, Transfer & Disaster Recovery](08-Migration-Transfer-Services.md)
**Services Covered:**
- Migration Hub
- Application Discovery Service
- Application Migration Service (MGN)
- Database Migration Service (DMS)
- DataSync
- Transfer Family
- Snow Family
- Disaster Recovery Strategies

**Key Topics:** Migration planning, server migration, database migration, data transfer, DR strategies (Multi-Site, Warm Standby, Pilot Light, Backup/Restore)

---

### 9. [Exam Strategy & Tips](09-Exam-Strategy-Tips.md)
**Sections Covered:**
- Exam Overview
- Exam Domains (with weightings)
- General Exam Tips
- Service Selection Heuristics
- Common Exam Traps
- Time Management
- Day Before & Day Of Tips
- Key Memorization Items
- Quick Review Checklist

---

## 🌳 Decision Tree Files (Quick Reference for Exam)

### 10. [Decision Trees: Compute Services](10-Decision-Trees-Compute.md)
**Quick Decision Trees For:**
- Main Compute Selection (EC2 vs Lambda vs ECS vs EKS)
- EC2 Instance Type Selection (T, M, C, R, I, etc.)
- EC2 Pricing Models (On-Demand, Reserved, Spot, Savings Plans)
- Container Service Selection (ECS, EKS, Fargate)
- Serverless vs Server-based Decision
- Lambda Appropriateness Check
- Batch Processing Selection

**Perfect For:** One-shot quick decisions on compute services during exam

---

### 11. [Decision Trees: Storage Services](11-Decision-Trees-Storage.md)
**Quick Decision Trees For:**
- Main Storage Selection (S3, EBS, EFS, FSx)
- S3 Storage Class Selection (Standard, IA, Glacier variants)
- EBS Volume Type Selection (gp3, io2, st1, sc1)
- File System Selection (EFS, FSx variants)
- Data Transfer Method (Online, Snowball, DataSync)
- Storage Gateway Types
- Backup Strategy

**Perfect For:** Storage and data transfer decisions in seconds

---

### 12. [Decision Trees: Database Services](12-Decision-Trees-Database.md)
**Quick Decision Trees For:**
- Main Database Selection (Relational vs NoSQL)
- Relational Database (RDS vs Aurora)
- NoSQL Database (DynamoDB, DocumentDB, Neptune)
- DynamoDB Capacity Mode (Provisioned vs On-Demand)
- DynamoDB Index Selection (GSI vs LSI)
- Caching Strategy (DAX, ElastiCache Redis, Memcached)
- Analytics & Data Warehouse (Redshift, Athena, EMR)
- Database Migration (DMS + SCT)

**Perfect For:** Database and caching decisions instantly

---

### 13. [Decision Trees: Networking & Content Delivery](13-Decision-Trees-Networking.md)
**Quick Decision Trees For:**
- Load Balancer Selection (ALB, NLB, GWLB)
- Route 53 Routing Policies (Simple, Weighted, Latency, Failover, etc.)
- VPC Components (Security Groups, NACLs, NAT Gateway, IGW)
- Hybrid Connectivity (VPN, Direct Connect, Transit Gateway)
- Content Delivery (CloudFront vs Global Accelerator)
- API Gateway Types (REST, HTTP, WebSocket)

**Perfect For:** Network architecture decisions on the spot

---

### 14. [Decision Trees: Security, Identity & Compliance](14-Decision-Trees-Security.md)
**Quick Decision Trees For:**
- Identity & Access Management (IAM Users, Roles, Cognito, Directory Service)
- IAM Policy Evaluation Logic
- Encryption & Key Management (KMS, CloudHSM, ACM)
- Secrets Management (Secrets Manager vs Parameter Store)
- Threat Detection (GuardDuty, Inspector, Macie)
- Audit & Compliance (CloudTrail, Config, Security Hub)
- Network Security (Shield, WAF, Network Firewall)

**Perfect For:** Security decisions and IAM scenarios instantly

---

### 15. [Decision Trees: Management, Monitoring & Cost](15-Decision-Trees-Management.md)
**Quick Decision Trees For:**
- Monitoring & Observability (CloudWatch, X-Ray, VPC Flow Logs)
- Infrastructure as Code (CloudFormation, CDK, SAM)
- Automation & Operations (Systems Manager, Patch Manager, Session Manager)
- Cost Optimization (Cost Explorer, Budgets, Reserved Instances, Savings Plans)
- Governance & Compliance (Organizations, Control Tower, Service Catalog)

**Perfect For:** Operations, monitoring, and cost optimization decisions

---

### 16. [Decision Trees: Integration, Messaging & Analytics](16-Decision-Trees-Integration-Analytics.md)
**Quick Decision Trees For:**
- Messaging & Integration (SQS vs SNS vs MQ)
- Event-Driven Architecture (EventBridge, Step Functions)
- Streaming Data (Kinesis Data Streams, Firehose, Analytics)
- Analytics & Big Data (Athena, Redshift, EMR, OpenSearch, QuickSight)
- ETL & Data Processing (Glue, MSK, Data Pipeline)

**Perfect For:** Integration patterns and analytics decisions instantly

---

### 28. [Decision Trees: High Availability & Disaster Recovery](28-Decision-Trees-High-Availability-DR.md)
**Quick Decision Trees For:**
- High Availability Architecture Patterns
- Disaster Recovery Strategies (Backup/Restore, Pilot Light, Warm Standby, Multi-Site)
- Multi-AZ vs Multi-Region Decision
- Backup & Recovery Selection
- Fault Tolerance Patterns
- RPO/RTO Decision Matrix

**Perfect For:** HA and DR architectural decisions instantly

---

### 29. [Decision Trees: Migration & Data Transfer](29-Decision-Trees-Migration-Transfer.md)
**Quick Decision Trees For:**
- 6 Rs of Migration (Retire, Retain, Repurchase, Rehost, Replatform, Refactor)
- Data Transfer Method Selection (Snow Family, DataSync, Transfer Acceleration)
- Database Migration (DMS, SCT)
- Application Migration (MGN, VMware, Containers)
- Hybrid Architecture Patterns

**Perfect For:** Migration strategy and data transfer decisions

---

### 30. [Decision Trees: Cost Optimization](30-Decision-Trees-Cost-Optimization.md)
**Quick Decision Trees For:**
- EC2 Pricing Decision (On-Demand, Reserved, Spot, Savings Plans)
- Storage Cost Optimization (S3 classes, EBS types, lifecycle policies)
- Database Cost Optimization (Capacity modes, Reserved Instances)
- Data Transfer Cost Optimization
- Serverless Cost Optimization
- Cost Management Tools (Cost Explorer, Budgets, Compute Optimizer)

**Perfect For:** Cost optimization decisions for any service

---

### 31. [Decision Trees: Serverless Architecture](31-Decision-Trees-Serverless.md)
**Quick Decision Trees For:**
- Serverless Service Selection
- Lambda Decision Trees (appropriateness, triggers, configuration)
- API Gateway Decision (REST vs HTTP vs WebSocket)
- Serverless Patterns (Web App, Event-Driven, Fan-Out, Saga)
- Event-Driven Architecture
- Serverless Data Processing

**Perfect For:** Serverless architecture and Lambda decisions

---

### 32. [Decision Trees: Auto Scaling & Elasticity](32-Decision-Trees-Auto-Scaling.md)
**Quick Decision Trees For:**
- Auto Scaling Types (EC2, Application Auto Scaling)
- EC2 Auto Scaling Configuration
- Scaling Policies (Target Tracking, Step, Scheduled, Predictive)
- Elasticity Patterns
- Queue-Based Scaling
- Mixed Instance Scaling

**Perfect For:** Auto scaling and elasticity decisions

---

### 33. [Decision Trees: Exam Question Patterns](33-Decision-Trees-Exam-Patterns.md)
**⚡ CRITICAL FOR EXAM DAY**

**Contains:**
- Keyword → Service Quick Reference
- Pattern Recognition for All Question Types
- HA, DR, Cost, Security, Performance Decision Trees
- Common Exam Traps and Corrections
- Service Comparison Quick Reference Tables
- Answer Selection Strategy
- Final Exam Tips

**📌 REVIEW THIS BEFORE EVERY PRACTICE EXAM!**

---

### 34. [🏆 200+ Scenario-Based Quick Answers](34-Scenario-Quick-Answers.md)
**⚡ ULTIMATE EXAM PRACTICE FILE**

**Contains:**
- 200+ Exam-Style Scenarios with Instant Answers
- All Topics: Compute, Storage, Database, Networking, Security, Integration, Analytics
- Migration & Transfer Scenarios
- Cost & Management Scenarios
- Architecture Pattern Scenarios
- Exam Day Quick Reference Tables
- Keyword → Service Mapping

**Perfect For:** Rapid-fire practice, pattern recognition, last-minute review

**📌 PRACTICE WITH THIS FILE TO BUILD SPEED AND CONFIDENCE!**

---

### 35. [📖 One-Liner Service Definitions & Features](35-One-Liner-Service-Definitions.md)
**⚡ COMPLETE SERVICE REFERENCE**

**Contains:**
- Every AWS Service in One Line (Definition + Key Feature)
- Categories: Compute, Storage, Database, Networking, Security, Analytics, Migration, ML/AI
- Load Balancers Comparison
- Cost Management Tools
- Developer Tools
- Memory Aids for Exam Day

**Perfect For:** Quick service lookup, understanding "what does this service do?", last-minute memorization

**📌 USE THIS AS YOUR SERVICE DICTIONARY!**

---

### 36. [🎯 Complete Service Reference with Exam Heuristics](36-Complete-Service-Reference-Heuristics.md)
**⚡ ULTIMATE SERVICE GUIDE - DEFINITION + FEATURE + WHEN TO CHOOSE**

**Contains:**
- Every AWS Service: Definition + Key Feature + Exam Heuristic
- EC2 Instance Types with Selection Criteria
- EC2 Pricing Models Decision Guide
- S3 Storage Classes with Use Cases
- EBS Volume Types Selection
- Database Decision Trees (RDS vs Aurora, DynamoDB features)
- Load Balancer Selection Guide
- Route 53 Routing Policies
- All Security Services with "When to Choose"
- CloudWatch Components
- Messaging Services Comparison (SQS vs SNS vs EventBridge)
- Kinesis Decision Guide
- Migration Strategies (6 Rs)
- Master Exam Heuristics (Always True Rules)
- Cost Optimization Rules
- High Availability Rules

**Perfect For:** Deep understanding of each service, knowing exactly when to choose what

**📌 THIS IS YOUR COMPLETE EXAM REFERENCE - STUDY THIS THOROUGHLY!**

---

### 37. [🏗️ Exam Heuristics & Multi-Service Solutions](37-Exam-Heuristics-Multi-Service-Solutions.md)
**⚡ ARCHITECTURE PATTERNS & COMBINED SERVICE SOLUTIONS**

**Contains:**
- 10 Complete Multi-Service Architecture Diagrams:
  - Highly Available Web Application
  - Serverless Image Processing Pipeline
  - Real-Time Data Analytics Pipeline
  - Decoupled Microservices (Fan-Out Pattern)
  - E-Commerce Order Processing with Step Functions
  - Global Application with DR
  - Secure Application with Secrets Management
  - Data Lake Architecture
  - Hybrid Cloud Architecture
  - Secure Multi-Tier VPC Architecture
- Exam Heuristics by Category:
  - Cost Optimization | Security | Performance | High Availability | Analytics | Integration | Migration
- Keyword → Solution Quick Mapping (50+ mappings)
- Common Exam Traps (Wrong vs Right answers)
- 10 Quick Reference Solution Patterns
- Exam Day Checklist

**Perfect For:** Understanding how services work TOGETHER, architecture questions, scenario-based questions

**📌 CRITICAL FOR ARCHITECTURE QUESTIONS - MOST EXAM QUESTIONS TEST COMBINED SOLUTIONS!**

---

### 38. [📐 Solution Patterns & Exam Heuristics](38-Solution-Patterns-Exam-Heuristics.md)
**⚡ 50 SOLUTION PATTERNS FOR INSTANT PATTERN MATCHING**

**Contains:**
- **15 Pattern Categories:**
  1. Web Application Patterns (Static, Dynamic, Serverless, Microservices)
  2. Serverless Patterns (Event-Driven, Workflows, APIs)
  3. Data Processing Patterns (Real-Time, Batch, Big Data, Data Lake)
  4. Database Patterns (Relational, Read-Heavy, Global, NoSQL, Caching)
  5. Storage Patterns (Tiered, Cross-Region, Shared, Hybrid)
  6. Networking Patterns (Load Balancing, DNS, Connectivity)
  7. Security Patterns (Defense in Depth, Secure Data, Compliance)
  8. High Availability Patterns (Multi-AZ, Auto Scaling, Queue-Based)
  9. Disaster Recovery Patterns (Backup, Pilot Light, Warm Standby, Active-Active)
  10. Cost Optimization Patterns (Compute, Storage, Data Transfer)
  11. Migration Patterns (Database, Server, Data Transfer)
  12. Hybrid Cloud Patterns (Connectivity, Storage Gateway)
  13. Decoupling Patterns (Queue, Fan-Out, Event-Driven)
  14. Caching Patterns (Database, API, Content)
  15. Monitoring Patterns (Application, Infrastructure)

- **50 Complete Solution Patterns** with diagrams and component explanations
- **Pattern → Solution Quick Reference Table**
- **Exam Success Checklist**

**Perfect For:** Pattern-based question solving, instant architecture decisions

**📌 MEMORIZE THESE PATTERNS - THEY APPEAR IN 90%+ OF EXAM QUESTIONS!**

---

### 17. [🔥 MASTER Decision Tree - Ultimate Quick Reference](17-Master-Decision-Tree.md)
**⚡ THE MOST IMPORTANT FILE FOR EXAM DAY**

**Contains:**
- Super Quick Service Selection (Keyword → Service)
- Top 20 Exam Patterns (Must Know)
- All Essential Decision Trees Consolidated
- Exam Heuristics (Always True Rules)
- Common Exam Traps to Avoid
- Service Selection Matrix
- Time-Saving Shortcuts
- Final Exam Day Checklist

**Perfect For:** Last-minute review, exam day reference, 10-second decisions

**📌 READ THIS FILE THE NIGHT BEFORE YOUR EXAM!**

---

## 🎯 Quick Reference: Service Selection Matrix

| Scenario | Service(s) |
|----------|-----------|
| **Compute** |
| Full control, custom config | EC2 |
| Event-driven, serverless | Lambda |
| Quick web app deployment | Elastic Beanstalk |
| Docker containers | ECS/Fargate |
| Kubernetes | EKS |
| Batch processing | AWS Batch |
| **Storage** |
| Object storage | S3 |
| Block storage | EBS |
| Shared file system (Linux) | EFS |
| Shared file system (Windows) | FSx for Windows |
| HPC storage | FSx for Lustre |
| Hybrid storage | Storage Gateway |
| Offline transfer | Snow Family |
| **Database** |
| RDBMS | RDS/Aurora |
| NoSQL | DynamoDB |
| Caching | ElastiCache |
| Data warehouse | Redshift |
| Graph database | Neptune |
| **Networking** |
| DNS | Route 53 |
| CDN | CloudFront |
| API | API Gateway |
| HTTP/HTTPS LB | ALB |
| TCP/UDP LB | NLB |
| Dedicated connection | Direct Connect |
| **Security** |
| Access management | IAM |
| Encryption keys | KMS |
| Secrets with rotation | Secrets Manager |
| User authentication | Cognito |
| Threat detection | GuardDuty |
| **Management** |
| Monitoring | CloudWatch |
| Infrastructure as Code | CloudFormation |
| Fleet management | Systems Manager |
| Cost analysis | Cost Explorer |
| **Integration** |
| Message queue | SQS |
| Pub/sub | SNS |
| Event routing | EventBridge |
| Orchestration | Step Functions |
| Streaming | Kinesis |
| **Analytics** |
| Query S3 | Athena |
| ETL | Glue |
| BI dashboards | QuickSight |
| Big data | EMR |

---

## 🏛️ Well-Architected Framework - 6 Pillars

### 1. Operational Excellence
- **Key Services**: CloudWatch, CloudFormation, Systems Manager, CodePipeline
- **Focus**: Run and monitor systems, continuous improvement
- **Exam Weight**: ~15%

### 2. Security
- **Key Services**: IAM, KMS, VPC, CloudTrail, GuardDuty, WAF
- **Focus**: Protect data, systems, and assets
- **Exam Weight**: ~30%

### 3. Reliability
- **Key Services**: Auto Scaling, Multi-AZ, Route 53, Backup
- **Focus**: Recovery from failures, meet demand
- **Exam Weight**: ~26%

### 4. Performance Efficiency
- **Key Services**: EC2 types, Lambda, CloudFront, ElastiCache
- **Focus**: Efficient use of resources, adapt to changes
- **Exam Weight**: ~24%

### 5. Cost Optimization
- **Key Services**: Cost Explorer, S3 Intelligent-Tiering, Spot Instances, Reserved Instances
- **Focus**: Minimize costs, maximize value
- **Exam Weight**: ~20%

### 6. Sustainability
- **Key Services**: Auto Scaling, serverless, managed services
- **Focus**: Minimize environmental impact
- **Exam Weight**: ~5%

---

## 🔑 Most Important Exam Concepts

### Must Know Cold
1. **IAM**: Users, groups, roles, policies, evaluation logic
2. **VPC**: Subnets, route tables, IGW, NAT Gateway, Security Groups, NACLs
3. **S3**: Storage classes, lifecycle, versioning, encryption, replication
4. **EC2**: Instance types, pricing models, placement groups
5. **RDS/Aurora**: Multi-AZ vs Read Replicas, backups, encryption
6. **DynamoDB**: Provisioned vs On-Demand, GSI vs LSI, DAX
7. **Lambda**: Limits, triggers, use cases
8. **CloudWatch**: Metrics, alarms, logs, Events/EventBridge
9. **Load Balancers**: ALB vs NLB, when to use each
10. **Route 53**: Routing policies (weighted, latency, failover, geolocation)

### Key Patterns
- **High Availability**: Multi-AZ + Auto Scaling + ELB + Route 53
- **Disaster Recovery**: Backups + Cross-region replication + Route 53 failover
- **Decoupling**: SQS + SNS + EventBridge + Lambda
- **Caching**: CloudFront + API Gateway + ElastiCache + DAX
- **Security**: Least privilege + Encryption + Private subnets + MFA
- **Cost Optimization**: Right-sizing + Reserved/Spot + S3 IA/Glacier + Lifecycle

---

## 📊 Exam Domain Breakdown

| Domain | Weight | Focus |
|--------|--------|-------|
| **1. Design Secure Architectures** | 30% | IAM, encryption, network security |
| **2. Design Resilient Architectures** | 26% | HA, fault tolerance, decoupling, DR |
| **3. Design High-Performing Architectures** | 24% | Compute, storage, database, network optimization |
| **4. Design Cost-Optimized Architectures** | 20% | Storage, compute, data transfer cost optimization |

---

## 🎓 Study Approach Recommendations

### Week 1-2: Core Services
- [ ] Read files 1-3 (Compute, Storage, Database)
- [ ] Practice questions on these services
- [ ] Hands-on labs for EC2, S3, RDS

### Week 3-4: Networking & Security
- [ ] Read files 4-5 (Networking, Security)
- [ ] Practice VPC scenarios
- [ ] Understand IAM thoroughly

### Week 5-6: Management & Integration
- [ ] Read files 6-7 (Management, Integration)
- [ ] Focus on CloudWatch, CloudFormation
- [ ] Understand event-driven patterns

### Week 7-8: Migration & Final Review
- [ ] Read file 8 (Migration)
- [ ] Read file 9 (Exam Strategy)
- [ ] Take practice exams
- [ ] Review weak areas

### Last Week Before Exam
- [ ] Quick review of all cheat sheets
- [ ] Focus on exam tips and heuristics
- [ ] Take full-length practice exam
- [ ] Review flagged topics
- [ ] Get good sleep!

---

## 💡 Golden Rules for the Exam

1. **Always Choose AWS Services Over Custom Solutions**
   - Managed > Self-managed
   - Serverless > Server-based (when appropriate)

2. **Security First**
   - Encryption at rest and in transit
   - Least privilege
   - IAM roles for AWS resources
   - Private subnets for databases

3. **High Availability = Multi-AZ**
   - RDS Multi-AZ for failover
   - Multiple NAT Gateways
   - ELB across AZs
   - Auto Scaling groups

4. **Cost Optimization Keywords**
   - "Cost-effective" → Look for Reserved, Spot, S3 IA/Glacier
   - "Variable workload" → Auto Scaling, On-Demand
   - "Predictable workload" → Reserved Instances/Savings Plans

5. **Performance Keywords**
   - "Low latency" → CloudFront, ElastiCache, Direct Connect
   - "High throughput" → Appropriate instance types, EBS types
   - "Real-time" → Kinesis, Lambda, DynamoDB

6. **When in Doubt**
   - Choose the AWS-native solution
   - Choose the managed service
   - Choose the more secure option
   - Choose Multi-AZ for HA questions

---

## 🚀 Final Checklist

### Technical Knowledge
- [ ] Can explain each service and its use cases
- [ ] Know the differences between similar services
- [ ] Understand architectural patterns
- [ ] Know service limits and constraints
- [ ] Can design for HA, security, performance, and cost

### Exam Readiness
- [ ] Completed practice exams (scoring 80%+)
- [ ] Reviewed all wrong answers
- [ ] Understand exam format and question types
- [ ] Know time management strategy
- [ ] Have ID and confirmation ready

### Mental Preparation
- [ ] Confident in core services
- [ ] Comfortable with scenario-based questions
- [ ] Ready to eliminate wrong answers
- [ ] Will flag and review difficult questions
- [ ] Calm and focused mindset

---

## 📞 Additional Resources

### AWS Official Resources
- AWS Whitepapers (Well-Architected Framework)
- AWS FAQs for key services
- AWS Architecture Center
- AWS Skill Builder (free training)

### Practice Exams
- AWS Official Practice Exam
- Tutorials Dojo Practice Tests
- WhizLabs Practice Tests
- Udemy Practice Exams

### Hands-On Practice
- AWS Free Tier (12 months)
- AWS Workshops
- A Cloud Guru Labs
- Qwiklabs

---

## 🎯 You're Ready!

You've covered:
- ✅ 100+ AWS services
- ✅ All exam domains
- ✅ Architectural patterns
- ✅ Best practices
- ✅ Exam strategies

**Now go ace that exam!** 🏆

---

## 🤖 OTHER CERTIFICATION GUIDES

### [GitHub Copilot Certification (GH-300) Complete Guide](GitHub-Copilot-GH300-Exam-Guide.md)
**Microsoft GitHub Copilot Certification Exam - Comprehensive Study Guide**

**Contains:**
- Exam Overview (domains, format, passing score)
- GitHub Copilot Fundamentals (products, IDEs, how it works)
- All Features (code completion, chat, CLI, slash commands, @ participants)
- Plan Comparison (Individual vs Business vs Enterprise)
- Prompt Engineering Best Practices
- Security & Privacy (data handling, responsible AI)
- Administration & Management (policies, seat management, audit logs)
- **Complete Exam Heuristics** (keyword → answer mapping)
- **15 Scenario-Based Questions with Answers**
- Quick Reference Card (shortcuts, commands, plans)

**📌 COMPLETE GH-300 EXAM PREPARATION - DETAILED STUDY GUIDE!**

---

### [GitHub Copilot GH-300 Quick Cheatsheet](GitHub-Copilot-GH300-Cheatsheet.md)
**⚡ PRINTABLE EXAM DAY REFERENCE**

**Contains:**
- All Keyboard Shortcuts (Tab, Alt+], Ctrl+I, etc.)
- All Slash Commands (/explain, /fix, /tests, /doc, /optimize, /new)
- All @ Participants (@workspace, @vscode, @terminal, @github)
- Complete Plan Comparison Table (Individual vs Business vs Enterprise)
- Enterprise-Only Features Quick List
- CLI Commands (ghcs, ghce)
- Content Exclusion Configuration
- **Instant Answer Heuristics** (50+ keyword → answer mappings)
- Quick Scenario Answers
- Common Exam Traps
- Printable Quick Reference Card

**📌 PRINT THIS BEFORE YOUR EXAM - CONDENSED QUICK REFERENCE!**

---

## 📋 AWS SAA-C03 SERVICE CHEATSHEETS (PRINT-READY)

### [40. EC2 & Compute Cheatsheet](40-Cheatsheet-EC2-Compute.md)
**Complete EC2 & Compute Quick Reference**
- All Instance Types (M, C, R, X, I, D, G, P, T, Hpc, Mac)
- Instance Naming Convention
- All Purchasing Options (On-Demand, Reserved, Spot, Savings Plans, Dedicated)
- Spot Instance Strategies
- Placement Groups (Cluster, Spread, Partition)
- EBS Volume Types with IOPS Limits
- Load Balancer Types (ALB, NLB, GLB)
- Auto Scaling Policies
- Instant Answer Heuristics

**📌 PRINT FOR COMPUTE QUESTIONS!**

---

### [41. S3 & Storage Cheatsheet](41-Cheatsheet-S3-Storage.md)
**Complete Storage Quick Reference**
- All S3 Storage Classes with Retrieval Times
- S3 Glacier Retrieval Options
- S3 Encryption Types (SSE-S3, SSE-KMS, SSE-C)
- S3 Replication Rules
- S3 Lifecycle Rules
- EFS Performance & Throughput Modes
- FSx Types (Windows, Lustre, NetApp, OpenZFS)
- Storage Gateway Types
- Snow Family Devices
- Instant Answer Heuristics

**📌 PRINT FOR STORAGE QUESTIONS!**

---

### [42. Database Services Cheatsheet](42-Cheatsheet-Database-Services.md)
**Complete Database Quick Reference**
- RDS Engines & Features
- Multi-AZ vs Read Replicas
- Aurora Architecture & Global Database
- DynamoDB Capacity Modes & Indexes
- DynamoDB Streams & Global Tables
- ElastiCache Redis vs Memcached
- Redshift Architecture
- Specialty Databases (DocumentDB, Neptune, QLDB, Timestream)
- Instant Answer Heuristics

**📌 PRINT FOR DATABASE QUESTIONS!**

---

### [43. VPC & Networking Cheatsheet](43-Cheatsheet-VPC-Networking.md)
**Complete Networking Quick Reference**
- VPC Components & CIDR Rules
- Security Groups vs NACLs
- NAT Gateway vs NAT Instance
- VPC Endpoints (Gateway vs Interface)
- VPC Peering & Transit Gateway
- VPN vs Direct Connect
- Route 53 Routing Policies
- CloudFront Features & Security
- Global Accelerator
- Instant Answer Heuristics

**📌 PRINT FOR NETWORKING QUESTIONS!**

---

### [44. Security & Identity Cheatsheet](44-Cheatsheet-Security-Identity.md)
**Complete Security Quick Reference**
- IAM Policies & Evaluation Order
- STS APIs
- AWS Organizations & SCPs
- KMS Key Types & Encryption
- Secrets Manager vs Parameter Store
- WAF Rules & Shield Tiers
- Inspector, Macie, GuardDuty
- Cognito User Pools vs Identity Pools
- ACM & Certificate Management
- Instant Answer Heuristics

**📌 PRINT FOR SECURITY QUESTIONS!**

---

### [45. Serverless & Integration Cheatsheet](45-Cheatsheet-Serverless-Integration.md)
**Complete Serverless Quick Reference**
- Lambda Limits & Invocation Types
- Lambda VPC & Concurrency
- API Gateway Types (REST vs HTTP)
- SQS Standard vs FIFO
- SNS Features & Fanout Pattern
- Kinesis Streams vs Firehose
- Step Functions Workflow Types
- EventBridge Rules & Targets
- Instant Answer Heuristics

**📌 PRINT FOR SERVERLESS QUESTIONS!**

---

### [46. Containers, Migration & Analytics Cheatsheet](46-Cheatsheet-Containers-Migration-Analytics.md)
**Complete Containers/Migration/Analytics Quick Reference**
- ECS Launch Types (EC2 vs Fargate)
- EKS Node Types
- ECR Features
- DMS & SCT Usage
- DataSync vs Storage Gateway
- Athena Optimization
- Glue Components
- EMR Node Types
- OpenSearch Use Cases
- Instant Answer Heuristics

**📌 PRINT FOR CONTAINERS/MIGRATION/ANALYTICS QUESTIONS!**

---

### [47. Monitoring & Governance Cheatsheet](47-Cheatsheet-Monitoring-Governance.md)
**Complete Monitoring Quick Reference**
- CloudWatch Metrics & Alarms
- CloudWatch Logs & Agent
- CloudTrail Event Types
- AWS Config Rules & Remediation
- AWS Organizations & SCPs
- Cost Explorer vs Budgets vs CUR
- Systems Manager Features
- Trusted Advisor Categories
- Instant Answer Heuristics

**📌 PRINT FOR MONITORING/GOVERNANCE QUESTIONS!**

---

### [48. HA, DR & Architecture Cheatsheet](48-Cheatsheet-HA-DR-Architecture.md)
**Complete HA/DR Quick Reference**
- Well-Architected Framework Pillars
- Availability Targets (99.9%, 99.99%, etc.)
- DR Strategies (Backup, Pilot Light, Warm, Hot)
- Multi-Region Patterns
- HA Patterns by Service
- Load Balancing Patterns
- Auto Scaling Best Practices
- Resilience Patterns
- Global vs Regional Services
- Instant Answer Heuristics

**📌 PRINT FOR HA/DR QUESTIONS!**

---

### [49. Master Quick Reference Cheatsheet](49-Cheatsheet-Master-Quick-Reference.md)
**🌟 ULTIMATE EXAM DAY REFERENCE 🌟**
- Service Selection Keywords (Instant Answers)
- Security Quick Answers
- Storage Quick Answers
- Database Quick Answers
- Networking Quick Answers
- Monitoring Quick Answers
- HA/DR Quick Answers
- Performance Quick Answers
- Cost Quick Answers
- Critical Numbers to Memorize
- Pre-Exam Checklist
- Exam Traps to Avoid
- Exam Strategies

**📌 THIS IS YOUR EXAM DAY BIBLE - PRINT THIS!**

---

## 🔒 DESIGN SECURE ARCHITECTURES - DEEP DIVE (30% of Exam)

### [50. Security Domain Part 1: Identity & Detection Services](50-Security-Domain-Part1-Identity-Detection.md)
**⚡ COMPREHENSIVE SECURITY SERVICES REFERENCE**

**Contains:**
- **Domain Overview** (30% weight, 4 focus areas)
- **19 Security Services with Complete Details:**
  - **Identity & Access Management:**
    - IAM (Users, Groups, Roles, Policies, Identity Providers)
    - AWS Organizations (SCPs, OUs, Consolidated Billing)
    - Cognito (User Pools vs Identity Pools, Authentication Flows)
    - Directory Service (Types, Use Cases)
  - **Encryption & Key Management:**
    - KMS (Key Types, CMK, Automatic Rotation, Grants, Policies)
    - Secrets Manager (Rotation, Cross-Account, RDS Integration)
    - Parameter Store (Tiers, Types, Advanced Features)
    - ACM (Certificate Types, Validation Methods, Private CA)
  - **Detection & Compliance:**
    - GuardDuty (Threat Detection, Findings, Data Sources)
    - Inspector (Vulnerability Scanning, EC2 vs Lambda)
    - Macie (S3 Data Discovery, Sensitive Data Patterns)
    - Security Hub (Aggregation, Standards, Integrations)
    - Detective (Investigation, Root Cause Analysis)
    - AWS Config (Rules, Conformance Packs, Remediation)
    - CloudTrail (Management vs Data Events, Log Validation)

- **Each Service Has:**
  - Definition
  - Key Concepts
  - Configuration Rules
  - Limits/Quotas
  - Exam Heuristics (Keyword → Answer)

**📌 MASTER IAM, ENCRYPTION, AND DETECTION FOR 30% OF EXAM!**

---

### [51. Security Domain Part 2: VPC, S3, Database & Compute Security](51-Security-Domain-Part2-VPC-S3-DB-Compute.md)
**⚡ RESOURCE-LEVEL SECURITY CONFIGURATIONS**

**Contains:**
- **VPC Security (Complete Reference):**
  - Security Groups vs NACLs (15-Point Comparison Table)
  - Security Group Referencing Patterns
  - NAT Gateway Security
  - VPC Endpoints (Gateway vs Interface)
  - VPC Flow Logs (Configuration, Analysis)
  - Network Firewall (Stateful/Stateless Rules)
  - PrivateLink Architecture

- **S3 Security (Complete Reference):**
  - Bucket Policies vs IAM Policies
  - All Encryption Types (SSE-S3, SSE-KMS, SSE-C, Client-Side)
  - S3 Block Public Access (Account vs Bucket Level)
  - S3 Object Lock (Governance vs Compliance Mode)
  - S3 Access Points
  - Presigned URLs
  - S3 Bucket Keys
  - Cross-Account Access Patterns

- **Database Security:**
  - RDS Encryption (At-Rest, In-Transit)
  - RDS IAM Authentication
  - Aurora Security Features
  - DynamoDB Encryption & VPC Endpoints
  - Redshift Security (Encryption, VPC)
  - ElastiCache Security

- **Compute Security:**
  - EC2 Security (Instance Profiles, IMDSv2)
  - Systems Manager Session Manager
  - Lambda Security (VPC, IAM Roles)
  - ECS/EKS Security

- **Exam Heuristics for Each Category**

**📌 KNOW ALL RESOURCE-LEVEL SECURITY FOR VPC, S3, DB, COMPUTE!**

---

### [52. Security Domain Part 3: Patterns, Scenarios & Exam Questions](52-Security-Domain-Part3-Patterns-Scenarios.md)
**⚡ SECURITY ARCHITECTURE PATTERNS & EXAM PRACTICE**

**Contains:**
- **5 Security Architecture Patterns (ASCII Diagrams):**
  1. Secure Three-Tier Web Application
  2. Secure Serverless Application
  3. Secure Multi-Account Architecture
  4. Secure Data Lake
  5. Secure Hybrid Connectivity

- **8 Detailed Exam Scenario Questions:**
  1. Cross-Account S3 Access
  2. Encrypting Existing RDS
  3. Restricting S3 to VPC
  4. Lambda Database Access (RDS Proxy)
  5. Centralized Security Monitoring
  6. Secure API for Partners
  7. Rotating Database Credentials
  8. Preventing Data Exfiltration

- **Security Domain Instant Answers:**
  - Encryption Heuristics
  - Access Control Heuristics
  - Network Security Heuristics
  - Protection Heuristics
  - Detection Heuristics
  - Secrets Heuristics

- **Final Security Checklist (23 Items)**

**📌 PRACTICE THESE PATTERNS - THEY APPEAR IN SECURITY QUESTIONS!**

---

## 🔄 DESIGN RESILIENT ARCHITECTURES - DEEP DIVE (26% of Exam)

### [53. Resilient Domain Part 1: High Availability & Fault Tolerance](53-Resilient-Domain-Part1-HA-Fault-Tolerance.md)
**⚡ LOAD BALANCING, AUTO SCALING & HA PATTERNS**

**Contains:**
- **Domain Overview** (26% weight, 4 focus areas)
- **Elastic Load Balancing (Complete Reference):**
  - ALB (Layer 7): Routing rules, target groups, sticky sessions, algorithms
  - NLB (Layer 4): Static IP, UDP support, PrivateLink
  - GWLB (Layer 3): Third-party appliances, GENEVE
  - Comparison tables and use cases
- **EC2 Auto Scaling:**
  - Launch Template vs Launch Configuration
  - All Scaling Policies (Target Tracking, Step, Scheduled, Predictive)
  - Lifecycle Hooks (Pending, Terminating)
  - Termination Policies
- **Application Auto Scaling** (DynamoDB, ECS, Aurora, Lambda)
- **Health Checks** (EC2, ELB, Route 53)
- **Route 53:**
  - All 8 Routing Policies with diagrams
  - Alias vs CNAME comparison
  - Record types
- **Availability Concepts:**
  - Availability targets (99.9% to 99.999%)
  - Multi-AZ architecture patterns

**📌 MASTER HA PATTERNS FOR RESILIENT ARCHITECTURE QUESTIONS!**

---

### [54. Resilient Domain Part 2: Decoupling & Messaging](54-Resilient-Domain-Part2-Decoupling-Messaging.md)
**⚡ SQS, SNS, EVENTBRIDGE & STEP FUNCTIONS**

**Contains:**
- **Amazon SQS (Complete Reference):**
  - Standard vs FIFO comparison
  - Visibility Timeout with diagrams
  - Long Polling vs Short Polling
  - Dead Letter Queues (DLQ)
  - SQS with Auto Scaling (queue-based scaling)
  - All limits and configurations
- **Amazon SNS:**
  - Pub/Sub architecture
  - All subscription types
  - SNS + SQS Fan-Out Pattern
  - Message Filtering with examples
- **Amazon EventBridge:**
  - Event Bus types (Default, Custom, Partner)
  - Event patterns and rules
  - Schedule expressions
- **AWS Step Functions:**
  - State machine architecture
  - All state types (Task, Choice, Parallel, Map, Wait)
  - Standard vs Express workflows
  - Integration patterns
- **Decoupling Comparison:**
  - SQS vs SNS vs EventBridge vs Step Functions
  - Decision tree for choosing service
- **Amazon MQ** (for migrations)

**📌 UNDERSTAND ALL DECOUPLING PATTERNS FOR RESILIENT DESIGN!**

---

### [55. Resilient Domain Part 3: Disaster Recovery & Backup](55-Resilient-Domain-Part3-Disaster-Recovery.md)
**⚡ DR STRATEGIES, RPO/RTO & AWS BACKUP**

**Contains:**
- **DR Fundamentals:**
  - RPO vs RTO definitions with visualizations
  - DR Strategy comparison table
- **All 4 DR Strategies (with ASCII diagrams):**
  1. Backup and Restore (lowest cost, hours RTO)
  2. Pilot Light (core running, 10-30 min RTO)
  3. Warm Standby (scaled-down running, minutes RTO)
  4. Multi-Site Active-Active (near-zero RTO/RPO)
- **DR Strategy Decision Matrix**
- **AWS Backup:**
  - Supported services
  - Backup Plans, Vaults, Lifecycle
  - Cross-Region and Cross-Account backup
  - Vault Lock (immutable)
- **Service-Specific DR:**
  - RDS Multi-AZ vs Read Replicas
  - Aurora Global Database
  - DynamoDB Global Tables + PITR
  - S3 Cross-Region Replication
  - EBS Snapshots + Data Lifecycle Manager

**📌 KNOW ALL DR STRATEGIES AND WHEN TO USE EACH!**

---

### [56. Resilient Domain Part 4: Patterns, Scenarios & Exam Questions](56-Resilient-Domain-Part4-Patterns-Scenarios.md)
**⚡ ARCHITECTURE PATTERNS & EXAM PRACTICE**

**Contains:**
- **4 Multi-Tier Architecture Patterns (ASCII diagrams):**
  1. Classic Three-Tier Web Application
  2. Serverless Resilient Architecture
  3. Decoupled Microservices
  4. Global Multi-Region Application
- **8 Detailed Exam Scenarios:**
  1. E-Commerce Peak Traffic
  2. Global Application Latency
  3. Decoupling Order Processing
  4. Database Failover (RPO/RTO)
  5. Handling Traffic Spikes
  6. Zero-Downtime Deployment
  7. Message Processing Failure
  8. Cross-Region Disaster Recovery
- **Resilience Domain Instant Answers:**
  - High Availability heuristics
  - Scaling heuristics
  - Decoupling heuristics
  - DR heuristics
  - Load Balancing heuristics
- **Final Resilience Checklist (22 items)**

**📌 PRACTICE THESE SCENARIOS - THEY REPRESENT EXAM QUESTION PATTERNS!**

---

## ⚡ DESIGN HIGH-PERFORMING ARCHITECTURES - DEEP DIVE (24% of Exam)

### [57. Performance Domain Part 1: Compute Performance](57-Performance-Domain-Part1-Compute.md)
**⚡ EC2, LAMBDA, CONTAINERS PERFORMANCE OPTIMIZATION**

**Contains:**
- **Domain Overview** (24% weight, focus areas)
- **EC2 Instance Types (Complete Reference):**
  - All Instance Families (M, C, R, X, I, D, G, P, T, Hpc, Mac)
  - Instance Type Naming Convention
  - Specs per family (vCPU, Memory, Use Cases)
- **Placement Groups:**
  - Cluster (HPC, low latency)
  - Spread (HA, separate hardware)
  - Partition (Hadoop, Kafka)
  - Comparison table with use cases
- **Enhanced Networking:**
  - ENA (Elastic Network Adapter) - up to 100 Gbps
  - EFA (Elastic Fabric Adapter) - HPC, MPI
  - SR-IOV hardware virtualization
- **Lambda Performance:**
  - Memory-CPU scaling relationship
  - Cold start optimization
  - Provisioned Concurrency
  - Lambda limits and configuration
- **Container Performance:**
  - ECS capacity providers
  - EKS node types
  - Fargate sizing

**📌 MASTER COMPUTE PERFORMANCE FOR 24% OF EXAM!**

---

### [58. Performance Domain Part 2: Storage Performance](58-Performance-Domain-Part2-Storage.md)
**⚡ EBS, S3, EFS, FSX PERFORMANCE OPTIMIZATION**

**Contains:**
- **EBS Volume Types (Complete Reference):**
  - gp3 (3,000 baseline IOPS, up to 16,000)
  - gp2 (3 IOPS/GB, up to 16,000)
  - io2 (up to 64,000 IOPS)
  - io2 Block Express (up to 256,000 IOPS)
  - st1 (throughput optimized HDD - 500 MB/s)
  - sc1 (cold HDD - 250 MB/s)
- **EBS IOPS/Throughput Selection Guide**
- **EBS-Optimized Instances**
- **Instance Store** (highest performance, ephemeral)
- **S3 Performance Optimization:**
  - Prefix partitioning (3,500 PUT, 5,500 GET per prefix)
  - Multipart Upload (>100MB recommended, required >5GB)
  - S3 Transfer Acceleration
  - Byte-Range Fetches
- **EFS Performance Modes:**
  - General Purpose (35,000 IOPS)
  - Max I/O (500,000+ IOPS)
- **EFS Throughput Modes:**
  - Bursting vs Provisioned vs Elastic
- **FSx Types Comparison:**
  - FSx for Windows
  - FSx for Lustre (HPC, sub-ms latency)
  - FSx for NetApp ONTAP
  - FSx for OpenZFS

**📌 KNOW ALL STORAGE LIMITS AND PERFORMANCE CHARACTERISTICS!**

---

### [59. Performance Domain Part 3: Database & Network Performance](59-Performance-Domain-Part3-Database-Network.md)
**⚡ RDS, AURORA, DYNAMODB, CACHING, CLOUDFRONT**

**Contains:**
- **RDS Performance:**
  - Instance classes (db.m, db.r, db.x, db.t)
  - Read Replicas (scaling reads, cross-region)
  - RDS Proxy (connection pooling for Lambda)
  - Performance Insights
- **Aurora Performance:**
  - Architecture (6 copies, 3 AZs)
  - Up to 15 read replicas with <10ms lag
  - Aurora Serverless v2 (auto-scaling ACUs)
  - Aurora Global Database (<1 second lag)
  - Parallel Query
- **DynamoDB Performance:**
  - Capacity modes (Provisioned vs On-Demand)
  - Read/Write Capacity Units calculation
  - DAX (DynamoDB Accelerator - microseconds)
  - Global Tables
  - Partition key design (avoiding hot partitions)
- **Database Selection Guide**
- **Network Performance:**
  - CloudFront (cache behaviors, Origin Shield)
  - Lambda@Edge vs CloudFront Functions
  - Global Accelerator (TCP/UDP, static IPs)
  - VPC Performance (ENA, EFA, endpoints)

**📌 MASTER DATABASE CACHING AND NETWORK ACCELERATION!**

---

### [60. Performance Domain Part 4: Patterns, Scenarios & Exam Questions](60-Performance-Domain-Part4-Patterns-Scenarios.md)
**⚡ CACHING, ARCHITECTURE PATTERNS & EXAM PRACTICE**

**Contains:**
- **ElastiCache (Complete Reference):**
  - Redis vs Memcached comparison
  - Caching patterns (Lazy Loading, Write-Through)
  - Cluster Mode Disabled vs Enabled
- **4 High-Performance Architecture Patterns:**
  1. Static Website with CloudFront
  2. High-Performance API
  3. High-Performance Analytics
  4. Global High-Performance Application
- **7 Detailed Exam Scenarios:**
  1. Web Application Performance
  2. DynamoDB Performance Issues
  3. Lambda Cold Starts
  4. Global Application Latency
  5. Database Selection
  6. Storage Performance (500K IOPS)
  7. Analytics Performance
- **Performance Instant Answer Table (35+ mappings)**
- **Performance Optimization Checklist:**
  - Compute checklist
  - Storage checklist
  - Database checklist
  - Network checklist
- **Final Exam Tips - Performance Domain**

**📌 PRACTICE THESE PATTERNS FOR PERFORMANCE ARCHITECTURE QUESTIONS!**

---

## 💰 DESIGN COST-OPTIMIZED ARCHITECTURES - DEEP DIVE (20% of Exam)

### [61. Cost Domain Part 1: Compute Pricing & Cost Optimization](61-Cost-Domain-Part1-Compute-Pricing.md)
**⚡ EC2 PRICING MODELS, SAVINGS PLANS, SPOT INSTANCES**

**Contains:**
- **Domain Overview** (20% weight, focus areas)
- **EC2 Pricing Models (Complete Reference):**
  - On-Demand (billing, use cases)
  - Reserved Instances (Standard vs Convertible, payment options, scope)
  - Savings Plans (Compute vs EC2 Instance, comparison with RIs)
  - Spot Instances (strategies, Spot Fleet, allocation strategies)
  - Dedicated Hosts vs Dedicated Instances
- **EC2 Pricing Decision Tree**
- **Reserved Instance Attributes:**
  - What can/cannot be changed
  - Standard vs Convertible capabilities
- **Spot Instance Strategies:**
  - Spot Fleet allocation (lowestPrice, diversified, capacityOptimized)
  - Auto Scaling with mixed instances
  - Use cases and anti-patterns
- **AWS Compute Optimizer:**
  - Supported resources
  - How it works
  - Right-sizing recommendations
- **Serverless Cost Optimization:**
  - Lambda pricing model
  - Lambda cost tips (ARM64, right-size memory)
  - Fargate pricing and Spot

**📌 MASTER EC2 PRICING FOR COST OPTIMIZATION QUESTIONS!**

---

### [62. Cost Domain Part 2: Storage Cost Optimization](62-Cost-Domain-Part2-Storage-Pricing.md)
**⚡ S3 STORAGE CLASSES, EBS, EFS, FSX COSTS**

**Contains:**
- **S3 Storage Classes (Complete Reference):**
  - All 7 storage classes with costs, availability, retrieval
  - Storage class decision tree
  - Glacier retrieval options (Expedited, Standard, Bulk)
- **S3 Intelligent-Tiering:**
  - Automatic tier transitions
  - When to use (and when not)
  - Archive tiers (optional)
- **S3 Lifecycle Policies:**
  - Transition rules and constraints
  - Expiration actions
  - Example policy JSON
- **S3 Cost Components:**
  - Storage, requests, retrieval, data transfer
  - Request pricing by storage class
- **EBS Cost Optimization:**
  - Volume type costs (gp3, gp2, io2, st1, sc1)
  - GP3 vs GP2 cost comparison example
  - Optimization strategies
- **EFS Cost Optimization:**
  - Storage classes (Standard, IA, One Zone)
  - Lifecycle management
- **FSx Cost Tips**

**📌 KNOW ALL STORAGE CLASSES AND WHEN TO USE EACH!**

---

### [63. Cost Domain Part 3: Database & Network Cost Optimization](63-Cost-Domain-Part3-Database-Network.md)
**⚡ RDS, DYNAMODB, ELASTICACHE, DATA TRANSFER COSTS**

**Contains:**
- **RDS Cost Optimization:**
  - Pricing components
  - Reserved Instances (discounts by term and payment)
  - Cost optimization strategies
  - Open source vs commercial engines
- **Aurora Pricing:**
  - Provisioned vs Serverless v2
  - When Serverless is more/less cost-effective
- **DynamoDB Pricing:**
  - Provisioned vs On-Demand comparison
  - Reserved Capacity (up to 75% savings)
  - Standard-IA table class
  - Cost optimization strategies (8 tips)
  - Provisioned vs On-Demand cost example
- **ElastiCache Pricing:**
  - Reserved Nodes
  - Optimization strategies
- **Redshift Pricing:**
  - Provisioned vs Serverless
  - When to use each
- **Data Transfer Costs (Complete Reference):**
  - Ingress, egress, inter-region, inter-AZ
  - VPC Endpoints cost benefits
  - NAT Gateway vs VPC Endpoint example
  - CloudFront cost advantages
- **AWS Cost Management Tools:**
  - Cost Explorer features
  - AWS Budgets types and actions

**📌 UNDERSTAND ALL DATABASE PRICING AND DATA TRANSFER COSTS!**

---

### [64. Cost Domain Part 4: Patterns, Scenarios & Exam Questions](64-Cost-Domain-Part4-Patterns-Scenarios.md)
**⚡ ARCHITECTURE PATTERNS & EXAM PRACTICE**

**Contains:**
- **4 Cost-Optimized Architecture Patterns:**
  1. Cost-Optimized Web Application (mixed instances, caching, endpoints)
  2. Cost-Optimized Data Pipeline (S3 lifecycle, Spot EMR, Parquet)
  3. Cost-Optimized Serverless (HTTP API, ARM Lambda, DynamoDB reserved)
  4. Cost-Optimized Hybrid (Direct Connect, Pilot Light, Glacier)
- **8 Detailed Exam Scenarios:**
  1. E-Commerce Cost Optimization
  2. Data Lake Cost Optimization
  3. Startup Cost Management
  4. Batch Processing
  5. Development Environment
  6. Data Transfer Costs
  7. Archive Requirements
  8. Multi-Account Cost Management
- **Complete Instant Answer Table (50+ mappings)**
- **Cost Optimization Checklist:**
  - Compute, Storage, Database, Network, Management
- **Cost Optimization Rules (6 key rules)**
- **Common Exam Traps**
- **Exam Question Keywords**

**📌 PRACTICE THESE SCENARIOS FOR COST OPTIMIZATION QUESTIONS!**

---

*Last Updated: December 2025*
*AWS Exam Version: SAA-C03*
*GitHub Copilot Exam: GH-300*

