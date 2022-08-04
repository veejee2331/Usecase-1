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
