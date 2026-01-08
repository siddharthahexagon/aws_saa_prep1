# AWS Management, Monitoring & Governance - Complete Definitions (SAA-C03)

## Table of Contents
- [Amazon CloudWatch](#amazon-cloudwatch)
- [AWS CloudFormation](#aws-cloudformation)
- [AWS Systems Manager](#aws-systems-manager)
- [AWS Trusted Advisor](#aws-trusted-advisor)
- [AWS Service Catalog](#aws-service-catalog)
- [AWS Control Tower](#aws-control-tower)
- [AWS License Manager](#aws-license-manager)
- [AWS Health Dashboard](#aws-health-dashboard)
- [AWS Compute Optimizer](#aws-compute-optimizer)
- [AWS Cost Explorer](#aws-cost-explorer)
- [AWS Budgets](#aws-budgets)
- [AWS Cost and Usage Report](#aws-cost-and-usage-report)
- [AWS Resource Groups](#aws-resource-groups)
- [AWS Tag Editor](#aws-tag-editor)

---

## Amazon CloudWatch

**Definition**: Monitoring and observability service for AWS resources and applications. Collects and tracks metrics, collects log files, sets alarms, and automatically reacts to changes.

**Key Components**:

### CloudWatch Metrics

**Definition**: Time-ordered data points that represent your AWS resources and applications.

**Key Features**:
- Default metrics from AWS services (CPU, network, disk)
- Custom metrics (application metrics)
- Metric resolution (standard: 1 min, high: 1 sec)
- Metric math
- Metric filters on logs
- Retention: 15 months

**Common Metrics**:
- **EC2**: CPUUtilization, NetworkIn/Out, DiskReadOps/WriteOps
- **EBS**: VolumeReadBytes, VolumeWriteBytes, VolumeIdleTime
- **ELB**: RequestCount, TargetResponseTime, HealthyHostCount
- **RDS**: DatabaseConnections, CPUUtilization, FreeableMemory
- **Lambda**: Invocations, Duration, Errors, Throttles

**Note**: EC2 default monitoring does NOT include memory or disk utilization (need CloudWatch Agent)

### CloudWatch Logs

**Definition**: Centralized log management system for AWS resources and applications.

**Key Features**:
- Log groups and log streams
- Log retention policies (indefinite to 1 day)
- Export to S3, Kinesis, Lambda
- Metric filters (create metrics from logs)
- Insights for querying logs
- Subscription filters for real-time processing
- Log encryption with KMS

**Log Sources**:
- EC2 instances (via CloudWatch Agent)
- Lambda functions
- VPC Flow Logs
- CloudTrail
- Route 53
- Custom applications

**CloudWatch Logs Insights**:
- Query and analyze log data
- Purpose-built query language
- Visualizations
- Save queries

### CloudWatch Alarms

**Definition**: Watch metrics and automatically initiate actions based on defined thresholds.

**Alarm States**:
- **OK**: Metric within threshold
- **ALARM**: Metric breached threshold
- **INSUFFICIENT_DATA**: Not enough data

**Actions**:
- SNS notifications
- Auto Scaling actions
- EC2 actions (stop, terminate, reboot, recover)
- Systems Manager actions

**Alarm Types**:
- **Static Threshold**: Fixed threshold value
- **Anomaly Detection**: ML-based dynamic threshold
- **Composite Alarms**: Combine multiple alarms

**Evaluation**:
- Evaluation periods
- Datapoints to alarm
- Missing data treatment

### CloudWatch Dashboards

**Definition**: Customizable home pages for monitoring resources and applications.

**Key Features**:
- Visualize metrics and logs
- Cross-region and cross-account
- Automatic refresh
- Graph types: line, stacked area, number, gauge
- Shareable dashboards

### CloudWatch Events / EventBridge

**Definition**: Event bus for connecting applications using events from AWS services, SaaS, and custom applications.

**Note**: CloudWatch Events is the legacy name; now called Amazon EventBridge.

**Key Features**:
- Event-driven architecture
- Event patterns and rules
- Scheduled events (cron/rate)
- Multiple targets per rule
- Cross-account event routing
- Schema registry

**Event Sources**:
- AWS services (EC2, S3, Lambda, etc.)
- Custom applications
- SaaS partners

**Targets**:
- Lambda functions
- SNS topics
- SQS queues
- Step Functions
- Kinesis streams
- ECS tasks
- SSM Run Command
- Many more

### CloudWatch Agent

**Definition**: Software agent to collect system-level metrics and logs from EC2 and on-premises servers.

**Collects**:
- Memory utilization
- Disk utilization
- Swap usage
- Disk I/O
- Network metrics
- Process metrics
- Custom metrics
- Log files

**Installation**:
- SSM Run Command
- Manual installation
- CloudFormation

### CloudWatch Application Insights

**Definition**: Automated monitoring for .NET and SQL Server applications.

**Key Features**:
- Automatic dashboard creation
- Anomaly detection
- Troubleshooting recommendations
- Application topology view

### CloudWatch ServiceLens

**Definition**: Visualize and analyze health, performance, and availability of applications using distributed tracing.

**Key Features**:
- Service map visualization
- Traces from X-Ray
- Metrics and logs correlation
- End-to-end view

**Use Cases**:
- Infrastructure monitoring
- Application performance monitoring
- Log aggregation and analysis
- Automated responses to operational changes
- Troubleshooting and root cause analysis
- Compliance and auditing

**Exam Tips**:
- CloudWatch for monitoring AWS resources
- Default EC2 metrics: CPU, network, disk ops (NOT memory/disk space)
- Use CloudWatch Agent for memory and disk utilization
- Standard monitoring: 5-minute intervals (default)
- Detailed monitoring: 1-minute intervals (extra cost)
- Alarms can trigger Auto Scaling, SNS, EC2 actions
- Logs retained indefinitely by default (configure retention)
- EventBridge (CloudWatch Events) for event-driven automation
- Metric filters create metrics from logs
- CloudWatch Logs Insights for querying logs
- Cross-region and cross-account dashboards
- Use unified CloudWatch Agent for metrics and logs

---

## AWS CloudFormation

**Definition**: Infrastructure as Code (IaC) service that provides a common language to model and provision AWS and third-party resources in cloud environments.

**Key Features**:
- Declarative templates (JSON or YAML)
- Automated resource provisioning
- Version control for infrastructure
- Rollback on errors
- Change sets preview
- Drift detection
- Stack sets for multi-account/region
- StackSets OU deployment

**Core Concepts**:

### Templates
- JSON or YAML format
- Define resources and configurations
- Parameters for customization
- Mappings for conditional values
- Outputs for cross-stack references
- Conditions for conditional resource creation

**Template Sections**:
```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Template description
Parameters: # Input values
Mappings: # Static variables
Conditions: # Conditional logic
Resources: # Required - AWS resources
Outputs: # Values to export
```

### Stacks
- Collection of AWS resources
- Single unit of deployment
- Managed as a single entity
- Create, update, delete operations

### Stack Sets
- Deploy stacks across multiple accounts and regions
- Centralized management
- Automatic deployment to new accounts
- OU (Organizational Unit) targeting

### Change Sets
- Preview changes before applying
- See what will be added, modified, deleted
- Safety mechanism
- No unintended changes

### Drift Detection
- Detect manual changes outside CloudFormation
- Compare current state to template
- Identify configuration drift
- Maintain infrastructure consistency

**Intrinsic Functions**:
- `Ref`: Reference parameter or resource
- `Fn::GetAtt`: Get attribute from resource
- `Fn::Join`: Join strings
- `Fn::Sub`: Substitute variables
- `Fn::ImportValue`: Import exported values
- `Fn::Select`: Select from list
- `Fn::If`: Conditional values

**Stack Policies**:
- Protect specific resources from updates
- JSON document
- Prevent accidental modifications

**Nested Stacks**:
- Stacks within stacks
- Reusable components
- Simplify complex templates
- Manage related resources

**Cross-Stack References**:
- Export values from one stack
- Import in another stack
- Share resources between stacks
- Loosely coupled architecture

**Helper Scripts** (cfn-init, cfn-signal, cfn-hup):
- Configure EC2 instances
- Signal completion
- Update configurations

**Use Cases**:
- Infrastructure as Code
- Disaster recovery
- Dev/test environment creation
- Multi-region deployments
- Compliance and governance
- Automated resource provisioning

**Exam Tips**:
- CloudFormation for IaC
- Templates define infrastructure
- Stacks manage resources as unit
- Stack Sets for multi-account/region
- Change Sets to preview changes
- Drift Detection for manual changes
- Free service (pay for resources created)
- Rollback on error (default)
- Use Parameters for reusability
- Use Outputs for cross-stack references
- Nested stacks for modular templates
- DeletionPolicy: Retain, Snapshot, Delete
- UpdateReplacePolicy for resource updates

---

## AWS Systems Manager

**Definition**: Unified interface for viewing and controlling AWS infrastructure. Provides operational insights and automates tasks across AWS resources.

**Key Components**:

### Session Manager

**Definition**: Secure, browserless shell access to EC2 instances without opening inbound ports or managing SSH keys.

**Key Features**:
- No bastion hosts needed
- No SSH keys required
- IAM-based access control
- Session logging to S3 or CloudWatch
- Port forwarding
- Compliance with no inbound ports

### Run Command

**Definition**: Remotely execute commands on EC2 instances or on-premises servers.

**Key Features**:
- Execute scripts and commands
- No SSH required
- Command documents (SSM Documents)
- Rate control (concurrency and error threshold)
- Output to S3 or CloudWatch
- Integration with EventBridge

### Patch Manager

**Definition**: Automate patching of operating systems and applications.

**Key Features**:
- Patch baselines
- Maintenance windows
- Compliance reporting
- Scan and install patches
- Support for Linux and Windows

### Parameter Store

**Definition**: Secure, hierarchical storage for configuration data and secrets.

(Covered in detail in Security section)

**Key Features**:
- Configuration management
- Secrets storage
- Hierarchical organization
- Version tracking
- KMS encryption

### Automation

**Definition**: Simplify common operational tasks using predefined or custom runbooks.

**Key Features**:
- Automation documents (runbooks)
- Predefined automations (AWS-managed)
- Custom automations
- Approve/reject steps
- Integration with other services
- Multi-account automation

**Common Use Cases**:
- Create golden AMIs
- Recover impaired instances
- Resize EBS volumes
- Snapshot creation

### State Manager

**Definition**: Maintain consistent configuration across instances.

**Key Features**:
- Define desired state
- Automatic enforcement
- Configuration compliance
- Association with instances

### Inventory

**Definition**: Collect metadata about instances and installed software.

**Key Features**:
- Software inventory
- AWS component inventory
- Custom inventory
- Query with AWS Config
- Compliance dashboards

### Maintenance Windows

**Definition**: Define schedules for running administrative and maintenance tasks.

**Key Features**:
- Scheduled task execution
- Control impact to workloads
- Multiple tasks per window
- Target selection
- Monitoring and logging

### OpsCenter

**Definition**: Central location to view, investigate, and resolve operational work items (OpsItems).

**Key Features**:
- Aggregate operational issues
- CloudWatch alarms, Config rules
- Manual OpsItem creation
- Runbook automation
- Integration with EventBridge

### Compliance

**Definition**: Scan managed instances for patch compliance and configuration inconsistencies.

**Key Features**:
- Patch compliance
- Association compliance
- Custom compliance
- Dashboards and reports

**Systems Manager Agent (SSM Agent)**:
- Pre-installed on Amazon Linux, Ubuntu, Windows
- Enables Systems Manager capabilities
- Required for managed instances
- Communicates with Systems Manager service

**Use Cases**:
- Centralized operational management
- Automated patching
- Secure remote access
- Configuration management
- Compliance reporting
- Operational automation

**Exam Tips**:
- Session Manager for secure shell access without SSH/RDP
- Run Command for remote command execution
- Patch Manager for automated patching
- Parameter Store for configuration and secrets
- Automation for operational runbooks
- State Manager for configuration consistency
- SSM Agent required on instances
- No inbound ports needed (agent connects outbound)
- IAM roles for instance access (not SSH keys)
- Free service (except Parameter Store advanced)

---

## AWS Trusted Advisor

**Definition**: Online tool providing real-time guidance to help provision resources following AWS best practices.

**Check Categories**:

### Cost Optimization
- Idle resources (RDS, EC2, EBS)
- Underutilized resources
- Reserved Instance recommendations
- Savings Plans recommendations
- Unassociated Elastic IPs

### Performance
- High utilization instances
- CloudFront optimizations
- Route 53 alias records
- EBS throughput optimization
- Service limits

### Security
- S3 bucket permissions
- Security group rules
- IAM use
- MFA on root account
- EBS and RDS public snapshots
- RDS public accessibility

### Fault Tolerance
- EBS snapshots
- RDS Multi-AZ
- RDS backups
- Auto Scaling group configuration
- VPC subnet distribution
- Route 53 health checks

### Service Limits
- Monitor usage against service limits
- Prevent service disruptions
- Plan for limit increases

**Support Tiers**:

| Feature | Basic/Developer | Business/Enterprise |
|---------|----------------|---------------------|
| **Core Checks** | 7 checks | All checks |
| **Cost Optimization** | Limited | Full |
| **Performance** | Limited | Full |
| **Security** | Limited | Full |
| **Fault Tolerance** | Limited | Full |
| **Service Limits** | Limited | Full |
| **API Access** | No | Yes |
| **CloudWatch Integration** | No | Yes |

**Business & Enterprise Features**:
- All checks unlocked
- Weekly notification emails
- AWS Support API access
- CloudWatch integration
- Programmatic access
- Organizational view

**Refresh**:
- Manual refresh (5-minute cooldown)
- Automatic refresh: Weekly
- API for programmatic refresh

**Use Cases**:
- Cost reduction
- Improve system performance
- Close security gaps
- Increase fault tolerance
- Monitor service limits

**Exam Tips**:
- Trusted Advisor for best practices guidance
- 5 categories: Cost, Performance, Security, Fault Tolerance, Service Limits
- Business/Enterprise Support for all checks
- Basic/Developer gets 7 core checks
- Refresh checks manually or wait for weekly update
- API access requires Business/Enterprise Support
- CloudWatch integration for alerts
- Organizational view for multi-account
- Free for 7 core checks, paid support for full access

---

## AWS Service Catalog

**Definition**: Allows organizations to create and manage catalogs of IT services approved for use on AWS. Self-service portal for end users.

**Key Features**:
- Standardized products
- Self-service provisioning
- Version control
- Governance and compliance
- Cost management
- Access control
- Launch constraints

**Components**:

### Products
- CloudFormation templates
- Versioned
- Define AWS resources
- Can include constraints

### Portfolios
- Collection of products
- Access control unit
- Share across accounts
- IAM-based permissions

### Constraints
- **Launch Constraints**: IAM role for provisioning
- **Notification Constraints**: SNS notifications
- **Template Constraints**: Limit parameter values
- **Stack Set Constraints**: Deploy across accounts/regions

### Provisioned Products
- Instantiated products
- User-launched resources
- Managed by Service Catalog
- Update and terminate capabilities

**TagOptions**:
- Key-value pairs
- Enforce tagging standards
- Inherited by provisioned products
- Simplify cost tracking

**Use Cases**:
- IT governance
- Compliance enforcement
- Self-service provisioning
- Standardized deployments
- Cost control
- Multi-account management

**Exam Tips**:
- Service Catalog for standardized, self-service IT services
- Products based on CloudFormation templates
- Portfolios group products and control access
- Launch constraints specify IAM role for provisioning
- TagOptions enforce tagging standards
- Version control for products
- Share portfolios across accounts
- End users don't need CloudFormation knowledge

---

## AWS Control Tower

**Definition**: Simplifies setup and governance of secure, compliant multi-account AWS environment based on AWS best practices.

**Key Features**:
- Automated account setup
- Guardrails (preventive and detective)
- Account Factory
- Centralized logging and monitoring
- Dashboard for compliance
- Landing Zone

**Landing Zone**:
- Well-architected multi-account environment
- Organizational Units (OUs)
- Shared accounts (Log Archive, Audit)
- Baseline configurations
- Network configuration
- Identity management

**Guardrails**:

### Mandatory Guardrails
- Automatically enabled
- Cannot disable
- Critical security controls
- Example: Disallow public write access to S3

### Strongly Recommended Guardrails
- Based on best practices
- Optional but recommended
- Example: Enable MFA for root user

### Elective Guardrails
- Optional
- Based on specific requirements
- Example: Disallow specific instance types

**Guardrail Types**:
- **Preventive**: SCPs (Service Control Policies)
- **Detective**: AWS Config rules

**Account Factory**:
- Automated account provisioning
- Baseline configurations
- Network setup
- Guardrails application
- Self-service for end users
- Integration with Service Catalog

**Dashboard**:
- Organizational view
- Compliance status
- Guardrail violations
- Account inventory
- Drift detection

**Accounts**:
- **Management Account**: Control Tower operations
- **Log Archive Account**: Centralized logging
- **Audit Account**: Cross-account auditing
- **Member Accounts**: Application workloads

**Use Cases**:
- Multi-account setup
- Governance at scale
- Compliance enforcement
- Automated account provisioning
- Baseline security controls
- New AWS environment setup

**Exam Tips**:
- Control Tower for multi-account governance
- Landing Zone: pre-configured multi-account environment
- Guardrails: preventive (SCPs) and detective (Config)
- Account Factory for automated account creation
- Mandatory, strongly recommended, elective guardrails
- Log Archive and Audit accounts
- Built on Organizations, Config, CloudTrail
- Dashboard for compliance monitoring
- Use for new AWS implementations
- Drift detection for changes outside Control Tower

---

## AWS License Manager

**Definition**: Manages software licenses from vendors like Microsoft, SAP, Oracle in AWS and on-premises environments.

**Key Features**:
- License tracking
- Usage limits
- Compliance rules
- License reporting
- Integration with EC2, RDS
- Automated discovery
- License conversion (BYOL to License Included)

**License Types**:
- Bring Your Own License (BYOL)
- License Included
- Core-based
- Instance-based
- Socket-based

**License Rules**:
- Define terms and conditions
- Limit usage
- Enforce compliance
- Prevent overages

**Use Cases**:
- Software license management
- Compliance tracking
- Cost optimization
- License reporting
- BYOL to AWS

**Exam Tips**:
- License Manager for software license tracking
- Manages licenses in AWS and on-premises
- Prevents license violations
- Supports various licensing models
- Integration with Systems Manager
- Automated license discovery

---

## AWS Health Dashboard

**Definition**: Provides alerts and guidance when AWS is experiencing events that may impact you.

**Types**:

### AWS Health Dashboard (Service Health)
- Overall AWS service health
- Public view
- All AWS services
- All regions
- Historical information

### AWS Health Dashboard (Your Account)
- Personalized view
- Events affecting your resources
- Proactive notifications
- Upcoming maintenance
- Remediation guidance
- Integration with EventBridge

**Event Categories**:
- **Issue**: Service disruption
- **Scheduled Change**: Planned maintenance
- **Account Notification**: Billing, security
- **Investigation**: AWS investigating issue

**Health API**:
- Programmatic access
- Automation of responses
- Integration with monitoring tools
- Requires Business or Enterprise Support

**EventBridge Integration**:
- Automated responses to health events
- Lambda functions for remediation
- SNS notifications
- Custom workflows

**Use Cases**:
- Proactive issue resolution
- Maintenance planning
- Operational automation
- Impact assessment
- Compliance and auditing

**Exam Tips**:
- Health Dashboard shows service health
- Personalized view for your account
- Proactive notifications about issues
- EventBridge integration for automation
- Health API requires Business/Enterprise Support
- Use for operational awareness
- Different from Trusted Advisor (best practices)

---

## AWS Compute Optimizer

**Definition**: Recommends optimal AWS compute resources for workloads to reduce costs and improve performance using machine learning.

**Supported Resources**:
- EC2 instances
- Auto Scaling groups
- EBS volumes
- Lambda functions
- ECS services on Fargate

**Key Features**:
- ML-based recommendations
- Utilization metrics analysis
- Cost and performance projections
- Historical analysis (14 days default, up to 3 months)
- Export recommendations
- Multi-account support

**Recommendation Types**:
- **Overprovisioned**: Can downsize
- **Underprovisioned**: Should upsize
- **Optimized**: Current is optimal
- **None**: Insufficient data

**Metrics Analyzed**:
- CPU utilization
- Memory utilization (requires CloudWatch Agent)
- Network throughput
- Disk I/O
- Lambda invocations and duration

**Preference Settings**:
- Enhanced infrastructure metrics
- External metrics (Datadog, Dynatrace)
- Inferred workload types
- Look-back period customization

**Use Cases**:
- Cost optimization
- Performance improvement
- Rightsizing instances
- Resource selection
- Capacity planning

**Exam Tips**:
- Compute Optimizer for rightsizing recommendations
- Uses ML to analyze utilization
- Recommends optimal instance types
- Supports EC2, Auto Scaling, EBS, Lambda, ECS Fargate
- Requires CloudWatch Agent for memory metrics
- 14-day analysis by default
- Free service
- Export recommendations for offline analysis
- Different from Trusted Advisor (broader scope)

---

## AWS Cost Explorer

**Definition**: Visualize, understand, and manage AWS costs and usage over time.

**Key Features**:
- Cost and usage reports
- Historical data (up to 12 months)
- Forecasting (up to 12 months)
- Filtering and grouping
- Savings Plans and RI recommendations
- Cost allocation tags
- API access

**Views**:
- **Daily**: Day-by-day costs
- **Monthly**: Monthly aggregates
- **Hourly/Resource**: Resource-level granularity

**Dimensions**:
- Service
- Linked Account
- Region
- Instance Type
- Purchase Option
- Tags

**Grouping**:
- By service, account, tag, etc.
- Multiple dimensions
- Custom reports
- Saved reports

**Forecasting**:
- Predict future costs
- Based on historical usage
- Up to 12 months ahead
- Confidence intervals

**Recommendations**:
- Reserved Instance recommendations
- Savings Plans recommendations
- Rightsizing recommendations
- Based on usage patterns

**Cost Allocation Tags**:
- AWS-generated tags
- User-defined tags
- Track costs by project, department, etc.
- Must activate tags

**Anomaly Detection**:
- ML-based unusual spending detection
- Alerts via SNS
- Root cause analysis
- Monitor by service, account, tag

**Use Cases**:
- Cost analysis
- Budget planning
- Cost optimization
- Chargeback/showback
- Forecasting
- Anomaly detection

**Exam Tips**:
- Cost Explorer for visualizing and analyzing costs
- Historical data up to 12 months
- Forecast up to 12 months
- Filter by service, account, region, tags
- RI and Savings Plans recommendations
- Cost allocation tags for tracking
- Anomaly detection for unusual spending
- Free basic features, paid for API and hourly data
- Use with AWS Budgets for proactive cost management

---

## AWS Budgets

**Definition**: Set custom cost and usage budgets and receive alerts when exceeding thresholds.

**Budget Types**:

### Cost Budgets
- Track AWS spending
- Actual vs budgeted costs
- Forecasted costs

### Usage Budgets
- Track usage of AWS services
- Specific units (GB, hours, requests)

### Reservation Budgets
- Track RI and Savings Plans utilization
- Coverage metrics

### Savings Plans Budgets
- Track Savings Plans utilization
- Coverage metrics

**Key Features**:
- Custom budgets
- Alert thresholds
- SNS notifications
- Email notifications
- Budget actions (automated responses)
- Forecasted alerts
- Multiple budgets per account

**Time Periods**:
- Daily
- Monthly
- Quarterly
- Annually
- Custom

**Alert Thresholds**:
- Actual costs/usage
- Forecasted costs/usage
- Multiple thresholds per budget
- Percentage or absolute value

**Budget Actions**:
- IAM policy application
- Service Control Policy application
- Target EC2 or RDS instances
- Automated cost control

**Integration**:
- SNS for notifications
- ChatBot for Slack/Chime
- EventBridge for automation
- Cost Explorer for visualization

**Use Cases**:
- Cost control
- Spending alerts
- Department budgets
- Project cost tracking
- Prevent overspending
- RI and Savings Plans monitoring

**Exam Tips**:
- AWS Budgets for cost and usage alerts
- 4 budget types: Cost, Usage, Reservation, Savings Plans
- Alert on actual or forecasted spending
- Up to 5 SNS notifications per budget
- Budget actions for automated responses
- First 2 budgets free, then $0.02/day per budget
- Can set multiple thresholds
- Use with Cost Explorer for visualization
- SNS integration for notifications

---

## AWS Cost and Usage Report (CUR)

**Definition**: Most comprehensive set of cost and usage data available, containing detailed information about AWS costs and usage.

**Key Features**:
- Most detailed billing report
- Hourly, daily, or monthly granularity
- Line-item details
- Resource IDs and tags
- Reserved Instance details
- Savings Plans information
- Support charges
- Credits and refunds

**Delivery**:
- S3 bucket
- Gzip or Parquet format
- Athena, Redshift, QuickSight integration
- Daily updates
- Version control

**Data Included**:
- Service usage
- Pricing
- Cost allocation tags
- Reserved Instances
- Savings Plans
- Resource attributes
- Account information

**Integration**:
- **Athena**: Query CUR data with SQL
- **QuickSight**: Visualize CUR data
- **Redshift**: Load for analysis
- **Third-party tools**: Import for analysis

**Use Cases**:
- Detailed cost analysis
- Chargeback/showback
- Cost allocation
- Custom reporting
- RI and Savings Plans analysis
- Compliance reporting

**Exam Tips**:
- CUR for most detailed cost and usage data
- Delivered to S3
- Hourly granularity available
- Include resource IDs and tags
- Query with Athena
- Visualize with QuickSight
- More detailed than Cost Explorer
- Free to generate (pay for S3 storage)
- Use for detailed custom analysis

---

## AWS Resource Groups

**Definition**: Organize AWS resources for management and automation. Use tags to group resources.

**Key Features**:
- Tag-based grouping
- Query-based grouping
- Resource type filtering
- Cross-service groups
- Save groups for reuse
- Actions on group

**Group Types**:

### Tag-Based Groups
- Resources with specific tags
- AND/OR logic
- Multiple tag filters

### CloudFormation Stack-Based Groups
- All resources in stack
- Automatic grouping
- Stack updates reflected

**Actions on Groups**:
- View configurations
- CloudWatch monitoring
- Systems Manager actions
- Automation
- Bulk operations

**Tag Editor**:
- Manage tags
- Find resources without tags
- Bulk tag operations
- Search across regions

**Use Cases**:
- Organize resources
- Bulk operations
- Environment grouping (dev, test, prod)
- Application grouping
- Cost allocation
- Automation

**Exam Tips**:
- Resource Groups for organizing resources
- Tag-based or CloudFormation-based
- Perform actions on groups
- Integration with Systems Manager
- Tag Editor for tag management
- Free service
- Use tags for organization
- Cross-region groups

---

## AWS Tag Editor

**Definition**: Find and manage tags for resources across regions.

**Key Features**:
- Search for resources
- View tags
- Edit tags in bulk
- Find untagged resources
- Export tag data
- Multi-region search

**Operations**:
- Add tags
- Remove tags
- Edit tag values
- Find missing tags
- Tag compliance

**Use Cases**:
- Tag management at scale
- Find untagged resources
- Enforce tagging policies
- Cost allocation setup
- Resource organization

**Exam Tips**:
- Tag Editor for bulk tag management
- Search across regions and services
- Part of Resource Groups console
- Find resources without tags
- Enforce tagging standards
- Use for tagging governance

---

## Management Services Summary Table

| Service | Purpose | Key Feature |
|---------|---------|-------------|
| **CloudWatch** | Monitoring | Metrics, logs, alarms |
| **CloudFormation** | IaC | Automate infrastructure |
| **Systems Manager** | Operations | Unified management console |
| **Trusted Advisor** | Best Practices | Recommendations |
| **Service Catalog** | IT Governance | Self-service portal |
| **Control Tower** | Multi-Account | Landing zone setup |
| **License Manager** | License Tracking | Software licenses |
| **Health Dashboard** | Service Health | Personalized alerts |
| **Compute Optimizer** | Rightsizing | ML recommendations |
| **Cost Explorer** | Cost Analysis | Visualize spending |
| **Budgets** | Cost Control | Spending alerts |
| **CUR** | Detailed Billing | Comprehensive cost data |
| **Resource Groups** | Organization | Tag-based grouping |
| **Tag Editor** | Tag Management | Bulk tag operations |

---

## Exam Focus Areas

1. **CloudWatch**: Metrics, logs, alarms, agent for custom metrics
2. **CloudFormation**: IaC, stacks, templates, drift detection
3. **Systems Manager**: Session Manager, Run Command, Parameter Store, Patch Manager
4. **Trusted Advisor**: Categories, support tier access
5. **Service Catalog**: Standardized IT services, portfolios, products
6. **Control Tower**: Landing zone, guardrails, multi-account
7. **CloudWatch vs Trusted Advisor vs Compute Optimizer**: Different purposes
8. **Cost Management**: Cost Explorer vs Budgets vs CUR
9. **Tagging**: Resource Groups, Tag Editor, cost allocation
10. **Automation**: EventBridge, Systems Manager Automation, CloudFormation

---

*Last Updated: January 2026 - SAA-C03 Exam*
