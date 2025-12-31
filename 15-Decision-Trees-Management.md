# AWS SAA-C03: Management, Monitoring & Cost Optimization Decision Trees

## 🌳 Quick Navigation
- [Monitoring & Observability](#monitoring--observability)
- [Infrastructure as Code](#infrastructure-as-code)
- [Automation & Operations](#automation--operations)
- [Cost Optimization](#cost-optimization)
- [Governance & Compliance](#governance--compliance)

---

## Monitoring & Observability

```
START: Need monitoring?
│
├─→ Collect metrics and logs?
│   └─→ **Amazon CloudWatch**
│       ├─→ Metrics:
│       │   ├─→ Default metrics (CPU, network, disk, etc.)
│       │   ├─→ Custom metrics (application-level)
│       │   ├─→ 1-second resolution (detailed monitoring)
│       │   └─→ Retention: 15 months
│       │
│       ├─→ Logs:
│       │   ├─→ CloudWatch Logs (centralized logging)
│       │   ├─→ Log Groups and Log Streams
│       │   ├─→ Log Insights (query and analyze)
│       │   ├─→ Metric Filters (create metrics from logs)
│       │   └─→ Export to S3, stream to Kinesis
│       │
│       ├─→ Alarms:
│       │   ├─→ Trigger on metric thresholds
│       │   ├─→ Actions: SNS, Auto Scaling, EC2 actions
│       │   ├─→ States: OK, ALARM, INSUFFICIENT_DATA
│       │   └─→ Composite alarms (combine multiple alarms)
│       │
│       ├─→ Dashboards:
│       │   ├─→ Visual metrics display
│       │   ├─→ Cross-region support
│       │   └─→ Shareable
│       │
│       └─→ Events (now EventBridge):
│           ├─→ Event-driven automation
│           └─→ Moved to Amazon EventBridge
│
├─→ Application performance monitoring (APM)?
│   ├─→ Distributed tracing?
│   │   └─→ **AWS X-Ray**
│   │       ├─→ End-to-end request tracking
│   │       ├─→ Service map (visualize dependencies)
│   │       ├─→ Identify bottlenecks and errors
│   │       ├─→ Integrates with: Lambda, ECS, Elastic Beanstalk, API Gateway
│   │       └─→ Use case: Microservices debugging, performance optimization
│   │
│   └─→ Real User Monitoring (RUM)?
│       └─→ **CloudWatch RUM**
│           ├─→ Client-side performance monitoring
│           ├─→ JavaScript snippet in web app
│           └─→ Use case: Monitor actual user experience
│
├─→ Monitor network traffic?
│   └─→ **VPC Flow Logs**
│       ├─→ Capture IP traffic info
│       ├─→ Levels: VPC, Subnet, ENI
│       ├─→ Send to: CloudWatch Logs, S3, Kinesis Data Firehose
│       └─→ Use case: Network troubleshooting, security analysis
│
├─→ Application insights (auto-discovery)?
│   └─→ **CloudWatch Application Insights**
│       ├─→ Automated monitoring for applications
│       ├─→ Detects and correlates anomalies
│       ├─→ Supports: .NET, SQL Server, etc.
│       └─→ Use case: Automated application monitoring
│
└─→ Third-party monitoring?
    └─→ CloudWatch supports integrations with Datadog, New Relic, Splunk, etc.
```

### CloudWatch Components Decision

```
Need to...
│
├─→ Store and query logs → CloudWatch Logs
├─→ Track performance metrics → CloudWatch Metrics
├─→ Alert on thresholds → CloudWatch Alarms
├─→ Visualize data → CloudWatch Dashboards
├─→ Trace requests across services → X-Ray
├─→ Monitor real user experience → CloudWatch RUM
└─→ Event-driven automation → EventBridge
```

---

## Infrastructure as Code

```
START: Need to automate infrastructure?
│
├─→ AWS-native IaC?
│   └─→ **AWS CloudFormation**
│       ├─→ Templates: JSON or YAML
│       ├─→ Stacks: Collection of AWS resources
│       ├─→ StackSets: Deploy across multiple accounts/regions
│       ├─→ Change Sets: Preview changes before apply
│       ├─→ Drift Detection: Detect manual changes
│       ├─→ Rollback on failure
│       ├─→ Free (pay for resources created)
│       ├─→ Features:
│       │   ├─→ Nested stacks (modular templates)
│       │   ├─→ Cross-stack references
│       │   ├─→ Custom resources (Lambda-backed)
│       │   └─→ Helper scripts (cfn-init, cfn-signal)
│       └─→ Use case: Repeatable infrastructure, multi-region deployments
│
├─→ Simplified CloudFormation?
│   └─→ **AWS SAM** (Serverless Application Model)
│       ├─→ Extension of CloudFormation
│       ├─→ Simplified syntax for serverless
│       ├─→ Resources: Lambda, API Gateway, DynamoDB, etc.
│       ├─→ Local testing (SAM CLI)
│       └─→ Use case: Serverless applications
│
├─→ Cloud Development Kit (write code)?
│   └─→ **AWS CDK** (Cloud Development Kit)
│       ├─→ Define infrastructure using programming languages
│       ├─→ Languages: TypeScript, Python, Java, C#, Go
│       ├─→ Synthesizes to CloudFormation
│       ├─→ Higher-level constructs (L2, L3)
│       └─→ Use case: Developers prefer code over YAML/JSON
│
├─→ Third-party IaC?
│   └─→ **Terraform, Pulumi, etc.**
│       └─→ Multi-cloud support
│
└─→ Visual editor?
    └─→ **CloudFormation Designer**
        └─→ Drag-and-drop interface (limited)
```

---

## Automation & Operations

```
START: Need automation?
│
├─→ Patch management and fleet operations?
│   └─→ **AWS Systems Manager**
│       ├─→ Session Manager:
│       │   ├─→ Secure shell access without SSH keys
│       │   ├─→ No bastion hosts needed
│       │   ├─→ Audit with CloudTrail
│       │   └─→ Use case: Secure instance access
│       │
│       ├─→ Patch Manager:
│       │   ├─→ Automated patching for OS and apps
│       │   ├─→ Maintenance windows
│       │   └─→ Use case: Compliance, security updates
│       │
│       ├─→ Run Command:
│       │   ├─→ Execute commands on multiple instances
│       │   ├─→ No SSH needed
│       │   └─→ Use case: Bulk operations, automation
│       │
│       ├─→ Parameter Store:
│       │   ├─→ Store configuration and secrets
│       │   ├─→ Free (Standard), paid (Advanced)
│       │   └─→ Use case: Configuration management
│       │
│       ├─→ Automation:
│       │   ├─→ Runbooks (predefined workflows)
│       │   ├─→ AWS-managed and custom runbooks
│       │   └─→ Use case: Operational tasks, remediation
│       │
│       ├─→ Inventory:
│       │   ├─→ Collect software inventory from instances
│       │   └─→ Use case: Asset management, compliance
│       │
│       ├─→ State Manager:
│       │   ├─→ Maintain instance configuration state
│       │   └─→ Use case: Configuration compliance
│       │
│       └─→ OpsCenter:
│           ├─→ Centralized operational issue management
│           └─→ Use case: Incident management
│
├─→ EC2 instance configuration at launch?
│   └─→ **User Data** or **CloudFormation::Init**
│       ├─→ User Data: Bash/PowerShell scripts
│       └─→ CloudFormation::Init: Structured configuration
│
├─→ AMI management?
│   └─→ **EC2 Image Builder**
│       ├─→ Automated AMI creation pipeline
│       ├─→ Patching, testing, distribution
│       └─→ Use case: Maintain golden AMIs
│
├─→ Service catalog for end users?
│   └─→ **AWS Service Catalog**
│       ├─→ Create and manage approved service catalogs
│       ├─→ Self-service provisioning
│       ├─→ Enforce governance and compliance
│       └─→ Use case: Standardized product offerings, governance
│
├─→ Multi-account setup?
│   └─→ **AWS Control Tower**
│       ├─→ Automated multi-account setup
│       ├─→ Landing zone (best-practice environment)
│       ├─→ Account Factory (provision new accounts)
│       ├─→ Guardrails (preventive and detective controls)
│       └─→ Use case: Large organizations, multi-account governance
│
├─→ Check AWS best practices?
│   └─→ **AWS Trusted Advisor**
│       ├─→ Real-time recommendations
│       ├─→ Categories:
│       │   ├─→ Cost Optimization
│       │   ├─→ Performance
│       │   ├─→ Security
│       │   ├─→ Fault Tolerance
│       │   └─→ Service Limits
│       ├─→ Free: Basic checks (7 checks)
│       ├─→ Business/Enterprise Support: All checks (100+)
│       └─→ Use case: Proactive optimization, best practices
│
├─→ AWS Well-Architected Framework review?
│   └─→ **AWS Well-Architected Tool**
│       ├─→ Review workloads against best practices
│       ├─→ 6 pillars assessment
│       └─→ Use case: Architecture review, improvement plans
│
└─→ Operational health and performance?
    └─→ **AWS Personal Health Dashboard**
        ├─→ Personalized view of AWS service health
        ├─→ Alerts for events affecting your resources
        └─→ Use case: Proactive issue awareness
```

---

## Cost Optimization

```
START: Need to optimize costs?
│
├─→ Analyze current spending?
│   └─→ **AWS Cost Explorer**
│       ├─→ Visualize and analyze costs
│       ├─→ Forecasting (up to 12 months)
│       ├─→ Filter by: Service, account, tag, region, etc.
│       ├─→ Savings Plans and Reserved Instance recommendations
│       ├─→ Custom reports
│       └─→ Use case: Cost analysis, trend identification
│
├─→ Set spending limits and alerts?
│   └─→ **AWS Budgets**
│       ├─→ Create custom budgets (cost, usage, RI, Savings Plans)
│       ├─→ Alerts when threshold exceeded
│       ├─→ Actions: SNS, email, stop EC2/RDS
│       ├─→ Track: Service, linked account, tag, etc.
│       └─→ Use case: Cost control, proactive alerts
│
├─→ Commit to usage for savings?
│   ├─→ EC2 Reserved Instances?
│   │   └─→ **Reserved Instances**
│   │       ├─→ 1-year or 3-year commitment
│   │       ├─→ Payment: All upfront, partial, no upfront
│   │       ├─→ Types:
│   │       │   ├─→ Standard: Specific instance type (highest discount)
│   │       │   ├─→ Convertible: Change instance type (lower discount)
│   │       │   └─→ Scheduled: Specific time windows
│   │       ├─→ Savings: 40-60%
│   │       └─→ Use case: Steady-state workloads
│   │
│   └─→ Flexible commitment across compute?
│       └─→ **Savings Plans**
│           ├─→ Compute Savings Plan:
│           │   ├─→ Most flexible (EC2, Fargate, Lambda)
│           │   ├─→ Any region, instance family, OS
│           │   └─→ Savings: Up to 66%
│           ├─→ EC2 Instance Savings Plan:
│           │   ├─→ Specific instance family in region
│           │   ├─→ Flexible size, OS, tenancy
│           │   └─→ Savings: Up to 72%
│           ├─→ Commitment: $/hour for 1 or 3 years
│           └─→ Use case: Flexible compute savings, mixed workloads
│
├─→ Spot instances for interruption-tolerant workloads?
│   └─→ **Spot Instances**
│       ├─→ Up to 90% discount
│       ├─→ Can be interrupted with 2-minute notice
│       ├─→ Spot Fleet: Mix instance types for resilience
│       └─→ Use case: Batch jobs, big data, CI/CD, containerized workloads
│
├─→ Right-size resources?
│   └─→ **AWS Compute Optimizer**
│       ├─→ ML-based recommendations
│       ├─→ Analyze: EC2, EBS, Lambda, Auto Scaling
│       ├─→ Based on CloudWatch metrics
│       └─→ Use case: Right-sizing, over-provisioned resources
│
├─→ Optimize storage costs?
│   ├─→ S3 storage?
│   │   ├─→ **S3 Lifecycle Policies**
│   │   │   ├─→ Transition between storage classes
│   │   │   └─→ Delete old objects
│   │   └─→ **S3 Intelligent-Tiering**
│   │       └─→ Automatic cost optimization
│   │
│   └─→ EBS volumes?
│       └─→ Delete unattached volumes, use gp3 instead of gp2
│
├─→ Consolidated billing?
│   └─→ **AWS Organizations**
│       ├─→ Volume discounts across accounts
│       ├─→ Shared Reserved Instances and Savings Plans
│       └─→ Use case: Multi-account cost consolidation
│
└─→ Tag resources for cost allocation?
    └─→ **Cost Allocation Tags**
        ├─→ Track costs by project, team, environment
        └─→ Use case: Granular cost tracking, chargeback
```

### Cost Optimization Decision Matrix

| Workload Type | Recommendation | Savings |
|--------------|----------------|---------|
| **Steady, 24/7** | Reserved Instances or Savings Plans | 40-72% |
| **Variable, growing** | Savings Plans (flexible) | Up to 66% |
| **Batch, interruptible** | Spot Instances | Up to 90% |
| **Unpredictable** | On-Demand + Auto Scaling | Pay as you go |
| **Mixed workload** | Compute Savings Plan | Up to 66% |
| **Development** | On-Demand or Spot | Variable |

---

## Governance & Compliance

```
START: Need governance?
│
├─→ Multiple AWS accounts?
│   └─→ **AWS Organizations**
│       ├─→ Centrally manage multiple accounts
│       ├─→ Organizational Units (OUs)
│       ├─→ Service Control Policies (SCPs)
│       │   ├─→ Maximum permissions (doesn't grant)
│       │   ├─→ Applied to OUs or accounts
│       │   └─→ Use case: Restrict actions, compliance
│       ├─→ Consolidated billing
│       ├─→ Volume discounts
│       └─→ Use case: Enterprise account management
│
├─→ Set up multi-account environment?
│   └─→ **AWS Control Tower**
│       ├─→ Automated setup and governance
│       ├─→ Landing zone
│       ├─→ Account Factory
│       ├─→ Guardrails (SCPs + Config Rules)
│       └─→ Use case: Best-practice multi-account setup
│
├─→ Enforce compliance and standards?
│   ├─→ Configuration compliance?
│   │   └─→ **AWS Config**
│   │       ├─→ Track resource configurations
│   │       ├─→ Config Rules (compliance checks)
│   │       └─→ Auto-remediation
│   │
│   └─→ Security posture management?
│       └─→ **AWS Security Hub**
│           └─→ Automated compliance checks (CIS, PCI-DSS, etc.)
│
├─→ Catalog approved services?
│   └─→ **AWS Service Catalog**
│       ├─→ Approved products/services
│       ├─→ Self-service with governance
│       └─→ Use case: Standardization, compliance
│
├─→ License management?
│   └─→ **AWS License Manager**
│       ├─→ Manage software licenses
│       ├─→ Prevent overages
│       └─→ Use case: License compliance, cost control
│
└─→ Resource tagging enforcement?
    └─→ **Tag Policies** (in AWS Organizations)
        └─→ Standardize tags across accounts
```

---

## Exam Scenarios - Quick Answers

### Scenario 1: Monitor EC2 CPU and memory, alert if > 80%
**Answer**: CloudWatch (custom metric for memory) + CloudWatch Alarms

### Scenario 2: Store application configuration, free option
**Answer**: Systems Manager Parameter Store (Standard)

### Scenario 3: Automate OS patching across 100 EC2 instances
**Answer**: Systems Manager Patch Manager

### Scenario 4: Deploy same infrastructure to 10 AWS regions
**Answer**: CloudFormation StackSets

### Scenario 5: Analyze spending and identify cost savings
**Answer**: AWS Cost Explorer + Compute Optimizer

### Scenario 6: Alert when monthly bill exceeds $1000
**Answer**: AWS Budgets

### Scenario 7: Secure SSH access without SSH keys or bastion
**Answer**: Systems Manager Session Manager

### Scenario 8: Track all resource configuration changes
**Answer**: AWS Config

### Scenario 9: Get AWS best practice recommendations
**Answer**: AWS Trusted Advisor

### Scenario 10: Multi-account setup with governance
**Answer**: AWS Control Tower

### Scenario 11: Trace requests across microservices
**Answer**: AWS X-Ray

### Scenario 12: Commit to $100/hour EC2 usage for 1 year
**Answer**: Compute Savings Plan

### Scenario 13: Reduce EC2 costs for batch jobs (can be interrupted)
**Answer**: Spot Instances

### Scenario 14: Centrally manage 50 AWS accounts
**Answer**: AWS Organizations

### Scenario 15: Provide self-service cloud resources to developers
**Answer**: AWS Service Catalog

---

## 🎯 Key Takeaways

### Monitoring:
- **CloudWatch**: Metrics, logs, alarms, dashboards
- **X-Ray**: Distributed tracing, performance debugging
- **VPC Flow Logs**: Network traffic analysis

### Infrastructure as Code:
- **CloudFormation**: AWS-native IaC, JSON/YAML templates
- **CDK**: Define infrastructure with programming languages
- **SAM**: Simplified CloudFormation for serverless

### Automation:
- **Systems Manager**: Fleet management, patching, secure access
- **Session Manager**: Secure shell access without SSH keys
- **Patch Manager**: Automated patching
- **Run Command**: Execute commands on multiple instances

### Cost Optimization:
- **Cost Explorer**: Analyze spending, forecasting
- **Budgets**: Set spending limits, alerts
- **Compute Optimizer**: Right-sizing recommendations
- **Reserved Instances / Savings Plans**: Commit for 40-72% savings
- **Spot Instances**: Up to 90% savings for interruptible workloads

### Governance:
- **Organizations**: Multi-account management, consolidated billing
- **Control Tower**: Automated multi-account setup
- **Service Catalog**: Approved service catalog
- **Config**: Configuration compliance and remediation

### Best Practices:
- **Trusted Advisor**: Real-time best practice checks
- **Well-Architected Tool**: Review against 6 pillars
- **Personal Health Dashboard**: Personalized AWS service health

---

*Use these decision trees for quick management and cost optimization decisions!*
