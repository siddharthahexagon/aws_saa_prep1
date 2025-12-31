# AWS SAA-C03 CHEATSHEET: VPC & NETWORKING

## 📋 PRINT-READY EXAM REFERENCE

---

# 🌐 AMAZON VPC (VIRTUAL PRIVATE CLOUD)

## Definition
Logically isolated virtual network where you launch AWS resources.

## VPC Components

| Component | Description |
|-----------|-------------|
| **VPC** | Virtual network (CIDR block) |
| **Subnet** | Segment of VPC in one AZ |
| **Route Table** | Rules for traffic routing |
| **Internet Gateway** | Connect VPC to internet |
| **NAT Gateway** | Internet for private subnets |
| **Security Group** | Instance-level firewall |
| **NACL** | Subnet-level firewall |
| **VPC Peering** | Connect VPCs |
| **VPN** | Encrypted connection to on-prem |
| **Direct Connect** | Dedicated connection to on-prem |

## VPC CIDR Rules

| Rule | Description |
|------|-------------|
| **CIDR range** | /16 (65,536 IPs) to /28 (16 IPs) |
| **Max CIDRs** | 5 per VPC (can request more) |
| **Reserved IPs** | 5 per subnet (.0, .1, .2, .3, .255) |
| **Private ranges** | 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 |

### Example CIDR Calculation
```
10.0.0.0/24 = 256 IPs - 5 reserved = 251 usable
10.0.0.0/16 = 65,536 IPs
10.0.0.0/28 = 16 IPs - 5 reserved = 11 usable
```

## Subnet Types

| Type | Internet Access | Use Case |
|------|-----------------|----------|
| **Public** | Via IGW | Web servers, bastion hosts |
| **Private** | No direct (NAT for outbound) | Databases, app servers |

### Making a Subnet Public
1. Create Internet Gateway (IGW)
2. Attach IGW to VPC
3. Add route: `0.0.0.0/0 → IGW`
4. Assign public IP to instances

---

# 🚪 GATEWAYS & NAT

## Internet Gateway (IGW)

| Feature | Description |
|---------|-------------|
| **Purpose** | Connect VPC to internet |
| **HA** | Automatically highly available |
| **Limit** | 1 per VPC |
| **Scaling** | Scales automatically |

## NAT Gateway

| Feature | Description |
|---------|-------------|
| **Purpose** | Internet access for private subnets |
| **Direction** | Outbound only (no inbound) |
| **AZ** | Single AZ (deploy per AZ for HA) |
| **Bandwidth** | 45 Gbps |
| **Protocol** | TCP, UDP, ICMP |

### NAT Gateway Setup
1. Create in **public subnet**
2. Assign Elastic IP
3. Add route in private subnet: `0.0.0.0/0 → NAT-GW`

### NAT Gateway vs NAT Instance

| Feature | NAT Gateway | NAT Instance |
|---------|-------------|--------------|
| **Managed** | Yes | No |
| **Bandwidth** | 45 Gbps | Instance-dependent |
| **HA** | Per AZ | Manual |
| **Security Groups** | No | Yes |
| **Bastion** | No | Can use as bastion |
| **Cost** | Higher | Lower |

## Egress-Only Internet Gateway

| Feature | Description |
|---------|-------------|
| **Purpose** | Outbound IPv6 only |
| **Use case** | Private subnets needing IPv6 internet |
| **Stateful** | Allows return traffic |

---

# 🔒 SECURITY GROUPS & NACLs

## Security Groups

| Feature | Description |
|---------|-------------|
| **Level** | Instance (ENI) level |
| **State** | Stateful (return traffic auto-allowed) |
| **Rules** | Allow only (no deny) |
| **Default** | All inbound denied, all outbound allowed |
| **Evaluation** | All rules evaluated |
| **Reference** | Can reference other SGs |

### Security Group Rules

| Direction | Default |
|-----------|---------|
| **Inbound** | All denied |
| **Outbound** | All allowed |

### Security Group Reference
```
Web SG → allows traffic from ALB SG (not IP)
ALB SG → allows 443 from 0.0.0.0/0
```

## Network ACLs (NACLs)

| Feature | Description |
|---------|-------------|
| **Level** | Subnet level |
| **State** | Stateless (must allow return traffic) |
| **Rules** | Allow and Deny |
| **Default** | All traffic allowed |
| **Evaluation** | Rules evaluated in order (lowest first) |
| **Rule numbers** | 1-32766 (lower = higher priority) |

### Default NACL vs Custom NACL

| Type | Default Behavior |
|------|------------------|
| **Default NACL** | Allows all inbound and outbound |
| **Custom NACL** | Denies all inbound and outbound |

### NACL Best Practices
- Use rule numbers: 100, 200, 300... (leave gaps)
- Remember **ephemeral ports** for return traffic (1024-65535)
- Last rule is always `*` = DENY ALL

## Security Group vs NACL

| Feature | Security Group | NACL |
|---------|----------------|------|
| **Level** | Instance | Subnet |
| **State** | Stateful | Stateless |
| **Rules** | Allow only | Allow & Deny |
| **Default** | Deny all in | Allow all (default NACL) |
| **Evaluation** | All rules | First match |

---

# 🔗 VPC PEERING

## Definition
Network connection between two VPCs using private IPs.

## VPC Peering Features

| Feature | Description |
|---------|-------------|
| **Cross-account** | Yes |
| **Cross-region** | Yes |
| **Transitive** | NO (A↔B, B↔C doesn't mean A↔C) |
| **Overlapping CIDR** | NOT allowed |
| **Encryption** | Traffic encrypted (cross-region) |

### VPC Peering Setup
1. Create peering connection (requester)
2. Accept peering connection (accepter)
3. Update route tables in BOTH VPCs
4. Update security groups

---

# 🚇 VPC ENDPOINTS

## Definition
Private connection to AWS services without using internet.

## Endpoint Types

| Type | Description | Services |
|------|-------------|----------|
| **Gateway Endpoint** | Free, route table entry | S3, DynamoDB only |
| **Interface Endpoint** | ENI with private IP (PrivateLink) | Most AWS services |
| **Gateway LB Endpoint** | For third-party appliances | Firewalls, IDS |

### Gateway Endpoint
- **Free**
- Add route to route table
- Only S3 and DynamoDB
- Per VPC

### Interface Endpoint (PrivateLink)
- **Charged** per hour + data
- Creates ENI in subnet
- Use private DNS
- Security groups apply
- Can access across VPC peering, VPN, Direct Connect

## VPC Endpoint Policies
- Control access to the service
- JSON policy document
- Independent of IAM policies

---

# 🌉 AWS TRANSIT GATEWAY

## Definition
Regional network transit hub connecting VPCs and on-premises networks.

## Transit Gateway Features

| Feature | Description |
|---------|-------------|
| **Hub-and-spoke** | Central connectivity |
| **Attachments** | VPCs, VPN, Direct Connect, peering |
| **Route tables** | Control traffic flow |
| **Cross-region** | Peering between TGWs |
| **Cross-account** | Share via RAM |
| **Multicast** | Supported |
| **Bandwidth** | 50 Gbps per attachment |

### Transit Gateway Use Cases
- Connect 100s of VPCs
- Centralized VPN hub
- Shared services VPC
- Network segmentation

### Transit Gateway vs VPC Peering

| Feature | Transit Gateway | VPC Peering |
|---------|-----------------|-------------|
| **Scale** | 1000s of VPCs | Few VPCs |
| **Transitive** | Yes | No |
| **Cost** | Higher | Lower |
| **Central mgmt** | Yes | No |

---

# 🔐 VPN & DIRECT CONNECT

## Site-to-Site VPN

| Feature | Description |
|---------|-------------|
| **Connection** | Over public internet |
| **Encryption** | IPsec |
| **Bandwidth** | Up to 1.25 Gbps |
| **Setup time** | Minutes |
| **Components** | Virtual Private Gateway + Customer Gateway |

### VPN Components

| Component | Description |
|-----------|-------------|
| **VGW** | AWS side endpoint |
| **CGW** | Customer side (on-prem device) |
| **VPN Connection** | Two tunnels for HA |

### VPN Acceleration
- Use **AWS Global Accelerator** for VPN
- Reduces latency
- Uses AWS backbone

## Direct Connect (DX)

| Feature | Description |
|---------|-------------|
| **Connection** | Dedicated private connection |
| **Bandwidth** | 1 Gbps to 100 Gbps |
| **Setup time** | Weeks to months |
| **Encryption** | Not encrypted by default |
| **Use case** | Large data transfer, consistent latency |

### Direct Connect Components

| Component | Description |
|-----------|-------------|
| **Connection** | Physical connection at DX location |
| **Virtual Interface** | VIF - access AWS services |
| **Public VIF** | Access public AWS services |
| **Private VIF** | Access VPC |
| **Transit VIF** | Access Transit Gateway |

### Direct Connect Gateway
- Connect DX to multiple VPCs in different regions
- Single connection, multiple VPCs

### Direct Connect Encryption
- Not encrypted by default
- Use VPN over DX for encryption
- Or use MACsec for Layer 2 encryption

### Direct Connect Resiliency

| Level | Description |
|-------|-------------|
| **Maximum** | Separate DX locations + devices |
| **High** | Separate devices at one location |
| **Standard** | Single connection |

### VPN as Backup
- Configure VPN as backup to DX
- Lower priority route
- Automatic failover

## VPN vs Direct Connect

| Feature | VPN | Direct Connect |
|---------|-----|----------------|
| **Connection** | Internet | Dedicated |
| **Bandwidth** | ~1.25 Gbps | Up to 100 Gbps |
| **Latency** | Variable | Consistent |
| **Setup** | Minutes | Weeks/months |
| **Cost** | Lower | Higher |
| **Encryption** | Yes | Optional |

---

# 🌍 ROUTE 53

## Definition
Scalable DNS and domain name registration service.

## Route 53 Features

| Feature | Description |
|---------|-------------|
| **DNS** | Domain Name System |
| **Domain registration** | Register domains |
| **Health checks** | Monitor endpoint health |
| **Traffic flow** | Visual routing editor |

## Record Types

| Type | Description |
|------|-------------|
| **A** | IPv4 address |
| **AAAA** | IPv6 address |
| **CNAME** | Canonical name (alias to another domain) |
| **ALIAS** | AWS-specific, points to AWS resources |
| **MX** | Mail server |
| **NS** | Name servers |
| **TXT** | Text records |
| **SRV** | Service records |
| **PTR** | Reverse DNS |

### CNAME vs ALIAS

| Feature | CNAME | ALIAS |
|---------|-------|-------|
| **Zone apex** | Not supported | Supported |
| **Targets** | Any domain | AWS resources only |
| **Cost** | DNS queries charged | Free for AWS resources |
| **TTL** | User-defined | AWS-defined |

## Routing Policies

| Policy | Description | Use Case |
|--------|-------------|----------|
| **Simple** | Single resource | Basic routing |
| **Weighted** | % of traffic to each resource | Blue/green, A/B testing |
| **Latency** | Route to lowest latency region | Global apps |
| **Failover** | Active-passive | DR |
| **Geolocation** | Route by user location | Localization, compliance |
| **Geoproximity** | Route by distance + bias | Shift traffic between regions |
| **Multi-value** | Multiple healthy resources | Simple load balancing |
| **IP-based** | Route by client IP | Specific user routing |

### Health Checks

| Type | Description |
|------|-------------|
| **Endpoint** | Monitor HTTP/HTTPS/TCP endpoint |
| **Calculated** | Combine multiple health checks |
| **CloudWatch Alarm** | Based on CloudWatch metrics |

### Health Check Settings
- Interval: 10 or 30 seconds
- Threshold: Number of failures before unhealthy
- Regions: Choose checker regions

## Route 53 Resolver

| Feature | Description |
|---------|-------------|
| **Inbound Endpoint** | On-prem → AWS DNS |
| **Outbound Endpoint** | AWS → On-prem DNS |
| **Rules** | Forward queries to specific resolvers |

---

# ☁️ CLOUDFRONT

## Definition
Global Content Delivery Network (CDN) with edge locations.

## CloudFront Components

| Component | Description |
|-----------|-------------|
| **Distribution** | Configuration for content delivery |
| **Origin** | Source of content (S3, ALB, EC2, custom) |
| **Edge Location** | Cache location near users |
| **Regional Edge Cache** | Larger cache between origin and edge |

## CloudFront Origins

| Origin | Description |
|--------|-------------|
| **S3 Bucket** | Static content |
| **S3 Website** | Static website |
| **ALB/NLB** | Load balancer |
| **EC2** | Direct to instance |
| **Custom HTTP** | Any HTTP server |
| **MediaStore** | Video streaming |

## CloudFront Security

| Feature | Description |
|---------|-------------|
| **OAC** | Origin Access Control (S3) |
| **OAI** | Origin Access Identity (legacy) |
| **Signed URLs** | Time-limited access to single file |
| **Signed Cookies** | Time-limited access to multiple files |
| **Field-Level Encryption** | Encrypt specific POST fields |
| **WAF** | Web Application Firewall integration |

### OAC vs OAI
- **OAC** = Origin Access Control (recommended, new)
- **OAI** = Origin Access Identity (legacy)
- Both restrict S3 access to CloudFront only

### Signed URLs vs Signed Cookies

| Feature | Signed URLs | Signed Cookies |
|---------|-------------|----------------|
| **Access** | Single file | Multiple files |
| **URL change** | Yes | No |
| **Use case** | Individual downloads | Streaming, multiple files |

## CloudFront Cache

| Feature | Description |
|---------|-------------|
| **TTL** | Time to live (default 24 hours) |
| **Cache Key** | What determines cache hit |
| **Cache Policy** | Control what's in cache key |
| **Origin Request Policy** | What headers sent to origin |
| **Invalidation** | Remove cached content |

### Cache Invalidation
- `/*` = invalidate all
- `/images/*` = invalidate images folder
- Charged per path
- Use versioned file names instead

## CloudFront Functions vs Lambda@Edge

| Feature | CloudFront Functions | Lambda@Edge |
|---------|---------------------|-------------|
| **Runtime** | JavaScript | Node.js, Python |
| **Max duration** | < 1 ms | 5-30 seconds |
| **Max memory** | 2 MB | 128-10,240 MB |
| **Network access** | No | Yes |
| **Triggers** | Viewer only | Viewer + Origin |
| **Cost** | Lower | Higher |
| **Use case** | Simple, high volume | Complex logic |

### CloudFront Triggers

| Trigger | Location |
|---------|----------|
| **Viewer Request** | Edge, before cache check |
| **Viewer Response** | Edge, after cache |
| **Origin Request** | Before origin fetch |
| **Origin Response** | After origin response |

## Geo Restriction

| Type | Description |
|------|-------------|
| **Allowlist** | Allow only specific countries |
| **Blocklist** | Block specific countries |

---

# 🌐 GLOBAL ACCELERATOR

## Definition
Network service that improves availability and performance using AWS global network.

## Global Accelerator Features

| Feature | Description |
|---------|-------------|
| **Anycast IPs** | 2 static IPs |
| **Health checks** | Automatic failover |
| **Endpoints** | ALB, NLB, EC2, Elastic IP |
| **Use case** | Non-HTTP, gaming, IoT |

## Global Accelerator vs CloudFront

| Feature | Global Accelerator | CloudFront |
|---------|-------------------|------------|
| **Type** | Network layer | CDN |
| **Caching** | No | Yes |
| **Protocol** | TCP/UDP | HTTP/HTTPS |
| **Static IP** | Yes | No |
| **Use case** | Gaming, IoT, VoIP | Web content |

---

# 🎯 NETWORKING QUICK EXAM HEURISTICS

| Keyword/Scenario | Answer |
|------------------|--------|
| Private to internet | **NAT Gateway** |
| IPv6 outbound only | **Egress-only IGW** |
| Connect two VPCs | **VPC Peering** |
| Connect 100s of VPCs | **Transit Gateway** |
| Private access to S3 | **Gateway Endpoint** |
| Private access to other services | **Interface Endpoint** |
| Dedicated network connection | **Direct Connect** |
| Quick VPN setup | **Site-to-Site VPN** |
| Encrypt Direct Connect | **VPN over DX** |
| Lowest latency routing | **Route 53 Latency** |
| Route by user location | **Route 53 Geolocation** |
| Failover routing | **Route 53 Failover** |
| CDN, cache static content | **CloudFront** |
| Static IPs, TCP/UDP | **Global Accelerator** |
| Secure S3 via CloudFront | **OAC + Bucket Policy** |
| Instance-level firewall | **Security Group** |
| Subnet-level firewall | **NACL** |
| Block specific IPs | **NACL (deny rules)** |

---

# ✅ EXAM DAY QUICK CHECKS

```
□ Know VPC CIDR rules and reserved IPs
□ Know Security Group vs NACL differences
□ Know Gateway vs Interface Endpoints
□ Know VPN vs Direct Connect trade-offs
□ Know Route 53 routing policies
□ Know CloudFront origins and security
□ Know Transit Gateway vs VPC Peering
□ Know NAT Gateway vs NAT Instance
```

---

*Last Updated: December 2025*
*Exam Version: SAA-C03*
