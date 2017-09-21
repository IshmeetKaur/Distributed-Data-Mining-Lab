## Setting up a Single and Multinode Cluster (Week 2)

## Description
This week we would setup a multi node Hadoop installation up and running.We would explore Hadoop and the process of establishing a cluster.

### Hadoop

[Hadoop](https://de.hortonworks.com/apache/hadoop/) is a framework written in Java for running applications on large clusters of commodity hardware and incorporates features similar to those of the Google File System (GFS) and of the MapReduce computing paradigm. Hadoop’s HDFS is a highly fault-tolerant distributed file system and, like Hadoop in general, designed to be deployed on low-cost hardware. It provides high throughput access to application data and is suitable for applications that have large data sets.

![hadoop-cluster](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week2/hadoop-cluster.jpg)


The main goal of this exercise is to setup a multi node Hadoop installation up and running.The steps followed for setting up a distributed single node  and then multi node cluster are described below.

### Single Node Hadoop Installation

**Installing Java on Master and Slaves**

Hadoop requires a java installation.

![check_java_installation.png](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week2/check_java_installation.png)


```
$ sudo add-apt-repository ppa:webupd8team/java
$ sudo apt-get update
$ sudo apt-get install oracle-java7-installer
$ sudo update-java-alternatives -s java-7-oracle
```


**Adding a dedicated Hadoop system user**

We will use a dedicated Hadoop user account for running Hadoop. While that’s not required it is recommended because it helps to separate the Hadoop installation from other software applications and user accounts running on the same machine (think: security, permissions, backups, etc).
```
$ sudo addgroup hadoop
$ sudo adduser --ingroup hadoop hduser
# Giving hduser root privileges
$ sudo usermod -a -G sudo hduser
```
![adding_dedicated_user.png](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week2/adding_dedicated_user.png)


**Configuring SSH**

Hadoop requires SSH access to manage its nodes, i.e. remote machines plus your local machine if Hadoop needs to be used. For a single-node setup of Hadoop, we therefore need to configure SSH access to localhost for the hduser user created in the previous section. First SSH needs to be running on the machine and then an SSH key for the hduser needs to be generated.
```
$ su - hduser
$ ssh-keygen -t rsa -P ""
```

This will generate a public/private rsa key pair. The file in which the key (/home/hduser/.ssh/id_rsa) needs to be saved can be altered. This will create a  directory '/home/hduser/.ssh' and the identification is saved. The public key has been saved in /home/hduser/.ssh/id_rsa.pub. The key fingerprint is generated.
Second, you have to enable SSH access to your local machine with this newly created key.

```
$ cat $HOME/.ssh/id_rsa.pub >> $HOME/.ssh/authorized_keys
```
The final step is to test the SSH setup by connecting to your local machine with the hduser user. The step is also needed to save your local machine’s host key fingerprint to the hduser user’s known_hosts file. If you have any special SSH configuration for your local machine like a non-standard SSH port, you can define host-specific SSH options in $HOME/.ssh/config (see man ssh_config for more information).
```
$ ssh localhost
```

**Hadoop Installation**

In order to install hadoop the following commands need to be followed.
```
This is the directory where we are installing hadoop, you can change if you want. But make sure to change the rest of the paths accordingly.
$ cd /usr/local
$ wget http://apache.lauf-forum.at/hadoop/core/hadoop-2.8.0/hadoop-2.8.0.tar.gz
$ sudo tar xzf hadoop-2.8.0.tar.gz
$ sudo mv hadoop-2.8.0 hadoop
$ sudo chown -R hduser:hadoop hadoop
```

***Update $HOME/.bashrc***

Add the following lines to the end of the $HOME/.bashrc file of user hduser. If you use a shell other than bash, you should of course update its appropriate configuration files instead of .bashrc.
```
# Set Hadoop-related environment variables
export HADOOP_HOME=/usr/local/hadoop

# Set JAVA_HOME (we will also configure JAVA_HOME directly for Hadoop later on)
export JAVA_HOME=/usr/lib/jvm/java-7-oracle

# Some convenient aliases and functions for running Hadoop-related commands
unalias fs &> /dev/null
alias fs="hadoop fs"
unalias hls &> /dev/null
alias hls="fs -ls"

# If you have LZO compression enabled in your Hadoop cluster and
# compress job outputs with LZOP (not covered in this tutorial):
# Conveniently inspect an LZOP compressed file from the command
# line; run via:
#
# $ lzohead /hdfs/path/to/lzop/compressed/file.lzo
#
# Requires installed 'lzop' command.
#
lzohead () {
    hadoop fs -cat $1 | lzop -dc | head -1000 | less
}

# Add Hadoop bin/ directory to PATH
export PATH=$PATH:$HADOOP_HOME/bin
```
Update hadoop-env.sh on Master and Slave Nodes.Update JAVA_HOME in /home/hduser/hadoop/etc/hadoop/hadoop_env.sh to following. Do this step on master and every slave node.

**Update Configuration Files**

 Hadoop’s default configurations use hadoop.tmp.dir as the base temporary directory both for the local file system and HDFS.Now we create the directory and set the required ownerships and permissions:
```
$ sudo mkdir -p /app/hadoop/tmp
$ sudo chown hduser:hadoop /app/hadoop/tmp

#if you want to tighten up security, chmod from 755 to 750...
$ sudo chmod 750 /app/hadoop/tmp
```

The rest of the configuration files will be available in the following path - usr/local/hadoop/etc/hadoop/

***core-site.xml***

Add the following snippets between the &lt;configuration&gt; .... &lt;/configuration&gt; tags in the respective configuration XML file.
```
<property>
  <name>hadoop.tmp.dir</name>
  <value>/app/hadoop/tmp</value>
  <description>A base for other temporary directories.</description>
</property>

<property>
  <name>fs.default.name</name>
  <value>hdfs://localhost:54310</value>
  <description>The name of the default file system.  A URI whose
  scheme and authority determine the FileSystem implementation.  The
  uri's scheme determines the config property (fs.SCHEME.impl) naming
  the FileSystem implementation class.  The uri's authority is used to
  determine the host, port, etc. for a filesystem.</description>
</property>
```

***mapred-site.xml***

Before editing mapred-site.xml,you need to rename the mapred-site.xml.template file to mapred-site.xml.For this, you can use:
```
mv mapred-site.xml.template mapred-site.xml
```
and then make changes in the configuration file mapred-site.xml as required. Add the following snippets between the &lt;configuration&gt; .... &lt;/configuration&gt; tags in the respective configuration XML file.
```
<property>
  <name>mapred.job.tracker</name>
  <value>localhost:54311</value>
  <description>The host and port that the MapReduce job tracker runs
  at.  If "local", then jobs are run in-process as a single map
  and reduce task.
  </description>
</property>
```

***hdfs-site.xml***

Add the following snippets between the &lt;configuration&gt; .... &lt;/configuration&gt; tags in the respective configuration XML file.
```
<property>
  <name>dfs.replication</name>
  <value>1</value>
  <description>Default block replication.
  The actual number of replications can be specified when the file is created.
  The default is used if replication is not specified in create time.
  </description>
</property>
```

**Formatting the HDFS filesystem via the NameNode**

The first step to starting up your Hadoop installation is formatting the Hadoop filesystem which is implemented on top of the local filesystem of your “cluster”. You need to do this the first time you set up a Hadoop cluster.
To format the filesystem (which simply initializes the directory specified by the dfs.name.dir variable), run the command:
```
$ hdfs namenode -format
```
![namenode_format](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week2/namenode_format.png)


**Start the Distributed Format System**

Run the following on master node command to start the DFS.You need to navigate to the sbin directory in hadoop.
and then run ***./start-dfs.sh***

**Check Installation**

Once everything is up and running,a nifty tool for checking whether the expected Hadoop processes are running is jps.
```
hduser@ubuntu:/usr/local/hadoop$ jps
```

![jsp_check_hadoop_installation.png](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week2/jsp_check_hadoop_installation.png)


**Stopping your single-node cluster**
```
hduser@ubuntu:~$ /usr/local/hadoop/bin/stop-all.sh
```

### Multi Node Hadoop Installation

We will build a multi-node cluster using the single node Hadoop installations on each virtual machine(4 data nodes and 1 master node). After installing a single node Hadoop cluster on each node, we will merge the five nodes into one single multi node cluster in which 4 nodes would become slaves and one node would become the master.
It would be much easier to track down any problems which we might encounter.


**Networking**

Since all the machines must be able to reach each other over the network, the easiest is to put both machines in the same network with regard to hardware and software configuration. To make it simple, we will assign the IP address (10.155.208.244) to the master machine and the rest to the slave machines. Update /etc/hosts on all the machines with the following lines:

```
10.155.208.244    master
10.155.208.220    datanode3
10.155.208.245    datanode2
10.155.208.227    datanode1
```
![multinode_setup.png](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week2/multinode_setup.png)


**SSH access**

The hduser user on the master must be able to connect 
* to its own user account on the master – i.e. ssh master in this context and not necessarily ssh localhost
* to the hduser user account on the slave (aka hduser@slave) via a password-less SSH login.


The hduser@master’s public SSH key (which should be in $HOME/.ssh/id_rsa.pub) should be added to the authorized_keys file of hduser@slave (in this user’s $HOME/.ssh/authorized_keys). This can be done manually or through the following SSH command.

```
hduser@master:~$ ssh-copy-id -i $HOME/.ssh/id_rsa.pub hduser@datanode1
```

Similar steps would be repeated on all the data nodes. This command will prompt for the login password for user hduser on datanode, then copy the public SSH key of the master to the slave, creating the correct directory and fixing the permissions as necessary.

The final step is to test the SSH setup by connecting with user hduser from the master to the user account hduser on the slave. The step is also needed to save slave’s host key fingerprint to the hduser@master’s known_hosts file.
So, connecting from master to master…

```
 ssh master
 ``` 
 …and from master to slave.
 ```
 ssh datanode1
```
![ssh_access](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week2/ssh_access.png)

**Hadoop Configuration**

**conf/masters(master only)**
The conf/masters file defines on which machines Hadoop will start secondary NameNodes in our multi-node cluster. The primary NameNode and the JobTracker will always be the machines on which you run the bin/start-dfs.sh and bin/start-mapred.sh scripts, respectively (the primary NameNode and the JobTracker will be started on the same machine if you run bin/start-all.sh). On master, update conf/masters that it looks like this:
```
master
```

**conf/slaves(master only)** 
The conf/slaves file lists the hosts, one per line, where the Hadoop slave daemons (DataNodes and TaskTrackers) will be run. We want both the master and the slaves to act as Hadoop slaves because we want both of them to store and process data.

On master, update conf/slaves that it looks like this:
```
datanode1
datanode2
datanode3
```

The conf/slaves file on master is used only by the scripts like bin/start-dfs.sh or bin/stop-dfs.sh. For example, if you want to add DataNodes on the fly, you can “manually” start the DataNode daemon on a new slave machine via bin/hadoop-daemon.sh start datanode. Using the conf/slaves file on the master simply helps you to make “full” cluster restarts easier.

**conf/-site.xml(all machines)**

We must change the configuration files conf/core-site.xml, conf/mapred-site.xml and conf/hdfs-site.xml on all machines as follows.

**conf/core-site.xml (ALL machines)**

First, we have to change the fs.default.name parameter (in conf/core-site.xml), which specifies the NameNode (the HDFS master) host and port. In our case, this is the master machine.
```
<property>
  <name>fs.default.name</name>
  <value>hdfs://master:54310</value>
  <description>The name of the default file system.  A URI whose
  scheme and authority determine the FileSystem implementation.  The
  uri's scheme determines the config property (fs.SCHEME.impl) naming
  the FileSystem implementation class.  The uri's authority is used to
  determine the host, port, etc. for a filesystem.</description>
</property>
```
![core-site.xml](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week2/core-site.xml.png)


**conf/yarn-site.xml (ALL machines)**

![yarn.sh](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week2/yarn.sh.png)

![yarn-site](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week2/yarn-site.png)


<property>
 <name>yarn.nodemanager.aux-services</name>
 <value>mapreduce_shuffle</value>
</property>
<property>
 <name>yarn.resourcemanager.scheduler.address</name>
 <value>master:8030</value>
</property> 
<property>
 <name>yarn.resourcemanager.address</name>
 <value>master:8032</value>
</property>
<property>
  <name>yarn.resourcemanager.webapp.address</name>
  <value>master:8088</value>
</property>
<property>
  <name>yarn.resourcemanager.resource-tracker.address</name>
  <value>master:8031</value>
</property>
<property>
  <name>yarn.resourcemanager.admin.address</name>
  <value>master:8033</value>
</property>

**conf/mapred-site.xml (master only)**

Second, we have to change the mapred.job.tracker parameter (in conf/mapred-site.xml), which specifies the JobTracker (MapReduce master) host and port. Again, this is the master in our case.
```
<property>
  <name>mapred.job.tracker</name>
  <value>master:54311</value>
  <description>The host and port that the MapReduce job tracker runs
  at.  If "local", then jobs are run in-process as a single map
  and reduce task.
  </description>
</property>
```

**conf/hdfs-site.xml (ALL machines)**

Third, we change the dfs.replication parameter (in conf/hdfs-site.xml) which specifies the default block replication. It defines how many machines a single file should be replicated to before it becomes available. If you set this to a value higher than the number of available slave nodes (more precisely, the number of DataNodes), you will start seeing a lot of “(Zero targets found, forbidden1.size=1)” type errors in the log files. The default value of dfs.replication is 3. We have 3 data nodes, so we set dfs.replication to 3.
```
<property>
  <name>dfs.replication</name>
  <value>3</value>
  <description>Default block replication.
  The actual number of replications can be specified when the file is created.
  The default is used if replication is not specified in create time.
  </description>
</property>
```
![hdfs-core](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week2/hdfs-core.png)

This completes the installation and the configuration.

![complete_running](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week2/complete_running.png)


### References:

1. http://www.michael-noll.com/tutorials/running-hadoop-on-ubuntu-linux-multi-node-cluster/
