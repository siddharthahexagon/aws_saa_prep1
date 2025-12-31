# Configuration Questionnaire: Hybrid & Migration
## Step-by-Step Hybrid Cloud and Migration Configurations

---

## 📋 Table of Contents
1. [On-Premises to AWS Migration](#on-premises-to-aws-migration)
2. [Hybrid Cloud Architecture](#hybrid-cloud-architecture)
3. [Disaster Recovery Configurations](#disaster-recovery-configurations)
4. [Data Transfer Strategies](#data-transfer-strategies)
5. [VMware Cloud Integration](#vmware-cloud-integration)
6. [Active Directory Integration](#active-directory-integration)

---

## 🚀 SCENARIO 1: On-Premises to AWS Migration

### Initial Questions:
```
Q1: What type of workload are you migrating?
    ├─ Servers/VMs → Application Migration Service
    ├─ Databases → Database Migration Service
    ├─ Large data → Snowball/DataSync
    └─ VMware → VMware Cloud on AWS

Q2: What is the migration strategy?
    ├─ Rehost (Lift & Shift) → Minimal changes
    ├─ Replatform → Some optimization
    ├─ Refactor → Redesign for cloud
    └─ Repurchase → SaaS replacement

Q3: What is the acceptable downtime?
    ├─ None → Live migration with DMS/MGN
    ├─ < 1 hour → Coordinated cutover
    └─ Weekend → Full migration window

Q4: What is the data volume?
    ├─ < 10 TB → Direct upload or DataSync
    ├─ 10-80 TB → Snowball Edge
    └─ > 80 TB → Multiple Snowballs or Snowmobile
```

### Configuration Matrix:

#### **Complete Enterprise Migration (Lift & Shift)**
```
Services Required:
Phase 1: Discovery & Planning
├─ Application Discovery Service
├─ Migration Hub
├─ Migration Evaluator
└─ AWS Config

Phase 2: Migration Execution
├─ Application Migration Service (MGN)
├─ Database Migration Service (DMS)
├─ DataSync or Snowball
├─ Direct Connect or VPN
├─ CloudEndure (alternative)
└─ Server Migration Service (SMS)

Phase 3: Optimization
├─ Compute Optimizer
├─ Cost Explorer
├─ Trusted Advisor
└─ Systems Manager

Configuration Steps:

[Phase 1: Discovery & Assessment - Week 1-2]

1. Install Discovery Agent:
   - Download agent from Migration Hub
   - Install on all on-premises servers
   - Collects:
     • System performance data
     • Resource utilization
     • Network dependencies
     • Process information
   - Data sent to AWS every 15 minutes
   
2. Migration Hub Setup:
   - Central tracking console
   - Create migration project: "Enterprise-Migration-2025"
   - Group applications:
     a. Web Application Stack
        - Web servers (3)
        - Application servers (5)
        - Database servers (2)
     b. Internal Tools
        - File servers (2)
        - Application servers (3)
   
3. AWS Migration Evaluator:
   - Upload inventory data
   - Get cost estimates:
     • Current on-premises cost
     • Projected AWS cost (3 scenarios)
     • Break-even analysis
   - Business case report
   
4. Dependency Mapping:
   - Identify dependencies between servers
   - Network traffic patterns
   - Group servers by application
   - Plan migration waves:
     • Wave 1: Dev/Test environment
     • Wave 2: Internal applications
     • Wave 3: Production web app
     • Wave 4: Critical databases

[Phase 2: Network Connectivity - Week 2-3]

5. Establish Connectivity:
   
   Option A: AWS Direct Connect (Recommended)
   - Order 1 Gbps or 10 Gbps connection
   - Lead time: 2-4 weeks
   - Dedicated fiber from on-prem to AWS
   - BGP routing configuration
   - Redundant connections (2x) for HA
   
   Configuration:
   - Virtual Interface (VIF) types:
     • Private VIF: Access VPC resources
     • Public VIF: Access S3, DynamoDB
   - BGP ASN: 65000 (on-prem), 64512 (AWS)
   - VLAN: 100
   - Peer IPs: 169.254.0.1/30
   
   Option B: Site-to-Site VPN (Faster Setup)
   - Virtual Private Gateway on AWS side
   - Customer Gateway (on-prem router)
   - Two IPSec tunnels for redundancy
   - Setup time: Hours
   - Bandwidth: Up to 1.25 Gbps per tunnel
   
6. VPC Configuration:
   - Create Migration VPC:
     • CIDR: 10.1.0.0/16
     • Public subnets: 10.1.1.0/24, 10.1.2.0/24
     • Private subnets: 10.1.10.0/24, 10.1.11.0/24
     • Database subnets: 10.1.20.0/24, 10.1.21.0/24
   - Route propagation:
     • On-prem CIDR (192.168.0.0/16) via Direct Connect/VPN
   - DNS:
     • Route 53 Resolver endpoints
     • Hybrid DNS resolution

[Phase 3: Database Migration - Week 3-5]

7. Assess Database Compatibility:
   - Schema Conversion Tool (SCT):
     • Connect to source database
     • Generate assessment report
     • Identify compatibility issues
     • Create conversion roadmap
   
8. Create Target RDS/Aurora:
   - Engine: PostgreSQL (if migrating from Oracle)
   - Instance: db.r6g.xlarge
   - Multi-AZ: YES
   - Storage: Encrypted with KMS
   - Subnet: Private database subnet
   - Security group: Allow 5432 from on-prem + app tier
   - Backup: 7 days retention
   
9. DMS Replication Instance:
   - Instance class: dms.c5.xlarge
   - Multi-AZ: YES
   - VPC: Migration VPC
   - Storage: 500 GB (auto-expands)
   - Subnet: Private subnet
   - Security group:
     • Allow from source DB
     • Allow to target DB
   
10. Create DMS Endpoints:
    a. Source Endpoint:
       - Type: Oracle
       - Server: on-prem-db.company.com
       - Port: 1521
       - SSL: Optional
       - Test connection via replication instance
       
    b. Target Endpoint:
       - Type: Aurora PostgreSQL
       - Server: aurora-cluster.us-east-1.rds.amazonaws.com
       - Port: 5432
       - SSL: Required
       - Test connection
   
11. Create DMS Task:
    - Name: "oracle-to-aurora-full-load-cdc"
    - Migration type: Full load + ongoing replication (CDC)
    - Table mappings:
      ```json
      {
        "rules": [
          {
            "rule-type": "selection",
            "rule-id": "1",
            "rule-name": "include-all-tables",
            "object-locator": {
              "schema-name": "PROD",
              "table-name": "%"
            },
            "rule-action": "include"
          },
          {
            "rule-type": "transformation",
            "rule-id": "2",
            "rule-name": "convert-lowercase",
            "rule-action": "convert-lowercase",
            "rule-target": "schema"
          }
        ]
      }
      ```
    - Validation: Enabled
    - CloudWatch logs: Enabled
    - Start task immediately
    
12. Monitor Migration:
    - Full load progress: Watch table statistics
    - CDC lag: Should be < 10 seconds
    - Validation: Compare row counts
    - Expected duration: Hours to days (depends on size)
    
13. Application Testing:
    - Update connection string to Aurora
    - Run integration tests
    - Performance testing
    - Data validation queries

[Phase 4: Server Migration - Week 4-8]

14. Application Migration Service (MGN) Setup:
    - Create replication settings template:
      • Staging area: Migration VPC private subnets
      • EBS encryption: Enabled
      • Instance type: t3.small (for replication)
      • Replication server security group
    
15. Install MGN Agent:
    - On each source server to migrate
    - Agent continuously replicates:
      • Block-level replication
      • All attached volumes
      • Minimal performance impact (< 5%)
    - Initial sync: Full disk copy
    - Ongoing: Changed blocks only
    
16. Configure Launch Settings:
    a. For Web Server:
       - Instance type: t3.large
       - Subnet: Private subnet
       - Security groups: Web-SG
       - IAM role: WebServerRole
       - User data: Install CloudWatch agent
       - Tags: Environment=Production
       
    b. For Application Server:
       - Instance type: m5.xlarge
       - Similar network/security settings
    
17. Test Migration (Non-Disruptive):
    - Launch "Test" instance in AWS
    - Validate:
      • Instance boots successfully
      • All volumes attached
      • Network connectivity
      • Application functionality
    - Delete test instance after validation
    - Repeat for all servers
    
18. Plan Cutover:
    - Schedule: Friday 11 PM - Sunday 6 AM
    - Checklist:
      ✅ All servers at 100% replication
      ✅ Test migrations successful
      ✅ DNS records prepared
      ✅ Rollback plan documented
      ✅ Team on standby
      ✅ Customer notification sent
    
19. Execute Cutover:
    a. Stop source servers (maintenance mode)
    b. Wait for final sync (5-15 minutes)
    c. Launch cutover instances in AWS
    d. Verify all instances running
    e. Update DNS records or load balancer
    f. Test application endpoints
    g. Monitor for 2-4 hours
    h. If successful: Decommission source servers
       If issues: Rollback to source

[Phase 5: Data Migration - Week 3-6]

20. Assess Data Transfer Method:
    - Total data: 50 TB
    - Network bandwidth: 100 Mbps
    - Calculation:
      • 50 TB = 50,000 GB = 400,000 Gb
      • At 100 Mbps = 400,000/100 = 4,000 hours = 167 days
      • Too slow! Use Snowball
    
21. Order AWS Snowball Edge:
    - Device: Snowball Edge Storage Optimized
    - Capacity: 80 TB usable
    - Quantity: 1 device
    - Shipping: 3-5 days
    - Encryption: 256-bit AES
    
22. Snowball Data Transfer:
    - Receive device
    - Connect to local network
    - Power on and unlock (via Snowball client)
    - Copy data:
      ```bash
      # Install Snowball client
      ./snowball-client configure
      
      # Copy data
      ./snowball-client cp -r /mnt/fileserver/ s3://mybucket/
      ```
    - Monitor progress
    - Complete copy verification
    - Ship back to AWS (2-3 days)
    
23. AWS Receives Snowball:
    - Data transferred to S3 bucket
    - Notification via SNS
    - Verify all data present
    - Device securely erased (NIST standards)
    - Duration: 1-2 days after receipt
    
24. Alternative: AWS DataSync (For Smaller Datasets):
    - Install DataSync agent on-premises
    - Create task:
      • Source: NFS/SMB share
      • Destination: S3 or EFS
      • Schedule: One-time or recurring
      • Bandwidth throttle: Optional
    - Execute task
    - Incremental transfers supported

[Phase 6: Post-Migration Optimization - Week 8-12]

25. Right-Sizing:
    - Use Compute Optimizer:
      • Analyze CloudWatch metrics (14 days)
      • Recommendations for instance types
      • Potential savings: 20-40%
    - Example:
      • Current: m5.2xlarge (8 vCPUs, 32 GB)
      • Recommended: m5.xlarge (4 vCPUs, 16 GB)
      • Savings: 50% on that instance
    
26. Cost Optimization:
    - Purchase Reserved Instances (1-3 years):
      • Savings: 30-75% vs On-Demand
    - Use Savings Plans for flexible workloads
    - Enable S3 Lifecycle policies:
      • Move to IA after 30 days
      • Move to Glacier after 90 days
    - Delete unused EBS snapshots
    - Remove orphaned EBS volumes
    
27. Security Hardening:
    - Enable AWS Config rules:
      • Encrypted EBS volumes
      • S3 bucket public access
      • Security group rules
    - AWS Security Hub:
      • Centralized findings
      • CIS benchmarks
    - GuardDuty: Threat detection
    - Patch Management: Systems Manager Patch Manager
    
28. Monitoring & Alerting:
    - CloudWatch dashboards:
      • EC2 CPU/Memory/Disk/Network
      • RDS connections/CPU/storage
      • Application metrics
    - SNS topics for alarms:
      • Critical: PagerDuty
      • Warning: Email
    - Log aggregation:
      • CloudWatch Logs
      • Centralized log analysis
    
29. Backup Strategy:
    - AWS Backup:
      • Daily EC2 snapshots (retain 7 days)
      • Daily RDS snapshots (retain 30 days)
      • Weekly for long-term (retain 1 year)
    - Cross-region replication:
      • For disaster recovery
      • Secondary region: us-west-2
    
30. Documentation:
    - Update architecture diagrams
    - Document runbooks
    - Create disaster recovery plan
    - Knowledge transfer to ops team

[Phase 7: Decommission On-Premises - Week 12+]

31. Validation Period (4 weeks):
    - Monitor AWS environment
    - Ensure stability
    - Performance meets expectations
    - Cost within budget
    - No rollback needed
    
32. Decommission Process:
    - Stop on-premises servers
    - Cancel maintenance contracts
    - Reclaim licenses
    - Data center space freed
    - Calculate savings

Migration Timeline Summary:
- Week 1-2: Discovery & Planning
- Week 2-3: Network setup
- Week 3-5: Database migration
- Week 4-8: Server migration (overlaps with DB)
- Week 3-6: Data transfer (parallel)
- Week 8-12: Optimization
- Week 12+: Decommission on-prem
Total: 3-4 months for complete migration

Cost Estimate:
- Direct Connect: $0.30/hour + data transfer
- DMS replication instance: ~$300/month × 2 months
- MGN replication: ~$500 for 100 servers
- Snowball: ~$250 per device
- Target infrastructure: $2,000-5,000/month (depends on size)
- Migration services: $500-1,000 one-time

ROI:
- On-premises costs eliminated: $10,000/month
- AWS costs: $3,000/month
- Net savings: $7,000/month = $84,000/year
- Migration costs: $5,000
- Break-even: < 1 month

Success Metrics:
✅ Zero data loss
✅ Downtime < 4 hours
✅ All applications functional
✅ Performance equal or better
✅ Cost reduction: 30%+
```

---

## 🔗 SCENARIO 2: Hybrid Cloud Architecture

### Configuration Matrix:

#### **Production Hybrid Environment**
```
Services Required:
├─ Direct Connect (Primary connectivity)
├─ VPN (Backup connectivity)
├─ Storage Gateway (File/Volume/Tape)
├─ Outposts (AWS on-premises)
├─ Active Directory Connector
├─ Route 53 Resolver (Hybrid DNS)
├─ Transit Gateway (Network hub)
└─ DataSync (File synchronization)

Configuration Steps:

[Step 1: Network Connectivity]
1. Direct Connect:
   - Port speed: 10 Gbps
   - Location: Equinix DC
   - Virtual Interfaces:
     a. Private VIF → Access VPCs
     b. Transit VIF → Access Transit Gateway
   - LAG (Link Aggregation): 2×10 Gbps for redundancy
   
2. Backup VPN:
   - Site-to-site VPN over internet
   - Two tunnels for HA
   - Automatic failover if Direct Connect down
   - BGP routing with lower priority
   
3. Transit Gateway:
   - Central routing hub
   - Connect multiple VPCs
   - Connect on-premises via Direct Connect
   - Routing tables:
     • Production VPC routes
     • On-premises routes (192.168.0.0/16)
     • Isolated routes per environment

[Step 2: Hybrid Storage]
4. AWS Storage Gateway:
   
   Option A: File Gateway (NFS/SMB)
   - Deploy gateway VM on-premises
   - Local cache: 1 TB SSD
   - Backend: S3 bucket
   - Use case: File shares with cloud storage
   - Files cached locally, stored in S3
   
   Option B: Volume Gateway (iSCSI)
   - Block storage
   - Cached mode: Frequent data cached
   - Stored mode: Full copy local + S3
   - Use case: Database volumes, VMware datastores
   
   Option C: Tape Gateway (VTL)
   - Virtual tape library
   - Backup software compatible
   - Archives to S3 Glacier
   - Use case: Backup/archive replacement

[Step 3: Hybrid Identity]
5. AWS Directory Service:
   
   Option A: AD Connector
   - Proxy to on-premises Active Directory
   - No data sync, real-time authentication
   - Users/groups remain on-premises
   - Use case: Extend AD to AWS
   
   Option B: Managed Microsoft AD
   - Standalone AD in AWS
   - Trust relationship with on-prem AD
   - Supports AD-aware workloads
   - Use case: AWS-first applications
   
6. Single Sign-On (SSO):
   - IAM Identity Center
   - SAML federation with on-prem AD
   - Access AWS Console and applications
   - MFA supported

[Step 4: Hybrid DNS]
7. Route 53 Resolver:
   - Inbound endpoint: On-prem queries AWS
   - Outbound endpoint: AWS queries on-prem
   - Conditional forwarding rules:
     • company.local → On-prem DNS
     • aws.company.com → Route 53
   - Resolves across hybrid environment

[Step 5: Application Integration]
8. Hybrid Workload Patterns:
   
   Pattern 1: Burst to Cloud
   - Primary compute on-premises
   - Overflow to AWS during peak
   - Auto Scaling in AWS
   
   Pattern 2: Disaster Recovery
   - Production on-premises
   - Standby in AWS (pilot light)
   - Failover to AWS if disaster
   
   Pattern 3: Data Processing
   - Data generated on-premises
   - Synced to S3 via DataSync
   - Processed in AWS (EMR, Glue)
   - Results returned

[Step 6: Monitoring]
9. Unified Monitoring:
   - CloudWatch for AWS resources
   - CloudWatch Agent on on-prem servers
   - Systems Manager for hybrid fleet
   - Centralized dashboard

Cost: $2,000-10,000/month (depends on connectivity)
```

---

## 🛡️ SCENARIO 3: Disaster Recovery Configurations

### Configuration Matrix:

#### **Multi-Tier DR Strategy**
```
DR Strategies (by RTO/RPO):

1. Backup & Restore (Lowest Cost)
   - RTO: 24 hours
   - RPO: 24 hours
   - Cost: $
   - Method: Backup to S3, restore when needed

2. Pilot Light (Balanced)
   - RTO: Hours
   - RPO: Minutes
   - Cost: $$
   - Method: Minimal infrastructure running, scale up in DR

3. Warm Standby (Faster)
   - RTO: Minutes
   - RPO: Seconds
   - Cost: $$$
   - Method: Scaled-down version always running

4. Multi-Site Active-Active (Fastest)
   - RTO: Seconds
   - RPO: None (real-time)
   - Cost: $$$$
   - Method: Full infrastructure in multiple regions

Configuration for Pilot Light:

[Primary Region: us-east-1]
1. Production Environment:
   - Web tier: Auto Scaling (3-20 instances)
   - App tier: Auto Scaling (5-30 instances)
   - Database: Aurora MySQL cluster
   - Storage: S3, EFS
   
2. Replication:
   - Aurora: Global Database to us-west-2
   - S3: Cross-Region Replication
   - EFS: AWS Backup to us-west-2

[DR Region: us-west-2]
3. Pilot Light Setup:
   - VPC: Pre-configured (matching primary)
   - AMIs: Copied from primary region
   - Launch configurations: Pre-created
   - Load balancers: Pre-configured (no targets)
   - Auto Scaling groups: Min=0, Max=20
   - Aurora: Read-only replica running
   - S3: Replicated bucket (read-only)
   
4. DR Runbook:
   a. Detection (automated):
      - Route 53 health checks fail
      - CloudWatch alarm triggers
   b. Activation (semi-automated):
      - Lambda promotes Aurora replica to primary
      - Auto Scaling groups scale up (0 → desired capacity)
      - Update Route 53 to point to DR region
   c. Validation:
      - Test application endpoints
      - Verify database connectivity
      - Monitor for errors
   d. Communication:
      - SNS notification to team
      - Status page updated
   e. Timeline:
      - Detection: 2 minutes
      - Activation: 10 minutes
      - Validation: 5 minutes
      - Total RTO: ~20 minutes
   
5. Failback (when primary recovered):
   - Reverse replication (us-west-2 → us-east-1)
   - Validate primary region health
   - Planned cutover during low traffic
   - Update Route 53 back to primary

Cost:
- Pilot Light: 25-30% of primary region cost
- Warm Standby: 50-60% of primary region cost
- Active-Active: 200% of single region cost

Testing:
- Quarterly DR drill (full failover)
- Monthly validation (partial failover)
- Continuous health checks
```

---

## 📦 SCENARIO 4: Data Transfer Strategies

### Decision Matrix:

```
Data Transfer Decision Tree:

Data Volume < 10 TB?
├─ YES → Fast internet connection?
│   ├─ YES (>100 Mbps) → Direct upload to S3 or DataSync
│   └─ NO (<100 Mbps) → Snowball Edge (80 TB device)
└─ NO (>10 TB) → How much data?
    ├─ 10-80 TB → Single Snowball Edge
    ├─ 80 TB-1 PB → Multiple Snowballs
    └─ >1 PB → Snowmobile (100 PB per truck)

Transfer Methods Comparison:

| Method | Speed | Cost | Use Case |
|--------|-------|------|----------|
| Direct Upload | Varies | Free (+ bandwidth) | < 1 TB |
| S3 Transfer Acceleration | 50-500% faster | $0.04/GB | 1-10 TB |
| DataSync | ~10x network speed | $0.0125/GB | 1-100 TB, recurring |
| Snowball Edge | ~1 week total | $300/device | 10-80 TB |
| Snowball (multiple) | Parallel | $300 each | 80 TB-1 PB |
| Snowmobile | Weeks | $0.005/GB/month | > 1 PB |

Configuration for DataSync:

1. Install Agent (on-premises):
   - VM or hardware appliance
   - Connect to NFS/SMB share
   - 1 Gbps+ network to AWS
   
2. Create Task:
   - Source: NFS share (10.0.1.50:/data)
   - Destination: s3://company-data-lake/
   - Options:
     • Verify data: Enabled
     • Bandwidth throttle: 800 Mbps (leave 200 for other traffic)
     • Schedule: Daily at 2 AM
     • Transfer mode: Changed files only
   
3. Execute:
   - Initial: Full transfer (may take days)
   - Subsequent: Incremental (much faster)
   - Parallel transfers (10 files simultaneously)
   
4. Monitoring:
   - CloudWatch metrics
   - Data transferred
   - Files copied
   - Errors/retries

Cost for 50 TB DataSync:
- Data transfer out: 50 TB × $0.09/GB = $4,500
- DataSync fee: 50 TB × $0.0125/GB = $625
- Total: ~$5,125

Cost for Snowball Edge:
- Device: $300 (10 days)
- Shipping: ~$100 roundtrip
- Data transfer: FREE
- Total: ~$400
- Clear winner for large one-time transfers!
```

---

## ☁️ SCENARIO 5-6: Summaries

### VMware Cloud on AWS
- Extend on-premises VMware to AWS
- Same VMware tools (vCenter, vSphere)
- Dedicated hardware (no noisy neighbors)
- Hybrid connectivity (Direct Connect)
- Use cases: Migration, DR, data center extension
- Cost: $8.54/hour per host (i3en.metal)

### Active Directory Integration
- AD Connector vs Managed Microsoft AD
- Trust relationships
- SAML federation for AWS Console
- IAM Identity Center (SSO)
- Group-based IAM policies
- Seamless authentication across hybrid

---

## 📊 Quick Reference

| **Scenario** | **Best Service** | **Duration** | **Complexity** |
|--------------|------------------|--------------|----------------|
| Server migration | MGN | 1-2 months | Medium |
| Database migration | DMS | 2-4 weeks | Medium |
| Large data transfer | Snowball | 1-2 weeks | Low |
| Hybrid connectivity | Direct Connect | 2-4 weeks setup | High |
| Disaster recovery | Multi-region | 1-2 months | High |
| File sync | DataSync | Days | Low |
| VMware migration | VMware Cloud | Weeks | Medium |

---

**Next**: See `26-Config-Security-Compliance.md` for security configurations
