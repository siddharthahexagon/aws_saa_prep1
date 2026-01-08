# AWS Compute Services - Complete Definitions (SAA-C03)

## Table of Contents
- [Amazon EC2](#amazon-ec2)
- [AWS Lambda](#aws-lambda)
- [Amazon ECS](#amazon-ecs)
- [Amazon EKS](#amazon-eks)
- [AWS Fargate](#aws-fargate)
- [AWS Elastic Beanstalk](#aws-elastic-beanstalk)
- [AWS Batch](#aws-batch)
- [AWS Outposts](#aws-outposts)
- [AWS Wavelength](#aws-wavelength)
- [AWS Local Zones](#aws-local-zones)

---

## Amazon EC2 (Elastic Compute Cloud)

**Definition**: Virtual servers in the cloud that provide resizable compute capacity. EC2 allows you to launch and manage virtual machine instances with complete control over computing resources.

**Key Features**:
- Multiple instance types optimized for different use cases
- Pay-as-you-go, Reserved, Spot, and Savings Plans pricing
- Auto Scaling capabilities
- Integration with VPC for network isolation
- EBS-backed and Instance Store storage options
- Placement groups for optimized networking

**Instance Types**:
- **General Purpose** (T3, T3a, M5, M6i): Balanced compute, memory, networking
- **Compute Optimized** (C5, C6i, C7g): High-performance processors
- **Memory Optimized** (R5, R6i, X2, z1d): Fast performance for memory-intensive workloads
- **Storage Optimized** (I3, I4i, D2, D3): High sequential read/write to local storage
- **Accelerated Computing** (P4, G4, F1): Hardware accelerators (GPUs, FPGAs)

**Purchasing Options**:
- **On-Demand**: Pay per hour/second, no commitment
- **Reserved Instances**: 1 or 3-year commitment, up to 75% discount
- **Spot Instances**: Bid for spare capacity, up to 90% discount
- **Savings Plans**: Flexible pricing model with 1 or 3-year commitment
- **Dedicated Hosts**: Physical servers dedicated for your use
- **Dedicated Instances**: Instances on hardware dedicated to single customer

**Use Cases**:
- Web application hosting
- Development and test environments
- High-performance computing (HPC)
- Batch processing
- Gaming servers
- Enterprise applications

**Exam Tips**:
- Choose instance type based on workload characteristics
- Use Auto Scaling for high availability and cost optimization
- Spot Instances for fault-tolerant, flexible workloads
- Reserved Instances for steady-state workloads
- EBS-optimized instances for consistent I/O performance
- Placement groups: Cluster (low latency), Partition (distributed), Spread (critical instances)

---

## AWS Lambda

**Definition**: Serverless compute service that runs code in response to events without provisioning or managing servers. You pay only for the compute time consumed.

**Key Features**:
- Event-driven execution
- Automatic scaling
- Pay-per-request and compute time pricing
- Supports multiple languages (Python, Node.js, Java, Go, .NET, Ruby, custom runtimes)
- Up to 15 minutes execution time per invocation
- Up to 10 GB memory allocation
- Integrated with AWS services for event sources

**Components**:
- **Function**: Code and configuration
- **Event Source**: Services that trigger Lambda (S3, DynamoDB, API Gateway, etc.)
- **Execution Role**: IAM role for permissions
- **Layers**: Reusable code and dependencies
- **Versions & Aliases**: Version management and traffic routing

**Concurrency**:
- Default account limit: 1,000 concurrent executions
- Reserved concurrency: Guarantee capacity for critical functions
- Provisioned concurrency: Pre-warm functions for reduced latency

**Use Cases**:
- Real-time file processing
- Data transformation and ETL
- Backends for mobile/web applications
- IoT data processing
- Scheduled tasks and automation
- Serverless APIs with API Gateway

**Exam Tips**:
- Lambda is ideal for event-driven, short-duration tasks
- Cold start latency for infrequently used functions
- Use provisioned concurrency for latency-sensitive applications
- VPC Lambda functions can access private resources but have cold start overhead
- Maximum execution time: 15 minutes (use Step Functions for longer workflows)
- Best for stateless, idempotent operations

---

## Amazon ECS (Elastic Container Service)

**Definition**: Fully managed container orchestration service that makes it easy to deploy, manage, and scale containerized applications using Docker containers.

**Key Features**:
- Native AWS integration
- Two launch types: EC2 and Fargate
- Task definitions define container configurations
- Service scheduler maintains desired task count
- Integration with ELB for load balancing
- CloudWatch for monitoring

**Components**:
- **Cluster**: Logical grouping of tasks or services
- **Task Definition**: Blueprint for application (JSON format)
- **Task**: Instantiation of task definition
- **Service**: Maintains specified number of running tasks
- **Container Instance**: EC2 instance in ECS cluster

**Launch Types**:
- **EC2 Launch Type**: You manage underlying EC2 instances
- **Fargate Launch Type**: Serverless, AWS manages infrastructure

**Use Cases**:
- Microservices architectures
- Batch processing workloads
- CI/CD pipelines
- Hybrid applications
- Machine learning model deployment

**Exam Tips**:
- Use Fargate for serverless container management
- Use EC2 launch type for more control and cost optimization
- Service Auto Scaling adjusts desired task count
- Integration with ALB for advanced routing
- Use IAM roles for tasks (not instance profiles)
- ECS Anywhere extends ECS to on-premises

---

## Amazon EKS (Elastic Kubernetes Service)

**Definition**: Managed Kubernetes service that makes it easy to run Kubernetes on AWS without needing to install and operate your own Kubernetes control plane.

**Key Features**:
- Fully managed Kubernetes control plane
- Automatic upgrades and patching
- High availability across multiple AZs
- Integration with AWS services (IAM, VPC, ELB, EBS, EFS)
- Support for Fargate for serverless pods
- EKS Anywhere for on-premises deployments

**Components**:
- **Control Plane**: Managed by AWS (API server, etcd, scheduler)
- **Worker Nodes**: EC2 instances or Fargate
- **Pods**: Smallest deployable units
- **Services**: Expose pod networking
- **Namespaces**: Virtual clusters

**Networking**:
- VPC CNI plugin for pod networking
- Each pod gets VPC IP address
- Security groups for pods (SG per pod)
- Network policies for traffic control

**Use Cases**:
- Cloud-native applications
- Multi-cloud Kubernetes deployments
- Microservices with Kubernetes
- ML/AI workloads with Kubernetes
- Hybrid Kubernetes clusters

**Exam Tips**:
- EKS provides managed Kubernetes control plane
- Use Fargate with EKS for serverless pods
- Worker nodes are EC2 instances you manage
- ALB Ingress Controller for load balancing
- IAM roles for service accounts (IRSA) for pod permissions
- More complex than ECS but provides standard Kubernetes API

---

## AWS Fargate

**Definition**: Serverless compute engine for containers that works with both ECS and EKS, eliminating the need to provision and manage servers.

**Key Features**:
- No EC2 instances to manage
- Pay only for resources used by containers
- Automatic scaling
- Built-in security isolation
- Integration with VPC, security groups
- Task-level resource allocation

**Specifications**:
- CPU: 0.25 vCPU to 16 vCPU
- Memory: 0.5 GB to 120 GB
- Ephemeral storage: 20 GB minimum
- Task IAM roles
- Task networking mode: awsvpc (each task gets ENI)

**Use Cases**:
- Microservices
- Batch processing
- CI/CD pipelines
- Event-driven applications
- Web applications

**Exam Tips**:
- Fargate is serverless - no infrastructure management
- More expensive than EC2 launch type but no management overhead
- Best for variable workloads and when you don't want to manage servers
- Each task gets its own ENI and security group
- Cannot use Spot pricing with Fargate
- Storage is ephemeral unless using EFS

---

## AWS Elastic Beanstalk

**Definition**: Platform as a Service (PaaS) that makes it easy to deploy and scale web applications and services. You upload your code, and Elastic Beanstalk handles deployment, capacity provisioning, load balancing, and auto-scaling.

**Key Features**:
- Supports multiple platforms (Java, .NET, PHP, Node.js, Python, Ruby, Go, Docker)
- Automatic capacity provisioning
- Load balancing and auto-scaling
- Application health monitoring
- Managed platform updates
- Full control over underlying resources

**Components**:
- **Application**: Logical collection of components
- **Application Version**: Specific iteration of deployable code
- **Environment**: Collection of AWS resources running an application version
- **Environment Tier**: Web server or worker environment
- **Configuration**: Parameters defining environment behavior

**Deployment Policies**:
- **All at once**: Deploy to all instances simultaneously (downtime)
- **Rolling**: Deploy in batches (reduced capacity during deployment)
- **Rolling with additional batch**: Maintain full capacity during deployment
- **Immutable**: Deploy to new instances, then swap (safest, zero downtime)
- **Traffic splitting**: Canary testing with percentage-based routing
- **Blue/Green**: Deploy to separate environment, then swap URLs

**Use Cases**:
- Quick web application deployment
- Standard web applications
- Developer productivity
- Applications requiring minimal infrastructure management

**Exam Tips**:
- Elastic Beanstalk is free; you pay for underlying resources
- Great for developers who want to focus on code
- Provides full control of resources if needed
- Use immutable deployments for zero downtime
- Supports .ebextensions for advanced configuration
- Can integrate with RDS, ElastiCache, etc.

---

## AWS Batch

**Definition**: Fully managed batch processing service that plans, schedules, and executes batch computing workloads across the full range of AWS compute services (EC2, Spot Instances, Fargate).

**Key Features**:
- Automatic provisioning of compute resources
- Job scheduling and prioritization
- Dynamic scaling based on job queue volume
- Integration with Spot Instances for cost optimization
- No software to install or manage
- Integration with AWS services (S3, DynamoDB, ECS, etc.)

**Components**:
- **Job**: Unit of work submitted to AWS Batch
- **Job Definition**: Specifies how jobs are to be run
- **Job Queue**: Where jobs reside until scheduled
- **Compute Environment**: Managed or unmanaged compute resources
- **Scheduler**: Determines when and where to run jobs

**Compute Types**:
- **Managed**: AWS manages capacity and instance types
- **Unmanaged**: You manage your own compute resources
- **Fargate**: Serverless compute for batch jobs

**Use Cases**:
- Financial services modeling
- Drug discovery and genomics
- Media transcoding
- Image processing
- Log analysis
- Scientific simulations

**Exam Tips**:
- AWS Batch for long-running batch jobs
- Automatically uses Spot Instances for cost savings
- Different from Lambda (15-minute limit) - Batch has no time limit
- Integrates with Step Functions for workflow orchestration
- Multi-node parallel jobs for tightly coupled HPC workloads
- Array jobs for easily parallelizable work

---

## AWS Outposts

**Definition**: Fully managed service that extends AWS infrastructure, services, APIs, and tools to virtually any datacenter, co-location space, or on-premises facility for a consistent hybrid cloud experience.

**Key Features**:
- Same AWS infrastructure as public AWS regions
- Runs native AWS services locally
- Consistent APIs and tools
- Local data processing and storage
- Low-latency access to on-premises systems
- AWS managed and maintained

**Available Services**:
- EC2, EBS, S3 on Outposts
- ECS, EKS
- RDS
- EMR
- VPC, subnets, security groups
- And more

**Configurations**:
- 42U racks
- 1U and 2U servers
- Various compute and storage options

**Use Cases**:
- Low-latency applications
- Local data processing
- Data residency requirements
- Hybrid cloud architectures
- Migration to cloud

**Exam Tips**:
- Outposts brings AWS infrastructure on-premises
- Ideal for data residency and low-latency requirements
- AWS manages hardware, patching, and maintenance
- Connect to parent AWS region for management
- Local data stays local; can sync to AWS region
- Supports VPC extension from AWS region

---

## AWS Wavelength

**Definition**: Brings AWS compute and storage services to the edge of 5G networks, enabling developers to build applications that deliver ultra-low latency to mobile devices and end users.

**Key Features**:
- Deploy applications at 5G edge
- Single-digit millisecond latency
- Wavelength Zones in telecom carrier networks
- Seamless integration with AWS services
- Standard AWS APIs and tools

**Supported Services**:
- EC2 instances
- EBS volumes
- VPC and subnets
- Carrier Gateway for connectivity

**Use Cases**:
- Real-time gaming
- Live video streaming
- AR/VR applications
- ML inference at the edge
- Autonomous vehicles
- IoT applications

**Exam Tips**:
- Wavelength for ultra-low latency mobile applications
- Deployed at telecom 5G edge locations
- Application traffic stays on carrier network
- Different from Local Zones (which are AWS mini-regions)
- Use Carrier Gateway to route traffic between Wavelength and carrier network
- EC2 instances in Wavelength Zones can access parent region services

---

## AWS Local Zones

**Definition**: Type of infrastructure deployment that places AWS compute, storage, database, and other services closer to large population, industry, and IT centers where no AWS Region exists.

**Key Features**:
- Extension of AWS Region
- Single-digit millisecond latency to end users
- Run latency-sensitive applications closer to users
- Seamless connection to parent region
- Use same APIs and tools

**Supported Services**:
- EC2, EBS
- VPC, subnets
- ECS, EKS
- Application Load Balancer
- Direct Connect
- FSx

**Use Cases**:
- Real-time gaming
- Live video streaming
- Media production
- Machine learning inference
- AR/VR applications
- Latency-sensitive applications

**Exam Tips**:
- Local Zones for low-latency access in specific geographies
- Extension of AWS Region, not separate region
- Subnet spans multiple AZs and Local Zones
- Different from Edge Locations (CloudFront CDN)
- Use for applications requiring sub-10ms latency
- Opt-in required for Local Zones

---

## Summary Table

| Service | Type | Management | Use Case |
|---------|------|------------|----------|
| **EC2** | Virtual Machines | Self-managed | General compute, full control |
| **Lambda** | Serverless Functions | Fully managed | Event-driven, short tasks |
| **ECS** | Container Orchestration | Managed orchestration | Docker containers |
| **EKS** | Kubernetes Service | Managed control plane | Kubernetes workloads |
| **Fargate** | Serverless Containers | Fully managed | Containers without servers |
| **Elastic Beanstalk** | PaaS | Fully managed | Quick web app deployment |
| **Batch** | Batch Processing | Fully managed | Long-running batch jobs |
| **Outposts** | Hybrid Infrastructure | AWS-managed hardware | On-premises AWS |
| **Wavelength** | Edge Compute | Managed at 5G edge | Ultra-low latency mobile |
| **Local Zones** | Regional Extension | Managed infrastructure | Low latency in metros |

---

## Exam Focus Areas

1. **Instance Type Selection**: Match workload to appropriate EC2 instance type
2. **Pricing Models**: When to use On-Demand, Reserved, Spot, Savings Plans
3. **Lambda Limits**: 15-minute max execution, memory up to 10 GB
4. **Container Services**: ECS vs EKS vs Fargate comparison
5. **Serverless**: Lambda vs Fargate for different scenarios
6. **Deployment**: Elastic Beanstalk deployment strategies
7. **Batch vs Lambda**: Duration and use case differences
8. **Edge Computing**: Wavelength vs Local Zones vs Outposts
9. **Auto Scaling**: EC2 Auto Scaling vs ECS Service Auto Scaling
10. **High Availability**: Multi-AZ deployments for compute services

---

*Last Updated: January 2026 - SAA-C03 Exam*
