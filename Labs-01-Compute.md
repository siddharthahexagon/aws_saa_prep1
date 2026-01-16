# AWS SAA-C03 Hands-On Labs: Compute Services

## 📋 Overview
These labs cover EC2, Lambda, Auto Scaling, and related compute services.

**Estimated Time:** 4-6 hours total  
**Cost Estimate:** $2-5 (if cleaned up promptly)  
**Free Tier Eligible:** Yes (most labs)

---

## Lab 1.1: EC2 Instance Lifecycle & Instance Types

### Objectives
- Launch EC2 instances with different configurations
- Understand instance metadata and user data
- Work with different instance types

### Prerequisites
- AWS account with admin access
- Basic understanding of Linux commands

### Steps

#### Part A: Launch Your First EC2 Instance

**Step 1: Navigate to EC2 Console**
```
1. Sign in to AWS Console
2. Search for "EC2" in the search bar
3. Click "EC2" to open the EC2 Dashboard
4. Note the current region (top right) - use us-east-1
```

**Step 2: Launch Instance**
```
1. Click "Launch instance"
2. Name: "Lab-WebServer-1"
3. Application and OS Images:
   - Select "Amazon Linux 2023 AMI" (Free tier eligible)
4. Instance type:
   - Select "t2.micro" (Free tier eligible)
5. Key pair:
   - Click "Create new key pair"
   - Name: "my-lab-keypair"
   - Key pair type: RSA
   - Private key format: .pem (Linux/Mac) or .ppk (Windows/PuTTY)
   - Click "Create key pair" (file downloads automatically)
```

**Step 3: Configure Network Settings**
```
1. Click "Edit" in Network settings
2. VPC: Default VPC
3. Subnet: No preference
4. Auto-assign public IP: Enable
5. Security group: Create security group
   - Security group name: "lab-web-sg"
   - Description: "Allow SSH and HTTP"
6. Inbound rules:
   - Rule 1: SSH, Source: My IP
   - Click "Add security group rule"
   - Rule 2: HTTP, Source: Anywhere (0.0.0.0/0)
```

**Step 4: Configure Storage**
```
1. Root volume: 8 GiB gp3
2. Delete on termination: Yes (checked)
```

**Step 5: Advanced Details - User Data**
```
1. Expand "Advanced details"
2. Scroll to "User data"
3. Paste the following script:

#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<h1>Hello from $(hostname -f)</h1>" > /var/www/html/index.html

4. Click "Launch instance"
```

**Step 6: Verify Instance**
```
1. Click "View all instances"
2. Wait for Instance state: "Running"
3. Wait for Status checks: "2/2 checks passed"
4. Copy the Public IPv4 address
5. Open browser and navigate to: http://[PUBLIC-IP]
6. You should see "Hello from [hostname]"
```

#### Part B: Access Instance Metadata

**Step 7: Connect via EC2 Instance Connect**
```
1. Select your instance
2. Click "Connect"
3. Choose "EC2 Instance Connect"
4. Click "Connect"
5. A browser-based terminal opens
```

**Step 8: Explore Instance Metadata**
```bash
# Get instance ID
curl http://169.254.169.254/latest/meta-data/instance-id

# Get instance type
curl http://169.254.169.254/latest/meta-data/instance-type

# Get public IP
curl http://169.254.169.254/latest/meta-data/public-ipv4

# Get availability zone
curl http://169.254.169.254/latest/meta-data/placement/availability-zone

# Get AMI ID
curl http://169.254.169.254/latest/meta-data/ami-id

# Get security groups
curl http://169.254.169.254/latest/meta-data/security-groups

# List all metadata categories
curl http://169.254.169.254/latest/meta-data/

# Get user data script
curl http://169.254.169.254/latest/user-data
```

#### Part C: Instance Actions

**Step 9: Stop and Start Instance**
```
1. Select instance → Actions → Instance state → Stop instance
2. Confirm stop
3. Wait for state: "Stopped"
4. Note: Public IP may change after restart
5. Actions → Instance state → Start instance
6. Wait for state: "Running"
7. Verify new public IP (likely different)
```

**Step 10: Create AMI from Instance**
```
1. Select instance
2. Actions → Image and templates → Create image
3. Image name: "Lab-WebServer-AMI"
4. Description: "AMI from lab web server"
5. No reboot: Unchecked (allows consistent snapshot)
6. Click "Create image"
7. Go to AMIs (left menu) to see your AMI
```

### Cleanup for Lab 1.1
```
1. Terminate EC2 instance:
   - Select instance → Actions → Instance state → Terminate
   - Confirm termination
2. Deregister AMI:
   - Go to AMIs → Select AMI → Actions → Deregister AMI
3. Delete snapshot:
   - Go to Snapshots → Select associated snapshot → Actions → Delete
4. Delete security group (after instance terminated):
   - Go to Security Groups → Select "lab-web-sg" → Actions → Delete
```

---

## Lab 1.2: EC2 Pricing Models & Spot Instances

### Objectives
- Launch Spot instances
- Understand Spot pricing and interruptions
- Compare pricing models

### Steps

#### Part A: View Spot Instance Pricing

**Step 1: Check Spot Pricing History**
```
1. Go to EC2 → Spot Requests
2. Click "Pricing history"
3. Instance type: t3.micro
4. Select multiple Availability Zones
5. Review price fluctuations over time
6. Note: Spot prices are typically 60-90% lower than On-Demand
```

#### Part B: Launch Spot Instance

**Step 2: Request Spot Instance**
```
1. Go to EC2 → Instances → Launch instance
2. Name: "Lab-Spot-Instance"
3. AMI: Amazon Linux 2023
4. Instance type: t3.micro
5. Key pair: my-lab-keypair
6. Network settings: Use existing security group "lab-web-sg"
   (or create new with SSH access)
```

**Step 3: Configure Spot Request**
```
1. Expand "Advanced details"
2. Purchasing option: Check "Request Spot Instances"
3. Maximum price: Leave empty (uses On-Demand price as cap)
4. Request type: One-time
5. Interruption behavior: Terminate
6. Launch instance
```

**Step 4: View Spot Request**
```
1. Go to EC2 → Spot Requests
2. Find your request
3. Review:
   - Request state
   - Status
   - Instance ID (when fulfilled)
   - Current price
```

#### Part C: Savings Plans Estimation

**Step 5: Explore Savings Plans (View Only)**
```
1. Go to AWS Cost Management → Savings Plans
2. Click "Recommendations"
3. Review recommendations based on usage
4. Note: This shows potential savings but don't purchase for labs
```

### Cleanup for Lab 1.2
```
1. Cancel Spot request:
   - EC2 → Spot Requests → Select request → Actions → Cancel request
2. Terminate Spot instance:
   - EC2 → Instances → Select instance → Terminate
```

---

## Lab 1.3: EC2 Placement Groups

### Objectives
- Create different types of placement groups
- Understand use cases for each type

### Steps

#### Part A: Create Cluster Placement Group

**Step 1: Create Placement Group**
```
1. Go to EC2 → Placement Groups
2. Click "Create placement group"
3. Name: "lab-cluster-pg"
4. Placement strategy: Cluster
5. Click "Create group"
```

**Step 2: Launch Instance in Cluster PG**
```
1. Launch instance
2. Name: "Lab-Cluster-Instance-1"
3. AMI: Amazon Linux 2023
4. Instance type: t2.micro
5. Key pair: my-lab-keypair
6. Network settings: Create/select security group with SSH
7. Advanced details → Placement group: lab-cluster-pg
8. Launch instance
9. Repeat to launch "Lab-Cluster-Instance-2" in same placement group
```

#### Part B: Create Spread Placement Group

**Step 3: Create Spread PG**
```
1. Create placement group
2. Name: "lab-spread-pg"
3. Placement strategy: Spread
4. Spread level: Rack
5. Create group
```

**Step 4: Launch Instances in Spread PG**
```
1. Launch 3 instances named:
   - Lab-Spread-Instance-1
   - Lab-Spread-Instance-2
   - Lab-Spread-Instance-3
2. Each in "lab-spread-pg"
3. Verify they're on different hardware:
   - Select each instance
   - Check "Host ID" in details (should be different)
```

#### Part C: Create Partition Placement Group

**Step 5: Create Partition PG**
```
1. Create placement group
2. Name: "lab-partition-pg"
3. Placement strategy: Partition
4. Number of partitions: 3
5. Create group
```

**Step 6: Launch Instances in Partition PG**
```
1. Launch instance in partition 1:
   - Advanced details → Placement group: lab-partition-pg
   - Target partition: 1
2. Repeat for partition 2 and 3
```

### Cleanup for Lab 1.3
```
1. Terminate all instances
2. Delete placement groups:
   - EC2 → Placement Groups → Select each → Actions → Delete
```

---

## Lab 1.4: Auto Scaling Groups

### Objectives
- Create a launch template
- Configure Auto Scaling Group
- Test scaling policies

### Steps

#### Part A: Create Launch Template

**Step 1: Create Launch Template**
```
1. Go to EC2 → Launch Templates
2. Click "Create launch template"
3. Launch template name: "lab-web-template"
4. Template version description: "v1 - Basic web server"
5. Check "Auto Scaling guidance"
```

**Step 2: Configure Template**
```
1. AMI: Amazon Linux 2023
2. Instance type: t2.micro
3. Key pair: my-lab-keypair
4. Network settings:
   - Subnet: Don't include in launch template
   - Security groups: Create new
     - Name: "lab-asg-sg"
     - Add SSH (My IP) and HTTP (Anywhere)
5. Resource tags:
   - Key: Environment, Value: Lab
6. Advanced details → User data:

#!/bin/bash
yum update -y
yum install -y httpd stress
systemctl start httpd
systemctl enable httpd
INSTANCE_ID=$(curl -s http://169.254.169.254/latest/meta-data/instance-id)
echo "<h1>Instance: $INSTANCE_ID</h1>" > /var/www/html/index.html

7. Create launch template
```

#### Part B: Create Auto Scaling Group

**Step 3: Create ASG**
```
1. Go to EC2 → Auto Scaling Groups
2. Click "Create Auto Scaling group"
3. Name: "lab-asg"
4. Launch template: lab-web-template
5. Click Next
```

**Step 4: Configure Network**
```
1. VPC: Default VPC
2. Availability Zones: Select at least 2 AZs
3. Click Next
```

**Step 5: Configure Load Balancing (Optional for basic test)**
```
1. For now, select "No load balancer"
2. Health checks: EC2 (default)
3. Click Next
```

**Step 6: Configure Group Size**
```
1. Desired capacity: 2
2. Minimum capacity: 1
3. Maximum capacity: 4
4. Click Next
```

**Step 7: Configure Scaling Policies**
```
1. Select "Target tracking scaling policy"
2. Metric type: Average CPU utilization
3. Target value: 50
4. Instances need: 300 seconds to warm up
5. Click Next
6. Add notifications (optional - skip for lab)
7. Add tags:
   - Key: Name, Value: Lab-ASG-Instance
8. Review and Create
```

#### Part C: Test Auto Scaling

**Step 8: Verify Initial State**
```
1. Wait for instances to launch
2. Go to EC2 → Instances
3. Verify 2 instances running with Name: Lab-ASG-Instance
4. Note public IPs
5. Test HTTP access to both instances
```

**Step 9: Trigger Scale Out**
```
1. Connect to one instance via EC2 Instance Connect
2. Run stress test:
   stress --cpu 4 --timeout 300
3. Wait 2-3 minutes
4. Check Auto Scaling → Activity tab
5. New instance should launch when CPU > 50%
```

**Step 10: Trigger Scale In**
```
1. Stop the stress test (Ctrl+C or wait for timeout)
2. Wait 5-10 minutes
3. Instance count should reduce back to desired (2)
```

#### Part D: Test Instance Refresh

**Step 11: Update Launch Template**
```
1. Go to Launch Templates
2. Select lab-web-template
3. Actions → Modify template (Create new version)
4. Modify user data to show version:

#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<h1>Version 2 - Instance: $(curl -s http://169.254.169.254/latest/meta-data/instance-id)</h1>" > /var/www/html/index.html

5. Create template version
```

**Step 12: Start Instance Refresh**
```
1. Go to Auto Scaling Groups → lab-asg
2. Instance refresh tab
3. Click "Start instance refresh"
4. Minimum healthy percentage: 50
5. Instance warmup: 60 seconds
6. Start refresh
7. Watch instances being replaced with new version
```

### Cleanup for Lab 1.4
```
1. Delete Auto Scaling Group:
   - Select ASG → Actions → Delete
   - Confirm deletion (this terminates instances)
2. Delete Launch Template:
   - Launch Templates → Select → Actions → Delete
3. Delete Security Group:
   - Security Groups → Select lab-asg-sg → Delete
```

---

## Lab 1.5: AWS Lambda Basics

### Objectives
- Create Lambda functions
- Configure triggers (S3, API Gateway)
- Understand Lambda execution environment

### Steps

#### Part A: Create Basic Lambda Function

**Step 1: Create Function**
```
1. Go to Lambda → Functions
2. Click "Create function"
3. Author from scratch
4. Function name: "lab-hello-lambda"
5. Runtime: Python 3.12
6. Architecture: x86_64
7. Permissions: Create new role with basic Lambda permissions
8. Create function
```

**Step 2: Write Function Code**
```python
# Replace the default code with:

import json

def lambda_handler(event, context):
    # Log the incoming event
    print(f"Received event: {json.dumps(event)}")
    
    # Get name from event or use default
    name = event.get('name', 'World')
    
    return {
        'statusCode': 200,
        'body': json.dumps({
            'message': f'Hello, {name}!',
            'event': event
        })
    }

# Click "Deploy" to save changes
```

**Step 3: Test Function**
```
1. Click "Test"
2. Create new test event:
   - Event name: "TestEvent"
   - Event JSON:
     {
       "name": "AWS Student"
     }
3. Save
4. Click "Test" again
5. Review execution results:
   - Response
   - Logs
   - Duration
   - Memory used
```

#### Part B: Configure Lambda with S3 Trigger

**Step 4: Create S3 Bucket**
```
1. Go to S3 → Create bucket
2. Bucket name: "lab-lambda-trigger-[your-account-id]"
   (Use account ID to ensure uniqueness)
3. Region: Same as Lambda function
4. Keep defaults
5. Create bucket
```

**Step 5: Create S3 Trigger Lambda**
```
1. Lambda → Create function
2. Function name: "lab-s3-processor"
3. Runtime: Python 3.12
4. Create function
```

**Step 6: Write S3 Processor Code**
```python
import json
import urllib.parse

def lambda_handler(event, context):
    # Get bucket and key from S3 event
    for record in event['Records']:
        bucket = record['s3']['bucket']['name']
        key = urllib.parse.unquote_plus(record['s3']['object']['key'])
        event_name = record['eventName']
        
        print(f"Event: {event_name}")
        print(f"Bucket: {bucket}")
        print(f"Key: {key}")
        print(f"Size: {record['s3']['object'].get('size', 'N/A')} bytes")
    
    return {
        'statusCode': 200,
        'body': json.dumps('Processed S3 event')
    }
```

**Step 7: Add S3 Trigger**
```
1. Click "Add trigger"
2. Select "S3"
3. Bucket: lab-lambda-trigger-[your-account-id]
4. Event type: All object create events
5. Acknowledge recursive invocation warning
6. Add
```

**Step 8: Test S3 Trigger**
```
1. Go to S3 → Your bucket
2. Upload any small file (e.g., test.txt)
3. Go back to Lambda
4. Click "Monitor" tab
5. Click "View CloudWatch Logs"
6. Open latest log stream
7. Verify file details were logged
```

#### Part C: Lambda with API Gateway

**Step 9: Create API Lambda**
```
1. Lambda → Create function
2. Function name: "lab-api-handler"
3. Runtime: Python 3.12
4. Create function
```

**Step 10: Write API Handler Code**
```python
import json
from datetime import datetime

def lambda_handler(event, context):
    # Parse HTTP method and path
    http_method = event.get('httpMethod', 'UNKNOWN')
    path = event.get('path', '/')
    query_params = event.get('queryStringParameters') or {}
    body = event.get('body')
    
    # Parse body if present
    if body:
        try:
            body = json.loads(body)
        except:
            pass
    
    # Simple routing
    if path == '/hello':
        name = query_params.get('name', 'World')
        response_body = {'message': f'Hello, {name}!'}
    elif path == '/time':
        response_body = {'current_time': datetime.now().isoformat()}
    elif path == '/echo':
        response_body = {'method': http_method, 'body': body, 'params': query_params}
    else:
        response_body = {'message': 'Welcome to the API', 'available_endpoints': ['/hello', '/time', '/echo']}
    
    return {
        'statusCode': 200,
        'headers': {
            'Content-Type': 'application/json',
            'Access-Control-Allow-Origin': '*'
        },
        'body': json.dumps(response_body)
    }
```

**Step 11: Add API Gateway Trigger**
```
1. Add trigger
2. Select "API Gateway"
3. Create a new API
4. API type: REST API
5. Security: Open
6. Add
7. Note the API endpoint URL
```

**Step 12: Test API**
```
1. Copy API endpoint URL
2. Test in browser or curl:

# Test root
curl https://[api-id].execute-api.[region].amazonaws.com/default/lab-api-handler

# Test hello endpoint
curl "https://[api-id].execute-api.[region].amazonaws.com/default/lab-api-handler/hello?name=Student"

# Test time endpoint
curl https://[api-id].execute-api.[region].amazonaws.com/default/lab-api-handler/time
```

#### Part D: Lambda Layers

**Step 13: Create Lambda Layer**
```
1. Create a folder structure locally:
   python/
     └── my_utils.py

2. Content of my_utils.py:
   def format_response(data, status=200):
       import json
       return {
           'statusCode': status,
           'body': json.dumps(data)
       }

3. Zip the python folder:
   - Select python folder → Compress to zip

4. Lambda → Layers → Create layer
5. Name: "lab-utils-layer"
6. Upload zip file
7. Compatible runtimes: Python 3.12
8. Create
```

**Step 14: Use Layer in Function**
```
1. Go to lab-hello-lambda function
2. Scroll to Layers
3. Add a layer
4. Custom layers → lab-utils-layer
5. Add

6. Update function code to use layer:
   from my_utils import format_response
   
   def lambda_handler(event, context):
       return format_response({'message': 'Hello from layer!'})
```

### Cleanup for Lab 1.5
```
1. Delete Lambda functions:
   - Lambda → Functions → Select each → Actions → Delete
2. Delete Lambda layers:
   - Lambda → Layers → Delete all versions
3. Delete API Gateway:
   - API Gateway → APIs → Select → Actions → Delete
4. Delete S3 bucket:
   - S3 → Empty bucket → Delete bucket
5. Delete CloudWatch Log Groups:
   - CloudWatch → Logs → Log groups → Delete /aws/lambda/lab-*
```

---

## Lab 1.6: Elastic Beanstalk

### Objectives
- Deploy a web application using Elastic Beanstalk
- Understand environment types and deployment options

### Steps

#### Part A: Create Elastic Beanstalk Application

**Step 1: Prepare Sample Application**
```
Create a folder with these files:

application.py:
from flask import Flask
application = Flask(__name__)

@application.route('/')
def hello():
    return '<h1>Hello from Elastic Beanstalk!</h1><p>Version 1.0</p>'

@application.route('/health')
def health():
    return 'OK', 200

if __name__ == '__main__':
    application.run(host='0.0.0.0', port=5000)

requirements.txt:
Flask==2.3.0
gunicorn==21.2.0

Zip all files into: app.zip
```

**Step 2: Create Application**
```
1. Go to Elastic Beanstalk
2. Click "Create application"
3. Application name: "lab-eb-app"
4. Platform: Python
5. Platform branch: Python 3.11
6. Application code: Upload your code
7. Upload app.zip
8. Click "Next"
```

**Step 3: Configure Service Access**
```
1. Service role: Create and use new service role
2. EC2 key pair: my-lab-keypair
3. EC2 instance profile: Create new instance profile
4. Click "Next"
```

**Step 4: Configure Networking**
```
1. VPC: Default VPC
2. Public IP address: Activated
3. Instance subnets: Select 2+ AZs
4. Click "Next"
```

**Step 5: Configure Instance**
```
1. Root volume type: General Purpose (SSD)
2. Size: 8 GB
3. Instance types: t2.micro
4. Click "Next"
```

**Step 6: Configure Scaling**
```
1. Environment type: Load balanced
2. Min instances: 1
3. Max instances: 2
4. Click "Next"
5. Review and Create
```

**Step 7: Access Application**
```
1. Wait for environment to launch (5-10 minutes)
2. Click the environment URL
3. Verify application is running
```

#### Part B: Deploy Application Update

**Step 8: Create New Version**
```
Update application.py:
@application.route('/')
def hello():
    return '<h1>Hello from Elastic Beanstalk!</h1><p>Version 2.0 - Updated!</p>'

Create new zip file: app-v2.zip
```

**Step 9: Deploy with Rolling Update**
```
1. Go to your environment
2. Click "Upload and deploy"
3. Upload app-v2.zip
4. Version label: v2
5. Deployment policy: Rolling
6. Batch size: 50%
7. Deploy
8. Watch deployment progress
```

### Cleanup for Lab 1.6
```
1. Terminate environment:
   - Elastic Beanstalk → Environments → Actions → Terminate
2. Delete application:
   - Elastic Beanstalk → Applications → Actions → Delete
3. Delete S3 bucket (created by EB):
   - S3 → Find elasticbeanstalk-* bucket → Empty → Delete
```

---

## 📝 Lab Summary & Key Takeaways

### What You Learned:
1. **EC2 Basics**: Launching instances, user data, metadata
2. **Pricing Models**: On-Demand vs Spot vs Reserved
3. **Placement Groups**: Cluster, Spread, Partition use cases
4. **Auto Scaling**: Launch templates, scaling policies, instance refresh
5. **Lambda**: Functions, triggers, layers, API Gateway integration
6. **Elastic Beanstalk**: Quick deployment, rolling updates

### Exam-Relevant Points:
- Instance metadata: `169.254.169.254`
- Spot instances: 2-minute interruption notice
- Placement groups: Cluster (low latency), Spread (HA), Partition (big data)
- Lambda: 15-minute max, 10GB RAM max
- Elastic Beanstalk: Manages EC2, ELB, ASG automatically

---

**Next:** [Labs-02-Storage](./Labs-02-Storage.md)
