# Patterns for Continuous Integration with Docker using Jenkins 

## Install Jenkins via Docker 

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
docker build -f Dockerfile_Jenkins -t my-jenkins . 
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

## Using credentials with Jenkins 

Necessary for 

* Docker Hub account 
* Coveralls account (option)

see https://jenkins.io/doc/book/using/using-credentials/ 

* From the Jenkins home page (i.e. the Dashboard of the Jenkins classic UI), click Credentials > System on the left.
* Under System, click the Global credentials (unrestricted) link to access this default domain.

![Using credentials with Jenkins](https://raw.githubusercontent.com/gtesei/Patterns_for_Continuous_Integration_Docker_Jenkins/master/img/jen_cred.PNG)


![Using credentials with Jenkins](https://raw.githubusercontent.com/gtesei/Patterns_for_Continuous_Integration_Docker_Jenkins/master/img/jen_cred.PNG)

![Using credentials with Jenkins](https://raw.githubusercontent.com/gtesei/Patterns_for_Continuous_Integration_Docker_Jenkins/master/img/jen_cred2.PNG)

## Creating Jenkins Pipeline for a Git Project 

#### New Item >> Enter pipeline name and select Pipeline >> OK 

![New Item >> Enter pipeline name and select Pipeline >> OK](https://raw.githubusercontent.com/gtesei/Patterns_for_Continuous_Integration_Docker_Jenkins/master/img/jen_pip.PNG)

#### Pipeline >> Pipeline Script from SCM >> Git >> Enter Git URL >> Save 

![Pipeline >> Pipeline Script from SCM >> Git](https://raw.githubusercontent.com/gtesei/Patterns_for_Continuous_Integration_Docker_Jenkins/master/img/jen_pip2.PNG)

## Jenkinsfile

```groovy
pipeline {
  agent none
  stages {
    stage('install-build-test-coveralls') {
      agent {
        docker {
          image 'python:3.6.1'
          args '-u root:sudo'
        }
      }
      environment {
        COVERALLS_REPO_TOKEN = credentials('d79070bd-2e2b-4a91-ba27-e1ecd68897ad')
      }

      steps {
        /*** INSTALL ***/
        sh "python --version"
        sh "pip install --upgrade pip"
        sh "pip install -r requirements-dev.txt"
        sh "pip install pytest pytest-cov"
        sh "pip install coveralls"
        sh "pip install coverage"

        /*** BUILD ***/
        sh "pip install -e ."

        /*** TEST ***/
        sh "py.test --doctest-modules --cov"

        /*** COVERALLS ***/
         sh "echo $COVERALLS_REPO_TOKEN"
        /* sh "coverage run setup.py test" */
        sh "py.test --doctest-modules --cov"
        sh "coverage report -m"
        sh "COVERALLS_REPO_TOKEN=$COVERALLS_REPO_TOKEN_PSW coveralls"
      }

    }

    stage('docker') {
      agent any
      environment {
        IMAGE_NAME = 'gtesei/hello_docker_jenkins'
        DOCKER_REGISTRY = credentials('3897e886-55e9-491f-95f9-bf0280b72966')
      }
      steps {
        sh "docker pull $IMAGE_NAME || true"
        sh 'docker build --pull --cache-from "${IMAGE_NAME}:develop" --tag "$IMAGE_NAME" . || true'
        sh 'docker login -u $DOCKER_REGISTRY_USR -p $DOCKER_REGISTRY_PSW'
        sh 'docker tag $IMAGE_NAME "${IMAGE_NAME}:develop"'
        sh 'docker push "${IMAGE_NAME}:develop"'
      }
    }

  }
}

```

