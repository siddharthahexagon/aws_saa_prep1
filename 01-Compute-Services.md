# AWS SAA-C03: Compute Services Cheat Sheet

## 📋 Table of Contents
- [EC2 (Elastic Compute Cloud)](#ec2)
- [Lambda](#lambda)
- [Elastic Beanstalk](#elastic-beanstalk)
- [ECS (Elastic Container Service)](#ecs)
- [EKS (Elastic Kubernetes Service)](#eks)
- [Fargate](#fargate)
- [Batch](#batch)
- [Lightsail](#lightsail)

---

## EC2 (Elastic Compute Cloud)

### Definition
Virtual servers in the cloud that provide scalable computing capacity.

### Key Features
- **Instance Types**: General Purpose (T3, M5), Compute Optimized (C5), Memory Optimized (R5, X1), Storage Optimized (I3, D2), Accelerated Computing (P3, G4)
- **Pricing Models**: On-Demand, Reserved (1-3 years), Spot (up to 90% discount), Dedicated Hosts, Savings Plans
- **AMI (Amazon Machine Images)**: Pre-configured templates for instances
- **User Data**: Bootstrap scripts that run at instance launch
- **Metadata**: Instance information accessible from http://169.254.169.254/latest/meta-data/
- **Placement Groups**: 
  - **Cluster**: Low latency, high throughput (same AZ)
  - **Spread**: Distinct hardware (max 7 instances per AZ)
  - **Partition**: Distributed across partitions (for Hadoop, Cassandra)

### Exam Tips & Heuristics
✅ **When to Use EC2:**
- Need full control over OS and applications
- Require custom configurations
- Long-running applications
- Lift-and-shift migrations

⚠️ **Common Pitfalls:**
- Spot instances can be terminated with 2-minute notice
- Reserved Instances are regional by default (can enable AZ flexibility)
- T2/T3 unlimited can incur extra charges if credits depleted

🎯 **Exam Heuristics:**
- "Cost optimization" + "interruptible workload" = **Spot Instances**
- "Predictable usage" + "1-3 years" = **Reserved Instances**
- "Short-term, unpredictable" = **On-Demand**
- "Big Data, HPC, low latency" = **Placement Groups (Cluster)**
- "High availability across hardware" = **Spread Placement Group**

### Instance Store vs EBS
| Feature | Instance Store | EBS |
|---------|---------------|-----|
| Persistence | Ephemeral (lost on stop) | Persistent |
| Performance | Higher IOPS | Good, varies by type |
| Use Case | Temporary data, cache | Root volumes, databases |

---

## Lambda

### Definition
Serverless compute service that runs code in response to events without provisioning servers.

### Key Features
- **Execution Limits**: 15 min timeout, 10GB RAM max, 512MB-10GB /tmp storage
- **Pricing**: Per request + compute time (GB-seconds)
- **Triggers**: S3, DynamoDB, Kinesis, API Gateway, EventBridge, SQS, SNS, etc.
- **Concurrency**: Default 1000 concurrent executions per region (soft limit)
- **Environment Variables**: Store configuration
- **Layers**: Share code and dependencies across functions
- **Versions & Aliases**: Manage different function versions
- **VPC Integration**: Access private resources (adds cold start time)
- **Provisioned Concurrency**: Pre-warm instances to avoid cold starts

### Exam Tips & Heuristics
✅ **When to Use Lambda:**
- Event-driven architectures
- Short-running tasks (<15 minutes)
- Variable or unpredictable workload
- Want to minimize operational overhead

⚠️ **Common Pitfalls:**
- Cold start latency (first invocation)
- 15-minute max execution time
- VPC Lambda needs NAT for internet access
- Concurrent execution limits can cause throttling

🎯 **Exam Heuristics:**
- "Event-driven" + "serverless" = **Lambda**
- "15 minutes not enough" = Use **ECS/Fargate** or **Step Functions** (for orchestration)
- "Process S3 uploads" = **Lambda + S3 trigger**
- "Real-time stream processing" = **Lambda + Kinesis/DynamoDB Streams**
- "Cold start issues" = **Provisioned Concurrency** or consider alternatives
- "Share code between functions" = **Lambda Layers**

### Lambda@Edge vs CloudFront Functions
| Feature | Lambda@Edge | CloudFront Functions |
|---------|-------------|---------------------|
| Runtime | Node.js, Python | JavaScript |
| Max Duration | 5-30 seconds | <1 millisecond |
| Use Case | Complex logic | Lightweight transformations |
| Price | Higher | Very low cost |

---

## Elastic Beanstalk

### Definition
Platform as a Service (PaaS) for deploying and scaling web applications without managing infrastructure.

### Key Features
- **Supported Platforms**: Java, .NET, PHP, Node.js, Python, Ruby, Go, Docker
- **Components**: Application, Environment (Web Server/Worker), Configuration
- **Deployment Options**: All at once, Rolling, Rolling with batch, Immutable, Blue/Green
- **Auto Scaling**: Built-in based on metrics
- **Load Balancing**: ALB, NLB, or Classic LB
- **RDS Integration**: Can create and manage RDS instances
- **EB CLI**: Command-line interface for deployment

### Exam Tips & Heuristics
✅ **When to Use Elastic Beanstalk:**
- Quick deployment of web applications
- Don't want to manage infrastructure
- Standard application architectures
- Developer-focused deployments

⚠️ **Common Pitfalls:**
- RDS created by EB is tied to environment lifecycle (use external RDS for production)
- Limited customization compared to direct EC2/ECS
- Can still access underlying resources (EC2, ELB, etc.)

🎯 **Exam Heuristics:**
- "Deploy quickly" + "don't manage infrastructure" = **Elastic Beanstalk**
- "Zero downtime deployment" = **Immutable** or **Blue/Green**
- "Fast deployment, some downtime OK" = **All at once**
- "Database should survive environment deletion" = **External RDS** (not EB-managed)
- "Need more control than EB" = Use **ECS** or **EC2** directly

### Deployment Strategies
| Strategy | Downtime | Rollback | Production Ready |
|----------|----------|----------|------------------|
| All at once | Yes | Manual redeploy | Dev/Test |
| Rolling | No (reduced capacity) | Manual redeploy | OK |
| Rolling with batch | No (extra capacity) | Manual redeploy | Better |
| Immutable | No (full capacity) | Terminate new | Best |
| Blue/Green | No | Swap URL | Best |

---

## ECS (Elastic Container Service)

### Definition
Fully managed container orchestration service for Docker containers.

### Key Features
- **Launch Types**: EC2 (manage instances) or Fargate (serverless)
- **Task Definitions**: Blueprint for containers (image, CPU, memory, ports, env vars)
- **Services**: Maintain desired number of tasks, integrate with ELB
- **Clusters**: Logical grouping of tasks or services
- **Task Placement Strategies**: Binpack, Random, Spread
- **IAM Roles**: Task Role (for containers), Task Execution Role (for ECS agent)
- **Networking Modes**: Bridge, Host, awsvpc, None
- **Service Discovery**: Cloud Map integration
- **Load Balancing**: ALB, NLB integration

### Exam Tips & Heuristics
✅ **When to Use ECS:**
- Run Docker containers
- Need more control than Beanstalk
- Microservices architecture
- Long-running containers

⚠️ **Common Pitfalls:**
- EC2 launch type requires managing EC2 instances
- Task IAM roles require awsvpc network mode for EC2 launch type
- Security groups on tasks require awsvpc mode
- Container insights for monitoring costs extra

🎯 **Exam Heuristics:**
- "Docker containers" + "AWS native" = **ECS**
- "Manage infrastructure" = **ECS on EC2**
- "Serverless containers" = **ECS on Fargate**
- "Container needs IAM role" = **Task Role** (not instance role)
- "Dynamic port mapping" = **ALB** (not CLB)
- "Cost optimization for containers" = **Fargate Spot** or **ECS on EC2 Spot**

---

## EKS (Elastic Kubernetes Service)

### Definition
Managed Kubernetes service to run Kubernetes on AWS without managing control plane.

### Key Features
- **Kubernetes Compatibility**: Standard K8s API
- **Node Types**: Managed nodes, Self-managed nodes, Fargate
- **Networking**: VPC CNI plugin for pod networking
- **IAM Integration**: IAM roles for service accounts (IRSA)
- **Logging**: CloudWatch, CloudTrail integration
- **Add-ons**: CoreDNS, kube-proxy, VPC CNI
- **Multi-cluster**: Supports multiple clusters in different regions

### Exam Tips & Heuristics
✅ **When to Use EKS:**
- Already using Kubernetes
- Need Kubernetes-specific features
- Multi-cloud strategy (portability)
- Complex orchestration requirements

⚠️ **Common Pitfalls:**
- More expensive than ECS
- More complex to operate
- Need Kubernetes expertise
- Control plane costs (~$0.10/hour per cluster)

🎯 **Exam Heuristics:**
- "Kubernetes required" = **EKS**
- "Existing K8s workload migration" = **EKS**
- "AWS-native containers, no K8s requirement" = **ECS** (cheaper, simpler)
- "Hybrid cloud with Kubernetes" = **EKS Anywhere** or **EKS**
- "Serverless K8s pods" = **EKS on Fargate**

---

## Fargate

### Definition
Serverless compute engine for containers (works with ECS and EKS).

### Key Features
- **No Infrastructure Management**: AWS manages servers
- **Task-level Isolation**: Each task has its own kernel
- **Pricing**: Pay per vCPU and memory per second
- **Networking**: awsvpc mode only (each task gets ENI)
- **Fargate Spot**: Up to 70% discount for interruptible workloads
- **Platform Versions**: Control Fargate runtime environment
- **Integration**: Works with ECS and EKS

### Exam Tips & Heuristics
✅ **When to Use Fargate:**
- Don't want to manage EC2 instances
- Variable workload
- Small to medium scale
- Quick deployment

⚠️ **Common Pitfalls:**
- More expensive per hour than EC2 at scale
- Limited instance types/sizes compared to EC2
- Can't use host mode networking
- No SSH access to underlying host
- Longer startup time than warm EC2 instances

🎯 **Exam Heuristics:**
- "Serverless containers" = **Fargate**
- "Don't manage servers" = **Fargate**
- "Large scale, cost-sensitive" = **ECS on EC2** (more cost-effective at scale)
- "Need GPU or specific instance types" = **ECS on EC2**
- "Batch jobs, can be interrupted" = **Fargate Spot**

---

## AWS Batch

### Definition
Fully managed batch processing service at any scale.

### Key Features
- **Compute Environments**: Managed or Unmanaged EC2, Fargate
- **Job Queues**: Priority-based job scheduling
- **Job Definitions**: Specify how jobs should run
- **Spot Instance Support**: Cost optimization for batch jobs
- **Array Jobs**: Submit multiple related jobs
- **Depends On**: Job dependencies
- **Multi-node Parallel Jobs**: HPC workloads
- **Scheduling**: Integrates with EventBridge for scheduled jobs

### Exam Tips & Heuristics
✅ **When to Use Batch:**
- Long-running batch jobs
- Need job scheduling and dependencies
- HPC workloads
- Cost optimization with Spot instances

⚠️ **Common Pitfalls:**
- Not for real-time processing
- Requires containerized applications
- Job scheduling adds complexity

🎯 **Exam Heuristics:**
- "Batch processing" + "job dependencies" = **AWS Batch**
- "Scheduled batch jobs" = **AWS Batch + EventBridge**
- "HPC, scientific computing" = **AWS Batch** (multi-node parallel)
- "Image/video processing at scale" = **AWS Batch**
- "Simple scheduled task" = **Lambda + EventBridge** (simpler)
- "Long-running batch (>15 min)" = **AWS Batch** (not Lambda)

---

## Lightsail

### Definition
Simplified VPS (Virtual Private Server) service with predictable pricing.

### Key Features
- **Pre-configured Blueprints**: WordPress, LAMP, Node.js, etc.
- **Fixed Pricing**: Bundled compute, storage, and data transfer
- **Simplified Management**: Easy-to-use console
- **Load Balancers**: Simple load balancing
- **DNS Management**: Built-in DNS management
- **Snapshots**: Manual or automatic backups
- **VPC Peering**: Connect to VPC resources

### Exam Tips & Heuristics
✅ **When to Use Lightsail:**
- Simple web applications
- Development/test environments
- Small-scale projects
- Predictable monthly costs
- Less AWS experience

⚠️ **Common Pitfalls:**
- Limited scalability compared to EC2
- Fewer features than full AWS services
- Not suitable for enterprise workloads
- Limited integration with other AWS services

🎯 **Exam Heuristics:**
- "Simple web application" + "predictable cost" = **Lightsail**
- "Small business website" = **Lightsail**
- "Need advanced features" = **EC2/ECS/EKS**
- "Rapid prototyping" = **Lightsail**
- "Small scale WordPress" = **Lightsail**
- "Enterprise workload" = Use other compute services (not Lightsail)

---

## 🎓 General Compute Service Selection Guide

| Requirement | Service |
|------------|---------|
| Full control, custom configuration | EC2 |
| Serverless, event-driven, <15 min | Lambda |
| Quick web app deployment, PaaS | Elastic Beanstalk |
| Docker containers, AWS-native | ECS |
| Kubernetes required | EKS |
| Serverless containers | Fargate |
| Batch processing, job scheduling | AWS Batch |
| Simple VPS, predictable pricing | Lightsail |

---

## 💡 Key Exam Patterns

### Cost Optimization Patterns
- Interruptible workloads → Spot Instances/Fargate Spot
- Predictable usage → Reserved Instances/Savings Plans
- Variable workloads → Lambda/Fargate
- Consistent, long-term → Reserved Instances

### High Availability Patterns
- Multi-AZ deployment with Auto Scaling
- ELB for load distribution
- Spread placement groups for critical apps
- Container services with service mesh

### Security Patterns
- IAM roles for EC2/ECS tasks (not access keys)
- Security groups for fine-grained control
- VPC for network isolation
- Encryption at rest and in transit

