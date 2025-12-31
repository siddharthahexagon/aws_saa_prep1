# Configuration Questionnaire: Microservices & Containers
## Step-by-Step Container Orchestration and Serverless Configurations

---

## 📋 Table of Contents
1. [Container Orchestration with ECS](#container-orchestration-with-ecs)
2. [Kubernetes with EKS](#kubernetes-with-eks)
3. [Serverless Microservices](#serverless-microservices)
4. [API Gateway Configurations](#api-gateway-configurations)
5. [Event-Driven Architecture](#event-driven-architecture)
6. [Service Mesh Implementation](#service-mesh-implementation)

---

## 🐳 SCENARIO 1: Container Orchestration with ECS

### Initial Questions:
```
Q1: Do you want to manage servers?
    ├─ NO → Use Fargate (serverless containers)
    └─ YES → Use EC2 launch type

Q2: How many containers/services?
    ├─ 1-5 → Simple ECS setup
    ├─ 5-20 → ECS with load balancing
    └─ >20 → ECS with Service Discovery + App Mesh

Q3: Do you need persistent storage?
    ├─ YES → EFS volumes
    └─ NO → Container storage only

Q4: Container registry location?
    ├─ AWS → ECR (Elastic Container Registry)
    └─ External → Docker Hub, private registry
```

### Configuration Matrix:

#### **Complete ECS Fargate Microservices Platform**
```
Services Required:
├─ ECS Cluster (Fargate)
├─ ECR (Container registry)
├─ ALB (Application Load Balancer)
├─ RDS/DynamoDB (Databases)
├─ ElastiCache (Caching)
├─ EFS (Shared storage)
├─ Service Discovery (Cloud Map)
├─ Secrets Manager (Credentials)
├─ CloudWatch (Monitoring)
├─ X-Ray (Distributed tracing)
└─ CodePipeline (CI/CD)

Configuration Steps:

[Step 1: Container Registry]
1. Create ECR Repositories:
   a. Frontend Service:
      - Repository name: myapp/frontend
      - Image tag immutability: Enabled
      - Scan on push: Enabled
      - Encryption: AES-256
      
   b. Backend API Service:
      - Repository name: myapp/backend-api
      - Same settings as above
      
   c. Worker Service:
      - Repository name: myapp/worker
      
2. Build and Push Images:
   ```bash
   # Authenticate to ECR
   aws ecr get-login-password --region us-east-1 | \
     docker login --username AWS --password-stdin \
     123456789012.dkr.ecr.us-east-1.amazonaws.com
   
   # Build image
   docker build -t myapp/frontend:latest ./frontend
   
   # Tag for ECR
   docker tag myapp/frontend:latest \
     123456789012.dkr.ecr.us-east-1.amazonaws.com/myapp/frontend:latest
   
   # Push to ECR
   docker push \
     123456789012.dkr.ecr.us-east-1.amazonaws.com/myapp/frontend:latest
   ```

[Step 2: VPC and Networking]
3. VPC Configuration:
   - CIDR: 10.0.0.0/16
   - Public Subnets: 10.0.1.0/24, 10.0.2.0/24 (for ALB)
   - Private Subnets: 10.0.10.0/24, 10.0.11.0/24 (for containers)
   - Database Subnets: 10.0.20.0/24, 10.0.21.0/24
   - Availability Zones: 2 minimum, 3 recommended
   - NAT Gateways: 1 per AZ (for private subnet internet access)
   - VPC Endpoints:
     • S3 (Gateway endpoint)
     • ECR (Interface endpoint)
     • CloudWatch Logs (Interface endpoint)
     • Secrets Manager (Interface endpoint)

4. Security Groups:
   a. ALB Security Group:
      - Inbound: 80, 443 from 0.0.0.0/0
      - Outbound: All to container SG
      
   b. Container Security Group:
      - Inbound: 
        • 3000 from ALB SG (frontend)
        • 8080 from ALB SG (backend)
      - Outbound: All
      
   c. Database Security Group:
      - Inbound: 5432 from container SG
      - Outbound: None needed
      
   d. ElastiCache Security Group:
      - Inbound: 6379 from container SG

[Step 3: ECS Cluster]
5. Create ECS Cluster:
   - Cluster name: "myapp-production"
   - Infrastructure: AWS Fargate (serverless)
   - Container Insights: Enabled
   - Namespace: myapp.local (for Service Discovery)
   
6. Cluster Configuration:
   - Default capacity provider: FARGATE
   - Add FARGATE_SPOT for cost savings (non-critical tasks)
   - CloudWatch Container Insights: Enabled

[Step 4: Task Definitions]
7. Frontend Task Definition:
   ```json
   {
     "family": "frontend-task",
     "networkMode": "awsvpc",
     "requiresCompatibilities": ["FARGATE"],
     "cpu": "512",
     "memory": "1024",
     "taskRoleArn": "arn:aws:iam::123456789012:role/frontend-task-role",
     "executionRoleArn": "arn:aws:iam::123456789012:role/ecsTaskExecutionRole",
     "containerDefinitions": [
       {
         "name": "frontend",
         "image": "123456789012.dkr.ecr.us-east-1.amazonaws.com/myapp/frontend:latest",
         "portMappings": [
           {
             "containerPort": 3000,
             "protocol": "tcp"
           }
         ],
         "environment": [
           {
             "name": "NODE_ENV",
             "value": "production"
           },
           {
             "name": "API_URL",
             "value": "http://backend-api.myapp.local:8080"
           }
         ],
         "secrets": [
           {
             "name": "SESSION_SECRET",
             "valueFrom": "arn:aws:secretsmanager:us-east-1:123456789012:secret:frontend/session-secret"
           }
         ],
         "logConfiguration": {
           "logDriver": "awslogs",
           "options": {
             "awslogs-group": "/ecs/frontend",
             "awslogs-region": "us-east-1",
             "awslogs-stream-prefix": "ecs"
           }
         },
         "healthCheck": {
           "command": ["CMD-SHELL", "curl -f http://localhost:3000/health || exit 1"],
           "interval": 30,
           "timeout": 5,
           "retries": 3,
           "startPeriod": 60
         }
       }
     ]
   }
   ```

8. Backend API Task Definition:
   ```json
   {
     "family": "backend-api-task",
     "networkMode": "awsvpc",
     "requiresCompatibilities": ["FARGATE"],
     "cpu": "1024",
     "memory": "2048",
     "taskRoleArn": "arn:aws:iam::123456789012:role/backend-task-role",
     "executionRoleArn": "arn:aws:iam::123456789012:role/ecsTaskExecutionRole",
     "containerDefinitions": [
       {
         "name": "backend-api",
         "image": "123456789012.dkr.ecr.us-east-1.amazonaws.com/myapp/backend-api:latest",
         "portMappings": [
           {
             "containerPort": 8080,
             "protocol": "tcp"
           }
         ],
         "environment": [
           {
             "name": "ENVIRONMENT",
             "value": "production"
           },
           {
             "name": "REDIS_HOST",
             "value": "redis-cluster.abcdef.cache.amazonaws.com"
           }
         ],
         "secrets": [
           {
             "name": "DB_PASSWORD",
             "valueFrom": "arn:aws:secretsmanager:us-east-1:123456789012:secret:rds/postgres-password"
           },
           {
             "name": "JWT_SECRET",
             "valueFrom": "arn:aws:secretsmanager:us-east-1:123456789012:secret:backend/jwt-secret"
           }
         ],
         "mountPoints": [
           {
             "sourceVolume": "efs-storage",
             "containerPath": "/mnt/efs",
             "readOnly": false
           }
         ],
         "logConfiguration": {
           "logDriver": "awslogs",
           "options": {
             "awslogs-group": "/ecs/backend-api",
             "awslogs-region": "us-east-1",
             "awslogs-stream-prefix": "ecs"
           }
         },
         "healthCheck": {
           "command": ["CMD-SHELL", "curl -f http://localhost:8080/health || exit 1"],
           "interval": 30,
           "timeout": 5,
           "retries": 3,
           "startPeriod": 90
         }
       }
     ],
     "volumes": [
       {
         "name": "efs-storage",
         "efsVolumeConfiguration": {
           "fileSystemId": "fs-12345678",
           "transitEncryption": "ENABLED",
           "authorizationConfig": {
             "iam": "ENABLED"
           }
         }
       }
     ]
   }
   ```

9. Worker Task Definition (Background Jobs):
   - Similar to backend, but no port mappings
   - Higher memory for processing
   - Reads from SQS queue

[Step 5: Application Load Balancer]
10. Create ALB:
    - Name: "myapp-alb"
    - Scheme: Internet-facing
    - IP address type: ipv4
    - Subnets: Public subnets (2+ AZs)
    - Security group: ALB-SG
    
11. Target Groups:
    a. Frontend Target Group:
       - Name: "frontend-tg"
       - Target type: IP
       - Protocol: HTTP
       - Port: 3000
       - VPC: myapp-vpc
       - Health check path: /health
       - Health check interval: 30 seconds
       - Healthy threshold: 2
       - Unhealthy threshold: 3
       - Deregistration delay: 30 seconds
       
    b. Backend API Target Group:
       - Name: "backend-api-tg"
       - Target type: IP
       - Protocol: HTTP
       - Port: 8080
       - Health check path: /api/health
       - Stickiness: Enabled (for sessions)
       
12. ALB Listeners:
    a. HTTP Listener (Port 80):
       - Default action: Redirect to HTTPS
       
    b. HTTPS Listener (Port 443):
       - SSL Certificate: From ACM
       - Rules:
         • Path /api/* → backend-api-tg
         • Path /* → frontend-tg
         • Host header api.myapp.com → backend-api-tg
         • Host header myapp.com → frontend-tg

[Step 6: ECS Services]
13. Frontend Service:
    - Service name: "frontend-service"
    - Cluster: myapp-production
    - Launch type: FARGATE
    - Task definition: frontend-task:latest
    - Desired count: 3
    - Deployment configuration:
      • Deployment type: Rolling update
      • Minimum healthy: 100%
      • Maximum: 200%
    - Network configuration:
      • Subnets: Private subnets
      • Security groups: Container-SG
      • Auto-assign public IP: NO (use NAT)
    - Load balancing:
      • Load balancer: myapp-alb
      • Target group: frontend-tg
      • Container: frontend:3000
    - Auto Scaling:
      • Min tasks: 3
      • Max tasks: 20
      • Target CPU: 70%
      • Target memory: 80%
      • Scale-in cooldown: 300 seconds
      • Scale-out cooldown: 60 seconds
    - Service Discovery:
      • Enable
      • Namespace: myapp.local
      • Service name: frontend
      • DNS records: A record
    
14. Backend API Service:
    - Service name: "backend-api-service"
    - Similar configuration to frontend
    - Desired count: 5 (higher load)
    - Auto Scaling: Min 5, Max 30
    - Service Discovery: backend-api.myapp.local

15. Worker Service:
    - Service name: "worker-service"
    - No load balancer (consumes from SQS)
    - Desired count: 2
    - Auto Scaling based on SQS queue depth:
      • Metric: ApproximateNumberOfMessages
      • Target: 100 messages per task
      • Min: 2, Max: 10

[Step 7: Service Discovery]
16. Cloud Map Configuration:
    - Namespace: myapp.local (private DNS)
    - Services registered automatically by ECS
    - Service endpoints:
      • frontend.myapp.local
      • backend-api.myapp.local
      • worker.myapp.local
    - Health checks: ECS task health
    
17. Benefits:
    - Automatic DNS registration/deregistration
    - Service-to-service communication
    - No hard-coded IPs or endpoints

[Step 8: Monitoring & Logging]
18. CloudWatch Logs:
    - Log groups created for each service
    - Retention: 7 days (adjustable)
    - Log Insights for querying
    
19. Container Insights:
    - CPU/Memory utilization per task
    - Network metrics
    - Task-level metrics
    - Dashboard per cluster
    
20. X-Ray Tracing:
    - Enable X-Ray daemon sidecar:
      ```json
      {
        "name": "xray-daemon",
        "image": "amazon/aws-xray-daemon",
        "cpu": 32,
        "memoryReservation": 256,
        "portMappings": [
          {
            "containerPort": 2000,
            "protocol": "udp"
          }
        ]
      }
      ```
    - Update app to send traces
    - View service map in X-Ray console
    
21. CloudWatch Alarms:
    - High CPU (> 80%) across service
    - High memory (> 90%)
    - Target group unhealthy hosts
    - ECS service task count (< desired)
    - ALB 5xx errors

[Step 9: CI/CD Pipeline]
22. CodePipeline Configuration:
    
    Stage 1: Source
    - GitHub/CodeCommit repository
    - Trigger on push to main branch
    
    Stage 2: Build
    - CodeBuild project
    - buildspec.yml:
      ```yaml
      version: 0.2
      phases:
        pre_build:
          commands:
            - echo Logging in to Amazon ECR...
            - aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REPO
            - COMMIT_HASH=$(echo $CODEBUILD_RESOLVED_SOURCE_VERSION | cut -c 1-7)
            - IMAGE_TAG=${COMMIT_HASH:=latest}
        build:
          commands:
            - echo Build started on `date`
            - docker build -t $IMAGE_REPO_NAME:$IMAGE_TAG .
            - docker tag $IMAGE_REPO_NAME:$IMAGE_TAG $ECR_REPO/$IMAGE_REPO_NAME:$IMAGE_TAG
        post_build:
          commands:
            - echo Pushing Docker image...
            - docker push $ECR_REPO/$IMAGE_REPO_NAME:$IMAGE_TAG
            - printf '[{"name":"frontend","imageUri":"%s"}]' $ECR_REPO/$IMAGE_REPO_NAME:$IMAGE_TAG > imagedefinitions.json
      artifacts:
        files: imagedefinitions.json
      ```
    
    Stage 3: Deploy
    - ECS deployment (Blue/Green or Rolling)
    - Deploy to frontend-service
    - Health checks before completion
    
23. Blue/Green Deployment:
    - CodeDeploy for ECS
    - Test traffic: 10% for 5 minutes
    - If successful: Shift 100%
    - If failed: Rollback automatically
    - Zero-downtime deployments

[Step 10: Security Best Practices]
24. Task IAM Roles:
    - Separate role per service
    - Least privilege access
    - Frontend role:
      • S3 read (for assets)
      • CloudWatch Logs write
    - Backend role:
      • DynamoDB read/write
      • S3 read/write
      • SES send email
      • Secrets Manager read
    
25. Secrets Management:
    - All credentials in Secrets Manager
    - Referenced in task definition
    - Automatic rotation enabled
    - Never in environment variables (plain text)
    
26. Network Security:
    - Containers in private subnets
    - No public IPs
    - Security groups restrict access
    - TLS for all inter-service communication
    
27. Image Scanning:
    - ECR scan on push
    - Continuous scanning
    - Block critical vulnerabilities
    - Integrate with Security Hub

Architecture Diagram:
Internet → Route 53 → CloudFront (optional)
                      ↓
               ALB (Public Subnet)
                      ↓
          ┌──────────┴──────────┐
    Frontend (3 tasks)    Backend API (5 tasks)
          │                     │
          └──────┬──────────────┘
                 ↓
        RDS / DynamoDB / ElastiCache
                 ↓
          Worker (2 tasks) ← SQS

Cost Estimate:
- Fargate: $0.04048/vCPU/hr + $0.004445/GB/hr
- Frontend (3 × 0.5 vCPU, 1 GB): ~$50/month
- Backend (5 × 1 vCPU, 2 GB): ~$170/month
- Worker (2 × 1 vCPU, 2 GB): ~$70/month
- ALB: ~$20/month + data processed
- NAT Gateway: ~$90/month (2 AZs)
- EFS: $0.30/GB/month
- Total: ~$400-600/month (base infrastructure)

Performance:
- Startup time: 30-60 seconds (Fargate)
- Scale-out: 1-2 minutes
- Zero-downtime deployments
- Auto-scales based on demand

Best Practices:
✅ Use Fargate for simplified management
✅ Implement health checks in containers
✅ Use Service Discovery for inter-service communication
✅ Enable Container Insights and X-Ray
✅ Implement CI/CD with automated testing
✅ Use secrets management (never env vars)
✅ Implement proper IAM roles per task
✅ Use private subnets with NAT Gateway
✅ Enable auto-scaling with appropriate metrics
✅ Implement blue/green deployments
```

---

## ☸️ SCENARIO 2: Kubernetes with EKS

### Configuration Matrix:

#### **Production EKS Cluster**
```
Services Required:
├─ EKS (Managed Kubernetes)
├─ EC2 or Fargate (Worker nodes)
├─ ALB Controller (Ingress)
├─ EBS CSI Driver (Persistent volumes)
├─ EFS CSI Driver (Shared storage)
├─ Cluster Autoscaler
├─ Metrics Server
├─ Prometheus + Grafana (Monitoring)
└─ Fluentd (Logging)

Configuration Steps:

[Step 1: Create EKS Cluster]
1. Using eksctl:
   ```yaml
   apiVersion: eksctl.io/v1alpha5
   kind: ClusterConfig
   
   metadata:
     name: myapp-eks-cluster
     region: us-east-1
     version: "1.28"
   
   vpc:
     cidr: 10.0.0.0/16
     nat:
       gateway: HighlyAvailable
   
   managedNodeGroups:
     - name: general-workloads
       instanceType: t3.medium
       minSize: 3
       maxSize: 10
       desiredCapacity: 3
       volumeSize: 20
       ssh:
         allow: false
       labels:
         role: general
       tags:
         nodegroup-role: general
       iam:
         withAddonPolicies:
           autoScaler: true
           ebs: true
           efs: true
           albIngress: true
           cloudWatch: true
     
     - name: compute-intensive
       instanceType: c5.xlarge
       minSize: 0
       maxSize: 20
       desiredCapacity: 2
       labels:
         role: compute
       taints:
         - key: compute
           value: "true"
           effect: NoSchedule
   
   fargateProfiles:
     - name: serverless-workloads
       selectors:
         - namespace: fargate-ns
   
   addons:
     - name: vpc-cni
       version: latest
     - name: coredns
       version: latest
     - name: kube-proxy
       version: latest
     - name: aws-ebs-csi-driver
       version: latest
   
   cloudWatch:
     clusterLogging:
       enableTypes: ["api", "audit", "authenticator", "controllerManager", "scheduler"]
   ```
   
2. Deploy cluster:
   ```bash
   eksctl create cluster -f cluster.yaml
   ```

[Step 2: Configure kubectl]
3. Update kubeconfig:
   ```bash
   aws eks update-kubeconfig \
     --region us-east-1 \
     --name myapp-eks-cluster
   ```

[Step 3: Install Add-ons]
4. AWS Load Balancer Controller:
   ```bash
   # Create IAM policy
   curl -o iam_policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/main/docs/install/iam_policy.json
   aws iam create-policy \
     --policy-name AWSLoadBalancerControllerIAMPolicy \
     --policy-document file://iam_policy.json
   
   # Create service account
   eksctl create iamserviceaccount \
     --cluster=myapp-eks-cluster \
     --namespace=kube-system \
     --name=aws-load-balancer-controller \
     --attach-policy-arn=arn:aws:iam::123456789012:policy/AWSLoadBalancerControllerIAMPolicy \
     --approve
   
   # Install controller
   helm repo add eks https://aws.github.io/eks-charts
   helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
     -n kube-system \
     --set clusterName=myapp-eks-cluster \
     --set serviceAccount.create=false \
     --set serviceAccount.name=aws-load-balancer-controller
   ```

5. Cluster Autoscaler:
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: cluster-autoscaler
     namespace: kube-system
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: cluster-autoscaler
     template:
       metadata:
         labels:
           app: cluster-autoscaler
       spec:
         serviceAccountName: cluster-autoscaler
         containers:
         - image: k8s.gcr.io/autoscaling/cluster-autoscaler:v1.28.0
           name: cluster-autoscaler
           command:
             - ./cluster-autoscaler
             - --v=4
             - --stderrthreshold=info
             - --cloud-provider=aws
             - --skip-nodes-with-local-storage=false
             - --expander=least-waste
             - --node-group-auto-discovery=asg:tag=k8s.io/cluster-autoscaler/enabled,k8s.io/cluster-autoscaler/myapp-eks-cluster
   ```

[Step 4: Deploy Application]
6. Deployment manifest:
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: backend-api
     namespace: production
   spec:
     replicas: 5
     selector:
       matchLabels:
         app: backend-api
     template:
       metadata:
         labels:
           app: backend-api
       spec:
         containers:
         - name: backend-api
           image: 123456789012.dkr.ecr.us-east-1.amazonaws.com/myapp/backend-api:v1.0.0
           ports:
           - containerPort: 8080
           env:
           - name: DATABASE_URL
             valueFrom:
               secretKeyRef:
                 name: db-credentials
                 key: url
           resources:
             requests:
               cpu: 500m
               memory: 1Gi
             limits:
               cpu: 1000m
               memory: 2Gi
           livenessProbe:
             httpGet:
               path: /health
               port: 8080
             initialDelaySeconds: 30
             periodSeconds: 10
           readinessProbe:
             httpGet:
               path: /ready
               port: 8080
             initialDelaySeconds: 5
             periodSeconds: 5
   ---
   apiVersion: v1
   kind: Service
   metadata:
     name: backend-api-service
     namespace: production
   spec:
     selector:
       app: backend-api
     ports:
     - protocol: TCP
       port: 80
       targetPort: 8080
     type: ClusterIP
   ---
   apiVersion: networking.k8s.io/v1
   kind: Ingress
   metadata:
     name: backend-api-ingress
     namespace: production
     annotations:
       kubernetes.io/ingress.class: alb
       alb.ingress.kubernetes.io/scheme: internet-facing
       alb.ingress.kubernetes.io/target-type: ip
       alb.ingress.kubernetes.io/certificate-arn: arn:aws:acm:us-east-1:123456789012:certificate/abc123
       alb.ingress.kubernetes.io/listen-ports: '[{"HTTP": 80}, {"HTTPS": 443}]'
       alb.ingress.kubernetes.io/actions.ssl-redirect: '{"Type": "redirect", "RedirectConfig": { "Protocol": "HTTPS", "Port": "443", "StatusCode": "HTTP_301"}}'
   spec:
     rules:
     - host: api.myapp.com
       http:
         paths:
         - path: /
           pathType: Prefix
           backend:
             service:
               name: backend-api-service
               port:
                 number: 80
   ```

[Step 5: Horizontal Pod Autoscaler]
7. HPA configuration:
   ```yaml
   apiVersion: autoscaling/v2
   kind: HorizontalPodAutoscaler
   metadata:
     name: backend-api-hpa
     namespace: production
   spec:
     scaleTargetRef:
       apiVersion: apps/v1
       kind: Deployment
       name: backend-api
     minReplicas: 5
     maxReplicas: 50
     metrics:
     - type: Resource
       resource:
         name: cpu
         target:
           type: Utilization
           averageUtilization: 70
     - type: Resource
       resource:
         name: memory
         target:
           type: Utilization
           averageUtilization: 80
     behavior:
       scaleDown:
         stabilizationWindowSeconds: 300
         policies:
         - type: Percent
           value: 50
           periodSeconds: 60
       scaleUp:
         stabilizationWindowSeconds: 0
         policies:
         - type: Percent
           value: 100
           periodSeconds: 15
         - type: Pods
           value: 4
           periodSeconds: 15
         selectPolicy: Max
   ```

Cost: $200-2,000/month (depends on node types and count)
```

---

## ⚡ SCENARIO 3: Serverless Microservices

### Configuration Matrix:

#### **Complete Serverless Architecture**
```
Services Required:
├─ Lambda (Compute)
├─ API Gateway (REST/HTTP API)
├─ DynamoDB (Database)
├─ S3 (Storage)
├─ SQS (Message queue)
├─ SNS (Notifications)
├─ EventBridge (Event routing)
├─ Step Functions (Orchestration)
├─ Cognito (Authentication)
└─ X-Ray (Tracing)

Configuration:

[Step 1: API Gateway]
1. Create HTTP API (cheaper, simpler):
   - Name: myapp-api
   - CORS: Enabled
   - Authorizer: JWT (Cognito)
   
2. Routes:
   - GET /users → Lambda: GetUsers
   - POST /users → Lambda: CreateUser
   - GET /users/{id} → Lambda: GetUser
   - PUT /users/{id} → Lambda: UpdateUser
   - DELETE /users/{id} → Lambda: DeleteUser

[Step 2: Lambda Functions]
3. GetUsers Function:
   - Runtime: Python 3.11
   - Memory: 512 MB
   - Timeout: 10 seconds
   - Environment variables:
     • TABLE_NAME=Users
   - IAM Role: DynamoDB read access
   - Code:
     ```python
     import json
     import boto3
     import os
     
     dynamodb = boto3.resource('dynamodb')
     table = dynamodb.Table(os.environ['TABLE_NAME'])
     
     def lambda_handler(event, context):
         try:
             response = table.scan(
                 Limit=100
             )
             
             return {
                 'statusCode': 200,
                 'headers': {
                     'Content-Type': 'application/json',
                     'Access-Control-Allow-Origin': '*'
                 },
                 'body': json.dumps(response['Items'])
             }
         except Exception as e:
             return {
                 'statusCode': 500,
                 'body': json.dumps({'error': str(e)})
             }
     ```

4. Provisioned Concurrency:
   - For latency-sensitive functions
   - Keep 5 instances warm
   - Auto-scaling: 5-50 based on invocations

[Step 3: Async Processing]
5. SQS Queue:
   - Queue name: order-processing-queue
   - Type: Standard
   - Visibility timeout: 300 seconds
   - Message retention: 4 days
   - DLQ: order-processing-dlq (after 3 failures)
   
6. Lambda Consumer:
   - Triggered by SQS
   - Batch size: 10
   - Concurrent executions: 5
   - Process orders asynchronously

[Step 4: Event-Driven Workflows]
7. EventBridge Rules:
   - Order Created → SNS (email notification)
   - Order Shipped → Lambda (update inventory)
   - Daily at 2 AM → Lambda (generate reports)
   
8. Step Functions State Machine:
   - Order fulfillment workflow
   - Coordinating multiple Lambda functions
   - Error handling and retries

Cost: $10-200/month (pay per use)
```

---

## 🚪 SCENARIO 4-6: Summaries

### API Gateway Configurations
- REST API vs HTTP API
- Custom authorizers
- Usage plans and API keys
- Request/response transformations
- Caching strategies

### Event-Driven Architecture
- EventBridge event buses
- SNS topics and subscriptions
- SQS queues (Standard vs FIFO)
- Lambda event source mappings
- Dead letter queues

### Service Mesh (App Mesh)
- Virtual nodes and services
- Traffic routing
- Circuit breakers
- Retry policies
- mTLS encryption

---

## 📊 Quick Comparison

| **Architecture** | **Management** | **Cost** | **Complexity** | **Best For** |
|------------------|----------------|----------|----------------|--------------|
| ECS Fargate | Low | $$ | Low | Microservices, containers |
| ECS EC2 | Medium | $ | Medium | Cost optimization |
| EKS | High | $$$ | High | Kubernetes workloads |
| Serverless | None | $ | Low | Event-driven, variable load |

---

**Next**: See `25-Config-Hybrid-Migration.md` for hybrid and migration scenarios
