# AWS SAA-C03 Hands-On Labs: Networking & Content Delivery

## 📋 Overview
These labs cover VPC, Route 53, CloudFront, ELB, and networking services.

**Estimated Time:** 5-6 hours total  
**Cost Estimate:** $2-5 (NAT Gateway is expensive - cleanup quickly!)  
**Free Tier Eligible:** Partially

---

## Lab 4.1: VPC from Scratch

### Objectives
- Create a custom VPC with public and private subnets
- Configure Internet Gateway and NAT Gateway
- Set up route tables
- Implement Network ACLs

### Steps

#### Part A: Create VPC

**Step 1: Create VPC**
```
1. Go to VPC → Your VPCs → Create VPC
2. Resources to create: VPC only
3. Name tag: "lab-vpc"
4. IPv4 CIDR: 10.0.0.0/16
5. IPv6 CIDR: No IPv6 CIDR block
6. Tenancy: Default
7. Create VPC
```

**Step 2: Enable DNS Settings**
```
1. Select lab-vpc
2. Actions → Edit VPC settings
3. Enable DNS hostnames: Yes
4. Enable DNS resolution: Yes (should be enabled)
5. Save
```

#### Part B: Create Subnets

**Step 3: Create Public Subnets**
```
Create Subnet 1:
1. VPC → Subnets → Create subnet
2. VPC: lab-vpc
3. Subnet name: "lab-public-1a"
4. Availability Zone: us-east-1a
5. IPv4 CIDR: 10.0.1.0/24
6. Create subnet

Create Subnet 2:
1. Create another subnet
2. Subnet name: "lab-public-1b"
3. Availability Zone: us-east-1b
4. IPv4 CIDR: 10.0.2.0/24
```

**Step 4: Create Private Subnets**
```
Create Subnet 3:
1. Create subnet
2. Subnet name: "lab-private-1a"
3. Availability Zone: us-east-1a
4. IPv4 CIDR: 10.0.3.0/24

Create Subnet 4:
1. Create subnet
2. Subnet name: "lab-private-1b"
3. Availability Zone: us-east-1b
4. IPv4 CIDR: 10.0.4.0/24
```

**Step 5: Enable Auto-assign Public IP**
```
For public subnets only:
1. Select lab-public-1a
2. Actions → Edit subnet settings
3. Enable auto-assign public IPv4 address: Yes
4. Save
5. Repeat for lab-public-1b
```

#### Part C: Create Internet Gateway

**Step 6: Create and Attach IGW**
```
1. VPC → Internet Gateways → Create internet gateway
2. Name: "lab-igw"
3. Create
4. Select lab-igw
5. Actions → Attach to VPC
6. Select lab-vpc
7. Attach
```

#### Part D: Create NAT Gateway

**Step 7: Allocate Elastic IP**
```
1. VPC → Elastic IPs → Allocate Elastic IP address
2. Network Border Group: us-east-1
3. Tags: Name=lab-nat-eip
4. Allocate
```

**Step 8: Create NAT Gateway**
```
1. VPC → NAT Gateways → Create NAT gateway
2. Name: "lab-nat-gw"
3. Subnet: lab-public-1a (must be public subnet!)
4. Elastic IP allocation ID: Select lab-nat-eip
5. Create NAT gateway
6. Wait for state: Available
```

#### Part E: Configure Route Tables

**Step 9: Create Public Route Table**
```
1. VPC → Route Tables → Create route table
2. Name: "lab-public-rt"
3. VPC: lab-vpc
4. Create

Add route:
1. Select lab-public-rt → Routes → Edit routes
2. Add route:
   - Destination: 0.0.0.0/0
   - Target: Internet Gateway → lab-igw
3. Save changes
```

**Step 10: Associate Public Subnets**
```
1. Select lab-public-rt
2. Subnet associations → Edit subnet associations
3. Select: lab-public-1a, lab-public-1b
4. Save associations
```

**Step 11: Create Private Route Table**
```
1. Create route table
2. Name: "lab-private-rt"
3. VPC: lab-vpc
4. Create

Add route:
1. Edit routes
2. Add route:
   - Destination: 0.0.0.0/0
   - Target: NAT Gateway → lab-nat-gw
3. Save changes

Associate:
1. Edit subnet associations
2. Select: lab-private-1a, lab-private-1b
3. Save
```

#### Part F: Test Connectivity

**Step 12: Launch Test Instances**
```
Launch Public Instance:
1. EC2 → Launch instance
2. Name: "lab-public-instance"
3. AMI: Amazon Linux 2023
4. Instance type: t2.micro
5. Network settings:
   - VPC: lab-vpc
   - Subnet: lab-public-1a
   - Auto-assign public IP: Enable
   - Create security group: lab-public-sg
   - Allow SSH from My IP
6. Launch

Launch Private Instance:
1. Launch another instance
2. Name: "lab-private-instance"
3. Network settings:
   - VPC: lab-vpc
   - Subnet: lab-private-1a
   - Auto-assign public IP: Disable
   - Security group: Create new (lab-private-sg)
   - Allow SSH from 10.0.0.0/16 (VPC CIDR)
4. Launch
```

**Step 13: Test Public Instance**
```bash
# Connect via EC2 Instance Connect or SSH
ssh -i my-lab-keypair.pem ec2-user@[PUBLIC-IP]

# Test internet
ping google.com
curl http://example.com
```

**Step 14: Test Private Instance**
```bash
# From public instance, SSH to private instance
# First, copy key pair to public instance or use agent forwarding

# Option 1: Copy key (not recommended for production)
# On your local machine:
scp -i my-lab-keypair.pem my-lab-keypair.pem ec2-user@[PUBLIC-IP]:~/

# On public instance:
chmod 400 my-lab-keypair.pem
ssh -i my-lab-keypair.pem ec2-user@[PRIVATE-IP]

# Test internet via NAT Gateway
ping google.com
curl http://example.com
```

#### Part G: Network ACLs

**Step 15: Create Custom NACL**
```
1. VPC → Network ACLs → Create network ACL
2. Name: "lab-private-nacl"
3. VPC: lab-vpc
4. Create
```

**Step 16: Configure NACL Rules**
```
Inbound Rules:
1. Select lab-private-nacl → Inbound rules → Edit
2. Add rules:
   - Rule 100: Allow SSH (22) from 10.0.0.0/16
   - Rule 110: Allow HTTP (80) from 0.0.0.0/0
   - Rule 120: Allow HTTPS (443) from 0.0.0.0/0
   - Rule 130: Allow Ephemeral ports (1024-65535) from 0.0.0.0/0
3. Save

Outbound Rules:
1. Edit outbound rules
2. Add rules:
   - Rule 100: Allow HTTP (80) to 0.0.0.0/0
   - Rule 110: Allow HTTPS (443) to 0.0.0.0/0
   - Rule 120: Allow Ephemeral ports (1024-65535) to 10.0.0.0/16
3. Save
```

**Step 17: Associate NACL**
```
1. Subnet associations → Edit
2. Select lab-private-1a, lab-private-1b
3. Save
```

### Cleanup for Lab 4.1
```
1. Terminate EC2 instances
2. Delete NAT Gateway (wait until deleted)
3. Release Elastic IP
4. Delete Network ACLs (except default)
5. Delete route tables (except main)
6. Delete subnets
7. Detach and delete Internet Gateway
8. Delete VPC
```

---

## Lab 4.2: VPC Peering

### Objectives
- Create VPC peering connection
- Configure routing between VPCs
- Test cross-VPC communication

### Steps

#### Part A: Create Second VPC

**Step 1: Create Second VPC**
```
1. Create VPC: "lab-vpc-2"
2. CIDR: 10.1.0.0/16 (non-overlapping!)
3. Enable DNS hostnames

Create Subnet:
1. Name: "lab-vpc2-subnet"
2. CIDR: 10.1.1.0/24
3. AZ: us-east-1a
4. Enable auto-assign public IP

Create IGW:
1. Name: "lab-igw-2"
2. Attach to lab-vpc-2

Create Route Table:
1. Name: "lab-vpc2-rt"
2. Add route: 0.0.0.0/0 → lab-igw-2
3. Associate with lab-vpc2-subnet
```

#### Part B: Create VPC Peering

**Step 2: Create Peering Connection**
```
1. VPC → Peering Connections → Create peering connection
2. Name: "lab-vpc-peering"
3. VPC (Requester): lab-vpc
4. VPC (Accepter): My account, This region
5. VPC ID: lab-vpc-2
6. Create peering connection
```

**Step 3: Accept Peering Request**
```
1. Select the peering connection
2. Actions → Accept request
3. Accept
```

**Step 4: Update Route Tables**
```
Update lab-vpc route tables:
1. Edit lab-public-rt
2. Add route:
   - Destination: 10.1.0.0/16
   - Target: Peering Connection → lab-vpc-peering
3. Save

Update lab-vpc-2 route table:
1. Edit lab-vpc2-rt
2. Add route:
   - Destination: 10.0.0.0/16
   - Target: Peering Connection → lab-vpc-peering
3. Save
```

#### Part C: Test Peering

**Step 5: Launch Instance in VPC 2**
```
1. Launch instance in lab-vpc2-subnet
2. Name: "lab-vpc2-instance"
3. Security group: Allow SSH and ICMP from 10.0.0.0/8
```

**Step 6: Test Connectivity**
```bash
# From lab-public-instance (VPC 1)
ping [Private IP of lab-vpc2-instance]
```

### Cleanup for Lab 4.2
```
1. Terminate instances
2. Delete peering connection
3. Remove peering routes from route tables
4. Delete VPC 2 components (IGW, subnet, route table, VPC)
```

---

## Lab 4.3: Application Load Balancer

### Objectives
- Create Application Load Balancer
- Configure target groups
- Set up path-based routing

### Steps

#### Part A: Prepare Environment

**Step 1: Launch Web Servers**
```
Launch 2 EC2 instances in lab-vpc public subnets:

Instance 1 (us-east-1a):
- Name: "web-server-1"
- User data:
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<h1>Web Server 1 - AZ A</h1>" > /var/www/html/index.html
mkdir /var/www/html/api
echo '{"server": "web-1", "status": "ok"}' > /var/www/html/api/health

Instance 2 (us-east-1b):
- Name: "web-server-2"
- User data:
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<h1>Web Server 2 - AZ B</h1>" > /var/www/html/index.html
mkdir /var/www/html/api
echo '{"server": "web-2", "status": "ok"}' > /var/www/html/api/health
```

**Step 2: Create Security Groups**
```
ALB Security Group:
1. Name: "lab-alb-sg"
2. Inbound: HTTP (80) from 0.0.0.0/0
3. Inbound: HTTPS (443) from 0.0.0.0/0

Web Server Security Group:
1. Name: "lab-web-sg"
2. Inbound: HTTP (80) from lab-alb-sg
3. Inbound: SSH from My IP
```

#### Part B: Create Target Group

**Step 3: Create Target Group**
```
1. EC2 → Target Groups → Create target group
2. Target type: Instances
3. Target group name: "lab-tg-web"
4. Protocol: HTTP, Port: 80
5. VPC: lab-vpc
6. Health check path: /
7. Next
8. Register targets:
   - Select web-server-1 and web-server-2
   - Click "Include as pending below"
9. Create target group
```

#### Part C: Create ALB

**Step 4: Create Application Load Balancer**
```
1. EC2 → Load Balancers → Create Load Balancer
2. Select: Application Load Balancer
3. Name: "lab-alb"
4. Scheme: Internet-facing
5. IP address type: IPv4
6. Network mapping:
   - VPC: lab-vpc
   - Mappings: us-east-1a (lab-public-1a), us-east-1b (lab-public-1b)
7. Security groups: lab-alb-sg
8. Listeners:
   - HTTP:80 → Forward to lab-tg-web
9. Create load balancer
```

**Step 5: Test Load Balancer**
```
1. Wait for ALB state: Active
2. Copy DNS name
3. Open in browser
4. Refresh multiple times
5. You should see alternating responses from Server 1 and Server 2
```

#### Part D: Path-Based Routing

**Step 6: Create API Target Group**
```
1. Create target group
2. Name: "lab-tg-api"
3. Same instances as before
4. Health check path: /api/health
```

**Step 7: Add Listener Rules**
```
1. Select lab-alb → Listeners
2. Select HTTP:80 → View/edit rules
3. Add rule:
   - IF Path is /api/*
   - THEN Forward to lab-tg-api
4. Save
```

**Step 8: Test Path Routing**
```
curl http://[ALB-DNS-NAME]/
curl http://[ALB-DNS-NAME]/api/health
```

#### Part E: Sticky Sessions

**Step 9: Enable Stickiness**
```
1. Go to Target Groups → lab-tg-web
2. Attributes → Edit
3. Stickiness: Enable
4. Stickiness type: Load balancer generated cookie
5. Stickiness duration: 1 day
6. Save changes
```

**Step 10: Test Stickiness**
```
# In browser, refresh multiple times
# Should now always hit the same server
# Check cookies in browser developer tools
```

### Cleanup for Lab 4.3
```
1. Delete load balancer
2. Delete target groups
3. Terminate EC2 instances
4. Delete security groups
```

---

## Lab 4.4: Route 53 DNS

### Objectives
- Create hosted zone
- Configure DNS records
- Implement health checks

### Steps

#### Part A: Create Hosted Zone

**Step 1: Create Public Hosted Zone**
```
1. Go to Route 53 → Hosted zones
2. Create hosted zone
3. Domain name: Use a domain you own, or use for testing:
   - example: "lab.internal" (won't work publicly)
4. Type: Public hosted zone
5. Create
```

**Note:** For testing without a real domain, use Private Hosted Zone with your VPC.

#### Part B: Create Private Hosted Zone

**Step 2: Create Private Zone**
```
1. Create hosted zone
2. Domain name: "lab.private"
3. Type: Private hosted zone
4. VPC: lab-vpc
5. Create
```

**Step 3: Add Records**
```
A Record:
1. Create record
2. Record name: "web"
3. Record type: A
4. Value: [Private IP of web-server-1]
5. Create

CNAME Record:
1. Create record
2. Record name: "www"
3. Record type: CNAME
4. Value: web.lab.private
5. Create
```

**Step 4: Test DNS Resolution**
```bash
# From EC2 instance in lab-vpc
nslookup web.lab.private
nslookup www.lab.private
```

#### Part C: Health Checks and Failover

**Step 5: Create Health Checks**
```
1. Route 53 → Health checks → Create health check
2. Name: "web-server-1-health"
3. What to monitor: Endpoint
4. Endpoint:
   - Protocol: HTTP
   - IP address: [Public IP of web-server-1]
   - Port: 80
   - Path: /
5. Advanced:
   - Request interval: Fast (10 seconds)
   - Failure threshold: 2
6. Create

Repeat for web-server-2
```

**Step 6: Create Failover Record Set**
```
Primary Record:
1. Create record
2. Record name: "app"
3. Record type: A
4. Routing policy: Failover
5. Failover record type: Primary
6. Value: [IP of web-server-1]
7. Health check: web-server-1-health
8. Record ID: primary
9. Create

Secondary Record:
1. Create record
2. Same settings but:
   - Failover record type: Secondary
   - Value: [IP of web-server-2]
   - Health check: web-server-2-health
   - Record ID: secondary
```

**Step 7: Test Failover**
```bash
# Stop web-server-1
# Wait for health check to fail (2-3 minutes)
# DNS should now resolve to web-server-2

nslookup app.lab.private
```

### Cleanup for Lab 4.4
```
1. Delete all records in hosted zones
2. Delete health checks
3. Delete hosted zones
```

---

## Lab 4.5: CloudFront Distribution

### Objectives
- Create CloudFront distribution
- Configure caching behaviors
- Set up custom error pages

### Steps

#### Part A: Create S3 Origin

**Step 1: Create S3 Bucket**
```
1. Create S3 bucket: "lab-cloudfront-origin-[account-id]"
2. Region: us-east-1
3. Unblock public access
4. Enable static website hosting
5. Index document: index.html
```

**Step 2: Upload Content**
```html
<!-- index.html -->
<!DOCTYPE html>
<html>
<head>
    <title>CloudFront Lab</title>
</head>
<body>
    <h1>Hello from CloudFront!</h1>
    <p>Served via AWS CloudFront CDN</p>
    <img src="image.jpg" alt="Sample Image">
</body>
</html>

<!-- Upload with any sample image as image.jpg -->
```

**Step 3: Add Bucket Policy**
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::lab-cloudfront-origin-[account-id]/*"
        }
    ]
}
```

#### Part B: Create CloudFront Distribution

**Step 4: Create Distribution**
```
1. Go to CloudFront → Distributions → Create distribution
2. Origin:
   - Origin domain: lab-cloudfront-origin-[account-id].s3.amazonaws.com
   - Origin path: leave empty
   - Name: S3-lab-origin
   - Origin access: Public (for lab simplicity)
3. Default cache behavior:
   - Viewer protocol policy: Redirect HTTP to HTTPS
   - Allowed HTTP methods: GET, HEAD
   - Cache key and origin requests: CachingOptimized
4. Settings:
   - Price class: Use only North America and Europe
   - Default root object: index.html
5. Create distribution
```

**Step 5: Wait and Test**
```
1. Wait for deployment (5-15 minutes)
2. Copy Distribution domain name (xxxx.cloudfront.net)
3. Test in browser
4. Compare latency with direct S3 access
```

#### Part C: Custom Error Pages

**Step 6: Create Error Page**
```html
<!-- error.html in S3 -->
<!DOCTYPE html>
<html>
<head>
    <title>Error</title>
</head>
<body>
    <h1>Oops! Something went wrong</h1>
    <p>The page you're looking for doesn't exist.</p>
    <a href="/">Go to Homepage</a>
</body>
</html>
```

**Step 7: Configure Error Pages**
```
1. Select distribution → Error pages
2. Create custom error response
3. HTTP error code: 404
4. Customize error response: Yes
5. Response page path: /error.html
6. HTTP response code: 404
7. Create
```

#### Part D: Cache Invalidation

**Step 8: Invalidate Cache**
```
1. Select distribution → Invalidations
2. Create invalidation
3. Object paths: /*
4. Create invalidation
5. Wait for completion
```

### Cleanup for Lab 4.5
```
1. Disable CloudFront distribution
2. Wait for deployment
3. Delete distribution
4. Empty and delete S3 bucket
```

---

## Lab 4.6: VPC Endpoints

### Objectives
- Create Gateway endpoint for S3
- Create Interface endpoint for other services

### Steps

#### Part A: Gateway Endpoint

**Step 1: Create S3 Gateway Endpoint**
```
1. VPC → Endpoints → Create endpoint
2. Name: "lab-s3-endpoint"
3. Service category: AWS services
4. Service: com.amazonaws.us-east-1.s3 (Gateway type)
5. VPC: lab-vpc
6. Route tables: Select private route tables
7. Policy: Full access
8. Create
```

**Step 2: Test S3 Access from Private Subnet**
```bash
# From private instance (via bastion)
aws s3 ls
# Should work without internet access via NAT
```

#### Part B: Interface Endpoint

**Step 3: Create Interface Endpoint**
```
1. Create endpoint
2. Name: "lab-ssm-endpoint"
3. Service: com.amazonaws.us-east-1.ssm (Interface type)
4. VPC: lab-vpc
5. Subnets: Select private subnets
6. Security groups: Create new allowing HTTPS from VPC
7. Create

Also create endpoints for:
- ssmmessages
- ec2messages
```

**Step 4: Test Session Manager**
```
1. Ensure EC2 instance has SSM role
2. Use Session Manager to connect to private instance
3. No SSH or bastion needed
```

### Cleanup for Lab 4.6
```
1. Delete VPC endpoints
2. Delete associated security groups
```

---

## 📝 Lab Summary & Key Takeaways

### What You Learned:
1. **VPC**: Custom VPC with public/private subnets, IGW, NAT Gateway
2. **VPC Peering**: Non-overlapping CIDRs, route table updates
3. **ALB**: Target groups, path-based routing, stickiness
4. **Route 53**: DNS records, health checks, failover
5. **CloudFront**: CDN distribution, caching, error pages
6. **VPC Endpoints**: Gateway vs Interface endpoints

### Exam-Relevant Points:
- NAT Gateway: Must be in public subnet
- NACL: Stateless, need both inbound AND outbound rules
- VPC Peering: Non-transitive
- ALB: Layer 7, path/host routing
- NLB: Layer 4, static IP
- Gateway Endpoint: S3 and DynamoDB (free)
- Interface Endpoint: Most other services (charged)

---

**Next:** [Labs-05-Security](./Labs-05-Security.md)
