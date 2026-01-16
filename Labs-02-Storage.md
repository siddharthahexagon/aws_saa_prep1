# AWS SAA-C03 Hands-On Labs: Storage Services

## 📋 Overview
These labs cover S3, EBS, EFS, FSx, and Storage Gateway.

**Estimated Time:** 3-4 hours total  
**Cost Estimate:** $1-3 (if cleaned up promptly)  
**Free Tier Eligible:** Yes (most labs)

---

## Lab 2.1: Amazon S3 Fundamentals

### Objectives
- Create and configure S3 buckets
- Implement versioning and lifecycle policies
- Configure bucket policies and access controls

### Steps

#### Part A: Create and Configure S3 Bucket

**Step 1: Create Bucket**
```
1. Go to S3 → Create bucket
2. Bucket name: "lab-storage-[your-account-id]" (must be globally unique)
3. Region: us-east-1
4. Object Ownership: ACLs disabled (recommended)
5. Block Public Access: Keep all blocked (default)
6. Bucket Versioning: Enable
7. Tags: Environment=Lab
8. Encryption: Server-side encryption with Amazon S3 managed keys (SSE-S3)
9. Create bucket
```

**Step 2: Upload Objects**
```
1. Click on your bucket
2. Click "Upload"
3. Add files:
   - Create a text file: test-file-v1.txt with content "Version 1"
4. Click "Upload"
5. Verify file in bucket
```

**Step 3: Test Versioning**
```
1. Modify test-file-v1.txt locally: "Version 2"
2. Upload again with same name
3. Enable "Show versions" toggle
4. You should see two versions of the same file
5. Click on older version to download/view
```

**Step 4: Recover Deleted Object**
```
1. Delete test-file-v1.txt (without showing versions)
2. Object appears deleted
3. Enable "Show versions"
4. You'll see a "Delete marker"
5. Delete the delete marker to restore the file
```

#### Part B: S3 Storage Classes

**Step 5: Upload to Different Storage Classes**
```
1. Create 4 small test files locally
2. Upload each with different storage class:
   - standard-file.txt → Standard
   - ia-file.txt → Standard-IA
   - glacier-file.txt → Glacier Instant Retrieval
   - intelligent-file.txt → Intelligent-Tiering
3. For each upload:
   - Click "Properties" during upload
   - Select Storage class
   - Upload
```

**Step 6: View Storage Class**
```
1. Select each file
2. Properties tab
3. Note the Storage class
4. Compare costs (shown in pricing info)
```

#### Part C: Lifecycle Policies

**Step 7: Create Lifecycle Rule**
```
1. Go to bucket → Management tab
2. Click "Create lifecycle rule"
3. Rule name: "lab-lifecycle-rule"
4. Apply to all objects in bucket
5. Lifecycle rule actions:
   - Check: Move current versions of objects between storage classes
   - Check: Move noncurrent versions of objects between storage classes
   - Check: Expire noncurrent versions of objects
```

**Step 8: Configure Transitions**
```
1. Transition current versions:
   - Standard-IA: After 30 days
   - Glacier Flexible Retrieval: After 90 days
   - Glacier Deep Archive: After 180 days

2. Transition noncurrent versions:
   - Glacier Flexible Retrieval: After 30 days

3. Expire noncurrent versions:
   - After 365 days

4. Review and create rule
```

#### Part D: S3 Bucket Policies

**Step 9: Create Bucket Policy for Read Access**
```
1. Go to bucket → Permissions
2. Bucket policy → Edit
3. Paste this policy (replace BUCKET-NAME):

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::BUCKET-NAME/public/*",
            "Condition": {
                "IpAddress": {
                    "aws:SourceIp": "YOUR-IP/32"
                }
            }
        }
    ]
}

4. Replace YOUR-IP with your actual IP
5. Save changes
```

**Step 10: Test Access**
```
1. Create folder: public/
2. Upload file: public/test.txt
3. Make bucket block public access settings allow public policies:
   - Uncheck "Block public access to buckets and objects granted through new public bucket or access point policies"
4. Try accessing: https://BUCKET-NAME.s3.amazonaws.com/public/test.txt
```

#### Part E: S3 Encryption

**Step 11: Upload with Different Encryption**
```
1. Upload file with SSE-S3 (default)
2. Upload file with SSE-KMS:
   - Properties → Server-side encryption
   - AWS Key Management Service key (SSE-KMS)
   - AWS managed key (aws/s3)
3. View properties to confirm encryption type
```

#### Part F: S3 Replication

**Step 12: Create Destination Bucket**
```
1. Create new bucket in different region (e.g., us-west-2)
2. Name: "lab-storage-replica-[account-id]"
3. Enable versioning (required for replication)
4. Create bucket
```

**Step 13: Configure Replication**
```
1. Go to source bucket → Management
2. Create replication rule
3. Rule name: "lab-replication-rule"
4. Status: Enabled
5. Source bucket: All objects
6. Destination: Browse and select replica bucket
7. IAM role: Create new role
8. Save
9. Choose: No (don't replicate existing objects)
```

**Step 14: Test Replication**
```
1. Upload new file to source bucket
2. Wait 1-2 minutes
3. Check destination bucket
4. Verify file was replicated
```

### Cleanup for Lab 2.1
```
1. Delete replication rule
2. Empty and delete replica bucket
3. Disable versioning and delete all versions in source bucket
4. Empty and delete source bucket
5. Delete lifecycle rules
```

---

## Lab 2.2: Amazon EBS (Elastic Block Store)

### Objectives
- Create and attach EBS volumes
- Create snapshots and restore
- Understand volume types

### Steps

#### Part A: Create and Attach EBS Volume

**Step 1: Launch EC2 Instance**
```
1. Launch t2.micro Amazon Linux 2023 instance
2. Name: "Lab-EBS-Instance"
3. Use existing key pair
4. Security group: Allow SSH
5. Note the Availability Zone (e.g., us-east-1a)
```

**Step 2: Create EBS Volume**
```
1. Go to EC2 → Elastic Block Store → Volumes
2. Create volume
3. Volume type: General Purpose SSD (gp3)
4. Size: 10 GiB
5. IOPS: 3000 (default)
6. Throughput: 125 MB/s (default)
7. Availability Zone: Same as your EC2 instance!
8. Encryption: Yes, use default key
9. Tags: Name=Lab-Extra-Volume
10. Create volume
```

**Step 3: Attach Volume**
```
1. Select the new volume
2. Actions → Attach volume
3. Instance: Select Lab-EBS-Instance
4. Device name: /dev/sdf (or suggested name)
5. Attach
```

**Step 4: Mount Volume on Instance**
```bash
# Connect to instance via EC2 Instance Connect

# List block devices
lsblk

# Check if volume has filesystem
sudo file -s /dev/xvdf

# Create filesystem (only if output shows "data")
sudo mkfs -t xfs /dev/xvdf

# Create mount point
sudo mkdir /data

# Mount volume
sudo mount /dev/xvdf /data

# Verify mount
df -h

# Create test file
sudo sh -c 'echo "Hello from EBS!" > /data/test.txt'
cat /data/test.txt

# Make mount persistent (add to fstab)
sudo sh -c 'echo "/dev/xvdf /data xfs defaults,nofail 0 2" >> /etc/fstab'
```

#### Part B: EBS Snapshots

**Step 5: Create Snapshot**
```
1. Go to EC2 → Volumes
2. Select Lab-Extra-Volume
3. Actions → Create snapshot
4. Description: "Lab EBS snapshot"
5. Tags: Name=Lab-Snapshot
6. Create snapshot
```

**Step 6: Monitor Snapshot Progress**
```
1. Go to EC2 → Snapshots
2. Find your snapshot
3. Wait for Status: completed
```

**Step 7: Create Volume from Snapshot**
```
1. Select snapshot
2. Actions → Create volume from snapshot
3. Volume type: gp3
4. Size: 10 GiB (or larger)
5. Availability Zone: Can be different from original!
6. Create volume
```

**Step 8: Copy Snapshot to Another Region**
```
1. Select snapshot
2. Actions → Copy snapshot
3. Destination region: us-west-2
4. Description: "Lab snapshot copy to us-west-2"
5. Copy snapshot
6. Switch to us-west-2 region to verify
```

#### Part C: EBS Volume Types Comparison

**Step 9: Create Different Volume Types**
```
Create volumes (don't attach, just for learning):

1. gp3: General Purpose SSD
   - 100 GiB, 3000 IOPS, 125 MB/s

2. io2: Provisioned IOPS SSD
   - 100 GiB, 5000 IOPS
   - Note: Higher cost

3. st1: Throughput Optimized HDD
   - 500 GiB minimum
   - Good for big data, data warehouses

4. sc1: Cold HDD
   - 500 GiB minimum
   - Lowest cost for infrequently accessed data
```

**Step 10: Document Volume Specifications**
```
Note down for each volume type:
- Minimum/Maximum size
- Baseline IOPS
- Maximum IOPS
- Throughput
- Use cases
```

#### Part D: EBS Encryption

**Step 11: Enable Default Encryption**
```
1. Go to EC2 → EBS Settings (bottom left)
2. Account attributes → EBS encryption
3. Click "Manage"
4. Enable "Always encrypt new EBS volumes"
5. Select default KMS key
6. Update
```

**Step 12: Create Encrypted Volume**
```
1. Create new volume
2. Note that encryption is now selected by default
3. Create volume
4. Verify encryption in volume details
```

### Cleanup for Lab 2.2
```
1. Unmount and detach volumes from instances
2. Terminate EC2 instance
3. Delete all EBS volumes
4. Delete all snapshots (including copied ones in other regions)
5. Disable default EBS encryption if desired
```

---

## Lab 2.3: Amazon EFS (Elastic File System)

### Objectives
- Create EFS file system
- Mount on multiple EC2 instances
- Configure performance modes

### Steps

#### Part A: Create EFS File System

**Step 1: Create Security Group for EFS**
```
1. Go to EC2 → Security Groups
2. Create security group
3. Name: "lab-efs-sg"
4. Description: "Allow NFS traffic for EFS"
5. VPC: Default
6. Inbound rules:
   - Type: NFS
   - Source: Your EC2 security group OR 0.0.0.0/0 (for lab)
7. Create
```

**Step 2: Create EFS**
```
1. Go to EFS → Create file system
2. Click "Customize"
3. Name: "lab-efs"
4. Storage class: Standard
5. Automatic backups: Disable (for lab)
6. Lifecycle management: None
7. Encryption: Enable
8. Next
```

**Step 3: Configure Network**
```
1. VPC: Default VPC
2. Mount targets:
   - For each AZ, select:
     - Subnet: Default subnet
     - Security group: lab-efs-sg
3. Next
4. File system policy: Skip
5. Review and Create
```

#### Part B: Mount EFS on EC2 Instances

**Step 4: Launch Two EC2 Instances**
```
1. Launch 2 EC2 instances in different AZs:
   - Lab-EFS-Instance-1 (us-east-1a)
   - Lab-EFS-Instance-2 (us-east-1b)
2. Both with:
   - Amazon Linux 2023
   - t2.micro
   - Security group allowing SSH
   - Same key pair
```

**Step 5: Install EFS Utils**
```bash
# On both instances:
sudo yum install -y amazon-efs-utils
```

**Step 6: Mount EFS on Instance 1**
```bash
# Create mount point
sudo mkdir /efs

# Get EFS file system ID from console (fs-xxxxxxxx)

# Mount using EFS mount helper
sudo mount -t efs fs-xxxxxxxx:/ /efs

# Or with TLS encryption:
sudo mount -t efs -o tls fs-xxxxxxxx:/ /efs

# Verify mount
df -h

# Create test file
sudo sh -c 'echo "Hello from Instance 1!" > /efs/test.txt'
```

**Step 7: Mount EFS on Instance 2**
```bash
# Create mount point
sudo mkdir /efs

# Mount (same file system ID)
sudo mount -t efs fs-xxxxxxxx:/ /efs

# Verify mount
df -h

# Read file created by Instance 1
cat /efs/test.txt

# Add content from Instance 2
sudo sh -c 'echo "Hello from Instance 2!" >> /efs/test.txt'
```

**Step 8: Verify Shared Storage**
```bash
# On Instance 1:
cat /efs/test.txt
# Should show both messages
```

#### Part C: EFS Access Points

**Step 9: Create Access Point**
```
1. Go to EFS → Your file system
2. Access points tab → Create access point
3. Name: "lab-access-point"
4. Root directory path: /app1
5. POSIX user:
   - User ID: 1000
   - Group ID: 1000
6. Root directory creation permissions:
   - Owner user ID: 1000
   - Owner group ID: 1000
   - Permissions: 755
7. Create access point
```

**Step 10: Mount Using Access Point**
```bash
# Get access point ID (fsap-xxxxxxxx)

sudo mkdir /app1

sudo mount -t efs -o tls,accesspoint=fsap-xxxxxxxx fs-xxxxxxxx:/ /app1

# Files here will have 1000:1000 ownership
ls -la /app1
```

### Cleanup for Lab 2.3
```
1. Unmount EFS from all instances:
   sudo umount /efs
   sudo umount /app1
2. Terminate EC2 instances
3. Delete EFS access points
4. Delete EFS file system
5. Delete lab-efs-sg security group
```

---

## Lab 2.4: S3 Static Website Hosting

### Objectives
- Host a static website on S3
- Configure custom error pages
- Integrate with CloudFront (optional)

### Steps

#### Part A: Create Static Website Bucket

**Step 1: Create Bucket**
```
1. Create S3 bucket
2. Name: "lab-website-[account-id]"
3. Region: us-east-1
4. Uncheck "Block all public access"
   - Acknowledge warning
5. Create bucket
```

**Step 2: Enable Static Website Hosting**
```
1. Go to bucket → Properties
2. Static website hosting → Edit
3. Enable
4. Hosting type: Host a static website
5. Index document: index.html
6. Error document: error.html
7. Save changes
8. Note the bucket website endpoint URL
```

**Step 3: Create Website Files**
```html
<!-- index.html -->
<!DOCTYPE html>
<html>
<head>
    <title>My AWS Lab Website</title>
    <style>
        body { font-family: Arial; text-align: center; padding: 50px; }
        h1 { color: #FF9900; }
    </style>
</head>
<body>
    <h1>Welcome to My AWS Lab Website!</h1>
    <p>This website is hosted on Amazon S3.</p>
    <p>Current time: <span id="time"></span></p>
    <script>
        document.getElementById('time').textContent = new Date().toLocaleString();
    </script>
</body>
</html>
```

```html
<!-- error.html -->
<!DOCTYPE html>
<html>
<head>
    <title>Error - Page Not Found</title>
    <style>
        body { font-family: Arial; text-align: center; padding: 50px; }
        h1 { color: red; }
    </style>
</head>
<body>
    <h1>404 - Page Not Found</h1>
    <p>Sorry, the page you're looking for doesn't exist.</p>
    <a href="index.html">Go to Homepage</a>
</body>
</html>
```

**Step 4: Upload Files**
```
1. Upload index.html and error.html to bucket
2. Keep default settings
```

**Step 5: Configure Bucket Policy**
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::lab-website-[account-id]/*"
        }
    ]
}
```
```
1. Go to Permissions → Bucket policy
2. Paste policy (update bucket name)
3. Save
```

**Step 6: Test Website**
```
1. Copy bucket website endpoint URL
2. Open in browser
3. Verify index.html loads
4. Test error page: add /nonexistent to URL
```

#### Part B: Add CloudFront Distribution (Optional)

**Step 7: Create CloudFront Distribution**
```
1. Go to CloudFront → Create distribution
2. Origin domain: Select your S3 website endpoint
   (Use the website endpoint, not the S3 bucket)
3. Origin path: Leave empty
4. Name: lab-website-origin
5. Viewer protocol policy: Redirect HTTP to HTTPS
6. Cache policy: CachingOptimized
7. Default root object: index.html
8. Create distribution
```

**Step 8: Wait and Test**
```
1. Wait for deployment (5-10 minutes)
2. Copy CloudFront domain name (xxxxx.cloudfront.net)
3. Test in browser
4. Website now served via CloudFront with HTTPS
```

### Cleanup for Lab 2.4
```
1. Delete CloudFront distribution:
   - Disable first, wait for deployment
   - Then delete
2. Empty and delete S3 bucket
```

---

## Lab 2.5: S3 Presigned URLs

### Objectives
- Generate presigned URLs for secure temporary access
- Upload files using presigned URLs

### Steps

**Step 1: Create Private Bucket**
```
1. Create S3 bucket with all default settings (blocked public access)
2. Name: "lab-presigned-[account-id]"
3. Upload a file: secret-document.txt
```

**Step 2: Generate Presigned URL (AWS CLI)**
```bash
# Generate URL valid for 1 hour (3600 seconds)
aws s3 presign s3://lab-presigned-[account-id]/secret-document.txt --expires-in 3600

# Output will be a long URL with signature
# Copy and paste in browser to download file
```

**Step 3: Generate Presigned URL (Python)**
```python
# Save as generate_presigned.py

import boto3
from botocore.exceptions import ClientError

def create_presigned_url(bucket_name, object_name, expiration=3600):
    s3_client = boto3.client('s3')
    try:
        response = s3_client.generate_presigned_url('get_object',
            Params={'Bucket': bucket_name, 'Key': object_name},
            ExpiresIn=expiration)
    except ClientError as e:
        print(e)
        return None
    return response

# Usage
bucket = 'lab-presigned-[account-id]'
key = 'secret-document.txt'
url = create_presigned_url(bucket, key)
print(f"Presigned URL: {url}")
```

**Step 4: Generate Presigned URL for Upload**
```python
def create_presigned_post(bucket_name, object_name, expiration=3600):
    s3_client = boto3.client('s3')
    try:
        response = s3_client.generate_presigned_post(bucket_name,
            object_name,
            ExpiresIn=expiration)
    except ClientError as e:
        print(e)
        return None
    return response

# Usage
bucket = 'lab-presigned-[account-id]'
key = 'uploads/user-file.txt'
post_data = create_presigned_post(bucket, key)
print(f"URL: {post_data['url']}")
print(f"Fields: {post_data['fields']}")
```

### Cleanup for Lab 2.5
```
1. Empty and delete bucket
```

---

## 📝 Lab Summary & Key Takeaways

### What You Learned:
1. **S3 Basics**: Buckets, versioning, lifecycle policies
2. **S3 Storage Classes**: Standard, IA, Glacier options
3. **S3 Security**: Bucket policies, encryption, access control
4. **S3 Features**: Replication, static hosting, presigned URLs
5. **EBS**: Volume types, snapshots, encryption
6. **EFS**: Shared file storage, access points

### Exam-Relevant Points:
- S3 bucket names are globally unique
- Versioning cannot be disabled, only suspended
- Cross-region replication requires versioning
- EBS volumes must be in same AZ as EC2
- EBS snapshots are region-specific
- EFS can span multiple AZs

---

**Next:** [Labs-03-Database](./Labs-03-Database.md)
