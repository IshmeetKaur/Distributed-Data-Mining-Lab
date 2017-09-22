## Exploration of Hadoop & Spark (Week 3)

## Description
This week we would setup a multi node Spark installation up and running.We would explore Hadoop and Spark by running different examples as well.

### **Spark**
[Apache Spark](https://spark.apache.org/docs/latest/) is a fast and general-purpose cluster computing system. It provides high-level APIs in Java, Scala, Python and R, and an optimized engine that supports general execution graphs. It also supports a rich set of higher-level tools including Spark SQL for SQL and structured data processing, MLlib for machine learning, GraphX for graph processing, and Spark Streaming.

![driver-sparkcontext-clustermanager-workers-executors](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week3/driver-sparkcontext-clustermanager-workers-executors.png)

Spark uses a master/worker architecture. There is a driver that talks to a single coordinator called master that manages workers in which executors run.

### Multi Node Spark Installation

**Add Entries in hosts file**

Edit hosts file, similar to the Hadoop cluster configuration.

```
$sudo nano /etc/hosts
```

Now add entries of master and slaves.

**Install Java**

Since we installed java while installing hadoop, we can check the java version by

```
java -version
```

**Install Scala**

Install scala on master as well as slaves.

```
$sudo apt-get install scala
```

**Configure SSH**

```
$ssh-keygen -t rsa -P ""
```
Configure passwordless SSH by copying the content of .ssh/id_rsa.pub (of master) to .ssh/authorized_keys (of all the slaves as well as master)

Check by SSH to all the Slaves

```
$ssh datanode1
$ssh datanode2
$ssh datanode3
$ssh datanode4
```
**Install Spark**

Install spark on all slaves as well as master and extract it.
```
$wget http://artfiles.org/apache.org/spark/spark-2.1.1/spark-2.1.1-bin-hadoop2.7.tgz
$tar xzf spark-2.1.1-bin-hadoop2.7.tgz
```
![install_spark](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week3/install_spark.png)

**Setup Configuration**

*  Edit .bashrc

Edit .bashrc file located in user’s home directory and add following environment variables:
```
export JAVA_HOME=<path-of-Java-installation> (eg: /usr/lib/jvm/java-7-oracle/)
export SPARK_HOME=<path-to-the-root-of-your-spark-installation>(eg: /home/dataflair/spark-2.0.0-bin-hadoop2.6/)
export PATH=$PATH:$SPARK_HOME/bin
```

* Edit  spark-env.sh

Edit configuration file spark-env.sh (in $SPARK_HOME/conf/) and set following parameters.
```
$cp spark-env.sh.template spark-env.sh
export JAVA_HOME=<path-of-Java-installation> (eg: /usr/lib/jvm/java-7-oracle/)
export SPARK_WORKER_CORES=8
```
![spark_env.sh](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week3/spark_env.sh.png)

* Add slaves

Create configuration file slaves (in $SPARK_HOME/conf/) and add following entries:

```
datanode1
datanode2
datanode3
datanode4
```

Copy **configuration** file to all nodes.Copy spark-env.sh and slaves to all nodes

```
$scp spark-env.sh datanode1:/usr/local/spark/conf
$scp spark-env.sh datanode2:/usr/local/spark/conf
$scp slaves datanode1:/usr/local/spark/conf
$scp slaves datanode2:/usr/local/spark/conf
```

**Start Spark Cluster**

In order to start Spark Services, similar to hdfs we need to give the following command on master

```
$sbin/start-all.sh
```

![start_spark_cluster](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week3/start_spark_cluster.png)

**Check whether services have been started**

* Check daemons on Master and slave by:

```
$jps
```

### Spark Web UI

Spark Web UI is availaible at port 8080, datanode2 at 8081 ,node3 at 8082.

![Spark_Master_Web_UI](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week3/Spark_Master_Web_UI.png)


### Troubleshooting

Initially the live nodes in the ALIVE NODES was only for master. To enable the worker nodes to communicate with the master, we need to update the iptables and open the port IDs by the following command. 


![Troubleshooting](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week3/troubleshooting.png)



### Examples

![examples_1](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week3/examples_1.png)

![examples_2(textfile_first_spark).png](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week3/examples_2(textfile_first_spark).png)


### References
1. https://spark.apache.org/docs/latest/index.html
2. http://data-flair.training/blogs/apache-spark-installation-on-multi-node-cluster-step-by-step-guide/
3. https://www.tutorialspoint.com/apache_spark/apache_spark_installation.htm









