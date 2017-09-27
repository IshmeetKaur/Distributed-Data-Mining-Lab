## Text-mine the relationship of Protein & Cell with LocText with Map Reduce: Week 11

## Description
This week we would continue using LocText to mine the relationships between the Protein and cell using mapreduce job. Also, the work on the problems of text mining of [Week 10](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week10/) would be solved. 

### Creating Additional Disks for Elastic Search Records

[Last week](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week10/), a POC was made using only 11059 records,to mine the fulltext records due to memory issues.This week we mounted 2 additional disks,to store all the records.The steps of creating a new storage block to store the parsed papers along with the screenshots in the Elasticsearch are as mentioned below:

  Add additional storage using the OpenNebula platform to the VM.

![additional_storage](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week11/additional_storage.png)

 Using the fdisk utilities in Ubuntu, create a new partition of this attached storage.

![fdisk_utilities](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week11/fdisk_utilities.png)

 Once created we format it in ext4 format.

![format](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week11/format-ext4.png)

 Now after the partition is finally created and formatted, we mount it to our pubmedelasticsearch directory.

![mount_to_pbes.png](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week11/mount_to_pbes.png)

 After the mounting, we change the path of the elasticsearch data in the elasticsearch.yml file

![es_yml](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week11/es_yml.png)

 We change the ownwership of this directory for read/write access by the root user.

![rw_access](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week11/rw_access.png)

 The elasticsearch is restarted and then using curl we test if everything is working fine.

![check_es](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week11/check_es.png)

This mounts the disk successfully and stores the results in elastic search. To check if the disk was mounted successfully, we can check as shown in the screenshot below.

![successful_mounting](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week11/successful_mounting.png)

### Mining with LocText and Nalaf

![mining_loctext.png](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week11/mining_loctext.png)


* Using the script above as explained last week, we used Loctext to retrieve the ids of the stored records in the elastic search and obtain relations.But any document with no proteins as explained in the [github issue](https://github.com/Rostlab/LocText/issues/40) raised, returned an error.


**Updated Nalaf Installation**

* After the issue was solved, we updated the Loctext, Nalaf and String Tagger repositories. While installing the nalaf library,we were unable to install the packages from python due to linux images as explained in the issue below.

https://askubuntu.com/questions/223248/gzip-stdout-no-space-left-on-device-while-upgrading-the-kernel.

We followed the steps as mentioned in the link to solve the issue. As installation of **setup.py** in nalaf using python3 step was not successful, we updated python packages stepwise as mentioned by the commands below:

```
sudo apt-get install python-pip
sudo pip install -U setuptools 
apt-get install python-numpy
sudo apt-get install python-dev
sudo apt-get install libtiff5-dev libjpeg8-dev zlib1g-dev libfreetype6-dev liblcms2-dev libwebp-dev tcl8.6-dev tk8.6-dev python-tk
pip install pandas
sudo apt-get install libfreetype6-dev
sudo apt-get install libfreetype6-dev libxft-dev
pip install matplotlib
pip install pprint
pip install tabulate
sudo apt-get install libatlas-base-dev gfortran
easy_install scipy
sudo apt-get install build-essential gfortran libatlas-base-dev python-pip python-dev
sudo apt-get install python-matplotlib
sudo pip install -U scikit-learn
sudo pip install pandas 
```

After installing these libraries, we intalled nalaf successfully.

**Updated String Tagger Installation**

Docker run of String Tagger repository failed because of which we had to build the image again. The building of the image gave an error as explained in the screenshot below.

![updated_installation_string_tagger.png](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week11/updated_installation_string_tagger.png)
 
We solved this problem by the steps mentioned below:

First we deleted all containers and images of docker using the commands below:
```
# Delete all containers
docker rm $(docker ps -a -q)
# Delete all images
docker rmi $(docker images -q)
#One liner to stop / remove all of Docker containers:
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)
```

Another workaround

*  Copy the image from one machine to another.

```
sudo docker images
```

*  Get image id by doing:

```
sudo docker images
Say you have image with id "matrix-data"
```

Save image with id:

```
sudo docker save -o /home/matrix/matrix-data.tar matrix-data
```

Copy image from path to any host Now import to your local docker using :

```
sudo docker load -i <path to copied image file>
```

If there is not much space , use the mounted disk with enough space available.
(https://forums.docker.com/t/how-do-i-change-the-docker-image-installation-directory/1169)



*  Add entry to the Ip Tables.

```
iptables -I INPUT -p tcp --dport 5000  -j ACCEPT
sudo ufw disable
sudo ufw enable
```
*  Kill the port if the port is already being used.

```
sudo kill $(sudo lsof -t -i:5000)
```

### Writing a map Reduce Job

We wrote a map reduce job simply accessing the unique IDs of the Pubmedelasticsearch records parsed,

![writing_MR](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week11/writing_MR.png)

These IDs were given as an input in the mapper.py line by line and the reducer.py was used to access these IDs and run the **usingLocText** script explained last week. But the map reduce job didn't finish  because **no schedulers were allocated and the cluster resources were not used.**


## Remarks

1. How to solve the map reduce schedulers problem causing the map reduce job to halt?
2. We will try to retrieve the [NalafDatastructure](https://github.com/Rostlab/nalaf/blob/develop/nalaf/structures/data.py)  in the reducer.py to access the relationships between the IDs and and the cell locations.
3. String Tagger Docker build is still giving an error on one machine in the cluster. 

## References

1. https://github.com/Rostlab/LocText#sample-script
2. https://github.com/Rostlab/LocText/blob/develop/run.py
3. https://github.com/Rostlab/nalaf/blob/develop/nalaf/structures/data.py
4. https://askubuntu.com/questions/223248/gzip-stdout-no-space-left-on-device-while-upgrading-the-kernel

