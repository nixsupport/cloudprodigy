---
layout: article
title: Launch Template vs Launch Configuration
featured_img: launch-template-vs-launch-config.jpg
description: "A new bucket is checked whether it is encrypted or not. If not, Lambda function is triggered which sets the default encryption"
---
<br>
<h2 style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;color:#fed136;text-align:center"> Launch Template<br>vs<br>Launch Configuration</h2>
<br>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px">
Launch Templates were introduced in 2017. At a high level, both Launch Templates and Launch Configuration are used to configure AMI, Instance Type, Key Pair, etc in order to launch new instances in Auto Scaling environment.
<br>
However, Launch Templates allow you to version the template which was not the possible using Launch Configuration. The common practice is to create a default template with standard configuration and create new versions which allows specifying new parameters.
<br>
With Launch Templates, you can also provision instances across multiple instance types using On-Demand and Spot Instances. It was not possible using Launch Configuration.
<br>
You can also launch instances right from the Launch Templates. It allows you to have single source of configuration for your instances. It is worth mentioning that instances created directly using Launch Templates are not put under Auto Scaling by default. 
<br>
Below animation demonstrates simple process of creating a launch template.
<br>
<img src="/gif/launch-template-1.gif" class="center">
<br>
Once a basic launch template is created, you can take do any of the following:
<ul>
    <li>Launch Instance from this template</li>
    <li>Create Auto Scaling Group</li>
    <li>Create Spot Fleet</li>
</ul>
<br>
<img src="/img/articles/launch-template-1.jpg" class="center">
<br>
</p>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px">
Out of three options, option 1 and 3 cannot be done using Launch Configuration. Option 1 is useful for provisioning instances from same template which leads to greater consistency.
<br>
Option second allows you to launch fleet of spot instances with configuration steps as shown below:
<br>
<img src="/gif/launch-template-2.gif" class="center">
<br>
Hope it helps in understanding the key differences between Launch Templates and Launch Configuration.
</p>

