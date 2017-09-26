## Programming Experience on Hadoop and Spark (Week 4)

## Description
This week we would get a hands on programming experience with Hadoop as well as Spark. MLlib would also be explored.
After setting up a Hadoop and Spark Cluster, we tried to run some examples on both Hadoop and Spark Cluster with different language interfaces.This would help us run distributed jobs (comprising of several jobs) via an application framework(for instance zeppelin) or through the terminal respectively.

### HDFS

HDFS is structured similarly to a regular Unix filesystem except that data storage is distributed across several machines. It is not intended as a replacement to a regular filesystem, but rather as a filesystem-like layer for large distributed systems to use. It has in built mechanisms to handle machine outages, and is optimized for throughput rather than latency.

The types of machine in a HDFS cluster:

* Datanode - where HDFS actually stores the data, there are usually quite a few of these.
* Namenode - the ‘master’ machine. It controls all the meta data for the cluster. Eg - what blocks make up a file, and what datanodes those blocks are stored on.
* Secondary Namenode - this is NOT a backup namenode, but is a separate service that keeps a copy of both the edit logs, and filesystem image, merging them periodically to keep the size reasonable.


![HDFS_Architecture.png](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week4/HDFS_Architecture.png)

## Visualizing Job Status (Hadoop and Spark Cluster)

Hadoop provides a Web User Interface to both HDFS and YARN Resource Manager, which are critical in a distributed setup.

**YARN**

[YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) is a distributed resource management system. The fundamental idea of YARN is to split up the functionalities of resource management and job scheduling/monitoring into separate daemons. The idea is to have a global ResourceManager (RM) and per-application ApplicationMaster (AM). An application is either a single job or a DAG of jobs.
![yarn_on_8088](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week4/yarn_on_8088.png)

**SUMMARY OF HDFS ,MASTER & DATANODE** 

![Hadoop_Ports](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week4/Hadoop_Ports.png)

![master_on_50070](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week4/master_on_50070.png)

![data_nodeon50075](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week4/data_nodeon50075.png)

### HADOOP EXAMPLES

List of available examples can be found at:

```
yarn jar /usr/local/hadoop-2.8.0/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.8.0.jar 
```

![examples_hadoop_1](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week4/examples_hadoop_1.png)

The pi example  can be run with 16 maps and 1,000,000 samples per map, enter the following command:

```
yarn jar /usr/local/hadoop-2.8.0/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.8.0.jar pi 16 1000000
```

![pi_example_16_maps](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week4/pi_example_16_maps.png)


*First we need to copy a file (input) from the local virtual machine to HDFS. To view the files mounted on the Hadoop Distributed File System, we can use the command:
```
hdfs dfs -ls /
hdfs dfs -ls 
```
![MapReduce_Work_Structure](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week4/MapReduce_Work_Structure.png)



In order to run the map reduce job (Wordcount) for the input mounted at /user/4300.txt, we need to provide the output location as well.

```
 hadoop jar /usr/local/hadoop-2.8.0/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.8.0.jar wordcount /user/4300.txt dft-output18 
```

To view output

```
 hadoop fs -getmerge dft-output18 ~/dft/out.txt
 nano  ~/dft/out.txt
```
![wordcount-output](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week4/wordcount-output.png)


* For running the wordcount example in **Python**

The Hadoop Streaming API is used for passing data between our Map and Reduce code via STDIN and STDOUT.

```
bin/hadoop jar contrib/streaming/hadoop-*streaming*.jar \
-mapper "/usr/local/mapper.py python" \
-reducer "/usr/local/reducer.py python"\
-input /user/gutenberg/* -output /user/gutenberg-output
```

![running_hadoop_python](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week4/running_hadoop_python.png)

![python_mapreduce_wordcount](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week4/python_mapreduce_wordcount.png)

## MLLIB

MLlib is Spark’s machine learning (ML) library. Its goal is to make practical machine learning scalable and easy. At a high level, it provides tools such as:

* ML Algorithms: common learning algorithms such as classification, regression, clustering, and collaborative filtering
* Featurization: feature extraction, transformation, dimensionality reduction, and selection
* Pipelines: tools for constructing, evaluating, and tuning ML Pipelines
* Persistence: saving and load algorithms, models, and Pipelines
* Utilities: linear algebra, statistics, data handling, etc

It is available in Spark development environment.

**Python**
We used to represent a labeled feature vector in a NumPy array, where the first entry corresponds to the label and the rest are features. This representation is replaced by class LabeledPoint, which takes both dense and sparse feature vectors.

![mllib_pyspark](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week4/mllib_pyspark.png)

**Scala**

![spark_scala_mllib](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week4/spark_scala_mllib.png)


### TroubleShooting

*  **Error** - Datanode not connecting to master at 9000 port.The cluster is connected but the live nodes are one(for only the master)

![error1](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week4/error1.png)

**Solution** disable ipv6.
add entry to the Ip Tables.

```
iptables -I INPUT -p tcp --dport 9000  -j ACCEPT
iptables -I INPUT -p tcp --dport 9001  -j ACCEPT
sudo ufw disable
sudo ufw enable
```
On each of the datanodes/tasktracker:

```
iptables -I INPUT -p tcp --dport 50010  -j ACCEPT
sudo ufw disable
sudo ufw enable
```

*   In order to visualize the status of hadoop and spark cluster, ssh [local port forwarding](http://blog.trackets.com/2014/05/17/ssh-tunnel-local-and-remote-port-forwarding-explained-with-examples.html) needs to be done.

![local_port_forwarding_yarn](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week4/local_port_forwarding_yarn.png)

``` 
ssh -L 8088:localhost:8088 root@10.155.209.10
```
10.155.209.10 is the IP Address of the Master.

### References

*  http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/
*  http://cs.smith.edu/dftwiki/index.php/Hadoop_Tutorial_1_--_Running_WordCount
*  http://www.informit.com/articles/article.aspx?p=2453563
*  https://spark.apache.org/docs/1.2.0/mllib-guide.html




