# AWS-Serverless-Microservices---Lambda-Power-Tuning

## **Overview**

This project sets up a serverless RESTful API through Amazon API Gateway, which invokes a AWS Lambda function to execute CRUD (Create, Read, Update, Delete) operations on a DynamoDB table. This architecture demonstrates a Serverless Microservice design aimed at achieving high scalability, cost efficiency and with minimal operational overhead. Optimization has been done with Lambda Power Tuning Tool.

## **Architecture Components**

1.	Amazon API Gateway - API Gateway is the entry point for client requests. It acts as a "front door" for your application.
2.	AWS Lambda - Lambda functions are the compute layer where your application logic lives. They execute in response to events from API Gateway.
3.	Amazon DynamoDB - DynamoDB is a fully managed NoSQL database service. It stores the application data.

## **Serverless Microservices Architecture Design**
<img width="1376" height="370" alt="image" src="https://github.com/user-attachments/assets/e19becf4-1b87-471f-b798-1fdf62f6c0f8" />

## **AWS Well-Architected Framework & Lambda Power Tuning Impact**
The Lambda Power Tuning Tool directly influences two key pillars of the AWS Well-Architected Framework - Cost Optimization and Performance Efficiency. Performance and cost don’t always move in the same direction but with AWS Lambda Power Tuning, you can find the perfect balance.
The tool visualizes the trade-offs between:
 - Execution time
 - Cost per invocation
 - Memory allocation
   
And gives you the sweet spot, the point where your Lambda runs fastest for the lowest cost.

## **How AWS Lambda Power Tuning Works**
Here’s the high-level process:
1.	You trigger a Step Functions state machine provided by the tool.
2.	The state machine runs your Lambda at multiple configured memory sizes (128 MB → 10,240 MB).
3.	For each power level, it captures:
    -	Execution duration
    -	Estimated cost
    -	Statistical metrics (avg, min, max, std deviation)
4.	Step Functions aggregates the results.
5.	A visual chart is generated showing cost vs. execution time.
6.	The tool suggests one of the following optimal configurations:
    -	Cost-optimized
    -	Performance-optimized
    -	Balanced cost/performance choice
You can then update your Lambda's memory setting accordingly.

## **Lambda Function**
The function acts as a command handler, routing the input operation from the API Gateway payload to the appropriate boto3 (DynamoDB SDK) method.

```
from __future__ import print_function
import boto3
import json

print('Loading function')


def lambda_handler(event, context):
    Provide an event that contains the following keys:

      - operation: one of the operations in the operations dict below
      - tableName: required for operations that interact with DynamoDB
      - payload: a parameter to pass to the operation being performed
    '''
    #print("Received event: " + json.dumps(event, indent=2))

    operation = event['operation']

    if 'tableName' in event:
        dynamo = boto3.resource('dynamodb').Table(event['tableName'])

    operations = {
        'create': lambda x: dynamo.put_item(**x),
        'read': lambda x: dynamo.get_item(**x),
        'update': lambda x: dynamo.update_item(**x),
        'delete': lambda x: dynamo.delete_item(**x),
        'list': lambda x: dynamo.scan(**x),
        'echo': lambda x: x,
        'ping': lambda x: 'pong'
    }

    if operation in operations:
        return operations[operation](event.get('payload'))
    else:
        raise ValueError('Unrecognized operation "{}"'.format(operation))
```

## **AWS Lambda Power Tuning Results**

<img width="1692" height="775" alt="image" src="https://github.com/user-attachments/assets/4ea91983-a934-42f2-acc7-553c7ec76b02" />

## **Load Testing Using Postman**

<img width="1708" height="907" alt="image" src="https://github.com/user-attachments/assets/baddbfac-d89b-4bbf-bf26-f60df0cde4ac" />

<img width="962" height="756" alt="image" src="https://github.com/user-attachments/assets/6a27d99e-cdde-4b7b-ac80-7fed3e468dac" />


## **Conclusion**
More memory doesn’t always mean higher cost. Sometimes increasing memory reduces execution time enough to lower total cost. Data-driven tuning helps identify the sweet spot where performance and cost are both optimized.





