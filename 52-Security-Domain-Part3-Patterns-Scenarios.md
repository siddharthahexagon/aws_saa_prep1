# AWS SAA-C03: DESIGN SECURE ARCHITECTURES - PART 3

## 📋 SECURITY PATTERNS, SCENARIOS & EXAM QUESTIONS

---

# 🏗️ SECURITY ARCHITECTURE PATTERNS

## Pattern 1: Secure Three-Tier Web Application

```
┌─────────────────────────────────────────────────────────────────────┐
│                        SECURE THREE-TIER ARCHITECTURE                │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   Internet                                                           │
│       │                                                              │
│       ▼                                                              │
│   ┌─────────┐     ┌──────────────────────────────────────────────┐  │
│   │  WAF    │────▶│              PUBLIC SUBNET                    │  │
│   └─────────┘     │  ┌─────────────────────────────────────────┐ │  │
│       │           │  │       Application Load Balancer          │ │  │
│       │           │  │       (ACM certificate, HTTPS)           │ │  │
│       │           │  └─────────────────────────────────────────┘ │  │
│       │           │  Security Group: 443 from 0.0.0.0/0          │  │
│       │           └──────────────────────────────────────────────┘  │
│       │                              │                               │
│       │                              ▼                               │
│       │           ┌──────────────────────────────────────────────┐  │
│       │           │              PRIVATE SUBNET (App)             │  │
│       │           │  ┌─────────────────────────────────────────┐ │  │
│       │           │  │       EC2 / ECS / Lambda                 │ │  │
│       │           │  │       (IAM roles, encrypted EBS)         │ │  │
│       │           │  └─────────────────────────────────────────┘ │  │
│       │           │  Security Group: 80 from ALB-SG only         │  │
│       │           └──────────────────────────────────────────────┘  │
│       │                              │                               │
│       │                              ▼                               │
│       │           ┌──────────────────────────────────────────────┐  │
│       │           │              PRIVATE SUBNET (Data)            │  │
│       │           │  ┌─────────────────────────────────────────┐ │  │
│       │           │  │       RDS (Multi-AZ, encrypted)          │ │  │
│       │           │  │       (IAM auth, SSL required)           │ │  │
│       │           │  └─────────────────────────────────────────┘ │  │
│       │           │  Security Group: 3306 from App-SG only       │  │
│       │           └──────────────────────────────────────────────┘  │
│                                                                      │
│   SECURITY CONTROLS:                                                 │
│   ✓ WAF for OWASP protection                                        │
│   ✓ ACM for SSL/TLS                                                 │
│   ✓ Security Groups (least privilege)                               │
│   ✓ Private subnets for app/data                                    │
│   ✓ IAM roles (no access keys)                                      │
│   ✓ KMS encryption (EBS, RDS)                                       │
│   ✓ VPC Flow Logs                                                   │
│   ✓ CloudTrail enabled                                              │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Pattern 2: Secure Serverless Application

```
┌─────────────────────────────────────────────────────────────────────┐
│                     SECURE SERVERLESS ARCHITECTURE                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   ┌─────────────┐    ┌────────────────┐    ┌──────────────────┐    │
│   │   Cognito   │───▶│  API Gateway   │───▶│     Lambda       │    │
│   │  User Pool  │    │  (WAF, Auth)   │    │  (VPC, IAM role) │    │
│   └─────────────┘    └────────────────┘    └──────────────────┘    │
│         │                   │                       │               │
│         │                   │                       ▼               │
│         ▼                   │              ┌──────────────────┐    │
│   ┌─────────────┐          │              │    DynamoDB      │    │
│   │  Cognito    │          │              │  (Encrypted,     │    │
│   │Identity Pool│          │              │   VPC Endpoint)  │    │
│   └─────────────┘          │              └──────────────────┘    │
│         │                   │                       │               │
│         ▼                   │                       ▼               │
│   ┌─────────────┐          │              ┌──────────────────┐    │
│   │  AWS Creds  │          │              │       S3         │    │
│   │  (Temp)     │          │              │  (Encrypted,     │    │
│   └─────────────┘          │              │   VPC Endpoint)  │    │
│                            │              └──────────────────┘    │
│                            │                                       │
│   SECURITY CONTROLS:                                               │
│   ✓ Cognito authentication                                         │
│   ✓ API Gateway authorizers                                        │
│   ✓ WAF on API Gateway                                             │
│   ✓ Lambda in VPC (if needed)                                      │
│   ✓ VPC endpoints for S3/DynamoDB                                  │
│   ✓ IAM roles (least privilege)                                    │
│   ✓ KMS encryption                                                 │
│   ✓ X-Ray for tracing                                              │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Pattern 3: Secure Multi-Account Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                    MULTI-ACCOUNT SECURITY ARCHITECTURE               │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   ┌───────────────────────────────────────────────────────────────┐ │
│   │                    MANAGEMENT ACCOUNT                          │ │
│   │   ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐  │ │
│   │   │Organizations│  │ CloudTrail  │  │   IAM Identity      │  │ │
│   │   │    (SCPs)   │  │  (Org trail)│  │   Center (SSO)      │  │ │
│   │   └─────────────┘  └─────────────┘  └─────────────────────┘  │ │
│   └───────────────────────────────────────────────────────────────┘ │
│                              │                                       │
│         ┌────────────────────┼────────────────────┐                 │
│         ▼                    ▼                    ▼                 │
│   ┌───────────┐        ┌───────────┐        ┌───────────┐          │
│   │SECURITY OU│        │  PROD OU  │        │  DEV OU   │          │
│   └───────────┘        └───────────┘        └───────────┘          │
│         │                    │                    │                 │
│   ┌─────┴─────┐        ┌─────┴─────┐        ┌─────┴─────┐          │
│   │           │        │           │        │           │          │
│   ▼           ▼        ▼           ▼        ▼           ▼          │
│ ┌─────┐   ┌─────┐   ┌─────┐   ┌─────┐   ┌─────┐   ┌─────┐        │
│ │Log  │   │Sec  │   │Prod │   │Prod │   │Dev  │   │Dev  │        │
│ │Acct │   │Acct │   │Acct1│   │Acct2│   │Acct │   │Test │        │
│ └─────┘   └─────┘   └─────┘   └─────┘   └─────┘   └─────┘        │
│                                                                      │
│   LOG ACCOUNT:                                                       │
│   ✓ Central CloudTrail logs                                         │
│   ✓ Central Config data                                             │
│   ✓ VPC Flow Logs (cross-account)                                   │
│                                                                      │
│   SECURITY ACCOUNT:                                                  │
│   ✓ GuardDuty (delegated admin)                                     │
│   ✓ Security Hub (aggregator)                                       │
│   ✓ Inspector (delegated admin)                                     │
│   ✓ Macie (delegated admin)                                         │
│   ✓ Detective                                                       │
│                                                                      │
│   SCPs APPLIED:                                                      │
│   ✓ Deny region restrictions                                        │
│   ✓ Deny leaving organization                                       │
│   ✓ Require encryption                                              │
│   ✓ Deny root user actions                                          │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Pattern 4: Secure Data Lake

```
┌─────────────────────────────────────────────────────────────────────┐
│                     SECURE DATA LAKE ARCHITECTURE                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   DATA INGESTION                  DATA STORAGE                       │
│   ┌─────────────┐               ┌─────────────────────────────────┐ │
│   │  Kinesis    │              │           S3 Data Lake            │ │
│   │  Firehose   │─────────────▶│  ┌────────────────────────────┐ │ │
│   │ (encrypted) │              │  │ Raw Zone (SSE-KMS)         │ │ │
│   └─────────────┘              │  ├────────────────────────────┤ │ │
│                                │  │ Curated Zone (SSE-KMS)     │ │ │
│   ┌─────────────┐              │  ├────────────────────────────┤ │ │
│   │    DMS      │─────────────▶│  │ Analytics Zone (SSE-KMS)   │ │ │
│   │ (SSL/TLS)   │              │  └────────────────────────────┘ │ │
│   └─────────────┘              │                                   │ │
│                                │  SECURITY:                        │ │
│                                │  ✓ Bucket policies (VPC endpoint) │ │
│                                │  ✓ S3 Access Points per zone     │ │
│                                │  ✓ Object Lock (compliance)      │ │
│                                │  ✓ Macie (PII detection)         │ │
│                                │  ✓ Server access logging         │ │
│                                └─────────────────────────────────┘ │
│                                               │                     │
│                                               ▼                     │
│   DATA ACCESS                  ┌─────────────────────────────────┐ │
│   ┌─────────────┐              │         Lake Formation           │ │
│   │   Athena    │◀─────────────│  ✓ Fine-grained access control  │ │
│   │ (encrypted) │              │  ✓ Column-level security        │ │
│   └─────────────┘              │  ✓ Row-level security           │ │
│                                │  ✓ Data filters                  │ │
│   ┌─────────────┐              │  ✓ Tag-based permissions         │ │
│   │  Redshift   │◀─────────────│  ✓ Cross-account sharing        │ │
│   │ (encrypted) │              └─────────────────────────────────┘ │
│   └─────────────┘                                                   │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Pattern 5: Secure Hybrid Connectivity

```
┌─────────────────────────────────────────────────────────────────────┐
│                   SECURE HYBRID CONNECTIVITY                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   ON-PREMISES                           AWS CLOUD                    │
│   ┌───────────────────┐                ┌───────────────────────────┐│
│   │                   │                │                           ││
│   │  ┌─────────────┐  │   VPN/DX      │  ┌─────────────────────┐  ││
│   │  │ Applications│  │───────────────▶│  │   Transit Gateway   │  ││
│   │  └─────────────┘  │                │  │   (Route tables)    │  ││
│   │        │          │                │  └─────────────────────┘  ││
│   │        ▼          │                │           │               ││
│   │  ┌─────────────┐  │                │           ▼               ││
│   │  │   Active    │  │   AD Trust    │  ┌─────────────────────┐  ││
│   │  │  Directory  │◀─┼───────────────▶│  │  AWS Managed AD     │  ││
│   │  └─────────────┘  │                │  │  (Multi-AZ)         │  ││
│   │        │          │                │  └─────────────────────┘  ││
│   │        ▼          │                │           │               ││
│   │  ┌─────────────┐  │                │           ▼               ││
│   │  │   Data      │  │   DataSync    │  ┌─────────────────────┐  ││
│   │  │   Store     │──┼───────────────▶│  │   S3 / EFS          │  ││
│   │  └─────────────┘  │  (Encrypted)  │  │   (Encrypted)        │  ││
│   │                   │                │  └─────────────────────┘  ││
│   └───────────────────┘                └───────────────────────────┘│
│                                                                      │
│   SECURITY CONTROLS:                                                 │
│   ✓ Site-to-Site VPN (IPsec) or Direct Connect                      │
│   ✓ VPN over DX for encryption                                      │
│   ✓ Transit Gateway route tables (isolation)                        │
│   ✓ AD trust relationship                                           │
│   ✓ DataSync with encryption                                        │
│   ✓ VPC endpoints for AWS services                                  │
│   ✓ Network Firewall (inspection)                                   │
└─────────────────────────────────────────────────────────────────────┘
```

---

# 📝 EXAM SCENARIO QUESTIONS

## Scenario 1: Cross-Account S3 Access

**Question:** A company has two AWS accounts: Account A (data producer) and Account B (data consumer). Account B needs read-only access to a specific S3 bucket in Account A. What is the most secure and manageable solution?

**Options:**
A. Create IAM user in Account A, share credentials with Account B
B. Add bucket policy allowing Account B root, create role in Account B
C. Make bucket public with IP restrictions
D. Use S3 Access Points with cross-account sharing

**Answer: B**

**Explanation:**
- **B is correct**: Bucket policy allows Account B, then Account B creates IAM role with s3:GetObject permissions. Users in Account B assume the role. No credentials shared.
- A is wrong: Sharing credentials is a security anti-pattern
- C is wrong: Public buckets are security risks
- D is partial: Access Points help but still need bucket policy

**Implementation:**
```json
// Account A - Bucket Policy
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": {"AWS": "arn:aws:iam::ACCOUNT-B:root"},
    "Action": ["s3:GetObject", "s3:ListBucket"],
    "Resource": [
      "arn:aws:s3:::bucket-name",
      "arn:aws:s3:::bucket-name/*"
    ]
  }]
}
```

---

## Scenario 2: Encrypting Existing RDS

**Question:** A company has an unencrypted RDS MySQL database that must be encrypted to meet compliance requirements. The solution must minimize downtime. What should the architect recommend?

**Options:**
A. Enable encryption on the existing database
B. Create encrypted Read Replica, promote to master
C. Take snapshot, copy with encryption, restore, switch endpoint
D. Export data, create new encrypted database, import data

**Answer: C**

**Explanation:**
- **C is correct**: Snapshot → Copy with encryption → Restore is the AWS-recommended approach
- A is wrong: Cannot enable encryption on existing RDS instance
- B is wrong: Cannot create encrypted replica from unencrypted source
- D is wrong: Unnecessarily complex and more downtime

**Steps:**
1. Create snapshot of unencrypted database
2. Copy snapshot, enable encryption with KMS key
3. Restore new DB instance from encrypted snapshot
4. Update application connection string
5. Verify functionality
6. Delete old unencrypted instance

---

## Scenario 3: Restricting S3 to VPC

**Question:** A company wants to ensure their S3 bucket can only be accessed from within their VPC, not from the internet or other VPCs. What combination of controls should be implemented?

**Options:**
A. Block Public Access only
B. VPC Endpoint + Endpoint Policy + Bucket Policy with VPC condition
C. Security Groups on S3
D. NACL rules blocking S3 IP ranges

**Answer: B**

**Explanation:**
- **B is correct**: Comprehensive solution requires:
  1. Gateway VPC Endpoint for S3
  2. Endpoint Policy limiting bucket access
  3. Bucket Policy with `aws:SourceVpce` condition
- A is partial: Blocks public but not other VPCs
- C is wrong: S3 doesn't use Security Groups
- D is wrong: Complex, incomplete solution

**Implementation:**
```json
// Bucket Policy
{
  "Version": "2012-10-17",
  "Statement": [{
    "Sid": "RestrictToVPCEndpoint",
    "Effect": "Deny",
    "Principal": "*",
    "Action": "s3:*",
    "Resource": ["arn:aws:s3:::bucket", "arn:aws:s3:::bucket/*"],
    "Condition": {
      "StringNotEquals": {
        "aws:SourceVpce": "vpce-xxxxxxxxx"
      }
    }
  }]
}
```

---

## Scenario 4: Lambda Database Access

**Question:** A Lambda function needs to access an RDS database in a private subnet. The function runs frequently and experiences connection issues due to connection exhaustion. What is the best solution?

**Options:**
A. Increase Lambda timeout
B. Increase RDS instance size
C. Use RDS Proxy with IAM authentication
D. Move Lambda outside VPC

**Answer: C**

**Explanation:**
- **C is correct**: RDS Proxy provides connection pooling, reduces connections by 66%, supports IAM auth
- A is wrong: Doesn't solve connection pooling
- B is wrong: Doesn't solve connection limits efficiently
- D is wrong: Would require database to be public (security risk)

**Benefits of RDS Proxy:**
- Connection pooling (fewer DB connections)
- IAM authentication (no passwords in code)
- Faster failover (66% faster)
- Fully managed

---

## Scenario 5: Centralized Security Monitoring

**Question:** A company with 50 AWS accounts needs to centrally monitor security findings from GuardDuty, Inspector, and Macie. Security team should see all findings in one place. What architecture should be used?

**Options:**
A. Enable services in each account, check each console
B. Use AWS Organizations with delegated administrator, Security Hub aggregation
C. Export all findings to S3, use Athena
D. Create Lambda in each account to forward findings

**Answer: B**

**Explanation:**
- **B is correct**: 
  - Security Hub as aggregator
  - Delegated administrator in security account
  - GuardDuty, Inspector, Macie integrate with Security Hub
  - Single pane of glass
- A is wrong: Not centralized, 50 consoles to check
- C is wrong: Complex, not real-time
- D is wrong: Unnecessarily complex

**Architecture:**
1. Management account: Organizations
2. Security account: Delegated admin for GuardDuty, Inspector, Macie
3. Security Hub: Aggregates findings from all accounts
4. EventBridge: Automates responses

---

## Scenario 6: Secure API for Partners

**Question:** A company needs to expose an API to external partners. The API must be protected against DDoS, SQL injection, and should authenticate partners using API keys with usage quotas. What services should be used?

**Options:**
A. ALB with WAF
B. API Gateway with WAF, usage plans, and API keys
C. CloudFront with Lambda@Edge
D. EC2 with nginx and rate limiting

**Answer: B**

**Explanation:**
- **B is correct**: API Gateway provides:
  - WAF integration (SQLi, XSS protection)
  - API keys for partner identification
  - Usage plans for quotas and throttling
  - Built-in DDoS protection
- A is partial: ALB with WAF works but no native API key/quota support
- C is partial: Lacks API management features
- D is wrong: Requires self-management, less secure

---

## Scenario 7: Rotating Database Credentials

**Question:** A company wants to automatically rotate their RDS PostgreSQL credentials every 30 days. Applications should always use current credentials without code changes. What is the best approach?

**Options:**
A. Create Lambda to rotate and update Parameter Store
B. Use Secrets Manager with automatic rotation
C. Use IAM database authentication
D. Store credentials in environment variables, rotate manually

**Answer: B**

**Explanation:**
- **B is correct**: Secrets Manager provides:
  - Built-in rotation for RDS
  - Lambda rotation function (AWS-managed)
  - Automatic credential update
  - Application retrieves current secret via API
- A is wrong: Reinventing the wheel
- C is partial: Good for some use cases but not credential rotation
- D is wrong: Manual, error-prone, not secure

---

## Scenario 8: Preventing Data Exfiltration

**Question:** A company is concerned about insiders exfiltrating sensitive data from S3 to external accounts. What controls should be implemented?

**Options:**
A. Enable S3 versioning
B. Block Public Access + VPC Endpoint + S3 bucket policy denying external principals
C. Enable MFA Delete
D. Use S3 Object Lock

**Answer: B**

**Explanation:**
- **B is correct**: Multiple layers:
  - Block Public Access: No public access
  - VPC Endpoint: Traffic stays in VPC
  - Bucket Policy: Deny external accounts/principals
  - Additional: Macie for monitoring, CloudTrail for auditing
- A is wrong: Versioning doesn't prevent exfiltration
- C is wrong: MFA Delete prevents deletion, not exfiltration
- D is wrong: Object Lock prevents modification, not exfiltration

**Additional Controls:**
- S3 Access Analyzer (find external access)
- Macie (detect sensitive data access patterns)
- CloudTrail (audit all access)
- GuardDuty (detect anomalous access)

---

# 🎯 SECURITY DOMAIN INSTANT ANSWERS

## Encryption

| Question Pattern | Answer |
|------------------|--------|
| Encrypt S3 with audit trail | **SSE-KMS** |
| Encrypt S3 default | **SSE-S3** |
| Customer controls encryption keys | **SSE-C or Client-side** |
| Reduce KMS API calls for S3 | **S3 Bucket Keys** |
| Encrypt unencrypted RDS | **Snapshot → Copy encrypted → Restore** |
| Encrypt EBS at creation | **Enable at launch (can't change later)** |

## Access Control

| Question Pattern | Answer |
|------------------|--------|
| Cross-account S3 | **Bucket policy + IAM role** |
| Cross-account resources | **AssumeRole** |
| Limit services in account | **SCP** |
| Fine-grained S3 access | **S3 Access Points** |
| Temporary credentials | **STS AssumeRole** |
| Web/mobile authentication | **Cognito User Pools** |
| AWS creds for web app | **Cognito Identity Pools** |

## Network Security

| Question Pattern | Answer |
|------------------|--------|
| Instance firewall | **Security Groups** |
| Subnet firewall | **NACLs** |
| Block specific IPs | **NACL deny rule** |
| Private AWS access | **VPC Endpoints** |
| Inspect traffic | **Network Firewall** |
| Reference other instances | **Security Group referencing** |

## Protection

| Question Pattern | Answer |
|------------------|--------|
| SQL injection | **WAF** |
| XSS attack | **WAF** |
| DDoS | **Shield** |
| Rate limiting | **WAF rate-based rules** |
| Bot protection | **WAF Bot Control** |

## Detection

| Question Pattern | Answer |
|------------------|--------|
| Threat detection | **GuardDuty** |
| Vulnerability scanning | **Inspector** |
| Sensitive data discovery | **Macie** |
| Centralized findings | **Security Hub** |
| API auditing | **CloudTrail** |
| Configuration compliance | **Config** |

## Secrets

| Question Pattern | Answer |
|------------------|--------|
| Rotate DB credentials | **Secrets Manager** |
| Store config parameters | **Parameter Store** |
| Cross-region secrets | **Secrets Manager replication** |
| Free secret storage | **Parameter Store SecureString** |

---

# ✅ FINAL SECURITY CHECKLIST

```
BEFORE THE EXAM, VERIFY YOU KNOW:

□ IAM policy structure (Effect, Action, Resource, Condition)
□ IAM policy evaluation order (Deny > SCP > Allow)
□ Cross-account access patterns (bucket policy, AssumeRole)
□ SCP inheritance (management account exempt)
□ KMS key types (AWS owned, managed, customer managed)
□ Envelope encryption process
□ S3 encryption types (SSE-S3, SSE-KMS, SSE-C)
□ S3 bucket policy patterns (deny unencrypted, force HTTPS, VPC only)
□ Security Groups vs NACLs differences
□ VPC endpoint types (Gateway vs Interface)
□ Cognito User Pools vs Identity Pools
□ Secrets Manager vs Parameter Store
□ WAF rules and protected resources
□ Shield Standard vs Advanced
□ GuardDuty, Inspector, Macie purposes
□ CloudTrail event types
□ Config rules and remediation
□ How to encrypt existing unencrypted resources
```

---

*Last Updated: December 2025*
*Exam Version: SAA-C03*
*Domain: Design Secure Architectures (30%)*
