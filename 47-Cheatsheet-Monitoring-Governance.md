# AWS SAA-C03 CHEATSHEET: MONITORING, GOVERNANCE & COST

## 📋 PRINT-READY EXAM REFERENCE

---

# 📊 AMAZON CLOUDWATCH

## Definition
Monitoring and observability service for AWS resources.

## CloudWatch Components

| Component | Description |
|-----------|-------------|
| **Metrics** | Time-series data points |
| **Alarms** | React to metric thresholds |
| **Logs** | Store and query logs |
| **Events/EventBridge** | React to AWS events |
| **Dashboards** | Visual monitoring |
| **Insights** | Analyze logs |

## CloudWatch Metrics

### Metric Types

| Type | Description | Example |
|------|-------------|---------|
| **Default** | Auto-collected | EC2 CPUUtilization |
| **Custom** | User-defined | Application metrics |
| **High-resolution** | 1-second intervals | Custom metrics |

### EC2 Default Metrics
- CPUUtilization
- NetworkIn/Out
- DiskReadOps/WriteOps
- StatusCheck

### EC2 NOT Default (Need CloudWatch Agent)
- Memory utilization
- Disk space utilization
- Number of processes

### Metric Resolution

| Type | Interval | Cost |
|------|----------|------|
| **Standard** | 1 minute | Included |
| **Detailed** | 1 minute | Extra charge |
| **High-resolution** | 1 second | Extra charge |

## CloudWatch Alarms

### Alarm States

| State | Description |
|-------|-------------|
| **OK** | Within threshold |
| **ALARM** | Threshold breached |
| **INSUFFICIENT_DATA** | Not enough data |

### Alarm Actions

| Action | Description |
|--------|-------------|
| **SNS** | Send notification |
| **Auto Scaling** | Scale up/down |
| **EC2 Action** | Stop, terminate, reboot |

### Composite Alarms
- Combine multiple alarms
- AND/OR logic
- Reduce alarm noise

## CloudWatch Logs

### Log Components

| Component | Description |
|-----------|-------------|
| **Log Group** | Collection of streams |
| **Log Stream** | Sequence of events |
| **Log Event** | Single log entry |

### Log Sources
- EC2 (CloudWatch Agent)
- Lambda (automatic)
- ECS
- API Gateway
- CloudTrail
- Route 53
- VPC Flow Logs

### Log Features

| Feature | Description |
|---------|-------------|
| **Filter patterns** | Search logs |
| **Metric filters** | Create metrics from logs |
| **Subscription** | Real-time to destinations |
| **Retention** | 1 day to indefinite |
| **Export** | To S3 (not real-time) |

### Log Destinations
- Kinesis Data Streams
- Kinesis Firehose
- Lambda
- OpenSearch

### CloudWatch Logs Insights
- Query logs with SQL-like syntax
- Visualize results
- Auto-discovered fields

## CloudWatch Agent

### Purpose
- Collect system-level metrics
- Collect custom logs
- More detailed than default

### Unified Agent Features
- Memory, disk, swap
- Processes
- Custom application logs
- On-premises servers

## CloudWatch Contributor Insights

### Features
- Top-N contributors
- Real-time analysis
- VPC Flow Logs, CloudTrail, etc.

---

# 📜 AWS CLOUDTRAIL

## Definition
Track user activity and API usage across AWS.

## CloudTrail Events

| Event Type | Description |
|------------|-------------|
| **Management Events** | Control plane (create, delete) |
| **Data Events** | Data plane (S3 GetObject, Lambda Invoke) |
| **Insight Events** | Unusual activity |

### Management Events (Default ON)
- CreateBucket
- RunInstances
- CreateUser
- All API calls that manage resources

### Data Events (Must Enable)
- S3: GetObject, PutObject, DeleteObject
- Lambda: Invoke

### Insight Events
- Unusual API activity
- Error rate anomalies
- Write rate anomalies

## CloudTrail Configuration

| Setting | Description |
|---------|-------------|
| **Trail** | Configuration for logging |
| **Multi-region** | Log all regions |
| **Organization trail** | All accounts in org |
| **S3 bucket** | Store logs |
| **CloudWatch Logs** | Real-time analysis |
| **Encryption** | SSE-S3 or SSE-KMS |
| **Log file validation** | Integrity checking |

## CloudTrail Key Points

| Point | Description |
|-------|-------------|
| **Default** | 90 days in Event History |
| **Long-term** | Create trail to S3 |
| **Global services** | Logged in us-east-1 |
| **Delay** | Up to 15 minutes |

---

# ⚙️ AWS CONFIG

## Definition
Track resource configurations and compliance.

## Config Features

| Feature | Description |
|---------|-------------|
| **Configuration items** | Point-in-time resource state |
| **Configuration history** | Changes over time |
| **Config rules** | Compliance evaluation |
| **Conformance packs** | Collection of rules |
| **Remediation** | Auto-fix non-compliant |

## Config Rules

### Rule Types

| Type | Description |
|------|-------------|
| **AWS Managed** | Pre-built rules |
| **Custom** | Lambda-based rules |

### Common Managed Rules
- `restricted-ssh` - No SSH from 0.0.0.0/0
- `s3-bucket-public-read-prohibited`
- `encrypted-volumes`
- `required-tags`

### Trigger Types

| Trigger | Description |
|---------|-------------|
| **Configuration change** | When resource changes |
| **Periodic** | At regular intervals |

## Config Remediation

| Type | Description |
|------|-------------|
| **Manual** | User initiates |
| **Automatic** | Auto-remediate |

### Remediation Actions
- SSM Automation documents
- Built-in or custom

---

# 🏛️ AWS ORGANIZATIONS

## Definition
Central management of multiple AWS accounts.

## Organization Features

| Feature | Description |
|---------|-------------|
| **Root** | Top-level container |
| **OUs** | Organizational Units |
| **Accounts** | AWS accounts |
| **SCPs** | Service Control Policies |
| **Consolidated billing** | Single payment |

## Service Control Policies (SCPs)

### SCP Key Points
- Don't grant permissions
- Set maximum permissions
- Inherited by child OUs/accounts
- Management account NOT affected
- Default: FullAWSAccess

### SCP Strategies

| Strategy | Description |
|----------|-------------|
| **Denylist** | Allow all, deny specific |
| **Allowlist** | Deny all, allow specific |

## Consolidated Billing

| Benefit | Description |
|---------|-------------|
| **Single bill** | One payment method |
| **Volume discounts** | Combined usage |
| **Reserved Instance sharing** | Share RI across accounts |
| **Savings Plans sharing** | Share across accounts |

---

# 💰 AWS COST MANAGEMENT

## AWS Cost Explorer

### Features
- Visualize costs
- Forecast future costs
- Identify trends
- Filter by service, tag, region

### Cost Explorer Reports
- Monthly costs
- Daily costs
- Reserved Instance utilization
- Savings Plans utilization

## AWS Budgets

### Budget Types

| Type | Description |
|------|-------------|
| **Cost** | Monitor spending |
| **Usage** | Monitor usage |
| **Reservation** | RI/SP coverage |
| **Savings Plans** | SP utilization |

### Budget Actions
- IAM policies (restrict new resources)
- SCP application
- Stop EC2 or RDS instances

## AWS Cost and Usage Reports (CUR)

### Features
- Most detailed cost data
- Hourly or daily
- S3 delivery
- Integrate with Athena, Redshift, QuickSight

## Savings Plans

### Types

| Type | Flexibility | Discount |
|------|-------------|----------|
| **Compute** | EC2, Fargate, Lambda | Up to 66% |
| **EC2** | Instance family in region | Up to 72% |

### Key Points
- 1 or 3 year commitment
- Commit to $/hour
- Applied automatically

## Reserved Instances

### RI Types

| Type | Description |
|------|-------------|
| **Standard** | Up to 72% discount |
| **Convertible** | Up to 66%, can change attributes |
| **Scheduled** | Specific time windows |

### RI Key Points
- 1 or 3 year term
- Payment: All Upfront, Partial, No Upfront
- Can sell Standard on Marketplace

---

# 🛠️ AWS SYSTEMS MANAGER (SSM)

## Definition
Operations hub for AWS resources.

## SSM Features

| Feature | Description |
|---------|-------------|
| **Session Manager** | Secure shell without SSH |
| **Run Command** | Execute commands remotely |
| **Patch Manager** | Automate patching |
| **Parameter Store** | Store configuration/secrets |
| **State Manager** | Maintain instance state |
| **Inventory** | Collect metadata |
| **Automation** | Automate workflows |

## Session Manager

### Benefits
- No SSH keys to manage
- No bastion hosts needed
- IAM-based access
- Audit trail in CloudTrail
- Logging to S3 or CloudWatch

### Requirements
- SSM Agent installed
- IAM role with SSM permissions
- Outbound to SSM endpoints (or VPC endpoint)

## SSM Agent

### Pre-installed On
- Amazon Linux
- Amazon Linux 2
- Ubuntu 16.04, 18.04, 20.04
- Windows Server 2008 R2+

## Run Command

### Features
- Execute commands on multiple instances
- No SSH required
- Command document (JSON/YAML)
- Output to S3 or CloudWatch

## Patch Manager

### Features
- Define patch baselines
- Define maintenance windows
- Auto-approve patches
- Compliance reporting

## SSM Automation

### Features
- Predefined or custom runbooks
- Simplify maintenance
- Multi-account and region
- Integration with other services

---

# 🔧 AWS TRUSTED ADVISOR

## Definition
Best practice recommendations across 5 categories.

## Categories

| Category | Description |
|----------|-------------|
| **Cost Optimization** | Reduce spending |
| **Performance** | Improve performance |
| **Security** | Security vulnerabilities |
| **Fault Tolerance** | Increase availability |
| **Service Limits** | Approaching limits |

## Trusted Advisor Checks

### Basic (Free)

| Category | Checks |
|----------|--------|
| **Security** | S3 public, security groups, IAM |
| **Service Limits** | All service quotas |

### Business/Enterprise Support

| Category | Checks |
|----------|--------|
| **All** | Full checks |
| **Programmatic** | API access |
| **Refresh** | Weekly or on-demand |
| **Notifications** | Weekly email |

---

# 📋 AWS SERVICE CATALOG

## Definition
Create and manage approved service portfolios.

## Service Catalog Components

| Component | Description |
|-----------|-------------|
| **Portfolio** | Collection of products |
| **Product** | CloudFormation template |
| **Constraint** | Limits on product use |
| **Provisioned Product** | Deployed stack |

## Use Cases
- Self-service deployment
- Governance and compliance
- Standardized environments

---

# 🎯 MONITORING & GOVERNANCE HEURISTICS

| Keyword/Scenario | Answer |
|------------------|--------|
| Monitor metrics | **CloudWatch Metrics** |
| Memory/disk metrics | **CloudWatch Agent** |
| Alert on threshold | **CloudWatch Alarms** |
| Centralize logs | **CloudWatch Logs** |
| Query logs | **CloudWatch Logs Insights** |
| Track API calls | **CloudTrail** |
| Who did what | **CloudTrail** |
| Resource compliance | **AWS Config** |
| Auto-remediate | **Config + SSM Automation** |
| Multi-account management | **Organizations** |
| Limit account permissions | **SCPs** |
| Cost visualization | **Cost Explorer** |
| Spending alerts | **AWS Budgets** |
| Detailed cost data | **Cost and Usage Reports** |
| Secure shell without SSH | **Session Manager** |
| Run commands remotely | **Run Command** |
| Automate patching | **Patch Manager** |
| Best practices check | **Trusted Advisor** |
| Approved services | **Service Catalog** |

---

# 📊 MONITORING SERVICE SELECTION

| Need | Service |
|------|---------|
| Metrics & dashboards | CloudWatch |
| Logs & analysis | CloudWatch Logs |
| API audit | CloudTrail |
| Compliance | Config |
| Cost management | Cost Explorer, Budgets |
| Operations | Systems Manager |
| Best practices | Trusted Advisor |

---

# ✅ EXAM DAY QUICK CHECKS

```
□ Know CloudWatch default vs custom metrics
□ Know CloudTrail event types
□ Know Config rules and remediation
□ Know SCP inheritance and limitations
□ Know SSM features (Session Manager, Run Command)
□ Know Cost Explorer vs Budgets vs CUR
□ Know Trusted Advisor check categories
□ Know Savings Plans vs Reserved Instances
```

---

*Last Updated: December 2025*
*Exam Version: SAA-C03*
