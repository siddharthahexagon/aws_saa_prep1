# AWS SAA-C03 CHEATSHEET: SECURITY, IDENTITY & COMPLIANCE

## 📋 PRINT-READY EXAM REFERENCE

---

# 🔐 AWS IAM (IDENTITY & ACCESS MANAGEMENT)

## Definition
Service for managing access to AWS services and resources securely.

## IAM Components

| Component | Description |
|-----------|-------------|
| **Users** | Individual identities |
| **Groups** | Collection of users |
| **Roles** | Temporary credentials for services/users |
| **Policies** | JSON documents defining permissions |

## IAM Policies

### Policy Structure
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "UniqueIdentifier",
      "Effect": "Allow/Deny",
      "Action": "service:action",
      "Resource": "arn:aws:...",
      "Condition": {}
    }
  ]
}
```

### Policy Types

| Type | Description | Use Case |
|------|-------------|----------|
| **Identity-based** | Attached to users/groups/roles | User permissions |
| **Resource-based** | Attached to resources | Cross-account access |
| **Permission boundaries** | Max permissions limit | Delegate admin |
| **SCPs** | Organization limits | Account restrictions |
| **Session policies** | Limit role session | Temporary restrictions |

### Policy Evaluation Order
1. **Explicit Deny** → DENY
2. **SCP** (if in Organization)
3. **Resource-based policy** (cross-account)
4. **Identity-based policy**
5. **Permission boundary**
6. **Session policy**
7. **Default** → DENY

## IAM Roles

| Role Type | Description |
|-----------|-------------|
| **Service role** | For AWS services (EC2, Lambda) |
| **Cross-account** | Access from other accounts |
| **Federation** | For external users (SAML, OIDC) |
| **Instance profile** | Wrapper for EC2 roles |

### Role Trust Policy
Defines WHO can assume the role.

### Role Permissions Policy
Defines WHAT the role can do.

## IAM Best Practices

| Practice | Description |
|----------|-------------|
| **Root account** | Don't use, enable MFA |
| **MFA** | Enable for all users |
| **Least privilege** | Minimum necessary permissions |
| **Groups** | Assign permissions via groups |
| **Roles** | Use for services, not access keys |
| **Rotate keys** | Regular key rotation |
| **Conditions** | Use for extra security |

## IAM Credentials

| Type | Use Case |
|------|----------|
| **Password** | Console access |
| **Access keys** | CLI/SDK access |
| **SSH keys** | CodeCommit |
| **MFA** | Additional authentication |

## IAM Access Analyzer

| Feature | Description |
|---------|-------------|
| **Purpose** | Identify unintended access |
| **Analyzes** | S3, IAM, KMS, Lambda, SQS |
| **Zone of Trust** | Account or Organization |
| **Findings** | Resources accessible from outside |

---

# 🔑 AWS STS (SECURITY TOKEN SERVICE)

## Definition
Service for temporary security credentials.

## STS APIs

| API | Description | Use Case |
|-----|-------------|----------|
| **AssumeRole** | Assume role in same/other account | Cross-account |
| **AssumeRoleWithSAML** | SAML federation | Enterprise SSO |
| **AssumeRoleWithWebIdentity** | Web identity (OIDC) | Mobile apps |
| **GetSessionToken** | Temporary creds with MFA | CLI with MFA |
| **GetFederationToken** | Federated user creds | Custom federation |

## Temporary Credentials

| Component | Description |
|-----------|-------------|
| **Access Key ID** | Identifies credentials |
| **Secret Access Key** | Signs requests |
| **Session Token** | Must be included |
| **Expiration** | When creds expire |

### Duration

| API | Default | Max |
|-----|---------|-----|
| AssumeRole | 1 hour | 12 hours |
| GetSessionToken | 12 hours | 36 hours |
| GetFederationToken | 12 hours | 36 hours |

---

# 🏢 AWS ORGANIZATIONS

## Definition
Centrally manage multiple AWS accounts.

## Organizations Features

| Feature | Description |
|---------|-------------|
| **Master account** | Management account |
| **OUs** | Organizational Units |
| **SCPs** | Service Control Policies |
| **Consolidated billing** | Single bill, volume discounts |
| **Tag policies** | Standardize tags |
| **Backup policies** | Centralized backup |

## Service Control Policies (SCPs)

| Feature | Description |
|---------|-------------|
| **Type** | Allowlist or Denylist |
| **Inheritance** | Inherited down the tree |
| **Management account** | NOT affected by SCPs |
| **Effect** | Limit maximum permissions |

### SCP Key Points
- SCPs don't grant permissions
- They set maximum available permissions
- Intersect with IAM policies
- Default: FullAWSAccess

### SCP Strategies

| Strategy | Description |
|----------|-------------|
| **Denylist** | Start with full access, deny specific |
| **Allowlist** | Deny all, allow specific |

---

# 🔒 AWS KMS (KEY MANAGEMENT SERVICE)

## Definition
Managed service to create and control encryption keys.

## KMS Key Types

| Type | Description | Use Case |
|------|-------------|----------|
| **AWS Managed** | Created by AWS services | Default encryption |
| **Customer Managed** | Created by you | Custom control |
| **AWS Owned** | Shared across accounts | Free, no control |

### Key Properties

| Property | AWS Managed | Customer Managed |
|----------|-------------|------------------|
| **Rotation** | Annual (auto) | Optional (manual) |
| **Key policy** | AWS-managed | User-managed |
| **Alias** | aws/service | Custom |
| **Cost** | Free | $1/month + API calls |
| **Audit** | CloudTrail | CloudTrail |

## KMS Key Policies

### Default Key Policy
- Allows root account full access
- Required for any key access

### Custom Key Policy
- Define specific principals
- Cross-account access
- Grant specific actions

## KMS Grants

| Feature | Description |
|---------|-------------|
| **Purpose** | Temporary permissions |
| **Use case** | Encrypt large files |
| **Token** | Grant token for immediate use |

## Envelope Encryption

| Component | Description |
|-----------|-------------|
| **Data Key** | Encrypts your data |
| **CMK** | Encrypts data key |
| **Benefit** | Encrypt large files efficiently |

### Process
1. Generate data key (GenerateDataKey)
2. Encrypt data with plaintext data key
3. Store encrypted data + encrypted data key
4. To decrypt: Decrypt data key → Decrypt data

## KMS Multi-Region Keys

| Feature | Description |
|---------|-------------|
| **Type** | Identical keys in multiple regions |
| **Primary** | One primary, multiple replicas |
| **Use case** | DR, global applications |
| **Key ID** | Different ARN, same key ID |

## KMS Operations

| Operation | Description |
|-----------|-------------|
| **Encrypt** | Encrypt data (< 4 KB) |
| **Decrypt** | Decrypt data |
| **GenerateDataKey** | Create data key |
| **ReEncrypt** | Re-encrypt with different key |
| **EnableKeyRotation** | Enable automatic rotation |

---

# 🔏 AWS SECRETS MANAGER

## Definition
Managed service for secrets with automatic rotation.

## Secrets Manager Features

| Feature | Description |
|---------|-------------|
| **Rotation** | Automatic rotation via Lambda |
| **Supported** | RDS, Redshift, DocumentDB, custom |
| **Multi-region** | Replicate secrets |
| **Integration** | RDS, ECS, EKS, Lambda |
| **Encryption** | KMS encryption |

## Secrets Manager vs Parameter Store

| Feature | Secrets Manager | Parameter Store |
|---------|-----------------|-----------------|
| **Rotation** | Built-in | Manual (Lambda) |
| **Cost** | $0.40/secret/month | Free tier available |
| **Cross-region** | Built-in | Manual |
| **Secret type** | Any | String, StringList, SecureString |
| **Size** | 64 KB | 8 KB (standard), 10 KB (advanced) |

---

# 📝 AWS SYSTEMS MANAGER PARAMETER STORE

## Definition
Secure storage for configuration and secrets.

## Parameter Store Tiers

| Feature | Standard | Advanced |
|---------|----------|----------|
| **Parameters** | 10,000 | 100,000 |
| **Max size** | 4 KB | 8 KB |
| **Policies** | No | Yes |
| **Cost** | Free | $0.05/parameter/month |

## Parameter Types

| Type | Description |
|------|-------------|
| **String** | Plain text |
| **StringList** | Comma-separated |
| **SecureString** | Encrypted (KMS) |

## Parameter Store Features

| Feature | Description |
|---------|-------------|
| **Hierarchy** | `/app/prod/db/password` |
| **Versioning** | Track changes |
| **Integration** | CloudFormation, Lambda, EC2 |
| **Policies** | Expiration, notification |

---

# 🛡️ AWS WAF (WEB APPLICATION FIREWALL)

## Definition
Protect web applications from common exploits.

## WAF Components

| Component | Description |
|-----------|-------------|
| **Web ACL** | Set of rules |
| **Rules** | Conditions to match |
| **Rule Groups** | Reusable rule collections |
| **IP Sets** | IP address lists |
| **Regex Pattern Sets** | Regex patterns |

## WAF Protects

| Resource | Description |
|----------|-------------|
| **CloudFront** | Global |
| **ALB** | Regional |
| **API Gateway** | Regional |
| **AppSync** | Regional |

## WAF Rules

| Rule Type | Description |
|-----------|-------------|
| **Regular** | Match based on conditions |
| **Rate-based** | Block high request rates |
| **Managed** | AWS/Marketplace rules |

## Common Use Cases

| Use Case | Rule Type |
|----------|-----------|
| **SQL injection** | SQLi rule |
| **Cross-site scripting** | XSS rule |
| **Block countries** | Geo match |
| **Block IPs** | IP set |
| **Rate limiting** | Rate-based rule |
| **Bot control** | Managed rule |

---

# 🛡️ AWS SHIELD

## Definition
DDoS protection service.

## Shield Tiers

| Feature | Standard | Advanced |
|---------|----------|----------|
| **Cost** | Free | $3,000/month |
| **Protection** | Layer 3/4 | Layer 3/4/7 |
| **Auto** | Yes | Yes |
| **Response team** | No | 24/7 DRT |
| **Cost protection** | No | Yes |
| **Reports** | Basic | Advanced |
| **WAF included** | No | Yes |

## Shield Advanced Protects

- CloudFront
- Route 53
- Global Accelerator
- ALB/NLB
- Elastic IP

---

# 🔍 AMAZON INSPECTOR

## Definition
Automated security assessment for EC2 and ECR.

## Inspector Features

| Feature | Description |
|---------|-------------|
| **EC2** | Network and host assessment |
| **ECR** | Container image scanning |
| **Lambda** | Function code scanning |
| **Continuous** | Automatic scanning |
| **CVE database** | Known vulnerabilities |

## Inspector Assessments

| Type | Description |
|------|-------------|
| **Network** | Network reachability |
| **Host** | OS vulnerabilities, CIS benchmarks |
| **Container** | Image vulnerabilities |

---

# 🔍 AMAZON MACIE

## Definition
ML-powered service to discover and protect sensitive data.

## Macie Features

| Feature | Description |
|---------|-------------|
| **PII detection** | Personal information |
| **S3 inventory** | Analyze S3 buckets |
| **Custom identifiers** | Define your patterns |
| **Findings** | Security alerts |
| **Integration** | EventBridge, Security Hub |

---

# 🔍 AWS SECURITY HUB

## Definition
Central security view across accounts.

## Security Hub Features

| Feature | Description |
|---------|-------------|
| **Aggregation** | Findings from multiple services |
| **Standards** | AWS best practices, CIS, PCI DSS |
| **Automation** | Auto-remediation via EventBridge |
| **Cross-account** | Organization-wide view |

## Integrates With
- GuardDuty
- Inspector
- Macie
- IAM Access Analyzer
- Firewall Manager
- Partner products

---

# 🔍 AMAZON GUARDDUTY

## Definition
Intelligent threat detection using ML.

## GuardDuty Sources

| Source | Description |
|--------|-------------|
| **CloudTrail** | API calls |
| **VPC Flow Logs** | Network traffic |
| **DNS Logs** | DNS queries |
| **EKS Audit Logs** | Kubernetes activity |
| **S3 Data Events** | S3 access |

## GuardDuty Findings

| Type | Description |
|------|-------------|
| **Reconnaissance** | Scanning, port probing |
| **Instance compromise** | Malware, crypto mining |
| **Account compromise** | Unusual API calls |
| **Bucket compromise** | S3 data access |

---

# 🔐 AWS CERTIFICATE MANAGER (ACM)

## Definition
Provision, manage, and deploy SSL/TLS certificates.

## ACM Features

| Feature | Description |
|---------|-------------|
| **Free** | Public certificates free |
| **Auto-renewal** | Automatic renewal |
| **Validation** | DNS or email |
| **Integration** | ALB, CloudFront, API Gateway |
| **Private CA** | Issue private certificates |

## ACM Key Points

| Point | Description |
|-------|-------------|
| **Regional** | Certificates are regional |
| **CloudFront** | Must be in us-east-1 |
| **EC2** | Cannot use ACM directly |
| **Export** | Cannot export public cert |

---

# 🔑 AWS COGNITO

## Definition
User identity and access management.

## Cognito Components

| Component | Description |
|-----------|-------------|
| **User Pools** | User directory, sign-up/sign-in |
| **Identity Pools** | Federated identities, AWS creds |

## User Pools

| Feature | Description |
|---------|-------------|
| **Authentication** | Username/password, social, SAML |
| **MFA** | SMS, TOTP |
| **Password policies** | Customizable |
| **Email/phone verification** | Built-in |
| **Hosted UI** | Pre-built login pages |
| **Tokens** | JWT (ID, access, refresh) |

## Identity Pools

| Feature | Description |
|---------|-------------|
| **Purpose** | Get AWS credentials |
| **Sources** | User pools, social, SAML |
| **Unauthenticated** | Guest access supported |
| **Role mapping** | Map users to IAM roles |

## User Pools vs Identity Pools

| Feature | User Pools | Identity Pools |
|---------|------------|----------------|
| **Purpose** | Authentication | Authorization (AWS) |
| **Output** | JWT tokens | AWS credentials |
| **Use case** | Sign-in | Access AWS services |

---

# 🔐 AWS SSO (IAM IDENTITY CENTER)

## Definition
Single sign-on for multiple AWS accounts and applications.

## IAM Identity Center Features

| Feature | Description |
|---------|-------------|
| **SSO** | One login for all accounts |
| **Identity source** | Built-in, AD, external IdP |
| **Permission sets** | Reusable permissions |
| **SAML 2.0** | Application integration |
| **MFA** | Built-in MFA support |

---

# 🎯 SECURITY QUICK EXAM HEURISTICS

| Keyword/Scenario | Answer |
|------------------|--------|
| Encrypt data at rest | **KMS** |
| Manage encryption keys | **KMS Customer Managed Key** |
| Rotate database secrets | **Secrets Manager** |
| Store configuration | **Parameter Store** |
| Block SQL injection | **WAF** |
| DDoS protection | **Shield (Standard free, Advanced paid)** |
| Vulnerability scanning | **Inspector** |
| Find sensitive data in S3 | **Macie** |
| Threat detection | **GuardDuty** |
| SSL/TLS certificates | **ACM** |
| User authentication | **Cognito User Pools** |
| AWS credentials for web/mobile | **Cognito Identity Pools** |
| SSO for AWS accounts | **IAM Identity Center** |
| Cross-account access | **IAM Roles + Trust Policy** |
| Centralized security view | **Security Hub** |
| Limit account permissions | **SCPs** |
| Temporary credentials | **STS AssumeRole** |

---

# ✅ EXAM DAY QUICK CHECKS

```
□ Know IAM policy evaluation order
□ Know SCP inheritance and limitations
□ Know KMS key types and policies
□ Know Secrets Manager vs Parameter Store
□ Know WAF rules and protected resources
□ Know Shield Standard vs Advanced
□ Know Cognito User Pools vs Identity Pools
□ Know STS APIs for different use cases
□ Know ACM regional requirements
```

---

*Last Updated: December 2025*
*Exam Version: SAA-C03*
