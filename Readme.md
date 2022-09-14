# USECASE-1 ( SNS,SQS,DynamoDB,S3,Python)

# What you will be learn this usecase :

In our Daily life we are ordering food/grocery items/ clothes on an online platform  . We will order the items in the support platform with zomato, big basket or some different app  . Once we order a pallrell notification will be delivered to the merchant and order platform. Then they will consider the orders and will process further, How the notification happens and how they will  maintain the database . We will discuss this use case . 


# Architecture

![Watch the image](/Sree-usecase-1.png)

# Steps

- Step 1: Create SNS and SQS then Subscribe to Amazon SNS topic from SQS ( SNS --> SQS)
- Step 2: Create Lambda function , DynamoDB and integrated with SNS & SQS ( SNS --> SQS --> Lambda --> DynamoDB)
- Step 3: Trigger the Lambda function using of SQS 
- step 4: Create SQS, Lambda function and integrated to S3 ( SNS -->SQS --> Lambda --> S3)


#

# Step 1: Create SNS and SQS then Subscribe to Amazon SNS topic from SQS ( SNS --> SQS)

- Go to Amazon SNS --> Create topic --> Select standard --> Topic Name --> Create Topic
- Go to Amazon SQS --> Create queue --> Select Standard --> Queue Name --> Create Queue
- Open the Queue --> Go to SNS Subscriptions  --> Click Subscribe to Amazon SNS topic --> Select Amazon topic name --> Save
- Goto SNS --> Select Topic name --> Publish message --> Give subject and also Message like below
```
{
    "customer": 100,
    
    "items": [
        {
            "name": "produto 1", "amount": 10},
        {
            "name": "produto 2", "amount": 14.4}
    ]
}
```

-  Go to SQS --> Select queue name --> Click Send and Recive Message --> Poll for message --> click message --> select Body  --> OUTPUT

# Step 2: Create Lambda function , DynamoDB and integrated with SNS & SQS ( SNS --> SQS --> Lambda --> DynamoDB)


- Create Lambda --> Select Function name --> Runtime Python 3.6--> create function
- Click Run --> Give Event name & below code -- > Save then Run test it wil dispaly 200  output

```
{
  
  "Fooditem": "Product1"
  "Area": "Chennai"
}
```  
- Click DynamoDB --> create table --> Tablename "orders" --> Primary key "id"  --> create
- Goto Lambdo --> select the lambda function --> goto configuration --> permission --> clieck rolename --> Add polices --> AmazonDynamoDBFullAccess , AmazonSQS full access --> attached
- Goto Lambda code --> update like below --> Click deploy -->click Run 


```

import json
import uuid
import boto3
from decimal import Decimal


def lambda_handler(event, context):
    print(event)

    print("###############")

    dynamodb = boto3.resource("dynamodb")
    table = dynamodb.Table("orders")

    table.put_item(
        Item={
            "id": str(uuid.uuid4()),
            
             "content": {
                 "customer": 3,
                 "items": [
                     {"name": "teste 1", "amount": 10},
                     {"name": "teste 2", "amount": Decimal("13.4")},
                 ]
             }
        }
    )

    return {"statusCode": 200, "body": json.dumps("Hello from Lambda!")}
```

- You can able to see Output on  dynamodb Table 


# Step 3 : Trigger the Lambda function using of SQS

- Go to Lambda --> open the Function -->Add the trigger --> SQS --> provide sqs details --> click ADD
- Update Lambda function code like below  : --> Then Click Deploy

```
import json
import uuid
import boto3
from decimal import Decimal


def lambda_handler(event, context):
    print(event)

    body = json.loads(event["Records"][0]["body"])
    message = json.loads(body["Message"], parse_float=Decimal)
    print(body["Message"])
    print(message)
    print(type(message))

    dynamodb = boto3.resource("dynamodb")
    table = dynamodb.Table("orders")

    table.put_item(
        Item={
            "id": str(uuid.uuid4()),
            "content": message
            # "content": {
            #     "customer": 3,
            #     "items": [
            #         {"name": "teste 1", "amount": 10},
            #         {"name": "teste 2", "amount": Decimal("13.4")},
            #     ]
            # }
        }
    )

    return {"statusCode": 200, "body": json.dumps("Hello from Lambda!")}
```

- click on test tab --> test event action (create new event) , event name and update tempalte area as SQS then update below code --> save

```
{
    "Records": [
        {
            "messageId": "19dd0b57-b21e-4ac1-bd88-01bbb068cb78",
            "receiptHandle": "MessageReceiptHandle",
            "body": "{\"customer\": 2, \"age\": 27, \"phone\": 5516999999999, \"items\": [{\"name\": \"produto 1\", \"amount\": 10},{\"name\": \"produto 2\",\"amount\": 14.4}]}",
            "attributes": {
                "ApproximateReceiveCount": "1",
                "SentTimestamp": "1523232000000",
                "SenderId": "123456789012",
                "ApproximateFirstReceiveTimestamp": "1523232000001"
            },
            "messageAttributes": {},
            "md5OfBody": "{{{md5_of_body}}}",
            "eventSource": "aws:sqs",
            "eventSourceARN": "arn:aws:sqs:us-east-1:123456789012:MyQueue",
            "awsRegion": "us-east-1"
        }
    ]
}

```
- Now go to SNS and publish the msg it will automatcally save in dynamodb 

# Step 4: Create SQS, Lambda function and integrated to S3 ( SNS -->SQS --> Lambda --> s3)

- Create new lambda function --> Select  runtime python 3.6 --> Create  --> configuration --> permission --> add policy AmazonSQSFULLAccess , AmazonS3fullaccess --> save

- Update code like below code in Lambda --> deploy -->Test use default config  

```
import json
import boto3
from datetime import datetime


def lambda_handler(event, context):
    # TODO implement

    s3 = boto3.client("s3")

    body = json.loads(event["Records"][0]["body"])
    message = body["Message"]

    s3.put_object(
        Body=message,
        Bucket="test9999",
        Key=f"{datetime.now().strftime('%Y-%m-%d %H:%M:%S')}.txt",
    )

    return {"statusCode": 200, "body": json.dumps("Hello from Lambda!")}

```

- Create another SQS  same like above then  subscribe to existing SNS

- Create s3 bucket 
        bucket name --> test9999 remaing default config

- Go to Lambda2 --> add Trigger to SQS2 

- Now you can and publish the msg on sns it will publish both s3 and dynamo db

```
{
    "customer": 2,
    "age": 28,
    "phone": 5516999999999,
    "items": [
        {
            "name": "produto 1",
            "amount": 10
        },
        {
            "name": "produto 2",
            "amount": 14.4
        }
    ]
}

```


