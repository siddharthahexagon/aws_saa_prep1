# AWS SAA-C03: DESIGN COST-OPTIMIZED ARCHITECTURES - PART 2

## 📋 STORAGE COST OPTIMIZATION

---

# 📦 S3 STORAGE CLASSES

## Definition
**S3 Storage Classes** provide different tiers of storage with varying costs, availability, and retrieval characteristics to optimize costs based on access patterns.

## Complete Storage Class Reference

| Storage Class | Use Case | Availability | Min Storage | Retrieval Fee |
|---------------|----------|--------------|-------------|---------------|
| **S3 Standard** | Frequently accessed | 99.99% | None | None |
| **S3 Intelligent-Tiering** | Unknown/changing patterns | 99.9% | None | None (monitoring fee) |
| **S3 Standard-IA** | Infrequent, rapid access | 99.9% | 30 days | Per GB |
| **S3 One Zone-IA** | Infrequent, recreatable | 99.5% | 30 days | Per GB |
| **S3 Glacier Instant** | Archive, instant access | 99.9% | 90 days | Per GB |
| **S3 Glacier Flexible** | Archive, minutes-hours | 99.99% | 90 days | Per GB + time |
| **S3 Glacier Deep Archive** | Long-term archive | 99.99% | 180 days | Per GB + time |

## Storage Class Decision Tree

```
┌─────────────────────────────────────────────────────────────────────┐
│                    S3 STORAGE CLASS DECISION                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   How often is data accessed?                                        │
│       │                                                              │
│       ├── FREQUENTLY (multiple times/month)                         │
│       │       │                                                      │
│       │       └──▶ S3 STANDARD                                      │
│       │                                                              │
│       ├── UNKNOWN or CHANGING patterns                              │
│       │       │                                                      │
│       │       └──▶ S3 INTELLIGENT-TIERING                           │
│       │            (auto-moves between tiers)                        │
│       │                                                              │
│       ├── INFREQUENTLY (once/month or less)                         │
│       │       │                                                      │
│       │       └── Is data recreatable?                              │
│       │               │                                              │
│       │               ├── YES ──▶ S3 ONE ZONE-IA                    │
│       │               │           (20% cheaper than Standard-IA)     │
│       │               │                                              │
│       │               └── NO ───▶ S3 STANDARD-IA                    │
│       │                           (Multi-AZ durability)              │
│       │                                                              │
│       └── RARELY (Archive)                                          │
│               │                                                      │
│               └── How fast do you need retrieval?                   │
│                       │                                              │
│                       ├── MILLISECONDS ──▶ S3 GLACIER INSTANT       │
│                       │                    RETRIEVAL                 │
│                       │                                              │
│                       ├── MINUTES to HOURS ──▶ S3 GLACIER           │
│                       │                        FLEXIBLE RETRIEVAL    │
│                       │                                              │
│                       └── 12-48 HOURS ──▶ S3 GLACIER                │
│                                           DEEP ARCHIVE               │
│                                           (Cheapest storage)         │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

## S3 Glacier Retrieval Options

### Glacier Flexible Retrieval

| Tier | Time | Cost |
|------|------|------|
| **Expedited** | 1-5 minutes | Highest |
| **Standard** | 3-5 hours | Medium |
| **Bulk** | 5-12 hours | Lowest |

### Glacier Deep Archive Retrieval

| Tier | Time | Cost |
|------|------|------|
| **Standard** | 12 hours | Higher |
| **Bulk** | 48 hours | Lowest |

## S3 Intelligent-Tiering

### Definition
**S3 Intelligent-Tiering** automatically moves data between access tiers based on access patterns, optimizing costs without operational overhead.

### How It Works

```
┌─────────────────────────────────────────────────────────────────────┐
│                    S3 INTELLIGENT-TIERING                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   AUTOMATIC TIERS (No retrieval fees):                               │
│                                                                      │
│   ┌─────────────────┐     30 days no access                         │
│   │ Frequent Access │ ─────────────────────▶ ┌──────────────────┐   │
│   │ (Standard cost) │ ◀─────────────────────  │ Infrequent Access│   │
│   └─────────────────┘     Object accessed    │ (40% cheaper)    │   │
│                                              └──────────────────┘   │
│                                                       │              │
│                                                90 days no access     │
│                                                       ▼              │
│                                              ┌──────────────────┐   │
│                                              │ Archive Instant  │   │
│                                              │ (68% cheaper)    │   │
│                                              └──────────────────┘   │
│                                                       │              │
│                           OPTIONAL TIERS:     90+ days no access    │
│                                                       ▼              │
│                                              ┌──────────────────┐   │
│                                              │ Archive Access   │   │
│                                              │ (71% cheaper)    │   │
│                                              └──────────────────┘   │
│                                                       │              │
│                                              180+ days no access    │
│                                                       ▼              │
│                                              ┌──────────────────┐   │
│                                              │ Deep Archive     │   │
│                                              │ (95% cheaper)    │   │
│                                              └──────────────────┘   │
│                                                                      │
│   COST: $0.0025 per 1,000 objects/month (monitoring fee)            │
│   NOTE: No retrieval fees between automatic tiers                   │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### When to Use Intelligent-Tiering

```
✓ GOOD FOR:
  • Unknown access patterns
  • Variable access patterns
  • Large datasets with mixed access
  • Data lakes
  • Long-lived data

✗ NOT GOOD FOR:
  • Small objects (< 128 KB) - monitoring fee overhead
  • Objects smaller than 30 days
  • Known frequent access (use Standard)
  • Known archive (use Glacier directly)
```

---

## S3 Lifecycle Policies

### Definition
**S3 Lifecycle Policies** automate transitioning objects between storage classes or expiring objects to optimize costs.

### Lifecycle Actions

| Action | Description |
|--------|-------------|
| **Transition** | Move to different storage class |
| **Expiration** | Delete objects after specified time |
| **Abort Multipart** | Clean up incomplete uploads |
| **Noncurrent Version Expiration** | Delete old versions |
| **Noncurrent Version Transition** | Move old versions to cheaper class |

### Transition Constraints

```
┌─────────────────────────────────────────────────────────────────────┐
│                    LIFECYCLE TRANSITION RULES                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   ALLOWED TRANSITIONS (Waterfall):                                   │
│                                                                      │
│   S3 Standard                                                        │
│       │                                                              │
│       ├──▶ S3 Intelligent-Tiering                                   │
│       │       │                                                      │
│       ├──────┼──▶ S3 Standard-IA (min 30 days after creation)       │
│       │       │       │                                              │
│       ├──────┼───────┼──▶ S3 One Zone-IA                            │
│       │       │       │       │                                      │
│       ├──────┼───────┼───────┼──▶ S3 Glacier Instant                │
│       │       │       │       │       │                              │
│       ├──────┼───────┼───────┼───────┼──▶ S3 Glacier Flexible       │
│       │       │       │       │       │       │                      │
│       └──────┴───────┴───────┴───────┴───────┴──▶ Glacier Deep      │
│                                                      Archive         │
│                                                                      │
│   CONSTRAINTS:                                                       │
│   • Cannot transition to S3 Standard                                │
│   • Minimum 30 days in Standard before IA transition                │
│   • Minimum 30 days in IA before Glacier                            │
│   • Minimum 90 days for Glacier Instant Retrieval                   │
│   • Minimum 180 days for Deep Archive                               │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Example Lifecycle Policy

```json
{
  "Rules": [
    {
      "ID": "Move to IA after 30 days",
      "Status": "Enabled",
      "Filter": { "Prefix": "logs/" },
      "Transitions": [
        {
          "Days": 30,
          "StorageClass": "STANDARD_IA"
        },
        {
          "Days": 90,
          "StorageClass": "GLACIER"
        },
        {
          "Days": 365,
          "StorageClass": "DEEP_ARCHIVE"
        }
      ],
      "Expiration": {
        "Days": 2555  // Delete after 7 years
      }
    }
  ]
}
```

---

## S3 Cost Components

### Pricing Factors

| Component | Description |
|-----------|-------------|
| **Storage** | Per GB/month (varies by class) |
| **Requests** | PUT, GET, LIST, etc. |
| **Retrieval** | For IA and Glacier classes |
| **Data Transfer** | Out to internet, other regions |
| **Management** | Inventory, Analytics, Replication |

### Request Pricing (Approximate)

| Operation | Standard | Standard-IA | Glacier |
|-----------|----------|-------------|---------|
| PUT/POST/LIST | $0.005/1K | $0.01/1K | $0.05/1K |
| GET | $0.0004/1K | $0.001/1K | $0.0004/1K |

### Cost Optimization Tips

```
┌─────────────────────────────────────────────────────────────────────┐
│                    S3 COST OPTIMIZATION                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   1. USE LIFECYCLE POLICIES:                                         │
│      • Transition to cheaper classes automatically                  │
│      • Delete objects that are no longer needed                     │
│      • Clean up incomplete multipart uploads                        │
│                                                                      │
│   2. USE INTELLIGENT-TIERING:                                        │
│      • For unknown/changing access patterns                         │
│      • No retrieval fees between automatic tiers                    │
│                                                                      │
│   3. MINIMIZE DATA TRANSFER:                                         │
│      • Use same-region services                                     │
│      • Use VPC endpoints (no data transfer charges)                 │
│      • Use CloudFront for frequent access                           │
│                                                                      │
│   4. USE S3 STORAGE LENS:                                            │
│      • Analyze storage usage                                        │
│      • Identify optimization opportunities                          │
│      • Dashboard for organization-wide visibility                   │
│                                                                      │
│   5. USE S3 INVENTORY:                                               │
│      • List objects for analysis                                    │
│      • Identify old/unused objects                                  │
│                                                                      │
│   6. REQUESTER PAYS:                                                 │
│      • Bucket owner pays storage                                    │
│      • Requester pays data transfer and requests                    │
│      • Use for shared datasets                                      │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

# 💾 EBS COST OPTIMIZATION

## EBS Volume Type Costs

| Volume Type | Cost (approx) | IOPS Cost | Throughput Cost |
|-------------|---------------|-----------|-----------------|
| **gp3** | $0.08/GB/month | $0.005/IOPS (>3000) | $0.04/MB/s (>125) |
| **gp2** | $0.10/GB/month | Included (3 IOPS/GB) | Included |
| **io2** | $0.125/GB/month | $0.065/IOPS | Included |
| **io2 Block Express** | $0.125/GB/month | $0.065/IOPS | Included |
| **st1** | $0.045/GB/month | N/A | Included |
| **sc1** | $0.015/GB/month | N/A | Included |

## EBS Cost Optimization Strategies

```
┌─────────────────────────────────────────────────────────────────────┐
│                    EBS COST OPTIMIZATION                             │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   1. USE GP3 INSTEAD OF GP2:                                         │
│      • gp3 is 20% cheaper than gp2 at baseline                      │
│      • Same 3,000 IOPS baseline                                     │
│      • Provision only needed IOPS/throughput                        │
│                                                                      │
│   2. RIGHT-SIZE VOLUMES:                                             │
│      • Use Compute Optimizer recommendations                        │
│      • Monitor CloudWatch metrics                                   │
│      • Reduce oversized volumes                                     │
│                                                                      │
│   3. DELETE UNUSED VOLUMES:                                          │
│      • Unattached volumes still cost money                          │
│      • Use AWS Config rules to detect                               │
│      • Delete or snapshot and delete                                │
│                                                                      │
│   4. DELETE OLD SNAPSHOTS:                                           │
│      • Snapshots charged per GB/month                               │
│      • Use Data Lifecycle Manager (DLM)                             │
│      • Set retention policies                                       │
│                                                                      │
│   5. USE COLD HDD (SC1) FOR ARCHIVES:                               │
│      • Cheapest EBS option ($0.015/GB)                              │
│      • For infrequently accessed data                               │
│                                                                      │
│   6. USE INSTANCE STORE FOR TEMP DATA:                              │
│      • Free (included with instance)                                │
│      • For scratch data, caches, buffers                            │
│      • Data lost on stop/terminate                                  │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### GP3 vs GP2 Cost Comparison

```
EXAMPLE: 500 GB volume, 6,000 IOPS needed

GP2:
• Need 2,000 GB to get 6,000 IOPS (3 IOPS/GB)
• Cost: 2,000 GB × $0.10 = $200/month

GP3:
• Provision 500 GB + 3,000 extra IOPS
• Storage: 500 GB × $0.08 = $40
• IOPS: 3,000 × $0.005 = $15
• Total: $55/month

SAVINGS: $145/month (72.5% savings!)
```

---

# 📁 EFS COST OPTIMIZATION

## EFS Storage Classes

| Class | Cost (approx) | Use Case |
|-------|---------------|----------|
| **EFS Standard** | $0.30/GB/month | Frequently accessed |
| **EFS Infrequent Access** | $0.016/GB/month | Infrequently accessed |
| **EFS One Zone** | $0.16/GB/month | Single AZ, recreatable |
| **EFS One Zone-IA** | $0.0133/GB/month | Single AZ, infrequent |

## EFS Lifecycle Management

```
┌─────────────────────────────────────────────────────────────────────┐
│                    EFS LIFECYCLE MANAGEMENT                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   AUTOMATIC TIERING:                                                 │
│                                                                      │
│   ┌──────────────────┐   7/14/30/60/90 days    ┌──────────────────┐ │
│   │   EFS Standard   │ ─────────────────────▶  │   EFS IA         │ │
│   │   $0.30/GB       │  no access             │   $0.016/GB      │ │
│   └──────────────────┘ ◀─────────────────────  └──────────────────┘ │
│                           accessed                                   │
│                                                                      │
│   LIFECYCLE POLICIES:                                                │
│   • Transition to IA: 7, 14, 30, 60, or 90 days                    │
│   • Transition to Standard: On first access                         │
│                                                                      │
│   COST SAVINGS:                                                      │
│   • Up to 92% for infrequently accessed files                       │
│   • Retrieval fee applies for IA access                             │
│                                                                      │
│   ONE ZONE OPTION:                                                   │
│   • 47% cheaper than Standard                                       │
│   • Use for dev/test or recreatable data                            │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

# 🗄️ FSx COST OPTIMIZATION

## FSx Pricing Overview

| FSx Type | Storage Cost | Use Case |
|----------|--------------|----------|
| **FSx for Windows** | ~$0.13/GB/month | Windows workloads |
| **FSx for Lustre** | ~$0.14/GB/month | HPC, ML |
| **FSx for NetApp ONTAP** | ~$0.125/GB/month | Enterprise NAS |
| **FSx for OpenZFS** | ~$0.09/GB/month | Linux workloads |

## FSx Cost Tips

```
FSx for Lustre:
• Use S3 integration for data repository
• Choose scratch storage for temporary (cheaper)
• Use persistent only when needed

FSx for Windows:
• Use Single-AZ for non-critical (50% cheaper)
• Right-size throughput capacity

General:
• Delete unused file systems
• Monitor and right-size storage
• Use data compression
```

---

# ✅ STORAGE COST HEURISTICS

## Instant Answer Table

| Keyword/Scenario | Answer |
|------------------|--------|
| Unknown S3 access pattern | **S3 Intelligent-Tiering** |
| Infrequent S3 access, critical | **S3 Standard-IA** |
| Infrequent S3 access, recreatable | **S3 One Zone-IA** |
| Archive, instant retrieval | **S3 Glacier Instant Retrieval** |
| Archive, hours retrieval OK | **S3 Glacier Flexible Retrieval** |
| Long-term archive, 12+ hours OK | **S3 Glacier Deep Archive** |
| Automate S3 transitions | **Lifecycle Policies** |
| Analyze S3 storage | **S3 Storage Lens** |
| Lower EBS cost, same performance | **gp3 instead of gp2** |
| Cheapest EBS | **sc1 (Cold HDD)** |
| Free temporary storage | **Instance Store** |
| EFS infrequent access | **EFS Lifecycle to IA** |
| Cheapest EFS | **One Zone-IA** |
| Delete old EBS snapshots | **Data Lifecycle Manager** |

## Storage Cost Rules

```
┌─────────────────────────────────────────────────────────────────────┐
│                    STORAGE COST RULES                                │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   RULE 1: Match storage class to access pattern                     │
│   • Frequent → Standard                                             │
│   • Infrequent → IA classes                                         │
│   • Archive → Glacier classes                                       │
│                                                                      │
│   RULE 2: Use lifecycle policies for automation                     │
│   • Don't manually manage transitions                               │
│   • Set expiration for temporary data                               │
│                                                                      │
│   RULE 3: Use Intelligent-Tiering when unsure                       │
│   • Automatic cost optimization                                     │
│   • No retrieval fees between auto tiers                            │
│                                                                      │
│   RULE 4: Delete unused resources                                   │
│   • Unattached EBS volumes                                          │
│   • Old snapshots                                                   │
│   • Orphaned objects                                                │
│                                                                      │
│   RULE 5: Use gp3 over gp2                                          │
│   • 20% cheaper at baseline                                         │
│   • More cost-effective provisioning                                │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

*Continued in Part 3: Database & Network Cost Optimization*

---

*Last Updated: December 2025*
*Exam Version: SAA-C03*
*Domain: Design Cost-Optimized Architectures (20%)*
