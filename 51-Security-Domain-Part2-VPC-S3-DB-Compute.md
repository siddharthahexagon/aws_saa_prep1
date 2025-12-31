# AWS SAA-C03: DESIGN SECURE ARCHITECTURES - PART 2

## 📋 VPC SECURITY, S3 SECURITY, DATABASE SECURITY, COMPUTE SECURITY

---

# 🌐 20. VPC SECURITY

## VPC Security Components

| Component | Layer | Description |
|-----------|-------|-------------|
| **Security Groups** | Instance | Stateful firewall |
| **NACLs** | Subnet | Stateless firewall |
| **VPC Flow Logs** | VPC/Subnet/ENI | Traffic logging |
| **Network Firewall** | VPC | Advanced firewall |
| **VPC Endpoints** | VPC | Private AWS access |

---

## SECURITY GROUPS

### Definition
Security Groups act as a virtual firewall at the instance level, controlling inbound and outbound traffic.

### Security Group Rules

| Property | Description |
|----------|-------------|
| **Stateful** | Return traffic automatically allowed |
| **Default Inbound** | All traffic denied |
| **Default Outbound** | All traffic allowed |
| **Rules** | Allow only (no deny rules) |
| **Evaluation** | All rules evaluated |

### Rule Components

| Component | Description |
|-----------|-------------|
| **Type** | Protocol (SSH, HTTP, Custom) |
| **Protocol** | TCP, UDP, ICMP |
| **Port Range** | Single or range |
| **Source/Destination** | CIDR, SG, or Prefix List |

### Security Group Best Practices

| Practice | Description |
|----------|-------------|
| **Least Privilege** | Only required ports |
| **No 0.0.0.0/0** | Avoid open inbound |
| **Reference SGs** | Use SG IDs, not IPs |
| **Separate by Function** | Web SG, App SG, DB SG |
| **Document** | Use descriptions |

### Security Group Referencing

```
┌─────────────────────────────────────────────────────────────┐
│                  SECURITY GROUP CHAINING                     │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│    Internet                                                  │
│        │                                                     │
│        ▼                                                     │
│   ┌─────────┐    ┌─────────────────────────────────────┐    │
│   │   ALB   │    │  ALB-SG: Inbound 443 from 0.0.0.0/0 │    │
│   └────┬────┘    └─────────────────────────────────────┘    │
│        │                                                     │
│        ▼                                                     │
│   ┌─────────┐    ┌─────────────────────────────────────┐    │
│   │   EC2   │    │  EC2-SG: Inbound 80 from ALB-SG     │    │
│   │  (Web)  │    │         (Reference SG, not IP)      │    │
│   └────┬────┘    └─────────────────────────────────────┘    │
│        │                                                     │
│        ▼                                                     │
│   ┌─────────┐    ┌─────────────────────────────────────┐    │
│   │   RDS   │    │  RDS-SG: Inbound 3306 from EC2-SG   │    │
│   └─────────┘    └─────────────────────────────────────┘    │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## NETWORK ACCESS CONTROL LISTS (NACLs)

### Definition
NACLs are an optional layer of security that acts as a stateless firewall at the subnet level.

### NACL vs Security Group

| Feature | Security Group | NACL |
|---------|----------------|------|
| **Level** | Instance | Subnet |
| **State** | Stateful | Stateless |
| **Rules** | Allow only | Allow & Deny |
| **Default** | Deny inbound | Allow all |
| **Evaluation** | All rules | First match (number) |
| **Return Traffic** | Automatic | Must be allowed |

### NACL Rules

| Property | Description |
|----------|-------------|
| **Rule Number** | 1-32766 (lower = higher priority) |
| **Type** | Protocol type |
| **Protocol** | TCP, UDP, ICMP, etc. |
| **Port Range** | Source or destination ports |
| **Source/Destination** | CIDR block |
| **Allow/Deny** | Action to take |

### Default NACL vs Custom NACL

| Type | Default Behavior |
|------|------------------|
| **Default NACL** | Allows ALL inbound and outbound |
| **Custom NACL** | Denies ALL inbound and outbound |

### Ephemeral Ports

| Client | Ephemeral Port Range |
|--------|----------------------|
| **Linux** | 32768-65535 |
| **Windows** | 49152-65535 |
| **NAT Gateway** | 1024-65535 |
| **Load Balancer** | 1024-65535 |

### NACL Example

| Rule # | Type | Protocol | Port | Source | Allow/Deny |
|--------|------|----------|------|--------|------------|
| 100 | HTTPS | TCP | 443 | 0.0.0.0/0 | Allow |
| 110 | SSH | TCP | 22 | 10.0.0.0/16 | Allow |
| 120 | Custom | TCP | 1024-65535 | 0.0.0.0/0 | Allow |
| * | All | All | All | 0.0.0.0/0 | Deny |

---

## VPC FLOW LOGS

### Definition
VPC Flow Logs capture information about IP traffic going to and from network interfaces in your VPC.

### Flow Log Levels

| Level | Captures |
|-------|----------|
| **VPC** | All ENIs in VPC |
| **Subnet** | All ENIs in subnet |
| **ENI** | Specific network interface |

### Flow Log Destinations

| Destination | Use Case |
|-------------|----------|
| **CloudWatch Logs** | Real-time analysis |
| **S3** | Long-term storage, analysis |
| **Kinesis Firehose** | Streaming analysis |

### Flow Log Record Fields

| Field | Description |
|-------|-------------|
| **version** | Log format version |
| **account-id** | AWS account |
| **interface-id** | ENI ID |
| **srcaddr** | Source IP |
| **dstaddr** | Destination IP |
| **srcport** | Source port |
| **dstport** | Destination port |
| **protocol** | IANA protocol number |
| **packets** | Number of packets |
| **bytes** | Number of bytes |
| **start** | Start time |
| **end** | End time |
| **action** | ACCEPT or REJECT |
| **log-status** | OK, NODATA, SKIPDATA |

### Flow Log Key Points

| Point | Description |
|-------|-------------|
| **Not real-time** | 10-15 minute delay |
| **Not changeable** | Cannot modify after creation |
| **No content** | Captures metadata only |
| **Not captured** | DHCP, DNS, metadata, NTP |

---

## AWS NETWORK FIREWALL

### Definition
AWS Network Firewall is a managed service that makes it easy to deploy essential network protections for all of your VPCs.

### Features

| Feature | Description |
|---------|-------------|
| **Stateful** | Full packet inspection |
| **Stateless** | 5-tuple filtering |
| **IDS/IPS** | Intrusion detection/prevention |
| **Web Filtering** | Domain filtering |
| **Protocol Detection** | Application protocol |

### Rule Types

| Type | Description |
|------|-------------|
| **Stateless** | Process packets individually |
| **Stateful** | Track connection state |
| **Domain List** | Allow/block domains |
| **Suricata** | Compatible IPS rules |

### Network Firewall Architecture

```
┌────────────────────────────────────────────────────────────┐
│                        VPC                                  │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌───────────────┐    ┌───────────────┐    ┌────────────┐  │
│  │   Internet    │    │   Firewall    │    │  Private   │  │
│  │    Gateway    │───▶│    Subnet     │───▶│  Subnets   │  │
│  └───────────────┘    │  (Endpoints)  │    └────────────┘  │
│                       └───────────────┘                     │
│                             │                               │
│                    ┌────────▼────────┐                      │
│                    │ Network Firewall│                      │
│                    │   (Stateful +   │                      │
│                    │   Stateless)    │                      │
│                    └─────────────────┘                      │
│                                                             │
└────────────────────────────────────────────────────────────┘
```

---

## VPC ENDPOINTS

### Definition
VPC Endpoints enable private connections between your VPC and supported AWS services without requiring an internet gateway, NAT device, VPN connection, or AWS Direct Connect.

### Endpoint Types

| Type | Description | Services |
|------|-------------|----------|
| **Gateway** | Route table entry | S3, DynamoDB |
| **Interface** | ENI with private IP | 100+ services |
| **Gateway LB** | For appliances | Third-party |

### Gateway Endpoint

| Feature | Description |
|---------|-------------|
| **Cost** | Free |
| **Setup** | Route table entry |
| **Per VPC** | One per VPC per service |
| **Security** | Endpoint policy |

### Interface Endpoint (PrivateLink)

| Feature | Description |
|---------|-------------|
| **Cost** | $0.01/hour + $0.01/GB |
| **Setup** | ENI in subnet |
| **Security** | Security groups + endpoint policy |
| **DNS** | Private DNS option |
| **Cross-VPC** | Accessible via peering/TGW |

### Endpoint Policy Example

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Principal": "*",
      "Action": "s3:GetObject",
      "Effect": "Allow",
      "Resource": "arn:aws:s3:::my-bucket/*"
    }
  ]
}
```

### Private DNS

| Setting | Effect |
|---------|--------|
| **Enabled** | service.region.amazonaws.com → private IP |
| **Disabled** | vpce-xxx.service.region.vpce.amazonaws.com |

---

## VPC PRIVATE CONNECTIVITY

### VPC Peering Security

| Consideration | Description |
|---------------|-------------|
| **Not transitive** | A↔B, B↔C doesn't mean A↔C |
| **No overlapping CIDR** | Must have unique ranges |
| **Route tables** | Must add routes in both VPCs |
| **Security groups** | Can reference peer SGs |

### Transit Gateway Security

| Feature | Description |
|---------|-------------|
| **Route tables** | Control traffic flow |
| **Attachments** | Per VPC permissions |
| **Propagation** | Automatic or static routes |
| **Sharing** | RAM for cross-account |

### AWS PrivateLink

| Use Case | Description |
|----------|-------------|
| **Expose service** | Provider creates endpoint service |
| **Consume service** | Consumer creates interface endpoint |
| **Security** | Allowlisting by AWS account |

---

# 🪣 21. S3 SECURITY

## S3 Security Layers

```
┌─────────────────────────────────────────────────────────────┐
│                    S3 SECURITY LAYERS                        │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌─────────────────────────────────────────────────────┐    │
│  │ 1. Block Public Access (Account/Bucket Level)       │    │
│  └─────────────────────────────────────────────────────┘    │
│                         │                                    │
│  ┌─────────────────────────────────────────────────────┐    │
│  │ 2. Bucket Policies (Resource-based)                 │    │
│  └─────────────────────────────────────────────────────┘    │
│                         │                                    │
│  ┌─────────────────────────────────────────────────────┐    │
│  │ 3. IAM Policies (Identity-based)                    │    │
│  └─────────────────────────────────────────────────────┘    │
│                         │                                    │
│  ┌─────────────────────────────────────────────────────┐    │
│  │ 4. ACLs (Legacy - avoid)                            │    │
│  └─────────────────────────────────────────────────────┘    │
│                         │                                    │
│  ┌─────────────────────────────────────────────────────┐    │
│  │ 5. Object Ownership (BucketOwnerEnforced)           │    │
│  └─────────────────────────────────────────────────────┘    │
│                         │                                    │
│  ┌─────────────────────────────────────────────────────┐    │
│  │ 6. Encryption (SSE-S3, SSE-KMS, SSE-C)              │    │
│  └─────────────────────────────────────────────────────┘    │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## S3 BLOCK PUBLIC ACCESS

### Settings

| Setting | Description |
|---------|-------------|
| **BlockPublicAcls** | Block PUT with public ACLs |
| **IgnorePublicAcls** | Ignore existing public ACLs |
| **BlockPublicPolicy** | Block PUT of public policies |
| **RestrictPublicBuckets** | Only authorized users for public buckets |

### Levels

| Level | Scope |
|-------|-------|
| **Account** | All buckets in account |
| **Bucket** | Individual bucket |

### Best Practice
- **Enable all 4 settings at account level** (default for new accounts)
- Override only when public access is explicitly needed

---

## S3 BUCKET POLICIES

### Policy Structure

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowSpecificUser",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123456789012:user/Alice"
      },
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::my-bucket/*",
      "Condition": {
        "IpAddress": {
          "aws:SourceIp": "192.168.1.0/24"
        }
      }
    }
  ]
}
```

### Common Bucket Policy Patterns

#### Deny Unencrypted Uploads
```json
{
  "Sid": "DenyUnencryptedUploads",
  "Effect": "Deny",
  "Principal": "*",
  "Action": "s3:PutObject",
  "Resource": "arn:aws:s3:::bucket/*",
  "Condition": {
    "StringNotEquals": {
      "s3:x-amz-server-side-encryption": "aws:kms"
    }
  }
}
```

#### Force HTTPS
```json
{
  "Sid": "ForceHTTPS",
  "Effect": "Deny",
  "Principal": "*",
  "Action": "s3:*",
  "Resource": [
    "arn:aws:s3:::bucket",
    "arn:aws:s3:::bucket/*"
  ],
  "Condition": {
    "Bool": {
      "aws:SecureTransport": "false"
    }
  }
}
```

#### Restrict to VPC Endpoint
```json
{
  "Sid": "RestrictToVPCEndpoint",
  "Effect": "Deny",
  "Principal": "*",
  "Action": "s3:*",
  "Resource": "arn:aws:s3:::bucket/*",
  "Condition": {
    "StringNotEquals": {
      "aws:SourceVpce": "vpce-1234567890abcdef0"
    }
  }
}
```

#### Cross-Account Access
```json
{
  "Sid": "CrossAccountAccess",
  "Effect": "Allow",
  "Principal": {
    "AWS": "arn:aws:iam::OTHER-ACCOUNT:root"
  },
  "Action": [
    "s3:GetObject",
    "s3:ListBucket"
  ],
  "Resource": [
    "arn:aws:s3:::bucket",
    "arn:aws:s3:::bucket/*"
  ]
}
```

---

## S3 ENCRYPTION

### Encryption Types

| Type | Key Management | Header |
|------|----------------|--------|
| **SSE-S3** | AWS managed | AES256 |
| **SSE-KMS** | KMS managed | aws:kms |
| **SSE-C** | Customer provided | Customer key |
| **Client-Side** | Customer | N/A |

### SSE-S3 (Default)
| Feature | Description |
|---------|-------------|
| **Key** | AWS managed |
| **Algorithm** | AES-256 |
| **Cost** | Free |
| **Audit** | No key-level audit |
| **Header** | `x-amz-server-side-encryption: AES256` |

### SSE-KMS
| Feature | Description |
|---------|-------------|
| **Key** | AWS managed or CMK |
| **Algorithm** | AES-256 |
| **Cost** | KMS API charges |
| **Audit** | CloudTrail logging |
| **Header** | `x-amz-server-side-encryption: aws:kms` |
| **Bucket Key** | Reduce KMS calls |

### SSE-C
| Feature | Description |
|---------|-------------|
| **Key** | Customer provides per request |
| **Storage** | AWS doesn't store key |
| **HTTPS** | Required |
| **Use Case** | Full key control |

### Default Encryption
- Set at bucket level
- All new objects automatically encrypted
- SSE-S3 is default for new buckets

### Bucket Key
- Reduces KMS API calls
- S3 generates bucket-level key from CMK
- 99% reduction in KMS costs

---

## S3 ACCESS POINTS

### Definition
S3 Access Points simplify managing data access at scale for applications using shared datasets in S3.

### Features

| Feature | Description |
|---------|-------------|
| **Unique DNS** | Each access point has own endpoint |
| **Policy** | Separate policy per access point |
| **VPC Restriction** | Can restrict to specific VPC |
| **Block Public** | Per access point setting |

### Use Cases

| Use Case | Description |
|----------|-------------|
| **Multi-tenant** | Separate access per team |
| **VPC-restricted** | Data only from VPC |
| **Application-specific** | Different apps, different policies |

---

## S3 OBJECT LOCK

### Definition
S3 Object Lock stores objects using a Write Once Read Many (WORM) model to prevent deletion or modification.

### Retention Modes

| Mode | Description |
|------|-------------|
| **Governance** | Users with permissions can override |
| **Compliance** | NO ONE can delete (even root) |

### Lock Types

| Type | Description |
|------|-------------|
| **Retention Period** | Fixed time protection |
| **Legal Hold** | Indefinite protection |

### Requirements
- Versioning must be enabled
- Cannot disable Object Lock once enabled
- Works at object version level

---

## S3 ACCESS LOGGING

### Definition
Server Access Logging provides detailed records for requests made to a bucket.

### Configuration

| Setting | Description |
|---------|-------------|
| **Target Bucket** | Where logs are stored |
| **Target Prefix** | Log file prefix |
| **Permissions** | S3 Log Delivery group |

### Log Contents
- Bucket owner
- Requester
- Request time
- Operation
- Response status
- Error code

---

# 🗃️ 22. DATABASE SECURITY

## RDS SECURITY

### Network Security

| Layer | Control |
|-------|---------|
| **VPC** | Deploy in VPC |
| **Subnet** | Use private subnets |
| **Security Groups** | Control access |
| **Public Access** | Disable (default) |

### Encryption at Rest

| Feature | Description |
|---------|-------------|
| **Algorithm** | AES-256 |
| **Key** | AWS managed or CMK |
| **Enable** | At creation only |
| **Snapshots** | Encrypted if DB encrypted |
| **Read Replicas** | Encrypted if source encrypted |

### Encrypt Existing Database

```
┌─────────────────────────────────────────────────────────────┐
│         ENCRYPT EXISTING UNENCRYPTED RDS                     │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  1. Create snapshot of unencrypted DB                        │
│  2. Copy snapshot with encryption enabled                    │
│  3. Restore new DB from encrypted snapshot                   │
│  4. Update application to new endpoint                       │
│  5. Delete old unencrypted DB                                │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Encryption in Transit

| Feature | Description |
|---------|-------------|
| **SSL/TLS** | Supported all engines |
| **Force SSL** | rds.force_ssl parameter |
| **Certificates** | AWS provides |

### IAM Database Authentication

| Feature | Description |
|---------|-------------|
| **Supported** | MySQL, PostgreSQL |
| **Token** | 15-minute lifetime |
| **Benefits** | No password management |
| **Use Case** | Lambda, EC2 with roles |

### RDS Proxy

| Feature | Description |
|---------|-------------|
| **Purpose** | Connection pooling |
| **IAM Auth** | Enforce IAM authentication |
| **Secrets** | Integrate with Secrets Manager |
| **Security** | TLS/SSL to database |

---

## AURORA SECURITY

### All RDS Security Features Plus:

| Feature | Description |
|---------|-------------|
| **IAM Auth** | Native support |
| **Audit Logging** | Advanced auditing |
| **Activity Streams** | Real-time to Kinesis |
| **Network Isolation** | VPC only |

### Aurora Activity Streams

| Feature | Description |
|---------|-------------|
| **Purpose** | Database activity monitoring |
| **Destination** | Kinesis Data Stream |
| **Encryption** | KMS encrypted |
| **Mode** | Async or sync |

---

## DYNAMODB SECURITY

### Encryption

| Feature | Description |
|---------|-------------|
| **At Rest** | Always encrypted |
| **Key Options** | AWS owned, AWS managed, CMK |
| **In Transit** | HTTPS endpoints |

### Access Control

| Method | Description |
|--------|-------------|
| **IAM Policies** | Fine-grained access |
| **Conditions** | Item-level, attribute-level |
| **VPC Endpoint** | Private access |

### Fine-Grained Access Example

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "dynamodb:GetItem",
        "dynamodb:Query"
      ],
      "Resource": "arn:aws:dynamodb:*:*:table/Users",
      "Condition": {
        "ForAllValues:StringEquals": {
          "dynamodb:LeadingKeys": ["${cognito-identity.amazonaws.com:sub}"]
        }
      }
    }
  ]
}
```

---

## REDSHIFT SECURITY

### Features

| Feature | Description |
|---------|-------------|
| **VPC** | Deploy in VPC |
| **Encryption** | At rest and in transit |
| **Enhanced VPC** | All traffic through VPC |
| **Column-level** | Grant on specific columns |
| **Row-level** | Dynamic data masking |

### Encryption Options

| Option | Description |
|--------|-------------|
| **KMS** | AWS managed or CMK |
| **CloudHSM** | Hardware security module |

---

## ELASTICACHE SECURITY

### Redis Security

| Feature | Description |
|---------|-------------|
| **AUTH** | Password authentication |
| **IAM Auth** | ElastiCache IAM |
| **Encryption at Rest** | KMS |
| **Encryption in Transit** | TLS |
| **VPC** | Private subnets |

### Redis AUTH + ACL

| Feature | Description |
|---------|-------------|
| **AUTH Token** | Single password |
| **RBAC** | User-based access control |
| **ACL** | Command-level permissions |

---

# 💻 23. COMPUTE SECURITY

## EC2 SECURITY

### Security Layers

| Layer | Control |
|-------|---------|
| **Network** | VPC, Security Groups |
| **OS** | Patching, hardening |
| **Application** | Code security |
| **Data** | Encryption |

### Key Pairs

| Type | Description |
|------|-------------|
| **ED25519** | Recommended, more secure |
| **RSA** | 2048/4096 bit |
| **Import** | Bring your own |

### Instance Metadata Service (IMDS)

| Version | Security |
|---------|----------|
| **IMDSv1** | Less secure (SSRF vulnerable) |
| **IMDSv2** | Requires session token |

### IMDSv2 Enforcement
```bash
aws ec2 modify-instance-metadata-options \
  --instance-id i-1234567890abcdef0 \
  --http-tokens required
```

### EC2 Encryption

| Component | Encryption |
|-----------|------------|
| **EBS Volumes** | KMS |
| **Instance Store** | Not persistent |
| **AMI** | Encrypted snapshots |
| **In Transit** | Application-level |

### Systems Manager

| Feature | Security Benefit |
|---------|------------------|
| **Session Manager** | No SSH/bastion needed |
| **Patch Manager** | Automated patching |
| **Inventory** | Track software |
| **State Manager** | Configuration compliance |

---

## LAMBDA SECURITY

### Execution Role

| Permissions | Use Case |
|-------------|----------|
| **AWSLambdaBasicExecutionRole** | CloudWatch Logs |
| **AWSLambdaVPCAccessExecutionRole** | VPC access |
| **Custom** | S3, DynamoDB, etc. |

### Resource-Based Policy

| Feature | Description |
|---------|-------------|
| **Purpose** | Who can invoke function |
| **Principals** | Services, accounts |
| **Cross-Account** | Allow other accounts |

### Environment Variables

| Feature | Description |
|---------|-------------|
| **Encryption** | KMS at rest |
| **Runtime** | Decrypted automatically |
| **Secrets** | Use Secrets Manager instead |

### VPC Configuration

| Consideration | Description |
|---------------|-------------|
| **ENI** | Creates ENI in VPC |
| **NAT** | Needed for internet |
| **Endpoints** | Needed for AWS services |
| **Cold Start** | May increase |

### Code Signing

| Feature | Description |
|---------|-------------|
| **Purpose** | Ensure code integrity |
| **Signer** | AWS Signer service |
| **Policy** | Enforce signed code |

---

## ECS/EKS SECURITY

### ECS Task Roles

| Role Type | Purpose |
|-----------|---------|
| **Task Role** | Container permissions |
| **Execution Role** | Pull images, logs |

### ECS Security Best Practices

| Practice | Description |
|----------|-------------|
| **IAM Roles** | Task-level roles |
| **Secrets** | Use Secrets Manager |
| **Images** | Scan with ECR |
| **Network** | awsvpc network mode |
| **Logging** | CloudWatch Logs |

### EKS Security

| Feature | Description |
|---------|-------------|
| **IAM Integration** | IRSA (IAM Roles for Service Accounts) |
| **Network Policy** | Kubernetes policies |
| **Pod Security** | Security context |
| **Secrets** | Kubernetes secrets + KMS |
| **Audit Logs** | Control plane logging |

### ECR Security

| Feature | Description |
|---------|-------------|
| **Image Scanning** | Vulnerability detection |
| **Encryption** | KMS at rest |
| **IAM Policies** | Repository access |
| **Cross-Account** | Resource policies |

---

# 🎯 SECURITY DOMAIN EXAM HEURISTICS

## Quick Answers

| Keyword/Scenario | Answer |
|------------------|--------|
| Cross-account S3 access | **Bucket policy with Principal** |
| Cross-account role access | **AssumeRole with trust policy** |
| Encrypt S3 with audit | **SSE-KMS** |
| Encrypt at rest default | **SSE-S3** |
| Customer-managed keys | **SSE-KMS with CMK** |
| Rotate database secrets | **Secrets Manager** |
| Store config | **Parameter Store** |
| User authentication | **Cognito User Pools** |
| AWS credentials for web app | **Cognito Identity Pools** |
| SSO for AWS accounts | **IAM Identity Center** |
| Block SQL injection | **WAF** |
| DDoS protection | **Shield** |
| Threat detection | **GuardDuty** |
| Vulnerability scanning | **Inspector** |
| Find sensitive data | **Macie** |
| API audit trail | **CloudTrail** |
| Resource compliance | **AWS Config** |
| Restrict to VPC | **VPC Endpoint + policy** |
| Block public S3 | **Block Public Access** |
| WORM compliance | **S3 Object Lock Compliance** |
| Instance firewall | **Security Groups** |
| Subnet firewall | **NACL** |
| Central firewall | **Network Firewall** |
| Limit account services | **SCPs** |
| Temporary credentials | **STS AssumeRole** |
| Encrypt unencrypted RDS | **Snapshot → Copy encrypted → Restore** |

---

# ✅ SECURITY DOMAIN CHECKLIST

```
IAM
□ Policy structure and evaluation
□ Trust policies for roles
□ Cross-account access patterns
□ Permission boundaries
□ IAM Access Analyzer

ORGANIZATIONS
□ SCP inheritance
□ Denylist vs Allowlist strategies
□ Management account exceptions

ENCRYPTION
□ KMS key types
□ Envelope encryption
□ SSE-S3 vs SSE-KMS vs SSE-C
□ Bucket keys

SECRETS
□ Secrets Manager rotation
□ Parameter Store tiers
□ When to use each

COGNITO
□ User Pools vs Identity Pools
□ Lambda triggers
□ Role mapping

DETECTION & PROTECTION
□ GuardDuty data sources
□ Inspector scan types
□ WAF rules and conditions
□ Shield Standard vs Advanced

COMPLIANCE
□ CloudTrail event types
□ Config rules and remediation
□ Security Hub standards

VPC SECURITY
□ Security Groups vs NACLs
□ VPC Flow Logs
□ VPC Endpoints types
□ Network Firewall

DATA SECURITY
□ S3 bucket policies
□ S3 Block Public Access
□ S3 Object Lock modes
□ RDS encryption
□ DynamoDB security
```

---

*Last Updated: December 2025*
*Exam Version: SAA-C03*
*Domain: Design Secure Architectures (30%)*
