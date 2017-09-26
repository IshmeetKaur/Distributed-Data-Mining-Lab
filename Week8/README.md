## Elastic Search Installation , LocText Installation & Parsing of Full Text Papers : Week 8

## Description
This week we would explore Elastic Search, Install LocText and Parse full text Papers(XML format) by mounting the NFS Server and save the parsed dataset in elastic search(json format)

### LocText Installation

[LocText](https://github.com/Rostlab/LocText) is a repository of RostLab for mining the relationship of Proteins <--> Cell Compartments. Our aim is to run PubMed abstracts as well as full text papers , extract the features , save it to elastic search and find relations.Since the [Requirements](https://github.com/Rostlab/LocText#requirements) involved Python 3.5 and Non-packaged dependencies , 

* [nalaf](https://github.com/Rostlab/nalaf)
* [STRING tagger](https://github.com/juanmirocks/STRING-tagger-server)

### STRING tagger Installation

Pre-requisites involved  [Docker](https://git.rostlab.org/Lectures/ddmlab-sose17/wikis/week7_dataengineers#docker-installation) installation.

```
git clone https://github.com/juanmirocks/STRING-tagger-server.git
cd STRING-tagger-server
docker build -t tagger .
docker run -p 5000:5000 tagger
```

![docker_build_string_tagger](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week8/docker_build_string_tagger.png)

![docker_run](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week8/docker_run.png)

Check whether the server is running: localhost:5000/ should display a 'Welcome' message.

![localhost_5000](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week8/localhost_5000.png)

### Nalaf Installation
[nalaf](https://github.com/Rostlab/nalaf) is a NLP framework written in python. The goal is to be a general-purpose module-based and easy-to-use framework for common text mining tasks. At the moment two tasks are covered: named-entity recognition (NER) and relationship extraction. 

```
git clone https://github.com/Rostlab/nalaf.git
cd nalaf
python3 setup.py install
python3 -m nalaf.download_corpora
```
### LocText Installation

```
git clone https://github.com/Rostlab/LocText.git
cd LocText
pip3 install .
python -m loctext.download_data
python -m spacy download en
```

![loctext_installation.png](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week8/loctext_installation.png)



### What is Elastic Search?

[Elasticsearch]() is a highly scalable open-source full-text search and analytics engine. It allows you to store, search, and analyze big volumes of data quickly and in near real time. It is generally used as the underlying engine/technology that powers applications that have complex search features and requirements.

![Scaling-Elasticsearch-Typical-Architectur-700x186](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week8/Scaling-Elasticsearch-Typical-Architectur-700x186.jpg)

Elasticsearch is often used in the so-called ELK pipeline for log file collection, analysis and visualization:

**E**lasticsearch is for searching, analyzing, and storing your data
**L**ogstash (and Beats) is for collecting and transforming data, from any source, in any format
**K**ibana is a portal for visualizing the data and to navigate within the elastic stack.

![Elasticsearch__Hello_World__Example](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week8/Elasticsearch__Hello_World__Example.png)


### Multi-Node Elastic Search Installation

We will install multi-node elastic search, the following steps were taken

**Update all the servers** and edit /etc/hosts file of all the nodes and make entries for all the nodes for the hostnames as shown below. Change the IP addresses with the IP addresses of your VM’s.

```
sudo apt-get update
nano  /etc/hosts 
```

![etc_hosts_multinode.png](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week8/etc_hosts_multinode.png)


 **Install java**
Chek installation by 
```
java -version
```

 **Download and Install** Elastic Search on all the nodes.
By now, Elasticsearch should be running on port 9200 individually. You can test it with curl, the command line client-side URL transfers tool and a simple GET request like this:

```
wget https://download.elastic.co/elasticsearch/release/org/elasticsearch/distribution/deb/elasticsearch/2.3.1/elasticsearch-2.3.1.deb
sudo dpkg -i elasticsearch-2.3.1.deb
sudo service elasticsearch start
 curl -X GET 'http://localhost:9200'
```

![Download_install_es.png](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week8/Download_install_es.png)

The above output shows the name of the node, cluster name, and few other details.If you do not specify a node name in the configuration, elasticsearch assigns a random name on every restart.

**Configure**
 Now, the elasticsearch.yml file has to be edited for the configuring the node as a client node. Open the elasticsearch.ym file located in /etc/elasticsearch directory and change the configurations as follows.

The configuration file has many sections like cluster, node, paths etc.

![configure_es.png](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week8/configure_es.png)

The configuration file is for master and clients can be configured as mentioned in https://devopscube.com/how-to-setup-an-elasticsearch-cluster/.

  Save the file and **restart** the elasticsearch service for changes.

```
sudo service elasticsearch restart
```
 Open /etc/security/limits.conf file to **change the file limits** that can be used. 

```
*        soft   nofile   64000
*        hard   nofile   64000
root     soft   nofile   64000
root     hard   nofile   64000
```

 Open /etc/pam.d/common-session file and add the following line.

```
session required                        pam_limits.so
```
 Open /etc/pam.d/common-session-noninteractive and add the following.

```
session required                        pam_limits.so
```

 You need to set an environment variable for elasticsearch heap size. You can do this by editing the /etc/environment file. The file should look like the following.

```
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games"
ES_HEAP_SIZE="512M"
```

After restarting the elastic search server and local port forwarding to port 9200, we can get the configuration of each node.

![localhost_9200.png](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week8/localhost_9200.png)

Installation of **sense** extension, we can see the elastic search server. 

![sense](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week8/sense.png)

### Mounting NFS server

On the client server, we need to install a package called nfs-common, which provides NFS functionality without including unneeded server components. We will refresh the local package index prior to installation to ensure that we have up-to-date information:

```
sudo apt-get update
sudo apt-get install nfs-common
```

Creating the Share Directories/mounting point on the Host where we will mount the NFS Server

```
sudo mkdir -p /mnt/nfs
```

Mounting the Directories on the Client. Since we have some place to put the NFS shares, we can mount the shares by addressing our host server, which is 141.40.254.15

```
sudo mount 141.40.254.15:/datastore /mnt/nfs/
```
![Mounting_the_NFS_Steps](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week8/Mounting_the_NFS_Steps.png)

These commands should mount the shares from the host computer onto the client machine. We can check use the command df -h which gives a more human readable output.
```
df -h
```

We can mount the remote NFS shares automatically at boot by adding them to /etc/fstab file on the client.

```
sudo nano /etc/fstab
```

![Making_NFS_Mount_Persistant](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week8/Making_NFS_Mount_Persistant.png)

At the bottom of the file, we're going to add a line for our shares which will look like this:
```
141.40.254.15:/datastore  /mnt/nfs/  nfs auto,noatime,nolock,bg,nfsvers=4,sec=krb5p,intr,tcp,actimeo=1800 0 0
```

![Accessing_the_Mounted_Share](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week8/Accessing_the_Mounted_Share.png)

### Parsing the Data ###

Connecting to Elastic Search via Python and parsing the PubMed Texts XML

```
import requests
res = requests.get('http://localhost:9200')
```

We establish a connection to the ElasticSearch server

```
from elasticsearch import Elasticsearch
es = Elasticsearch([{'host': 'localhost', 'port': 9200}])
```

Parsing of PubMed Text XMLs and then converting them to JSONs so as to be stored in the ElasticSearch

We are using pubmed_parser library for the same

```
import pubmed_parser as pp
import json
path_xml = pp.list_xml_path('data') # list all xml paths under directory
pubmed_dict = pp.parse_pubmed_xml(path_xml[0]) # dictionary output
samplePaper = json.dumps(pubmed_dict)
```

Then, we create a new index in our ElasticSearch and store this parsed JSON file into it

es.index(index='pubmed', doc_type='paper', id=1,  body=samplePaper)

![SamplePaperElasticSearch](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week8/SamplePaperElasticSearch.png)


**Remarks**: Automation of this script and fetching all the full text papers from the NFS share still needs to be done, since the final schema of the elasticsearch index is still unknown.

## TroubleShooting

1. Schema for the elastic search index is still not known.
2. All the machines not getting identified by the master even after installing the GUI plugin.


## References
*  https://github.com/juanmirocks/STRING-tagger-server/
*  https://github.com/Rostlab/nalaf
* https://devopscube.com/how-to-setup-an-elasticsearch-cluster/
* https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-elasticsearch-on-ubuntu-14-04




