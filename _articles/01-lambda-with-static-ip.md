---
layout: article
title: AWS Lambda with Static IP Address
featured_img: lambda-with-static-ip-address.jpg
description: "Configure AWS Lambda with Static IP Address"
---
<br>
<h2 style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;color:#fed136;text-align:center"> AWS Lambda with Static IP Address</h2>
<br>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px;font-size:15px">
AWS Lambda is a serverless computing platform which is highly used in building serverless and event driven architectures on AWS. By default, Lambda function runs in a default system managed VPC. However, it is possible to run Lambda inside your VPC and that too in the private subnet to access your resources over private network.<br>
There is a classic use case if you've hosted your APIs using API Gateway and Lambda. If you're in banking/financial domain, most of the target system needs your IP to be whitelisted. But how can you whitelist the IP address of a Lambda since we cannot assign Elastic IP to a Lambda function. Well, here is the solution which not only sends requests originated from Lambda using a static IP but also interacts with other resources without going over the internet. <br>
Let's explore how it can be implemented.<br>
</p>
<hr>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px;font-size:15px">
Let's create a new VPC for this demonstration. You can use existing VPC and follow rest of the applicable steps.
<img src="/img/articles/lambda-static-ip-vpc.PNG" class="center">
</p>
<hr>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px;font-size:15px">
Now add public and private subnets.
<img src="/img/articles/lambda-static-ip-subnet.PNG" class="center">
</p>
<hr>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px;font-size:15px">
Create Internet Gateway and attach it with the VPC
<img src="/img/articles/lambda-static-ip-igw.PNG" class="center">
</p>
<hr>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px;font-size:15px">
Create NAT Gateway and map an Elastic IP address
<img src="/img/articles/lambda-static-ip-nat.PNG" class="center">
<hr>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px;font-size:15px">
Create one route table each for public and private subnets. Edit the route table for public subnet and add default route of 0.0.0.0/0 to go thourgh Internet Gateway. Similarly, edit the route table for private subnet and add default route of 0.0.0.0/0 to go through NAT gateway. 
<img src="/img/articles/lambda-static-ip-route-table.PNG" class="center">
</p>
<hr>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px;font-size:15px">
Let's create a new security group which will be used by Lambda function. This security group will have NO inbound rules and all the outbound traffic will be allowed, which is also default in case of security groups.
<img src="/img/articles/lambda-static-ip-sg-inbound.PNG" class="center">
<img src="/img/articles/lambda-static-ip-sg-outbound.PNG" class="center">
</p>
<hr>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px;font-size:15px">
All the above steps are related to VPC configuration. Now let's create a new Lambda function and configure the VPC settings as per our configuration done above.
<img src="/img/articles/lambda-static-ip-config.PNG" class="center">
<br>
As you might have noticed, I've configured the VPC, private subnet and security group created above. Now the Lambda function will run in my VPC instead of the default system VPC. Since we have mapped a NAT gateway with the private subnet, any request from this Lambda function will go through NAT gateway, which eventually has an Elastic IP configured. Now the target systems can whitelist the Elastic IP address.
</p>
<hr>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px;font-size:15px">
To test this out, let's make a simple call to google.com and get the response code. A response code of 200 confirms that our Lambda function is able to make calls external to our VPC.
<img src="/img/articles/lambda-static-ip-code.PNG" class="center">
<br>
I hope it will be helpful. As I mentioned earlier this is a very common use case and the fix is super easy.
<br>
As always, <b><i>Happy Learning!</i></b>