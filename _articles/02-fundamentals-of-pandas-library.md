---
layout: article
title: Fundamentals of Pandas
featured_img: fundamentals-of-pandas.jpg
description: "Learn basic Pandas functions"
---
<br>
<h2 style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;color:#fed136;text-align:center"> Fundamentals of Pandas Library</h2>
<br>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px;font-size:15px">
If you're just getting started with your data scientist/analyst journey and want to explore some of the basic libraries to begin with, then this article is for you. I am going to cover Pandas library and demostarte some of the basic but very useful functions of this library. 
<br>
Pandas is a Python library which is mostly used for constructing data frames to do some kind of analysis on the data. You can think Pandas as an Excel spreadsheet and one such spreadsheet is called a <b>data frame</b> in Pandas.
<br>
Basically when you read any kind of data in Pandas it constructs a data frame and then you use several functions to take action on this data frame. Let's understand it more by doing. As always, I'm using Docker to run Jupyter notebooks with Pandas functionality. If you want to know how to setup Docker with Jupyter, please checkout my <a href="https://medium.com/analytics-vidhya/run-pyspark-and-jupyter-notebook-using-docker-bed12ecb755a">article</a>.
<br>
</p>
<hr>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px;font-size:15px">
<b>Importing Pandas and reading data</b><br>
Before doing anything you've to import Pandas in your notebook. Once done you can load data and construct a data frame. You can either load data using a local file or by pointing to a web URL.
<br>
<img src="/img/articles/fundamentals-of-pandas-inner-1.PNG" class="center">
In the above snap, I have imported Pandas in my notebook, load a data set from my local system, consturcuted a data frame and displayed first five records. But I hit one issue rightaway. My data set has no header that's why Pandas assigned first row of my data as the header of this data frame. That's not what I'm looking for. Let's correct that problem.
<img src="/img/articles/fundamentals-of-pandas-inner-2.PNG" class="center">
As you can see I have added an additional parameter <b>header=None</b> to avoid the above situation. Now there is second problem I need to address. I need to assign header to my data set so that I can understand the meaning of each value.
<img src="/img/articles/fundamentals-of-pandas-inner-3.PNG" class="center">
I have created a list of headers and then assigned that to our data frame using <b>df.columns</b> function. Now when you list out the contents of data frame, you can see the header at the top.
<br>
Now let's go through some of the common functions we can use with our data frame.
</p>
<hr>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px;font-size:15px">
<b>df.head() and df.tail()</b>
<img src="/img/articles/fundamentals-of-pandas-inner-4.PNG" class="center">
</p>
<hr>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px;font-size:15px">
<b>df.dtypes and df.describe()</b>
<img src="/img/articles/fundamentals-of-pandas-inner-5.png" class="center">
<img src="/img/articles/fundamentals-of-pandas-inner-6.PNG" class="center">
<img src="/img/articles/fundamentals-of-pandas-inner-7.PNG" class="center">
</p>
<hr>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px;font-size:15px">
<b>Working with Missing Data</b>
<br>
There is a possiblity that some of the values in your data frame shown as Nan. <b>NaN</b> is shorthand for Not a Number and it represents missing data. Missing data can also be a ?, 0(zero), NaN or simply empty value.
<br>
There are several approaches to tackle with missing data. Some of the popular ones are:
<ul style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:40px;font-size:15px">
<li>Try to get more data from the source</li>
<li>Replace missing data with average value of that column</li>
<li>Drop empty values</li>
</ul>
</p>
<hr>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px;font-size:15px">
Let's take a look how missing data can be replaced or dropped.
<br>
<b>Replace Missing Values</b>
<img src="/img/articles/fundamentals-of-pandas-inner-8.PNG" class="center">
<b>Drop Missing Values</b>
<br>
Data can be dropped along rows (axis=0 which is also default) or columns (axis=1) defined by axis. Here are couple of examples demonstrating it.
<img src="/img/articles/fundamentals-of-pandas-inner-9.PNG" class="center">
And now drop data based on columns.
<img src="/img/articles/fundamentals-of-pandas-inner-10.PNG" class="center">
<br>
I believe it's good enough to start with. I will add some more functions in upcoming articles as this one is getting very long. 
<br>
As always, <b><i>Happy Learning!</i></b>