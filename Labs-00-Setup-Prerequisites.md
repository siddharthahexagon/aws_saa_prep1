# AWS SAA-C03 Hands-On Labs: Setup & Prerequisites

## 📋 Overview
This guide covers the initial setup required before starting any hands-on labs.

---

## 🔧 Required Tools & Setup

### 1. AWS Account Setup
```
1. Go to https://aws.amazon.com/
2. Click "Create an AWS Account"
3. Complete registration with email, password, and payment method
4. Verify phone number
5. Select "Basic Support - Free" plan
```

### 2. Enable MFA for Root Account
```
1. Sign in to AWS Console as root user
2. Go to IAM → Security credentials
3. Click "Assign MFA device"
4. Select "Virtual MFA device"
5. Use Google Authenticator or Authy to scan QR code
6. Enter two consecutive MFA codes
7. Click "Assign MFA"
```

### 3. Create IAM Admin User
```
1. Go to IAM → Users → Create user
2. User name: "admin-user"
3. Select "Provide user access to the AWS Management Console"
4. Select "I want to create an IAM user"
5. Set custom password
6. Uncheck "Users must create a new password at next sign-in"
7. Click Next
8. Select "Attach policies directly"
9. Search and select "AdministratorAccess"
10. Click Next → Create user
11. Save the console sign-in URL and credentials
```

### 4. Install AWS CLI
**Windows (PowerShell as Admin):**
```powershell
msiexec.exe /i https://awscli.amazonaws.com/AWSCLIV2.msi
# Restart PowerShell after installation
aws --version
```

**macOS:**
```bash
curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
sudo installer -pkg AWSCLIV2.pkg -target /
aws --version
```

**Linux:**
```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
aws --version
```

### 5. Configure AWS CLI
```bash
# Create access keys first in IAM Console
# IAM → Users → admin-user → Security credentials → Create access key

aws configure
# AWS Access Key ID: [Enter your access key]
# AWS Secret Access Key: [Enter your secret key]
# Default region name: us-east-1
# Default output format: json

# Verify configuration
aws sts get-caller-identity
```

### 6. Set Up Budget Alerts (CRITICAL!)
```
1. Go to AWS Billing → Budgets
2. Click "Create budget"
3. Select "Cost budget - Recommended"
4. Budget name: "Monthly-Spend-Alert"
5. Enter budgeted amount: $10 (or your preferred limit)
6. Configure alerts:
   - Alert threshold: 80%
   - Email: your-email@example.com
7. Create budget
```

### 7. Enable Free Tier Usage Alerts
```
1. Go to Billing → Billing preferences
2. Check "Receive Free Tier Usage Alerts"
3. Enter email address
4. Save preferences
```

---

## 🛡️ Cost Management Best Practices

### Always Remember:
1. **Delete resources after labs** - Set reminders!
2. **Use Free Tier eligible services** when possible
3. **Stop EC2 instances** when not in use
4. **Delete EBS volumes** that are unattached
5. **Empty and delete S3 buckets** after labs
6. **Terminate NAT Gateways** - They charge hourly
7. **Delete Elastic IPs** not attached to running instances
8. **Check CloudWatch for resource usage**

### Cost-Saving Tips:
- Use `t2.micro` or `t3.micro` (Free Tier eligible)
- Stay in `us-east-1` region (most services free tier available)
- Use S3 Standard storage class
- Monitor daily spend in Cost Explorer

---

## 📁 Lab File Organization

| File | Domain | Topics Covered |
|------|--------|----------------|
| Labs-01-Compute | Domain 1 & 2 | EC2, Lambda, ECS, Auto Scaling |
| Labs-02-Storage | Domain 1 | S3, EBS, EFS, Storage Gateway |
| Labs-03-Database | Domain 1 | RDS, DynamoDB, Aurora, ElastiCache |
| Labs-04-Networking | Domain 1 & 3 | VPC, Route 53, CloudFront, ELB |
| Labs-05-Security | Domain 3 | IAM, KMS, Secrets Manager, WAF |
| Labs-06-Monitoring | Domain 4 | CloudWatch, CloudTrail, Config |
| Labs-07-Serverless | Domain 1 & 2 | Lambda, API Gateway, Step Functions |
| Labs-08-Containers | Domain 1 | ECS, EKS, Fargate, ECR |
| Labs-09-HA-DR | Domain 2 | Multi-AZ, Global Accelerator, Backup |
| Labs-10-Migration | Domain 2 | DMS, Snow Family, DataSync |

---

## ✅ Pre-Lab Checklist

Before starting any lab, verify:
- [ ] Signed in as IAM admin user (NOT root)
- [ ] Budget alerts configured
- [ ] Working in correct region (us-east-1 recommended)
- [ ] AWS CLI configured and working
- [ ] Notepad ready for saving resource IDs/ARNs
- [ ] Timer set to remind cleanup after lab

---

## 🚨 Emergency Cleanup

If you forget to clean up resources, use these commands:

```bash
# List all running EC2 instances
aws ec2 describe-instances --query 'Reservations[*].Instances[*].[InstanceId,State.Name,InstanceType]' --output table

# Terminate all running instances in a region
aws ec2 describe-instances --filters "Name=instance-state-name,Values=running" --query 'Reservations[*].Instances[*].InstanceId' --output text | xargs -n1 aws ec2 terminate-instances --instance-ids

# List all S3 buckets
aws s3 ls

# Delete S3 bucket (empty it first)
aws s3 rm s3://bucket-name --recursive
aws s3 rb s3://bucket-name

# List all RDS instances
aws rds describe-db-instances --query 'DBInstances[*].[DBInstanceIdentifier,DBInstanceStatus]' --output table

# Delete RDS instance (skip final snapshot)
aws rds delete-db-instance --db-instance-identifier instance-name --skip-final-snapshot
```

---

**Next:** Start with [Labs-01-Compute](./Labs-01-Compute.md)
