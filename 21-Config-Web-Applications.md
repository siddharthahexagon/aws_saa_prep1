# Configuration Questionnaire: Web Applications
## Step-by-Step Service Selection and Configuration

---

## 📋 Table of Contents
1. [Static Website Configuration](#static-website-configuration)
2. [Dynamic Web Application](#dynamic-web-application)
3. [E-Commerce Platform](#e-commerce-platform)
4. [Media Streaming Application](#media-streaming-application)
5. [Global Web Application](#global-web-application)
6. [Serverless Web Application](#serverless-web-application)

---

## 🌐 SCENARIO 1: Static Website Configuration

### Initial Questions:
```
Q1: Is the website static (HTML/CSS/JS only)?
    └─ YES → Continue
    └─ NO → Go to Dynamic Web Application

Q2: Do you need global distribution?
    └─ YES → CloudFront required
    └─ NO → S3 alone may suffice

Q3: Do you need a custom domain?
    └─ YES → Route 53 required
    └─ NO → Use S3 website endpoint

Q4: Do you need HTTPS?
    └─ YES → ACM + CloudFront required
    └─ NO → HTTP only (not recommended)
```

### Configuration Matrix:

#### **Basic Static Website (Minimal Cost)**
```
Services Required:
├─ S3 (Storage + Static Website Hosting)
└─ Route 53 (Optional - if custom domain)

Configuration Steps:
1. Create S3 bucket (name = domain name if using Route 53)
2. Enable "Static Website Hosting" on bucket
3. Upload HTML/CSS/JS files
4. Set bucket policy for public read access
5. Configure Route 53 A record (Alias) → S3 endpoint

Estimated Cost: $0.023/GB storage + $0.004/10K requests
```

#### **Global Static Website (Best Practice)**
```
Services Required:
├─ S3 (Origin storage)
├─ CloudFront (CDN)
├─ Route 53 (DNS)
├─ ACM (SSL Certificate)
└─ WAF (Optional - security)

Configuration Steps:
1. Create S3 bucket (private, not public)
2. Request SSL certificate in ACM (us-east-1 region)
3. Create CloudFront distribution:
   - Origin: S3 bucket
   - Viewer Protocol Policy: Redirect HTTP to HTTPS
   - Alternate Domain Names: your-domain.com
   - Custom SSL Certificate: Select ACM cert
4. Create Origin Access Identity (OAI)
5. Update S3 bucket policy to allow OAI only
6. Configure Route 53:
   - A record (Alias) → CloudFront distribution
7. (Optional) Attach WAF to CloudFront

High Availability: ✅ (CloudFront multi-region)
Security: ✅ (HTTPS, private S3, OAI)
Performance: ✅ (Edge caching)
Cost: ~$1-5/month for small sites
```

#### **Static Website with CI/CD**
```
Services Required:
├─ S3 (Storage)
├─ CloudFront (CDN)
├─ Route 53 (DNS)
├─ ACM (SSL)
├─ CodePipeline (CI/CD)
├─ CodeBuild (Build process)
└─ GitHub/CodeCommit (Source control)

Configuration Steps:
1-7. Same as Global Static Website above
8. Create CodeBuild project:
   - Build commands: npm install, npm run build
   - Output artifacts to S3
9. Create CodePipeline:
   - Source: GitHub/CodeCommit
   - Build: CodeBuild
   - Deploy: S3 bucket
10. Add CloudFront invalidation after deployment

Best For: Professional websites with frequent updates
```

---

## 🖥️ SCENARIO 2: Dynamic Web Application

### Initial Questions:
```
Q1: What is the expected traffic?
    ├─ Low (<100 users) → Single EC2 or Lightsail
    ├─ Medium (100-10K users) → Auto Scaling Group
    └─ High (>10K users) → Multi-AZ ASG + caching

Q2: Do you need a database?
    └─ YES → What type?
        ├─ Relational → RDS
        ├─ NoSQL → DynamoDB
        └─ In-memory → ElastiCache

Q3: What level of availability do you need?
    ├─ Basic → Single AZ
    ├─ High → Multi-AZ
    └─ Critical → Multi-Region

Q4: Do you need session management?
    └─ YES → ElastiCache or DynamoDB
```

### Configuration Matrix:

#### **Small Web Application (Single AZ)**
```
Services Required:
├─ EC2 (Web server)
├─ RDS (Database)
├─ Elastic IP (Static IP)
├─ Security Groups (Firewall)
└─ Route 53 (DNS)

Configuration Steps:
1. Create VPC with public/private subnets
2. Launch EC2 instance in public subnet:
   - AMI: Amazon Linux 2 or Ubuntu
   - Instance Type: t3.small (start small)
   - User Data: Install web server (Apache/Nginx)
   - Security Group: Allow 80, 443, 22
3. Allocate and associate Elastic IP
4. Launch RDS instance in private subnet:
   - Engine: MySQL/PostgreSQL
   - Instance Type: db.t3.micro
   - Multi-AZ: NO (for cost)
   - Security Group: Allow 3306/5432 from EC2 SG only
5. Configure EC2 to connect to RDS
6. Route 53 A record → Elastic IP

Cost: ~$20-50/month
Availability: 95-99% (single point of failure)
Use Case: Development, testing, small internal apps
```

#### **Medium Web Application (High Availability)**
```
Services Required:
├─ ALB (Application Load Balancer)
├─ Auto Scaling Group (EC2 instances)
├─ RDS Multi-AZ (Database)
├─ ElastiCache (Session/cache)
├─ S3 (Static assets)
├─ CloudFront (CDN for assets)
├─ Route 53 (DNS)
├─ ACM (SSL Certificate)
└─ CloudWatch (Monitoring)

Configuration Steps:
1. VPC Setup:
   - Create VPC with 2 AZs
   - Public subnets (2) for ALB
   - Private subnets (2) for EC2
   - Private subnets (2) for RDS
   - Internet Gateway + NAT Gateway

2. Database Layer:
   - Launch RDS (MySQL/PostgreSQL)
   - Multi-AZ: YES
   - Instance Type: db.t3.small
   - Automated backups: Enabled (7 days)
   - Security Group: Port 3306/5432 from app tier

3. Cache Layer:
   - Launch ElastiCache Redis cluster
   - Node Type: cache.t3.micro
   - Multi-AZ: YES
   - For session storage and database caching
   - Security Group: Port 6379 from app tier

4. Application Layer:
   - Create Launch Template:
     • AMI: Amazon Linux 2 with app installed
     • Instance Type: t3.medium
     • User Data: Application startup script
     • IAM Role: Access to S3, CloudWatch
   - Create Auto Scaling Group:
     • Min: 2, Max: 10, Desired: 2
     • Subnets: Private app subnets (both AZs)
     • Health Check: EC2 + ELB
   - Scaling Policies:
     • Target Tracking: 70% CPU utilization

5. Load Balancer:
   - Create ALB:
     • Scheme: Internet-facing
     • Subnets: Public subnets (both AZs)
     • Security Group: Allow 80, 443 from internet
     • SSL Certificate: From ACM
   - Create Target Group:
     • Protocol: HTTP (80)
     • Health Check: /health endpoint
     • Deregistration Delay: 30 seconds
   - Attach ASG to Target Group

6. Static Content:
   - Create S3 bucket for assets (images, CSS, JS)
   - Create CloudFront distribution:
     • Origin: S3 bucket
     • Cache Policy: CachingOptimized
   - Configure app to serve static content from CloudFront

7. DNS:
   - Request SSL certificate in ACM
   - Route 53 A record (Alias) → ALB

8. Monitoring:
   - CloudWatch alarms:
     • High CPU on EC2
     • High database connections
     • ALB 5xx errors
     • Auto Scaling events

Architecture Diagram:
Internet → Route 53 → CloudFront (static) + ALB (dynamic)
                          ↓
                    Auto Scaling Group (EC2)
                          ↓
          ElastiCache ← → RDS Multi-AZ

High Availability: ✅ Multi-AZ for all layers
Auto Scaling: ✅ Based on demand
Security: ✅ Private subnets, security groups
Cost: ~$200-500/month
Use Case: Production web apps, SaaS platforms
```

#### **Large-Scale Web Application (Enterprise)**
```
Services Required:
├─ Route 53 (DNS with health checks)
├─ CloudFront (Global CDN)
├─ WAF (Web Application Firewall)
├─ ALB (In multiple regions)
├─ Auto Scaling Groups (Multi-region)
├─ Aurora Global Database
├─ ElastiCache Global Datastore
├─ S3 + Cross-Region Replication
├─ CloudWatch + X-Ray (Monitoring)
├─ Systems Manager (Configuration)
└─ Secrets Manager (Credentials)

Configuration Steps:
[Primary Region - us-east-1]
1. VPC: 3 AZs, public/private/data subnets
2. Aurora Database Cluster:
   - Engine: Aurora MySQL/PostgreSQL
   - Instance: db.r5.xlarge
   - Multi-AZ: 3 instances across AZs
   - Read replicas: 2-3 for scaling
   - Automated backups + point-in-time recovery
3. ElastiCache Redis Cluster:
   - Cluster Mode Enabled
   - Multi-AZ: YES
   - Automatic failover
4. Auto Scaling Group:
   - Min: 6, Max: 50
   - Instance Type: c5.xlarge
   - Distributed across 3 AZs
5. ALB with SSL termination
6. CloudFront distribution with WAF

[Secondary Region - eu-west-1]
7. Replicate infrastructure
8. Aurora Global Database (replication)
9. ElastiCache Global Datastore
10. S3 Cross-Region Replication

[Global Configuration]
11. Route 53:
    - Geolocation routing (serve from nearest region)
    - Health checks on ALBs
    - Failover to secondary region
12. WAF Rules:
    - SQL injection protection
    - XSS protection
    - Rate limiting
13. Secrets Manager:
    - Automatic rotation for database credentials
14. CloudWatch:
    - Custom metrics
    - Log aggregation from all regions
15. X-Ray:
    - Distributed tracing

High Availability: 99.99%+ (multi-region)
Auto Scaling: ✅ Advanced with predictive scaling
Security: ✅ Enterprise-grade with WAF, encryption
Global: ✅ Low latency worldwide
Cost: $2,000-10,000+/month
Use Case: Global SaaS, large e-commerce, fintech
```

---

## 🛒 SCENARIO 3: E-Commerce Platform

### Initial Questions:
```
Q1: What is the expected transaction volume?
    ├─ Low (<1000/day) → Standard web app config
    ├─ Medium (1K-100K/day) → Caching + queues required
    └─ High (>100K/day) → Multi-region + specialized services

Q2: Do you need real-time inventory?
    └─ YES → DynamoDB with streams

Q3: Do you need payment processing?
    └─ YES → Integrate with payment gateway (PCI-DSS)

Q4: Do you need search functionality?
    └─ YES → OpenSearch Service
```

### Configuration Matrix:

#### **Complete E-Commerce Platform**
```
Services Required:
Frontend:
├─ CloudFront (CDN)
├─ S3 (Static assets)
├─ ALB (Load balancer)
└─ Auto Scaling Group (Web tier)

Application:
├─ ECS/EKS (Microservices)
├─ API Gateway (RESTful APIs)
├─ Lambda (Serverless functions)
└─ SQS (Order queue)

Data:
├─ Aurora (Transactional data)
├─ DynamoDB (Shopping cart, sessions)
├─ ElastiCache (Product catalog cache)
├─ OpenSearch (Product search)
└─ S3 (Product images)

Analytics:
├─ Kinesis (Clickstream)
├─ Redshift (Data warehouse)
└─ QuickSight (Business intelligence)

Security:
├─ WAF (Protection)
├─ Cognito (User authentication)
├─ KMS (Encryption)
└─ Secrets Manager (API keys)

Configuration Steps:

[Layer 1: Frontend]
1. CloudFront distribution:
   - Origin: ALB for dynamic content
   - Origin: S3 for static assets
   - Caching: Optimize for images/CSS/JS
   - WAF: Enable with common protection rules

[Layer 2: Web/API Tier]
2. ECS Cluster with Fargate:
   - Frontend Service (React/Vue)
   - Backend API Service (Node.js/Python)
   - Auto Scaling based on CPU/memory
3. API Gateway:
   - RESTful endpoints
   - Throttling: 1000 req/sec
   - API keys for partners
   - Lambda authorizer with Cognito

[Layer 3: Microservices]
4. Product Service → Aurora + ElastiCache
5. Cart Service → DynamoDB (sessions)
6. Order Service → Aurora + SQS
7. Payment Service → Lambda + Secrets Manager
8. Inventory Service → DynamoDB Streams
9. Notification Service → SNS + SES

[Layer 4: Data Layer]
10. Aurora MySQL Cluster:
    - Orders, Users, Products (relational)
    - Multi-AZ, 2 read replicas
    - Automated backups
11. DynamoDB Tables:
    - ShoppingCarts (TTL enabled)
    - Sessions (TTL enabled)
    - Inventory (Real-time updates)
    - On-Demand capacity mode
12. ElastiCache Redis:
    - Product catalog cache
    - User session cache
    - Cache aside pattern

[Layer 5: Search]
13. OpenSearch Service:
    - Product catalog indexing
    - Full-text search
    - Faceted search (filters)
    - Multi-AZ deployment

[Layer 6: Async Processing]
14. SQS Queues:
    - Order processing queue
    - Email notification queue
    - Inventory update queue
15. Lambda functions to process queues
16. Dead Letter Queue (DLQ) for failed messages

[Layer 7: Analytics]
17. Kinesis Data Stream:
    - Capture clickstream data
    - User behavior tracking
18. Kinesis Firehose:
    - Load to S3 for storage
    - Load to Redshift for analytics
19. QuickSight dashboards:
    - Sales metrics
    - User behavior
    - Inventory turnover

[Layer 8: Security & Compliance]
20. Cognito User Pools:
    - User registration/login
    - MFA support
    - Social login (Google, Facebook)
21. WAF Rules:
    - Rate limiting per IP
    - SQL injection protection
    - Bot protection
22. KMS Keys:
    - Encrypt data at rest
    - Separate keys per service
23. Secrets Manager:
    - Payment gateway credentials
    - Database passwords
    - API keys

[Layer 9: Monitoring]
24. CloudWatch:
    - Custom metrics for orders/min
    - Alarms for critical errors
    - Log aggregation
25. X-Ray:
    - Trace requests across microservices
    - Identify bottlenecks

Architecture Flow:
User → CloudFront/WAF → ALB → ECS Services
                              ↓
              API Gateway → Lambda Functions
                              ↓
        DynamoDB ← → ElastiCache ← → Aurora
                              ↓
                            SQS → Lambda
                              ↓
                      Kinesis → S3/Redshift

High Availability: 99.99%
Security: PCI-DSS compliant architecture
Scalability: Auto-scales all tiers
Cost: $1,000-5,000+/month (depends on traffic)
```

---

## 🎥 SCENARIO 4: Media Streaming Application

### Configuration Matrix:

#### **Video Streaming Platform**
```
Services Required:
├─ S3 (Video storage)
├─ CloudFront (Video delivery)
├─ MediaConvert (Transcoding)
├─ MediaPackage (Video packaging)
├─ MediaLive (Live streaming)
├─ Elastic Transcoder (Alternative)
├─ Lambda (Automation)
└─ DynamoDB (Metadata)

Configuration Steps:

[Upload & Storage]
1. S3 Bucket for source videos:
   - Lifecycle policy to Glacier after 90 days
   - Server-side encryption (SSE-S3)
   - Versioning enabled

[Transcoding Pipeline]
2. Lambda triggered on S3 upload:
   - Invoke MediaConvert job
   - Create multiple formats (1080p, 720p, 480p)
   - Output to processed S3 bucket
3. MediaConvert Job Templates:
   - HLS format for adaptive streaming
   - Different bitrates for quality
   - Thumbnail generation

[Content Delivery]
4. CloudFront Distribution:
   - Origin: S3 bucket (processed videos)
   - Signed URLs/Cookies for access control
   - Custom error pages
   - Geo-restriction (if needed)
5. CloudFront Origin Shield (optional):
   - Additional caching layer
   - Reduced origin load

[Live Streaming]
6. MediaLive:
   - Input: RTMP/HLS from encoder
   - Output: HLS streams
7. MediaPackage:
   - Package for different devices
   - DRM integration
   - Time-shifted viewing

[Metadata & Search]
8. DynamoDB:
   - Video metadata (title, description, tags)
   - User watch history
   - Playlists
9. OpenSearch (optional):
   - Full-text search of videos

[User Management]
10. Cognito:
    - User authentication
    - Subscription tiers
11. API Gateway + Lambda:
    - Video catalog API
    - Playback tracking API

Cost Optimization:
- Use S3 Intelligent-Tiering
- CloudFront reserved capacity
- Spot instances for transcoding
```

---

## 🌍 SCENARIO 5: Global Web Application

### Configuration Matrix:

#### **Multi-Region Active-Active**
```
Services Required:
├─ Route 53 (Global DNS)
├─ CloudFront (Global CDN)
├─ ALB (per region)
├─ Aurora Global Database
├─ DynamoDB Global Tables
├─ S3 Cross-Region Replication
└─ Global Accelerator (optional)

Configuration:

[Region 1: us-east-1]
1. Full application stack (as in Scenario 2)
2. Aurora Primary Cluster
3. DynamoDB Global Table (replica 1)

[Region 2: eu-west-1]
4. Replicate application stack
5. Aurora Secondary Cluster (read replicas)
6. DynamoDB Global Table (replica 2)

[Region 3: ap-southeast-1]
7. Replicate application stack
8. Aurora Secondary Cluster (read replicas)
9. DynamoDB Global Table (replica 3)

[Global Routing]
10. Route 53:
    - Geolocation routing policy
    - Health checks on each region
    - Automatic failover
11. CloudFront:
    - Origin groups for failover
    - Multiple regional origins

RTO: < 1 minute
RPO: < 1 second
Availability: 99.99%+
```

---

## 🔧 SCENARIO 6: Serverless Web Application

### Configuration Matrix:

#### **Complete Serverless Stack**
```
Services Required:
├─ S3 (Frontend hosting)
├─ CloudFront (CDN)
├─ API Gateway (REST API)
├─ Lambda (Backend logic)
├─ DynamoDB (Database)
├─ Cognito (Authentication)
├─ SES (Email)
└─ CloudWatch (Monitoring)

Configuration Steps:

1. Frontend:
   - S3 bucket with static website
   - React/Vue/Angular SPA
   - CloudFront distribution

2. API Layer:
   - API Gateway REST API
   - Lambda authorizer with Cognito
   - CORS enabled
   - Throttling configured

3. Backend Functions:
   - Lambda functions (Node.js/Python)
   - Environment variables from Parameter Store
   - IAM roles with least privilege
   - VPC configuration (if needed for RDS)

4. Database:
   - DynamoDB tables
   - On-Demand capacity or Auto Scaling
   - Point-in-time recovery enabled
   - DynamoDB Streams for change capture

5. Authentication:
   - Cognito User Pool
   - App client configuration
   - MFA optional

6. Monitoring:
   - CloudWatch Logs for Lambda
   - X-Ray for tracing
   - Custom metrics

Advantages:
✅ No server management
✅ Pay only for what you use
✅ Auto-scaling built-in
✅ High availability by default

Cost: $5-50/month for small apps
Limitations: Cold starts, execution time limits
```

---

## 📊 Quick Comparison Table

| **Scenario** | **HA** | **Cost/Month** | **Complexity** | **Best For** |
|--------------|--------|----------------|----------------|--------------|
| Static Website | High | $1-5 | Low | Blogs, portfolios |
| Small Dynamic | Medium | $20-50 | Low | Internal apps, MVPs |
| Medium HA | High | $200-500 | Medium | Production apps |
| Enterprise | Very High | $2K-10K+ | High | Large SaaS |
| E-Commerce | Very High | $1K-5K | High | Online stores |
| Media Streaming | High | $500-2K | Medium | Video platforms |
| Global Multi-Region | Very High | $5K-20K+ | Very High | Global services |
| Serverless | High | $5-50 | Low | Small apps, APIs |

---

**Next**: See `22-Config-Data-Analytics.md` for data and analytics scenarios
