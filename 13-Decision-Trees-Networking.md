# AWS SAA-C03: Networking & Content Delivery Decision Trees

## 🌳 Quick Navigation
- [Main Networking Decision Tree](#main-networking-decision-tree)
- [Load Balancer Selection](#load-balancer-selection)
- [Route 53 Routing Policy Selection](#route-53-routing-policy-selection)
- [VPC Components Decision](#vpc-components-decision)
- [Hybrid Connectivity Selection](#hybrid-connectivity-selection)
- [Content Delivery Decision](#content-delivery-decision)

---

## Main Networking Decision Tree

```
START: Networking requirement?
│
├─→ Need to distribute traffic across targets?
│   └─→ Go to "Load Balancer Selection"
│
├─→ Need DNS management?
│   └─→ **Amazon Route 53**
│       └─→ Go to "Route 53 Routing Policy Selection"
│
├─→ Need content delivery network (CDN)?
│   └─→ Go to "Content Delivery Decision"
│
├─→ Need isolated network (private cloud)?
│   └─→ **Amazon VPC** (Virtual Private Cloud)
│       └─→ Go to "VPC Components Decision"
│
├─→ Need connection to on-premises?
│   └─→ Go to "Hybrid Connectivity Selection"
│
├─→ Need to expose APIs?
│   └─→ **Amazon API Gateway**
│       ├─→ RESTful APIs, WebSocket APIs, HTTP APIs
│       ├─→ Throttling, caching, authentication
│       ├─→ Integration: Lambda, HTTP endpoints, AWS services
│       └─→ Use case: Microservices, serverless backends
│
└─→ Need to connect multiple VPCs?
    └─→ **AWS Transit Gateway**
        ├─→ Hub-and-spoke network topology
        ├─→ Connect thousands of VPCs
        ├─→ Cross-region peering
        └─→ Use case: Large-scale VPC connectivity
```

---

## Load Balancer Selection

```
START: Which load balancer?
│
├─→ What layer (OSI model)?
│   │
│   ├─→ Layer 7 (Application - HTTP/HTTPS)?
│   │   └─→ **Application Load Balancer (ALB)**
│   │       ├─→ HTTP, HTTPS, gRPC, HTTP/2, WebSocket
│   │       ├─→ Path-based routing (/api, /images)
│   │       ├─→ Host-based routing (api.example.com, www.example.com)
│   │       ├─→ Query string & header-based routing
│   │       ├─→ Target types: EC2, ECS, Lambda, IP addresses
│   │       ├─→ Sticky sessions (cookie-based)
│   │       ├─→ SSL/TLS termination
│   │       ├─→ Authentication: Cognito, OIDC
│   │       ├─→ Fixed hostname (xxx.region.elb.amazonaws.com)
│   │       └─→ Use case: Web applications, microservices, containers
│   │
│   ├─→ Layer 4 (Transport - TCP/UDP)?
│   │   └─→ **Network Load Balancer (NLB)**
│   │       ├─→ TCP, UDP, TLS
│   │       ├─→ Ultra-high performance (millions req/sec)
│   │       ├─→ Ultra-low latency (~100 ms vs ~400 ms for ALB)
│   │       ├─→ Static IP per AZ (Elastic IP support)
│   │       ├─→ Preserves source IP
│   │       ├─→ Target types: EC2, ECS, IP addresses
│   │       ├─→ No sticky sessions (connection-based)
│   │       ├─→ TLS termination (optional)
│   │       └─→ Use case: Gaming, IoT, TCP/UDP traffic, extreme performance
│   │
│   ├─→ Layer 3 (Network - IP)?
│   │   └─→ **Gateway Load Balancer (GWLB)**
│   │       ├─→ Operates at Layer 3 (IP packets)
│   │       ├─→ Deploy, scale, manage 3rd-party virtual appliances
│   │       ├─→ Examples: Firewalls, IDS/IPS, deep packet inspection
│   │       ├─→ Uses GENEVE protocol (port 6081)
│   │       ├─→ Target: EC2 instances (appliances)
│   │       └─→ Use case: Intrusion detection, firewalls, security appliances
│   │
│   └─→ Legacy (not recommended for new apps)?
│       └─→ **Classic Load Balancer (CLB)**
│           ├─→ Layer 4 & 7 support (basic)
│           ├─→ HTTP, HTTPS, TCP, SSL
│           ├─→ Legacy, retired in new accounts
│           └─→ Migrate to ALB or NLB
│
├─→ Feature requirements?
│   ├─→ Need path/host-based routing → ALB
│   ├─→ Need Lambda targets → ALB
│   ├─→ Need static IP → NLB
│   ├─→ Need ultra-low latency → NLB
│   ├─→ Need to preserve source IP → NLB
│   └─→ Need 3rd-party appliances → GWLB
│
└─→ Protocol?
    ├─→ HTTP/HTTPS → ALB
    ├─→ TCP/UDP → NLB
    ├─→ gRPC → ALB
    └─→ WebSocket → ALB or NLB
```

### Load Balancer Comparison Matrix

| Feature | ALB | NLB | GWLB | CLB |
|---------|-----|-----|------|-----|
| **Layer** | 7 (Application) | 4 (Transport) | 3 (Network) | 4 & 7 (Legacy) |
| **Protocols** | HTTP, HTTPS, gRPC | TCP, UDP, TLS | IP | HTTP, HTTPS, TCP |
| **Routing** | Path, host, header | Connection-based | IP-based | Basic |
| **Performance** | Good | Excellent | Excellent | Moderate |
| **Latency** | ~400 ms | ~100 ms | ~100 ms | Variable |
| **Static IP** | No | Yes (Elastic IP) | No | No |
| **Lambda Target** | Yes | No | No | No |
| **Source IP** | Via headers | Preserved | Preserved | Via headers |
| **Use Case** | Web apps | Gaming, IoT | Security appliances | Legacy |

---

## Route 53 Routing Policy Selection

```
START: Which Route 53 routing policy?
│
├─→ Simple routing (single resource)?
│   └─→ **Simple Routing Policy**
│       ├─→ One record with one or more IP addresses
│       ├─→ No health checks
│       ├─→ Client randomly chooses from multiple IPs
│       └─→ Use case: Single web server, basic setup
│
├─→ Route based on multiple values (with health checks)?
│   └─→ **Multivalue Answer Routing Policy**
│       ├─→ Return multiple values (up to 8)
│       ├─→ Health checks for each value
│       ├─→ Only healthy resources returned
│       └─→ Use case: Simple load balancing, multiple web servers
│
├─→ Route based on weight/percentage?
│   └─→ **Weighted Routing Policy**
│       ├─→ Control percentage of traffic to each resource
│       ├─→ Example: 70% to new version, 30% to old
│       ├─→ Health checks supported
│       ├─→ Weights don't need to sum to 100
│       └─→ Use case: A/B testing, gradual deployments, blue/green
│
├─→ Route based on user location (geography)?
│   ├─→ Route to closest resource (lowest latency)?
│   │   └─→ **Latency-Based Routing Policy**
│   │       ├─→ Route to resource with lowest latency
│   │       ├─→ Based on AWS region latency measurements
│   │       ├─→ Health checks supported
│   │       └─→ Use case: Global applications, performance optimization
│   │
│   ├─→ Route based on continent/country?
│   │   └─→ **Geolocation Routing Policy**
│   │       ├─→ Route based on user's geographic location
│   │       ├─→ Can specify: Continent, country, US state
│   │       ├─→ Must have default record
│   │       ├─→ Health checks supported
│   │       └─→ Use case: Content localization, compliance, language
│   │
│   └─→ Route based on geographic proximity?
│       └─→ **Geoproximity Routing Policy**
│           ├─→ Route based on geographic location + bias
│           ├─→ Bias: Shift traffic to/from resources (-99 to +99)
│           ├─→ Requires Traffic Flow
│           └─→ Use case: Traffic distribution control, shift traffic regions
│
├─→ Failover (active-passive)?
│   └─→ **Failover Routing Policy**
│       ├─→ Primary and secondary resources
│       ├─→ Routes to secondary if primary health check fails
│       ├─→ Active-passive failover
│       └─→ Use case: Disaster recovery, high availability
│
└─→ Complex decision tree?
    └─→ **Traffic Flow** (visual editor)
        ├─→ Combine multiple routing policies
        ├─→ Version control for routing policies
        └─→ Use case: Complex routing logic
```

### Route 53 Routing Policy Use Cases

| Policy | Use Case | Key Feature |
|--------|----------|-------------|
| **Simple** | Single resource | No health checks |
| **Multivalue** | Multiple resources | Health checks, return up to 8 |
| **Weighted** | A/B testing, gradual rollout | Percentage-based traffic split |
| **Latency** | Global apps, best performance | Route to lowest latency region |
| **Geolocation** | Localization, compliance | Route by user's location |
| **Geoproximity** | Traffic shift between regions | Geographic + bias |
| **Failover** | DR, HA | Primary/secondary with health checks |

---

## VPC Components Decision

```
START: VPC component needed?
│
├─→ Need to control traffic at instance level?
│   └─→ **Security Groups**
│       ├─→ Stateful (return traffic automatically allowed)
│       ├─→ Allow rules only (no deny rules)
│       ├─→ Applies to ENI (Elastic Network Interface)
│       ├─→ Can reference other security groups
│       ├─→ Evaluate all rules before deciding
│       └─→ Use case: Instance-level firewall (EC2, RDS, etc.)
│
├─→ Need to control traffic at subnet level?
│   └─→ **Network Access Control Lists (NACLs)**
│       ├─→ Stateless (must explicitly allow return traffic)
│       ├─→ Allow and deny rules
│       ├─→ Applies to entire subnet
│       ├─→ Rules evaluated in order (lowest number first)
│       ├─→ Default: Allows all inbound/outbound
│       └─→ Use case: Subnet-level firewall, explicit deny
│
├─→ Need internet access for public subnet?
│   └─→ **Internet Gateway (IGW)**
│       ├─→ Horizontally scaled, HA, redundant
│       ├─→ One IGW per VPC
│       ├─→ No bandwidth constraints
│       └─→ Must update route table (0.0.0.0/0 → IGW)
│
├─→ Need internet access for private subnet?
│   └─→ **NAT Gateway** or **NAT Instance**
│       ├─→ NAT Gateway (recommended):
│       │   ├─→ Managed by AWS
│       │   ├─→ Highly available within AZ
│       │   ├─→ Up to 45 Gbps bandwidth
│       │   ├─→ No security groups
│       │   ├─→ One per AZ for HA
│       │   └─→ Use case: Production environments
│       │
│       └─→ NAT Instance (legacy):
│           ├─→ EC2 instance with NAT role
│           ├─→ You manage (patching, scaling, HA)
│           ├─→ Can use security groups
│           ├─→ Can be bastion host
│           └─→ Use case: Cost optimization, special requirements
│
├─→ Need VPC-to-VPC connectivity?
│   └─→ **VPC Peering**
│       ├─→ Private connectivity between two VPCs
│       ├─→ Same or different regions
│       ├─→ Same or different accounts
│       ├─→ Non-transitive (must peer each VPC)
│       ├─→ No overlapping CIDR blocks
│       └─→ Use case: Connect VPCs, share resources
│
├─→ Need private connection to AWS services?
│   └─→ **VPC Endpoints**
│       ├─→ Interface Endpoints (PrivateLink):
│       │   ├─→ ENI with private IP in your subnet
│       │   ├─→ Supports many AWS services
│       │   ├─→ Charged per hour + per GB
│       │   └─→ Use case: Private access to AWS services
│       │
│       └─→ Gateway Endpoints:
│           ├─→ Gateway target in route table
│           ├─→ Only S3 and DynamoDB
│           ├─→ Free
│           └─→ Use case: S3/DynamoDB from private subnet
│
├─→ Need to capture IP traffic logs?
│   └─→ **VPC Flow Logs**
│       ├─→ Capture IP traffic info
│       ├─→ Levels: VPC, Subnet, ENI
│       ├─→ Destination: CloudWatch Logs, S3, Kinesis Data Firehose
│       └─→ Use case: Troubleshooting, security analysis, monitoring
│
└─→ Need IPv6 support?
    └─→ **Egress-Only Internet Gateway**
        ├─→ IPv6 equivalent of NAT Gateway
        ├─→ Allows outbound IPv6, blocks inbound
        └─→ Use case: IPv6 internet access from private subnet
```

### Security Groups vs NACLs

| Feature | Security Groups | NACLs |
|---------|----------------|--------|
| **Level** | Instance (ENI) | Subnet |
| **State** | Stateful | Stateless |
| **Rules** | Allow only | Allow and Deny |
| **Rule Evaluation** | All rules | In order (by rule #) |
| **Applies To** | Instances opted-in | All instances in subnet |
| **Default** | Deny all inbound | Allow all |
| **Use Case** | Instance firewall | Subnet firewall, explicit deny |

---

## Hybrid Connectivity Selection

```
START: Connect on-premises to AWS?
│
├─→ Need quick setup (days)?
│   └─→ **AWS Site-to-Site VPN**
│       ├─→ Encrypted connection over internet
│       ├─→ Virtual Private Gateway (VGW) or Transit Gateway
│       ├─→ Customer Gateway (on-premises)
│       ├─→ Two tunnels for HA (using BGP)
│       ├─→ Up to 1.25 Gbps per tunnel
│       ├─→ Setup: Hours to days
│       └─→ Use case: Quick hybrid setup, backup for Direct Connect
│
├─→ Need dedicated, private connection?
│   └─→ **AWS Direct Connect**
│       ├─→ Dedicated physical connection (1 Gbps or 10 Gbps)
│       ├─→ Private, consistent network performance
│       ├─→ Reduce bandwidth costs (data transfer out)
│       ├─→ Virtual Interfaces (VIFs):
│       │   ├─→ Private VIF: Connect to VPC
│       │   └─→ Public VIF: Connect to public AWS services (S3, etc.)
│       ├─→ Setup: Weeks to months
│       ├─→ HA: Use multiple connections or VPN backup
│       └─→ Use case: Large data transfers, hybrid cloud, regulatory
│
├─→ Need Direct Connect + encryption?
│   └─→ **Direct Connect + VPN**
│       ├─→ Site-to-Site VPN over Direct Connect
│       ├─→ Private connection + encryption
│       └─→ Use case: Compliance requiring encryption
│
├─→ Need to connect to multiple VPCs?
│   └─→ **AWS Transit Gateway**
│       ├─→ Central hub for VPCs, on-premises, peering
│       ├─→ Simplifies network topology
│       ├─→ Works with VPN and Direct Connect
│       ├─→ Cross-region peering
│       └─→ Use case: Large-scale hybrid networks
│
└─→ Need global, accelerated connection?
    └─→ **AWS Global Accelerator**
        ├─→ Static anycast IPs
        ├─→ Route traffic via AWS global network
        ├─→ Improve performance (up to 60%)
        ├─→ Automatic failover
        └─→ Use case: Global applications, gaming, IoT
```

### VPN vs Direct Connect Comparison

| Feature | Site-to-Site VPN | Direct Connect |
|---------|-----------------|----------------|
| **Connection** | Over internet (encrypted) | Dedicated private line |
| **Setup Time** | Hours to days | Weeks to months |
| **Bandwidth** | Up to 1.25 Gbps per tunnel | 1 Gbps, 10 Gbps, 100 Gbps |
| **Cost** | Low | High (monthly + data out) |
| **Latency** | Variable (internet) | Consistent, low |
| **Security** | Encrypted | Private (not encrypted by default) |
| **HA** | Dual tunnels included | Need multiple connections |
| **Use Case** | Quick setup, cost-effective | High bandwidth, consistent performance |

---

## Content Delivery Decision

```
START: Need content delivery?
│
├─→ Cache static content globally?
│   └─→ **Amazon CloudFront**
│       ├─→ Content Delivery Network (CDN)
│       ├─→ 400+ edge locations globally
│       ├─→ Caches content close to users
│       ├─→ Origin: S3, ALB, EC2, custom HTTP
│       ├─→ Features:
│       │   ├─→ DDoS protection (Shield integration)
│       │   ├─→ SSL/TLS (HTTPS)
│       │   ├─→ Geo-restriction
│       │   ├─→ Lambda@Edge (run code at edge)
│       │   └─→ Signed URLs/Cookies (private content)
│       └─→ Use case: Website acceleration, video streaming, software distribution
│
├─→ Accelerate uploads to S3?
│   └─→ **S3 Transfer Acceleration**
│       ├─→ Upload to nearest edge location
│       ├─→ Transfer via AWS backbone network
│       └─→ Use case: Global uploads, large files
│
├─→ Accelerate application traffic globally?
│   └─→ **AWS Global Accelerator**
│       ├─→ Two static anycast IPs
│       ├─→ Route via AWS global network
│       ├─→ Endpoint: ALB, NLB, EC2, Elastic IP
│       ├─→ Health checks and failover
│       ├─→ No caching (routes directly to app)
│       └─→ Use case: Gaming, IoT, VoIP, non-HTTP traffic
│
└─→ CloudFront vs Global Accelerator?
    ├─→ Need caching → CloudFront
    ├─→ Need HTTP/HTTPS only → CloudFront
    ├─→ Need dynamic content, no caching → Global Accelerator
    ├─→ Need TCP/UDP → Global Accelerator
    └─→ Need static IPs → Global Accelerator
```

### CloudFront vs Global Accelerator

| Feature | CloudFront | Global Accelerator |
|---------|------------|-------------------|
| **Purpose** | CDN (caching) | Network accelerator |
| **Caching** | Yes | No |
| **Protocols** | HTTP, HTTPS, WebSocket | TCP, UDP, HTTP, HTTPS |
| **IPs** | Dynamic (edge locations) | Static (2 anycast IPs) |
| **Use Case** | Static content, videos | Dynamic apps, gaming, non-HTTP |
| **Latency** | Reduced via caching | Reduced via AWS network |
| **Failover** | Origin failover | Health checks, multi-region |

---

## API Gateway Decision

```
START: Exposing APIs?
│
├─→ What type of API?
│   ├─→ RESTful API (full features)?
│   │   └─→ **REST API**
│   │       ├─→ Full API Gateway features
│   │       ├─→ API keys, request validation, caching
│   │       ├─→ Usage plans, throttling
│   │       └─→ Use case: Complex APIs, need all features
│   │
│   ├─→ HTTP API (simple, low cost)?
│   │   └─→ **HTTP API**
│   │       ├─→ Simpler, faster, cheaper than REST
│   │       ├─→ JWT authorizers, CORS
│   │       ├─→ No API keys, caching
│   │       └─→ Use case: Serverless backends, cost optimization
│   │
│   └─→ WebSocket (bidirectional)?
│       └─→ **WebSocket API**
│           ├─→ Persistent connections
│           ├─→ Real-time two-way communication
│           └─→ Use case: Chat apps, real-time dashboards
│
├─→ Backend integration?
│   ├─→ AWS Lambda → Serverless backend
│   ├─→ HTTP endpoints → Proxy to any HTTP endpoint
│   ├─→ AWS services → Direct integration (DynamoDB, S3, SNS, etc.)
│   └─→ Mock → Testing without backend
│
└─→ Features needed?
    ├─→ Caching → REST API only
    ├─→ API keys, usage plans → REST API only
    ├─→ Request/response transformation → Both
    ├─→ Custom domain → Both
    └─→ Throttling, quotas → Both
```

---

## Exam Scenarios - Quick Answers

### Scenario 1: Distribute HTTP traffic to EC2 instances based on URL path
**Answer**: Application Load Balancer (ALB)

### Scenario 2: Extreme performance, millions req/sec, TCP traffic
**Answer**: Network Load Balancer (NLB)

### Scenario 3: Deploy 3rd-party firewall appliances
**Answer**: Gateway Load Balancer (GWLB)

### Scenario 4: Global website, route users to closest region
**Answer**: Route 53 Latency-Based Routing

### Scenario 5: Disaster recovery, active-passive failover
**Answer**: Route 53 Failover Routing

### Scenario 6: A/B testing, 10% to new version
**Answer**: Route 53 Weighted Routing

### Scenario 7: Private subnet needs internet access for updates
**Answer**: NAT Gateway (or NAT Instance for cost savings)

### Scenario 8: Connect on-premises to AWS, need quick setup
**Answer**: Site-to-Site VPN

### Scenario 9: Large data transfers, consistent performance, private
**Answer**: AWS Direct Connect

### Scenario 10: Cache static content globally
**Answer**: Amazon CloudFront

### Scenario 11: Accelerate dynamic app traffic globally
**Answer**: AWS Global Accelerator

### Scenario 12: Private access to S3 from private subnet
**Answer**: VPC Gateway Endpoint (for S3)

### Scenario 13: Connect 100 VPCs together
**Answer**: AWS Transit Gateway

### Scenario 14: Block specific IP address from subnet
**Answer**: Network ACL (NACL) with deny rule

### Scenario 15: Serverless REST API backed by Lambda
**Answer**: API Gateway (HTTP API for cost, REST API for features)

---

## 🎯 Key Takeaways

### Load Balancers:
- **ALB**: Layer 7, HTTP/HTTPS, path/host routing, Lambda targets
- **NLB**: Layer 4, TCP/UDP, ultra-performance, static IP
- **GWLB**: Layer 3, 3rd-party appliances, security
- **CLB**: Legacy, avoid for new applications

### Route 53 Routing:
- **Simple**: Single resource
- **Weighted**: Percentage-based traffic split
- **Latency**: Best performance globally
- **Failover**: DR, active-passive
- **Geolocation**: Route by user location

### VPC Security:
- **Security Groups**: Stateful, instance-level, allow only
- **NACLs**: Stateless, subnet-level, allow and deny
- **Both**: Use together for defense in depth

### Hybrid Connectivity:
- **VPN**: Quick, over internet, encrypted
- **Direct Connect**: Dedicated, consistent, private
- **Transit Gateway**: Connect many VPCs and on-premises

### Content Delivery:
- **CloudFront**: CDN, caching, global distribution
- **Global Accelerator**: Network acceleration, static IPs, no caching
- **S3 Transfer Acceleration**: Faster S3 uploads

---

*Use these decision trees for quick networking decisions during the exam!*
