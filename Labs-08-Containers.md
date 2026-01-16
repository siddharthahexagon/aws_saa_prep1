# AWS SAA-C03 Hands-On Labs: Containers (ECS, EKS, ECR)

## 📋 Overview
These labs cover ECR, ECS (EC2 and Fargate), and EKS basics.

**Estimated Time:** 4-5 hours total  
**Cost Estimate:** $3-8 (EKS control plane costs ~$0.10/hour)  
**Free Tier Eligible:** Partially

---

## Lab 8.1: Amazon ECR (Elastic Container Registry)

### Objectives
- Create ECR repository
- Build and push Docker images
- Configure lifecycle policies

### Steps

#### Part A: Create Repository

**Step 1: Create ECR Repository**
```
1. Go to ECR → Repositories → Create repository
2. Visibility: Private
3. Repository name: "lab-web-app"
4. Image tag mutability: Immutable (recommended)
5. Encryption: AES-256
6. Create repository
```

**Step 2: View Repository Details**
```
1. Click on repository
2. Note the repository URI:
   [account-id].dkr.ecr.us-east-1.amazonaws.com/lab-web-app
3. Click "View push commands"
```

#### Part B: Build and Push Image

**Step 3: Create Dockerfile**
```dockerfile
# Create project folder with these files

# Dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY app.py .

EXPOSE 5000

CMD ["python", "app.py"]
```

```python
# app.py
from flask import Flask, jsonify
import socket
import os

app = Flask(__name__)

@app.route('/')
def home():
    return jsonify({
        'message': 'Hello from Container!',
        'hostname': socket.gethostname(),
        'version': os.environ.get('APP_VERSION', '1.0')
    })

@app.route('/health')
def health():
    return jsonify({'status': 'healthy'})

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

```
# requirements.txt
flask==3.0.0
```

**Step 4: Build Image Locally**
```bash
# From project directory
docker build -t lab-web-app:v1 .

# Test locally
docker run -p 5000:5000 lab-web-app:v1
# Visit http://localhost:5000
```

**Step 5: Authenticate to ECR**
```bash
# Get login command
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin [account-id].dkr.ecr.us-east-1.amazonaws.com
```

**Step 6: Tag and Push Image**
```bash
# Tag image for ECR
docker tag lab-web-app:v1 [account-id].dkr.ecr.us-east-1.amazonaws.com/lab-web-app:v1
docker tag lab-web-app:v1 [account-id].dkr.ecr.us-east-1.amazonaws.com/lab-web-app:latest

# Push to ECR
docker push [account-id].dkr.ecr.us-east-1.amazonaws.com/lab-web-app:v1
docker push [account-id].dkr.ecr.us-east-1.amazonaws.com/lab-web-app:latest
```

**Step 7: Verify in Console**
```
1. ECR → lab-web-app repository
2. View images tab
3. Verify v1 and latest tags present
4. Click on image to view details:
   - Size
   - Pushed date
   - Digest
```

#### Part C: Lifecycle Policies

**Step 8: Create Lifecycle Policy**
```
1. Select repository → Lifecycle policy
2. Create rule:
   - Rule priority: 1
   - Rule description: "Delete untagged images after 7 days"
   - Image status: Untagged
   - Match criteria: Since image pushed > 7 days
   - Action: expire
3. Save

4. Add another rule:
   - Rule priority: 2
   - Description: "Keep only last 5 images"
   - Image status: Tagged
   - Match criteria: Image count more than 5
   - Action: expire
5. Save
```

#### Part D: Image Scanning

**Step 9: Enable Scanning**
```
1. Go to Private registry → Scanning
2. Edit scanning configuration
3. Scan frequency: Scan on push
4. Repository filters: lab-*
5. Save
```

**Step 10: View Scan Results**
```
1. Push a new image (or rescan existing)
2. ECR → Repository → Images
3. Click on image
4. View vulnerabilities tab
5. Review severity levels
```

### Cleanup for Lab 8.1
```
1. Delete all images in repository
2. Delete repository
3. Remove lifecycle policies
```

---

## Lab 8.2: Amazon ECS with Fargate

### Objectives
- Create ECS cluster
- Deploy containerized application on Fargate
- Configure service scaling

### Steps

#### Part A: Create ECS Cluster

**Step 1: Create Cluster**
```
1. Go to ECS → Clusters → Create cluster
2. Cluster name: "lab-ecs-cluster"
3. Infrastructure: AWS Fargate (serverless)
4. Create
```

#### Part B: Create Task Definition

**Step 2: Create Task Definition**
```
1. ECS → Task definitions → Create new task definition
2. Task definition family: "lab-web-task"
3. Launch type: AWS Fargate
4. Operating system: Linux/X86_64
5. CPU: 0.25 vCPU
6. Memory: 0.5 GB
7. Task role: Create new or select existing with ECR access
8. Task execution role: ecsTaskExecutionRole
```

**Step 3: Configure Container**
```
1. Container name: "web-container"
2. Image URI: [account-id].dkr.ecr.us-east-1.amazonaws.com/lab-web-app:latest
3. Port mappings:
   - Container port: 5000
   - Protocol: TCP
   - App protocol: HTTP
4. Environment variables:
   - APP_VERSION: 1.0
5. Health check:
   - Command: CMD-SHELL, curl -f http://localhost:5000/health || exit 1
   - Interval: 30
   - Timeout: 5
   - Retries: 3
6. Logging:
   - Log driver: awslogs
   - awslogs-group: /ecs/lab-web-task
   - awslogs-region: us-east-1
   - awslogs-stream-prefix: ecs
7. Create
```

#### Part C: Create Service

**Step 4: Create Security Group**
```
1. EC2 → Security Groups → Create
2. Name: "lab-ecs-service-sg"
3. VPC: Default or lab-vpc
4. Inbound:
   - HTTP (80) from 0.0.0.0/0
   - Custom TCP (5000) from 0.0.0.0/0
5. Create
```

**Step 5: Create ECS Service**
```
1. ECS → Clusters → lab-ecs-cluster
2. Services tab → Create
3. Compute options: Launch type → FARGATE
4. Application type: Service
5. Task definition:
   - Family: lab-web-task
   - Revision: LATEST
6. Service name: "lab-web-service"
7. Desired tasks: 2
```

**Step 6: Configure Networking**
```
1. VPC: Default VPC
2. Subnets: Select public subnets
3. Security group: lab-ecs-service-sg
4. Public IP: Turned on
```

**Step 7: Configure Load Balancer (Optional)**
```
1. Load balancer type: Application Load Balancer
2. Create new load balancer:
   - Name: "lab-ecs-alb"
   - Target group name: "lab-ecs-tg"
   - Health check path: /health
3. Or skip load balancer for basic testing
4. Create service
```

**Step 8: Verify Deployment**
```
1. Wait for service to stabilize
2. View Tasks tab
3. Click on running task
4. Note the public IP
5. Access: http://[public-ip]:5000
6. If using ALB, use ALB DNS name
```

#### Part D: Service Auto Scaling

**Step 9: Configure Auto Scaling**
```
1. Select service → Update service
2. Service auto scaling:
   - Minimum tasks: 1
   - Maximum tasks: 4
   - Desired tasks: 2
3. Scaling policy type: Target tracking
4. Policy name: "cpu-scaling"
5. ECS service metric: ECSServiceAverageCPUUtilization
6. Target value: 70
7. Scale-out cooldown: 60 seconds
8. Scale-in cooldown: 60 seconds
9. Update
```

**Step 10: Test Scaling**
```bash
# Generate load (from another machine)
# Install Apache Bench or use siege
ab -n 10000 -c 100 http://[service-url]:5000/

# Watch task count increase in ECS console
```

#### Part E: Rolling Updates

**Step 11: Update Container Image**
```python
# Update app.py
@app.route('/')
def home():
    return jsonify({
        'message': 'Hello from Container v2!',  # Changed
        'hostname': socket.gethostname(),
        'version': os.environ.get('APP_VERSION', '2.0')  # Changed
    })
```

**Step 12: Build and Push New Version**
```bash
docker build -t lab-web-app:v2 .
docker tag lab-web-app:v2 [account-id].dkr.ecr.us-east-1.amazonaws.com/lab-web-app:v2
docker tag lab-web-app:v2 [account-id].dkr.ecr.us-east-1.amazonaws.com/lab-web-app:latest
docker push [account-id].dkr.ecr.us-east-1.amazonaws.com/lab-web-app:v2
docker push [account-id].dkr.ecr.us-east-1.amazonaws.com/lab-web-app:latest
```

**Step 13: Update Task Definition**
```
1. Task definitions → lab-web-task
2. Create new revision
3. Update image tag to v2 or keep latest
4. Update environment variable: APP_VERSION=2.0
5. Create
```

**Step 14: Update Service**
```
1. Services → lab-web-service → Update
2. Task definition revision: Select latest
3. Force new deployment: Check
4. Update
5. Watch rolling deployment in Tasks tab
```

### Cleanup for Lab 8.2
```
1. Update service desired count to 0
2. Delete service
3. Delete ALB and target group (if created)
4. Delete task definitions (deregister all revisions)
5. Delete cluster
6. Delete CloudWatch log group
7. Delete security group
```

---

## Lab 8.3: ECS with EC2 Launch Type

### Objectives
- Create ECS cluster with EC2 instances
- Understand container placement
- Compare with Fargate

### Steps

#### Part A: Create EC2-backed Cluster

**Step 1: Create Cluster**
```
1. ECS → Create cluster
2. Cluster name: "lab-ecs-ec2-cluster"
3. Infrastructure: Amazon EC2 instances
4. Auto Scaling group:
   - Create new ASG
   - Operating system: Amazon Linux 2
   - EC2 instance type: t2.micro
   - Desired capacity: 2
   - Minimum: 1
   - Maximum: 3
5. Network settings:
   - VPC: Default
   - Subnets: Select 2 AZs
   - Security group: Create new with SSH and container ports
6. Create
```

**Step 2: Verify EC2 Instances**
```
1. EC2 → Instances
2. Find ECS container instances
3. Note they have ECS agent installed
4. View ECS cluster → Container instances tab
```

#### Part B: Deploy Service on EC2

**Step 3: Create EC2 Task Definition**
```
1. Create task definition
2. Family: "lab-ec2-task"
3. Launch type: EC2
4. Network mode: bridge
5. Container:
   - Name: web
   - Image: [ECR-URI]/lab-web-app:latest
   - Memory: 256 MB
   - Port mappings: Host port 0, Container port 5000
     (Dynamic port mapping)
6. Create
```

**Step 4: Create Service**
```
1. Create service
2. Launch type: EC2
3. Task definition: lab-ec2-task
4. Service name: "lab-ec2-service"
5. Desired tasks: 4
6. Load balancer: Create ALB
   - This enables dynamic port mapping
7. Create
```

**Step 5: Understand Placement**
```
1. View Tasks tab
2. Note which container instances run which tasks
3. Review placement strategies:
   - binpack: Minimize container instances
   - spread: Distribute across AZs
   - random: Random placement
```

#### Part C: Compare with Fargate

| Feature | EC2 Launch Type | Fargate |
|---------|----------------|---------|
| Infrastructure | Manage EC2 instances | Serverless |
| Pricing | Pay for EC2 instances | Pay per task vCPU/memory |
| Scaling | Scale cluster capacity | Automatic |
| Placement | Control placement | AWS managed |
| Use Case | Cost optimization at scale | Simplicity, variable workloads |

### Cleanup for Lab 8.3
```
1. Delete ECS service
2. Delete task definitions
3. Delete cluster (terminates EC2 instances)
4. Delete ALB
5. Delete security groups
```

---

## Lab 8.4: Amazon EKS Basics

### Objectives
- Create EKS cluster
- Deploy application using kubectl
- Understand EKS networking

### Prerequisites
- kubectl installed
- AWS CLI v2
- eksctl (optional but recommended)

### Steps

#### Part A: Create EKS Cluster

**Step 1: Create Cluster (Console)**
```
1. Go to EKS → Create cluster
2. Name: "lab-eks-cluster"
3. Kubernetes version: Latest stable
4. Cluster service role: Create new or select existing
5. Next
```

**Step 2: Specify Networking**
```
1. VPC: Default or lab-vpc
2. Subnets: Select at least 2 AZs
3. Security groups: Let EKS create
4. Cluster endpoint access: Public and private
5. Next
```

**Step 3: Configure Add-ons**
```
1. Amazon VPC CNI: Enable
2. CoreDNS: Enable
3. kube-proxy: Enable
4. Amazon EBS CSI Driver: Enable (optional)
5. Next → Create
6. Wait 10-15 minutes for cluster creation
```

**Alternative: Using eksctl (Faster)**
```bash
eksctl create cluster \
  --name lab-eks-cluster \
  --region us-east-1 \
  --nodegroup-name lab-nodes \
  --node-type t3.small \
  --nodes 2 \
  --nodes-min 1 \
  --nodes-max 3 \
  --managed
```

#### Part B: Add Node Group

**Step 4: Create Managed Node Group**
```
1. EKS → lab-eks-cluster → Compute → Add node group
2. Name: "lab-node-group"
3. Node IAM role: Create or select AmazonEKSNodeRole
4. Next
```

**Step 5: Configure Nodes**
```
1. AMI type: Amazon Linux 2
2. Capacity type: On-Demand
3. Instance types: t3.small
4. Disk size: 20 GB
5. Scaling:
   - Minimum: 1
   - Maximum: 3
   - Desired: 2
6. Next → Create
```

#### Part C: Configure kubectl

**Step 6: Update kubeconfig**
```bash
# Update kubeconfig for EKS cluster
aws eks update-kubeconfig --name lab-eks-cluster --region us-east-1

# Verify connection
kubectl get nodes
kubectl get namespaces
```

#### Part D: Deploy Application

**Step 7: Create Deployment Manifest**
```yaml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: lab-web-app
  labels:
    app: web
spec:
  replicas: 2
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: web
        image: [account-id].dkr.ecr.us-east-1.amazonaws.com/lab-web-app:latest
        ports:
        - containerPort: 5000
        env:
        - name: APP_VERSION
          value: "1.0-eks"
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /health
            port: 5000
          initialDelaySeconds: 10
          periodSeconds: 5
        readinessProbe:
          httpGet:
            path: /health
            port: 5000
          initialDelaySeconds: 5
          periodSeconds: 3
```

**Step 8: Create Service Manifest**
```yaml
# service.yaml
apiVersion: v1
kind: Service
metadata:
  name: lab-web-service
spec:
  type: LoadBalancer
  selector:
    app: web
  ports:
  - protocol: TCP
    port: 80
    targetPort: 5000
```

**Step 9: Deploy to EKS**
```bash
# Apply manifests
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml

# Watch deployment
kubectl get deployments -w
kubectl get pods

# Get service external IP
kubectl get service lab-web-service
# Wait for EXTERNAL-IP (takes 2-3 minutes)
```

**Step 10: Test Application**
```bash
# Get Load Balancer URL
EXTERNAL_URL=$(kubectl get service lab-web-service -o jsonpath='{.status.loadBalancer.ingress[0].hostname}')

# Test
curl http://$EXTERNAL_URL
```

#### Part E: Scaling

**Step 11: Manual Scaling**
```bash
# Scale deployment
kubectl scale deployment lab-web-app --replicas=4

# Verify
kubectl get pods
```

**Step 12: Horizontal Pod Autoscaler**
```bash
# Install metrics server (if not installed)
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

# Create HPA
kubectl autoscale deployment lab-web-app --cpu-percent=50 --min=2 --max=10

# View HPA
kubectl get hpa
```

#### Part F: Clean Up Deployment

**Step 13: Delete Resources**
```bash
# Delete application resources
kubectl delete service lab-web-service
kubectl delete deployment lab-web-app
kubectl delete hpa lab-web-app

# Verify
kubectl get all
```

### Cleanup for Lab 8.4
```bash
# Using eksctl (if used to create)
eksctl delete cluster --name lab-eks-cluster

# Or manually:
1. Delete node groups in EKS console
2. Delete cluster
3. Delete VPC if created for EKS
4. Delete IAM roles

# This takes 10-15 minutes
```

---

## Lab 8.5: App Runner

### Objectives
- Deploy container with AWS App Runner
- Understand fully managed container service

### Steps

**Step 1: Create App Runner Service**
```
1. Go to App Runner → Create service
2. Source: Container registry
3. Provider: Amazon ECR
4. Container image URI: Browse and select lab-web-app:latest
5. ECR access role: Create new
6. Next
```

**Step 2: Configure Service**
```
1. Service name: "lab-app-runner-service"
2. CPU: 1 vCPU
3. Memory: 2 GB
4. Port: 5000
5. Auto scaling:
   - Min instances: 1
   - Max instances: 4
   - Max concurrent requests: 100
6. Health check:
   - Protocol: HTTP
   - Path: /health
7. Create and deploy
```

**Step 3: Test Service**
```
1. Wait for deployment to complete
2. Copy default domain URL
3. Test: curl https://[app-runner-url]
4. Auto HTTPS enabled!
```

**Step 4: Automatic Deployments**
```
1. Edit service
2. Deployment settings:
   - Automatic: Deploy when new image pushed to ECR
3. Push new image to ECR
4. Watch automatic deployment
```

### Cleanup for Lab 8.5
```
1. Delete App Runner service
2. Delete ECR image (if no longer needed)
```

---

## 📝 Lab Summary & Key Takeaways

### What You Learned:
1. **ECR**: Container registry, lifecycle policies, scanning
2. **ECS Fargate**: Serverless containers, services, scaling
3. **ECS EC2**: Managed EC2 clusters, placement strategies
4. **EKS**: Kubernetes on AWS, deployments, services
5. **App Runner**: Fully managed container deployment

### Exam-Relevant Points:
- ECR: Private registry, lifecycle policies, image scanning
- ECS Fargate: No infrastructure management, pay per task
- ECS EC2: More control, better cost at scale
- EKS: Standard Kubernetes, ~$0.10/hour control plane
- Task Role vs Execution Role:
  - Task Role: Permissions for containers
  - Execution Role: Permissions for ECS agent (pull images, logs)
- awsvpc network mode: Each task gets ENI
- Dynamic port mapping: Requires ALB

---

**Next:** [Labs-09-HA-DR](./Labs-09-HA-DR.md)
