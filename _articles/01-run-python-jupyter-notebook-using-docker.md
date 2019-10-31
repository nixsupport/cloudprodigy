---
layout: article
title: Run PySpark and Jupyter Notebook using Docker
featured_img: pyspark-jupyter-notebook-docker.jpg
description: "Use Docker to run PySpark, Python, R to develop data analysis programs"
---
<br>
<h2 style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;color:#fed136;text-align:center"> Run PySpark and Jupyter Notebook using Docker</h2>
<br>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px;font-size:15px">
In this article, I'll explain about basic toolset required to write standard Data Analysis programs in the containerized environment using Docker. As always, my approach is to make your programs portable and platform independent.
<br>
Let's first understand briefly what I mean by toolset and what I'm going to package in the Docker container.
<br>
<ul style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif">
<li><b>PySpark</b> - PySpark programming is the collaboration of Apache Spark and Python. It is a Python API built to interact with Apache Spark. Since it's written in Python you can use other Python modules to be an efficient Data Analyst</li>
<li><b>Apache Spark</b> - It is a very popular framework for handling and working with Big Data. It is almost 100x faster than any other traditional large scale data processing frameworks</li> 
<li><b>Jupyter Notebook</b> - It is an open source web application mostly used by Data Analysts / Engineers to write code, mathematical equations, data visualization, etc </li>
<li><b>NumPy</b> - It is a Python library used to work with multi-dimensionsal arrays, matrices, high-level mathematical functions, etc</li>
</ul>
</p>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px;font-size:15px">
Now let's dig into technical details and see how to setup local environment which supports PySpark, Jupyter Notebook and NumPy. Here are step-by-step instructions:
<ol style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif">
<li>Create a new folder on your system, e.g. c:\code\pyspark-jupyter or whatever name you want to give</li>
<li>Create a file in that folder and call it <b>docker-compose.yaml</b> with the content given below:</li>
{% highlight ruby %}
version: "3"
services:
  pyspark:
    image: "jupyter/all-spark-notebook"
    volumes:
      - c:/code/pyspark-data:/home/jovyan
    ports:
      - 8888:8888
{% endhighlight %}
<li>In the above file, I'm pulling an official jupyter docker image, mapping the local folder with a folder inside container and exposing container port 8888 to host port 8888. Simple, isn't it?</li>
<li>Now run this file using command <b>docker-compose up</b> and you'll see the output similar as shown below</li>
{% highlight ruby %}
PS C:\code\pyspark-jupyter> docker-compose up
Recreating pyspark-jupyter_pyspark_1 ... done
Attaching to pyspark-jupyter_pyspark_1
pyspark_1  | Executing the command: jupyter notebook
pyspark_1  | [I 14:41:40.780 NotebookApp] Writing notebook server cookie secret to /home/jovyan/.local/share/jupyter/runtime/notebook_cookie_secret
pyspark_1  | [I 14:41:41.010 NotebookApp] Loading IPython parallel extension
pyspark_1  | [I 14:41:41.049 NotebookApp] JupyterLab extension loaded from /opt/conda/lib/python3.7/site-packages/jupyterlab
pyspark_1  | [I 14:41:41.049 NotebookApp] JupyterLab application directory is /opt/conda/share/jupyter/lab
pyspark_1  | [I 14:41:41.054 NotebookApp] Serving notebooks from local directory: /home/jovyan
pyspark_1  | [I 14:41:41.054 NotebookApp] The Jupyter Notebook is running at:
pyspark_1  | [I 14:41:41.054 NotebookApp] http://3d475598a523:8888/?token=YOUR_TOKEN
pyspark_1  | [I 14:41:41.054 NotebookApp]  or http://127.0.0.1:8888/?token=YOUR_TOKEN
pyspark_1  | [I 14:41:41.055 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
pyspark_1  | [C 14:41:41.073 NotebookApp]
pyspark_1  |
pyspark_1  |     To access the notebook, open this file in a browser:
pyspark_1  |         file:///home/jovyan/.local/share/jupyter/runtime/nbserver-6-open.html
pyspark_1  |     Or copy and paste one of these URLs:
pyspark_1  |         http://3d475598a523:8888/?token=YOUR_TOKEN
pyspark_1  |      or http://127.0.0.1:8888/?token=YOUR_TOKEN
{% endhighlight %}
<li>Copy the URL <b>http://127.0.0.1:8888/?token=YOUR_TOKEN</b> and open in the browser of your choice</li>
<li>You'll see an instance of Jupyter Notebook running in a container. As you might have noticed that your local folder is mapped inside the container</li>
<img src="/img/articles/pyspark-jupyter-inner-1.PNG" class="center">
<hr>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px;font-size:15px">
Now let's create our first notebook and work with PySpark. This is just a brief introduction as I'll be writing separte articles about PySpark and NumPy in detail.
<br>
<b>PySpark Demo using Docker</b>
<img src="/img/articles/pyspark-jupyter-inner-2.PNG" class="center">
<hr>
The second notebook briefs about using NumPy.
<br>
<b>NumPy Demo using Docker</b>
<img src="/img/articles/numpy-jupyter-inner-1.PNG" class="center">
<br>
Hope it helps in starting your Data Analysis journey and using Docker to make portable programs.
<br>
<b><i>Happy Learning!</i></b>
