# AWS Security, Identity & Compliance - Complete Definitions (SAA-C03)

## Table of Contents
- [AWS IAM](#aws-iam)
- [AWS Organizations](#aws-organizations)
- [Amazon Cognito](#amazon-cognito)
- [AWS Directory Service](#aws-directory-service)
- [AWS KMS](#aws-kms)
- [AWS CloudHSM](#aws-cloudhsm)
- [AWS Certificate Manager](#aws-certificate-manager)
- [AWS Secrets Manager](#aws-secrets-manager)
- [AWS Systems Manager Parameter Store](#aws-systems-manager-parameter-store)
- [AWS WAF](#aws-waf)
- [AWS Shield](#aws-shield)
- [AWS Firewall Manager](#aws-firewall-manager)
- [Amazon GuardDuty](#amazon-guardduty)
- [Amazon Inspector](#amazon-inspector)
- [Amazon Macie](#amazon-macie)
- [AWS Security Hub](#aws-security-hub)
- [AWS Config](#aws-config)
- [AWS CloudTrail](#aws-cloudtrail)
- [Amazon Detective](#amazon-detective)

---

## AWS IAM (Identity and Access Management)

**Definition**: Web service for securely controlling access to AWS services and resources. Manage users, groups, roles, and their permissions.

**Key Components**:

### Users
- Individual identity with long-term credentials
- Can have password for console access
- Can have access keys for programmatic access
- Best practice: One user per person

### Groups
- Collection of users
- Permissions attached to group apply to all members
- Users can belong to multiple groups
- Cannot nest groups

### Roles
- Identity with permissions that can be assumed
- No long-term credentials
- Can be assumed by:
  - AWS services (EC2, Lambda)
  - Users (cross-account access)
  - External identities (federated users)
- Temporary security credentials

### Policies
- JSON documents defining permissions
- **Identity-based**: Attached to users, groups, roles
- **Resource-based**: Attached to resources (S3 bucket policy)
- **Permission boundaries**: Maximum permissions
- **Organizations SCPs**: Service Control Policies
- **Access Control Lists (ACLs)**: Legacy (S3, VPC)
- **Session policies**: Limit role session permissions

**Policy Structure**:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow" or "Deny",
      "Action": "service:action",
      "Resource": "arn:aws:service:region:account:resource",
      "Condition": { }
    }
  ]
}
```

**Policy Evaluation Logic**:
1. By default, all requests are denied (implicit deny)
2. Explicit allow overrides implicit deny
3. Explicit deny overrides explicit allow
4. Only allowed if no explicit deny and at least one explicit allow

**IAM Roles for Services**:
- EC2 Instance Roles (Instance Profile)
- Lambda Execution Roles
- ECS Task Roles
- Service-Linked Roles (created by AWS services)

**Security Best Practices**:
- Enable MFA for privileged users
- Use roles instead of access keys when possible
- Rotate credentials regularly
- Use IAM Access Analyzer
- Follow principle of least privilege
- Use policy conditions for extra security
- Enable CloudTrail for auditing

**Cross-Account Access**:
- IAM roles with trust policy
- Assume role from another account
- No need to share credentials
- Use for service access or user access

**IAM Access Analyzer**:
- Identifies resources shared with external entities
- Validates IAM policies
- Generates policies based on access activity

**IAM Policy Simulator**:
- Test and troubleshoot IAM policies
- Simulate API calls
- Validate policy effects

**STS (Security Token Service)**:
- Provides temporary credentials
- Used for role assumption
- Federation with external identities
- Cross-account access

**Use Cases**:
- User authentication and authorization
- Service-to-service authentication
- Cross-account access
- Federation with corporate directories
- Temporary access for applications

**Exam Tips**:
- IAM is global (not region-specific)
- Root account has full access (protect with MFA)
- Users have no permissions by default
- Groups cannot contain other groups
- Roles provide temporary credentials
- Use roles for EC2 instances (not access keys)
- Explicit deny always wins
- Resource-based policies for cross-account access
- Use IAM roles for applications on EC2
- Password policy and MFA for user security

---

## AWS Organizations

**Definition**: Account management service that enables you to consolidate multiple AWS accounts into an organization that you create and centrally manage.

**Key Features**:
- Centralized management of multiple accounts
- Consolidated billing
- Service Control Policies (SCPs)
- Account grouping with OUs
- Automated account creation
- Integration with other AWS services

**Organizational Units (OUs)**:
- Hierarchical grouping of accounts
- Can nest up to 5 levels deep
- Apply policies at OU level
- Root → OU → Sub-OU → Account

**Service Control Policies (SCPs)**:
- Define maximum available permissions
- Do not grant permissions (only limit)
- Apply to member accounts, not management account
- Filter permissions
- Inheritance through OU hierarchy

**SCP Strategies**:
- **Deny List**: Allow all by default, explicitly deny
- **Allow List**: Deny all by default, explicitly allow

**Management Account**:
- Root account that creates organization
- Cannot restrict with SCPs
- Pays all charges
- Full administrative control

**Member Accounts**:
- Accounts joined to organization
- Can be restricted by SCPs
- Can leave organization (if allowed)

**Consolidated Billing**:
- One bill for all accounts
- Volume discounts across accounts
- Reserved Instance sharing
- Savings Plans sharing
- Unused Reserved Instances applied to other accounts

**Features**:
- **Tag Policies**: Standardize tags across accounts
- **AI Services Opt-Out Policies**: Control AI service data usage
- **Backup Policies**: Centralized backup management

**Use Cases**:
- Multi-account strategy
- Environment separation (dev, test, prod)
- Department/team isolation
- Security and compliance boundaries
- Cost allocation and tracking

**Exam Tips**:
- Organizations for managing multiple AWS accounts
- SCPs define maximum permissions (don't grant permissions)
- SCPs don't apply to management account
- Consolidated billing provides volume discounts
- Use OUs for hierarchical account organization
- Reserved Instances and Savings Plans shared across accounts
- Move accounts between OUs to change policies
- Root account → OU → Account hierarchy
- Use for multi-account security strategy

---

## Amazon Cognito

**Definition**: Managed service providing authentication, authorization, and user management for web and mobile apps.

**Components**:

### Cognito User Pools

**Definition**: User directory for managing sign-up and sign-in for mobile and web apps.

**Key Features**:
- User registration and authentication
- Multi-factor authentication (MFA)
- Email and phone verification
- Password policies
- User profile data
- Social identity providers (Google, Facebook, Amazon, Apple)
- SAML/OIDC identity providers
- Lambda triggers for custom workflows
- Hosted UI for authentication

**User Pool Features**:
- Customizable authentication flow
- Account recovery
- User migration
- Advanced security (adaptive authentication, compromised credentials)
- App clients for access control

### Cognito Identity Pools (Federated Identities)

**Definition**: Provide temporary AWS credentials to access AWS services.

**Key Features**:
- Federate identities from multiple providers
- Guest (unauthenticated) access
- IAM roles for authenticated and unauthenticated users
- User-based access control
- Integration with User Pools

**Identity Providers**:
- Cognito User Pools
- Social (Google, Facebook, Amazon, Apple)
- SAML identity providers
- OpenID Connect providers
- Developer authenticated identities

**How It Works**:
1. Authenticate with identity provider
2. Get token from identity provider
3. Exchange token for temporary AWS credentials
4. Access AWS services with credentials

**Use Cases**:
- Mobile app authentication
- Web app authentication
- Temporary AWS access for users
- Social identity provider integration
- Guest access to AWS resources
- User profile management

**Exam Tips**:
- **User Pools**: User directory, authentication
- **Identity Pools**: AWS credentials for users
- User Pools provide JWT tokens
- Identity Pools provide temporary AWS credentials (STS)
- Can use User Pools and Identity Pools together
- MFA and adaptive authentication for security
- Cognito Sync for cross-device syncing (replaced by AppSync)
- Social and enterprise identity federation
- Lambda triggers for custom auth flows

---

## AWS Directory Service

**Definition**: Managed directory services for integrating AWS resources with existing on-premises Microsoft Active Directory or creating new directory in AWS cloud.

**Types**:

### AWS Managed Microsoft AD

**Definition**: Fully managed Microsoft Active Directory in AWS cloud.

**Key Features**:
- Actual Microsoft Active Directory
- Standalone or trust relationship with on-premises AD
- Multi-AZ deployment
- Automated backups
- Patch management
- LDAP support
- Schema extensions
- Supports up to 500,000 objects

**Use Cases**:
- Lift-and-shift of AD-dependent applications
- SharePoint, .NET applications, SQL Server
- Trust relationships with on-premises AD

### AD Connector

**Definition**: Directory gateway to redirect directory requests to on-premises Active Directory.

**Key Features**:
- Proxy to on-premises AD
- No caching
- Requires VPN or Direct Connect
- No data stored in AWS
- MFA support
- Seamless domain join

**Use Cases**:
- Use existing on-premises AD users with AWS
- AWS Management Console access with AD credentials
- No AD data replication to cloud

### Simple AD

**Definition**: Standalone directory powered by Samba 4, compatible with Microsoft AD.

**Key Features**:
- Lower cost than Managed Microsoft AD
- Supports up to 5,000 users (small) or 20,000 users (large)
- No trust relationships
- Basic AD features
- Linux-based

**Use Cases**:
- Simple user management
- Linux workloads
- Low-cost directory needs

**Comparison**:

| Feature | Managed Microsoft AD | AD Connector | Simple AD |
|---------|---------------------|--------------|-----------|
| **Type** | Full AD in AWS | Proxy to on-premises | Standalone basic AD |
| **Trust** | Yes | N/A | No |
| **Objects** | Up to 500K | Unlimited (on-premises) | Up to 20K |
| **Cost** | Higher | Lower | Lowest |
| **On-Premises Required** | No | Yes | No |

**Use Cases**:
- Windows applications requiring AD
- SSO for AWS applications
- Linux LDAP authentication
- EC2 domain join
- WorkSpaces and WorkDocs

**Exam Tips**:
- **Managed Microsoft AD**: Full AD in AWS, trust relationships
- **AD Connector**: Proxy to existing on-premises AD
- **Simple AD**: Standalone, basic, lower cost
- Use Managed Microsoft AD for lift-and-shift
- Use AD Connector if AD must stay on-premises
- Simple AD for basic directory needs
- Multi-AZ for high availability
- Integrate with IAM for AWS access

---

## AWS KMS (Key Management Service)

**Definition**: Managed service for creating and controlling encryption keys used to encrypt data.

**Key Features**:
- Create and manage encryption keys
- Integration with most AWS services
- Centralized key management
- Audit key usage via CloudTrail
- Automatic key rotation
- Compliance (FIPS 140-2 Level 2)

**Key Types**:

### Customer Managed Keys (CMK)
- You create and manage
- Full control over policies and rotation
- Can be enabled/disabled
- Can schedule deletion (7-30 days)

### AWS Managed Keys
- Created and managed by AWS services
- Automatic rotation every year
- Cannot manage directly
- Format: aws/service-name

### AWS Owned Keys
- Used by AWS services
- Not in your account
- Cannot view or manage

**Key Material Origin**:
- **KMS**: Key material generated in KMS (default)
- **External**: Import your own key material
- **CloudHSM**: Use CloudHSM key store

**Key Policies**:
- Resource-based policy for KMS key
- Default policy allows account root full access
- Can grant access to users, roles, other accounts

**Encryption Types**:
- **Symmetric**: Same key for encryption and decryption (AES-256)
- **Asymmetric**: Public/private key pair (RSA, ECC)

**Operations**:
- **Encrypt**: Encrypt data (up to 4 KB)
- **Decrypt**: Decrypt data
- **GenerateDataKey**: Generate data encryption key (DEK)
- **GenerateDataKeyWithoutPlaintext**: Generate encrypted DEK only
- **ReEncrypt**: Change encryption key without decrypting
- **EnableKeyRotation**: Enable automatic rotation

**Envelope Encryption**:
- Encrypt data with data key (DEK)
- Encrypt data key with master key (CMK)
- Used for encrypting large data
- AWS services use envelope encryption

**Multi-Region Keys**:
- Replicate keys across regions
- Same key ID and material in multiple regions
- Independent key policies per region
- Use for global applications

**Grants**:
- Programmatic delegation of key usage
- Temporary permissions
- Use with AWS services

**Use Cases**:
- Data encryption at rest
- Database encryption
- S3 object encryption
- EBS volume encryption
- Secrets encryption
- Digital signatures (asymmetric keys)

**Exam Tips**:
- KMS for creating and managing encryption keys
- Integrated with most AWS services
- Audit key usage with CloudTrail
- Automatic key rotation for AWS managed and customer managed keys
- Encrypt data up to 4 KB directly
- Use GenerateDataKey for larger data (envelope encryption)
- Key policies control access
- Can share keys across accounts
- Cannot recover deleted keys (7-30 day waiting period)
- Multi-region keys for global encryption

---

## AWS CloudHSM

**Definition**: Cloud-based hardware security module (HSM) that enables you to generate and use your own encryption keys in AWS cloud.

**Key Features**:
- Dedicated hardware (single-tenant)
- FIPS 140-2 Level 3 compliance
- You manage keys (AWS has no access)
- Tamper-resistant
- Symmetric and asymmetric keys
- SSL/TLS offloading
- Integration with custom applications

**Architecture**:
- Deploy in VPC
- High availability across multiple AZs
- Cluster of HSMs
- Automatic synchronization
- Load balancing across HSMs

**Use Cases**:
- Regulatory compliance (FIPS 140-2 Level 3)
- Custom cryptographic applications
- SSL/TLS acceleration
- Oracle TDE (Transparent Data Encryption)
- Key management when you need full control

**CloudHSM vs KMS**:

| Feature | KMS | CloudHSM |
|---------|-----|----------|
| **Tenancy** | Multi-tenant | Single-tenant |
| **Compliance** | FIPS 140-2 Level 2 | FIPS 140-2 Level 3 |
| **Key Management** | AWS manages | You manage |
| **Integration** | Native with AWS services | Custom integration |
| **Cost** | Lower | Higher |
| **Availability** | Managed by AWS | You configure cluster |

**Exam Tips**:
- CloudHSM for dedicated HSM and full control
- FIPS 140-2 Level 3 compliance
- Single-tenant (you control keys)
- Deploy in VPC, not integrated with AWS services by default
- Use for regulatory requirements needing Level 3
- More expensive than KMS
- Cluster for high availability
- Use KMS if you don't need dedicated HSM

---

## AWS Certificate Manager (ACM)

**Definition**: Managed service for provisioning, managing, and deploying SSL/TLS certificates for AWS services and internal connected resources.

**Key Features**:
- Free public SSL/TLS certificates
- Automatic renewal
- Integration with AWS services
- Private certificate authority (ACM PCA)
- Wildcard certificates
- Multiple domain names (SAN)

**Supported Services**:
- Elastic Load Balancing (ALB, NLB, CLB)
- CloudFront distributions
- API Gateway
- Elastic Beanstalk
- AWS Nitro Enclaves

**Certificate Types**:
- **Public Certificates**: For internet-facing resources
- **Private Certificates**: For internal resources (requires ACM PCA)

**Domain Validation Methods**:
- **DNS Validation**: Add CNAME record to DNS (recommended)
- **Email Validation**: Email to domain owner

**Certificate Renewal**:
- Automatic renewal for ACM-issued certificates
- Manual renewal for imported certificates
- 60 days before expiry, ACM attempts renewal

**ACM Private CA**:
- Managed private certificate authority
- Issue certificates for internal resources
- API-driven certificate issuance
- Integration with AWS services
- Audit logging

**Use Cases**:
- SSL/TLS for load balancers
- HTTPS for CloudFront distributions
- API Gateway custom domains
- Internal PKI infrastructure
- Certificate management automation

**Exam Tips**:
- ACM provides free public certificates
- Automatic renewal (managed by AWS)
- Cannot use ACM certificates outside supported AWS services
- Cannot export private keys of ACM-issued certificates
- Use ACM PCA for private certificates
- DNS validation preferred over email
- Wildcard certificates supported
- Can import third-party certificates
- Regional service (except CloudFront - use us-east-1)

---

## AWS Secrets Manager

**Definition**: Helps protect secrets needed to access applications, services, and IT resources. Rotate, manage, and retrieve database credentials, API keys, and other secrets.

**Key Features**:
- Automatic secret rotation
- Integration with RDS, Redshift, DocumentDB
- Encryption using KMS
- Fine-grained access control (IAM)
- Audit with CloudTrail
- Cross-region replication
- Versioning

**Secret Types**:
- Database credentials
- API keys
- OAuth tokens
- General text or binary secrets

**Rotation**:
- Automatic rotation for supported databases
- Custom Lambda function for other secrets
- Configurable rotation schedule
- Zero downtime rotation

**Secret Access**:
- Programmatic access via API/SDK
- IAM policies control access
- VPC endpoint for private access
- Resource-based policies

**Versioning**:
- AWSCURRENT: Current version
- AWSPENDING: Version being rotated to
- AWSPREVIOUS: Previous version

**Use Cases**:
- Database credential management
- API key rotation
- Application secrets
- Third-party service credentials
- Secure application configuration

**Exam Tips**:
- Secrets Manager for secret storage and rotation
- Automatic rotation for RDS, Redshift, DocumentDB
- Encrypted with KMS
- Charged per secret per month
- Versioning for rotation
- Cross-region replication for DR
- IAM policies control access
- Integration with CloudFormation for secret creation

---

## AWS Systems Manager Parameter Store

**Definition**: Secure, hierarchical storage for configuration data and secrets management. Part of AWS Systems Manager.

**Key Features**:
- Hierarchical storage
- Encryption with KMS (optional)
- Versioning
- Parameter policies (expiration, change notification)
- Integration with other AWS services
- No additional charge (standard parameters)

**Parameter Types**:
- **String**: Plain text
- **StringList**: Comma-separated values
- **SecureString**: Encrypted with KMS

**Parameter Tiers**:

| Feature | Standard | Advanced |
|---------|----------|----------|
| **Cost** | Free | Charges apply |
| **Parameters** | 10,000 per account | 100,000 per account |
| **Size** | 4 KB | 8 KB |
| **Policies** | No | Yes |
| **Change Notifications** | No | Yes |

**Parameter Hierarchy**:
```
/application/environment/parameter
/myapp/prod/database/password
/myapp/dev/database/password
```

**Parameter Policies** (Advanced Only):
- Expiration
- ExpirationNotification
- NoChangeNotification

**Use Cases**:
- Application configuration
- Environment-specific settings
- Secrets and credentials
- License keys
- Connection strings

**Secrets Manager vs Parameter Store**:

| Feature | Secrets Manager | Parameter Store |
|---------|----------------|-----------------|
| **Cost** | $$ | Free (standard) |
| **Rotation** | Automatic | Manual |
| **KMS Encryption** | Always | Optional |
| **Use Case** | Secrets, credentials | Configuration, parameters |
| **Versioning** | Yes | Yes |
| **Cross-Region** | Replication | No built-in |

**Exam Tips**:
- Parameter Store for configuration and secrets
- Free for standard parameters
- SecureString encrypted with KMS
- Hierarchical naming
- Integration with CloudFormation, Lambda, EC2, ECS
- Use Secrets Manager for automatic rotation
- Parameter Store cheaper but less features
- Can reference parameters in other AWS services

---

## AWS WAF (Web Application Firewall)

**Definition**: Web application firewall that helps protect web applications from common web exploits and bots that may affect availability, compromise security, or consume excessive resources.

**Key Features**:
- Layer 7 (application layer) protection
- Customizable security rules
- Managed rule groups
- Real-time metrics
- Bot control
- Rate limiting
- IP reputation lists

**Protected Resources**:
- CloudFront distributions
- Application Load Balancer (ALB)
- API Gateway
- AWS AppSync
- Cognito User Pools

**Rule Types**:
- **Regular Rules**: Standard match conditions
- **Rate-Based Rules**: Block IPs exceeding request threshold
- **Group Rules**: Logical combination of rules

**Match Conditions**:
- IP addresses
- HTTP headers
- HTTP body
- URI strings
- SQL injection patterns
- Cross-site scripting (XSS) patterns
- Geographic location
- Request size

**Actions**:
- **Allow**: Allow request
- **Block**: Block request (403 Forbidden)
- **Count**: Count but don't block (testing)
- **CAPTCHA**: Challenge with CAPTCHA
- **Challenge**: Silent challenge

**AWS Managed Rules**:
- Baseline rule groups
- Use-case specific (WordPress, PHP)
- IP reputation lists
- Known bad inputs
- Managed by AWS

**Web ACL**:
- Collection of rules
- Specify default action (allow or block)
- Rule priority (evaluated in order)
- Attach to protected resources

**Use Cases**:
- OWASP Top 10 protection
- Bot mitigation
- DDoS protection (layer 7)
- Rate limiting
- Geographic blocking
- Custom security rules

**Exam Tips**:
- WAF for layer 7 (application) protection
- Protects CloudFront, ALB, API Gateway
- Rule conditions: IP, headers, body, query strings
- Rate-based rules for DDoS protection
- AWS Managed Rules for common threats
- Web ACL defines rules and default action
- Count mode for testing before blocking
- Real-time metrics in CloudWatch
- Integration with Shield Advanced for DDoS

---

## AWS Shield

**Definition**: Managed Distributed Denial of Service (DDoS) protection service that safeguards applications running on AWS.

**Tiers**:

### AWS Shield Standard

**Features**:
- Automatic protection
- No additional cost
- Protection against common DDoS attacks
- Layer 3 and Layer 4 protection
- Protects all AWS customers

**Protected Resources**:
- CloudFront
- Route 53
- Global Accelerator
- Elastic Load Balancing
- Elastic IP addresses

### AWS Shield Advanced

**Features**:
- Enhanced DDoS protection
- 24/7 DDoS Response Team (DRT)
- Advanced attack detection
- Real-time notifications
- Cost protection (DDoS cost credits)
- Application layer (Layer 7) protection with WAF
- Integration with CloudFront, Route 53, ALB, NLB, Global Accelerator
- Historical attack reports
- $3,000/month per organization

**Additional Protections**:
- WAF included at no extra cost
- DDoS Response Team (DRT)
- Protection against increased charges
- Access to specialized DDoS expertise

**Use Cases**:
- Public-facing web applications
- Gaming applications
- E-commerce platforms
- APIs
- High-value applications

**Exam Tips**:
- **Shield Standard**: Free, automatic, layer 3/4 protection
- **Shield Advanced**: Paid, enhanced protection, 24/7 support, layer 7
- Shield Advanced includes WAF
- DDoS Response Team for Advanced tier
- Cost protection for Advanced tier
- Protects CloudFront, Route 53, Global Accelerator, ELB
- Use with WAF for layer 7 protection
- Shield Advanced expensive ($3K/month)

---

## AWS Firewall Manager

**Definition**: Security management service to centrally configure and manage firewall rules across accounts and applications in AWS Organizations.

**Key Features**:
- Centralized security policy management
- Automatic enforcement of policies
- Works across AWS Organizations
- Multi-account protection
- Compliance reporting
- Continuous protection

**Supported Policies**:
- AWS WAF rules
- AWS Shield Advanced protections
- VPC Security Groups
- AWS Network Firewall rules
- Route 53 Resolver DNS Firewall rules

**Use Cases**:
- Multi-account WAF management
- Centralized security group management
- Organization-wide firewall policies
- Compliance enforcement
- Automatic protection for new resources

**Exam Tips**:
- Firewall Manager for centralized security management
- Requires AWS Organizations
- Manages WAF, Shield, Security Groups, Network Firewall
- Automatic policy enforcement
- Use for multi-account environments
- Ensures new resources are automatically protected

---

## Amazon GuardDuty

**Definition**: Threat detection service that continuously monitors for malicious activity and unauthorized behavior to protect AWS accounts, workloads, and data.

**Key Features**:
- Continuous monitoring
- Threat intelligence (AWS, CrowdStrike, Proofpoint)
- Machine learning anomaly detection
- Automated security findings
- Integration with EventBridge
- Multi-account support

**Data Sources**:
- VPC Flow Logs
- CloudTrail event logs
- DNS logs
- Kubernetes audit logs
- S3 data events
- EBS volumes
- Lambda network activity
- RDS login activity

**Finding Types**:
- Reconnaissance (port scanning, unusual API calls)
- Instance compromise (malware, backdoors)
- Account compromise (credential theft, unusual behavior)
- Bucket compromise (suspicious S3 access)
- Malware (detected in EBS volumes, S3 objects)

**Severity Levels**:
- **Low**: Attempted attack
- **Medium**: Suspicious activity
- **High**: Clear indication of compromise

**Integration**:
- EventBridge for automated responses
- Lambda for remediation
- Security Hub for centralized findings
- SNS for notifications

**Use Cases**:
- Threat detection
- Unauthorized access detection
- Cryptocurrency mining detection
- Malware detection
- Account compromise detection
- Data exfiltration detection

**Exam Tips**:
- GuardDuty for threat detection
- Uses ML and threat intelligence
- Analyzes VPC Flow Logs, CloudTrail, DNS logs
- Continuous monitoring, no agents required
- Findings with severity levels
- Multi-account support with Organizations
- Integrate with EventBridge for automated remediation
- 30-day free trial
- Regional service

---

## Amazon Inspector

**Definition**: Automated vulnerability management service that continually scans AWS workloads for software vulnerabilities and unintended network exposure.

**Key Features**:
- Automated security assessments
- Continuous scanning
- EC2, ECR, Lambda scanning
- CVE (Common Vulnerabilities and Exposures)
- Risk-based prioritization
- Integration with Security Hub

**Assessment Types**:
- **Package vulnerabilities**: Known CVEs in software
- **Network reachability**: Unintended network exposure
- **Lambda code vulnerabilities**: Vulnerabilities in function code

**Supported Resources**:
- EC2 instances
- Container images in ECR
- Lambda functions

**How It Works**:
1. Deploy Systems Manager Agent (EC2) or configure ECR/Lambda
2. Continuous scanning for vulnerabilities
3. Generate findings with risk scores
4. Prioritize based on risk
5. Remediation guidance

**Risk Scoring**:
- CVSS (Common Vulnerability Scoring System)
- Inspector risk score (0-10)
- Considers exploitability and impact

**Use Cases**:
- Vulnerability management
- Compliance scanning
- Container image scanning
- Security posture improvement
- Continuous security assessment

**Exam Tips**:
- Inspector for automated vulnerability scanning
- Scans EC2, ECR, Lambda
- Continuous scanning (not one-time)
- Identifies software vulnerabilities (CVEs)
- Risk-based prioritization
- Requires Systems Manager Agent for EC2
- Automatic scanning of ECR images on push
- Integration with Security Hub

---

## Amazon Macie

**Definition**: Data security service that uses machine learning to discover, classify, and protect sensitive data in AWS.

**Key Features**:
- Automated sensitive data discovery
- ML and pattern matching
- PII (Personally Identifiable Information) detection
- Continuous monitoring
- S3 data classification
- Compliance reporting
- Findings in Security Hub

**Sensitive Data Types**:
- Credentials (API keys, passwords)
- Financial data (credit cards, bank accounts)
- Personal information (SSN, passport numbers)
- Health information (PHI)
- Custom data identifiers

**Discovery Jobs**:
- One-time or scheduled
- S3 bucket analysis
- Sampling or full scan
- Custom data identifiers

**Findings**:
- Policy violations
- Sensitive data discovery
- Security issues
- Classification results

**Integration**:
- EventBridge for automated responses
- Security Hub for centralized findings
- CloudWatch for metrics

**Use Cases**:
- Sensitive data discovery
- GDPR/CCPA compliance
- Data classification
- Prevent data exposure
- Compliance auditing

**Exam Tips**:
- Macie for sensitive data discovery in S3
- Uses ML to identify PII and sensitive data
- Automated discovery jobs
- Compliance reporting
- Findings sent to Security Hub
- Custom data identifiers for specific patterns
- Pay per GB scanned
- Regional service

---

## AWS Security Hub

**Definition**: Cloud security posture management service that automates security checks, aggregates security alerts, and enables automated remediation.

**Key Features**:
- Centralized security findings
- Automated compliance checks
- Security standards (CIS, PCI DSS, AWS Foundational Security Best Practices)
- Multi-account aggregation
- Integration with 50+ AWS and partner services
- Custom insights
- Automated remediation

**Security Standards**:
- AWS Foundational Security Best Practices
- CIS AWS Foundations Benchmark
- PCI DSS
- Custom standards

**Findings**:
- Aggregated from:
  - GuardDuty
  - Inspector
  - Macie
  - IAM Access Analyzer
  - Firewall Manager
  - Config
  - Third-party integrations

**Finding Format**:
- AWS Security Finding Format (ASFF)
- Standardized across services
- Severity (Critical, High, Medium, Low, Informational)

**Insights**:
- Collections of findings
- Filter and group findings
- Custom insights
- Track security trends

**Use Cases**:
- Centralized security view
- Compliance monitoring
- Multi-account security management
- Security operations
- Automated compliance reporting

**Exam Tips**:
- Security Hub for centralized security findings
- Aggregates from GuardDuty, Inspector, Macie, etc.
- Compliance checks (CIS, PCI DSS)
- Multi-account with Organizations
- Automated checks for security best practices
- Findings in standardized format (ASFF)
- Custom and managed insights
- Integration with EventBridge for remediation

---

## AWS Config

**Definition**: Service that enables you to assess, audit, and evaluate configurations of AWS resources. Continuous monitoring and recording of AWS resource configurations.

**Key Features**:
- Resource inventory
- Configuration history
- Configuration change notifications
- Compliance auditing
- Security analysis
- Configuration rules
- Conformance packs

**Components**:

### Configuration Items
- Point-in-time configuration of resource
- Metadata, attributes, relationships
- CloudTrail events

### Configuration Recorder
- Records configurations
- Delivers to S3 bucket
- Continuous or periodic

### Config Rules
- Desired configurations
- Managed rules (AWS-provided)
- Custom rules (Lambda-based)
- Evaluation triggers (on change, periodic)

### Conformance Packs
- Collection of Config rules
- Templated compliance framework
- Deploy across accounts/regions

**Managed Rules Examples**:
- S3 bucket public access
- EBS encryption
- IAM password policy
- Security group rules
- RDS encryption
- EC2 instance types

**Compliance**:
- Compliant or non-compliant resources
- Compliance timeline
- Configuration timeline
- Relationship view

**Remediation**:
- Manual remediation
- Automatic remediation (SSM Automation)
- Custom remediation (Lambda)

**Multi-Account Support**:
- Aggregator for multi-account view
- Works with Organizations
- Centralized compliance dashboard

**Use Cases**:
- Compliance auditing
- Security analysis
- Configuration tracking
- Resource change tracking
- Relationship mapping

**Exam Tips**:
- Config for resource configuration tracking and compliance
- Records configuration changes over time
- Config Rules for compliance checking
- Automatic remediation with SSM Automation
- Conformance packs for compliance frameworks
- Multi-account with Aggregator
- Delivers to S3 and SNS
- Regional service (aggregator for multi-region)
- Not for real-time notification (CloudWatch Events for that)

---

## AWS CloudTrail

**Definition**: Service that enables governance, compliance, operational auditing, and risk auditing of AWS account by logging and monitoring account activity.

**Key Features**:
- API call logging
- User activity tracking
- Event history (90 days)
- Trails for long-term storage
- Log file validation
- Multi-region and multi-account
- Integration with CloudWatch Logs

**Event Types**:

### Management Events
- Control plane operations
- Creating/deleting resources
- Configuring security
- Logging/monitoring setup
- Default logging

### Data Events
- Data plane operations
- S3 object-level operations
- Lambda function invocations
- DynamoDB item-level operations
- Not logged by default (high volume)

### Insight Events
- Unusual API activity
- ML-based detection
- Spike in API calls
- Error rate increases

**Trail Configuration**:
- **Single Region**: Events from one region
- **All Regions**: Events from all regions
- **Organization Trail**: All accounts in organization
- **Read/Write Events**: Separate logging
- **Management/Data Events**: Separate logging

**Log Delivery**:
- S3 bucket (primary)
- CloudWatch Logs (optional)
- EventBridge integration
- Log file integrity validation

**CloudTrail Lake**:
- Managed data lake for CloudTrail
- SQL-based querying
- 7-year retention
- Immutable event store
- Audit across accounts and regions

**Use Cases**:
- Security analysis
- Compliance auditing
- Operational troubleshooting
- Resource change tracking
- User activity monitoring
- Forensic investigation

**Exam Tips**:
- CloudTrail logs all API calls
- 90-day event history by default
- Create trail for long-term storage
- Management events logged by default
- Data events require explicit configuration
- Log to S3 for long-term retention
- Use CloudWatch Logs for alarms
- Insight Events for unusual activity detection
- Organization trail for multi-account logging
- CloudTrail Lake for querying across accounts
- Enable log file validation for integrity

---

## Amazon Detective

**Definition**: Security service that makes it easy to analyze, investigate, and quickly identify the root cause of potential security issues or suspicious activities.

**Key Features**:
- Automated data collection and analysis
- Machine learning and graph analysis
- Visual investigation
- Root cause analysis
- Integration with GuardDuty, Security Hub
- Historical security data (up to 1 year)

**Data Sources**:
- VPC Flow Logs
- CloudTrail logs
- GuardDuty findings
- EKS audit logs

**Visualizations**:
- Behavior graphs
- Entity relationships
- Timeline views
- Anomaly detection

**Investigation Flow**:
1. Finding from GuardDuty or Security Hub
2. Investigate in Detective
3. View behavior graphs and relationships
4. Analyze historical activity
5. Identify root cause
6. Take action

**Use Cases**:
- Security incident investigation
- Threat hunting
- Root cause analysis
- Understanding attack patterns
- Verifying GuardDuty findings

**Exam Tips**:
- Detective for security investigation and root cause analysis
- Uses ML and graph analysis
- Automatically collects data from VPC Flow Logs, CloudTrail, GuardDuty
- Visual investigation tools
- Integrates with GuardDuty and Security Hub
- Up to 1 year of historical data
- 30-day free trial

---

## Security Services Summary Table

| Service | Purpose | Key Use Case |
|---------|---------|--------------|
| **IAM** | Identity and Access | User/role permissions |
| **Organizations** | Multi-Account Management | Consolidated billing, SCPs |
| **Cognito** | User Authentication | Mobile/web app auth |
| **Directory Service** | Managed AD | Windows app integration |
| **KMS** | Key Management | Encryption key management |
| **CloudHSM** | Hardware Security Module | Dedicated HSM, full control |
| **ACM** | Certificate Management | SSL/TLS certificates |
| **Secrets Manager** | Secret Rotation | Database credentials |
| **Parameter Store** | Configuration Management | App configuration, secrets |
| **WAF** | Web Firewall | Layer 7 protection |
| **Shield** | DDoS Protection | DDoS mitigation |
| **Firewall Manager** | Centralized Firewall | Multi-account firewall |
| **GuardDuty** | Threat Detection | Continuous threat monitoring |
| **Inspector** | Vulnerability Scanning | EC2/ECR/Lambda vulnerabilities |
| **Macie** | Data Classification | Sensitive data discovery |
| **Security Hub** | Security Aggregation | Centralized security view |
| **Config** | Configuration Tracking | Compliance auditing |
| **CloudTrail** | API Logging | Activity auditing |
| **Detective** | Investigation | Root cause analysis |

---

## Exam Focus Areas

1. **IAM Policies**: Evaluation logic, least privilege
2. **IAM Roles**: When to use vs users
3. **Organizations SCPs**: How they limit permissions
4. **Cognito**: User Pools vs Identity Pools
5. **KMS vs CloudHSM**: Compliance requirements
6. **Secrets Manager vs Parameter Store**: Cost and features
7. **WAF**: Layer 7 protection, rule types
8. **Shield**: Standard vs Advanced
9. **GuardDuty**: Threat detection, data sources
10. **Security Hub**: Central security view, integrations
11. **CloudTrail**: Management vs Data events
12. **Config**: Compliance checking and remediation

---

*Last Updated: January 2026 - SAA-C03 Exam*
