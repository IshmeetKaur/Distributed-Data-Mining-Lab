## Extraction of Elsevier Data - Week 7

## Description
This week we would explore the Elsevier API for the extraction of full texts. 

### Elsevier API

Elsevier(https://dev.elsevier.com/index.html) provides information and analytics that help institutions and professionals progress science, advance healthcare and improve performance.

https://dev.elsevier.com/api_docs.html provides the APIs and thus programmable access to all the journals.

## API Used

The API we used to download the journals -  

```
https://api.elsevier.com/content/search/author?apikey=933fba13d9006f4a5e750d257ca90e2c
```

Due to authentication issues for TUM, we get an error:
![authentication_error](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week7/authentication_error.png)

So far we’ve put the task of downloading the Elsevier data on hold.

## LocText

We cloned the repository [LocText](https://github.com/Rostlab/LocText),[nalaf](https://github.com/Rostlab/nalaf) and [STRING-tagger-server](https://github.com/juanmirocks/STRING-tagger-server)

### Docker Installation

Docker is the world’s leading software container platform. Developers use Docker to eliminate “works on my machine” problems when collaborating on code with co-workers. Operators use Docker to run and manage apps side-by-side in isolated containers to get better compute density. Enterprises use Docker to build agile software delivery pipelines to ship new features faster, more securely and with confidence for both Linux and Windows Server apps.

```
uname -r
apt-get update
apt-get -y upgrade
```
Once you had fulfilled the requirements to install Docker, then we are ready to go with its installation process. Let’s install Docker by installing the docker-io package now.


```
sudo apt-get update
sudo apt-get -y upgrade
sudo apt-get install linux-image-extra-`uname -r`
sudo apt-key adv --keyserver hkp://pgp.mit.edu:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
echo "deb https://apt.dockerproject.org/repo ubuntu-trusty main" | sudo tee /etc/apt/sources.list.d/docker.list
sudo apt-get update
sudo apt-get install docker-engine
sudo nano /etc/default/ufw
DEFAULT_FORWARD_POLICY="ACCEPT"
sudo ufw reload
sudo docker -d &
```

## Troubleshooting

Installation of docker-io failed to setup the build for string- tagger project.
After reading the post ( https://superuser.com/questions/784258/whats-the-difference-between-docker-io-and-docker), we switched to docker- engine installation .

docker-io is the deb package name used by Ubuntu distribution docker-engine is the deb package name from the official Docker Ubuntu distribution.Probably we want docker-engine because the Ubuntu one is too old and buggy to be used. As of today Ubuntu has 1.6.2 and Docker registry has 1.12.0 !

### References

1. https://dev.elsevier.com/api_docs.html
2. https://www.docker.com/
3. https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-getting-started