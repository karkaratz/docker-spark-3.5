# docker-spark-3.5

This Project contains the Dockerfile(s) to istantiate a Standalone Spark 3.5 cluster (No YARN no Hadoop).<BR>
<BR>
<pre>  spark-base/     contains the setup of the main image with scala 2.13.12, spark 3.5, Python 3.11, pyspark, sparkshell.<BR>
  spark-master/   contains the setup of the spark master node<BR>
  spark-worker/   contains the setup of a spark worker node that can join the spark cluster using the spark master<BR>
  spark-submit/   contains the setup of a node that can submit jobs in the cluster (using pyspark, sparkshell or spark-submit)<BR>

  spark-composer/ contains the yaml file to istantiate a cluster (1 master, 4 workers. All nodes with 1 core and 1 GB od RAM)and create a dedicated network.
</pre>

# Credits
I used as starting point this <a href="https://github.com/karkaratz/docker-spark-3.0.1">previous project</a>

# Preliminary Information
You need to have a working version of docker and docker-compose.<br>
$YOURTAG, in the commands below,should be something like repository:image_name. As an example, the base image I published on Docker Hub is karkaratz/spark-3.5:spark-base <BR>
You need a machine with at least 8G of RAM to run the cluster with 5 nodes. Otherwise you can lower the number of nodes in docker-compose.yml.

# If you want to skip the Build phase and Just Run the Cluster
Jump to START THE CLUSTER. The docker-compose.yml is configured to gather the images that are already available in Docker Hub.

# Images generation
The following build operations will require an internet connection and the download of other related images, the first step will take few minutes.

<pre>docker image build  --tag $YOURTAG:spark-base ./spark-base/  # Generates Base image

docker image build  --tag $YOURTAG:spark-master ./spark-master/   # Generates Spark Master image

docker image build  --tag $YOURTAG:spark-worker ./spark-worker/   # Generates Spark Worker image

docker image build  --tag $YOURTAG:spark-submit ./spark-submit/   # Generates Spark Submit image
</pre>

Now it's time to check that images have been created.
<pre>docker images</pre>

You should see something similar to the following:
<pre>
REPOSITORY              TAG                 IMAGE ID            CREATED             SIZE
karkaratz/spark-3.5   spark-submit        4942c0687d38        2 hours ago         634MB
karkaratz/spark-3.5   spark-worker        5b0a1d93270e        14 hours ago        634MB
karkaratz/spark-3.5   spark-master        c6c917f8aabb        14 hours ago        634MB
karkaratz/spark-3.5   spark-base          415d9d7beab6        14 hours ago        634MB
</pre>

# Edit docker-compose.yml
You need to edit the spark-composer/docker-compose.yml file in order to substitute karkaratz/spark-3.5 with $YOURTAG (The tag you selected earlier) otherwise the cluster would be started with images that are already created in Docker Hub.

# Start the cluster

Now you are ready to run the Spark Standalone Cluster.

<pre>cd spark-composer
docker-composer up
</pre>
Wait for few seconds and you sould see something like:<br>
<pre>Creating spark-master ... done
Creating spark-worker-2 ... done
Creating spark-worker-4 ... done
Creating spark-worker-3 ... done
Creating spark-worker-1 ... done
</pre>

# Accessing Spark Web UI
Open your favourite browser on https://10.5.0.2:8080/ <br>
You should see the worker nodes and 0 applications running.

# Run a Pyspark shell on the cluster
<pre>docker run --network spark-composer_spark-network -it karkaratz:spark-submit /bin/bash
bash-4.3# /spark/bin/pyspark --master $SPARK_MASTER
</pre>

At this point you should be able to run pyspark jobs from the pyspark shell:
<pre>
Type "help", "copyright", "credits" or "license" for more information.

Welcome to
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /__ / .__/\_,_/_/ /_/\_\   version 3.5
      /_/

>>>
</pre>

Go back to https://10.5.0.2:8080/ and refresh<br>
You should see the worker nodes and the running Pyspark shell application.

If you want to change CORE, RAM and OTHER parameters change the configuration in the shell file: spark-composer/env/spark-worker.sh
