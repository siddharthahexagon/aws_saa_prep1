# AWS SAA-C03: Networking & Content Delivery Cheat Sheet

## 📋 Table of Contents
- [VPC (Virtual Private Cloud)](#vpc)
- [Route 53](#route-53)
- [CloudFront](#cloudfront)
- [API Gateway](#api-gateway)
- [Direct Connect](#direct-connect)
- [Global Accelerator](#global-accelerator)
- [Transit Gateway](#transit-gateway)
- [VPN](#vpn)
- [Load Balancers](#load-balancers)

---

## VPC (Virtual Private Cloud)

### Definition
Logically isolated virtual network in AWS where you can launch resources.

### Key Features
- **CIDR Blocks**: IPv4 (primary + secondary), IPv6 optional
- **Subnets**: Public (internet-routable) and Private (internal only)
  - Public: Has route to Internet Gateway (IGW)
  - Private: No direct internet access
- **Route Tables**: Control traffic routing
- **Internet Gateway (IGW)**: Allow internet access for public subnets
- **NAT Gateway**: Allow private subnets to access internet (outbound only)
  - Deployed in public subnet
  - High availability: Deploy one per AZ
  - Charged per hour + data processed
- **NAT Instance**: EC2 instance acting as NAT (legacy, use NAT Gateway)
- **Security Groups**: Stateful firewall at instance level
  - Allow rules only
  - Default: Deny all inbound, allow all outbound
- **Network ACLs (NACLs)**: Stateless firewall at subnet level
  - Allow and deny rules
  - Rule numbers (lowest processed first)
  - Default: Allow all inbound/outbound
- **VPC Peering**: Connect two VPCs privately
  - Not transitive
  - No overlapping CIDR blocks
  - Cross-region and cross-account supported
- **VPC Endpoints**:
  - **Gateway Endpoints**: S3, DynamoDB (free)
  - **Interface Endpoints**: Powered by PrivateLink (most services)
- **Flow Logs**: Capture IP traffic information
  - VPC, Subnet, or ENI level
  - Send to CloudWatch Logs or S3
- **VPC Sharing**: Share subnets across AWS accounts
- **DHCP Option Sets**: Configure DNS, NTP for VPC

### Exam Tips & Heuristics
✅ **When to Use VPC:**
- Isolate resources
- Multi-tier applications
- Hybrid cloud connectivity
- Custom network configuration

⚠️ **Common Pitfalls:**
- Security groups are stateful, NACLs are stateless
- NAT Gateway/Instance must be in public subnet
- VPC peering is not transitive
- Default VPC allows internet access (be careful)
- Subnets cannot span AZs

🎯 **Exam Heuristics:**
- "Private network" = **VPC**
- "Private subnet needs internet" = **NAT Gateway** (in public subnet)
- "Connect two VPCs" = **VPC Peering** (non-transitive) or **Transit Gateway** (transitive)
- "Access S3 without internet" = **VPC Gateway Endpoint**
- "Access AWS services privately" = **VPC Interface Endpoint (PrivateLink)**
- "Stateful firewall" = **Security Group**
- "Stateless firewall" = **NACL**
- "Block specific IP" = **NACL** (security groups only allow)
- "Capture traffic logs" = **VPC Flow Logs**
- "High availability NAT" = **NAT Gateway per AZ**
- "Cost-effective NAT" = **NAT Instance** (but prefer NAT Gateway)

### Security Group vs NACL
| Feature | Security Group | NACL |
|---------|---------------|------|
| Level | Instance | Subnet |
| State | Stateful | Stateless |
| Rules | Allow only | Allow & Deny |
| Rule Processing | All rules evaluated | Rules processed in order |
| Default | Deny all inbound | Allow all |

### VPC Endpoint Types
| Type | Services | Cost | Use Case |
|------|----------|------|----------|
| Gateway | S3, DynamoDB | Free | Best for S3/DynamoDB |
| Interface (PrivateLink) | Most AWS services | Paid | Private connectivity |

---

## Route 53

### Definition
Highly available and scalable DNS web service with domain registration and health checking.

### Key Features
- **Hosted Zones**: Container for DNS records
  - **Public**: Internet-facing domains
  - **Private**: VPC-only domains
- **Routing Policies**:
  - **Simple**: Single resource, no health checks
  - **Weighted**: Distribute traffic by weight (A/B testing, gradual rollout)
  - **Latency**: Route to lowest latency region
  - **Failover**: Active-passive failover with health checks
  - **Geolocation**: Route based on user location
  - **Geoproximity**: Route based on resource and user location (bias adjustment)
  - **Multi-Value Answer**: Return multiple IPs with health checks (not load balancer)
- **Health Checks**: Monitor endpoint health
  - HTTP/HTTPS/TCP checks
  - String matching
  - Calculated health checks
  - CloudWatch alarms
- **Traffic Flow**: Visual editor for complex routing
- **DNSSEC**: Protect against DNS spoofing
- **Domain Registration**: Buy and manage domains
- **Resolver**: DNS resolution for hybrid clouds

### Exam Tips & Heuristics
✅ **When to Use Route 53:**
- DNS management
- Domain registration
- Health checking and failover
- Global load balancing
- Hybrid DNS resolution

⚠️ **Common Pitfalls:**
- Weighted routing needs health checks for failover
- Geolocation vs Geoproximity (location vs distance)
- Multi-value is not a load balancer (returns multiple IPs)
- Alias records are free for AWS resources

🎯 **Exam Heuristics:**
- "DNS" = **Route 53**
- "A/B testing" or "gradual rollout" = **Weighted routing**
- "Disaster recovery" + "DNS" = **Failover routing** with health checks
- "Lowest latency" = **Latency routing**
- "Route by user country" = **Geolocation routing**
- "Route by distance" = **Geoproximity routing**
- "Multiple IPs with health checks" = **Multi-Value Answer**
- "Private DNS in VPC" = **Private Hosted Zone**
- "On-premises + AWS DNS" = **Route 53 Resolver**
- "Prevent DNS spoofing" = **DNSSEC**

### Routing Policy Selection
| Use Case | Policy |
|----------|--------|
| Single resource | Simple |
| A/B testing, canary deployment | Weighted |
| Best performance for users | Latency |
| Active-passive DR | Failover |
| Compliance (data residency) | Geolocation |
| Route by proximity | Geoproximity |
| Multiple IPs, client-side selection | Multi-Value |

---

## CloudFront

### Definition
Global Content Delivery Network (CDN) that delivers data, videos, applications, and APIs with low latency.

### Key Features
- **Edge Locations**: 400+ points of presence worldwide
- **Origins**: S3, EC2, ELB, HTTP servers, MediaPackage, MediaStore
- **Distributions**: Web (HTTP/HTTPS), RTMP (media streaming, deprecated)
- **Cache Behaviors**: Routing rules based on path patterns
- **TTL (Time To Live)**: Control cache duration
- **Invalidations**: Clear cache (costs apply)
- **Signed URLs/Cookies**: Restrict content access
- **Origin Access Identity (OAI)**: Restrict S3 access to CloudFront only
- **Origin Access Control (OAC)**: Newer alternative to OAI (recommended)
- **Geo Restriction**: Whitelist/blacklist countries
- **Field-Level Encryption**: Encrypt sensitive data at edge
- **Lambda@Edge**: Run code at edge locations
- **CloudFront Functions**: Lightweight edge processing (<1ms)
- **Origin Failover**: Automatic failover to secondary origin
- **Real-time Logs**: Stream logs to Kinesis Data Streams
- **SSL/TLS**: HTTPS support with custom certificates (ACM)

### Exam Tips & Heuristics
✅ **When to Use CloudFront:**
- Global content delivery
- Static and dynamic content acceleration
- Video streaming
- API acceleration
- DDoS protection (with Shield)
- Reduce latency for global users

⚠️ **Common Pitfalls:**
- Invalidation costs money (design for low invalidation needs)
- OAI/OAC required to secure S3 origins
- TTL affects how quickly changes propagate
- Custom SSL certificates must be in us-east-1

🎯 **Exam Heuristics:**
- "CDN" or "content delivery" = **CloudFront**
- "Global low latency" = **CloudFront**
- "Protect S3 from direct access" = **CloudFront + OAI/OAC**
- "Restrict content by geography" = **CloudFront Geo Restriction**
- "Signed URLs for private content" = **CloudFront Signed URLs**
- "Process at edge" = **Lambda@Edge** (complex) or **CloudFront Functions** (simple)
- "DDoS protection" = **CloudFront + Shield**
- "Video streaming" = **CloudFront** (origin: S3, MediaPackage)
- "API acceleration" = **CloudFront** + API Gateway

### Lambda@Edge vs CloudFront Functions
| Feature | Lambda@Edge | CloudFront Functions |
|---------|-------------|---------------------|
| Runtime | Node.js, Python | JavaScript |
| Execution Time | 5-30 seconds | <1 millisecond |
| Memory | Up to 10GB | 2MB |
| Triggers | Viewer/Origin request/response | Viewer request/response only |
| Use Case | Complex logic, API calls | Simple transformations |
| Cost | Higher | Lower (1/6th price) |

---

## API Gateway

### Definition
Fully managed service to create, publish, maintain, monitor, and secure APIs at any scale.

### Key Features
- **API Types**:
  - **REST API**: Full features, caching, API keys
  - **HTTP API**: Lighter, 70% cheaper, lower latency
  - **WebSocket API**: Two-way communication
- **Integration Types**:
  - **Lambda**: Serverless backend
  - **HTTP**: Backend HTTP endpoints
  - **AWS Services**: Direct integration (DynamoDB, S3, etc.)
  - **Mock**: Return static responses
- **Stages**: Different environments (dev, test, prod)
- **Deployments**: Snapshot of API configuration
- **Canary Deployments**: Gradual rollout to subset of traffic
- **Throttling**: Rate limiting (10,000 RPS burst, 5,000 RPS steady)
- **Caching**: Cache responses (0.5GB to 237GB)
- **Usage Plans & API Keys**: Monetization and access control
- **Request/Response Transformation**: Modify data in-flight
- **Request Validation**: Validate requests before reaching backend
- **CORS**: Enable cross-origin requests
- **Authorization**: IAM, Cognito, Lambda Authorizers, API Keys
- **Logs**: CloudWatch Logs and X-Ray integration
- **Private API**: VPC endpoint access only

### Exam Tips & Heuristics
✅ **When to Use API Gateway:**
- Create REST/HTTP/WebSocket APIs
- Frontend for Lambda functions
- Rate limiting and throttling
- API versioning and staging
- Transform and validate requests
- Serverless architectures

⚠️ **Common Pitfalls:**
- 29-second timeout limit
- 10MB payload limit
- Throttling at account and per-method level
- Caching is per-stage
- HTTP API has fewer features but cheaper

🎯 **Exam Heuristics:**
- "REST API" or "HTTP API" = **API Gateway**
- "Serverless API" = **API Gateway + Lambda**
- "WebSocket" = **API Gateway WebSocket**
- "API throttling" = **API Gateway** throttling/usage plans
- "Cache API responses" = **API Gateway caching**
- "Gradual API rollout" = **Canary deployments**
- "Private API in VPC" = **Private API** + VPC Endpoint
- "Need full REST features" = **REST API**
- "Cost-effective, simple HTTP" = **HTTP API**
- "Custom authentication" = **Lambda Authorizer**

### API Gateway Types Comparison
| Feature | REST API | HTTP API | WebSocket API |
|---------|----------|----------|---------------|
| Cost | Standard | 70% cheaper | Standard |
| Caching | Yes | No | No |
| Usage Plans | Yes | No | No |
| Authorization | IAM, Cognito, Lambda, API Keys | IAM, Cognito, Lambda | IAM, Lambda |
| Use Case | Full-featured APIs | Cost-effective APIs | Real-time 2-way communication |

---

## Direct Connect

### Definition
Dedicated network connection from on-premises to AWS.

### Key Features
- **Connection Types**:
  - **Dedicated**: 1Gbps, 10Gbps, 100Gbps physical connection
  - **Hosted**: 50Mbps to 10Gbps via AWS Partner
- **Virtual Interfaces (VIFs)**:
  - **Private VIF**: Connect to VPC
  - **Public VIF**: Connect to AWS public services (S3, DynamoDB)
  - **Transit VIF**: Connect to Transit Gateway
- **Link Aggregation Groups (LAG)**: Combine multiple connections
- **Direct Connect Gateway**: Connect to multiple VPCs in different regions
- **Encryption**: Not encrypted by default (use VPN over Direct Connect)
- **SLA**: 99.99% availability

### Exam Tips & Heuristics
✅ **When to Use Direct Connect:**
- Consistent network performance
- Large data transfers
- Hybrid cloud connectivity
- Reduce bandwidth costs
- Compliance requirements (private connection)

⚠️ **Common Pitfalls:**
- Setup takes weeks to months
- Not encrypted by default
- Single point of failure (use redundant connections)
- More expensive than VPN
- Requires Direct Connect location

🎯 **Exam Heuristics:**
- "Dedicated private connection" = **Direct Connect**
- "Consistent network performance" = **Direct Connect**
- "Large data migration" = **Direct Connect** or **Snow Family**
- "Hybrid cloud" = **Direct Connect** or **VPN**
- "Quick setup needed" = **VPN** (not Direct Connect)
- "Encrypted connection" = **VPN** or **VPN over Direct Connect**
- "Connect to multiple VPCs/regions" = **Direct Connect Gateway**
- "Cost-effective hybrid" = **VPN**
- "High bandwidth, low latency" = **Direct Connect**

### Direct Connect vs VPN
| Feature | Direct Connect | VPN |
|---------|---------------|-----|
| Setup Time | Weeks to months | Minutes |
| Bandwidth | 50Mbps-100Gbps | Up to 1.25Gbps per tunnel |
| Cost | Higher | Lower |
| Connection | Dedicated | Over internet |
| Encryption | Optional (VPN over DX) | Yes |
| Latency | Lower, consistent | Variable |
| Use Case | Large-scale, consistent | Quick setup, cost-effective |

---

## Global Accelerator

### Definition
Networking service that improves availability and performance using AWS global network.

### Key Features
- **Static Anycast IPs**: 2 static IPs for your application
- **Edge Locations**: Route traffic through AWS edge network
- **Health Checks**: Automatic failover to healthy endpoints
- **Traffic Dials**: Control traffic to endpoint groups
- **Endpoints**: ALB, NLB, EC2, Elastic IP
- **Geographic Routing**: No (routes to nearest healthy endpoint)
- **DDoS Protection**: AWS Shield integration

### Exam Tips & Heuristics
✅ **When to Use Global Accelerator:**
- Global applications needing static IPs
- Improve performance via AWS network
- Instant failover (<30 seconds)
- Non-HTTP use cases (TCP/UDP)
- Gaming, IoT, VoIP

⚠️ **Common Pitfalls:**
- Different from CloudFront (not caching)
- More expensive than CloudFront
- Static IPs provided by AWS (can't bring your own)

🎯 **Exam Heuristics:**
- "Static IP" + "global application" = **Global Accelerator**
- "Improve performance globally" + "not HTTP" = **Global Accelerator**
- "Fast failover" = **Global Accelerator**
- "TCP/UDP performance" = **Global Accelerator**
- "HTTP/HTTPS caching" = **CloudFront** (not Global Accelerator)
- "Weighted routing by region" = **Global Accelerator** traffic dials

### CloudFront vs Global Accelerator
| Feature | CloudFront | Global Accelerator |
|---------|-----------|-------------------|
| Purpose | CDN, caching | Network performance |
| Protocols | HTTP/HTTPS | TCP/UDP |
| Caching | Yes | No |
| Static IP | No (dynamic) | Yes (2 anycast IPs) |
| Use Case | Content delivery | Non-HTTP applications |

---

## Transit Gateway

### Definition
Hub-and-spoke network topology to connect VPCs and on-premises networks.

### Key Features
- **Hub-and-Spoke**: Single gateway for multiple connections
- **Attachments**: VPCs, VPN, Direct Connect, Transit Gateway peering
- **Route Tables**: Control routing between attachments
- **Transitive Routing**: Traffic can flow between attachments
- **Multi-Account**: AWS Organizations integration
- **Multi-Region**: Transit Gateway peering across regions
- **Equal-Cost Multi-Path (ECMP)**: Multiple VPN tunnels for higher bandwidth
- **Multicast**: Support for multicast traffic

### Exam Tips & Heuristics
✅ **When to Use Transit Gateway:**
- Connect many VPCs
- Simplify network topology
- Centralized management
- Transitive routing needed
- Hybrid connectivity at scale

⚠️ **Common Pitfalls:**
- Costs per attachment and data processing
- More complex than VPC peering for 2-3 VPCs
- Replaces multiple VPN connections

🎯 **Exam Heuristics:**
- "Connect many VPCs" = **Transit Gateway**
- "Hub-and-spoke topology" = **Transit Gateway**
- "Transitive routing" = **Transit Gateway** (VPC peering is not transitive)
- "Simplify VPN connections" = **Transit Gateway**
- "2-3 VPCs" = **VPC Peering** (simpler, cheaper)
- "Central network hub" = **Transit Gateway**
- "Multi-region connectivity" = **Transit Gateway Peering**

---

## VPN

### Definition
Encrypted connection over the internet between on-premises network and AWS.

### Key Features
- **Site-to-Site VPN**: Connect on-premises network to VPC
  - **Customer Gateway**: On-premises device/software
  - **Virtual Private Gateway**: VPC side gateway
  - **Two tunnels**: For high availability
- **Client VPN**: Remote user access to AWS and on-premises
- **CloudHub**: Connect multiple sites together via VPN
- **Acceleration**: Can use Global Accelerator for improved performance
- **Monitoring**: CloudWatch metrics

### Exam Tips & Heuristics
✅ **When to Use VPN:**
- Quick hybrid connectivity
- Backup for Direct Connect
- Cost-effective connectivity
- Encrypted communication required

⚠️ **Common Pitfalls:**
- Performance depends on internet connection
- Two tunnels but only one active (use ECMP with Transit Gateway for both)
- Customer gateway is on-premises (not in AWS)

🎯 **Exam Heuristics:**
- "Quick hybrid connectivity" = **VPN**
- "Backup for Direct Connect" = **VPN**
- "Encrypted connection" = **VPN**
- "Remote user access" = **Client VPN**
- "Connect multiple sites" = **VPN CloudHub**
- "Improve VPN performance" = **VPN** + **Global Accelerator**
- "Use both VPN tunnels" = **Transit Gateway** with ECMP

---

## Load Balancers

### Definition
Distribute incoming traffic across multiple targets (EC2, containers, IPs, Lambda).

### Key Features

#### Application Load Balancer (ALB)
- **Layer**: 7 (HTTP/HTTPS)
- **Targets**: EC2, ECS, Lambda, IP addresses
- **Routing**: Path, host, header, query string, source IP
- **Advanced Features**: WebSocket, HTTP/2, redirect, fixed response
- **Authentication**: Cognito, OIDC integration
- **Sticky Sessions**: Cookie-based
- **Cross-Zone**: Enabled by default (free)

#### Network Load Balancer (NLB)
- **Layer**: 4 (TCP/UDP/TLS)
- **Performance**: Millions of requests/second, ultra-low latency
- **Static IP**: One per AZ, Elastic IP support
- **Targets**: EC2, ECS, IP addresses
- **Preserve Source IP**: Yes
- **Cross-Zone**: Disabled by default (costs extra if enabled)

#### Gateway Load Balancer (GWLB)
- **Layer**: 3 (Network layer) + Layer 4
- **Use Case**: Deploy, scale, manage 3rd-party virtual appliances (firewalls, IDS/IPS)
- **Protocol**: GENEVE protocol (port 6081)
- **Targets**: EC2 instances (virtual appliances)

#### Classic Load Balancer (CLB)
- **Status**: Legacy (not recommended for new deployments)
- **Layers**: 4 and 7
- **Features**: Limited compared to ALB/NLB

### Exam Tips & Heuristics
✅ **When to Use Load Balancers:**
- Distribute traffic across instances
- High availability
- Health checking
- SSL/TLS termination
- Auto Scaling integration

⚠️ **Common Pitfalls:**
- ALB cannot have static IP (use NLB or Global Accelerator)
- NLB doesn't support host/path routing (use ALB)
- Cross-zone load balancing behavior differs by type
- Connection draining/deregistration delay must be configured

🎯 **Exam Heuristics:**
- "HTTP/HTTPS" + "path routing" = **ALB**
- "WebSocket" = **ALB**
- "Static IP needed" = **NLB**
- "Ultra-low latency" = **NLB**
- "TCP/UDP" = **NLB**
- "3rd-party security appliances" = **GWLB**
- "Lambda as target" = **ALB**
- "Millions of requests/second" = **NLB**
- "SSL termination" = **ALB** or **NLB**
- "Preserve source IP" = **NLB** (or ALB with X-Forwarded-For header)

### Load Balancer Comparison
| Feature | ALB | NLB | GWLB |
|---------|-----|-----|------|
| Layer | 7 (HTTP/HTTPS) | 4 (TCP/UDP) | 3 (IP) + 4 |
| Performance | Good | Ultra-high | High |
| Static IP | No | Yes | Yes |
| Path Routing | Yes | No | No |
| Lambda Target | Yes | No | No |
| Use Case | Web apps | High performance, static IP | Network appliances |
| Cost | Moderate | Moderate | Moderate |

---

## 🎓 Networking Service Selection Guide

| Requirement | Service |
|------------|---------|
| Isolated network | VPC |
| DNS and domain management | Route 53 |
| Content delivery network | CloudFront |
| API management | API Gateway |
| Dedicated network connection | Direct Connect |
| Global application performance | Global Accelerator |
| Connect many VPCs | Transit Gateway |
| Quick encrypted hybrid connection | VPN |
| HTTP/HTTPS load balancing | ALB |
| TCP/UDP load balancing | NLB |
| Network appliance deployment | GWLB |

---

## 💡 Key Exam Patterns

### High Availability Patterns
- Multi-AZ deployment
- Multiple NAT Gateways (one per AZ)
- Cross-region replication
- Failover routing in Route 53
- ALB/NLB with multi-AZ targets

### Performance Optimization Patterns
- CloudFront for global content delivery
- Global Accelerator for non-HTTP traffic
- Direct Connect for consistent bandwidth
- Route 53 latency routing
- NLB for ultra-low latency

### Security Patterns
- Private subnets with NAT Gateway
- VPC endpoints for private AWS service access
- Security groups and NACLs
- VPN or Direct Connect for encrypted connectivity
- OAI/OAC for CloudFront to S3

### Cost Optimization Patterns
- VPN instead of Direct Connect for low bandwidth
- Gateway endpoints (free) for S3/DynamoDB
- Single NAT Gateway for cost savings (affects HA)
- HTTP API instead of REST API

