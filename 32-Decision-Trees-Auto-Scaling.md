# AWS SAA-C03: Auto Scaling & Elasticity Decision Trees

## 🌳 Quick Navigation
- [Auto Scaling Types](#auto-scaling-types)
- [EC2 Auto Scaling](#ec2-auto-scaling)
- [Application Auto Scaling](#application-auto-scaling)
- [Scaling Policies](#scaling-policies)
- [Elasticity Patterns](#elasticity-patterns)

---

## Auto Scaling Types

```
START: Need auto scaling?
│
├─→ What resource?
│   │
│   ├─→ EC2 Instances?
│   │   └─→ **EC2 Auto Scaling**
│   │       ├─→ Launch/terminate EC2 instances
│   │       ├─→ Auto Scaling Group (ASG)
│   │       ├─→ Launch Template (recommended) or Launch Configuration
│   │       └─→ Use case: Web servers, application tiers
│   │
│   ├─→ ECS Tasks?
│   │   └─→ **ECS Service Auto Scaling**
│   │       ├─→ Adjust task count
│   │       ├─→ Target tracking, step scaling, scheduled
│   │       └─→ Use case: Container workloads
│   │
│   ├─→ DynamoDB?
│   │   └─→ **DynamoDB Auto Scaling**
│   │       ├─→ Adjust provisioned capacity (RCU/WCU)
│   │       ├─→ Target tracking on utilization
│   │       └─→ Or use On-Demand capacity mode
│   │
│   ├─→ Aurora?
│   │   └─→ **Aurora Auto Scaling**
│   │       ├─→ Add/remove Aurora Replicas
│   │       ├─→ Or use Aurora Serverless (automatic)
│   │       └─→ Use case: Read scaling
│   │
│   ├─→ Lambda?
│   │   └─→ **Lambda Automatic Scaling**
│   │       ├─→ Built-in (automatic)
│   │       ├─→ Concurrency limits for control
│   │       ├─→ Provisioned Concurrency for consistent
│   │       └─→ Up to 1000 concurrent (default)
│   │
│   ├─→ ElastiCache?
│   │   └─→ **ElastiCache Auto Scaling** (Redis)
│   │       ├─→ Scale shards or replicas
│   │       └─→ Use case: Cache tier scaling
│   │
│   └─→ Other AWS Services?
│       └─→ **AWS Application Auto Scaling**
│           ├─→ Unified scaling for many services
│           ├─→ Services: ECS, DynamoDB, Aurora, EMR, AppStream
│           ├─→ Comprehend, SageMaker, Lambda provisioned
│           └─→ Custom resources via CloudWatch metrics
│
└─→ Auto Scaling Overview
    │
    │ Resource              │ Auto Scaling Type              │
    │───────────────────────│────────────────────────────────│
    │ EC2                   │ EC2 Auto Scaling Groups        │
    │ ECS                   │ Application Auto Scaling       │
    │ EKS                   │ Kubernetes HPA, Cluster AS     │
    │ DynamoDB              │ Application Auto Scaling       │
    │ Aurora                │ Application Auto Scaling       │
    │ Lambda                │ Automatic (built-in)           │
    │ ElastiCache           │ ElastiCache Auto Scaling       │
```

---

## EC2 Auto Scaling

```
START: Configure EC2 Auto Scaling?
│
├─→ Components
│   │
│   ├─→ Launch Template (Recommended)
│   │   └─→ Defines:
│   │       ├─→ AMI ID
│   │       ├─→ Instance type
│   │       ├─→ Key pair
│   │       ├─→ Security groups
│   │       ├─→ User data
│   │       ├─→ IAM role
│   │       ├─→ Storage (EBS)
│   │       ├─→ Network settings
│   │       ├─→ Supports: Mixed instances, Spot
│   │       └─→ **Versioned, can be updated**
│   │
│   ├─→ Launch Configuration (Legacy)
│   │   └─→ Similar to Launch Template
│   │       └─→ **Immutable, must create new**
│   │
│   └─→ Auto Scaling Group (ASG)
│       └─→ Defines:
│           ├─→ Desired capacity
│           ├─→ Minimum capacity
│           ├─→ Maximum capacity
│           ├─→ Availability Zones
│           ├─→ Load Balancer target groups
│           ├─→ Health check type
│           ├─→ Scaling policies
│           └─→ Instance refresh settings
│
├─→ Capacity Settings
│   │
│   ├─→ Min Capacity: Never go below this
│   ├─→ Max Capacity: Never go above this
│   ├─→ Desired Capacity: Target at any time
│   │
│   └─→ Example:
│       ├─→ Min: 2 (always running)
│       ├─→ Max: 10 (cap for cost control)
│       └─→ Desired: 4 (current target)
│
├─→ Health Checks
│   │
│   ├─→ EC2 Health Check (default)
│   │   └─→ Instance status checks (running, not impaired)
│   │
│   ├─→ ELB Health Check
│   │   └─→ Use Load Balancer health checks
│   │       └─→ Recommended when using ELB
│   │
│   └─→ Custom Health Check
│       └─→ Set instance health via API
│
├─→ Termination Policy
│   │
│   ├─→ Default:
│   │   1. Select AZ with most instances
│   │   2. Oldest launch configuration
│   │   3. Closest to billing hour
│   │
│   ├─→ Options:
│   │   ├─→ OldestInstance
│   │   ├─→ NewestInstance
│   │   ├─→ OldestLaunchConfiguration
│   │   ├─→ OldestLaunchTemplate
│   │   ├─→ ClosestToNextInstanceHour
│   │   └─→ AllocationStrategy (for Spot)
│   │
│   └─→ Instance Protection
│       └─→ Protect specific instances from termination
│
├─→ Mixed Instances Policy
│   │
│   ├─→ Multiple instance types
│   │   └─→ Diversify for cost and availability
│   │
│   ├─→ On-Demand + Spot mix
│   │   └─→ Example: 70% On-Demand base + 30% Spot
│   │
│   └─→ Spot allocation strategies:
│       ├─→ lowest-price: Cheapest pools
│       ├─→ capacity-optimized: Lowest interruption
│       └─→ capacity-optimized-prioritized: Priority + capacity
│
├─→ Instance Refresh
│   │
│   └─→ Rolling update of instances
│       ├─→ Replace instances with new Launch Template
│       ├─→ Min healthy percentage
│       ├─→ Warm-up time
│       └─→ Use case: AMI updates, configuration changes
│
└─→ Scaling Cooldowns
    │
    ├─→ Default cooldown: 300 seconds
    │   └─→ Wait before next scaling activity
    │
    └─→ Purpose:
        └─→ Prevent oscillation (scale up/down repeatedly)
```

---

## Application Auto Scaling

```
START: Application Auto Scaling?
│
├─→ Scalable Targets
│   │
│   ├─→ ECS Services
│   │   └─→ Scale task count
│   │
│   ├─→ DynamoDB Tables/Indexes
│   │   └─→ Scale RCU/WCU
│   │
│   ├─→ Aurora Replicas
│   │   └─→ Scale replica count
│   │
│   ├─→ EMR Clusters
│   │   └─→ Scale instance count
│   │
│   ├─→ Lambda Provisioned Concurrency
│   │   └─→ Scale provisioned capacity
│   │
│   └─→ Custom Resources
│       └─→ Any resource with CloudWatch metrics
│
├─→ DynamoDB Auto Scaling
│   │
│   ├─→ Target Tracking
│   │   ├─→ Target: Consumed capacity / Provisioned capacity
│   │   ├─→ Typical target: 70% utilization
│   │   └─→ Scales RCU and WCU independently
│   │
│   ├─→ Configuration:
│   │   ├─→ Min capacity
│   │   ├─→ Max capacity
│   │   └─→ Target utilization (%)
│   │
│   └─→ Alternative: On-Demand Mode
│       ├─→ No capacity planning
│       ├─→ Pay per request
│       └─→ Higher per-request cost
│
├─→ ECS Service Auto Scaling
│   │
│   ├─→ Target Tracking
│   │   ├─→ ECSServiceAverageCPUUtilization
│   │   ├─→ ECSServiceAverageMemoryUtilization
│   │   └─→ ALBRequestCountPerTarget
│   │
│   ├─→ Step Scaling
│   │   └─→ Multiple thresholds with different actions
│   │
│   └─→ Scheduled Scaling
│       └─→ Time-based capacity changes
│
└─→ Aurora Auto Scaling
    │
    ├─→ Aurora Replica Auto Scaling
    │   ├─→ Add/remove read replicas
    │   └─→ Scale: 0-15 replicas
    │
    ├─→ Target Tracking
    │   ├─→ Average CPU utilization
    │   └─→ Average connections
    │
    └─→ Alternative: Aurora Serverless
        └─→ Automatic capacity management (ACUs)
```

---

## Scaling Policies

```
START: Which scaling policy?
│
├─→ Simple, hands-off?
│   └─→ **Target Tracking Scaling**
│       ├─→ Set target metric value
│       ├─→ ASG maintains target automatically
│       ├─→ Scales up and down as needed
│       ├─→ Common targets:
│       │   ├─→ CPUUtilization: 50%
│       │   ├─→ RequestCountPerTarget: 1000
│       │   ├─→ NetworkIn/Out
│       │   └─→ Custom CloudWatch metric
│       ├─→ Built-in metrics:
│       │   ├─→ ASGAverageCPUUtilization
│       │   ├─→ ASGAverageNetworkIn/Out
│       │   └─→ ALBRequestCountPerTarget
│       └─→ Use case: Most common, recommended default
│
├─→ Fine-grained control?
│   └─→ **Step Scaling**
│       ├─→ Multiple steps based on alarm threshold
│       ├─→ Different actions at different levels
│       ├─→ Example:
│       │   ├─→ CPU 60-70%: Add 1 instance
│       │   ├─→ CPU 70-80%: Add 2 instances
│       │   └─→ CPU 80%+: Add 3 instances
│       └─→ Use case: Variable scaling response
│
├─→ Simple threshold-based?
│   └─→ **Simple Scaling** (Legacy)
│       ├─→ Single threshold, single action
│       ├─→ Waits for cooldown before next action
│       ├─→ Less responsive than Step Scaling
│       └─→ Use case: Simple scenarios (prefer Target Tracking)
│
├─→ Predictable patterns?
│   └─→ **Scheduled Scaling**
│       ├─→ Scale at specific times
│       ├─→ Cron-like scheduling
│       ├─→ Examples:
│       │   ├─→ Scale up Monday 8 AM
│       │   ├─→ Scale down Friday 6 PM
│       │   └─→ Scale for monthly events
│       └─→ Use case: Known traffic patterns
│
├─→ ML-based prediction?
│   └─→ **Predictive Scaling**
│       ├─→ ML forecasts future traffic
│       ├─→ Pre-provisions capacity
│       ├─→ Based on historical patterns
│       ├─→ 14-day history required
│       ├─→ Modes:
│       │   ├─→ Forecast only: Just predict
│       │   └─→ Forecast and scale: Automatic scaling
│       └─→ Use case: Cyclical/predictable workloads
│
└─→ Scaling Policy Comparison
    │
    │ Policy Type       │ Best For                    │ Complexity │
    │───────────────────│─────────────────────────────│────────────│
    │ Target Tracking   │ Most workloads, hands-off   │ Low        │
    │ Step Scaling      │ Variable response needed    │ Medium     │
    │ Simple Scaling    │ Basic scenarios (legacy)    │ Low        │
    │ Scheduled         │ Predictable patterns        │ Low        │
    │ Predictive        │ Cyclical, ML-based          │ Low        │
```

### Scaling Policy Decision Tree

```
START: Which policy to use?
│
├─→ Want simplest setup?
│   └─→ **Target Tracking**
│       └─→ Set target (e.g., CPU 50%), done
│
├─→ Need different responses at different levels?
│   └─→ **Step Scaling**
│       └─→ Define multiple thresholds and actions
│
├─→ Know exact schedule?
│   └─→ **Scheduled Scaling**
│       └─→ Scale at specific times
│
├─→ Have predictable daily/weekly patterns?
│   └─→ **Predictive Scaling + Target Tracking**
│       └─→ Pre-provision + reactive scaling
│
└─→ Combine Policies
    └─→ You can use multiple policies together:
        ├─→ Scheduled: Base capacity for known events
        ├─→ Predictive: Pre-warm for cyclical patterns
        └─→ Target Tracking: Handle unexpected traffic
```

---

## Elasticity Patterns

```
START: Design for elasticity?
│
├─→ Pattern: Horizontal Scaling
│   │
│   │ ┌──────────┐
│   │ │    ELB   │
│   │ └────┬─────┘
│   │      │
│   │ ┌────┴────┐
│   │ ▼    ▼    ▼
│   │ EC2  EC2  EC2  (Add/remove instances)
│   │
│   └─→ Implementation:
│       ├─→ EC2 Auto Scaling Group
│       ├─→ Load Balancer distributes traffic
│       ├─→ Stateless instances
│       └─→ External state (ElastiCache, DynamoDB)
│
├─→ Pattern: Vertical Scaling
│   │
│   │ Small → Medium → Large → XLarge
│   │
│   └─→ Limitations:
│       ├─→ Requires downtime (stop/start)
│       ├─→ Single instance limit
│       └─→ Less common for auto scaling
│
├─→ Pattern: Time-Based Scaling
│   │
│   │ Capacity
│   │     │    ┌────┐
│   │     │    │    │    Business Hours
│   │     │    │    │
│   │     └────┘    └────  Night/Weekend
│   │ ──────────────────────▶ Time
│   │
│   └─→ Implementation:
│       ├─→ Scheduled Scaling actions
│       ├─→ Higher capacity during business hours
│       └─→ Lower capacity nights/weekends
│
├─→ Pattern: Event-Based Scaling
│   │
│   │ Marketing Campaign → Scale up before event
│   │ Black Friday       → Pre-provision capacity
│   │ Game Launch        → Scale for launch day
│   │
│   └─→ Implementation:
│       ├─→ Scheduled Scaling (known events)
│       └─→ Target Tracking (handle surge)
│
├─→ Pattern: Queue-Based Scaling
│   │
│   │ Producers ──▶ SQS Queue ──▶ Consumers (ASG)
│   │                   │
│   │                   ▼
│   │             Queue Depth
│   │                   │
│   │                   ▼
│   │           Scale Workers
│   │
│   └─→ Implementation:
│       ├─→ CloudWatch: ApproximateNumberOfMessagesVisible
│       ├─→ Target Tracking or Step Scaling
│       └─→ Scale workers based on queue depth
│
├─→ Pattern: Mixed Instance Scaling
│   │
│   │ Base Capacity (On-Demand) + Burst Capacity (Spot)
│   │
│   │ ┌─────────────────────────────────────┐
│   │ │ On-Demand (base)  │ Spot (burst)    │
│   │ └─────────────────────────────────────┘
│   │
│   └─→ Implementation:
│       ├─→ Mixed Instances Policy in ASG
│       ├─→ On-Demand base percentage (e.g., 30%)
│       ├─→ Spot for additional capacity
│       └─→ Multiple instance types for Spot diversity
│
├─→ Pattern: Multi-Tier Scaling
│   │
│   │ Web Tier ASG ◄── ALB
│   │       │
│   │       ▼
│   │ App Tier ASG
│   │       │
│   │       ▼
│   │ Cache (ElastiCache) + Database (RDS)
│   │
│   └─→ Implementation:
│       ├─→ Separate ASG per tier
│       ├─→ Different scaling policies per tier
│       ├─→ Internal load balancers between tiers
│       └─→ Scale each tier independently
│
└─→ Pattern: Container Scaling
    │
    │ ECS/EKS Cluster
    │       │
    │       ├── Service Auto Scaling (task count)
    │       │
    │       └── Cluster Auto Scaling (EC2 capacity)
    │
    └─→ Implementation:
        ├─→ ECS Service Auto Scaling (tasks)
        ├─→ ECS Capacity Provider (cluster nodes)
        └─→ Or use Fargate (no cluster management)
```

---

## Exam Scenarios - Quick Answers

### Scenario 1: Maintain minimum 3 instances, max 10, scale on CPU
**Answer**: ASG with Min=3, Max=10, Target Tracking (CPU 50%)

### Scenario 2: Scale based on SQS queue depth
**Answer**: Step Scaling or Target Tracking based on ApproximateNumberOfMessagesVisible

### Scenario 3: Scale up every weekday at 8 AM
**Answer**: Scheduled Scaling action

### Scenario 4: Auto scale DynamoDB capacity
**Answer**: DynamoDB Auto Scaling (Target Tracking on utilization) or On-Demand mode

### Scenario 5: Replace EC2 instances with new AMI without downtime
**Answer**: Instance Refresh in Auto Scaling Group

### Scenario 6: Mix On-Demand and Spot instances in ASG
**Answer**: Mixed Instances Policy in Launch Template

### Scenario 7: Scale ECS tasks based on CPU
**Answer**: ECS Service Auto Scaling with Target Tracking

### Scenario 8: Use ML to predict and pre-provision capacity
**Answer**: Predictive Scaling

### Scenario 9: Scale at different rates for different CPU levels
**Answer**: Step Scaling policy

### Scenario 10: Automatically replace unhealthy instances
**Answer**: ASG with ELB health check type

### Scenario 11: Scale based on custom application metric
**Answer**: Target Tracking with custom CloudWatch metric

### Scenario 12: Scale Aurora read replicas
**Answer**: Aurora Auto Scaling (Application Auto Scaling)

---

## 🎯 Key Takeaways

### EC2 Auto Scaling:
- **Launch Template** > Launch Configuration (versioned, more features)
- **Min/Max/Desired**: Control capacity boundaries
- **Health checks**: EC2 (default) or ELB (recommended with load balancer)
- **Instance Refresh**: Rolling updates without downtime

### Scaling Policies:
- **Target Tracking**: Simplest, handles both scale-out and scale-in
- **Step Scaling**: Multiple thresholds, variable response
- **Scheduled**: Known patterns (time-based)
- **Predictive**: ML-based forecasting

### Best Practices:
1. Use Launch Templates (not Launch Configurations)
2. Enable ELB health checks when using load balancers
3. Start with Target Tracking (simplest)
4. Set appropriate cooldowns to prevent oscillation
5. Use multiple AZs for high availability
6. Consider Predictive + Target Tracking for best results

### Common Metrics:
- **EC2**: CPUUtilization, NetworkIn/Out
- **ALB**: RequestCountPerTarget
- **SQS**: ApproximateNumberOfMessagesVisible
- **DynamoDB**: ConsumedReadCapacityUnits/ConsumedWriteCapacityUnits

---

*Use these decision trees for auto scaling questions on the exam!*
