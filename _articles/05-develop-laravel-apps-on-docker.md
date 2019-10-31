---
layout: article
title: Develop and Deploy Laravel Apps using Docker
featured_img: develop-laravel-apps-using-docker.jpg
description: "Setup local development environment using Docker to build Laravel apps and deploy later."
---
<br>
<h2 style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;color:#fed136;text-align:center"> Develop Laravel Apps using Docker</h2>
<br>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px">
As more and more applications are getting deployed to containers, it is very helpful to start developing apps on containers itself. This practice makes sure that you capture every dependency to deploy and run your application in different environments.
<br>
One of the biggest advantage of containers is that you don't need to install anything on your local system. You just need to use the base image and customize that according to your requirements.
<br>
This is quick article on how to setup Laravel app on Docker. Let's dig into it.
<br>
<br>
My application has two parts:
<ul>
  <li>Frontend - using Apache, PHP and VueJS</li>
  <li>Backend - using MySQL</li>
</ul>
<br>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px">
Now we have to create two files required for docker setup.
<br>
First, lets create a Dockerfile for frontend and lets call it web.Dockerfile.
<br>
{% highlight ruby %}
FROM php:7
RUN apt-get update -y && apt-get install -y openssl zip unzip git libzip-dev zlib1g-dev
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN docker-php-ext-install pdo pdo_mysql zip
WORKDIR /var/www/app
RUN composer install
CMD php artisan serve --host=0.0.0.0 --port=80
EXPOSE 80
{% endhighlight %}
<br>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px">
I'm using php v7 image from docker hub. Then I'm extending that image and updating the container and installing some OS level modules.
<br>
Next, I'm installing Laravel in the container using composer. I'm also installing php extensions for mysql and zip in the container.
<br>
Then I set the working directory to /var/www/app, this is where my laravel app gets installed.
<br>
Finally, I started the built-in web server using <i>php artisan serve</i> command running on port 80 and then exposed port 80 out of the container.
<br>
It is worth mentioning that this is not recommended for Production workloads. You can easily setup Apache or Nginx instead of default server for production workloads.
<br>
<br>
Now we need to create a docker image out of this file and push it to docker hub registry.
<br>
{% highlight ruby %}
docker build -f web.Dockerfile
docker tag <source_image>:<tag> <destination_image>:<tag>
docker push <repo_url>/<image>:<tag>
{% endhighlight %}
<br>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px">
Now let's create our second file which is called docker-compose.yaml. It will help us in binding frontend and backend components. As you may notice we don't have any database configured yet. So let's create docker-compose.yaml and setup database service.
<br>
{% highlight ruby %}
version: '3'
services:
  web:
    build:
      context: ./
      dockerfile: web.Dockerfile
    volumes:
      - c:/code/myapp:/var/www/app
    image: <dockerhubrepo>/laravel:v1 #replace <dockerhubrepo> with your docker repository name
    environment:
      - "DB_PORT=3306"
      - "DB_HOST=database"
    depends_on:
      - database
    ports:
      - 8000:80
  database:
    image: mysql:5.6
    volumes: 
      - data:/var/lib/mysql
    environment:
      - "MYSQL_DATABASE=laravel"
      - "MYSQL_USER=someuser"
      - "MYSQL_PASSWORD=somepassword"
      - "MYSQL_ROOT_PASSWORD=rootpassword"
    ports:
      - 3306:3306
volumes:
  data:
{% endhighlight %}
<br>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px">
I'm defining two services in this file. First is called <u>web</u>. It used the image that we pushed to docker hub repository in the previous step. I'm also mounting container directory <i>/var/www/app</i> to local directory <i>c:/code/myapp</i>. This will make sure that changes made locally get reflected in the container. Next, it refers database host using variable <i>DB_HOST</i> and database port using <i>DB_PORT</i>. I have also configured the dependency on <i>database</i> service so that database service runs before web service. Finally, I mapped host port 8000 with container port 80.
<br>
<br>
The second service is called <u>database</u>. This is the name which is refered by web service using <i>DB_HOST</i> variable. I'm pulling the official <i>mysql</i> image straight from docker hub. That's why we don't need separate Dockerfile for database. I'm creating a persistent volume by the name <i>data</i> and mounting it inside container at <i>/var/lib/mysql</i> mount point. Next, I specify serveral database related parameters like database name, user name, user password and root password.
<br>
Once you've both of these files ready, run following command to spin up everything.
<br>
{% highlight ruby %}
docker-compose pull #It will pull both images from docker hub
docker-compose up #It will bring up the containers
{% endhighlight %}
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px">
And that't it!!! You would have your Laravel app up and running on http://localhost:8000. You can continue editing your code locally which is mounted at <i>c:/code/myapp</i> and changes will be pushed to container immediately.

