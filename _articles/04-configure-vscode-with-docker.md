---
layout: article
title: Configure VSCode with Docker
featured_img: configure-vscode-with-docker.jpg
description: "Use Docker as your development environment with VSCode"
---
<br>
<h2 style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;color:#fed136;text-align:center"> Configure VSCode with Docker</h2>
<br>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px">
Microsoft VSCode is a very popular IDE among developers. Docker is another powerful tool to do local devlopment and then push containerized applications to real environments.
<br>
It would be very helpful if one can use VSCode installed on local system for development but use docker to run the application. Doing so, we don't need to install any application specific libraries on our local systems.
<br>
This approach is very effective and easily configurable for web applications. All one needs to do is to create a Dockerfile, extend the base image, configure required packages and then expose the container port. Local storage is shared with container so that files can be edited directly from your local environment and changes are immediately reflected in the container.
<br>
Please refer my <a href="https://balkaranbrar.ca/articles/develop-laravel-apps-on-docker/">article</a> to setup similar environment.
<br>
However, how about using docker for some python programs which cannot be exposed on any port and mapped with host system? Let's say I want to run a simple Python program without installing Python on my local system. How can I achieve that? Well, lets dig into that.
<br>
</p>
<hr>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px">
First of all launch VSCode and search for <b>Remote Containers</b> extension and install that.
<br>
<br>
<img src="/img/articles/remote-containers.JPG" class="center">
<br>
</p>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px">
Next, let's create a simple application which requires Python3 to run the programs. We need to create a folder by name ".devcontainer" at the root of the application. Next, create two files in that folder.
<ul>
<li>devcontainer - Configure settings of the container like user id, path, etc. </li>
<li>Dockerfile - Definition of container where you want to run your programs </li>
</ul>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px">
<b>devcontainer</b>
{% highlight textscript %}
{
	"name": "Demo",
	"dockerFile": "Dockerfile",

	// Comment out the next line to run as root instead. Linux users, 
	// update Dockerfile with your user's UID/GID if not 1000.
	//"runArgs": ["-u", "vscode"],

	// Use 'settings' to set *default* container specific settings.json values on container create. 
	// You can edit these settings after create using File > Preferences > Settings > Remote.
	"settings": { 
		"terminal.integrated.shell.linux": "/bin/bash",
		"python.pythonPath": "/usr/local/bin/python",
		"python.linting.pylintEnabled": true,
		"python.linting.pylintPath": "/usr/local/bin/pylint",
		"python.linting.enabled": true
	},

	// Specifies a command to run after the container is created
	//"postCreateCommand": "sudo pip install -r requirements.txt",

	"extensions": [
		"ms-python.python"
	]
}
{% endhighlight %}
</p>
<hr>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px">
<b>Dockerfile</b>
{% highlight ruby %}
FROM python:3

# Avoid warnings by switching to noninteractive
ENV DEBIAN_FRONTEND=noninteractive

# Or your actual UID, GID on Linux if not the default 1000
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# Configure apt and install packages
RUN apt-get update \
    && apt-get -y install --no-install-recommends apt-utils dialog 2>&1 \
    #
    # Verify git, process tools, lsb-release (common in install instructions for CLIs) installed
    && apt-get -y install git procps lsb-release \
    #
    # Install pylint
    && pip --disable-pip-version-check --no-cache-dir install pylint \
    #
    # Create a non-root user to use if preferred - see https://aka.ms/vscode-remote/containers/non-root-user.
    && groupadd --gid $USER_GID $USERNAME \
    && useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME \
    # [Optional] Add sudo support for non-root user
    && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME \
    #
    # Clean up
    && apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*

# Switch back to dialog for any ad-hoc use of apt-get
ENV DEBIAN_FRONTEND=
{% endhighlight %}
</p>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px">
Now let's open your application folder and you'll see that VSCode will detect that this project needs to run in a container. Below screenshot explains various steps you may need to go through to run commands in the container from your VSCode terminal.
<br>
<img src="/img/articles/vscode-docker-inner-1.JPG" class="center">
<br>
You can create new python files in VSCode and run in the terminal and the actual executions happens in the container. 
<br>You can also view the files in container itself. Following screenshot demonstrates that process.
<br>
<img src="/img/articles/vscode-docker-inner-2.JPG" class="center">
<br>
I will publish some more articles where I explain how to use boto3, pyspark, numpy, etc using this approach.
<br>
<i>Hope this article will help someone. Thanks for reading.</i>