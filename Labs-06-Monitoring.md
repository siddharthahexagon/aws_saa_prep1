# AWS SAA-C03 Hands-On Labs: Monitoring, Governance & Management

## 📋 Overview
These labs cover CloudWatch, CloudTrail, AWS Config, and Systems Manager.

**Estimated Time:** 3-4 hours total  
**Cost Estimate:** $1-2 (some features incur charges)  
**Free Tier Eligible:** Yes (with limitations)

---

## Lab 6.1: Amazon CloudWatch Metrics & Alarms

### Objectives
- View and understand EC2 metrics
- Create custom metrics
- Configure CloudWatch alarms
- Set up alarm actions

### Steps

#### Part A: View Default Metrics

**Step 1: Launch Test EC2 Instance**
```
1. Launch t2.micro Amazon Linux 2023 instance
2. Name: "lab-monitoring-instance"
3. Enable detailed monitoring (optional, costs extra)
4. User data:
#!/bin/bash
yum update -y
yum install -y httpd stress amazon-cloudwatch-agent
systemctl start httpd
systemctl enable httpd
```

**Step 2: View EC2 Metrics**
```
1. Go to CloudWatch → Metrics → All metrics
2. Select EC2 → Per-Instance Metrics
3. Select your instance
4. View available metrics:
   - CPUUtilization
   - NetworkIn/NetworkOut
   - DiskReadOps/DiskWriteOps
   - StatusCheckFailed
5. Note: Memory and disk space NOT included by default
```

**Step 3: Create Dashboard**
```
1. CloudWatch → Dashboards → Create dashboard
2. Name: "Lab-EC2-Dashboard"
3. Add widget:
   - Type: Line
   - Metrics: EC2 → Per-Instance Metrics
   - Select: CPUUtilization for your instance
4. Create widget
5. Add more widgets for Network, Status checks
6. Save dashboard
```

#### Part B: CloudWatch Alarms

**Step 4: Create CPU Alarm**
```
1. CloudWatch → Alarms → Create alarm
2. Select metric:
   - EC2 → Per-Instance Metrics
   - CPUUtilization → Your instance
3. Metric configuration:
   - Statistic: Average
   - Period: 1 minute
4. Conditions:
   - Threshold type: Static
   - Whenever CPUUtilization is: Greater > 70
5. Next
```

**Step 5: Configure Actions**
```
1. Notification:
   - In alarm: Create new topic
   - Topic name: "lab-cpu-alerts"
   - Email: your-email@example.com
2. EC2 action (optional):
   - In alarm: Stop this instance (for testing)
3. Auto Scaling action: Skip
4. Next
5. Name: "lab-high-cpu-alarm"
6. Create alarm
7. Confirm email subscription
```

**Step 6: Test Alarm**
```bash
# SSH to EC2 instance

# Generate CPU load
stress --cpu 4 --timeout 300

# Watch alarm in CloudWatch console
# Should transition: OK → INSUFFICIENT_DATA → ALARM
# Email notification should arrive
```

**Step 7: Create Composite Alarm**
```
1. CloudWatch → Alarms → Create alarm
2. Select "Composite alarm"
3. Rule:
   (ALARM(lab-high-cpu-alarm) AND ALARM(lab-status-check-alarm))
4. Configure actions
5. Name: "lab-critical-composite-alarm"
6. Create
```

#### Part C: Custom Metrics

**Step 8: Install CloudWatch Agent**
```bash
# SSH to EC2

# Install agent (if not in user data)
sudo yum install -y amazon-cloudwatch-agent

# Create configuration file
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard

# Quick configuration:
# - OS: Linux
# - EC2 or On-Premises: EC2
# - Root user: Yes
# - CollectD: No
# - Host metrics: Yes
# - CPU metrics per core: No
# - EC2 dimensions: Yes
# - Memory metrics: Yes
# - Disk metrics: Yes
# - Disk IO metrics: No
# - Network performance: No
# - Log files: No (for now)
# - Additional config: No
# - Store in SSM: Yes
```

**Step 9: Start CloudWatch Agent**
```bash
# Start agent with configuration
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
    -a fetch-config \
    -m ec2 \
    -s \
    -c file:/opt/aws/amazon-cloudwatch-agent/bin/config.json
```

**Step 10: View Custom Metrics**
```
1. CloudWatch → Metrics → All metrics
2. Find "CWAgent" namespace
3. View: mem_used_percent, disk_used_percent
4. Add to dashboard
```

#### Part D: Publish Custom Metric via CLI

**Step 11: Publish Custom Metric**
```bash
# From EC2 instance with appropriate IAM role

# Publish single metric
aws cloudwatch put-metric-data \
    --namespace "Lab/Application" \
    --metric-name "ActiveUsers" \
    --value 42 \
    --unit Count

# Publish with dimensions
aws cloudwatch put-metric-data \
    --namespace "Lab/Application" \
    --metric-name "RequestLatency" \
    --value 150 \
    --unit Milliseconds \
    --dimensions Environment=Production,Service=WebApp

# Script to publish periodically
while true; do
    VALUE=$(( RANDOM % 100 ))
    aws cloudwatch put-metric-data \
        --namespace "Lab/Application" \
        --metric-name "ActiveUsers" \
        --value $VALUE
    sleep 60
done
```

### Cleanup for Lab 6.1
```
1. Delete CloudWatch alarms
2. Delete CloudWatch dashboard
3. Delete SNS topic
4. Terminate EC2 instance
5. Custom metrics will age out automatically
```

---

## Lab 6.2: CloudWatch Logs

### Objectives
- Configure log agents
- Create log groups and streams
- Set up metric filters
- Query logs with Insights

### Steps

#### Part A: Application Logging

**Step 1: Configure CloudWatch Agent for Logs**
```bash
# Create agent config with logs
cat > /tmp/cw-agent-config.json << 'EOF'
{
    "agent": {
        "metrics_collection_interval": 60,
        "run_as_user": "root"
    },
    "logs": {
        "logs_collected": {
            "files": {
                "collect_list": [
                    {
                        "file_path": "/var/log/httpd/access_log",
                        "log_group_name": "/lab/httpd/access",
                        "log_stream_name": "{instance_id}"
                    },
                    {
                        "file_path": "/var/log/httpd/error_log",
                        "log_group_name": "/lab/httpd/error",
                        "log_stream_name": "{instance_id}"
                    },
                    {
                        "file_path": "/var/log/messages",
                        "log_group_name": "/lab/system/messages",
                        "log_stream_name": "{instance_id}"
                    }
                ]
            }
        }
    },
    "metrics": {
        "metrics_collected": {
            "mem": {
                "measurement": ["mem_used_percent"]
            },
            "disk": {
                "measurement": ["disk_used_percent"],
                "resources": ["/"]
            }
        }
    }
}
EOF

# Apply configuration
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
    -a fetch-config \
    -m ec2 \
    -s \
    -c file:/tmp/cw-agent-config.json
```

**Step 2: Generate Log Data**
```bash
# Generate HTTP traffic
for i in {1..100}; do
    curl localhost
    curl localhost/nonexistent
    sleep 1
done
```

**Step 3: View Logs**
```
1. CloudWatch → Logs → Log groups
2. Select /lab/httpd/access
3. Select log stream (instance ID)
4. View log events
```

#### Part B: Metric Filters

**Step 4: Create Metric Filter for Errors**
```
1. Select log group: /lab/httpd/access
2. Metric filters → Create metric filter
3. Filter pattern: [ip, user, timestamp, request, status=4*, size]
   OR simple: "404"
4. Test pattern with sample logs
5. Next
6. Filter name: "http-4xx-errors"
7. Metric namespace: "Lab/HTTPErrors"
8. Metric name: "4xxCount"
9. Metric value: 1
10. Create metric filter
```

**Step 5: Create Alarm on Filter**
```
1. CloudWatch → Alarms → Create alarm
2. Select metric: Lab/HTTPErrors → 4xxCount
3. Configure: > 10 errors in 5 minutes
4. Create alarm
```

#### Part C: CloudWatch Logs Insights

**Step 6: Query Logs**
```
1. CloudWatch → Logs → Log Insights
2. Select log groups: /lab/httpd/access
3. Sample queries:

# Top 10 client IPs
fields @timestamp, @message
| parse @message '* - - *' as clientip, rest
| stats count(*) by clientip
| sort count desc
| limit 10

# Request distribution by status
fields @timestamp, @message
| parse @message '* - - [*] "* * *" * *' as ip, time, method, path, protocol, status, size
| stats count(*) by status

# Errors over time
fields @timestamp, @message
| filter @message like /404|500/
| stats count() by bin(5m)
```

**Step 7: Save Query**
```
1. Run a useful query
2. Actions → Save query
3. Query name: "Error rate by time"
4. Log group: /lab/httpd/access
5. Save
```

### Cleanup for Lab 6.2
```
1. Delete metric filters
2. Delete log groups:
   - CloudWatch → Logs → Select group → Actions → Delete
3. Delete related alarms
```

---

## Lab 6.3: AWS CloudTrail

### Objectives
- Create CloudTrail trail
- View API activity
- Set up event alerts

### Steps

#### Part A: Create Trail

**Step 1: Create CloudTrail Trail**
```
1. Go to CloudTrail → Trails → Create trail
2. Trail name: "lab-management-trail"
3. Storage location:
   - Create new S3 bucket: Yes
   - Trail log bucket: lab-cloudtrail-[account-id]
4. Log file SSE-KMS encryption: No (for lab simplicity)
5. Next
```

**Step 2: Configure Events**
```
1. Event type:
   - Management events: Yes
   - Data events: Skip (additional cost)
2. Management events:
   - Read/Write events: All
   - Exclude AWS KMS events: Yes (reduce noise)
3. Next
4. Review and Create trail
```

#### Part B: Generate Activity

**Step 3: Perform Actions**
```
1. Create an EC2 instance
2. Create an S3 bucket
3. Create an IAM user
4. Wait 5-15 minutes for events to appear
```

**Step 4: View Events**
```
1. CloudTrail → Event history
2. Filter by:
   - Event name: RunInstances
   - Event name: CreateUser
   - User name: [your username]
3. View event details:
   - User identity
   - Source IP
   - Request parameters
   - Response
```

#### Part C: Query with Athena

**Step 5: Create Athena Table**
```
1. CloudTrail → Event history → Create Athena table
2. Storage location: Select your trail S3 bucket
3. Create table
```

**Step 6: Query CloudTrail with Athena**
```sql
-- View recent events
SELECT eventtime, eventname, useridentity.username, sourceipaddress
FROM cloudtrail_logs_lab_cloudtrail_[account_id]
WHERE eventtime > date_add('day', -1, current_date)
ORDER BY eventtime DESC
LIMIT 100;

-- Find IAM changes
SELECT eventtime, eventname, useridentity.username, sourceipaddress
FROM cloudtrail_logs_lab_cloudtrail_[account_id]
WHERE eventsource = 'iam.amazonaws.com'
AND eventtime > date_add('day', -7, current_date);

-- Find root logins
SELECT eventtime, eventname, sourceipaddress
FROM cloudtrail_logs_lab_cloudtrail_[account_id]
WHERE useridentity.type = 'Root'
AND eventtime > date_add('day', -30, current_date);
```

#### Part D: CloudTrail Alerts

**Step 7: Create EventBridge Rule**
```
1. Go to EventBridge → Rules → Create rule
2. Name: "lab-root-login-alert"
3. Event pattern:
{
  "source": ["aws.signin"],
  "detail-type": ["AWS Console Sign In via CloudTrail"],
  "detail": {
    "userIdentity": {
      "type": ["Root"]
    }
  }
}
4. Target: SNS topic (create or select)
5. Create rule
```

### Cleanup for Lab 6.3
```
1. Delete CloudTrail trail
2. Empty and delete S3 bucket
3. Delete Athena table
4. Delete EventBridge rules
```

---

## Lab 6.4: AWS Config

### Objectives
- Enable AWS Config
- Create Config rules
- Review compliance

### Steps

#### Part A: Enable AWS Config

**Step 1: Set Up AWS Config**
```
1. Go to AWS Config → Get started
2. Settings:
   - Recording: All resources
   - Include global resources: Yes
3. Delivery method:
   - S3 bucket: Create new bucket
   - SNS topic: Create new topic (optional)
4. AWS Config role: Create new role
5. Next
```

**Step 2: Add Config Rules**
```
1. Rules → Add rule
2. Select AWS managed rules:
   - ec2-instance-no-public-ip
   - s3-bucket-public-read-prohibited
   - s3-bucket-ssl-requests-only
   - encrypted-volumes
   - iam-user-mfa-enabled
3. Configure each rule (accept defaults)
4. Save
```

#### Part B: Check Compliance

**Step 3: View Compliance Dashboard**
```
1. AWS Config → Dashboard
2. View:
   - Compliant resources
   - Noncompliant resources
   - Resource count by type
```

**Step 4: Investigate Noncompliant Resource**
```
1. Click on noncompliant rule
2. View noncompliant resources
3. Click on specific resource
4. View timeline of configuration changes
5. View relationships to other resources
```

#### Part C: Remediation Actions

**Step 5: Set Up Auto-Remediation**
```
1. Select rule: s3-bucket-public-read-prohibited
2. Actions → Manage remediation
3. Remediation action: AWS-DisableS3BucketPublicReadWrite
4. Auto remediation: Yes (use with caution!)
5. Save
```

**Step 6: Manual Remediation**
```
1. View noncompliant resources
2. For each:
   - Click resource
   - View configuration
   - Go to resource console to fix
```

#### Part D: Advanced Query

**Step 7: Query Configuration**
```sql
-- Go to AWS Config → Advanced queries

-- Find all public S3 buckets
SELECT
  resourceId,
  resourceName,
  resourceType
WHERE
  resourceType = 'AWS::S3::Bucket'
  AND configuration.accessControlList.grantList.grantee.uri = 'http://acs.amazonaws.com/groups/global/AllUsers'

-- Find unencrypted EBS volumes
SELECT
  resourceId,
  resourceType,
  configuration.encrypted
WHERE
  resourceType = 'AWS::EC2::Volume'
  AND configuration.encrypted = false
```

### Cleanup for Lab 6.4
```
1. Delete Config rules
2. Stop Config recorder:
   - Config → Settings → Stop recording
3. Delete delivery channel
4. Empty and delete S3 bucket
5. Delete SNS topic
```

---

## Lab 6.5: AWS Systems Manager

### Objectives
- Use Session Manager for access
- Run commands remotely
- Manage patches

### Steps

#### Part A: Session Manager

**Step 1: Configure IAM Role**
```
1. Create or modify EC2 instance role
2. Attach policy: AmazonSSMManagedInstanceCore
3. Attach role to EC2 instance
```

**Step 2: Connect via Session Manager**
```
1. Systems Manager → Session Manager → Start session
2. Select your instance
3. Start session
4. No SSH keys needed!
```

**Step 3: View Session History**
```
1. Session Manager → Session history
2. View past sessions
3. Enable session logging:
   - Preferences → Edit
   - CloudWatch logging: Enable
   - S3 logging: Enable (optional)
   - Save
```

#### Part B: Run Command

**Step 4: Run Shell Commands**
```
1. Systems Manager → Run Command
2. Command document: AWS-RunShellScript
3. Targets: Select instances manually
4. Command parameters:
   #!/bin/bash
   echo "Hello from SSM"
   uptime
   df -h
   free -m
5. Run
```

**Step 5: View Output**
```
1. Click on command ID
2. Select instance
3. View output tab
4. View step 1 output
```

**Step 6: Install Software**
```
1. Run Command
2. Document: AWS-RunShellScript
3. Commands:
   yum install -y htop tree
   htop --version
4. Run and verify
```

#### Part C: Patch Manager

**Step 7: Create Patch Baseline**
```
1. Systems Manager → Patch Manager
2. Create patch baseline
3. Name: "lab-amazon-linux-baseline"
4. Operating system: Amazon Linux 2023
5. Auto-approval: 7 days after release
6. Classification: Security, Bugfix
7. Severity: Critical, Important
8. Create
```

**Step 8: Configure Patching**
```
1. Patch Manager → Configure patching
2. Instances: Select manually or by tag
3. Patching schedule:
   - Schedule type: Skip scheduling (on-demand)
   OR
   - Create maintenance window
4. Patch baseline: lab-amazon-linux-baseline
5. Configure patching
```

**Step 9: Scan for Patches**
```
1. Run Patch Manager scan
2. View compliance results
3. Identify missing patches
```

#### Part D: Parameter Store

**Step 10: Create Parameters**
```
1. Systems Manager → Parameter Store
2. Create parameter

Standard parameter:
- Name: /lab/database/host
- Type: String
- Value: mydb.example.com

Secure parameter:
- Name: /lab/database/password
- Type: SecureString
- KMS key: Use default
- Value: MySecretPassword123!
```

**Step 11: Use Parameters**
```bash
# From EC2 with appropriate IAM permissions

# Get parameter
aws ssm get-parameter --name /lab/database/host

# Get secure parameter (decrypted)
aws ssm get-parameter --name /lab/database/password --with-decryption

# In application code, use AWS SDK to retrieve
```

**Step 12: Reference in EC2 User Data**
```bash
#!/bin/bash
# Get parameter value
DB_HOST=$(aws ssm get-parameter --name /lab/database/host --query Parameter.Value --output text)
DB_PASS=$(aws ssm get-parameter --name /lab/database/password --with-decryption --query Parameter.Value --output text)

echo "Connecting to: $DB_HOST"
```

### Cleanup for Lab 6.5
```
1. Delete parameters from Parameter Store
2. Delete patch baselines (if not default)
3. Terminate EC2 instances
4. Delete IAM roles if created specifically for lab
```

---

## Lab 6.6: AWS Organizations & Service Control Policies

### Objectives
- Understand Organizations structure
- Create Service Control Policies (SCPs)
- Implement guardrails

### Steps

**Note:** This lab requires AWS Organizations enabled. Skip if not available.

#### Part A: View Organization

**Step 1: Access Organizations**
```
1. Go to AWS Organizations
2. View organization structure:
   - Root
   - Organizational Units (OUs)
   - Accounts
3. Note: Cannot create organization in sub-account
```

#### Part B: Service Control Policies

**Step 2: View SCPs**
```
1. Organizations → Policies → Service control policies
2. View attached policies
3. View default FullAWSAccess policy
```

**Step 3: Create Restrictive SCP**
```json
// Example: Prevent leaving organization
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Deny",
            "Action": [
                "organizations:LeaveOrganization"
            ],
            "Resource": "*"
        }
    ]
}

// Example: Region restriction
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Deny",
            "NotAction": [
                "cloudfront:*",
                "iam:*",
                "route53:*",
                "support:*"
            ],
            "Resource": "*",
            "Condition": {
                "StringNotEquals": {
                    "aws:RequestedRegion": [
                        "us-east-1",
                        "us-west-2"
                    ]
                }
            }
        }
    ]
}
```

### Cleanup for Lab 6.6
```
1. Detach SCPs from OUs
2. Delete custom SCPs
3. Note: Cannot delete organization easily
```

---

## 📝 Lab Summary & Key Takeaways

### What You Learned:
1. **CloudWatch Metrics**: Default vs custom metrics, dashboards
2. **CloudWatch Alarms**: Static thresholds, composite alarms
3. **CloudWatch Logs**: Agent setup, metric filters, Insights
4. **CloudTrail**: API logging, event history, Athena queries
5. **AWS Config**: Compliance rules, remediation
6. **Systems Manager**: Session Manager, Run Command, Parameter Store

### Exam-Relevant Points:
- CloudWatch: Default metrics every 5 minutes, detailed every 1 minute
- Custom metrics: Memory, disk space require CloudWatch agent
- CloudTrail: Enabled by default for 90 days, create trail for longer
- AWS Config: Resource configuration history and compliance
- Systems Manager: Requires IAM role, no SSH needed
- Parameter Store: Free for standard, charges for advanced
- Secrets Manager: Automatic rotation, higher cost than Parameter Store

---

**Next:** [Labs-07-Serverless](./Labs-07-Serverless.md)
