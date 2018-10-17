# Patterns for Continuous Integration with Docker using Jenkins 

## Install Jenkins from Docker 

see https://github.com/jenkinsci/docker/blob/master/README.md

### Dockerfile 
 
```docker
FROM jenkins/jenkins:lts

USER root

RUN apt-get update && \
apt-get -y install apt-transport-https \
    ca-certificates \
    curl \
    gnupg2 \
    software-properties-common && \
curl -fsSL https://download.docker.com/linux/$(. /etc/os-release; echo "$ID")/gpg > /tmp/dkey; apt-key add /tmp/dkey && \
add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") \
    $(lsb_release -cs) \
    stable" && \
apt-get update && \
apt-get -y install docker-ce

RUN apt-get install -y docker-ce

RUN usermod -a -G root jenkins

RUN usermod -a -G sudo jenkins

USER jenkins

```

### Build Docker Image 

```sh
docker build -f Dockerfile -t my-jenkins . 
```

### Start Container 

```sh
docker run -p 8080:8080 -p 50000:50000 --name myjenkins  -v /var/run/docker.sock:/var/run/docker.sock my-jenkins  
```
  
### Enter in Docker Container and Execute

```sh
docker exec -it myjenkins bash
```

__Note__: use option ```-u 0``` to enter as root 

```sh
git config --global http.sslverify false
```
