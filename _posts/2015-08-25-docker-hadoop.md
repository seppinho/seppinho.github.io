---
layout:     post
title:      Creating a Docker Image for Hadoop MapReduce (CDH5)
date:       2015-08-26 09:00:21
categories: docker hadoop
---
There are many ways how [Docker](https://www.docker.com/) can be used. In this blog post I'll summarize the steps I did to create a running Hadoop Docker image for the Cloudera Version (CDH5) of Hadoop MapReduce MRv1 (the "old" MapReduce) and MRv2 (the "new" MapReduce aka YARN).  Before we start, please check if Docker is [installed](https://docs.docker.com/installation/) on your local OS.

## Ready-to-use image for MapReduce v1 and MapReduce v2:
To keep it simple, you can of course use one of my previously build Hadoop Docker images, execute Hadoop MapReduce jobs and skip the remaining steps. 

### MapReduce v1 
{% highlight bash %}
docker pull seppinho/cdh5-hadoop-mrv1:latest
docker run -it -p 50030:50030 seppinho/cdh5-hadoop-mrv1:latest
sh /usr/bin/execute-wordcount.sh
{% endhighlight %}

### MapReduce v2 - YARN Architecture
{% highlight bash %}
docker pull seppinho/cdh5-hadoop-mrv2:latest
docker run -it -p 19088:19088 seppinho/cdh5-hadoop-mrv2:latest
sh /usr/bin/execute-wordcount.sh
{% endhighlight %}

## Step by step tutorial
If you want to start from scratch, we need a basic OS image we can work with. For that, `pull` a fresh Docker Ubuntu image (14.04) and `run` it. The run command starts a new container which is a running instance of the Ubuntu image. 
{% highlight bash %}
docker pull ubuntu:14.04
docker run -i -t ubuntu:14.04
# verify that it's really Ubuntu 14.04 and not your local OS
lsb_release -a
{% endhighlight %}

Now, back on your local OS (type `exit` to close the Ubuntu container from before) create a new folder including an empty file named `Dockerfile`. The Dockerfile should include all necessary commands to build the new image. Have a look at my Github [repository](https://github.com/seppinho/cdh5-hadoop-mrv1) including a Dockerfile. 
{% highlight bash %}
mkdir new-docker-image
cd new-docker-image
touch Dockerfile
edit Dockerfile
{% endhighlight %}

When you are satisfied with your Dockerfile you are ready to build your first Docker image. Just execute the following commands on your OS where the Dockerfile is located. Keep in mind that every time your Dockerfile has been changed, a rerun of the `build` command is required. 
{% highlight bash %}
docker build --no-cache=false -t hadoop-image .
docker run -i -t -p 50030:50030  hadoop-image
{% endhighlight %}

You should now be able to connect to http://localhost:50030 from your local OS and execute a MapReduce job on the command line. 
{% highlight bash %}
sh /usr/bin/execute-wordcount.sh
{% endhighlight %}

This is all for today. In the next blog post, I'll show how our Hadoop exectution platform [Cloudgene](http://cloudgene.uibk.ac.at) uses such an image for an easy installation and execution process.
