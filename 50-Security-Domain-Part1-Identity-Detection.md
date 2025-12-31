# AWS SAA-C03: DESIGN SECURE ARCHITECTURES - COMPLETE GUIDE

## 📋 DOMAIN 1: DESIGN SECURE ARCHITECTURES (30% of Exam)

---

# 🎯 DOMAIN OVERVIEW

## Exam Tasks in This Domain

| Task | Description |
|------|-------------|
| **Task 1.1** | Design secure access to AWS resources |
| **Task 1.2** | Design secure workloads and applications |
| **Task 1.3** | Determine appropriate data security controls |

---

# 📚 TABLE OF CONTENTS

1. **IAM (Identity and Access Management)** - Core identity service
2. **AWS Organizations & SCPs** - Multi-account security
3. **AWS STS** - Temporary credentials
4. **AWS KMS** - Encryption key management
5. **AWS Secrets Manager** - Secrets rotation
6. **AWS Parameter Store** - Configuration storage
7. **AWS Cognito** - User authentication
8. **AWS Directory Service** - Active Directory
9. **AWS Certificate Manager** - SSL/TLS
10. **AWS WAF** - Web application firewall
11. **AWS Shield** - DDoS protection
12. **AWS Firewall Manager** - Centralized firewall
13. **Amazon GuardDuty** - Threat detection
14. **Amazon Inspector** - Vulnerability assessment
15. **Amazon Macie** - Data discovery
16. **AWS Security Hub** - Security posture
17. **Amazon Detective** - Security investigation
18. **AWS CloudTrail** - API auditing
19. **AWS Config** - Configuration compliance
20. **VPC Security** - Network security
21. **S3 Security** - Object storage security
22. **Database Security** - RDS/DynamoDB security
23. **Compute Security** - EC2/Lambda security

---

# 🔐 1. AWS IAM (IDENTITY AND ACCESS MANAGEMENT)

## Definition
AWS Identity and Access Management (IAM) enables you to manage access to AWS services and resources securely. You can create and manage AWS users, groups, roles, and permissions.

## IAM Components

### Users
| Property | Description |
|----------|-------------|
| **Definition** | Individual identity for person or service |
| **Credentials** | Password (console), Access keys (CLI/API) |
| **Limit** | 5,000 users per account |
| **Best Practice** | Use roles instead of users for applications |

### Groups
| Property | Description |
|----------|-------------|
| **Definition** | Collection of users |
| **Purpose** | Apply permissions to multiple users |
| **Limit** | 300 groups per account |
| **Nesting** | Groups cannot contain other groups |
| **Best Practice** | Assign permissions to groups, not users |

### Roles
| Property | Description |
|----------|-------------|
| **Definition** | Identity with permissions, assumable by anyone |
| **No credentials** | Uses temporary security credentials |
| **Trust policy** | Defines WHO can assume the role |
| **Permission policy** | Defines WHAT the role can do |

### Policies
| Type | Description | Attached To |
|------|-------------|-------------|
| **AWS Managed** | Created by AWS, read-only | Users, Groups, Roles |
| **Customer Managed** | Created by you, editable | Users, Groups, Roles |
| **Inline** | Embedded in single entity | One User, Group, or Role |

## IAM Policy Structure

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "OptionalStatementId",
      "Effect": "Allow | Deny",
      "Principal": "* | AWS:arn | Service",
      "Action": "service:ActionName",
      "Resource": "arn:aws:service:region:account:resource",
      "Condition": {
        "ConditionOperator": {
          "ConditionKey": "ConditionValue"
        }
      }
    }
  ]
}
```

## Policy Elements Explained

| Element | Required | Description |
|---------|----------|-------------|
| **Version** | Yes | Always "2012-10-17" |
| **Statement** | Yes | Array of permission statements |
| **Sid** | No | Statement identifier |
| **Effect** | Yes | Allow or Deny |
| **Principal** | Resource-based only | Who is allowed/denied |
| **Action** | Yes | What actions (s3:GetObject) |
| **Resource** | Yes | What resources (ARN) |
| **Condition** | No | When policy applies |

## Common Condition Keys

| Condition Key | Use Case |
|---------------|----------|
| `aws:SourceIp` | Restrict by IP address |
| `aws:SourceVpc` | Restrict to VPC |
| `aws:SourceVpce` | Restrict to VPC endpoint |
| `aws:CurrentTime` | Time-based access |
| `aws:MultiFactorAuthPresent` | Require MFA |
| `aws:PrincipalTag` | Tag-based access |
| `aws:RequestedRegion` | Restrict to regions |
| `s3:x-amz-acl` | S3 ACL conditions |
| `ec2:ResourceTag` | EC2 tag-based access |

## Policy Evaluation Logic

```
┌─────────────────────────────────────────┐
│         POLICY EVALUATION ORDER          │
├─────────────────────────────────────────┤
│ 1. Explicit DENY → DENIED (final)       │
│ 2. SCP (Organizations) → Check limits   │
│ 3. Resource-based policy → Allow?       │
│ 4. Identity-based policy → Allow?       │
│ 5. Permission boundary → Within limits? │
│ 6. Session policy → Within limits?      │
│ 7. Default → DENY                       │
└─────────────────────────────────────────┘
```

## IAM Best Practices

| Practice | Description |
|----------|-------------|
| **Root Account** | Use only for initial setup, enable MFA |
| **Least Privilege** | Grant minimum required permissions |
| **Use Groups** | Assign permissions via groups |
| **Use Roles** | For applications, not access keys |
| **Rotate Credentials** | Regular key rotation |
| **Enable MFA** | For all users, especially privileged |
| **Use Conditions** | Add extra security constraints |
| **Audit Regularly** | Use IAM Access Analyzer |

## IAM Roles for Services

| Service | Role Purpose |
|---------|--------------|
| **EC2** | Access other AWS services |
| **Lambda** | Execute and access resources |
| **ECS Tasks** | Container permissions |
| **RDS** | Access S3, CloudWatch |
| **Glue** | Access data sources |

## Cross-Account Access

### Method 1: Resource-Based Policy
```json
{
  "Effect": "Allow",
  "Principal": {"AWS": "arn:aws:iam::OTHER-ACCOUNT:root"},
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::bucket/*"
}
```

### Method 2: IAM Role (AssumeRole)
1. Create role in target account with trust policy
2. User/role in source account assumes role
3. Gets temporary credentials

## IAM Access Analyzer

| Feature | Description |
|---------|-------------|
| **Purpose** | Find unintended public/cross-account access |
| **Analyzes** | S3, IAM, KMS, Lambda, SQS, Secrets Manager |
| **Zone of Trust** | Account or Organization |
| **Findings** | External access identified |
| **Policy Validation** | Check policy grammar |
| **Policy Generation** | Generate policies from CloudTrail |

## IAM Identity Center (SSO)

| Feature | Description |
|---------|-------------|
| **Purpose** | Single sign-on for AWS accounts |
| **Identity Sources** | Built-in, Active Directory, external IdP |
| **Permission Sets** | Reusable permission templates |
| **Integration** | AWS Organizations |

---

# 🏢 2. AWS ORGANIZATIONS

## Definition
AWS Organizations is a service for consolidating multiple AWS accounts into an organization for centralized management, consolidated billing, and hierarchical policy control.

## Organization Structure

```
┌─────────────────────────────────────────┐
│            ROOT (Organization)           │
├─────────────────────────────────────────┤
│  ┌─────────────┐    ┌─────────────────┐ │
│  │ Management  │    │      OUs        │ │
│  │  Account    │    │  (Org Units)    │ │
│  └─────────────┘    └─────────────────┘ │
│                           │             │
│         ┌─────────────────┼────────┐    │
│         ▼                 ▼        ▼    │
│    ┌────────┐       ┌────────┐ ┌──────┐ │
│    │ Prod OU│       │ Dev OU │ │Test  │ │
│    └────────┘       └────────┘ └──────┘ │
│         │                │              │
│    ┌────┴────┐      ┌────┴────┐         │
│    │ Account │      │ Account │         │
│    └─────────┘      └─────────┘         │
└─────────────────────────────────────────┘
```

## Service Control Policies (SCPs)

### Definition
SCPs are JSON policies that specify the maximum permissions for an organization, OU, or account. They don't grant permissions—they set boundaries.

### SCP Key Rules

| Rule | Description |
|------|-------------|
| **Don't grant permissions** | Only limit maximum permissions |
| **Intersect with IAM** | Effective = IAM ∩ SCP |
| **Management account exempt** | SCPs don't affect management account |
| **Inherited** | Child OUs/accounts inherit parent SCPs |
| **Default** | FullAWSAccess (allow all) |

### SCP Strategies

| Strategy | How It Works |
|----------|--------------|
| **Denylist** | Start with FullAWSAccess, add deny statements |
| **Allowlist** | Remove FullAWSAccess, explicitly allow services |

### SCP Example - Deny Region

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyAllOutsideUS",
      "Effect": "Deny",
      "Action": "*",
      "Resource": "*",
      "Condition": {
        "StringNotEquals": {
          "aws:RequestedRegion": ["us-east-1", "us-west-2"]
        }
      }
    }
  ]
}
```

### SCP Example - Prevent Leaving Organization

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Action": "organizations:LeaveOrganization",
      "Resource": "*"
    }
  ]
}
```

## Organization Features

| Feature | Description |
|---------|-------------|
| **Consolidated Billing** | Single payment, volume discounts |
| **RI/SP Sharing** | Share Reserved Instances across accounts |
| **Tag Policies** | Standardize tags |
| **Backup Policies** | Centralized backup |
| **AI Services Opt-Out** | Control AI data usage |

---

# 🔑 3. AWS STS (SECURITY TOKEN SERVICE)

## Definition
AWS Security Token Service (STS) is a web service that enables you to request temporary, limited-privilege credentials for IAM users or federated users.

## STS APIs

| API | Description | Use Case |
|-----|-------------|----------|
| **AssumeRole** | Assume role in same/other account | Cross-account access |
| **AssumeRoleWithSAML** | Assume role with SAML assertion | Enterprise SSO |
| **AssumeRoleWithWebIdentity** | Assume role with OIDC token | Mobile apps, web identity |
| **GetSessionToken** | Temp creds for IAM user | MFA-protected API access |
| **GetFederationToken** | Temp creds for federated user | Custom identity broker |
| **GetCallerIdentity** | Returns caller's identity | Debugging, verification |
| **DecodeAuthorizationMessage** | Decode error messages | Troubleshooting |

## Temporary Credentials Components

| Component | Description |
|-----------|-------------|
| **Access Key ID** | Temporary key identifier |
| **Secret Access Key** | Temporary secret key |
| **Session Token** | Must be included in requests |
| **Expiration** | When credentials expire |

## Session Duration

| API | Default | Maximum |
|-----|---------|---------|
| **AssumeRole** | 1 hour | 12 hours |
| **AssumeRoleWithSAML** | 1 hour | 12 hours |
| **AssumeRoleWithWebIdentity** | 1 hour | 12 hours |
| **GetSessionToken** | 12 hours | 36 hours |
| **GetFederationToken** | 12 hours | 36 hours |

## AssumeRole Process

```
┌──────────────┐         ┌─────────────┐         ┌──────────────┐
│   Account A  │         │     STS     │         │   Account B  │
│   (Source)   │         │             │         │   (Target)   │
├──────────────┤         ├─────────────┤         ├──────────────┤
│              │         │             │         │              │
│  IAM User    │──(1)───▶│ AssumeRole  │◀──(2)──│  IAM Role    │
│  or Role     │         │             │         │  Trust Policy│
│              │◀──(3)───│ Temp Creds  │         │              │
│              │         │             │         │              │
│              │───(4)───Access Target Account───▶│              │
└──────────────┘         └─────────────┘         └──────────────┘
```

## Federation Types

| Type | Use Case | Identity Source |
|------|----------|-----------------|
| **SAML 2.0** | Enterprise SSO | Active Directory, Okta |
| **Web Identity** | Mobile/Web apps | Google, Facebook, Amazon |
| **Custom Broker** | Custom identity system | Your own IdP |
| **IAM Identity Center** | AWS SSO | Any supported IdP |

---

# 🔐 4. AWS KMS (KEY MANAGEMENT SERVICE)

## Definition
AWS Key Management Service (KMS) is a managed service that makes it easy to create and control the cryptographic keys used to protect your data.

## Key Types

| Type | Created By | Management | Cost | Use Case |
|------|------------|------------|------|----------|
| **AWS Owned** | AWS | AWS | Free | Default service encryption |
| **AWS Managed** | AWS | AWS | Free | aws/service keys |
| **Customer Managed** | You | You | $1/month | Custom control |

## Key Specifications

| Spec | Options |
|------|---------|
| **Key Type** | Symmetric (AES-256), Asymmetric (RSA, ECC) |
| **Key Usage** | Encrypt/Decrypt, Sign/Verify |
| **Origin** | AWS_KMS, External, CloudHSM |

## Customer Managed Key Features

| Feature | Description |
|---------|-------------|
| **Key Policy** | Resource-based policy for access control |
| **Key Rotation** | Automatic annual rotation (optional) |
| **Aliases** | Friendly names for keys |
| **Tags** | Metadata for organization |
| **Grants** | Temporary permissions |
| **Multi-Region** | Keys replicated across regions |

## Key Policy Structure

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Enable IAM User Permissions",
      "Effect": "Allow",
      "Principal": {"AWS": "arn:aws:iam::ACCOUNT:root"},
      "Action": "kms:*",
      "Resource": "*"
    },
    {
      "Sid": "Allow Key Administration",
      "Effect": "Allow",
      "Principal": {"AWS": "arn:aws:iam::ACCOUNT:user/Admin"},
      "Action": [
        "kms:Create*",
        "kms:Describe*",
        "kms:Enable*",
        "kms:List*",
        "kms:Put*",
        "kms:Update*",
        "kms:Revoke*",
        "kms:Disable*",
        "kms:Delete*"
      ],
      "Resource": "*"
    },
    {
      "Sid": "Allow Key Usage",
      "Effect": "Allow",
      "Principal": {"AWS": "arn:aws:iam::ACCOUNT:role/AppRole"},
      "Action": [
        "kms:Encrypt",
        "kms:Decrypt",
        "kms:GenerateDataKey*"
      ],
      "Resource": "*"
    }
  ]
}
```

## Envelope Encryption

```
┌─────────────────────────────────────────────────────────────┐
│                    ENVELOPE ENCRYPTION                       │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌─────────────┐   encrypts   ┌─────────────────────────┐  │
│  │  CMK (KMS)  │─────────────▶│  Encrypted Data Key     │  │
│  └─────────────┘              └─────────────────────────┘  │
│                                          │                  │
│  ┌─────────────┐   encrypts              │                  │
│  │  Data Key   │─────────────▶┌──────────▼──────────┐      │
│  │ (Plaintext) │              │   Encrypted Data    │      │
│  └─────────────┘              └─────────────────────┘      │
│                                                              │
│  Process:                                                    │
│  1. GenerateDataKey → Get plaintext + encrypted data key    │
│  2. Encrypt data with plaintext data key                    │
│  3. Store encrypted data + encrypted data key               │
│  4. Discard plaintext data key                              │
│                                                              │
│  Decrypt:                                                    │
│  1. Decrypt the encrypted data key using CMK                │
│  2. Use plaintext data key to decrypt data                  │
└─────────────────────────────────────────────────────────────┘
```

## KMS API Calls

| API | Purpose |
|-----|---------|
| **Encrypt** | Encrypt data (< 4 KB) |
| **Decrypt** | Decrypt data |
| **GenerateDataKey** | Create data key (plaintext + encrypted) |
| **GenerateDataKeyWithoutPlaintext** | Create encrypted data key only |
| **ReEncrypt** | Re-encrypt with different key |
| **EnableKeyRotation** | Enable automatic rotation |

## KMS Multi-Region Keys

| Feature | Description |
|---------|-------------|
| **Purpose** | Same key material in multiple regions |
| **Primary Key** | One primary, multiple replicas |
| **Key ID** | Same key ID across regions |
| **ARN** | Different ARN per region |
| **Use Case** | Cross-region DR, global applications |

## Services Using KMS

| Service | Default Key | Customer Key |
|---------|-------------|--------------|
| **S3** | SSE-S3, SSE-KMS | SSE-KMS (CMK) |
| **EBS** | aws/ebs | CMK |
| **RDS** | aws/rds | CMK |
| **EFS** | aws/elasticfilesystem | CMK |
| **DynamoDB** | AWS owned | CMK |
| **Lambda** | AWS managed | CMK |
| **CloudTrail** | SSE-S3 | SSE-KMS |
| **Secrets Manager** | aws/secretsmanager | CMK |

---

# 🔒 5. AWS SECRETS MANAGER

## Definition
AWS Secrets Manager helps you protect access to your applications, services, and IT resources by storing and managing secrets like database credentials, API keys, and other sensitive information.

## Features

| Feature | Description |
|---------|-------------|
| **Automatic Rotation** | Built-in Lambda rotation |
| **Encryption** | KMS encryption at rest |
| **Versioning** | Multiple versions (current, previous, pending) |
| **Cross-Region Replication** | Replicate secrets |
| **Resource Policy** | Cross-account access |

## Secret Types

| Type | Description |
|------|-------------|
| **RDS Credentials** | MySQL, PostgreSQL, Aurora |
| **Redshift Credentials** | Data warehouse |
| **DocumentDB Credentials** | MongoDB-compatible |
| **Other Databases** | Custom rotation Lambda |
| **API Keys** | Third-party services |
| **Custom Secrets** | Any key-value |

## Automatic Rotation

```
┌────────────────────────────────────────────────────────────┐
│                   SECRET ROTATION FLOW                      │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌──────────────┐      ┌──────────────┐      ┌───────────┐ │
│  │   Secrets    │─────▶│   Lambda     │─────▶│  Database │ │
│  │   Manager    │      │   Rotation   │      │           │ │
│  └──────────────┘      │   Function   │      └───────────┘ │
│         │              └──────────────┘                     │
│         ▼                    │                              │
│  ┌──────────────┐            │                              │
│  │     KMS      │◀───────────┘                              │
│  │  Encryption  │                                           │
│  └──────────────┘                                           │
│                                                             │
│  Rotation Steps:                                            │
│  1. createSecret - Create new version (AWSPENDING)          │
│  2. setSecret - Set new credentials in database             │
│  3. testSecret - Verify new credentials work                │
│  4. finishSecret - Move AWSPENDING to AWSCURRENT            │
└────────────────────────────────────────────────────────────┘
```

## Rotation Strategies

| Strategy | Description | Use Case |
|----------|-------------|----------|
| **Single User** | Rotate one user's password | Simple applications |
| **Alternating Users** | Two users, rotate between | Zero-downtime |

## Secrets Manager vs Parameter Store

| Feature | Secrets Manager | Parameter Store |
|---------|-----------------|-----------------|
| **Rotation** | Built-in | Manual (Lambda) |
| **Cost** | $0.40/secret/month | Free tier available |
| **Replication** | Built-in | Manual |
| **Secret Size** | 64 KB | 4-8 KB |
| **Versioning** | Yes | Yes |
| **Encryption** | Always | Optional (SecureString) |
| **Use Case** | Credentials | Config + secrets |

## Accessing Secrets

### CLI
```bash
aws secretsmanager get-secret-value --secret-id MySecret
```

### SDK (Python)
```python
import boto3
client = boto3.client('secretsmanager')
response = client.get_secret_value(SecretId='MySecret')
secret = response['SecretString']
```

---

# 📝 6. AWS SYSTEMS MANAGER PARAMETER STORE

## Definition
AWS Systems Manager Parameter Store provides secure, hierarchical storage for configuration data and secrets management.

## Parameter Types

| Type | Description | Use Case |
|------|-------------|----------|
| **String** | Plain text | Config values |
| **StringList** | Comma-separated | Multiple values |
| **SecureString** | KMS encrypted | Passwords, keys |

## Parameter Tiers

| Feature | Standard | Advanced |
|---------|----------|----------|
| **Max Parameters** | 10,000 | 100,000 |
| **Max Size** | 4 KB | 8 KB |
| **Parameter Policies** | No | Yes |
| **Cost** | Free | $0.05/parameter/month |

## Hierarchy Example

```
/myapp/
  /prod/
    /db/
      /host = prod-db.example.com
      /password = (SecureString)
    /api/
      /key = (SecureString)
  /dev/
    /db/
      /host = dev-db.example.com
      /password = (SecureString)
```

## Parameter Policies (Advanced Tier)

| Policy | Description |
|--------|-------------|
| **Expiration** | Delete or notify when expired |
| **ExpirationNotification** | Notify before expiration |
| **NoChangeNotification** | Notify if not updated |

## Accessing Parameters

### CLI
```bash
# Get single parameter
aws ssm get-parameter --name /myapp/prod/db/host

# Get with decryption
aws ssm get-parameter --name /myapp/prod/db/password --with-decryption

# Get by path (hierarchy)
aws ssm get-parameters-by-path --path /myapp/prod --recursive
```

---

# 👤 7. AMAZON COGNITO

## Definition
Amazon Cognito provides authentication, authorization, and user management for web and mobile applications.

## Cognito Components

### User Pools
| Feature | Description |
|---------|-------------|
| **Purpose** | User directory, authentication |
| **Output** | JWT tokens (ID, Access, Refresh) |
| **Features** | Sign-up, sign-in, MFA, password policies |
| **Federation** | Social IdPs, SAML, OIDC |
| **Customization** | Hosted UI, Lambda triggers |

### Identity Pools
| Feature | Description |
|---------|-------------|
| **Purpose** | AWS credentials for users |
| **Output** | Temporary AWS credentials |
| **Sources** | User Pools, social, SAML, unauthenticated |
| **Role Mapping** | Map users to IAM roles |

## User Pool vs Identity Pool

```
┌────────────────────────────────────────────────────────────┐
│                                                             │
│  ┌─────────────────┐          ┌─────────────────┐          │
│  │   USER POOLS    │          │  IDENTITY POOLS │          │
│  │                 │          │                 │          │
│  │  Authentication │─────────▶│  Authorization  │          │
│  │                 │  (JWT)   │                 │          │
│  │  "Who are you?" │          │ "AWS access"    │          │
│  │                 │          │                 │          │
│  │  Returns: JWT   │          │ Returns: AWS    │          │
│  │  tokens         │          │ credentials     │          │
│  └─────────────────┘          └─────────────────┘          │
│         │                             │                     │
│         ▼                             ▼                     │
│  ┌─────────────────┐          ┌─────────────────┐          │
│  │ API Gateway     │          │ AWS Services    │          │
│  │ (Authorizer)    │          │ (S3, DynamoDB)  │          │
│  └─────────────────┘          └─────────────────┘          │
│                                                             │
└────────────────────────────────────────────────────────────┘
```

## User Pool Features

| Feature | Description |
|---------|-------------|
| **MFA** | SMS, TOTP authenticator |
| **Password Policy** | Length, complexity, expiration |
| **Account Recovery** | Email, phone verification |
| **Custom Attributes** | User metadata |
| **Groups** | User grouping with IAM role |
| **Lambda Triggers** | Custom authentication flows |
| **Hosted UI** | Pre-built sign-in pages |
| **Advanced Security** | Compromised credentials, adaptive auth |

## Lambda Triggers

| Trigger | Use Case |
|---------|----------|
| **Pre Sign-up** | Validate, auto-confirm |
| **Post Confirmation** | Send welcome email |
| **Pre Authentication** | Custom validation |
| **Post Authentication** | Log sign-in |
| **Pre Token Generation** | Add custom claims |
| **Custom Message** | Customize emails/SMS |
| **User Migration** | Migrate from legacy |

## Identity Pool Role Mapping

| Type | Description |
|------|-------------|
| **Default** | All users get same role |
| **Rules-based** | Role based on token claims |
| **Token-based** | Role from cognito:preferred_role |

---

# 📁 8. AWS DIRECTORY SERVICE

## Definition
AWS Directory Service provides multiple ways to use Microsoft Active Directory with AWS services.

## Directory Types

| Type | Description | Use Case |
|------|-------------|----------|
| **AWS Managed Microsoft AD** | Full AD on AWS | Enterprise, trust relationships |
| **AD Connector** | Proxy to on-premises AD | Use existing AD |
| **Simple AD** | Samba-based | Small, basic needs |

## AWS Managed Microsoft AD

| Feature | Description |
|---------|-------------|
| **Deployment** | Multi-AZ (2+ DCs) |
| **Trust** | One-way or two-way with on-prem |
| **Integration** | RDS, WorkSpaces, QuickSight, SSO |
| **Management** | Standard AD tools |
| **Schema** | Extendable |

## AD Connector

| Feature | Description |
|---------|-------------|
| **Type** | Proxy (no caching) |
| **Sizes** | Small (< 500 users), Large (< 5,000 users) |
| **Requirements** | VPN or Direct Connect |
| **Use Case** | Use on-prem AD without sync |

---

# 🔏 9. AWS CERTIFICATE MANAGER (ACM)

## Definition
AWS Certificate Manager (ACM) handles the complexity of creating, storing, and renewing public and private SSL/TLS certificates.

## Certificate Types

| Type | Cost | Use Case |
|------|------|----------|
| **Public** | Free | Public websites |
| **Private** | Charged | Internal apps, IoT |

## Public Certificate Features

| Feature | Description |
|---------|-------------|
| **Validation** | DNS (recommended) or Email |
| **Auto-Renewal** | Automatic before expiration |
| **Regions** | Regional (us-east-1 for CloudFront) |
| **Integration** | ALB, CloudFront, API Gateway |
| **Cannot Export** | Keys stay in AWS |

## DNS Validation

| Step | Description |
|------|-------------|
| 1 | Request certificate for domain |
| 2 | ACM provides CNAME record |
| 3 | Add CNAME to DNS (Route 53 auto) |
| 4 | ACM validates ownership |
| 5 | Certificate issued |

## Private Certificate Authority

| Feature | Description |
|---------|-------------|
| **Purpose** | Issue private certificates |
| **Use Case** | Internal apps, microservices, IoT |
| **Hierarchy** | Root CA → Subordinate CAs |
| **CRL** | Certificate Revocation List |

## ACM Key Points

| Point | Description |
|-------|-------------|
| **CloudFront** | Must use us-east-1 |
| **ALB/NLB** | Use regional ACM |
| **EC2** | Cannot use ACM directly |
| **Export** | Cannot export public certs |
| **Wildcard** | Supports *.example.com |

---

# 🛡️ 10. AWS WAF (WEB APPLICATION FIREWALL)

## Definition
AWS WAF is a web application firewall that helps protect your web applications from common web exploits and bots that may affect availability, compromise security, or consume excessive resources.

## WAF Components

| Component | Description |
|-----------|-------------|
| **Web ACL** | Main container for rules |
| **Rules** | Define what to inspect |
| **Rule Groups** | Reusable rule collections |
| **IP Sets** | Lists of IP addresses |
| **Regex Pattern Sets** | Regular expressions |

## WAF Protects

| Resource | Type |
|----------|------|
| **CloudFront** | Global |
| **ALB** | Regional |
| **API Gateway** | Regional |
| **AppSync** | Regional |
| **Cognito User Pool** | Regional |

## Rule Types

| Type | Description |
|------|-------------|
| **Regular Rules** | Match based on conditions |
| **Rate-Based Rules** | Block high request rates |
| **Managed Rules** | AWS or Marketplace rules |

## Match Conditions

| Condition | Description |
|-----------|-------------|
| **IP Match** | Source IP address |
| **Geo Match** | Country of origin |
| **String Match** | Headers, URI, body |
| **Regex Match** | Pattern matching |
| **Size Constraint** | Request size |
| **SQL Injection** | SQLi patterns |
| **XSS** | Cross-site scripting |

## Rule Actions

| Action | Description |
|--------|-------------|
| **Allow** | Allow request |
| **Block** | Block request |
| **Count** | Count only (testing) |
| **CAPTCHA** | Require CAPTCHA |
| **Challenge** | Silent challenge |

## AWS Managed Rule Groups

| Rule Group | Protection |
|------------|------------|
| **Core Rule Set (CRS)** | OWASP Top 10 |
| **SQL Database** | SQL injection |
| **Known Bad Inputs** | Malicious patterns |
| **Linux/Windows OS** | OS-specific attacks |
| **PHP/WordPress** | App-specific |
| **Bot Control** | Bot management |
| **Account Takeover** | Credential stuffing |
| **IP Reputation** | Known malicious IPs |
| **Anonymous IP** | VPNs, proxies, Tor |

## Rate-Based Rules

| Setting | Description |
|---------|-------------|
| **Rate Limit** | Max requests per 5 minutes |
| **Minimum** | 100 requests |
| **Scope** | IP, forwarded IP, custom key |

---

# 🛡️ 11. AWS SHIELD

## Definition
AWS Shield is a managed DDoS protection service that safeguards applications running on AWS.

## Shield Tiers

| Feature | Standard | Advanced |
|---------|----------|----------|
| **Cost** | Free | $3,000/month |
| **Protection** | Layer 3/4 | Layer 3/4/7 |
| **Automatic** | Yes | Yes |
| **SRT Access** | No | 24/7 DDoS Response Team |
| **Cost Protection** | No | Yes (scaling costs) |
| **Advanced Metrics** | No | Yes |
| **WAF Included** | No | Yes |
| **Health Checks** | No | Route 53 integration |

## Shield Advanced Protected Resources

- Amazon CloudFront
- Amazon Route 53
- AWS Global Accelerator
- Elastic Load Balancing
- Amazon EC2 Elastic IPs

## Shield Advanced Features

| Feature | Description |
|---------|-------------|
| **DRT** | 24/7 DDoS Response Team |
| **Cost Protection** | Credit for scaling costs |
| **Attack Visibility** | Real-time metrics |
| **WAF Integration** | WAF rules at no extra cost |
| **Health-Based Detection** | Route 53 health checks |
| **Proactive Engagement** | DRT contacts you |

---

# 🔥 12. AWS FIREWALL MANAGER

## Definition
AWS Firewall Manager is a security management service that allows you to centrally configure and manage firewall rules across your accounts and resources in AWS Organizations.

## Managed Policies

| Policy Type | Description |
|-------------|-------------|
| **WAF** | Web ACL rules |
| **Shield Advanced** | DDoS protection |
| **Security Groups** | VPC security groups |
| **Network Firewall** | VPC network firewall |
| **Route 53 DNS Firewall** | DNS filtering |

## Features

| Feature | Description |
|---------|-------------|
| **Centralized** | Manage across all accounts |
| **Auto-Remediation** | Auto-apply to new resources |
| **Compliance** | Ensure policy compliance |
| **Cross-Account** | Organization-wide |

---

# 🔍 13. AMAZON GUARDDUTY

## Definition
Amazon GuardDuty is a threat detection service that continuously monitors for malicious activity and unauthorized behavior.

## Data Sources

| Source | Description |
|--------|-------------|
| **CloudTrail Events** | Management & data events |
| **VPC Flow Logs** | Network traffic |
| **DNS Logs** | DNS queries |
| **EKS Audit Logs** | Kubernetes activity |
| **RDS Login Activity** | Database access |
| **S3 Data Events** | S3 access patterns |
| **Lambda Network Activity** | Function network |
| **EBS Malware Protection** | Volume scanning |

## Finding Types

| Category | Examples |
|----------|----------|
| **Reconnaissance** | Port scanning, API probing |
| **Instance Compromise** | Crypto mining, malware |
| **Account Compromise** | Unusual API calls |
| **Bucket Compromise** | S3 exfiltration |
| **Kubernetes** | Privileged containers |

## GuardDuty Features

| Feature | Description |
|---------|-------------|
| **Machine Learning** | Anomaly detection |
| **Threat Intelligence** | Known malicious IPs/domains |
| **Multi-Account** | Organization integration |
| **Auto-Archive** | Suppress expected findings |
| **EventBridge** | Automate responses |

---

# 🔍 14. AMAZON INSPECTOR

## Definition
Amazon Inspector is an automated vulnerability management service that continually scans AWS workloads for software vulnerabilities and unintended network exposure.

## Scan Types

| Type | Description |
|------|-------------|
| **EC2 Scanning** | OS vulnerabilities, network |
| **ECR Scanning** | Container image vulnerabilities |
| **Lambda Scanning** | Function code/dependencies |

## Features

| Feature | Description |
|---------|-------------|
| **Continuous** | Always scanning |
| **Agent-based** | SSM Agent for EC2 |
| **CVE Database** | Known vulnerabilities |
| **Prioritization** | Risk-based scoring |
| **SBOM** | Software Bill of Materials |

## Inspector vs GuardDuty

| Feature | Inspector | GuardDuty |
|---------|-----------|-----------|
| **Purpose** | Vulnerabilities | Threats |
| **Scans** | Workloads | Logs/events |
| **Proactive** | Yes | No (reactive) |
| **Output** | CVE findings | Threat findings |

---

# 🔍 15. AMAZON MACIE

## Definition
Amazon Macie is a data security service that uses machine learning and pattern matching to discover and protect sensitive data in AWS.

## Features

| Feature | Description |
|---------|-------------|
| **Data Discovery** | Find sensitive data in S3 |
| **Classification** | PII, financial, credentials |
| **Custom Identifiers** | Define your own patterns |
| **Automated** | Continuous or scheduled |
| **Multi-Account** | Organization support |

## Sensitive Data Types

| Category | Examples |
|----------|----------|
| **PII** | Names, SSN, addresses |
| **Financial** | Credit cards, bank accounts |
| **Credentials** | API keys, passwords |
| **Health** | PHI data |
| **Custom** | Your defined patterns |

---

# 🔍 16. AWS SECURITY HUB

## Definition
AWS Security Hub is a cloud security posture management service that performs security best practice checks, aggregates alerts, and enables automated remediation.

## Features

| Feature | Description |
|---------|-------------|
| **Aggregation** | Findings from multiple services |
| **Standards** | AWS best practices, CIS, PCI DSS |
| **Automation** | EventBridge integration |
| **Multi-Account** | Organization support |

## Integrated Services

- Amazon GuardDuty
- Amazon Inspector
- Amazon Macie
- IAM Access Analyzer
- AWS Firewall Manager
- AWS Config
- Third-party partners

## Security Standards

| Standard | Description |
|----------|-------------|
| **AWS Foundational** | AWS best practices |
| **CIS AWS Foundations** | CIS benchmarks |
| **PCI DSS** | Payment card industry |
| **NIST** | Federal standards |

---

# 🔍 17. AMAZON DETECTIVE

## Definition
Amazon Detective automatically collects log data and uses machine learning, statistical analysis, and graph theory to help you conduct faster and more efficient security investigations.

## Features

| Feature | Description |
|---------|-------------|
| **Automated** | Auto-collects data |
| **Visualization** | Interactive graphs |
| **Integration** | GuardDuty findings |
| **Root Cause** | Investigate origins |

---

# 📜 18. AWS CLOUDTRAIL

## Definition
AWS CloudTrail is a service that enables governance, compliance, operational auditing, and risk auditing of your AWS account.

## Event Types

| Type | Description | Default |
|------|-------------|---------|
| **Management Events** | Control plane (Create, Delete) | Yes |
| **Data Events** | Data plane (S3 Get, Lambda Invoke) | No |
| **Insight Events** | Unusual activity | No |

## Trail Types

| Type | Description |
|------|-------------|
| **Single-Region** | One region |
| **Multi-Region** | All regions (recommended) |
| **Organization** | All accounts |

## CloudTrail Configuration

| Setting | Description |
|---------|-------------|
| **S3 Bucket** | Log storage |
| **CloudWatch Logs** | Real-time analysis |
| **SNS** | Notifications |
| **Encryption** | SSE-S3 or SSE-KMS |
| **Log Validation** | Integrity verification |

## Key Points

| Point | Description |
|-------|-------------|
| **Default** | 90 days in Event History (free) |
| **Long-term** | Create trail to S3 |
| **Global Services** | Logged in us-east-1 |
| **Delay** | Up to 15 minutes |

---

# ⚙️ 19. AWS CONFIG

## Definition
AWS Config is a service that enables you to assess, audit, and evaluate the configurations of your AWS resources.

## Components

| Component | Description |
|-----------|-------------|
| **Configuration Items** | Point-in-time resource state |
| **Configuration History** | Changes over time |
| **Config Rules** | Compliance evaluation |
| **Conformance Packs** | Rule collections |
| **Remediation** | Auto-fix non-compliant |

## Rule Types

| Type | Description |
|------|-------------|
| **AWS Managed** | Pre-built rules |
| **Custom** | Lambda-based |

## Common Managed Rules

| Rule | Description |
|------|-------------|
| `restricted-ssh` | No SSH from 0.0.0.0/0 |
| `s3-bucket-public-read-prohibited` | No public S3 |
| `encrypted-volumes` | EBS encryption |
| `rds-instance-public-access-check` | No public RDS |
| `iam-user-mfa-enabled` | MFA required |
| `root-account-mfa-enabled` | Root MFA |
| `required-tags` | Tag compliance |

## Remediation

| Type | Method |
|------|--------|
| **Manual** | User initiates |
| **Automatic** | Auto-remediate |
| **Actions** | SSM Automation documents |

---

*Continued in Part 2: VPC Security, S3 Security, Database Security, Compute Security*

---

*Last Updated: December 2025*
*Exam Version: SAA-C03*
*Domain: Design Secure Architectures (30%)*
