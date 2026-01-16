# AWS SAA-C03 Hands-On Labs: Security, Identity & Compliance

## 📋 Overview
These labs cover IAM, KMS, Secrets Manager, WAF, and other security services.

**Estimated Time:** 3-4 hours total  
**Cost Estimate:** $1-2 (most services have free tier)  
**Free Tier Eligible:** Yes

---

## Lab 5.1: IAM Deep Dive

### Objectives
- Create IAM users, groups, and roles
- Implement IAM policies
- Configure MFA
- Understand permission boundaries

### Steps

#### Part A: IAM Users and Groups

**Step 1: Create IAM Groups**
```
1. Go to IAM → User groups → Create group
2. Group name: "lab-developers"
3. Attach policies:
   - AmazonEC2ReadOnlyAccess
   - AmazonS3ReadOnlyAccess
4. Create group

Repeat for:
- Group: "lab-admins" with AdministratorAccess
- Group: "lab-auditors" with SecurityAudit
```

**Step 2: Create IAM Users**
```
1. IAM → Users → Create user
2. User name: "lab-developer-1"
3. Provide user access to AWS Management Console
4. I want to create an IAM user
5. Custom password, uncheck "must reset"
6. Next

7. Add user to groups: lab-developers
8. Next → Create user
9. Save credentials

Repeat for:
- lab-admin-1 → lab-admins group
- lab-auditor-1 → lab-auditors group
```

**Step 3: Test User Permissions**
```
1. Open incognito/private browser window
2. Sign in as lab-developer-1
3. Go to EC2 → Try to launch instance
   Should FAIL (read-only)
4. Go to EC2 → Instances → View instances
   Should SUCCEED
5. Go to S3 → View buckets
   Should SUCCEED
6. Go to S3 → Try to create bucket
   Should FAIL
```

#### Part B: Custom IAM Policies

**Step 4: Create Custom Policy**
```
1. IAM → Policies → Create policy
2. Use JSON editor:

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowEC2DescribeAll",
            "Effect": "Allow",
            "Action": [
                "ec2:Describe*",
                "ec2:Get*"
            ],
            "Resource": "*"
        },
        {
            "Sid": "AllowEC2RunInstancesWithTag",
            "Effect": "Allow",
            "Action": [
                "ec2:RunInstances",
                "ec2:CreateTags"
            ],
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "aws:RequestTag/Environment": "Development"
                }
            }
        },
        {
            "Sid": "AllowEC2ManageOwnInstances",
            "Effect": "Allow",
            "Action": [
                "ec2:StartInstances",
                "ec2:StopInstances",
                "ec2:TerminateInstances"
            ],
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "ec2:ResourceTag/Environment": "Development"
                }
            }
        }
    ]
}

3. Name: "lab-dev-ec2-restricted"
4. Description: "Allow EC2 management only for Development tagged instances"
5. Create policy
```

**Step 5: Attach Custom Policy**
```
1. Go to IAM → User groups → lab-developers
2. Permissions → Add permissions → Attach policies
3. Search and select "lab-dev-ec2-restricted"
4. Attach policies
```

**Step 6: Test Custom Policy**
```
1. Sign in as lab-developer-1
2. Launch EC2 instance:
   - Add tag: Environment=Development
   - Should SUCCEED
3. Launch EC2 instance:
   - Add tag: Environment=Production
   - Should FAIL
4. Stop instance with Development tag
   - Should SUCCEED
```

#### Part C: IAM Roles for EC2

**Step 7: Create EC2 Instance Role**
```
1. IAM → Roles → Create role
2. Trusted entity type: AWS service
3. Use case: EC2
4. Next

5. Attach policies:
   - AmazonS3ReadOnlyAccess
   - CloudWatchAgentServerPolicy
6. Next

7. Role name: "lab-ec2-s3-cloudwatch-role"
8. Create role
```

**Step 8: Attach Role to EC2**
```
1. Launch or use existing EC2 instance
2. Select instance → Actions → Security → Modify IAM role
3. Select lab-ec2-s3-cloudwatch-role
4. Update IAM role
```

**Step 9: Test Role from EC2**
```bash
# SSH to EC2 instance

# Test S3 access (no credentials needed!)
aws s3 ls

# View role credentials (temporary)
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/lab-ec2-s3-cloudwatch-role
```

#### Part D: Permission Boundaries

**Step 10: Create Permission Boundary**
```
1. Create policy:

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:*",
                "cloudwatch:*",
                "logs:*"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Deny",
            "Action": [
                "s3:DeleteBucket",
                "s3:DeleteObject"
            ],
            "Resource": "*"
        }
    ]
}

2. Name: "lab-boundary-no-delete"
```

**Step 11: Apply Permission Boundary**
```
1. IAM → Users → lab-developer-1
2. Permission boundary → Set boundary
3. Select lab-boundary-no-delete
4. Set boundary

Note: Even if user has S3FullAccess, they cannot delete due to boundary
```

#### Part E: MFA Setup

**Step 12: Enable Virtual MFA**
```
1. IAM → Users → lab-admin-1
2. Security credentials → Assign MFA device
3. Device name: "lab-admin-mfa"
4. MFA device: Authenticator app
5. Scan QR code with authenticator app
6. Enter two consecutive codes
7. Add MFA
```

**Step 13: Create MFA-Required Policy**
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DenyAllExceptListedIfNoMFA",
            "Effect": "Deny",
            "NotAction": [
                "iam:CreateVirtualMFADevice",
                "iam:EnableMFADevice",
                "iam:GetUser",
                "iam:ListMFADevices",
                "iam:ListVirtualMFADevices",
                "iam:ResyncMFADevice",
                "sts:GetSessionToken"
            ],
            "Resource": "*",
            "Condition": {
                "BoolIfExists": {
                    "aws:MultiFactorAuthPresent": "false"
                }
            }
        }
    ]
}
```

### Cleanup for Lab 5.1
```
1. Remove MFA devices from users
2. Delete IAM users
3. Delete IAM groups
4. Delete IAM roles
5. Delete custom policies
6. Detach IAM role from EC2 if used
```

---

## Lab 5.2: AWS KMS (Key Management Service)

### Objectives
- Create customer managed keys
- Use KMS for encryption
- Implement key policies

### Steps

#### Part A: Create KMS Key

**Step 1: Create Symmetric Key**
```
1. Go to KMS → Customer managed keys
2. Create key
3. Key type: Symmetric
4. Key usage: Encrypt and decrypt
5. Next

6. Alias: "lab-encryption-key"
7. Description: "Lab encryption key for S3 and EBS"
8. Tags: Environment=Lab
9. Next
```

**Step 2: Configure Key Administrators**
```
1. Key administrators: Select your IAM user/role
2. Allow key administrators to delete this key: Yes
3. Next
```

**Step 3: Configure Key Users**
```
1. Key users: Select IAM users/roles that can use key
2. Add lab-ec2-s3-cloudwatch-role
3. Next
4. Review and Create key
```

#### Part B: Use KMS with S3

**Step 4: Create Encrypted S3 Bucket**
```
1. Create S3 bucket: "lab-kms-encrypted-[account-id]"
2. Default encryption:
   - Server-side encryption: Enable
   - Encryption type: SSE-KMS
   - AWS KMS key: Choose from your AWS KMS keys
   - Select: lab-encryption-key
3. Create bucket
```

**Step 5: Upload Encrypted Object**
```
1. Upload file to bucket
2. Properties → Server-side encryption settings
3. Verify encryption with lab-encryption-key
```

**Step 6: Test Access**
```bash
# From EC2 with role that has key usage permission
aws s3 cp s3://lab-kms-encrypted-[account-id]/test.txt ./

# From EC2 without key permission - should fail
```

#### Part C: KMS Key Rotation

**Step 7: Enable Automatic Rotation**
```
1. KMS → lab-encryption-key
2. Key rotation → Edit
3. Automatically rotate this KMS key every year: Enable
4. Save
```

#### Part D: Use KMS with EBS

**Step 8: Create Encrypted Volume**
```
1. EC2 → Volumes → Create volume
2. Volume type: gp3
3. Size: 10 GiB
4. Encryption: Encrypted
5. KMS key: lab-encryption-key
6. Create volume
```

**Step 9: Attach to EC2**
```
1. Select volume → Actions → Attach volume
2. Select EC2 instance
3. Attach

# Note: EC2 instance role must have kms:Decrypt permission
```

### Cleanup for Lab 5.2
```
1. Detach and delete EBS volume
2. Empty and delete S3 bucket
3. Schedule KMS key deletion (minimum 7 days):
   - KMS → Key → Schedule key deletion
   - Waiting period: 7 days
```

---

## Lab 5.3: AWS Secrets Manager

### Objectives
- Store and retrieve secrets
- Enable automatic rotation
- Integrate with applications

### Steps

#### Part A: Create Secret

**Step 1: Store Database Credentials**
```
1. Go to Secrets Manager → Store a new secret
2. Secret type: Credentials for Amazon RDS database
   OR
   Other type of secret (for manual entry)
3. Key/value pairs:
   - username: dbadmin
   - password: MySecurePassword123!
   - host: mydb.cluster-xxx.us-east-1.rds.amazonaws.com
   - port: 3306
   - dbname: myapp
4. Encryption key: Use default or lab-encryption-key
5. Next
```

**Step 2: Configure Secret**
```
1. Secret name: "lab/database/credentials"
2. Description: "Lab database credentials"
3. Tags: Environment=Lab
4. Next
```

**Step 3: Configure Rotation**
```
1. Automatic rotation: Disable (for lab)
   In production, enable with Lambda rotation function
2. Next
3. Review and Store
```

#### Part B: Retrieve Secret

**Step 4: Retrieve via Console**
```
1. Select secret → Retrieve secret value
2. View plaintext or key/value pairs
```

**Step 5: Retrieve via CLI**
```bash
# Get secret value
aws secretsmanager get-secret-value --secret-id lab/database/credentials

# Get specific version
aws secretsmanager get-secret-value --secret-id lab/database/credentials --version-stage AWSCURRENT

# Parse with jq
aws secretsmanager get-secret-value --secret-id lab/database/credentials --query SecretString --output text | jq -r '.password'
```

**Step 6: Retrieve via Python**
```python
# Save as get_secret.py

import boto3
import json

def get_secret(secret_name):
    client = boto3.client('secretsmanager')
    
    response = client.get_secret_value(SecretId=secret_name)
    
    if 'SecretString' in response:
        return json.loads(response['SecretString'])
    return None

# Usage
secret = get_secret('lab/database/credentials')
print(f"Username: {secret['username']}")
print(f"Host: {secret['host']}")
# Don't print password in real applications!
```

#### Part C: Secret Versioning

**Step 7: Update Secret**
```bash
# Update secret value
aws secretsmanager update-secret --secret-id lab/database/credentials \
    --secret-string '{"username":"dbadmin","password":"NewPassword456!","host":"mydb.xxx.rds.amazonaws.com","port":"3306","dbname":"myapp"}'

# Previous version is kept as AWSPREVIOUS
```

**Step 8: View Versions**
```bash
aws secretsmanager list-secret-version-ids --secret-id lab/database/credentials
```

### Cleanup for Lab 5.3
```
1. Delete secret:
   - Secrets Manager → Select secret → Actions → Delete secret
   - Schedule deletion (7-30 days)
   - Or use CLI for immediate deletion:
   aws secretsmanager delete-secret --secret-id lab/database/credentials --force-delete-without-recovery
```

---

## Lab 5.4: AWS WAF (Web Application Firewall)

### Objectives
- Create WAF Web ACL
- Configure rules
- Associate with ALB/CloudFront

### Steps

#### Part A: Create Web ACL

**Step 1: Create Web ACL**
```
1. Go to WAF & Shield → Web ACLs
2. Create web ACL
3. Name: "lab-web-acl"
4. Resource type: Regional resources (for ALB)
5. Region: us-east-1
6. Associated resources: Skip for now
7. Next
```

**Step 2: Add AWS Managed Rules**
```
1. Add rules → Add managed rule groups
2. AWS managed rule groups:
   - Core rule set (AWSManagedRulesCommonRuleSet): Add
   - SQL database (AWSManagedRulesSQLiRuleSet): Add
   - Known bad inputs (AWSManagedRulesKnownBadInputsRuleSet): Add
3. Add rules
4. Set rule priority (drag to reorder)
5. Next
```

**Step 3: Configure Default Action**
```
1. Default action: Allow
2. Next
3. Configure metrics (CloudWatch)
4. Next
5. Review and Create
```

#### Part B: Create Custom Rule

**Step 4: Add Rate-Based Rule**
```
1. Edit Web ACL → Add rules → Add my own rules
2. Rule type: Rate-based rule
3. Name: "rate-limit-rule"
4. Rate limit: 100 requests per 5 minutes
5. IP address to use: Source IP address
6. Criteria to count: Consider all requests
7. Action: Block
8. Add rule
```

**Step 5: Add IP Set Rule**
```
Create IP Set:
1. WAF → IP sets → Create IP set
2. Name: "lab-blocked-ips"
3. IP version: IPv4
4. IP addresses: 
   1.2.3.4/32
   5.6.7.8/32
5. Create

Add to Web ACL:
1. Edit Web ACL → Add rules → Add my own rules
2. Rule type: IP set
3. Name: "blocked-ip-rule"
4. IP set: lab-blocked-ips
5. IP address to use: Source IP address
6. Action: Block
7. Add rule
```

#### Part C: Associate with ALB

**Step 6: Associate Web ACL**
```
1. Select Web ACL
2. Associated AWS resources → Add
3. Select your ALB
4. Add
```

**Step 7: Test WAF**
```bash
# Normal request (should pass)
curl http://[ALB-DNS-NAME]/

# SQL injection attempt (should be blocked)
curl "http://[ALB-DNS-NAME]/?id=1' OR '1'='1"

# Check response - should be 403 Forbidden
```

#### Part D: View Logs

**Step 8: Enable Logging**
```
1. Select Web ACL → Logging and metrics
2. Enable logging
3. Destination: CloudWatch Logs log group
4. Create log group: "aws-waf-logs-lab"
5. Save
```

**Step 9: View Blocked Requests**
```
1. WAF → Web ACLs → lab-web-acl → Overview
2. View sampled requests
3. Filter by Action: Block
4. Review blocked requests details
```

### Cleanup for Lab 5.4
```
1. Disassociate Web ACL from ALB
2. Delete Web ACL
3. Delete IP sets
4. Delete CloudWatch log group
```

---

## Lab 5.5: AWS Shield and Network Security

### Objectives
- Understand Shield Standard vs Advanced
- Configure security groups best practices
- Set up VPC Flow Logs

### Steps

#### Part A: Review Shield Standard

**Step 1: View Shield Dashboard**
```
1. Go to WAF & Shield → AWS Shield
2. Review Shield Standard protection
3. Note: Shield Standard is automatic and free
4. View detected events (if any)
```

#### Part B: VPC Flow Logs

**Step 2: Enable Flow Logs**
```
1. Go to VPC → Your VPCs
2. Select lab-vpc
3. Flow logs tab → Create flow log
4. Filter: All
5. Maximum aggregation interval: 1 minute
6. Destination: Send to CloudWatch Logs
7. Destination log group: /vpc/flow-logs/lab-vpc
8. IAM role: Create new or use existing
9. Create flow log
```

**Step 3: Analyze Flow Logs**
```bash
# After some traffic, view in CloudWatch
1. CloudWatch → Logs → /vpc/flow-logs/lab-vpc
2. Select ENI log stream
3. Review traffic patterns:
   - Source/Destination IPs
   - Ports
   - ACCEPT/REJECT status
```

**Step 4: Query Flow Logs with Insights**
```
1. CloudWatch → Logs → Log Insights
2. Select log group
3. Sample queries:

# Top 10 talkers
fields @timestamp, srcAddr, dstAddr, bytes
| stats sum(bytes) as totalBytes by srcAddr
| sort totalBytes desc
| limit 10

# Rejected traffic
fields @timestamp, srcAddr, dstAddr, dstPort, action
| filter action = "REJECT"
| limit 100
```

#### Part C: Security Group Best Practices

**Step 5: Create Layered Security Groups**
```
Create Web Tier SG:
1. Name: "lab-web-tier-sg"
2. Inbound:
   - HTTP (80) from 0.0.0.0/0
   - HTTPS (443) from 0.0.0.0/0

Create App Tier SG:
1. Name: "lab-app-tier-sg"
2. Inbound:
   - Custom TCP (8080) from lab-web-tier-sg

Create DB Tier SG:
1. Name: "lab-db-tier-sg"
2. Inbound:
   - MySQL (3306) from lab-app-tier-sg
```

**Step 6: Test Security Group Chain**
```
Launch instances in each tier:
- Web instance with lab-web-tier-sg
- App instance with lab-app-tier-sg
- DB instance (or simulate) with lab-db-tier-sg

Test:
- External → Web (HTTP) ✓
- External → App (8080) ✗
- Web → App (8080) ✓
- App → DB (3306) ✓
```

### Cleanup for Lab 5.5
```
1. Delete VPC Flow Logs
2. Delete CloudWatch log groups
3. Delete security groups (after removing references)
4. Terminate test instances
```

---

## Lab 5.6: AWS Certificate Manager (ACM)

### Objectives
- Request SSL/TLS certificates
- Attach to ALB/CloudFront

### Steps

#### Part A: Request Certificate

**Step 1: Request Public Certificate**
```
1. Go to ACM → Request certificate
2. Certificate type: Request a public certificate
3. Next
4. Domain names:
   - Add your domain (must own/control)
   - Example: *.yourdomain.com
5. Validation method: DNS validation (recommended)
6. Key algorithm: RSA 2048
7. Request
```

**Step 2: Validate Certificate**
```
DNS Validation:
1. Click on certificate
2. Copy CNAME record details
3. Add CNAME record to Route 53 or your DNS provider
4. Wait for validation (can take up to 30 minutes)

Or use Route 53 quick validation:
1. Click "Create record in Route 53" if using Route 53
```

**Step 3: Attach to ALB**
```
1. EC2 → Load Balancers → Select ALB
2. Listeners → Add listener
3. Protocol: HTTPS
4. Port: 443
5. Default action: Forward to target group
6. Certificate: Select ACM certificate
7. Add
```

### Cleanup for Lab 5.6
```
1. Remove certificate from ALB
2. Delete certificate (not required if attached)
3. Remove DNS validation records if added manually
```

---

## 📝 Lab Summary & Key Takeaways

### What You Learned:
1. **IAM**: Users, groups, roles, policies, permission boundaries
2. **KMS**: Key management, encryption, rotation
3. **Secrets Manager**: Secret storage, rotation, retrieval
4. **WAF**: Web ACLs, managed rules, custom rules
5. **Shield**: DDoS protection basics
6. **ACM**: SSL/TLS certificate management

### Exam-Relevant Points:
- IAM Policies: Explicit Deny always wins
- Permission Boundaries: Maximum permissions for IAM entities
- KMS: Customer managed keys vs AWS managed keys
- KMS: Automatic rotation rotates key material annually
- Secrets Manager: Automatic rotation with Lambda
- WAF: Layer 7 protection, integrate with ALB/CloudFront/API Gateway
- Shield Standard: Free, automatic, all customers
- Shield Advanced: Paid, DDoS response team, cost protection

---

**Next:** [Labs-06-Monitoring](./Labs-06-Monitoring.md)
