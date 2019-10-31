---
layout: article
title: Getting Started with AWS CDK
featured_img: getting-started-with-aws-cdk.jpg
description: "Write your first template using AWS CDK"
---
<br>
<h2 style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;color:#fed136;text-align:center">Getting Started with AWS CDK</h2>
<br>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px">
AWS CDK became Generall Available (GA) in July 2019 with support of TypeScript and Python to begin with. It is an open source development framework. With AWS CDK, you can generate CloudFormation templates and provision infrastructure. As of this writing, AWS CDK supports Python and TypeScript in GA and .NET and Java in Developer Preview mode.
<br>
Before AWS CDK, I used Troposphere which is a Python library to create CloudFormation templates. I found that very helpful to generate CloudFormation templates in JSON format in particular.
<br>
This is the first article of series of articles based on AWS CDK. Let's first understand what is AWS CDK, how does it work and how you can get started with it.
</p>
<hr>
<h4>What is AWS CDK?</h4>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px">
AWS CDK is an open source software development framework. Everything in AWS CDK is a construct. To understand better, you can think construct as a cloud component which can also be called as a resource to understand it in the terminology of CloudFormation templates.
<br>
For Example, S3 bucket resource is a construct, DynamoDB resource is a construct and so on. You can use constructs to define relationships and set their properties.
<br>
Let's dig into it and create your first CloudFormation template using AWS CDK. I'm going to keep it simple and create an S3 bucket template for better understanding.
</p>
<br>
<h4>Create S3 bucket template using AWS CDK</h4>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px">
In order to start working with AWS CDK, you need to setup your environment. Well, I'm not going to install anything on my local machine, instead I will configure the environment using Docker. Please checkout my <a href="https://balkaranbrar.ca/articles/configure-vscode-with-docker/">article</a> if you want to know more about configuring Docker with VSCode for Python.
<br>
<ul>
<li>
<h5 style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;color:black">
AWS CDK Folder Struture</h5>
</li>
</ul>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:50px">
At the bare minimum, you need to setup your AWS CDK app with three files as listed below:
<ul style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:70px">
  <li >app.py - A standard file to define your AWS CDK constructs</li>
  <li>cdk.json - A standard file to map your app with python executable</li>
  <li>requirements.txt - Optional but I recommend keeping this file. It contains all the requirements your app needs to generate template. You can install these requirements using pip.</li>
</ul>
</p>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:50px">
Here is how each of this file looks like.
<br>
<b>app.py</b>
{% highlight python %}
from aws_cdk import (
    aws_s3 as s3,
    aws_iam as iam,
    core,
)
class S3Template(core.Stack):
    def __init__(self, app: core.App, id: str, **kwargs) -> None:
        super().__init__(app, id)
        #create an S3 bucket
        myBucket = s3.Bucket(self, 'MyFirstBucket', bucket_name='balkaran-aws-cdk-s3-demo-bucket')
        myBucket.add_to_resource_policy(    #Grant read access to everyone in your account
            iam.PolicyStatement(
                    actions=['s3:GetObject'],
                    resources=[myBucket.arn_for_objects('*')],
                    principals=[iam.AccountPrincipal(account_id=core.Aws.ACCOUNT_ID)]
            )
        )
        myUser = iam.User(self,'s3User')    #Grant write access to a specific user
        myBucket.grant_write(myUser)
app = core.App()
S3Template(app, "S3Template", env={'region':'us-east-1'})
app.synth()
{% endhighlight %}
</p>
<br>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:50px">
<b>cdk.json</b>
{% highlight ruby %}
{
    "app": "python3 app.py"
}
{% endhighlight %}
</p>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:50px">
<b>requirements.txt</b>
{% highlight ruby %}
aws-cdk.aws-s3
aws-cdk.aws-iam
{% endhighlight %}
</p>
<hr>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:50px">
Now we have our code ready. In addition to these three files, let's create a new folder called <b>.divcontainer</b> at the root of our app. Let's configure that setup so that we run our code in the container instead of installing everything on local system.
<ul>
<li>
<h5 style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;color:black">
Configure VSCode with Docker</h5>
</li>
</ul>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:50px">
We need create two files in <b>.divcontainer</b> folder, viz. <i>dockercontainer.json</i> and <i>Dockerfile</i>. Let's setup both.
</p>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:50px">
<b>divcontainer.json</b>
{% highlight ruby %}
{
	"name": "AWS CDK",
	"dockerFile": "Dockerfile",

	"settings": { 
		"terminal.integrated.shell.linux": "/bin/bash",
		"python.pythonPath": "/usr/bin/python3"
	},

	"extensions": [
		"ms-python.python"
	]
}
{% endhighlight %}
</p>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:50px">
<b>Dockerfile</b>
{% highlight ruby %}
FROM amazonlinux:2
WORKDIR /app
ENV NODE_VERSION 8.16.0
RUN yum -y --security update \
  && yum install -y \
    git \
    rsync \
    zip \
    unzip \
    tar \
    xz \
    python3 \
    python3-dev \
    py3-setuptools \
  && yum clean all \
  && rm -rf /var/cache/yum \
  && curl -fsSLO --compressed "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-x64.tar.xz" \
  && curl -fsSLO --compressed "https://nodejs.org/dist/v$NODE_VERSION/SHASUMS256.txt" \
  && grep " node-v$NODE_VERSION-linux-x64.tar.xz\$" SHASUMS256.txt | sha256sum -c - \
  && tar -xJf "node-v$NODE_VERSION-linux-x64.tar.xz" -C /usr/local --strip-components=1 --no-same-owner \
  && ln -s /usr/local/bin/node /usr/local/bin/nodejs \
  && rm "node-v$NODE_VERSION-linux-x64.tar.xz" SHASUMS256.txt
RUN pip3 install --upgrade awscli
RUN npm install -g aws-cdk
RUN pip3 install --upgrade aws-cdk.core
ENTRYPOINT ["/bin/bash"]
{% endhighlight %}
</p>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px">
Once you have this setup completed, press F1 in VSCode and select <b>Remote-Containers: Open Folder in Container</b> option. You can also reopen VSCode and it will detect automatically that this projects need to run in the container. Below screenshot explains the entire process.
<img src="/img/articles/aws-cdk-inner-1.jpg" class="center">
Once dependencies are installed, it's time to generate the template and deploy in AWS. Below is the list of commands you need to execute to complete deployment.
{% highlight bash %}
bash-4.2# cdk synth  //To generate the template. If your code contains error that will be reported here.
{% endhighlight %}
<img src="/img/articles/aws-cdk-inner-2.JPG" class="center">
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px">
The command <i>cdk deploy</i> creates a CloudFormation change-set and deploys the template. Below is some more detailed output generated by <i>cdk deploy</i>command
<img src="/img/articles/aws-cdk-inner-3.JPG" class="center">
Finally, here is the list of commonly used AWS CDK commands:
{% highlight ruby %}
cdk ls                          Lists all stacks in the app
cdk synth                       Synthesizes and prints the CloudFormation template for this stack
cdk bootstrap [ENVIRONMENTS..]  Deploys the CDK toolkit stack into an AWS environment
cdk deploy                      Deploys the stack(s) into your AWS account
cdk destroy                     Destroy the stack(s)
cdk diff [STACKS..]             Compares the specified stack with the deployed
                                stack or a local template file, and returns
                                with status 1 if any difference is found
cdk metadata [STACK]            Returns all metadata associated with this
                                stack
cdk init [TEMPLATE]             Create a new, empty CDK project from a
                                template. Invoked without TEMPLATE, the app
                                template will be used.
cdk context                     Manage cached context values
cdk docs                        Opens the reference documentation in a browser
cdk doctor                      Check your set-up for potential problems
{% endhighlight %}