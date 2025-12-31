# AWS SAA-C03: Management, Monitoring & Governance Cheat Sheet

## 📋 Table of Contents
- [CloudWatch](#cloudwatch)
- [CloudFormation](#cloudformation)
- [Systems Manager](#systems-manager)
- [Trusted Advisor](#trusted-advisor)
- [Service Catalog](#service-catalog)
- [Control Tower](#control-tower)
- [AWS Well-Architected Tool](#well-architected-tool)
- [Personal Health Dashboard](#personal-health-dashboard)
- [Cost Management](#cost-management)

---

## CloudWatch

### Definition
Monitoring and observability service for AWS resources and applications.

### Key Features
- **Metrics**: Time-ordered data points
  - **Standard**: 5-minute intervals (free)
  - **Detailed**: 1-minute intervals (paid)
  - **Custom Metrics**: Application-defined metrics
  - **High-Resolution**: 1-second granularity
  - **Retention**: 15 months
- **Alarms**: Trigger actions based on metric thresholds
  - **States**: OK, ALARM, INSUFFICIENT_DATA
  - **Actions**: SNS, Auto Scaling, EC2 actions, Systems Manager
  - **Composite Alarms**: Combine multiple alarms
- **Logs**: Collect, monitor, and analyze log files
  - **Log Groups**: Container for log streams
  - **Log Streams**: Sequence of log events
  - **Retention**: Configurable (never expire to 10 years)
  - **Insights**: Query and analyze logs (SQL-like)
  - **Subscriptions**: Stream to Lambda, Kinesis, Elasticsearch
  - **Metric Filters**: Extract metrics from logs
- **Events/EventBridge**: Event-driven automation
  - **Rules**: Match event patterns
  - **Targets**: Lambda, SNS, SQS, Step Functions, etc.
  - **Scheduled**: Cron or rate expressions
- **Dashboards**: Customizable visualizations
- **Container Insights**: ECS, EKS, Kubernetes monitoring
- **Lambda Insights**: Lambda function monitoring
- **Contributor Insights**: Top-N analysis
- **Application Insights**: Automated application monitoring
- **Synthetics**: Create canaries to monitor endpoints
- **RUM (Real User Monitoring)**: Frontend performance
- **Evidently**: A/B testing and feature flags
- **CloudWatch Agent**: Collect metrics from EC2 and on-premises

### Exam Tips & Heuristics
✅ **When to Use CloudWatch:**
- Monitor AWS resources
- Application monitoring
- Log aggregation and analysis
- Automated responses to events
- Performance optimization
- Troubleshooting

⚠️ **Common Pitfalls:**
- Default metrics don't include memory/disk (need CloudWatch Agent)
- Detailed monitoring costs extra
- Log retention default is "never expire" (costs can grow)
- EventBridge is the new name for CloudWatch Events
- Cross-region dashboards available but cross-account needs setup

🎯 **Exam Heuristics:**
- "Monitoring" = **CloudWatch**
- "Application logs" = **CloudWatch Logs**
- "Alert on metric" = **CloudWatch Alarm**
- "Automated response to event" = **EventBridge** (CloudWatch Events)
- "Memory/disk usage on EC2" = **CloudWatch Agent**
- "Query logs" = **CloudWatch Logs Insights**
- "Scheduled tasks" = **EventBridge** (cron)
- "Monitor API calls" = **CloudTrail** (to CloudWatch Logs)
- "Real-time log processing" = **CloudWatch Logs** + **Lambda** subscription
- "Dashboards" = **CloudWatch Dashboards**
- "Container monitoring" = **Container Insights**
- "Synthetic monitoring" = **CloudWatch Synthetics**

### CloudWatch vs CloudTrail
| Feature | CloudWatch | CloudTrail |
|---------|-----------|-----------|
| Purpose | Performance monitoring | API call auditing |
| Data | Metrics, logs, events | API call logs |
| Use Case | Operational health | Security, compliance |
| Real-time | Yes (near real-time) | 15-minute delay |

---

## CloudFormation

### Definition
Infrastructure as Code (IaC) service to provision and manage AWS resources using templates.

### Key Features
- **Templates**: JSON or YAML files defining resources
  - **Parameters**: User inputs
  - **Resources**: AWS resources to create (required)
  - **Mappings**: Static variables
  - **Conditions**: Control resource creation
  - **Outputs**: Export values
  - **Metadata**: Additional info
  - **Transform**: Include code snippets (SAM, macros)
- **Stacks**: Collection of resources managed as single unit
  - **Create, Update, Delete**: Lifecycle management
  - **Change Sets**: Preview changes before execution
  - **Stack Sets**: Deploy stacks across accounts/regions
  - **Nested Stacks**: Reusable templates
  - **Cross-Stack References**: Export/import values
- **Drift Detection**: Identify manual changes
- **StackSets**: Multi-account, multi-region deployments
- **Designer**: Visual template creator
- **Registry**: Share and discover resource types
- **Rollback**: Automatic on failure
- **Intrinsic Functions**: Fn::GetAtt, Ref, Fn::Join, Fn::Sub, etc.

### Exam Tips & Heuristics
✅ **When to Use CloudFormation:**
- Infrastructure as Code
- Repeatable deployments
- Version control infrastructure
- Disaster recovery
- Multi-environment management
- Compliance and governance

⚠️ **Common Pitfalls:**
- Stack deletion deletes all resources (unless DeletionPolicy set)
- Circular dependencies will fail
- Some resource properties are immutable (require replacement)
- Stack creation can fail partially (automatic rollback)
- Manual changes cause drift

🎯 **Exam Heuristics:**
- "Infrastructure as Code" = **CloudFormation**
- "Automate resource provisioning" = **CloudFormation**
- "Repeatable infrastructure" = **CloudFormation**
- "Deploy across accounts" = **StackSets**
- "Detect manual changes" = **Drift Detection**
- "Reusable templates" = **Nested Stacks**
- "Preview changes" = **Change Sets**
- "Resource fails to delete" = Check **DeletionPolicy** or dependencies
- "Serverless apps" = **SAM** (built on CloudFormation)

### CloudFormation vs Terraform
| Feature | CloudFormation | Terraform |
|---------|---------------|-----------|
| Provider | AWS-native | Multi-cloud |
| Cost | Free | Free (enterprise paid) |
| State | Managed by AWS | State file management |
| Language | JSON/YAML | HCL |
| AWS Integration | Excellent | Good |

---

## Systems Manager

### Definition
Unified interface to view operational data and automate tasks across AWS resources.

### Key Features
- **Session Manager**: Secure shell access without SSH keys/bastion
- **Run Command**: Execute commands on multiple instances
- **Patch Manager**: Automate OS patching
  - **Patch Baselines**: Rules for patch approval
  - **Maintenance Windows**: Schedule patching
  - **Patch Groups**: Organize instances
- **State Manager**: Maintain consistent configuration
- **Automation**: Automate operational tasks (runbooks)
- **Parameter Store**: Secure configuration and secrets storage
- **Inventory**: Collect metadata from managed instances
- **Compliance**: View patch and configuration compliance
- **OpsCenter**: Aggregate operational work items
- **Explorer**: Customizable dashboard
- **Documents (SSM Documents)**: Define actions to perform
- **Distributor**: Package distribution
- **Fleet Manager**: Node management interface
- **Change Manager**: Change request workflow
- **Application Manager**: Application-centric operations
- **Hybrid Activations**: Manage on-premises servers

### Exam Tips & Heuristics
✅ **When to Use Systems Manager:**
- Manage EC2 at scale
- Secure access without SSH keys
- Automated patching
- Configuration management
- Hybrid environment management
- Operational insights

⚠️ **Common Pitfalls:**
- Requires SSM Agent on instances (pre-installed on many AMIs)
- IAM role required on instances
- Session Manager requires internet/VPC endpoints
- Not all features work on-premises

🎯 **Exam Heuristics:**
- "Secure shell without SSH keys" = **Session Manager**
- "Execute command on multiple EC2" = **Run Command**
- "Automate patching" = **Patch Manager**
- "Configuration as code" = **State Manager**
- "Operational automation" = **Automation**
- "Store configuration" = **Parameter Store**
- "Manage on-premises servers" = **Hybrid Activations**
- "No bastion host" = **Session Manager**
- "Compliance reporting" = **Systems Manager Compliance**

---

## Trusted Advisor

### Definition
Real-time guidance to provision resources following AWS best practices.

### Key Features
- **Categories**:
  - **Cost Optimization**: Unused resources, cost savings
  - **Performance**: Service limits, throughput optimization
  - **Security**: Security gaps, IAM best practices
  - **Fault Tolerance**: Backups, redundancy
  - **Service Limits**: Usage approaching limits
- **Support Plans**:
  - **Basic/Developer**: 7 core checks (free)
  - **Business/Enterprise**: All checks + API access
- **Notifications**: CloudWatch Events for status changes
- **Priority**: High-priority recommendations
- **Action Links**: Direct remediation actions
- **Refresh**: Manual (every 5 minutes) or automatic (weekly)

### Exam Tips & Heuristics
✅ **When to Use Trusted Advisor:**
- Optimize costs
- Improve security
- Increase performance
- Check service limits
- Best practice validation

⚠️ **Common Pitfalls:**
- Full features require Business/Enterprise support
- Not all checks are automated remediation
- Weekly refresh for most checks
- Some recommendations need manual action

🎯 **Exam Heuristics:**
- "Cost optimization recommendations" = **Trusted Advisor**
- "Security best practices check" = **Trusted Advisor**
- "Service limits monitoring" = **Trusted Advisor**
- "Idle resources" = **Trusted Advisor**
- "MFA on root account" = **Trusted Advisor** check
- "Underutilized instances" = **Trusted Advisor**
- "API access to recommendations" = **Trusted Advisor** (Business+)

---

## Service Catalog

### Definition
Create and manage catalogs of IT services approved for use on AWS.

### Key Features
- **Products**: CloudFormation templates for services
- **Portfolios**: Collections of products
- **Constraints**: Rules on how products can be launched
  - **Launch**: Who can launch
  - **Notification**: SNS on events
  - **Template**: Limit parameter values
- **Versioning**: Multiple product versions
- **TagOptions**: Standardized tagging
- **Self-Service**: End users launch approved products
- **Governance**: Centralized control
- **Multi-Account**: Share portfolios across accounts

### Exam Tips & Heuristics
✅ **When to Use Service Catalog:**
- Standardize deployments
- Governance and compliance
- Self-service provisioning
- Cost control
- Approved configurations only

⚠️ **Common Pitfalls:**
- Requires CloudFormation knowledge
- Setup overhead for small organizations
- Users need IAM permissions to Service Catalog

🎯 **Exam Heuristics:**
- "Self-service AWS resources" = **Service Catalog**
- "Approved configurations only" = **Service Catalog**
- "Standardize deployments" = **Service Catalog**
- "Governance for end users" = **Service Catalog**
- "Controlled resource provisioning" = **Service Catalog**

---

## Control Tower

### Definition
Set up and govern secure, multi-account AWS environment based on best practices.

### Key Features
- **Landing Zone**: Well-architected multi-account environment
- **Account Factory**: Automated account provisioning
- **Guardrails**: Pre-configured governance rules
  - **Mandatory**: Always enforced
  - **Strongly Recommended**: Best practices
  - **Elective**: Optional controls
  - **Preventive**: SCPs (prevent actions)
  - **Detective**: Config rules (detect violations)
- **Dashboard**: Compliance status overview
- **Organizations Integration**: Built on AWS Organizations
- **Customizations**: Terraform or CloudFormation
- **Account Baseline**: Consistent configuration across accounts

### Exam Tips & Heuristics
✅ **When to Use Control Tower:**
- New multi-account setup
- Governance at scale
- Best practice enforcement
- Centralized account management
- Quick setup for AWS environment

⚠️ **Common Pitfalls:**
- Best for new environments (migration complex)
- Uses Organizations and other services (can conflict)
- Cannot easily disable once enabled
- Limited customization of guardrails

🎯 **Exam Heuristics:**
- "Multi-account governance" = **Control Tower**
- "Landing Zone" = **Control Tower**
- "Automated account creation" = **Control Tower** Account Factory
- "Enforce best practices" = **Control Tower** Guardrails
- "Quick multi-account setup" = **Control Tower**
- "Preventive controls" = **Control Tower** guardrails (SCPs)
- "Detective controls" = **Control Tower** guardrails (Config)

---

## AWS Well-Architected Tool

### Definition
Review workloads against AWS best practices across 6 pillars.

### Key Features
- **Six Pillars**:
  - **Operational Excellence**: Run and monitor systems
  - **Security**: Protect information and systems
  - **Reliability**: Recover from failures
  - **Performance Efficiency**: Use resources efficiently
  - **Cost Optimization**: Minimize costs
  - **Sustainability**: Minimize environmental impact
- **Workload**: Collection of resources
- **Milestones**: Point-in-time snapshots
- **Review**: Question-based assessment
- **Recommendations**: Improvement suggestions
- **Lenses**: Custom or AWS-provided (e.g., Serverless, SaaS)

### Exam Tips & Heuristics
✅ **When to Use Well-Architected Tool:**
- Architecture review
- Best practice validation
- Continuous improvement
- Risk identification
- Pre-deployment validation

⚠️ **Common Pitfalls:**
- Self-assessment (not automatic)
- Requires architecture knowledge
- Recommendations need implementation

🎯 **Exam Heuristics:**
- "Architecture review" = **Well-Architected Tool**
- "Best practices check" = **Well-Architected Tool**
- "6 pillars" = **Well-Architected Framework**
- "Continuous improvement" = **Well-Architected Tool**
- "Risk assessment" = **Well-Architected Tool**

---

## Personal Health Dashboard

### Definition
Personalized view of AWS service health affecting your resources.

### Key Features
- **Account-Specific**: Your resources only
- **Event Types**: Issue, maintenance, notification
- **Notifications**: Email, CloudWatch Events
- **Proactive**: Advance notice of maintenance
- **Remediation Guidance**: Specific to your resources
- **Event History**: 90 days
- **Organizational View**: Aggregate across accounts (Enterprise support)
- **Automation**: Trigger Lambda via EventBridge

### Exam Tips & Heuristics
✅ **When to Use Personal Health Dashboard:**
- Monitor service health
- Planned maintenance alerts
- Automated remediation
- Service issue awareness

⚠️ **Common Pitfalls:**
- Different from Service Health Dashboard (all customers)
- Organization view requires Enterprise support
- 90-day history limit

🎯 **Exam Heuristics:**
- "AWS service affecting my resources" = **Personal Health Dashboard**
- "Automate response to AWS events" = **Personal Health Dashboard** + **EventBridge**
- "Planned maintenance notification" = **Personal Health Dashboard**
- "Service health across organization" = **Organizational view**

---

## Cost Management

### Definition
Suite of tools to understand, analyze, and optimize AWS costs.

### Key Features

#### Cost Explorer
- **Visualize Costs**: Interactive graphs
- **Forecasting**: Predict future costs (3-12 months)
- **Filters**: Service, tag, region, account, etc.
- **Reports**: Pre-built and custom
- **Recommendations**: Reserved Instance and Savings Plans
- **Granularity**: Monthly or daily

#### AWS Budgets
- **Budget Types**: Cost, usage, RI utilization, Savings Plans
- **Alerts**: Email, SNS when threshold exceeded
- **Actions**: Automated responses (SCPs, IAM)
- **Filters**: Service, tag, region, etc.
- **Forecasts**: Alert on projected spend

#### Cost and Usage Reports (CUR)
- **Detailed Data**: Comprehensive cost and usage
- **Granularity**: Hourly, daily, monthly
- **Storage**: S3
- **Integration**: Athena, QuickSight, Redshift
- **Use Case**: Detailed analysis, chargeback

#### Savings Plans
- **Flexible Pricing**: Commit to usage ($/hour)
- **Types**: Compute, EC2, SageMaker
- **Discount**: Up to 72% vs On-Demand
- **Flexibility**: Instance family, region, OS

#### Reserved Instances (RIs)
- **Commitment**: 1 or 3 years
- **Discount**: Up to 75% vs On-Demand
- **Types**: Standard (highest discount), Convertible (flexibility)
- **Payment**: All Upfront, Partial, No Upfront
- **Marketplace**: Buy/sell unused RIs

#### Compute Savings Plans
- **Flexibility**: EC2, Fargate, Lambda
- **Discount**: Up to 66%
- **Regional or Family**: More flexible than RIs

### Exam Tips & Heuristics
✅ **When to Use Cost Management:**
- Understand spending
- Forecast costs
- Set budgets and alerts
- Optimize resource usage
- Chargeback and showback

⚠️ **Common Pitfalls:**
- Cost Explorer has slight delay (not real-time)
- Budgets limited to 20,000 per account
- CUR requires S3 bucket setup
- Savings Plans vs RIs - different flexibility

🎯 **Exam Heuristics:**
- "Visualize costs" = **Cost Explorer**
- "Alert on cost threshold" = **Budgets**
- "Detailed cost analysis" = **Cost and Usage Reports**
- "Commit to usage" = **Savings Plans** or **Reserved Instances**
- "Forecast future costs" = **Cost Explorer** forecasting
- "Chargeback by team" = **Cost Allocation Tags** + **CUR**
- "Optimize costs automatically" = **Cost Explorer** recommendations
- "Most flexible commitment" = **Compute Savings Plans**
- "Specific instance commitment" = **Reserved Instances**
- "Cross-service commitment" = **Compute Savings Plans**

### Savings Plans vs Reserved Instances
| Feature | Savings Plans | Reserved Instances |
|---------|--------------|-------------------|
| Commitment | Usage ($/hour) | Capacity (instance type) |
| Flexibility | High (family, region, OS) | Lower (specific instance) |
| Services | EC2, Fargate, Lambda | EC2 only |
| Discount | Up to 66-72% | Up to 75% |
| Convertible | Yes | Yes (Convertible RI) |

---

## 🎓 Management Service Selection Guide

| Requirement | Service |
|------------|---------|
| Resource monitoring | CloudWatch |
| Infrastructure as Code | CloudFormation |
| EC2 fleet management | Systems Manager |
| Cost optimization | Trusted Advisor |
| Self-service provisioning | Service Catalog |
| Multi-account governance | Control Tower |
| Architecture review | Well-Architected Tool |
| Service health alerts | Personal Health Dashboard |
| Cost analysis | Cost Explorer |
| Budget alerts | AWS Budgets |

---

## 💡 Key Exam Patterns

### Monitoring & Alerting
- CloudWatch Metrics + Alarms for thresholds
- CloudWatch Logs for aggregation
- EventBridge for event-driven automation
- CloudWatch Agent for custom metrics
- CloudWatch Synthetics for endpoint monitoring

### Automation Patterns
- EventBridge + Lambda for event response
- Systems Manager Automation for operational tasks
- CloudFormation for infrastructure automation
- Config for auto-remediation
- Step Functions for orchestration

### Cost Optimization Patterns
- Trusted Advisor for recommendations
- Cost Explorer for analysis
- Budgets for alerts
- Savings Plans/RIs for committed usage
- Cost allocation tags for chargeback

### Governance & Compliance
- Organizations for account management
- Control Tower for landing zones
- Service Catalog for approved services
- Config for compliance monitoring
- CloudFormation for standardization

### Operational Excellence
- Systems Manager for fleet management
- CloudWatch for observability
- Personal Health Dashboard for proactive alerts
- Well-Architected reviews for continuous improvement

