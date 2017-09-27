# Distributed Data Mining Lab - TUM SS_2017

# Summary

The wiki contains the resources on How to Setup a Distributed Environment for Data Mining and Analysis.

#### Technologies/Resources involved are:

 1. [HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html)
 2. [Hadoop](https://hadoop.apache.org/)
 3. [Yarn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)
 4. [Spark](https://spark.apache.org/)
 5. [MLlib](https://spark.apache.org/mllib/)
 6. [Docker](https://docs.docker.com/)
 7. [ElasticSearch](https://www.elastic.co/)
 8. [Kibana](https://www.elastic.co/products/kibana)
 9. [LocText](https://github.com/Rostlab/LocText)
 10. [Nalaf](https://github.com/Rostlab/nalaf)
 11. [String-Tagger](https://github.com/juanmirocks/STRING-tagger-server)
 12. [PubMed](https://www.ncbi.nlm.nih.gov/pubmed/)

## Weekly Progress

The wiki for the Distributed Data Mining lab course would be available per week.

*   [Week 1 - Setting up the Virtual Machine](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week1/)
    *  Introduction to OpenNebula
    *  VM Creation Steps
    *  Logging in the Virtual Machine through SSH

*   [Week 2 - Setting up a Single and Multinode Cluster ](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week2/)
    *  Introduction to Hadoop
    *  Single Node Hadoop Installation
    *  Multi Node Hadoop Installation

*   [Week 3 - Exploration of Hadoop & Spark ](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week3/)
    *  Introduction to Spark
    *  Multi Node Spark Installation
    *  Examples of Hadoop and Spark

*   [Week 4 - Programming Experience on Hadoop and Spark ](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week4/)
    *  Introduction to MLlib
    *  Web UI - Hadoop , Yarn and Spark Cluster
    *  Examples of Hadoop and Spark
    *  Troubleshooting

*   [Week 5 - Programming Experience on Hadoop and Spark (continued)](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week5/)
    *  Prime Number Examples
    *  Spark Examples
    *  Performance Analysis
    *  Troubleshooting

*   [Week 6 - Extraction of NCBI Database : Part1 ](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week6/)
    *  NCBI API
    *  Retrieving Pubmed Reports
    *  Troubleshooting
*   [Week 7 - Extraction of Elsevier Data: Part2 ](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week7/)
    *  Elsevier API
    *  Nalaf, LocText, StringText
    *  Docker
*   [Week 8 - Elastic Search Installation , LocText Installation & Parsing of Full Text Papers](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week8/)
    *  LocText Installation
    *  Multi Node Elastic Search Installation
    *  Parsing of Pubmed Records

*   [Week 9 - Storing Parsed Papers in Elastic Search](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week9/)
    *  Basic architecture
    *  Elastic Search Storage
    *  Remarks

*   [Week 10 - Text-mine the relationship of Protein & Cell with LocText](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week10/)
    *  Elastic Search Records
    *  Mining with LocText
    *  Troubleshooting


*   [Week 11 - Text-mine the relationship of Protein & Cell with LocText with Map Reduce](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week11/)
    *  Creating Additional Disks for Elastic Search Records
    *  Mining with LocText and Nalaf
    *  Writing a map Reduce Job
    *  Remarks

*   [Week 12 - Text-mine the relationship of Protein & Cell with LocText with Map Reduce](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week12/)

    *  Mining with LocText and Nalaf
    *  map Reduce Job
    *  Kibana Visualizations
    *  Troubleshooting

*   [Final Presentation](https://prezi.com/zwm41xzg55y_/present/?auth_key=sjimnk7&follow=ohe0umkdjyyu&kw=present-zwm41xzg55y_&rc=ref-250904995#60)[prezi.pdf](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/prezi.pdf)

## References
1. https://linoxide.com/cluster/setup-hadoop-multi-node-cluster-ubuntu/ (Multinode Hadoop Setup)
2. http://data-flair.training/blogs/apache-spark-installation-on-multi-node-cluster-step-by-step-guide/  (Multinode Spark Setup)
3. https://spark.apache.org/docs/1.2.0/mllib-guide.html (MLLib Documentation)
4. http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/ (MapReduce in Python)
5. http://cs.smith.edu/dftwiki/index.php/Hadoop_Tutorial_1_--_Running_WordCount (Map Reduce Tutorial)
6. https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-getting-started (Docker Installation)
7. https://github.com/Rostlab/LocText (Loctext)
8. https://github.com/Rostlab/nalaf (Nalaf)
9. https://github.com/titipata/pubmed_parser(Pubmed Parser)
10. https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html (Elastic Search)
11. http://hadoop.apache.org/
12. https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html
13. https://rubenmiddeljans.files.wordpress.com/2015/08/hadoop-cluster.jpg
14. http://spark.apache.org/faq.html
15. https://spark.apache.org/mllib/
16. https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-ubuntu-16-04
17. https://docs.docker.com/engine/installation/linux/ubuntu/