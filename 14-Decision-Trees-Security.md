# AWS SAA-C03: Security, Identity & Compliance Decision Trees

## 🌳 Quick Navigation
- [Identity & Access Management](#identity--access-management)
- [Encryption & Key Management](#encryption--key-management)
- [Threat Detection & Compliance](#threat-detection--compliance)
- [Network Security](#network-security)
- [Application Security](#application-security)

---

## Identity & Access Management

```
START: Need access control?
│
├─→ Who needs access?
│   │
│   ├─→ AWS services (EC2, Lambda, etc.)?
│   │   └─→ **IAM Roles**
│   │       ├─→ Temporary security credentials
│   │       ├─→ No access keys needed
│   │       ├─→ Automatically rotated
│   │       ├─→ Types:
│   │       │   ├─→ Service Role: For AWS services (EC2, Lambda)
│   │       │   ├─→ Cross-Account Role: Access between accounts
│   │       │   └─→ Identity Provider Role: Federated access (SAML, OIDC)
│   │       └─→ **ALWAYS use roles for AWS services, not access keys**
│   │
│   ├─→ Human users (employees)?
│   │   ├─→ Single AWS account?
│   │   │   └─→ **IAM Users**
│   │   │       ├─→ Permanent credentials
│   │   │       ├─→ Password for console, access keys for CLI/API
│   │   │       ├─→ Organize with IAM Groups
│   │   │       └─→ Apply policies to groups, not individual users
│   │   │
│   │   └─→ Multiple AWS accounts or existing identity provider?
│   │       └─→ **AWS IAM Identity Center** (formerly AWS SSO)
│   │           ├─→ Single sign-on across AWS accounts
│   │           ├─→ Integration with AD, Azure AD, Okta, etc.
│   │           ├─→ Centralized access management
│   │           └─→ Use case: Enterprise, multiple accounts, existing IdP
│   │
│   ├─→ Mobile/web app users (customers)?
│   │   └─→ **Amazon Cognito**
│   │       ├─→ User Pools: Authentication (sign-up/sign-in)
│   │       │   ├─→ Email/password, social (Google, Facebook), SAML
│   │       │   └─→ JWT tokens
│   │       ├─→ Identity Pools: Authorization (AWS resource access)
│   │       │   ├─→ Trade tokens for temporary AWS credentials
│   │       │   └─→ Unauthenticated (guest) access supported
│   │       └─→ Use case: Mobile apps, web apps, customer authentication
│   │
│   └─→ On-premises users (Active Directory)?
│       └─→ **AWS Directory Service**
│           ├─→ AWS Managed Microsoft AD:
│           │   ├─→ Full AD features, trust with on-prem AD
│           │   └─→ Use case: AD-dependent apps, SSO
│           ├─→ AD Connector:
│           │   ├─→ Proxy to on-premises AD
│           │   └─→ Use case: Use existing on-prem AD
│           └─→ Simple AD:
│               ├─→ Standalone AD, Samba-based
│               └─→ Use case: Basic AD needs, no on-prem AD
│
├─→ What permissions needed?
│   ├─→ Pre-defined AWS permissions?
│   │   └─→ **AWS Managed Policies**
│   │       ├─→ Created and maintained by AWS
│   │       ├─→ Examples: AdministratorAccess, ReadOnlyAccess
│   │       └─→ Good starting point
│   │
│   ├─→ Custom permissions?
│   │   └─→ **Customer Managed Policies**
│   │       ├─→ You create and maintain
│   │       ├─→ Reusable across users/groups/roles
│   │       └─→ Follow least privilege principle
│   │
│   └─→ One-time inline permissions?
│       └─→ **Inline Policies**
│           ├─→ Embedded in single user/group/role
│           └─→ Not reusable (deleted with entity)
│
├─→ How to grant permissions?
│   ├─→ Identity-based (attach to user/group/role)
│   │   └─→ IAM policies attached to IAM identities
│   │
│   └─→ Resource-based (attach to resource)?
│       └─→ Policies on S3, SNS, SQS, KMS, etc.
│           └─→ Specify who can access the resource
│
└─→ Need to restrict permissions further?
    ├─→ **Permission Boundaries**
    │   ├─→ Maximum permissions an entity can have
    │   └─→ Use case: Delegated administration, prevent privilege escalation
    │
    └─→ **Service Control Policies (SCPs)**
        ├─→ Maximum permissions for AWS Organizations
        ├─→ Applied to OUs or accounts
        └─→ Does not grant permissions, only restricts
```

### IAM Policy Evaluation Logic

```
Decision Flow:
│
1. Explicit DENY?
   └─→ YES → DENY (stop here)
   └─→ NO → Continue

2. Explicit ALLOW?
   └─→ YES → ALLOW
   └─→ NO → DENY (implicit deny)

Factors considered:
- Identity-based policies
- Resource-based policies
- Permission boundaries
- SCPs (if using Organizations)
- Session policies (for assumed roles)

**Remember: Explicit DENY always wins!**
```

---

## Encryption & Key Management

```
START: Need encryption?
│
├─→ Where is data?
│   │
│   ├─→ Data at rest?
│   │   ├─→ S3?
│   │   │   └─→ S3 Encryption:
│   │   │       ├─→ SSE-S3: AWS-managed keys (AES-256)
│   │   │       │   └─→ Use case: Default, simple
│   │   │       ├─→ SSE-KMS: Customer managed keys (KMS)
│   │   │       │   └─→ Use case: Audit trail, key rotation control
│   │   │       ├─→ SSE-C: Customer-provided keys
│   │   │       │   └─→ Use case: You manage keys outside AWS
│   │   │       └─→ Client-side: Encrypt before upload
│   │   │           └─→ Use case: End-to-end encryption
│   │   │
│   │   ├─→ EBS?
│   │   │   └─→ EBS Encryption (KMS)
│   │   │       ├─→ Encrypt at volume creation
│   │   │       ├─→ Automatic: Data, snapshots, replicas
│   │   │       └─→ Minimal performance impact
│   │   │
│   │   ├─→ RDS/Aurora?
│   │   │   └─→ RDS Encryption (KMS)
│   │   │       ├─→ Encrypt at database creation
│   │   │       ├─→ Automatic: Backups, snapshots, replicas
│   │   │       └─→ Cannot encrypt existing DB (must create new)
│   │   │
│   │   └─→ Other services?
│   │       └─→ Most AWS services support KMS encryption
│   │
│   └─→ Data in transit?
│       ├─→ HTTPS/TLS?
│       │   └─→ **AWS Certificate Manager (ACM)**
│       │       ├─→ Free public SSL/TLS certificates
│       │       ├─→ Automatic renewal
│       │       ├─→ Integrates with: ELB, CloudFront, API Gateway
│       │       └─→ Use case: HTTPS for web apps, APIs
│       │
│       └─→ VPN?
│           └─→ Site-to-Site VPN automatically encrypts
│
├─→ How to manage keys?
│   │
│   ├─→ AWS-managed keys (simple)?
│   │   └─→ **AWS Managed Keys**
│   │       ├─→ Automatic rotation (every year)
│   │       ├─→ No management needed
│   │       └─→ Use case: Default encryption, simplicity
│   │
│   ├─→ Customer-managed keys (control)?
│   │   └─→ **AWS KMS** (Key Management Service)
│   │       ├─→ Customer Master Keys (CMKs)
│   │       ├─→ Symmetric (AES-256) or Asymmetric (RSA, ECC)
│   │       ├─→ Automatic rotation (optional, yearly)
│   │       ├─→ Key policies (who can use keys)
│   │       ├─→ Audit with CloudTrail
│   │       ├─→ Multi-region keys (replicate to other regions)
│   │       └─→ Use case: Compliance, audit requirements, fine-grained control
│   │
│   ├─→ Customer-provided keys (full control)?
│   │   └─→ **SSE-C** or **Client-side encryption**
│   │       ├─→ You manage keys entirely
│   │       └─→ Use case: Regulatory requirements, existing key management
│   │
│   └─→ Need dedicated hardware (compliance)?
│       └─→ **AWS CloudHSM** (Hardware Security Module)
│           ├─→ Dedicated hardware in AWS cloud
│           ├─→ FIPS 140-2 Level 3 compliance
│           ├─→ Single-tenant, you manage keys
│           ├─→ Clustered for HA
│           └─→ Use case: Contractual/regulatory requirements, asymmetric keys
│
└─→ Need to store secrets/credentials?
    ├─→ Simple configuration values?
    │   └─→ **AWS Systems Manager Parameter Store**
    │       ├─→ Free for Standard parameters
    │       ├─→ String, StringList, SecureString (KMS)
    │       ├─→ Versioning, hierarchies
    │       └─→ Use case: Configuration, non-sensitive data, cost optimization
    │
    └─→ Sensitive credentials (passwords, API keys)?
        └─→ **AWS Secrets Manager**
            ├─→ Automatic rotation for RDS, Redshift, DocumentDB
            ├─→ Cross-region replication
            ├─→ Fine-grained access control
            ├─→ Native integration with RDS, Lambda
            ├─→ Costs per secret + API calls
            └─→ Use case: Database credentials, API keys, automatic rotation
```

### Secrets Manager vs Parameter Store

| Feature | Secrets Manager | Parameter Store |
|---------|----------------|-----------------|
| **Cost** | $0.40/secret/month + API calls | Free (Standard), $0.05/advanced |
| **Rotation** | Automatic (built-in) | Manual (Lambda) |
| **Max Size** | 65 KB | 4 KB (Standard), 8 KB (Advanced) |
| **Cross-Region** | Yes | No |
| **Use Case** | Database credentials, secrets | Configuration, simple secrets |

---

## Threat Detection & Compliance

```
START: Need security monitoring?
│
├─→ Detect threats and anomalies?
│   └─→ **Amazon GuardDuty**
│       ├─→ Intelligent threat detection (ML-based)
│       ├─→ Analyzes: VPC Flow Logs, CloudTrail, DNS logs
│       ├─→ Detects: Compromised instances, reconnaissance, cryptocurrency mining
│       ├─→ Findings: Sent to Security Hub, EventBridge
│       ├─→ No agents needed
│       └─→ Use case: Continuous threat detection, anomaly detection
│
├─→ Assess vulnerabilities (EC2, container images)?
│   └─→ **Amazon Inspector**
│       ├─→ Automated vulnerability assessment
│       ├─→ EC2: Network and host vulnerabilities
│       ├─→ Container images (ECR): CVE scanning
│       ├─→ Lambda functions: Vulnerabilities
│       ├─→ Risk scores and remediation steps
│       └─→ Use case: Vulnerability management, compliance
│
├─→ Protect sensitive data (PII, PHI)?
│   └─→ **Amazon Macie**
│       ├─→ ML-powered data security for S3
│       ├─→ Discovers: PII, PHI, financial data
│       ├─→ Classifies and protects sensitive data
│       ├─→ Alerts on unusual access patterns
│       └─→ Use case: Data privacy, compliance (GDPR, HIPAA)
│
├─→ Monitor AWS resource configurations?
│   └─→ **AWS Config**
│       ├─→ Track resource configuration changes
│       ├─→ Config Rules: Compliance checks (managed or custom)
│       ├─→ Remediation: Auto-fix non-compliant resources
│       ├─→ Configuration timeline and history
│       ├─→ Integrates with: SSM, Lambda, CloudTrail
│       └─→ Use case: Compliance auditing, change management
│
├─→ Audit API calls and user activity?
│   └─→ **AWS CloudTrail**
│       ├─→ Records all API calls (who, what, when, where)
│       ├─→ Governance, compliance, auditing
│       ├─→ Trails: Multi-region, organization-wide
│       ├─→ Logs to: S3, CloudWatch Logs
│       ├─→ Integrity validation (detect tampering)
│       └─→ Use case: Audit trails, forensics, compliance
│
├─→ Centralized security view?
│   └─→ **AWS Security Hub**
│       ├─→ Aggregates findings from multiple services
│       ├─→ Sources: GuardDuty, Inspector, Macie, Config, Firewall Manager
│       ├─→ Security standards: CIS, PCI-DSS, AWS Foundational
│       ├─→ Automated compliance checks
│       ├─→ Prioritized findings
│       └─→ Use case: Centralized security dashboard, compliance reporting
│
├─→ Investigate security issues?
│   └─→ **Amazon Detective**
│       ├─→ Analyze, investigate, identify root cause
│       ├─→ Uses: VPC Flow Logs, CloudTrail, GuardDuty
│       ├─→ Visualizations and behavioral analysis
│       └─→ Use case: Security incident investigation
│
└─→ Compliance attestations?
    └─→ **AWS Artifact**
        ├─→ On-demand access to compliance reports
        ├─→ SOC, PCI, ISO, HIPAA, etc.
        └─→ Use case: Download compliance documents
```

### Security Monitoring Service Selection

| Service | Purpose | Data Sources |
|---------|---------|--------------|
| **GuardDuty** | Threat detection | VPC Flow, CloudTrail, DNS |
| **Inspector** | Vulnerability scanning | EC2, ECR, Lambda |
| **Macie** | Sensitive data discovery | S3 |
| **Config** | Configuration compliance | Resource configurations |
| **CloudTrail** | API audit logs | All AWS API calls |
| **Security Hub** | Centralized security | All of the above |
| **Detective** | Incident investigation | GuardDuty, VPC Flow, CloudTrail |

---

## Network Security

```
START: Network security needed?
│
├─→ Protect from DDoS attacks?
│   ├─→ Basic DDoS protection (free)?
│   │   └─→ **AWS Shield Standard**
│   │       ├─→ Automatic, always-on
│   │       ├─→ Protects: CloudFront, Route 53, ELB
│   │       ├─→ Layer 3 and 4 protection
│   │       └─→ Use case: Default protection (included free)
│   │
│   └─→ Advanced DDoS protection?
│       └─→ **AWS Shield Advanced**
│           ├─→ $3,000/month (org-wide)
│           ├─→ Enhanced protection for CloudFront, Route 53, ELB, EC2, Global Accelerator
│           ├─→ Real-time attack notifications
│           ├─→ DDoS Response Team (DRT)
│           ├─→ Cost protection (credits for scaling)
│           └─→ Use case: Business-critical applications, large DDoS risk
│
├─→ Web application firewall?
│   └─→ **AWS WAF** (Web Application Firewall)
│       ├─→ Layer 7 (HTTP/HTTPS) protection
│       ├─→ Protects: CloudFront, ALB, API Gateway, AppSync
│       ├─→ Rules: IP addresses, HTTP headers, body, URI strings
│       ├─→ Managed Rules: OWASP Top 10, known bad inputs
│       ├─→ Rate limiting
│       └─→ Use case: SQL injection, XSS, bot attacks
│
├─→ Centrally manage firewalls across accounts?
│   └─→ **AWS Firewall Manager**
│       ├─→ Central management of WAF, Shield Advanced
│       ├─→ Works with AWS Organizations
│       ├─→ Automatic enforcement across accounts
│       └─→ Use case: Large organizations, consistent security policies
│
├─→ Network firewall (VPC)?
│   └─→ **AWS Network Firewall**
│       ├─→ Managed network firewall for VPCs
│       ├─→ Layer 3-7 protection
│       ├─→ Stateful and stateless rules
│       ├─→ IDS/IPS capabilities
│       └─→ Use case: VPC-level firewall, outbound filtering
│
└─→ Private connectivity to AWS services?
    └─→ **AWS PrivateLink** (VPC Endpoints)
        ├─→ Private access without internet gateway
        ├─→ Interface Endpoints (ENI) or Gateway Endpoints
        └─→ Use case: Private access to S3, DynamoDB, SaaS
```

---

## Application Security

```
START: Application security?
│
├─→ API security?
│   └─→ **API Gateway**
│       ├─→ Authentication: IAM, Cognito, Lambda authorizers
│       ├─→ API keys and usage plans
│       ├─→ Throttling and quotas
│       ├─→ Request/response validation
│       └─→ WAF integration
│
├─→ User authentication for web/mobile apps?
│   └─→ **Amazon Cognito**
│       ├─→ User Pools: Sign-up/sign-in
│       ├─→ Identity Pools: AWS credential access
│       ├─→ MFA support
│       └─→ Social and enterprise IdP integration
│
├─→ Container security?
│   ├─→ Scan images for vulnerabilities
│   │   └─→ **Amazon Inspector** (for ECR)
│   │
│   └─→ Runtime security
│       └─→ **GuardDuty** for EKS protection
│
└─→ Secrets in application code?
    └─→ **AWS Secrets Manager** or **Parameter Store**
        └─→ Never hardcode credentials!
```

---

## Exam Scenarios - Quick Answers

### Scenario 1: EC2 needs to access S3, most secure method
**Answer**: IAM Role attached to EC2 instance

### Scenario 2: Mobile app users need to authenticate and access AWS resources
**Answer**: Amazon Cognito (User Pools + Identity Pools)

### Scenario 3: Encrypt S3 objects with customer-managed keys, need audit trail
**Answer**: SSE-KMS (S3 with KMS encryption)

### Scenario 4: Store database credentials with automatic rotation
**Answer**: AWS Secrets Manager

### Scenario 5: Need SSL/TLS certificate for load balancer
**Answer**: AWS Certificate Manager (ACM)

### Scenario 6: Detect compromised EC2 instances automatically
**Answer**: Amazon GuardDuty

### Scenario 7: Scan EC2 instances for vulnerabilities
**Answer**: Amazon Inspector

### Scenario 8: Track all API calls for compliance audit
**Answer**: AWS CloudTrail

### Scenario 9: Automatically check if S3 buckets are public
**Answer**: AWS Config with Config Rules

### Scenario 10: Protect web application from SQL injection
**Answer**: AWS WAF

### Scenario 11: DDoS protection with 24/7 support
**Answer**: AWS Shield Advanced

### Scenario 12: Discover PII data in S3 buckets
**Answer**: Amazon Macie

### Scenario 13: Centralized security findings from multiple services
**Answer**: AWS Security Hub

### Scenario 14: Investigate security incident root cause
**Answer**: Amazon Detective

### Scenario 15: Access on-premises Active Directory from AWS
**Answer**: AWS Directory Service (AD Connector)

---

## 🎯 Key Takeaways

### Identity & Access:
- **IAM Roles**: For AWS services (EC2, Lambda) - ALWAYS use instead of access keys
- **IAM Users**: For human users in single account
- **Cognito**: For mobile/web app users (customers)
- **IAM Identity Center**: For enterprise SSO across multiple accounts

### Encryption:
- **KMS**: Customer-managed keys, audit trail, automatic rotation
- **CloudHSM**: Dedicated hardware, FIPS 140-2 Level 3
- **ACM**: Free SSL/TLS certificates
- **Secrets Manager**: Store secrets with automatic rotation

### Threat Detection:
- **GuardDuty**: Intelligent threat detection (ML)
- **Inspector**: Vulnerability assessment (EC2, containers, Lambda)
- **Macie**: Discover sensitive data (PII) in S3
- **Config**: Configuration compliance and remediation

### Audit & Compliance:
- **CloudTrail**: Audit all API calls
- **Security Hub**: Centralized security dashboard
- **Detective**: Investigate security incidents
- **Artifact**: Download compliance reports

### Network Security:
- **Shield Standard**: Free DDoS protection
- **Shield Advanced**: Enhanced DDoS + DRT support
- **WAF**: Web application firewall (Layer 7)
- **Network Firewall**: VPC-level firewall (Layer 3-7)

### Best Practices:
1. Use IAM roles for AWS services
2. Enable MFA for privileged users
3. Encrypt data at rest and in transit
4. Use least privilege principle
5. Enable CloudTrail in all regions
6. Use GuardDuty for threat detection
7. Rotate credentials regularly
8. Use Secrets Manager for automatic rotation

---

*Use these decision trees to quickly identify the right security service!*
