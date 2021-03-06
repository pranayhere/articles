# API Proxy for SQS (Simple Queue Service)

If you want to process API Request asynchronously or add a queue in your application architecture, you landed to the right place.

This article shows how to Integrate Amazon API Gateway as proxy for SQS (Simple Queue Service).

### 1. Create a SQS queue ###

1.1. Open AWS console in services navigate to Simple Queue Service. <br />
1.2. Click on "Create New Queue" <br />
1.3. Give queue a name, in our case it is "sqs-lambda-demo" and hit "Create Queue" <br />
1.4. Remember Queue URL (AWS-ACCT-ID/queue-name) and Queue ARN (AWS-ACCT-ID:queue-name) <br />
![alt text](images/sqs_queue.png)

### 2. Create IAM Policy ###

We will create IAM Policy and Role for AWS API Gateway to push Request Message to Queue. <br />
2.1. Select IAM, Navigate to "policies" and click on "Create Policy"  <br />
2.2. Open JSON editor and add the following policy, click on "Review Policy", name policy as "apig-sqs-send-msg-policy" and hit "Create Policy".  <br />

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

### 3. Create IAM Role ###

3.1. Select IAM, Navigate to "Roles" and click on "Create Role".  <br />
3.2. Select "API Gateway" and click "Next:Permissions".  <br />
![alt text](images/role_apig.png)  <br />
3.3. Give a name to the role you created. In my case, it's "apig-sqs-send-msg-role", notice that only attached policy is "AmazonAPIGatewayPushToCloudWatchLogs".  <br />
![alt text](images/save_role.png)  <br />
3.4. Once role is created, edit the role and attach "apig-sqs-send-msg-policy"  <br />
![alt text](images/attach_policy_to_role.png)  <br />


### 4. Create an API Structure ###

4.1. Select API Gateway, click on "Create API", name api as "SQSProxy" and hit "Create".  <br />
![alt text](images/api_create.png)  <br />
4.2. Click on "Action" and "create resource" and resource as "v1".  <br />
4.3. Click on "v1", and hit "action > create resource" to create "enqueue". <br />
4.4. Click on "enqueue" and hit "action > create method" and from dropdown select "POST". <br />
![alt text](images/resource_structure.png)  <br />

### 5. Integrate the API with SQS ###

5.1. Click on the "POST" method, in "integration type" click on "AWS service", provide region and AWS servie as "SQS". <br />
5.2. Set Path override to Queue Path created in step 1. <br />
5.3. Set execution role to the role created in step 3. and hit "save". <br />
![alt text](images/post_api.png)  <br />

### 6. Modify API Request ###
6.1. Click on "Integrate Request" <br />
![alt text](images/api_req_flow.png)  <br />
6.2. Set HTTP header with Name as "Content-Type" and Mapped from as "'application/x-www-form-urlencoded'" <br />
6.3. In "Request body passthrough" select never and click on "add mapping template", name it as "application/json" and add the following snippet "Action=SendMessage&MessageBody=$input.body". This step will transform your request body to the one accepted by SQS. <br />
![alt text](images/api_intg.png)  <br />

### 7. Test And Deploy ###
7.1. AWS api gateway provides functionality to test the API. <br />
7.2. In the request body, add the test data. <br />
```json
{
    "data" : "test"
}
```
7.3. If you followed all the steps correctly, you should get 200 status. yey!!! <br />
7.4. click on the resource "enqueue" and from "actions" select "Deploy Api". <br />
7.5. Select or create new stage and deploy the Api. <br />

That's it! Test your API and deploy in production.