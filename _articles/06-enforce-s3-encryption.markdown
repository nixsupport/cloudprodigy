---
layout: article
title: Enforce S3 Bucket Encryption
featured_img: s3-encryption.jpg
description: "A new bucket is checked whether it is encrypted or not. If not, Lambda function is triggered which sets the default encryption"
---
<br>
<h2 style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;color:#fed136;text-align:center"> Enforce S3 Bucket Encryption</h2>
<br>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px">
It happens most of the times that users/developers create S3 buckets and leave them unencrypted. That leads to security violation and your data is at risk. There are number of scenarios to counter this issue. The best one is to publish S3 creation template to Service Catalog and let users create buckets using Service Catalog product.
However, in this article I'm going to explain how to create a workflow which detects unencrypted buckets and encrypt them automatically.
<br>
Let's first create an SNS topic and call it <u>s3encryption</u> and note down it's ARN.
Then create a Lambda function using following code written in Python.
{% highlight py linenos %}
import json
import boto3
import logging

def lambda_handler(event, context):
    logger = logging.getLogger()
    logger.setLevel(logging.INFO)
    
    s3 = boto3.client('s3')
    sns = boto3.resource('sns')
    topic = sns.Topic('arn:aws:sns:us-east-1:<accountid:s3encryption')
    
    bucketName = str(event['detail']['requestParameters']['bucketName'])
    logger.info('got event{}'.format(event))
    eventdetails='\nBucket Name: ' + str(event['detail']['requestParameters']['bucketName'])
    eventdetails+='\nCreated by: '+ str(event['detail']['userIdentity']['arn'])
    eventdetails+='\nBucket is now encrypted and Server Side Logging Enabled'
    
    s3.put_bucket_encryption(
            Bucket = bucketName,
            ServerSideEncryptionConfiguration = {
                'Rules': [
                        {
                            'ApplyServerSideEncryptionByDefault': {
                                'SSEAlgorithm': 'AES256'
                            }
                        }
                    ]
            }
        )
    response = topic.publish(
        Message=  'Event Details:'+str(eventdetails),
        Subject= 'New Bucket Created',
        MessageStructure='String',
        MessageAttributes={
            's3bucket': {
                'DataType': 'String',
                'StringValue': 'Bucket Properties'
    
            }
        }
    )
{% endhighlight %}
<br>
</p>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px">
Next, create a <u>CloudWatch Event Rule</u> with eventName set to <b>CreateBucket</b> and eventSource set to <b>s3.amazonaws.com</b>. Finally set the target to Lambda function created before.
</p>

