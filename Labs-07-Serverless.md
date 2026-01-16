# AWS SAA-C03 Hands-On Labs: Serverless & Application Integration

## 📋 Overview
These labs cover Lambda advanced features, API Gateway, Step Functions, SQS, SNS, and EventBridge.

**Estimated Time:** 4-5 hours total  
**Cost Estimate:** $1-3 (mostly free tier)  
**Free Tier Eligible:** Yes

---

## Lab 7.1: Advanced Lambda Patterns

### Objectives
- Configure Lambda with VPC access
- Implement Lambda destinations
- Use provisioned concurrency
- Create Lambda container images

### Steps

#### Part A: Lambda in VPC

**Step 1: Create VPC Resources**
```
Using existing lab-vpc or create:
1. VPC with private subnets
2. NAT Gateway (for internet access from Lambda)
3. Security group for Lambda: "lab-lambda-sg"
   - Outbound: All traffic
```

**Step 2: Create VPC Lambda Function**
```
1. Lambda → Create function
2. Name: "lab-vpc-lambda"
3. Runtime: Python 3.12
4. Advanced settings:
   - Enable VPC: Yes
   - VPC: lab-vpc
   - Subnets: Private subnets only
   - Security group: lab-lambda-sg
5. Create function
```

**Step 3: Add VPC Lambda Code**
```python
import json
import urllib.request
import socket

def lambda_handler(event, context):
    # Test DNS resolution
    try:
        ip = socket.gethostbyname('google.com')
        dns_works = True
    except:
        dns_works = False
        ip = None
    
    # Test internet access (requires NAT)
    try:
        response = urllib.request.urlopen('https://api.ipify.org', timeout=5)
        public_ip = response.read().decode()
        internet_works = True
    except Exception as e:
        public_ip = str(e)
        internet_works = False
    
    return {
        'statusCode': 200,
        'body': json.dumps({
            'dns_resolution': dns_works,
            'resolved_ip': ip,
            'internet_access': internet_works,
            'lambda_public_ip': public_ip
        })
    }
```

**Step 4: Test VPC Lambda**
```
1. Create test event
2. Test function
3. Verify internet access via NAT Gateway
4. Note: Cold start is longer with VPC
```

#### Part B: Lambda Destinations

**Step 5: Create Destination Queues**
```
1. Go to SQS → Create queue
2. Create two queues:
   - lab-lambda-success (Standard)
   - lab-lambda-failure (Standard)
```

**Step 6: Create Lambda with Destinations**
```python
# Function: lab-destination-demo
import json
import random

def lambda_handler(event, context):
    # Randomly succeed or fail
    if random.random() < 0.5:
        return {
            'statusCode': 200,
            'body': json.dumps({'message': 'Success!'})
        }
    else:
        raise Exception("Random failure occurred!")
```

**Step 7: Configure Destinations**
```
1. Lambda → lab-destination-demo → Configuration
2. Destinations → Add destination
3. On success:
   - Condition: On success
   - Destination type: SQS queue
   - Destination: lab-lambda-success
4. On failure:
   - Condition: On failure
   - Destination type: SQS queue
   - Destination: lab-lambda-failure
5. Save
```

**Step 8: Test Asynchronously**
```
1. Configure test event
2. Test type: Asynchronous invoke
3. Invoke multiple times
4. Check SQS queues for messages
```

#### Part C: Provisioned Concurrency

**Step 9: Create Version and Alias**
```
1. Lambda → lab-destination-demo
2. Actions → Publish new version
3. Description: "v1"
4. Publish

5. Aliases → Create alias
6. Name: "prod"
7. Version: 1
8. Create
```

**Step 10: Configure Provisioned Concurrency**
```
1. Select alias "prod"
2. Configuration → Provisioned concurrency
3. Edit
4. Provisioned concurrency: 2
5. Save
6. Wait for status: Ready
```

**Step 11: Test Cold Start Reduction**
```
# Compare invoke times:
# - $LATEST (cold start possible)
# - prod alias with provisioned concurrency (warm)
```

### Cleanup for Lab 7.1
```
1. Delete provisioned concurrency configuration
2. Delete Lambda functions
3. Delete SQS queues
4. Delete Lambda security group
```

---

## Lab 7.2: API Gateway REST API

### Objectives
- Create REST API with multiple resources
- Configure request/response transformations
- Implement API keys and usage plans
- Enable caching

### Steps

#### Part A: Create REST API

**Step 1: Create API**
```
1. API Gateway → Create API
2. REST API → Build
3. Protocol: REST
4. Create new API: New API
5. API name: "lab-rest-api"
6. Endpoint type: Regional
7. Create API
```

**Step 2: Create Resources**
```
1. Actions → Create Resource
2. Resource name: "users"
3. Resource path: /users
4. Enable API Gateway CORS: Yes
5. Create Resource

6. Select /users
7. Actions → Create Resource
8. Resource name: "userId"
9. Resource path: {userId}
10. Create Resource
```

**Step 3: Create Methods**
```
GET /users:
1. Select /users → Actions → Create Method
2. Method type: GET
3. Integration type: Lambda Function
4. Lambda function: Create new or use existing
5. Save

POST /users:
1. Create Method → POST
2. Integration type: Lambda Function
3. Save

GET /users/{userId}:
1. Select /{userId}
2. Create Method → GET
3. Save
```

**Step 4: Create Backend Lambda**
```python
# lab-users-api Lambda
import json

# Mock database
users = {
    "1": {"id": "1", "name": "John Doe", "email": "john@example.com"},
    "2": {"id": "2", "name": "Jane Smith", "email": "jane@example.com"}
}

def lambda_handler(event, context):
    http_method = event.get('httpMethod', '')
    path = event.get('path', '')
    path_params = event.get('pathParameters') or {}
    body = event.get('body')
    
    # GET /users
    if http_method == 'GET' and path == '/users':
        return {
            'statusCode': 200,
            'headers': {'Content-Type': 'application/json'},
            'body': json.dumps(list(users.values()))
        }
    
    # GET /users/{userId}
    if http_method == 'GET' and 'userId' in path_params:
        user_id = path_params['userId']
        if user_id in users:
            return {
                'statusCode': 200,
                'headers': {'Content-Type': 'application/json'},
                'body': json.dumps(users[user_id])
            }
        return {
            'statusCode': 404,
            'body': json.dumps({'error': 'User not found'})
        }
    
    # POST /users
    if http_method == 'POST' and path == '/users':
        try:
            new_user = json.loads(body)
            new_id = str(len(users) + 1)
            new_user['id'] = new_id
            users[new_id] = new_user
            return {
                'statusCode': 201,
                'body': json.dumps(new_user)
            }
        except:
            return {
                'statusCode': 400,
                'body': json.dumps({'error': 'Invalid request'})
            }
    
    return {
        'statusCode': 400,
        'body': json.dumps({'error': 'Unsupported method'})
    }
```

**Step 5: Deploy API**
```
1. Actions → Deploy API
2. Deployment stage: [New Stage]
3. Stage name: "dev"
4. Deploy
5. Note the Invoke URL
```

**Step 6: Test API**
```bash
# Get all users
curl https://[api-id].execute-api.us-east-1.amazonaws.com/dev/users

# Get specific user
curl https://[api-id].execute-api.us-east-1.amazonaws.com/dev/users/1

# Create user
curl -X POST https://[api-id].execute-api.us-east-1.amazonaws.com/dev/users \
  -H "Content-Type: application/json" \
  -d '{"name":"New User","email":"new@example.com"}'
```

#### Part B: API Keys and Usage Plans

**Step 7: Create API Key**
```
1. API Gateway → API Keys → Create API key
2. Name: "lab-api-key"
3. Auto Generate: Yes
4. Save
5. Note the API key value
```

**Step 8: Create Usage Plan**
```
1. Usage Plans → Create usage plan
2. Name: "lab-basic-plan"
3. Throttling:
   - Rate: 100 requests/second
   - Burst: 200 requests
4. Quota: 10000 requests/month
5. Next
6. Add API stage: lab-rest-api / dev
7. Next
8. Add API key: lab-api-key
9. Done
```

**Step 9: Require API Key**
```
1. Go to API → Resources
2. Select GET /users
3. Method Request
4. Settings → API Key Required: true
5. Save
6. Redeploy API
```

**Step 10: Test with API Key**
```bash
# Without key - should fail
curl https://[api-id].execute-api.us-east-1.amazonaws.com/dev/users

# With key - should succeed
curl https://[api-id].execute-api.us-east-1.amazonaws.com/dev/users \
  -H "x-api-key: [your-api-key]"
```

#### Part C: API Caching

**Step 11: Enable Caching**
```
1. Go to API → Stages → dev
2. Settings tab
3. Enable API cache: Yes
4. Cache capacity: 0.5 GB
5. Cache TTL: 300 seconds
6. Save Changes
```

**Step 12: Test Caching**
```bash
# First request - cache miss
time curl https://[api-id].execute-api.us-east-1.amazonaws.com/dev/users

# Second request - cache hit (faster)
time curl https://[api-id].execute-api.us-east-1.amazonaws.com/dev/users
```

### Cleanup for Lab 7.2
```
1. Disable caching
2. Delete API
3. Delete usage plans
4. Delete API keys
5. Delete Lambda functions
```

---

## Lab 7.3: AWS Step Functions

### Objectives
- Create state machine
- Implement different state types
- Handle errors and retries
- Integrate with Lambda

### Steps

#### Part A: Create Helper Functions

**Step 1: Create Lambda Functions**
```python
# Function 1: lab-validate-order
import json

def lambda_handler(event, context):
    order = event.get('order', {})
    
    if not order.get('items') or len(order['items']) == 0:
        raise ValueError("Order must have items")
    
    if not order.get('customerId'):
        raise ValueError("Order must have customerId")
    
    total = sum(item.get('price', 0) * item.get('quantity', 1) 
                for item in order['items'])
    
    return {
        'orderId': order.get('orderId', 'ORD-001'),
        'customerId': order['customerId'],
        'total': total,
        'status': 'VALIDATED'
    }
```

```python
# Function 2: lab-process-payment
import json
import random

def lambda_handler(event, context):
    order_id = event.get('orderId')
    total = event.get('total', 0)
    
    # Simulate payment processing
    if random.random() < 0.1:  # 10% failure rate
        raise Exception("Payment gateway timeout")
    
    return {
        'orderId': order_id,
        'paymentId': f"PAY-{random.randint(1000, 9999)}",
        'amount': total,
        'status': 'PAYMENT_COMPLETED'
    }
```

```python
# Function 3: lab-ship-order
import json

def lambda_handler(event, context):
    order_id = event.get('orderId')
    
    return {
        'orderId': order_id,
        'trackingNumber': f"TRK-{order_id[-3:]}-001",
        'status': 'SHIPPED'
    }
```

```python
# Function 4: lab-notify-customer
import json

def lambda_handler(event, context):
    order_id = event.get('orderId')
    status = event.get('status')
    
    print(f"Sending notification for order {order_id}: {status}")
    
    return {
        'orderId': order_id,
        'notificationSent': True,
        'status': 'NOTIFICATION_SENT'
    }
```

#### Part B: Create State Machine

**Step 2: Create State Machine**
```
1. Step Functions → State machines → Create state machine
2. Choose authoring method: Write workflow in code
3. Type: Standard
```

**Step 3: Define State Machine**
```json
{
  "Comment": "Order Processing Workflow",
  "StartAt": "ValidateOrder",
  "States": {
    "ValidateOrder": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:us-east-1:ACCOUNT:function:lab-validate-order",
      "Next": "ProcessPayment",
      "Catch": [
        {
          "ErrorEquals": ["ValueError"],
          "Next": "OrderFailed",
          "ResultPath": "$.error"
        }
      ]
    },
    "ProcessPayment": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:us-east-1:ACCOUNT:function:lab-process-payment",
      "Retry": [
        {
          "ErrorEquals": ["States.ALL"],
          "IntervalSeconds": 2,
          "MaxAttempts": 3,
          "BackoffRate": 2
        }
      ],
      "Next": "PaymentChoice",
      "Catch": [
        {
          "ErrorEquals": ["States.ALL"],
          "Next": "OrderFailed",
          "ResultPath": "$.error"
        }
      ]
    },
    "PaymentChoice": {
      "Type": "Choice",
      "Choices": [
        {
          "Variable": "$.status",
          "StringEquals": "PAYMENT_COMPLETED",
          "Next": "ParallelProcessing"
        }
      ],
      "Default": "OrderFailed"
    },
    "ParallelProcessing": {
      "Type": "Parallel",
      "Branches": [
        {
          "StartAt": "ShipOrder",
          "States": {
            "ShipOrder": {
              "Type": "Task",
              "Resource": "arn:aws:lambda:us-east-1:ACCOUNT:function:lab-ship-order",
              "End": true
            }
          }
        },
        {
          "StartAt": "NotifyCustomer",
          "States": {
            "NotifyCustomer": {
              "Type": "Task",
              "Resource": "arn:aws:lambda:us-east-1:ACCOUNT:function:lab-notify-customer",
              "End": true
            }
          }
        }
      ],
      "Next": "OrderComplete"
    },
    "OrderComplete": {
      "Type": "Succeed"
    },
    "OrderFailed": {
      "Type": "Fail",
      "Error": "OrderProcessingFailed",
      "Cause": "Order processing failed. See error details."
    }
  }
}
```

**Step 4: Configure and Create**
```
1. Name: "lab-order-processing"
2. Permissions: Create new role
3. Logging: ALL (CloudWatch)
4. Create state machine
```

#### Part C: Execute State Machine

**Step 5: Start Execution**
```json
// Input:
{
  "order": {
    "orderId": "ORD-12345",
    "customerId": "CUST-001",
    "items": [
      {"name": "Widget A", "price": 29.99, "quantity": 2},
      {"name": "Widget B", "price": 49.99, "quantity": 1}
    ]
  }
}
```

**Step 6: View Execution**
```
1. View Graph inspector
2. Watch state transitions
3. View input/output for each state
4. Check execution history
```

**Step 7: Test Failure Scenarios**
```json
// Test validation failure
{
  "order": {
    "orderId": "ORD-FAIL",
    "items": []
  }
}

// Watch it fail at validation
```

### Cleanup for Lab 7.3
```
1. Delete state machine
2. Delete Lambda functions
3. Delete IAM role created for Step Functions
4. Delete CloudWatch log groups
```

---

## Lab 7.4: Amazon SQS

### Objectives
- Create standard and FIFO queues
- Configure dead-letter queues
- Implement message processing

### Steps

#### Part A: Standard Queue

**Step 1: Create Standard Queue**
```
1. SQS → Create queue
2. Type: Standard
3. Name: "lab-orders-queue"
4. Configuration:
   - Visibility timeout: 30 seconds
   - Message retention: 4 days
   - Maximum message size: 256 KB
5. Access policy: Basic
6. Create queue
```

**Step 2: Send Messages**
```
1. Select queue → Send and receive messages
2. Message body:
   {"orderId": "ORD-001", "product": "Widget", "quantity": 5}
3. Send message

4. Send multiple messages using CLI:
aws sqs send-message \
  --queue-url https://sqs.us-east-1.amazonaws.com/[ACCOUNT]/lab-orders-queue \
  --message-body '{"orderId": "ORD-002", "product": "Gadget", "quantity": 3}'
```

**Step 3: Receive Messages**
```bash
# Receive messages
aws sqs receive-message \
  --queue-url https://sqs.us-east-1.amazonaws.com/[ACCOUNT]/lab-orders-queue \
  --max-number-of-messages 10 \
  --wait-time-seconds 20

# Delete message after processing
aws sqs delete-message \
  --queue-url https://sqs.us-east-1.amazonaws.com/[ACCOUNT]/lab-orders-queue \
  --receipt-handle [RECEIPT_HANDLE]
```

#### Part B: Dead-Letter Queue

**Step 4: Create DLQ**
```
1. Create queue
2. Type: Standard
3. Name: "lab-orders-dlq"
4. Create queue
```

**Step 5: Configure Main Queue DLQ**
```
1. Select lab-orders-queue
2. Edit → Dead-letter queue
3. Enable: Yes
4. Queue ARN: Select lab-orders-dlq
5. Maximum receives: 3
6. Save
```

**Step 6: Test DLQ**
```python
# Lambda that fails - triggers DLQ after 3 attempts
import json

def lambda_handler(event, context):
    for record in event['Records']:
        body = json.loads(record['body'])
        print(f"Processing: {body}")
        
        # Simulate failure
        raise Exception("Processing failed!")
```

#### Part C: FIFO Queue

**Step 7: Create FIFO Queue**
```
1. Create queue
2. Type: FIFO
3. Name: "lab-orders-queue.fifo" (must end in .fifo)
4. Configuration:
   - Content-based deduplication: Enable
5. Create queue
```

**Step 8: Send FIFO Messages**
```bash
# Send with message group
aws sqs send-message \
  --queue-url https://sqs.us-east-1.amazonaws.com/[ACCOUNT]/lab-orders-queue.fifo \
  --message-body '{"orderId": "ORD-001"}' \
  --message-group-id "customer-123" \
  --message-deduplication-id "unique-id-001"
```

#### Part D: Lambda Integration

**Step 9: Create SQS-Triggered Lambda**
```python
# lab-sqs-processor
import json

def lambda_handler(event, context):
    processed = 0
    
    for record in event['Records']:
        body = json.loads(record['body'])
        print(f"Processing order: {body.get('orderId')}")
        
        # Process message
        # ... your processing logic ...
        
        processed += 1
    
    return {
        'statusCode': 200,
        'body': json.dumps(f'Processed {processed} messages')
    }
```

**Step 10: Add SQS Trigger**
```
1. Lambda → lab-sqs-processor → Add trigger
2. Select SQS
3. Queue: lab-orders-queue
4. Batch size: 10
5. Enable trigger
6. Add
```

### Cleanup for Lab 7.4
```
1. Remove Lambda triggers
2. Delete Lambda functions
3. Delete SQS queues (including DLQ)
```

---

## Lab 7.5: Amazon SNS

### Objectives
- Create topics with multiple subscribers
- Implement message filtering
- Fan-out pattern with SQS

### Steps

#### Part A: Create Topic

**Step 1: Create SNS Topic**
```
1. SNS → Topics → Create topic
2. Type: Standard
3. Name: "lab-order-events"
4. Create topic
```

**Step 2: Add Subscriptions**
```
Email subscription:
1. Create subscription
2. Protocol: Email
3. Endpoint: your-email@example.com
4. Create subscription
5. Confirm via email

Lambda subscription:
1. Create subscription
2. Protocol: AWS Lambda
3. Endpoint: Select Lambda function ARN
4. Create subscription
```

#### Part B: Message Filtering

**Step 3: Create Filtered Subscriptions**
```
Create SQS queues:
- lab-high-priority-orders
- lab-low-priority-orders

Subscribe with filters:

High priority:
1. Create subscription
2. Protocol: SQS
3. Endpoint: lab-high-priority-orders ARN
4. Subscription filter policy:
{
  "priority": ["high"]
}

Low priority:
1. Create subscription
2. Filter policy:
{
  "priority": ["low"]
}
```

**Step 4: Publish with Attributes**
```bash
# Publish high priority
aws sns publish \
  --topic-arn arn:aws:sns:us-east-1:[ACCOUNT]:lab-order-events \
  --message '{"orderId": "ORD-HIGH-001"}' \
  --message-attributes '{"priority": {"DataType": "String", "StringValue": "high"}}'

# Publish low priority
aws sns publish \
  --topic-arn arn:aws:sns:us-east-1:[ACCOUNT]:lab-order-events \
  --message '{"orderId": "ORD-LOW-001"}' \
  --message-attributes '{"priority": {"DataType": "String", "StringValue": "low"}}'
```

**Step 5: Verify Filtering**
```
1. Check lab-high-priority-orders queue
   - Should only have high priority messages
2. Check lab-low-priority-orders queue
   - Should only have low priority messages
```

#### Part C: Fan-Out Pattern

**Step 6: Create Multiple Processors**
```
Architecture:
SNS Topic → SQS Queue 1 → Lambda Processor 1
         → SQS Queue 2 → Lambda Processor 2
         → SQS Queue 3 → Lambda Processor 3

1. Create 3 SQS queues
2. Subscribe all to SNS topic
3. Publish single message
4. All queues receive the message
```

### Cleanup for Lab 7.5
```
1. Delete subscriptions
2. Delete SNS topics
3. Delete SQS queues
4. Delete Lambda functions
```

---

## Lab 7.6: Amazon EventBridge

### Objectives
- Create event bus and rules
- Schedule events
- Cross-account events

### Steps

#### Part A: Custom Event Bus

**Step 1: Create Event Bus**
```
1. EventBridge → Event buses → Create event bus
2. Name: "lab-app-events"
3. Create
```

**Step 2: Create Rule**
```
1. EventBridge → Rules → Create rule
2. Name: "lab-order-created-rule"
3. Event bus: lab-app-events
4. Rule type: Rule with an event pattern
5. Next
```

**Step 3: Define Event Pattern**
```json
{
  "source": ["lab.orders"],
  "detail-type": ["Order Created"],
  "detail": {
    "status": ["NEW"]
  }
}
```

**Step 4: Add Target**
```
1. Target type: AWS service
2. Select target: Lambda function
3. Function: lab-order-processor
4. Next
5. Create rule
```

**Step 5: Send Custom Event**
```bash
aws events put-events --entries '[
  {
    "EventBusName": "lab-app-events",
    "Source": "lab.orders",
    "DetailType": "Order Created",
    "Detail": "{\"orderId\": \"ORD-001\", \"status\": \"NEW\", \"amount\": 99.99}"
  }
]'
```

#### Part B: Scheduled Events

**Step 6: Create Schedule Rule**
```
1. EventBridge → Rules → Create rule
2. Name: "lab-daily-report"
3. Event bus: default
4. Schedule:
   - rate(1 day)
   OR
   - cron(0 8 * * ? *)  # 8 AM daily
5. Target: Lambda function
6. Create rule
```

#### Part C: Archive and Replay

**Step 7: Create Archive**
```
1. EventBridge → Archives → Create archive
2. Name: "lab-events-archive"
3. Event bus: lab-app-events
4. Event pattern: Match all events
5. Retention period: 30 days
6. Create
```

**Step 8: Replay Events**
```
1. Archives → Select archive
2. Start replay
3. Time range: Last 24 hours
4. Destination: lab-app-events
5. Start replay
```

### Cleanup for Lab 7.6
```
1. Delete EventBridge rules
2. Delete archives
3. Delete event buses
4. Delete Lambda functions
```

---

## 📝 Lab Summary & Key Takeaways

### What You Learned:
1. **Lambda Advanced**: VPC integration, destinations, provisioned concurrency
2. **API Gateway**: REST APIs, API keys, caching
3. **Step Functions**: State machines, error handling, parallel execution
4. **SQS**: Standard/FIFO queues, DLQ, Lambda integration
5. **SNS**: Topics, subscriptions, message filtering
6. **EventBridge**: Custom events, scheduling, archives

### Exam-Relevant Points:
- Lambda VPC: Needs NAT for internet, adds cold start
- API Gateway: 29 second timeout, caching reduces Lambda calls
- Step Functions: Standard (long-running), Express (high-volume)
- SQS Standard: At-least-once, unlimited throughput
- SQS FIFO: Exactly-once, 300 msg/sec (3000 with batching)
- SNS: Push model, fan-out pattern
- EventBridge: Advanced event routing, cross-account

---

**Next:** [Labs-08-Containers](./Labs-08-Containers.md)
