# AWS SAA-C03: Security, Identity & Compliance Cheat Sheet

## 📋 Table of Contents
- [IAM (Identity and Access Management)](#iam)
- [AWS Organizations](#aws-organizations)
- [Cognito](#cognito)
- [Directory Service](#directory-service)
- [KMS (Key Management Service)](#kms)
- [Secrets Manager](#secrets-manager)
- [Parameter Store](#parameter-store)
- [Certificate Manager (ACM)](#acm)
- [WAF & Shield](#waf--shield)
- [GuardDuty](#guardduty)
- [Inspector](#inspector)
- [Macie](#macie)
- [Config](#config)
- [CloudTrail](#cloudtrail)
- [Security Hub](#security-hub)
- [Detective](#detective)

---

## IAM (Identity and Access Management)

### Definition
Web service for securely controlling access to AWS resources.

### Key Features
- **Users**: Individual identities with long-term credentials
- **Groups**: Collection of users with shared permissions
- **Roles**: Temporary credentials for AWS services or federated users
- **Policies**: JSON documents defining permissions
  - **Identity-based**: Attached to users, groups, roles
  - **Resource-based**: Attached to resources (S3, SQS, etc.)
  - **Permission Boundaries**: Max permissions a user/role can have
  - **Service Control Policies (SCPs)**: Organization-level restrictions
- **Policy Evaluation Logic**: Explicit Deny > Explicit Allow > Implicit Deny
- **MFA (Multi-Factor Authentication)**: Additional security layer
- **Access Keys**: For programmatic access (CLI, SDK)
- **IAM Roles for Services**: EC2, Lambda, etc. (not access keys)
- **Cross-Account Access**: Roles for accessing resources in other accounts
- **Identity Federation**: SAML 2.0, OpenID Connect, Web Identity
- **IAM Access Analyzer**: Identify resources shared externally
- **Credential Report**: List all IAM users and credential status
- **Access Advisor**: See service permissions used

### Exam Tips & Heuristics
✅ **When to Use IAM:**
- Control access to AWS resources
- Implement least privilege
- Manage users and permissions
- Enable federated access
- Audit and compliance

⚠️ **Common Pitfalls:**
- Never use root account for daily operations
- Don't share credentials or embed in code
- Use roles instead of access keys for EC2/Lambda
- IAM is global (not region-specific)
- Explicit Deny always wins

🎯 **Exam Heuristics:**
- "Control access" = **IAM**
- "EC2 needs AWS access" = **IAM Role** (not access keys)
- "Temporary credentials" = **IAM Role** or **STS**
- "Active Directory" = **Directory Service** or **IAM Identity Center**
- "External users" = **Cognito** or **IAM Federation**
- "Cross-account access" = **IAM Role** with trust policy
- "Least privilege" = Use specific permissions, not *
- "Explicit deny overrides allow" = Policy evaluation logic
- "Service-to-service access" = **IAM Role**
- "Find unused permissions" = **Access Advisor**

### IAM Best Practices
- Enable MFA for root and privileged users
- Use groups for permissions (not individual user policies)
- Use roles for applications on EC2/Lambda
- Rotate credentials regularly
- Apply least privilege principle
- Use policy conditions for additional security
- Enable CloudTrail for auditing

---

## AWS Organizations

### Definition
Account management service to consolidate multiple AWS accounts into an organization.

### Key Features
- **Organization Units (OUs)**: Hierarchical grouping of accounts
- **Service Control Policies (SCPs)**: Permission guardrails
  - Apply to OUs or accounts
  - Don't affect root user or service-linked roles
  - Restrict maximum permissions
- **Consolidated Billing**: Single payment for all accounts
- **Volume Discounts**: Aggregate usage across accounts
- **Reserved Instance Sharing**: Share across organization
- **Tag Policies**: Standardize tags across accounts
- **AI Services Opt-out Policies**: Control AI data usage
- **Backup Policies**: Centralized backup rules
- **Member Account Management**: Invite or create accounts

### Exam Tips & Heuristics
✅ **When to Use Organizations:**
- Manage multiple AWS accounts
- Centralized billing
- Enforce policies across accounts
- Cost optimization via aggregation
- Isolate workloads/environments

⚠️ **Common Pitfalls:**
- SCPs don't grant permissions (only restrict)
- SCPs don't affect management account (unless opt-in)
- Moving accounts between OUs affects SCPs
- Must explicitly allow services in SCPs

🎯 **Exam Heuristics:**
- "Multiple AWS accounts" = **AWS Organizations**
- "Centralized billing" = **Consolidated billing**
- "Restrict permissions across accounts" = **SCPs**
- "Prevent account from leaving" = **SCP** to deny leave organization
- "Cost optimization multiple accounts" = **Organizations** (volume discounts)
- "Isolate dev/test/prod" = **Separate accounts** with **Organizations**
- "Standardize tags" = **Tag Policies**

---

## Cognito

### Definition
User authentication, authorization, and user management service for web and mobile apps.

### Key Features

#### Cognito User Pools
- **User Directory**: Sign-up, sign-in, user profiles
- **Authentication**: Username/password, social identity providers
- **MFA**: SMS, TOTP
- **Hosted UI**: Pre-built sign-in pages
- **Lambda Triggers**: Customize auth workflows
- **Adaptive Authentication**: Risk-based authentication

#### Cognito Identity Pools (Federated Identities)
- **Temporary AWS Credentials**: Access AWS services directly
- **Identity Providers**: Cognito User Pools, social (Google, Facebook), SAML, OpenID
- **Unauthenticated Access**: Guest access with limited permissions
- **Fine-grained Access Control**: IAM policies based on user attributes

#### Cognito Sync (Deprecated)
- **Cross-device Sync**: Use **AppSync** instead

### Exam Tips & Heuristics
✅ **When to Use Cognito:**
- Mobile/web app authentication
- Social identity providers
- Temporary AWS credentials for users
- User management and profiles
- Serverless authentication

⚠️ **Common Pitfalls:**
- User Pools ≠ Identity Pools (authentication vs authorization)
- Identity Pools provide AWS credentials
- Cannot directly access AWS services with User Pools (need Identity Pools)
- Cognito Sync is deprecated (use AppSync)

🎯 **Exam Heuristics:**
- "Mobile app authentication" = **Cognito User Pools**
- "Social login" = **Cognito User Pools**
- "User needs AWS credentials" = **Cognito Identity Pools**
- "Guest access to S3" = **Cognito Identity Pools** (unauthenticated)
- "Web app sign-up/sign-in" = **Cognito User Pools**
- "Temporary AWS access for users" = **Cognito Identity Pools**
- "User data sync" = **AppSync** (not Cognito Sync)

### User Pools vs Identity Pools
| Feature | User Pools | Identity Pools |
|---------|-----------|---------------|
| Purpose | Authentication | Authorization (AWS access) |
| Provides | JWT tokens | AWS credentials (STS) |
| User Directory | Yes | No |
| Social Login | Yes | Yes (via User Pools) |
| AWS Service Access | No | Yes |

---

## Directory Service

### Definition
Managed Microsoft Active Directory service in AWS.

### Key Features
- **AWS Managed Microsoft AD**: Full-featured AD in AWS
  - Multi-AZ deployment
  - Trust relationships with on-premises AD
  - MFA support
- **AD Connector**: Proxy to on-premises AD
  - No caching, requires connectivity
  - MFA via existing RADIUS infrastructure
- **Simple AD**: Standalone, Samba-based directory
  - Small/large sizes
  - No trust relationships
  - Basic AD features

### Exam Tips & Heuristics
✅ **When to Use Directory Service:**
- Need AD in AWS
- Windows instances requiring domain join
- SSO to AWS applications
- Lift-and-shift Windows workloads

⚠️ **Common Pitfalls:**
- Simple AD doesn't support trust relationships
- AD Connector requires network connectivity
- Managed Microsoft AD is most feature-rich (but expensive)

🎯 **Exam Heuristics:**
- "Active Directory in AWS" = **AWS Managed Microsoft AD**
- "Proxy to on-premises AD" = **AD Connector**
- "Cheap AD for small workloads" = **Simple AD**
- "Trust relationship needed" = **AWS Managed Microsoft AD** (not Simple AD)
- "Windows domain" = **Directory Service**
- "AD for thousands of users" = **AWS Managed Microsoft AD**

---

## KMS (Key Management Service)

### Definition
Managed service to create and control encryption keys.

### Key Features
- **Customer Master Keys (CMKs)**: Logical representation of encryption keys
  - **AWS Managed**: Created by AWS services (free)
  - **Customer Managed**: You create and manage (charged)
  - **AWS Owned**: Used by AWS internally (not visible)
- **Key Material**: Can import your own or use AWS-generated
- **Key Policies**: Control access to CMKs
- **Grants**: Temporary, granular permissions
- **Envelope Encryption**: Encrypt data with data key, encrypt data key with CMK
- **Automatic Rotation**: Annual rotation for AWS-managed and customer-managed keys
- **Multi-Region Keys**: Replicate keys across regions
- **Key States**: Enabled, Disabled, Pending Deletion, Unavailable
- **CloudHSM Integration**: For FIPS 140-2 Level 3 compliance

### Exam Tips & Heuristics
✅ **When to Use KMS:**
- Encrypt data at rest
- Centralized key management
- Compliance requirements
- Integrate with AWS services
- Audit key usage (CloudTrail)

⚠️ **Common Pitfalls:**
- Cannot export CMKs (stays in KMS)
- Key deletion has 7-30 day waiting period
- Automatic rotation only for AWS-generated keys
- 4KB data limit (use envelope encryption for larger data)
- Cross-region copies require multi-region keys or re-encryption

🎯 **Exam Heuristics:**
- "Encryption key management" = **KMS**
- "Encrypt data at rest" = **KMS**
- "FIPS 140-2 Level 3" = **CloudHSM**
- "Import own keys" = **KMS** with external key material
- "Cross-region encrypted snapshots" = **Multi-region keys** or re-encrypt
- "Audit key usage" = **CloudTrail** with KMS
- "Large file encryption" = **Envelope encryption**
- "Automatic key rotation" = Enable in **KMS** (customer-managed keys)

---

## Secrets Manager

### Definition
Managed service to store, retrieve, and rotate secrets (passwords, API keys, etc.).

### Key Features
- **Automatic Rotation**: Built-in for RDS, Redshift, DocumentDB
- **Lambda-based Rotation**: Custom rotation for other secrets
- **Encryption**: Encrypted at rest with KMS
- **Fine-grained Access**: IAM and resource policies
- **Versioning**: Track secret versions
- **Cross-Region Replication**: Replicate secrets for DR
- **CloudFormation Integration**: Reference secrets in stacks
- **Monitoring**: CloudTrail, CloudWatch, EventBridge

### Exam Tips & Heuristics
✅ **When to Use Secrets Manager:**
- Store database credentials
- Automatic rotation required
- Application secrets
- API keys and tokens
- Compliance requirements

⚠️ **Common Pitfalls:**
- More expensive than Parameter Store
- Automatic rotation only for supported services
- Application must handle secret updates during rotation

🎯 **Exam Heuristics:**
- "Rotate secrets automatically" = **Secrets Manager**
- "Database password rotation" = **Secrets Manager**
- "Store secrets" + "automatic rotation" = **Secrets Manager**
- "Simple config values" = **Parameter Store** (cheaper)
- "API keys need rotation" = **Secrets Manager**
- "RDS password rotation" = **Secrets Manager**

---

## Parameter Store

### Definition
Secure, hierarchical storage for configuration data and secrets (part of Systems Manager).

### Key Features
- **Tiers**:
  - **Standard**: Free, 10,000 parameters, 4KB size, no policies
  - **Advanced**: Paid, 100,000 parameters, 8KB size, parameter policies
- **Types**: String, StringList, SecureString (KMS encryption)
- **Hierarchical**: Organize with paths (/app/dev/db-url)
- **Versioning**: Track parameter history
- **TTL Policies**: Expiration and notifications (Advanced tier)
- **Integration**: CloudFormation, Lambda, EC2, ECS, CodeBuild
- **No Automatic Rotation**: Manual or custom Lambda

### Exam Tips & Heuristics
✅ **When to Use Parameter Store:**
- Configuration management
- Store non-secret data
- Cost-sensitive secret storage
- Integration with Systems Manager
- Hierarchical organization

⚠️ **Common Pitfalls:**
- No automatic rotation (unlike Secrets Manager)
- Standard tier has limits
- SecureString requires KMS

🎯 **Exam Heuristics:**
- "Configuration management" = **Parameter Store**
- "Cost-effective secret storage" = **Parameter Store**
- "No automatic rotation needed" = **Parameter Store**
- "Need automatic rotation" = **Secrets Manager**
- "Hierarchical config" = **Parameter Store**
- "Free tier secrets" = **Parameter Store** (standard)

### Secrets Manager vs Parameter Store
| Feature | Secrets Manager | Parameter Store |
|---------|----------------|-----------------|
| Automatic Rotation | Yes (RDS, etc.) | No |
| Cost | Higher | Lower (standard free) |
| Size Limit | 64KB | 4KB (standard), 8KB (advanced) |
| TTL Policies | No | Yes (advanced tier) |
| Use Case | Secrets with rotation | Config, simple secrets |

---

## Certificate Manager (ACM)

### Definition
Managed service to provision, manage, and deploy SSL/TLS certificates.

### Key Features
- **Free Public Certificates**: For AWS services
- **Automatic Renewal**: ACM-issued certificates renew automatically
- **Integration**: CloudFront, ALB, API Gateway, Elastic Beanstalk
- **Private CA**: Create private certificate authority (paid)
- **Imported Certificates**: Bring your own (manual renewal)
- **Domain Validation**: DNS or email validation
- **Wildcard Certificates**: Support for *.example.com
- **Region-specific**: Must be in us-east-1 for CloudFront

### Exam Tips & Heuristics
✅ **When to Use ACM:**
- HTTPS for websites
- SSL/TLS termination on ALB
- CloudFront HTTPS
- API Gateway custom domains
- Free certificate management

⚠️ **Common Pitfalls:**
- Cannot export private keys (for AWS-issued certs)
- CloudFront requires certificates in us-east-1
- Imported certificates need manual renewal
- Cannot use with EC2 directly (use ALB/CloudFront)

🎯 **Exam Heuristics:**
- "HTTPS certificate" = **ACM**
- "CloudFront HTTPS" = **ACM** (us-east-1)
- "ALB SSL termination" = **ACM**
- "Free SSL certificate" = **ACM**
- "Private certificates" = **ACM Private CA**
- "EC2 HTTPS" = Install certificate directly (not ACM) or use **ALB + ACM**

---

## WAF & Shield

### Definition
Web application firewall and DDoS protection services.

### Key Features

#### WAF (Web Application Firewall)
- **Layer**: 7 (Application)
- **Integration**: CloudFront, ALB, API Gateway, AppSync
- **Rules**: IP sets, SQL injection, XSS, rate limiting, geo-match
- **Rule Groups**: Managed (AWS/Marketplace) or custom
- **Web ACLs**: Collection of rules
- **Logging**: Send to S3, CloudWatch Logs, Kinesis Firehose

#### Shield
- **Shield Standard**: Free, automatic DDoS protection
  - Layer 3/4 protection
  - Always-on detection and mitigation
- **Shield Advanced**: Paid, enhanced protection
  - 24/7 DDoS Response Team (DRT)
  - Cost protection (credits for scaling costs)
  - Advanced metrics and reports
  - Layer 7 protection with WAF included
  - Protected resources: EC2, ELB, CloudFront, Route 53, Global Accelerator

### Exam Tips & Heuristics
✅ **When to Use WAF & Shield:**
- Protect web applications from attacks
- DDoS protection
- Block malicious traffic
- Rate limiting
- Geo-blocking

⚠️ **Common Pitfalls:**
- WAF is Layer 7 only (use Shield for Layer 3/4)
- Shield Advanced costs $3,000/month
- Shield Standard is automatic (always enabled)
- WAF rules charged per rule

🎯 **Exam Heuristics:**
- "DDoS protection" = **Shield** (Standard automatic, Advanced for critical apps)
- "Block SQL injection" = **WAF**
- "Rate limiting" = **WAF**
- "Geo-blocking" = **WAF** geo-match or **CloudFront** geo restriction
- "Layer 7 protection" = **WAF**
- "Layer 3/4 DDoS" = **Shield Standard** (free, automatic)
- "Critical application DDoS" = **Shield Advanced**
- "Block specific IPs" = **WAF** IP set rules

---

## GuardDuty

### Definition
Intelligent threat detection service using machine learning.

### Key Features
- **Data Sources**: CloudTrail, VPC Flow Logs, DNS logs, EKS audit logs
- **Threat Detection**: Unusual API calls, unauthorized deployments, compromised instances
- **Findings**: Prioritized security findings
- **Integration**: EventBridge for automated response
- **Multi-Account**: Centralized in master account
- **Trusted IPs**: Whitelist IP addresses
- **Threat Lists**: Blacklist IP addresses

### Exam Tips & Heuristics
✅ **When to Use GuardDuty:**
- Automated threat detection
- Monitor account activity
- Detect compromised instances
- Cryptocurrency mining detection
- Continuous security monitoring

⚠️ **Common Pitfalls:**
- Doesn't prevent threats (only detects)
- 30-day free trial then charged
- Requires CloudTrail and VPC Flow Logs enabled

🎯 **Exam Heuristics:**
- "Threat detection" = **GuardDuty**
- "Detect compromised instances" = **GuardDuty**
- "Unusual API calls" = **GuardDuty**
- "Cryptocurrency mining" = **GuardDuty**
- "Automated response to threats" = **GuardDuty** + **EventBridge** + **Lambda**
- "Machine learning security" = **GuardDuty**

---

## Inspector

### Definition
Automated security assessment service for applications.

### Key Features
- **Assessment Types**: Network accessibility, security best practices
- **Agent-based**: Requires Inspector agent on EC2
- **EC2 Assessments**: CVE vulnerabilities, network reachability
- **ECR Scanning**: Container image vulnerabilities
- **Lambda Scanning**: Function code and dependencies
- **Findings**: Risk scores and remediation recommendations
- **Integrations**: Security Hub, EventBridge

### Exam Tips & Heuristics
✅ **When to Use Inspector:**
- Security assessments for EC2
- Vulnerability scanning
- Compliance validation
- Container image scanning
- Lambda security assessment

⚠️ **Common Pitfalls:**
- Requires agent on EC2 instances
- Only for EC2, ECR, Lambda (not all AWS services)
- Findings need manual or automated remediation

🎯 **Exam Heuristics:**
- "EC2 vulnerability scanning" = **Inspector**
- "Security assessment" = **Inspector**
- "Container image vulnerabilities" = **Inspector** (ECR)
- "Lambda vulnerabilities" = **Inspector**
- "CVE detection" = **Inspector**

---

## Macie

### Definition
ML-powered service to discover, classify, and protect sensitive data in S3.

### Key Features
- **Data Discovery**: Find PII, PHI, financial data
- **Classification**: ML-based content classification
- **S3 Focus**: Monitors S3 buckets
- **Sensitive Data Types**: Credit cards, SSN, passport numbers, etc.
- **Findings**: Dashboard and EventBridge integration
- **Multi-Account**: Centralized management

### Exam Tips & Heuristics
✅ **When to Use Macie:**
- Discover sensitive data in S3
- PII/PHI compliance
- Data classification
- S3 security assessment

⚠️ **Common Pitfalls:**
- Only for S3 (not other data stores)
- Costs based on data processed
- Requires enabling per account

🎯 **Exam Heuristics:**
- "Discover PII in S3" = **Macie**
- "Sensitive data classification" = **Macie**
- "Find credit card numbers" = **Macie**
- "S3 data security" = **Macie**
- "GDPR/HIPAA compliance for S3" = **Macie**

---

## Config

### Definition
Service to assess, audit, and evaluate configurations of AWS resources.

### Key Features
- **Configuration Recording**: Continuous recording of resource configs
- **Configuration History**: Track changes over time
- **Config Rules**: Managed or custom compliance rules
- **Conformance Packs**: Collection of Config rules
- **Remediation**: Automatic using Systems Manager Automation
- **Aggregators**: Multi-account, multi-region view
- **Snapshots**: Point-in-time configuration
- **Integrations**: S3, SNS, CloudWatch Events

### Exam Tips & Heuristics
✅ **When to Use Config:**
- Compliance auditing
- Resource inventory
- Configuration history
- Change management
- Security analysis
- Troubleshooting

⚠️ **Common Pitfalls:**
- Doesn't prevent changes (only records)
- Costs per configuration item recorded
- Rules evaluate but don't enforce (unless remediation configured)
- Not real-time (slight delay)

🎯 **Exam Heuristics:**
- "Configuration compliance" = **Config**
- "Track resource changes" = **Config**
- "Audit resource configurations" = **Config**
- "Automatic remediation" = **Config** + **Systems Manager Automation**
- "Who changed what when" = **CloudTrail** (API calls) or **Config** (config changes)
- "Security group compliance" = **Config Rules**

---

## CloudTrail

### Definition
Service that records AWS API calls and delivers log files.

### Key Features
- **Event Types**:
  - **Management Events**: Control plane operations (default)
  - **Data Events**: Resource operations (S3 object, Lambda invoke)
  - **Insights Events**: Unusual API activity
- **Trails**: Configuration for log delivery
- **Multi-Region**: Single trail for all regions
- **Multi-Account**: Organization trail
- **Log Storage**: S3 with optional encryption (KMS)
- **Log Validation**: Integrity verification
- **Integration**: CloudWatch Logs, EventBridge, Athena
- **90-Day History**: Free in console (longer requires trail)

### Exam Tips & Heuristics
✅ **When to Use CloudTrail:**
- Audit API calls
- Security analysis
- Compliance requirements
- Troubleshooting
- Operational issues
- Forensics

⚠️ **Common Pitfalls:**
- Data events cost extra (not enabled by default)
- 15-minute delay for log delivery
- Not real-time (use EventBridge for real-time)
- Management events free (1 copy per region)

🎯 **Exam Heuristics:**
- "Who made API call" = **CloudTrail**
- "Audit trail" = **CloudTrail**
- "Security analysis" = **CloudTrail**
- "Deleted resource, who did it?" = **CloudTrail**
- "Real-time API call response" = **EventBridge** (from CloudTrail)
- "S3 object-level logging" = **CloudTrail** data events or **S3 access logs**
- "Unusual API activity" = **CloudTrail Insights**
- "Compliance audit" = **CloudTrail** + **Config**

---

## Security Hub

### Definition
Centralized security and compliance service aggregating findings from multiple AWS services.

### Key Features
- **Findings Aggregation**: GuardDuty, Inspector, Macie, IAM Access Analyzer, Systems Manager, Firewall Manager
- **Third-Party Integration**: Partners like Palo Alto, Splunk, etc.
- **Security Standards**: CIS AWS Foundations, PCI DSS, AWS Best Practices
- **Compliance Scores**: Automated compliance checks
- **Automated Remediation**: EventBridge + Lambda
- **Multi-Account**: Centralized across organization
- **Custom Insights**: Filter and aggregate findings

### Exam Tips & Heuristics
✅ **When to Use Security Hub:**
- Centralized security management
- Compliance reporting
- Aggregate findings from multiple services
- Security posture assessment

⚠️ **Common Pitfalls:**
- Requires enabling source services (GuardDuty, etc.)
- Costs based on findings and compliance checks
- Doesn't replace individual security services

🎯 **Exam Heuristics:**
- "Centralized security dashboard" = **Security Hub**
- "Aggregate security findings" = **Security Hub**
- "Compliance standards" = **Security Hub**
- "CIS AWS Foundations" = **Security Hub**
- "Security across all accounts" = **Security Hub** with Organizations

---

## Detective

### Definition
Service to analyze, investigate, and identify root cause of security issues.

### Key Features
- **Data Sources**: VPC Flow Logs, CloudTrail, GuardDuty
- **Visualizations**: Interactive graphs and timelines
- **ML-based Analysis**: Identify patterns and anomalies
- **Investigation**: Root cause analysis
- **Integration**: GuardDuty findings as starting point
- **Multi-Account**: Centralized investigations

### Exam Tips & Heuristics
✅ **When to Use Detective:**
- Investigate security incidents
- Root cause analysis
- Understand GuardDuty findings
- Forensic analysis
- Threat hunting

⚠️ **Common Pitfalls:**
- Requires GuardDuty enabled
- Not for prevention (investigation only)
- Costs based on data ingested

🎯 **Exam Heuristics:**
- "Investigate security incident" = **Detective**
- "Root cause analysis" = **Detective**
- "Understand GuardDuty finding" = **Detective**
- "Visual investigation" = **Detective**
- "Threat hunting" = **Detective**

---

## 🎓 Security Service Selection Guide

| Requirement | Service |
|------------|---------|
| Access management | IAM |
| Multi-account management | AWS Organizations |
| User authentication | Cognito |
| Active Directory | Directory Service |
| Encryption keys | KMS |
| Secret rotation | Secrets Manager |
| Configuration storage | Parameter Store |
| SSL/TLS certificates | ACM |
| Web application firewall | WAF |
| DDoS protection | Shield |
| Threat detection | GuardDuty |
| Vulnerability scanning | Inspector |
| Sensitive data discovery | Macie |
| Configuration compliance | Config |
| API audit trail | CloudTrail |
| Centralized security | Security Hub |
| Security investigation | Detective |

---

## 💡 Key Exam Patterns

### Least Privilege
- Use IAM policies with specific permissions
- Implement permission boundaries
- Regular access reviews with Access Advisor
- Use SCPs for organization-wide restrictions

### Defense in Depth
- Multiple layers: Security Groups, NACLs, WAF
- Encryption at rest (KMS) and in transit (SSL/TLS)
- Detective controls: GuardDuty, Config, CloudTrail
- Automated response: EventBridge + Lambda

### Compliance & Auditing
- CloudTrail for API auditing
- Config for resource compliance
- Security Hub for standards compliance
- Macie for data classification
- AWS Artifact for compliance reports

### Incident Response
- GuardDuty for detection
- Detective for investigation
- CloudTrail for forensics
- EventBridge for automation
- Systems Manager for remediation

