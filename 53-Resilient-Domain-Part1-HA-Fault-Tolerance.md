# AWS SAA-C03: DESIGN RESILIENT ARCHITECTURES - PART 1

## 📋 HIGH AVAILABILITY & FAULT TOLERANCE

---

# 📊 DOMAIN OVERVIEW

| Aspect | Details |
|--------|---------|
| **Exam Weight** | **26%** (~17 questions out of 65) |
| **Focus Areas** | Multi-tier architectures, HA, fault tolerance, decoupling, DR |
| **Key Services** | ELB, Auto Scaling, RDS Multi-AZ, Route 53, SQS, SNS |
| **Primary Goal** | Design architectures that recover from failures automatically |

---

## 🎯 FOUR FOCUS AREAS

```
┌─────────────────────────────────────────────────────────────────────┐
│                    DESIGN RESILIENT ARCHITECTURES                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  1. MULTI-TIER ARCHITECTURES        2. HIGHLY AVAILABLE SYSTEMS     │
│  ┌─────────────────────────────┐   ┌─────────────────────────────┐  │
│  │ • Presentation tier         │   │ • Multi-AZ deployments      │  │
│  │ • Application tier          │   │ • Load balancing            │  │
│  │ • Data tier                 │   │ • Auto Scaling              │  │
│  │ • Caching tier              │   │ • Health checks             │  │
│  │ • Decoupling patterns       │   │ • Failover mechanisms       │  │
│  └─────────────────────────────┘   └─────────────────────────────┘  │
│                                                                      │
│  3. DECOUPLING MECHANISMS           4. DISASTER RECOVERY            │
│  ┌─────────────────────────────┐   ┌─────────────────────────────┐  │
│  │ • Message queues (SQS)      │   │ • Backup strategies         │  │
│  │ • Pub/sub (SNS)             │   │ • Cross-region replication  │  │
│  │ • Event-driven (EventBridge)│   │ • Recovery objectives       │  │
│  │ • Workflow orchestration    │   │ • Failover automation       │  │
│  └─────────────────────────────┘   └─────────────────────────────┘  │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

# 🏗️ ELASTIC LOAD BALANCING (ELB)

## Definition
**Elastic Load Balancing** automatically distributes incoming application traffic across multiple targets (EC2 instances, containers, IP addresses, Lambda functions) in one or more Availability Zones.

---

## Load Balancer Types Comparison

| Feature | ALB | NLB | GWLB | CLB |
|---------|-----|-----|------|-----|
| **Layer** | Layer 7 (HTTP/HTTPS) | Layer 4 (TCP/UDP/TLS) | Layer 3 (IP) | Layer 4/7 |
| **Protocol** | HTTP, HTTPS, gRPC | TCP, UDP, TLS | IP (GENEVE) | TCP, SSL, HTTP, HTTPS |
| **Performance** | Good | Millions RPS, ultra-low latency | High | Basic |
| **Static IP** | No (use Global Accelerator) | Yes (1 per AZ) | No | No |
| **Preserve Client IP** | X-Forwarded-For header | Yes (native) | Yes | X-Forwarded-For |
| **WebSockets** | Yes | Yes | N/A | No |
| **Target Types** | Instance, IP, Lambda | Instance, IP, ALB | Instance, IP | Instance |
| **Health Checks** | HTTP, HTTPS | TCP, HTTP, HTTPS | TCP, HTTP, HTTPS | TCP, HTTP |
| **SSL Termination** | Yes | Yes (TLS) | No | Yes |
| **Path-based Routing** | Yes | No | No | No |
| **Host-based Routing** | Yes | No | No | No |
| **Use Case** | Web apps, microservices | Extreme performance, gaming | 3rd party appliances | Legacy |

---

## Application Load Balancer (ALB)

### Definition
Layer 7 load balancer for HTTP/HTTPS traffic with advanced request routing based on URL path, host headers, HTTP headers, query strings, and source IP.

### Key Concepts

```
┌─────────────────────────────────────────────────────────────────────┐
│                    ALB ARCHITECTURE                                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   Internet                                                           │
│       │                                                              │
│       ▼                                                              │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │                    APPLICATION LOAD BALANCER                 │   │
│   │   ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │   │
│   │   │  Listener   │  │  Listener   │  │  Listener   │        │   │
│   │   │  Port 80    │  │  Port 443   │  │  Port 8080  │        │   │
│   │   └──────┬──────┘  └──────┬──────┘  └──────┬──────┘        │   │
│   │          │                │                │                │   │
│   │          ▼                ▼                ▼                │   │
│   │   ┌─────────────────────────────────────────────────────┐  │   │
│   │   │                    LISTENER RULES                    │  │   │
│   │   │  IF path=/api/*     → Target Group: API Servers     │  │   │
│   │   │  IF path=/images/*  → Target Group: Image Servers   │  │   │
│   │   │  IF host=api.com    → Target Group: API Servers     │  │   │
│   │   │  DEFAULT            → Target Group: Web Servers     │  │   │
│   │   └─────────────────────────────────────────────────────┘  │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│   ┌───────────────┐  ┌───────────────┐  ┌───────────────┐          │
│   │ Target Group  │  │ Target Group  │  │ Target Group  │          │
│   │  Web Servers  │  │  API Servers  │  │ Image Servers │          │
│   │ ┌───┐ ┌───┐  │  │ ┌───┐ ┌───┐  │  │ ┌───┐ ┌───┐  │          │
│   │ │EC2│ │EC2│  │  │ │EC2│ │EC2│  │  │ │EC2│ │EC2│  │          │
│   │ └───┘ └───┘  │  │ └───┘ └───┘  │  │ └───┘ └───┘  │          │
│   └───────────────┘  └───────────────┘  └───────────────┘          │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Configuration Rules

| Configuration | Details |
|---------------|---------|
| **Listeners** | HTTP (80), HTTPS (443), custom ports (1-65535) |
| **Rules** | Conditions + Actions, processed in order |
| **Conditions** | host-header, path-pattern, http-header, http-method, query-string, source-ip |
| **Actions** | forward, redirect, fixed-response, authenticate-cognito, authenticate-oidc |
| **Target Groups** | Instance ID, IP address, Lambda function |
| **Health Checks** | Path, port, protocol, thresholds, intervals |
| **Sticky Sessions** | Application-based (AWSALBAPP), Duration-based (AWSALB) |
| **Cross-Zone LB** | Enabled by default (no charges) |
| **Idle Timeout** | Default 60 seconds (1-4000 seconds) |

### Routing Algorithms

| Algorithm | Description | Use Case |
|-----------|-------------|----------|
| **Round Robin** | Requests distributed evenly | Default, general purpose |
| **Least Outstanding Requests** | Target with fewest pending requests | Varying request complexity |
| **Weighted Target Groups** | Route based on weight | Canary deployments, A/B testing |

### Limits

| Limit | Value |
|-------|-------|
| Listeners per ALB | 50 |
| Target groups per ALB | 100 |
| Targets per target group | 1,000 |
| Rules per ALB | 100 (default), 200 (max) |
| Condition values per rule | 5 |

### Exam Heuristics

| Keyword/Scenario | Answer |
|------------------|--------|
| HTTP/HTTPS routing | **ALB** |
| Path-based routing | **ALB with path-pattern condition** |
| Host-based routing (multiple domains) | **ALB with host-header condition** |
| Microservices routing | **ALB** |
| Lambda as target | **ALB** |
| Authentication integration | **ALB with authenticate-cognito/oidc** |
| WebSocket support | **ALB** |
| gRPC support | **ALB** |
| A/B testing | **ALB weighted target groups** |
| Blue-green deployment | **ALB weighted target groups** |
| HTTP to HTTPS redirect | **ALB redirect action** |
| Sticky sessions (application) | **ALB application-based stickiness** |

---

## Network Load Balancer (NLB)

### Definition
Layer 4 load balancer capable of handling millions of requests per second with ultra-low latency. Preserves client IP address and provides static IP per AZ.

### Key Concepts

```
┌─────────────────────────────────────────────────────────────────────┐
│                    NLB ARCHITECTURE                                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   Client (Source IP: 203.0.113.50)                                  │
│       │                                                              │
│       ▼                                                              │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │              NETWORK LOAD BALANCER                           │   │
│   │   Static IP: 52.1.2.3 (AZ-a)   Static IP: 52.4.5.6 (AZ-b)  │   │
│   │                                                              │   │
│   │   ┌─────────────┐  ┌─────────────┐                          │   │
│   │   │  Listener   │  │  Listener   │                          │   │
│   │   │  TCP 443    │  │  UDP 514    │                          │   │
│   │   └─────────────┘  └─────────────┘                          │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                              │                                       │
│            ┌─────────────────┼─────────────────┐                    │
│            ▼                 ▼                 ▼                    │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │                      TARGET GROUP                            │   │
│   │   Target sees: Source IP = 203.0.113.50 (Client IP)         │   │
│   │                                                              │   │
│   │   ┌───────────┐    ┌───────────┐    ┌───────────┐          │   │
│   │   │  EC2 (a)  │    │  EC2 (b)  │    │   ALB     │          │   │
│   │   │ Instance  │    │ Instance  │    │  Target   │          │   │
│   │   └───────────┘    └───────────┘    └───────────┘          │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Configuration Rules

| Configuration | Details |
|---------------|---------|
| **Protocols** | TCP, UDP, TLS, TCP_UDP |
| **Ports** | 1-65535 |
| **Static IP** | One Elastic IP per AZ (optional) |
| **Target Types** | Instance, IP, ALB |
| **Health Checks** | TCP, HTTP, HTTPS |
| **Cross-Zone LB** | Disabled by default (charges apply if enabled) |
| **Client IP Preservation** | Enabled by default for instance targets |
| **Proxy Protocol v2** | Optional, adds connection metadata |
| **TLS Termination** | Supported with ACM certificates |

### Unique Features

| Feature | Description |
|---------|-------------|
| **Zonal Isolation** | Cross-zone disabled = traffic stays in same AZ |
| **Static IP** | Whitelist-friendly, firewall-friendly |
| **PrivateLink** | Create VPC endpoint services |
| **ALB as Target** | Combine ALB features with NLB static IP |
| **UDP Support** | Only load balancer supporting UDP |
| **Flow Hash** | Same client → same target (TCP) |

### Limits

| Limit | Value |
|-------|-------|
| Listeners per NLB | 50 |
| Target groups per NLB | 50 |
| Targets per target group | 1,000 |
| Targets per AZ per NLB | 500 |

### Exam Heuristics

| Keyword/Scenario | Answer |
|------------------|--------|
| Static IP address | **NLB** |
| Ultra-low latency | **NLB** |
| Millions of requests | **NLB** |
| TCP/UDP traffic | **NLB** |
| Preserve client IP (Layer 4) | **NLB** |
| Gaming, IoT | **NLB** |
| Whitelist by IP | **NLB** (static IP) |
| VPC Endpoint Service | **NLB** (PrivateLink) |
| Expose service to other VPCs | **NLB + PrivateLink** |
| TLS passthrough | **NLB** |
| Need ALB features + static IP | **NLB with ALB as target** |

---

## Gateway Load Balancer (GWLB)

### Definition
Layer 3 load balancer for deploying, scaling, and managing third-party virtual appliances (firewalls, IDS/IPS, deep packet inspection systems).

### Key Concepts

```
┌─────────────────────────────────────────────────────────────────────┐
│                    GWLB ARCHITECTURE                                 │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   ┌───────────────────────────────────────────────────────────────┐ │
│   │                    SERVICE CONSUMER VPC                        │ │
│   │   ┌─────────────┐                                             │ │
│   │   │   EC2       │                                             │ │
│   │   │ Application │                                             │ │
│   │   └──────┬──────┘                                             │ │
│   │          │                                                     │ │
│   │          ▼                                                     │ │
│   │   ┌─────────────────────────────┐                             │ │
│   │   │   GWLB Endpoint (GWLBe)    │  ◀── Traffic routed here    │ │
│   │   └─────────────────────────────┘                             │ │
│   └───────────────────────────────────────────────────────────────┘ │
│                          │                                          │
│                          │ (GENEVE encapsulation)                   │
│                          ▼                                          │
│   ┌───────────────────────────────────────────────────────────────┐ │
│   │                    SERVICE PROVIDER VPC                        │ │
│   │   ┌─────────────────────────────┐                             │ │
│   │   │    GATEWAY LOAD BALANCER   │                              │ │
│   │   └─────────────────────────────┘                             │ │
│   │                    │                                           │ │
│   │          ┌─────────┼─────────┐                                │ │
│   │          ▼         ▼         ▼                                │ │
│   │   ┌──────────┐ ┌──────────┐ ┌──────────┐                     │ │
│   │   │ Firewall │ │ Firewall │ │ Firewall │                     │ │
│   │   │    #1    │ │    #2    │ │    #3    │                     │ │
│   │   └──────────┘ └──────────┘ └──────────┘                     │ │
│   │                                                                │ │
│   │   Traffic inspected → returned via same path                  │ │
│   └───────────────────────────────────────────────────────────────┘ │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Configuration Rules

| Configuration | Details |
|---------------|---------|
| **Protocol** | IP (all traffic) |
| **Port** | 6081 (GENEVE) |
| **Target Types** | Instance, IP |
| **Encapsulation** | GENEVE protocol |
| **Health Checks** | TCP, HTTP, HTTPS |
| **Cross-Zone LB** | Disabled by default |
| **Endpoint** | GWLBe in consumer VPC |

### Exam Heuristics

| Keyword/Scenario | Answer |
|------------------|--------|
| Third-party firewall | **GWLB** |
| IDS/IPS inspection | **GWLB** |
| Network appliance scaling | **GWLB** |
| Deep packet inspection | **GWLB** |
| Centralized security appliances | **GWLB** |
| Traffic inspection before reaching app | **GWLB** |

---

# 🔄 AUTO SCALING

## EC2 Auto Scaling

### Definition
**EC2 Auto Scaling** ensures you have the correct number of EC2 instances available to handle the load for your application. It automatically adjusts capacity based on conditions you define.

### Key Components

```
┌─────────────────────────────────────────────────────────────────────┐
│                    AUTO SCALING COMPONENTS                           │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │                    AUTO SCALING GROUP (ASG)                  │   │
│   │                                                              │   │
│   │   LAUNCH TEMPLATE / CONFIGURATION                           │   │
│   │   ┌─────────────────────────────────────────────────────┐   │   │
│   │   │ • AMI ID                                            │   │   │
│   │   │ • Instance Type                                     │   │   │
│   │   │ • Key Pair                                          │   │   │
│   │   │ • Security Groups                                   │   │   │
│   │   │ • IAM Instance Profile                              │   │   │
│   │   │ • User Data                                         │   │   │
│   │   │ • EBS Configuration                                 │   │   │
│   │   └─────────────────────────────────────────────────────┘   │   │
│   │                                                              │   │
│   │   CAPACITY SETTINGS                                         │   │
│   │   ┌─────────────────────────────────────────────────────┐   │   │
│   │   │ Minimum Capacity:  2                                │   │   │
│   │   │ Desired Capacity:  4                                │   │   │
│   │   │ Maximum Capacity: 10                                │   │   │
│   │   └─────────────────────────────────────────────────────┘   │   │
│   │                                                              │   │
│   │   AVAILABILITY ZONES                                        │   │
│   │   ┌─────────────────────────────────────────────────────┐   │   │
│   │   │  us-east-1a  │  us-east-1b  │  us-east-1c          │   │   │
│   │   │  ┌───┐ ┌───┐ │  ┌───┐ ┌───┐ │  ┌───┐               │   │   │
│   │   │  │EC2│ │EC2│ │  │EC2│ │EC2│ │  │EC2│               │   │   │
│   │   │  └───┘ └───┘ │  └───┘ └───┘ │  └───┘               │   │   │
│   │   └─────────────────────────────────────────────────────┘   │   │
│   │                                                              │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Launch Template vs Launch Configuration

| Feature | Launch Template | Launch Configuration |
|---------|-----------------|---------------------|
| **Versioning** | ✅ Yes | ❌ No |
| **Multiple Instance Types** | ✅ Yes | ❌ No |
| **Spot + On-Demand Mix** | ✅ Yes | ❌ No |
| **T2/T3 Unlimited** | ✅ Yes | ❌ No |
| **Placement Groups** | ✅ Yes | ❌ No |
| **Capacity Reservations** | ✅ Yes | ❌ No |
| **Dedicated Hosts** | ✅ Yes | ❌ No |
| **Recommendation** | ✅ **Use This** | ⚠️ Legacy |

### Scaling Policies

| Policy Type | How It Works | Use Case |
|-------------|--------------|----------|
| **Target Tracking** | Maintain metric at target value | "Keep CPU at 50%" |
| **Step Scaling** | Add/remove based on alarm thresholds | Complex scaling needs |
| **Simple Scaling** | Single adjustment per alarm | Basic scaling |
| **Scheduled Scaling** | Scale at specific times | Predictable patterns |
| **Predictive Scaling** | ML-based forecast | Cyclical traffic |

### Target Tracking Metrics

| Metric | Description | Use Case |
|--------|-------------|----------|
| `ASGAverageCPUUtilization` | Average CPU across ASG | General compute |
| `ASGAverageNetworkIn` | Average network bytes in | Network-bound apps |
| `ASGAverageNetworkOut` | Average network bytes out | Network-bound apps |
| `ALBRequestCountPerTarget` | Requests per target | Web applications |
| **Custom Metric** | Any CloudWatch metric | Application-specific |

### Configuration Rules

| Setting | Details |
|---------|---------|
| **Cooldown Period** | Default 300s, prevents rapid scaling |
| **Warm-up Time** | Time for new instance to contribute to metrics |
| **Health Check Grace** | Time before health checks start (default 300s) |
| **Health Check Type** | EC2 (default) or ELB |
| **Termination Policy** | Which instance to terminate first |
| **Instance Protection** | Protect specific instances from scale-in |
| **Lifecycle Hooks** | Run scripts during launch/terminate |
| **Suspend Processes** | Temporarily disable specific ASG processes |

### Termination Policies (Order of Selection)

```
DEFAULT TERMINATION POLICY:
1. AZ with most instances
2. Instance with oldest launch configuration/template
3. Instance closest to next billing hour
4. Random if tied
```

| Policy | Description |
|--------|-------------|
| `OldestInstance` | Terminate oldest instance |
| `NewestInstance` | Terminate newest instance |
| `OldestLaunchConfiguration` | Terminate with oldest config |
| `OldestLaunchTemplate` | Terminate with oldest template |
| `ClosestToNextInstanceHour` | Minimize billing |
| `Default` | AZ balance → Oldest config → Billing hour |
| `AllocationStrategy` | For mixed instances (Spot) |

### Lifecycle Hooks

```
┌─────────────────────────────────────────────────────────────────────┐
│                    LIFECYCLE HOOKS                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   SCALE OUT                          SCALE IN                        │
│                                                                      │
│   Pending                            InService                       │
│      │                                   │                           │
│      ▼                                   ▼                           │
│   ┌──────────────────┐               ┌──────────────────┐           │
│   │ Pending:Wait     │◀── HOOK       │ Terminating:Wait │◀── HOOK   │
│   │                  │               │                  │           │
│   │ • Register DNS   │               │ • Deregister DNS │           │
│   │ • Pull configs   │               │ • Drain conns    │           │
│   │ • Warm cache     │               │ • Upload logs    │           │
│   └──────────────────┘               └──────────────────┘           │
│      │                                   │                           │
│      ▼ (CONTINUE/ABANDON)                ▼ (CONTINUE/ABANDON)        │
│   ┌──────────────────┐               ┌──────────────────┐           │
│   │ Pending:Proceed  │               │Terminating:Proceed│          │
│   └──────────────────┘               └──────────────────┘           │
│      │                                   │                           │
│      ▼                                   ▼                           │
│   InService                          Terminated                      │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Limits

| Limit | Value |
|-------|-------|
| ASGs per region | 200 |
| Launch configurations per region | 200 |
| Scaling policies per ASG | 50 |
| Scheduled actions per ASG | 125 |
| Lifecycle hooks per ASG | 50 |
| SNS notifications per ASG | 10 |
| Max instances per ASG | 1,000 (can increase) |

### Exam Heuristics

| Keyword/Scenario | Answer |
|------------------|--------|
| Maintain capacity | **Auto Scaling Group** |
| Scale based on CPU | **Target Tracking (ASGAverageCPUUtilization)** |
| Scale based on requests | **Target Tracking (ALBRequestCountPerTarget)** |
| Scale at specific times | **Scheduled Scaling** |
| Predictable traffic patterns | **Predictive Scaling** |
| Complex scaling (multiple thresholds) | **Step Scaling** |
| Run script on launch | **Lifecycle Hook (Pending)** |
| Drain connections before terminate | **Lifecycle Hook (Terminating)** |
| Mix Spot and On-Demand | **Launch Template + Mixed Instances** |
| Blue-green with ASG | **Two ASGs, switch via Load Balancer** |
| Immediate scaling (no cooldown) | **Reduce cooldown period** |
| Health check from LB | **ELB health check type** |

---

## Application Auto Scaling

### Definition
**Application Auto Scaling** allows you to configure automatic scaling for scalable AWS resources beyond EC2, including DynamoDB, ECS, Aurora, and more.

### Supported Services

| Service | Scalable Dimension |
|---------|-------------------|
| **DynamoDB** | Read/Write Capacity Units |
| **ECS** | Service desired count |
| **Aurora** | Aurora Replica count |
| **EMR** | Instance count |
| **AppStream 2.0** | Fleet capacity |
| **Comprehend** | Endpoint throughput |
| **Lambda** | Provisioned concurrency |
| **SageMaker** | Endpoint instances |
| **Spot Fleet** | Target capacity |
| **Custom** | Custom resource |

### Exam Heuristics

| Keyword/Scenario | Answer |
|------------------|--------|
| Scale DynamoDB automatically | **Application Auto Scaling** |
| Scale ECS service | **Application Auto Scaling** |
| Scale Aurora replicas | **Application Auto Scaling** |
| Scale Lambda concurrency | **Application Auto Scaling** |

---

# 🏥 HEALTH CHECKS

## Health Check Types

| Type | Used By | Checks |
|------|---------|--------|
| **EC2 Health Check** | Auto Scaling | Instance status (running) |
| **ELB Health Check** | ELB, ASG | HTTP response, TCP connection |
| **Route 53 Health Check** | Route 53 | Endpoint, calculated, CloudWatch alarm |
| **Custom Health Check** | ASG API | Application-defined |

## Route 53 Health Checks

### Types

| Type | Description | Use Case |
|------|-------------|----------|
| **Endpoint** | Monitor IP or domain | Direct resource monitoring |
| **Calculated** | Combine other health checks | Complex availability logic |
| **CloudWatch Alarm** | Based on CloudWatch alarm state | Metric-based health |

### Configuration

| Setting | Options |
|---------|---------|
| **Protocol** | HTTP, HTTPS, TCP |
| **Port** | 1-65535 |
| **Path** | /health, /status (for HTTP/HTTPS) |
| **Request Interval** | 10s or 30s |
| **Failure Threshold** | 1-10 (consecutive failures) |
| **String Matching** | Check response body (first 5,120 bytes) |
| **Latency Graphs** | Enable/disable |
| **Invert Health Check** | Healthy ↔ Unhealthy |
| **Regions** | Choose health check locations |

### Exam Heuristics

| Keyword/Scenario | Answer |
|------------------|--------|
| DNS failover | **Route 53 Health Check + Failover routing** |
| Monitor endpoint from multiple regions | **Route 53 Endpoint Health Check** |
| Health based on CloudWatch metric | **Route 53 CloudWatch Alarm Health Check** |
| Combine multiple health checks | **Route 53 Calculated Health Check** |
| Health check private resources | **CloudWatch Alarm Health Check** (not direct) |

---

# 🌐 ROUTE 53 (DNS)

## Definition
**Amazon Route 53** is a highly available and scalable Domain Name System (DNS) web service that provides domain registration, DNS routing, and health checking.

## Routing Policies

| Policy | Description | Use Case | Health Check |
|--------|-------------|----------|--------------|
| **Simple** | Single resource | Basic DNS | No |
| **Weighted** | Distribute by percentage | Load distribution, testing | Yes |
| **Latency** | Route to lowest latency region | Global apps | Yes |
| **Failover** | Active-passive failover | DR | Yes (required) |
| **Geolocation** | Route by user location | Content localization | Yes |
| **Geoproximity** | Route by distance + bias | Traffic shifting | Yes |
| **Multi-Value** | Multiple healthy records | Simple LB (up to 8) | Yes |
| **IP-based** | Route by client IP CIDR | ISP-specific routing | Yes |

## Routing Policy Details

### Failover Routing

```
┌─────────────────────────────────────────────────────────────────────┐
│                    FAILOVER ROUTING                                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   DNS Query: app.example.com                                         │
│                    │                                                 │
│                    ▼                                                 │
│            ┌──────────────┐                                         │
│            │   Route 53   │                                         │
│            │              │                                         │
│            │ Health Check │                                         │
│            └──────┬───────┘                                         │
│                   │                                                  │
│          ┌───────┴───────┐                                          │
│          │               │                                          │
│          ▼               ▼                                          │
│   ┌─────────────┐ ┌─────────────┐                                   │
│   │   PRIMARY   │ │  SECONDARY  │                                   │
│   │ us-east-1   │ │ us-west-2   │                                   │
│   │  (Active)   │ │  (Standby)  │                                   │
│   └─────────────┘ └─────────────┘                                   │
│                                                                      │
│   IF Primary healthy → Return Primary IP                            │
│   IF Primary unhealthy → Return Secondary IP                        │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Weighted Routing

```
┌─────────────────────────────────────────────────────────────────────┐
│                    WEIGHTED ROUTING                                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   DNS Query: app.example.com                                         │
│                    │                                                 │
│                    ▼                                                 │
│            ┌──────────────┐                                         │
│            │   Route 53   │                                         │
│            │              │                                         │
│            │ Weight: 70   │────────▶ Server A (70% traffic)         │
│            │ Weight: 20   │────────▶ Server B (20% traffic)         │
│            │ Weight: 10   │────────▶ Server C (10% traffic)         │
│            └──────────────┘                                         │
│                                                                      │
│   Use Cases:                                                         │
│   • Blue-green deployment (90/10 then 50/50 then 10/90)             │
│   • A/B testing                                                      │
│   • Load balancing across regions                                    │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Latency Routing

```
┌─────────────────────────────────────────────────────────────────────┐
│                    LATENCY ROUTING                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   User in Europe                    User in Asia                     │
│        │                                 │                           │
│        ▼                                 ▼                           │
│   ┌──────────┐                      ┌──────────┐                    │
│   │ Route 53 │                      │ Route 53 │                    │
│   │          │                      │          │                    │
│   │ Lowest   │                      │ Lowest   │                    │
│   │ latency  │                      │ latency  │                    │
│   └────┬─────┘                      └────┬─────┘                    │
│        │                                 │                           │
│        ▼                                 ▼                           │
│   eu-west-1                         ap-southeast-1                   │
│   (Frankfurt)                       (Singapore)                      │
│                                                                      │
│   Returns region with lowest network latency to user                │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Geolocation vs Geoproximity

| Feature | Geolocation | Geoproximity |
|---------|-------------|--------------|
| **Based On** | User's location (country/continent) | Distance to resource |
| **Bias** | No | Yes (-99 to +99) |
| **Use Case** | Content restrictions, localization | Shift traffic between regions |
| **Default Record** | Required for non-matching locations | N/A |
| **Traffic Flow** | Not required | Required |

### Exam Heuristics

| Keyword/Scenario | Answer |
|------------------|--------|
| Active-passive failover | **Failover routing** |
| Blue-green deployment (DNS) | **Weighted routing** |
| Route to closest region | **Latency routing** |
| Route by country | **Geolocation routing** |
| Content localization | **Geolocation routing** |
| Shift traffic gradually | **Geoproximity + Traffic Flow** |
| Simple load balancing (DNS) | **Multi-Value routing** |
| 100% SLA | **Route 53** (only AWS service) |

---

## Record Types

| Type | Description | Use Case |
|------|-------------|----------|
| **A** | IPv4 address | Map domain to IP |
| **AAAA** | IPv6 address | Map domain to IPv6 |
| **CNAME** | Canonical name | Alias to another domain (not apex) |
| **Alias** | AWS resource | Map to ALB, CloudFront, S3, etc. (free, apex OK) |
| **MX** | Mail exchange | Email routing |
| **TXT** | Text record | Verification, SPF |
| **NS** | Name server | Delegation |
| **SOA** | Start of authority | Zone metadata |
| **SRV** | Service record | Service location |

### Alias vs CNAME

| Feature | Alias | CNAME |
|---------|-------|-------|
| **Zone Apex** | ✅ Yes | ❌ No |
| **Cost** | Free | Per query charge |
| **AWS Resources** | ✅ Native support | Requires IP |
| **Health Checks** | ✅ Yes | ✅ Yes |
| **TTL** | Inherited from target | Configurable |

### Exam Heuristics

| Keyword/Scenario | Answer |
|------------------|--------|
| Point apex domain to ALB | **Alias record** |
| Point subdomain to another domain | **CNAME** |
| Point to CloudFront | **Alias record** |
| Point to S3 website | **Alias record** |
| Free DNS queries | **Alias record** |

---

# 📊 AVAILABILITY & RELIABILITY CONCEPTS

## Availability Targets

| Availability | Downtime/Year | Downtime/Month | Downtime/Week |
|--------------|---------------|----------------|---------------|
| 99% | 3.65 days | 7.31 hours | 1.68 hours |
| 99.9% | 8.77 hours | 43.83 minutes | 10.08 minutes |
| 99.95% | 4.38 hours | 21.92 minutes | 5.04 minutes |
| 99.99% | 52.60 minutes | 4.38 minutes | 1.01 minutes |
| 99.999% | 5.26 minutes | 26.30 seconds | 6.05 seconds |

## Multi-AZ Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                    MULTI-AZ HIGH AVAILABILITY                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   Region: us-east-1                                                  │
│                                                                      │
│   ┌─────────────────────────────────────────────────────────────┐   │
│   │                    Application Load Balancer                 │   │
│   │                    (Cross-zone enabled)                      │   │
│   └─────────────────────────────────────────────────────────────┘   │
│                              │                                       │
│         ┌────────────────────┼────────────────────┐                 │
│         ▼                    ▼                    ▼                 │
│   ┌───────────┐        ┌───────────┐        ┌───────────┐          │
│   │   AZ-a    │        │   AZ-b    │        │   AZ-c    │          │
│   │           │        │           │        │           │          │
│   │  ┌─────┐  │        │  ┌─────┐  │        │  ┌─────┐  │          │
│   │  │ EC2 │  │        │  │ EC2 │  │        │  │ EC2 │  │          │
│   │  │ ASG │  │        │  │ ASG │  │        │  │ ASG │  │          │
│   │  └─────┘  │        │  └─────┘  │        │  └─────┘  │          │
│   │           │        │           │        │           │          │
│   │  ┌─────┐  │        │  ┌─────┐  │        │           │          │
│   │  │ RDS │◀─┼────────┼──│ RDS │  │        │  (Read    │          │
│   │  │Primary│        │  │Standby│  │        │  Replica) │          │
│   │  └─────┘  │        │  └─────┘  │        │           │          │
│   │           │        │           │        │           │          │
│   │  ┌─────┐  │        │  ┌─────┐  │        │  ┌─────┐  │          │
│   │  │Cache│  │        │  │Cache│  │        │  │Cache│  │          │
│   │  │Node │  │        │  │Node │  │        │  │Node │  │          │
│   │  └─────┘  │        │  └─────┘  │        │  └─────┘  │          │
│   └───────────┘        └───────────┘        └───────────┘          │
│                                                                      │
│   SERVICES WITH MULTI-AZ:                                           │
│   ✓ RDS Multi-AZ (synchronous)                                      │
│   ✓ Aurora (3 AZs, 6 copies)                                        │
│   ✓ ElastiCache Redis (cluster mode)                                │
│   ✓ EFS (automatically)                                             │
│   ✓ S3 (automatically)                                              │
│   ✓ DynamoDB (automatically)                                        │
│   ✓ NAT Gateway (per AZ)                                            │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

## Fault Tolerance vs High Availability

| Concept | Definition | Example |
|---------|------------|---------|
| **High Availability** | System remains operational | Multi-AZ with failover |
| **Fault Tolerance** | System continues without degradation | Redundant components |
| **Disaster Recovery** | Restore after catastrophic failure | Cross-region backup/replication |

---

## ✅ EXAM HEURISTICS - HIGH AVAILABILITY

| Keyword/Scenario | Answer |
|------------------|--------|
| High availability web tier | **ALB + Auto Scaling across AZs** |
| High availability database | **RDS Multi-AZ or Aurora** |
| High availability cache | **ElastiCache Redis Cluster Mode** |
| High availability NAT | **NAT Gateway per AZ** |
| High availability DNS | **Route 53** (100% SLA) |
| Automatic failover (DB) | **RDS Multi-AZ** (automatic) |
| Read scaling (DB) | **Read Replicas** |
| Zero downtime deployment | **Blue-green with ALB weighted target groups** |
| Minimize single points of failure | **Multi-AZ + redundant components** |
| Session persistence across instances | **ElastiCache or DynamoDB for sessions** |

---

*Continued in Part 2: Decoupling & Disaster Recovery*

---

*Last Updated: December 2025*
*Exam Version: SAA-C03*
*Domain: Design Resilient Architectures (26%)*
