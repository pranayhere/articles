# AWS API Gateway Proxy for SQS (Simple Queue Service)

If you came across a situation where you need to process API Request asynchronously and you want to add a queue in your application, you landed to right place.

This article shows how to Integrate Amazon API Gateway as proxy for SQS (Simple Queue Service).

### 1. Create a SQS queue ###

1.1. Open AWS console in services navigate to Simple Queue Service. <br />
1.2. Click on "Create New Queue" <br />
1.3. Give queue a name, in our case it is "sqs-lambda-demo" and hit "Create Queue" <br />
1.4. Remember Queue URL (AWS-ACCT-ID/queue-name) and Queue ARN (AWS-ACCT-ID:queue-name)
![alt text](images/sqs_queue.png)

### 2. Create IAM Policy ###

We will create IAM Policy and Role for AWS API Gateway to push Request Message to Queue.
2.1. Select IAM, Navigate to "policies" and click on "Create Policy"
2.2. Open JSON editor and add the following policy
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": "sqs:SendMessage",
            "Resource": "arn:aws:sqs:ap-southeast-1:123456789:sqs-lambda-demo"
        }
    ]
} 
```

2.3. click on "Review Policy", name policy as "apig-sqs-send-msg-policy" and hit "Create Policy"

### 3. Create IAM Role ###
