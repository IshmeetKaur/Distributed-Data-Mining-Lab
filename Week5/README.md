## Programming Experience on Hadoop and Spark (continued) - Week 5

## Description
This week we would continue running programming examples on Hadoop as well as Spark. Prime Number Generation on Hadoop and other examples would be run on Spark.This would help us compare the performance of distributed jobs (comprising of several jobs) for the hadoop and spark cluster via an application framework(for instance zeppelin) or through the terminal respectively.

### Prime Numbers Example:
In order to generate the prime numbers from 1 to 1000000000,the following scripts were written:

**Mapper**

![mapper_prime](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week5/mapper_prime.png)

**Reducer**

![reducer_prime](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week5/reducer_prime.png)

**Number Generation**

![number_generation](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week5/number_generation.png)

**Running the HDFS Job (Input-100 numbers)**

![prime_numbers_100_1](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week5/prime_numbers_100_1.png)

**Result**

There are 25 prime numbers till 100.

![hdfs_map_reduce_100_3_25](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week5/hdfs_map_reduce_100_3_25.png)

**Running the HDFS Job (Input-100 numbers)**

```
hadoop jar /usr/local/hadoop-2.8.0/share/hadoop/tools/lib/hadoop-streaming-2.8.0.jar -mapper "python /usr/local/practice/python/mapper_prime.py" -reducer "python /usr/local/practice/python/reducer_prime.py" -input "/user/primeNumber/numbersToFindPrime.txt" -output "/user/primeNumber/primeNumerOutput_cluster"

hadoop fs -getmerge /user/primeNumber/primeNumerOutput_cluster prime_number_100.txt
```

![Screenshot_from_2017-06-05_00_10_01](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week5/Running_HDFS_Job_input_100_numbers.png)

**Running the HDFS Job (Input-1000000 numbers)**
![Screenshot_from_2017-06-05_00_10_07](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week5/Running_HDFS_Job_input_1000000_numbers.png.png)

**Result**

Prime numbers till 1000000.

![python_prime_final_to_be_in_wiki](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week5/prime_numbers_till_10000000_1.png)

![output-successful_10to6](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week5/output-successful_10to6.png)

### Job Status

The job status for running, failed and completed jobs can be seen through the UI. The logs for a completed job can also be exracted.

![job_status_0](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week5/job_status_0.png)

![job_status](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week5/job_status.png)


### Spark Examples:

**WordCount**
First, let's grab a text-based dataset that will be used for counting the words.We used the map(func) to transform the words RDD into an RDD of (word, 1) key-value pairs: 

![Tutorial_Spark4](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week5/Tutorial_Spark4.png)

![Tutorial_Spark6](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week5/Tutorial_Spark6.png)

![Tutorial_Spark7](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week5/Tutorial_Spark7.png)

![word_Count_Spark](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week5/word_Count_Spark.png)

![pyspark_WC](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week5/pyspark_WC.png)

**Pi Estimation**

![pi_pyspark](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week5/pi_pyspark.png)


### TroubleShooting

1. The input file(around 9.5 GB) made the system ran out of memory resulting the HDFS system run in Safe Mode.

![Troubleshooting](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week5/Troubleshooting.png)

In order to make the system run out of safe mode, we tried the following

```
hdfs dfsadmin -safemode leave
hdfs dfsadmin -safemode get
```

Mounted a disk for 40GB for memory issues:
![mount_40GB_disk](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week5/mount_40GB_disk.png)


2. **Java Heap Space Error**

Due to memory issues and inefficient mapper function,[Java Heap space errors](https://chawlasumit.wordpress.com/tag/hadoop-java-heap-space/)

![yarn_logs_failure](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week5/yarn_logs_failure.png)

Heap size Environment variable needs to be changed in mapred-site.xml

![increasing_java_heap_space](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week5/increasing_java_heap_space.png)

### References

1. https://spark.apache.org/examples.html
2. http://spark.apache.org/docs/2.1.0/quick-start.html
3. https://nyu-cds.github.io/python-bigdata/03-spark/
4. https://mapr.com/products/mapr-sandbox-hadoop/tutorials/spark-tutorial/


