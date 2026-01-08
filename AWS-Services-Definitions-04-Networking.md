# AWS Networking & Content Delivery - Complete Definitions (SAA-C03)

## Table of Contents
- [Amazon VPC](#amazon-vpc)
- [Amazon CloudFront](#amazon-cloudfront)
- [Amazon Route 53](#amazon-route-53)
- [AWS Direct Connect](#aws-direct-connect)
- [AWS VPN](#aws-vpn)
- [AWS Transit Gateway](#aws-transit-gateway)
- [Elastic Load Balancing](#elastic-load-balancing)
- [AWS Global Accelerator](#aws-global-accelerator)
- [AWS PrivateLink](#aws-privatelink)
- [Amazon API Gateway](#amazon-api-gateway)
- [AWS App Mesh](#aws-app-mesh)
- [AWS Cloud Map](#aws-cloud-map)

---

## Amazon VPC (Virtual Private Cloud)

**Definition**: Logically isolated virtual network in AWS cloud where you can launch AWS resources. Complete control over network configuration including IP address ranges, subnets, route tables, and network gateways.

**Key Components**:

### Subnets
- Subdivision of VPC IP address range
- **Public Subnet**: Has route to Internet Gateway
- **Private Subnet**: No direct route to Internet
- Each subnet must reside entirely within one AZ
- Cannot span multiple AZs

### Internet Gateway (IGW)
- Horizontally scaled, redundant, highly available
- Allows communication between VPC and internet
- Performs NAT for instances with public IPs
- One IGW per VPC

### NAT Gateway
- Managed NAT service
- Allows private subnet instances to access internet
- Deployed in public subnet
- Requires Elastic IP
- Highly available within AZ (deploy in multiple AZs for redundancy)
- Bandwidth up to 45 Gbps

### NAT Instance
- EC2 instance performing NAT
- Must disable source/destination check
- Must be in public subnet
- Not highly available by default
- Lower bandwidth than NAT Gateway
- Cost-effective for small workloads

### Security Groups (SG)
- Virtual firewall at instance level
- Stateful (return traffic automatically allowed)
- Allow rules only (no deny rules)
- Default: All inbound denied, all outbound allowed
- Can reference other security groups

### Network Access Control Lists (NACL)
- Firewall at subnet level
- Stateless (must allow return traffic)
- Allow and deny rules
- Rules evaluated in number order
- Default NACL allows all traffic
- Custom NACL denies all traffic by default

### Route Tables
- Contains rules (routes) for network traffic
- Each subnet must be associated with a route table
- Main route table (default)
- Custom route tables

### VPC Peering
- Connect two VPCs privately
- Can be in different regions (inter-region peering)
- Non-transitive (A-B, B-C doesn't mean A-C)
- CIDR ranges cannot overlap
- Uses AWS backbone network

### VPC Endpoints

#### Interface Endpoint (AWS PrivateLink)
- Elastic Network Interface (ENI) with private IP
- Powered by AWS PrivateLink
- Supports many AWS services
- Per-hour and per-GB charges

#### Gateway Endpoint
- Target for route table
- Only supports S3 and DynamoDB
- No additional charges
- Preferred for S3 and DynamoDB

### VPC Flow Logs
- Capture IP traffic information
- Can be created at VPC, subnet, or ENI level
- Published to CloudWatch Logs or S3
- Cannot be tagged as resource
- Cannot enable flow logs for peered VPC (unless in same account)

**VPC Sizing**:
- CIDR block between /16 (65,536 IPs) and /28 (16 IPs)
- Can add up to 5 CIDR blocks per VPC
- Cannot change CIDR block after creation (can add secondary)
- 5 IPs reserved per subnet (first 4 and last 1)

**DNS in VPC**:
- **enableDnsHostnames**: Assign public DNS hostnames
- **enableDnsSupport**: Enable DNS resolution
- Route 53 Resolver for DNS queries

**Use Cases**:
- Host web applications
- Multi-tier architectures
- Hybrid cloud connectivity
- Disaster recovery
- Dev/test environments

**Exam Tips**:
- Security Groups are stateful, NACLs are stateless
- NAT Gateway is managed, NAT Instance is self-managed
- VPC Peering is non-transitive
- Gateway Endpoints (S3, DynamoDB) are free
- 5 IPs reserved in each subnet
- Default VPC has IGW, route to internet, public subnet
- Use VPC Flow Logs for network monitoring
- CIDR blocks cannot overlap for peering
- One IGW per VPC

---

## Amazon CloudFront

**Definition**: Fast content delivery network (CDN) service that securely delivers data, videos, applications, and APIs to customers globally with low latency and high transfer speeds.

**Key Features**:
- Global network of edge locations (400+)
- DDoS protection (AWS Shield Standard)
- AWS WAF integration
- HTTPS support
- Origin Shield (additional caching layer)
- Real-time metrics and logging
- Lambda@Edge for edge computing

**Components**:

### Edge Locations
- Locations where content is cached
- More edge locations than AWS Regions
- Can PUT objects (not just GET)

### Regional Edge Caches
- Sit between origin and edge locations
- Larger cache-width than edge locations
- Reduce load on origin

### Origins
- **S3 Bucket**: Distribute files, static website
- **Custom Origin**: EC2, ELB, on-premises servers
- **MediaPackage and MediaStore**: Video streaming

### Distributions
- Configuration for content delivery
- **Web Distribution**: Websites, HTTP/HTTPS
- **RTMP Distribution**: Streaming media (deprecated)

**Caching**:
- **TTL (Time To Live)**: Control cache duration
- Headers, cookies, query strings affect caching
- Invalidate objects (clear cache) - costs money
- Versioned URLs (e.g., v1, v2) instead of invalidation

**Security**:
- **Origin Access Identity (OAI)**: Restrict S3 access to CloudFront only
- **Origin Access Control (OAC)**: Newer, supports SSE-KMS
- **Signed URLs**: Access control for individual files
- **Signed Cookies**: Access control for multiple files
- **Field-Level Encryption**: Encrypt specific fields at edge
- **Geo-Restriction**: Whitelist or blacklist countries
- **HTTPS**: Viewer to CloudFront, CloudFront to Origin

**Lambda@Edge**:
- Run Lambda functions at edge locations
- Modify requests/responses
- Use cases:
  - A/B testing
  - User authentication
  - Dynamic content generation
  - Bot mitigation

**Pricing**:
- Data transfer out from edge locations
- HTTP/HTTPS requests
- Invalidation requests (first 1,000/month free)
- Price classes (all regions, exclude expensive regions)

**Use Cases**:
- Static website acceleration
- Video streaming
- Software distribution
- API acceleration
- Dynamic content delivery
- Live event streaming

**Exam Tips**:
- CloudFront caches at edge locations (not AZs or Regions)
- Use OAI/OAC to restrict S3 bucket access
- Signed URLs for single file, Signed Cookies for multiple files
- Invalidation costs money, use versioning instead
- Lambda@Edge for edge computing and content customization
- Geo-restriction for content access control
- Origin Shield for additional caching layer
- Supports both HTTP and HTTPS
- WebSockets and HTTP/2 supported

---

## Amazon Route 53

**Definition**: Highly available and scalable cloud Domain Name System (DNS) web service designed to route end users to internet applications.

**Key Features**:
- 100% SLA availability
- Domain registration
- DNS routing
- Health checking
- Traffic flow (visual editor)
- DNSSEC support

**Routing Policies**:

### Simple Routing
- Single resource
- Multiple IP addresses (random selection)
- No health checks

### Weighted Routing
- Distribute traffic based on assigned weights
- A/B testing
- Gradual migration
- Blue/green deployments

### Latency-Based Routing
- Route to region with lowest latency
- Based on user location
- Improves user experience

### Failover Routing
- Active-passive setup
- Primary and secondary resources
- Health checks on primary
- Automatic failover

### Geolocation Routing
- Route based on user's geographic location
- Content localization
- Restrict distribution

### Geoproximity Routing
- Route based on geographic location of users and resources
- Bias to route more or less traffic
- Requires Traffic Flow

### Multi-Value Answer Routing
- Return multiple healthy targets
- Up to 8 healthy records
- Health checks for each record
- Not a substitute for ELB

**Health Checks**:
- Monitor endpoint health
- Integrated with CloudWatch alarms
- Can check endpoint, CloudWatch alarm, or calculated health check
- Health checks for:
  - IP address or domain name
  - String matching in response
  - Other health checks (calculated)

**Record Types**:
- **A**: IPv4 address
- **AAAA**: IPv6 address
- **CNAME**: Canonical name (cannot use for zone apex)
- **Alias**: Route 53 specific (can use for zone apex)
- **MX**: Mail exchange
- **NS**: Name server
- **PTR**: Pointer (reverse DNS)
- **SOA**: Start of authority
- **TXT**: Text

**Alias Records**:
- Route 53 extension to DNS
- Map to AWS resources (ELB, CloudFront, S3, etc.)
- Free of charge
- Can be used for zone apex (example.com)
- Automatic IP address changes

**Traffic Flow**:
- Visual editor for routing policies
- Version control
- Reusable templates
- Can combine multiple routing policies

**Use Cases**:
- Domain registration and management
- DNS routing for applications
- Hybrid cloud DNS resolution
- Disaster recovery
- Global load balancing

**Exam Tips**:
- Route 53 is global service (not region-specific)
- Alias records are free and can point to zone apex
- Simple routing doesn't support health checks
- Failover routing for active-passive DR
- Latency routing for best performance
- Geolocation for content localization
- Weighted routing for A/B testing and gradual migrations
- Multi-value answers like simple routing but with health checks
- Health checks can monitor endpoints, CloudWatch alarms
- 50 domain name limit (can request increase)

---

## AWS Direct Connect

**Definition**: Dedicated network connection from on-premises to AWS. Provides private, dedicated, high-bandwidth connection to AWS.

**Key Features**:
- Dedicated private connection
- Reduced bandwidth costs
- Consistent network performance
- Compatible with all AWS services
- Supports 1 Gbps, 10 Gbps, 100 Gbps
- Virtual interfaces (VIFs) for multiple VPCs

**Connection Types**:

### Dedicated Connection
- Physical Ethernet connection
- 1 Gbps, 10 Gbps, 100 Gbps
- Request through AWS Direct Connect console
- Delivered at Direct Connect location

### Hosted Connection
- Provided by AWS Direct Connect Partner
- 50 Mbps to 10 Gbps
- Partner provisions connection
- More flexible bandwidth options

**Virtual Interfaces (VIFs)**:

### Private VIF
- Connect to VPC via Virtual Private Gateway
- Access private IP addresses
- One Private VIF per VPC

### Public VIF
- Connect to public AWS services (S3, DynamoDB)
- Does not traverse internet
- Access AWS public IPs

### Transit VIF
- Connect to Transit Gateway
- Access multiple VPCs through single VIF

**Direct Connect Gateway**:
- Connect to multiple VPCs across regions
- Not transitive between VPCs
- Works with Virtual Private Gateway or Transit Gateway

**High Availability**:
- Single Direct Connect connection is not highly available
- Use multiple connections for redundancy
- Use VPN as backup
- Multiple Direct Connect locations

**Link Aggregation Groups (LAG)**:
- Combine multiple connections
- Single logical connection
- Active-active mode
- Up to 4 connections per LAG

**Use Cases**:
- Hybrid cloud architectures
- Large data transfers
- Real-time data feeds
- Consistent network performance
- Reduced data transfer costs

**Exam Tips**:
- Direct Connect is NOT encrypted by default (use VPN over Direct Connect)
- Takes weeks to months to establish
- Private VIF for VPC access, Public VIF for AWS services
- Use Direct Connect Gateway to access multiple VPCs across regions
- Not highly available by default (use multiple connections)
- More consistent than internet, lower latency
- Reduces data transfer costs for large volumes
- Use VPN as backup connection

---

## AWS VPN

**Definition**: Secure connection between on-premises network, remote office, or client devices to AWS global network.

**Types**:

### Site-to-Site VPN

**Definition**: Encrypted tunnel between on-premises network and AWS VPC.

**Components**:
- **Virtual Private Gateway (VGW)**: VPN concentrator on AWS side
- **Customer Gateway (CGW)**: Physical or software appliance on customer side
- **VPN Connection**: Secure connection between VGW and CGW

**Key Features**:
- IPsec encrypted tunnels
- Redundant tunnels for high availability
- Dynamic routing with BGP or static routing
- Up to 1.25 Gbps per tunnel
- Multiple VPN connections supported

**High Availability**:
- Two tunnels per VPN connection (different AZs)
- Use multiple customer gateways
- Use Transit Gateway for multiple VPCs

### Client VPN

**Definition**: Managed client-based VPN service for secure remote access.

**Key Features**:
- OpenVPN-based
- Elastic scaling
- Authentication via Active Directory, certificate-based, SSO
- Split-tunnel or full-tunnel
- CloudWatch Logs integration

**Use Cases**:
- Remote employee access
- Secure access to AWS resources
- Work-from-home solutions

**CloudHub**:
- Connect multiple branch offices via VPN
- Hub-and-spoke model
- Uses Virtual Private Gateway
- Branch offices can communicate with each other

**VPN vs Direct Connect**:

| Feature | VPN | Direct Connect |
|---------|-----|----------------|
| **Connection** | Over internet | Dedicated private |
| **Speed** | Up to 1.25 Gbps | 1-100 Gbps |
| **Setup Time** | Minutes | Weeks to months |
| **Cost** | Lower | Higher |
| **Encryption** | Yes (IPsec) | No (unless VPN over DX) |
| **Consistency** | Variable | Consistent |

**Exam Tips**:
- Site-to-Site VPN for on-premises to VPC connection
- Client VPN for remote user access
- VPN is encrypted by default (unlike Direct Connect)
- Quick to set up (minutes vs weeks for Direct Connect)
- Use as backup for Direct Connect
- VPN over Direct Connect for encrypted private connection
- Virtual Private Gateway required for Site-to-Site VPN
- CloudHub for connecting multiple sites

---

## AWS Transit Gateway

**Definition**: Network transit hub that connects VPCs and on-premises networks through a central hub, simplifying network architecture.

**Key Features**:
- Hub-and-spoke topology
- Transitive routing (VPCs can communicate)
- Supports thousands of VPCs
- Cross-region peering
- Route tables for traffic isolation
- Multicast support
- Integration with Direct Connect and VPN

**Components**:
- **Attachments**: VPCs, VPNs, Direct Connect Gateways, Transit Gateway peering
- **Route Tables**: Control routing between attachments
- **Route Propagation**: Automatically add routes

**Architecture Benefits**:
- Simplifies network topology
- Reduces number of connections
- Central management
- Scalability
- Transitive peering

**Use Cases**:
- Connect multiple VPCs
- Central egress/ingress point
- Shared services VPC
- Hybrid connectivity hub
- Multi-region connectivity

**Comparison: VPC Peering vs Transit Gateway**:

| Feature | VPC Peering | Transit Gateway |
|---------|-------------|-----------------|
| **Transitive** | No | Yes |
| **Scalability** | N×(N-1)/2 connections | N connections |
| **Management** | Individual connections | Central hub |
| **Cross-Region** | Yes | Yes |
| **Cost** | Per GB transfer | Hourly + per GB |

**Exam Tips**:
- Transit Gateway enables transitive routing
- Simplifies network architecture (hub-and-spoke)
- Can share Transit Gateway across accounts (RAM)
- Supports IP multicast
- Use for connecting many VPCs and on-premises networks
- Route tables provide traffic isolation
- Can peer Transit Gateways across regions
- More expensive but simpler than VPC Peering at scale

---

## Elastic Load Balancing (ELB)

**Definition**: Automatically distributes incoming application traffic across multiple targets, such as EC2 instances, containers, IP addresses, and Lambda functions.

**Load Balancer Types**:

### Application Load Balancer (ALB)

**Definition**: Layer 7 load balancer for HTTP/HTTPS traffic.

**Key Features**:
- Path-based and host-based routing
- Support for containerized applications
- WebSocket and HTTP/2 support
- Native IPv6 support
- Integration with WAF
- User authentication (Cognito, OIDC, SAML)
- Fixed response rules
- Lambda as target

**Target Types**:
- EC2 instances
- IP addresses (private IPs)
- Lambda functions
- ECS tasks

**Routing**:
- Path-based: /api → Target Group A, /mobile → Target Group B
- Host-based: api.example.com → TG A, web.example.com → TG B
- Query string and header-based routing
- Source IP routing

**Use Cases**:
- Microservices
- Container-based applications
- HTTP/HTTPS applications
- Advanced routing requirements

### Network Load Balancer (NLB)

**Definition**: Layer 4 load balancer for TCP/UDP/TLS traffic.

**Key Features**:
- Ultra-high performance (millions of requests/sec)
- Ultra-low latency
- Static IP support (one per AZ)
- Elastic IP support
- Preserves source IP
- TLS termination
- Zonal isolation

**Target Types**:
- EC2 instances
- IP addresses (private IPs)
- ALB (NLB in front of ALB)

**Use Cases**:
- TCP/UDP traffic
- Extreme performance required
- Static IP requirements
- PrivateLink services

### Gateway Load Balancer (GWLB)

**Definition**: Layer 3 load balancer for deploying, scaling, and managing third-party virtual appliances.

**Key Features**:
- Single entry/exit point for traffic
- Transparent network gateway
- Distributes traffic to virtual appliances
- Uses GENEVE protocol (port 6081)
- Scales appliances

**Use Cases**:
- Firewalls
- Intrusion detection/prevention systems
- Deep packet inspection
- Payload manipulation

### Classic Load Balancer (CLB)

**Definition**: Previous generation load balancer (Layer 4 and Layer 7).

**Note**: AWS recommends using ALB or NLB instead.

**Key Features (compared to ALB/NLB)**:
- Less features
- Lower cost
- Legacy applications
- EC2-Classic support

**Common Features Across ELBs**:
- **Health Checks**: Monitor target health
- **Cross-Zone Load Balancing**: Distribute across all targets in all enabled AZs
- **Connection Draining** (Deregistration Delay): Complete in-flight requests
- **Sticky Sessions**: Route requests from same client to same target
- **SSL/TLS Termination**: Decrypt at load balancer
- **Access Logs**: Detailed request information
- **CloudWatch Metrics**: Monitor performance

**Security**:
- Security groups
- SSL/TLS certificates (ACM integration)
- WAF integration (ALB)
- Authentication (ALB)

**Cross-Zone Load Balancing**:
- **ALB**: Always enabled, no charges
- **NLB**: Disabled by default, charges for inter-AZ traffic
- **CLB**: Disabled by default, no charges if enabled

**Exam Tips**:
- **ALB**: Layer 7, HTTP/HTTPS, advanced routing
- **NLB**: Layer 4, TCP/UDP, ultra performance, static IP
- **GWLB**: Layer 3, third-party appliances
- ALB can route to Lambda functions
- NLB preserves source IP address
- Use target groups to organize targets
- Cross-zone load balancing for even distribution
- Sticky sessions use cookies
- Connection draining for graceful shutdown
- Health checks determine target health

---

## AWS Global Accelerator

**Definition**: Network service that improves availability and performance of applications with global users by routing traffic through AWS global network infrastructure.

**Key Features**:
- Two static Anycast IPs
- Deterministic routing
- Fast failover (< 30 seconds)
- Health checks
- DDoS protection (Shield Standard)
- Edge location ingress
- Traffic dials for weighted routing
- Fine-grained control

**Components**:
- **Static Anycast IPs**: Two IPs provided by AWS
- **Accelerator**: Main resource
- **Listeners**: Process inbound connections
- **Endpoint Groups**: Associated with AWS Regions
- **Endpoints**: Resources (ALB, NLB, EC2, Elastic IP)

**Traffic Management**:
- **Traffic Dials**: Control percentage of traffic to endpoint group (0-100%)
- **Endpoint Weights**: Distribute traffic within endpoint group

**Use Cases**:
- Global applications
- Gaming applications
- IoT device connectivity
- VoIP applications
- Disaster recovery

**Global Accelerator vs CloudFront**:

| Feature | Global Accelerator | CloudFront |
|---------|-------------------|------------|
| **Use Case** | Dynamic content, non-HTTP | Static/dynamic content, HTTP/HTTPS |
| **Caching** | No | Yes |
| **IPs** | Static Anycast IPs | Dynamic edge location IPs |
| **Protocol** | TCP, UDP | HTTP, HTTPS, WebSocket |
| **Failover** | Instant failover | Not designed for failover |
| **Region** | Routes to closest healthy endpoint | Caches at edge |

**Exam Tips**:
- Global Accelerator for performance of non-HTTP traffic
- Two static Anycast IPs (don't change)
- Routes through AWS global network
- Fast failover (< 30 seconds)
- Good for gaming, IoT, VoIP
- CloudFront for caching, Global Accelerator for routing
- Integrates with Shield for DDoS protection
- Traffic dials for blue/green deployments and A/B testing

---

## AWS PrivateLink

**Definition**: Provides private connectivity between VPCs, AWS services, and on-premises applications without exposing traffic to the internet.

**Key Features**:
- Private connectivity
- No VPC peering or internet gateway required
- Scalable to thousands of VPCs
- One-way access
- Uses Elastic Network Interfaces (ENI)
- No overlapping CIDR requirements

**Components**:
- **Service Provider**: VPC hosting the service (uses NLB)
- **Service Consumer**: VPC accessing the service (uses Interface Endpoint)
- **Endpoint Service**: Service exposed via PrivateLink
- **Interface Endpoint**: ENI in consumer VPC

**Use Cases**:
- SaaS applications
- Shared services
- Marketplace integration
- Partner integrations
- Accessing AWS services privately

**Comparison**:
- **VPC Peering**: Bidirectional, full access, requires non-overlapping CIDRs
- **PrivateLink**: Unidirectional, specific service access, allows overlapping CIDRs

**Exam Tips**:
- PrivateLink for private service access without internet
- One-way access (service consumer to provider)
- Requires Network Load Balancer on provider side
- Uses Interface Endpoint on consumer side
- Doesn't require VPC peering
- Allows overlapping CIDR blocks
- Scalable to thousands of connections
- Good for SaaS provider to customer connectivity

---

## Amazon API Gateway

**Definition**: Fully managed service for creating, publishing, maintaining, monitoring, and securing APIs at any scale.

**API Types**:

### REST API
- RESTful APIs
- Supports API keys, per-client throttling
- Request/response transformations
- Caching
- OpenAPI specification

### HTTP API
- Lower cost than REST API
- Lower latency
- Supports OIDC and OAuth 2.0
- Built-in CORS support
- Simpler than REST API

### WebSocket API
- Real-time two-way communication
- Stateful connections
- Use cases: chat apps, trading platforms, gaming

**Key Features**:
- Request throttling
- API caching
- Request/response transformation
- API versioning and staging
- Custom domain names
- Authentication and authorization
- CloudWatch monitoring
- API lifecycle management

**Integration Types**:
- **Lambda Function**: Invoke Lambda
- **HTTP Endpoint**: Proxy to HTTP endpoint
- **AWS Service**: Integrate with AWS services (S3, DynamoDB, Kinesis)
- **Mock**: Return response without backend
- **VPC Link**: Access resources in VPC (NLB)

**Authentication**:
- IAM permissions
- Lambda authorizer (custom authorization)
- Cognito User Pools
- API keys (for usage plans)

**Throttling and Quotas**:
- Default account-level throttle: 10,000 rps
- Per-method throttling
- Usage plans for API keys
- Burst limit: 5,000 requests

**Caching**:
- Cache responses (0.5 GB to 237 GB)
- TTL (default 300 seconds)
- Cache key parameters
- Per-stage caching
- Requires cache invalidation or TTL expiry

**Stages and Deployments**:
- Deploy API to stages (dev, test, prod)
- Stage variables for environment-specific config
- Canary deployments
- Stage-specific throttling and caching

**Use Cases**:
- Serverless APIs (with Lambda)
- Microservices
- Mobile backends
- Real-time applications (WebSocket)
- Legacy application modernization

**Exam Tips**:
- API Gateway for creating, publishing, managing APIs
- REST API for feature-rich, HTTP API for lower cost/latency
- WebSocket API for real-time communication
- Integrates with Lambda for serverless
- Can throttle requests (prevent overload)
- Caching improves performance
- Stages for environment separation
- Lambda authorizer for custom authentication
- VPC Link to access private resources
- CloudWatch for monitoring and logging

---

## AWS App Mesh

**Definition**: Service mesh that provides application-level networking to make it easy for services to communicate with each other across multiple types of compute infrastructure.

**Key Features**:
- Standardized service-to-service communication
- End-to-end visibility
- Traffic controls
- Works with ECS, EKS, Fargate, EC2
- Open source Envoy proxy
- Multi-account and multi-region

**Components**:
- **Mesh**: Logical boundary for network traffic
- **Virtual Services**: Abstract representation of service
- **Virtual Nodes**: Logical pointer to task group, pod, or service
- **Virtual Routers**: Route traffic to virtual nodes
- **Routes**: Route traffic based on criteria

**Use Cases**:
- Microservices communication
- Service discovery
- Traffic management
- Observability
- Security (mTLS)

**Exam Tips**:
- App Mesh for microservices communication management
- Uses Envoy proxy sidecar
- Works across ECS, EKS, EC2, Fargate
- Provides observability and traffic control

---

## AWS Cloud Map

**Definition**: Cloud resource discovery service that defines custom names for application resources and maintains updated location of dynamically changing resources.

**Key Features**:
- Service discovery
- Health checking
- Custom naming
- API-based service discovery
- DNS-based service discovery
- Integration with ECS, EKS, App Mesh

**Use Cases**:
- Microservices service discovery
- Dynamic resource location
- Container orchestration
- Multi-cloud environments

**Exam Tips**:
- Cloud Map for service discovery
- Maintains registry of resources
- Health checks for discovered resources
- Integrates with container services

---

## Summary Comparison Table

| Service | Type | Use Case |
|---------|------|----------|
| **VPC** | Virtual Network | Isolated cloud network |
| **CloudFront** | CDN | Content delivery, low latency |
| **Route 53** | DNS | Domain management, traffic routing |
| **Direct Connect** | Dedicated Connection | Private, dedicated network to AWS |
| **VPN** | Encrypted Tunnel | Secure connection over internet |
| **Transit Gateway** | Network Hub | Connect multiple VPCs and on-premises |
| **ELB** | Load Balancer | Distribute traffic across targets |
| **Global Accelerator** | Network Accelerator | Performance for global applications |
| **PrivateLink** | Private Connectivity | VPC-to-VPC service access |
| **API Gateway** | API Management | Create, publish, manage APIs |
| **App Mesh** | Service Mesh | Microservices communication |
| **Cloud Map** | Service Discovery | Resource discovery |

---

## Exam Focus Areas

1. **VPC Components**: Security Groups vs NACLs, public vs private subnets
2. **Load Balancer Types**: ALB vs NLB vs GWLB use cases
3. **Route 53 Routing**: When to use each routing policy
4. **VPN vs Direct Connect**: Trade-offs and use cases
5. **Transit Gateway**: Simplifying complex network architectures
6. **CloudFront**: Caching strategies, origins, security
7. **Global Accelerator vs CloudFront**: Different use cases
8. **PrivateLink**: Private service access without peering
9. **VPC Endpoints**: Gateway vs Interface endpoints
10. **High Availability**: Multi-AZ deployments, redundancy strategies

---

*Last Updated: January 2026 - SAA-C03 Exam*
