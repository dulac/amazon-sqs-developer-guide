# Custom Amazon SQS Access Policy Language Examples<a name="sqs-creating-custom-policies-access-policy-examples"></a>

The following are examples of typical Amazon SQS access policies\.

## Example 1: Give Permission to One Account<a name="one-account"></a>

The following example Amazon SQS policy gives AWS account 111122223333 permission to send to and receive from `queue2` owned by AWS account 444455556666\.

```
{   
   "Version": "2012-10-17",
   "Id": "UseCase1",
   "Statement" : [{
      "Sid": "1", 
      "Effect": "Allow",           
      "Principal": {
         "AWS": [
            "111122223333"
         ]
      },
      "Action": [
         "sqs:SendMessage",
         "sqs:ReceiveMessage"
      ], 
      "Resource": "arn:aws:sqs:us-east-2:444455556666:queue2"  
   }]
}
```

## Example 2: Give Permission to One or More Accounts<a name="two-accounts"></a>

The following example Amazon SQS policy gives one or more AWS accounts access to queues owned by your account for a specific time period\. It is necessary to write this policy and to upload it to Amazon SQS using the [http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html) action because the [http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_AddPermission.html](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_AddPermission.html) action doesn't permit specifying a time restriction when granting access to a queue\.

```
{   
   "Version": "2012-10-17",
   "Id": "UseCase2",
   "Statement" : [{
      "Sid": "1", 
      "Effect": "Allow",           
      "Principal": {
         "AWS": [
            "111122223333",
            "444455556666"
         ]
      },
      "Action": [
         "sqs:SendMessage",
         "sqs:ReceiveMessage"
      ], 
      "Resource": "arn:aws:sqs:us-east-2:444455556666:queue2",
      "Condition": {
         "DateLessThan": {
            "AWS:CurrentTime": "2009-06-30T12:00Z"
         }
      }   
   }]
}
```

## Example 3: Give Permission to Requests from Amazon EC2 Instances<a name="requests-from-ec2"></a>

The following example Amazon SQS policy gives access to requests that come from Amazon EC2 instances\. This example builds on the "[Example 2: Give Permission to One or More Accounts](#two-accounts)" example: it restricts access to before June 30, 2009 at 12 noon \(UTC\), it restricts access to the IP range `203.0.113.0/24`\. It is necessary to write this policy and to upload it to Amazon SQS using the [http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html) action because the [http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_AddPermission.html](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_AddPermission.html) action doesn't permit specifying an IP address restriction when granting access to a queue\.

```
{   
   "Version": "2012-10-17",
   "Id": "UseCase3",
   "Statement" : [{
      "Sid": "1", 
      "Effect": "Allow",           
      "Principal": {
         "AWS": [
            "111122223333"
         ]
      },
      "Action": [
         "sqs:SendMessage",
         "sqs:ReceiveMessage"
      ], 
      "Resource": "arn:aws:sqs:us-east-2:444455556666:queue2",
      "Condition": {
         "DateLessThan": {
            "AWS:CurrentTime": "2009-06-30T12:00Z"
         },
         "IpAddress": {
            "AWS:SourceIp": "203.0.113.0/24"
         }
      }   
   }]
}
```

## Example 4: Deny Access to a Specific Account<a name="deny-account"></a>

The following example Amazon SQS policy denies a specific AWS account access to your queue\. This example builds on the "[Example 1: Give Permission to One Account](#one-account)" example: it denies access to the specified AWS account\. It is necessary to write this policy and to upload it to Amazon SQS using the [http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html) action because the [http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_AddPermission.html](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_AddPermission.html) action doesn't permit deny access to a queue \(it allows only granting access to a queue\)\. 

```
{ 
   "Version": "2012-10-17",
   "Id": "UseCase4",
   "Statement" : [{
      "Sid": "1", 
      "Effect": "Deny",           
      "Principal": {
         "AWS": [
            "111122223333"
         ]
      },
      "Action": [
         "sqs:SendMessage",
         "sqs:ReceiveMessage"
      ], 
      "Resource": "arn:aws:sqs:us-east-2:444455556666:queue2"   
   }]
}
```

## Example 5: Deny Access if Not From VPC Endpoint<a name="vpc-endpoint"></a>

The following example Amazon SQS policy that restricts access to a specific queue `queue2` for the actions `sqs:SendMessage` and `sqs:ReceiveMessage` only from the VPC endpoint with the ID `vpce-1a2b3c4d`\. The aws:sourceVpce condition is used to the specify the endpoint\. The aws:sourceVpce condition does not require an ARN for the VPC endpoint resource, only the VPC endpoint ID\. For more information about using conditions in a policy, see [Specifying Conditions in a Policy](amazon-s3-policy-keys.md)\.

**Note**
You could change the policy example to restrict all actions to a specific VPC endpoint by denying all SQS actions (`sqs:*`) in the second statement of the example policy below\. However, such a policy statement would mean that all actions (including administrative actions needed to modify the queue permissions) must be made through that specific VPC endpoint defined in the policy. Be careful not to apply a policy that prevents you from modifying the queue permissions in the future\.

```
{
  "Version": "2012-10-17",
  "Id": "UseCase5",
  "Statement": [{
      "Sid": "1",
      "Effect": "Allow",
      "Principal": {
        "AWS": [
          "444455556666"
        ]
      },
      "Action": [
        "sqs:SendMessage",
        "sqs:ReceiveMessage"
      ],
      "Resource": "arn:aws:sqs:us-east-2:444455556666:queue2"
    },
    {
      "Sid": "2",
      "Effect": "Deny",
      "Principal": "*",
      "Action": [
        "sqs:SendMessage",
        "sqs:ReceiveMessage"
      ],
      "Resource": "arn:aws:sqs:us-east-2:444455556666:queue2",
      "Condition": {
         "StringNotEquals": {
            "aws:sourceVpce": "vpce-1a2b3c4d"
         }
      }
    }
  ]
}
```

