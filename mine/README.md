# udemy-jenkins-pipelines-viaene

Asumptions:
You have vagrant installed
You have virtualbox or hyperv installed

For virtualbox:
cd vagrant-docker-jenkins
vagrant up

For hyperv (not as easy as virtualbox)
In a powershell window with administrator privelages (run "powershell as administrator")
cd vagrant-docker-jenkins
vagrant up --provider=hyperv




######
Now build new container jenkins-docker which is the original container but with the docker binaries so that we can create and manage containers from the jenkins instance. Must also add new volume mapping to be able to interact with the VM docker instance (docker within docker must leverage external linux kernel)

cd ~
mkdir jenkins-docker
# Create docker container using docker file below

JENKINS-DOCKER (build docker container)
#### Find latest static binaries
https://download.docker.com/linux/static/stable/x86_64/

####################
### create Dockerfile with the follow
####################

FROM jenkins
USER root

RUN mkdir -p /tmp/download && \
 curl -L https://download.docker.com/linux/static/stable/x86_64/docker-18.03.0-ce.tgz| tar -xz -C /tmp/download && \
 rm -rf /tmp/download/docker/dockerd && \
 mv /tmp/download/docker/docker* /usr/local/bin/ && \
 rm -rf /tmp/download && \
 groupadd -g 993 docker && \
 usermod -aG staff,docker jenkins

user jenkins

#############################

docker build -t jenkins-docker .

###################

docker stop jenkins
#docker rm jenkins
docker run -p 8080:8080 -p 50000:50000 -v /var/jenkins_home:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock --name jenkins -d jenkins-docker

### Test that your container within container can see the parents containers and docker.sock
docker exec -it jenkins bash
docker ps -a

###
Test newly built container on parent jenkins container
docker pull purplefeetguy/docker-nodejs-demo
docker run -p 3000:3000 -d --name my-nodejs-app purplefeetguy/docker-nodejs-demo


######################
### Jenkins Job DSL
######################
Groovy based - scripting for Java


######################
### Jenkins Pipelines DSL
######################
