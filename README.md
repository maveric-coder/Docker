# Docker

## Index

* [Introduction](#hello-containers)
* [Images](#images)
* [Container](#container)
  * [Containerize a single-container app](#containerize-a-single-container-app)
  * [Deploying Apps with Docker Compose](#deploying-apps-with-docker-compose)
  * [Docker Swarm](#docker-swarm)
* [Docker Networking](#docker-networking)
* [Docker Volume](#docker-volumes)
  * [Spring boot mongo app](#spring-boot-mongo-app)
* [Docker installation guide](https://docs.docker.com/engine/install/ubuntu/)

<h2 align="left">Hello Containers!</h2>
Docker is a software platform that allows you to build, test, and deploy applications quickly. Docker packages software into standardized units called containers that have everything the software needs to run including libraries, system tools, code, and runtime. Using Docker, you can quickly deploy and scale applications into any environment and know your code will run.<br>

* Enables consistent environment
* Easy to use and maintain
* Efficient use of the system resources
* Increase in the rate of software delivery
* Increases operational efficiency
* Increases developer productivity

### The best place to play is [**Docker Playground**](https://labs.play-with-docker.com/)<br><br>
The ***Docker Engine*** is the infrastructure plumbing software that runs and orchestrates containers.
<br>
<br>
When you install Docker, you get two major components:<br>
• the Docker Client<br>
• the Docker Daemon (sometimes called “server” or “engine”)<br><br>

You can use the ``` docker version ```command to test that the client and daemon (server) are running and talking to each other.
```bash
 docker version ## to check docker version
```
## Images
Docker Images are made up of multiple layers of read-only filesystems, these filesystems are called a Docker file, they are just text file with a set of pre-written commands.
For every text written or instructions given in docker file a layer is created and is placed on top of another layer forming a docker image, which is future used to create docker container.<br>
Run the ```docker image ls ``` command on your Docker host.
```bash
 docker image ls or docker images
 docker search python:3.7
 docker search registry
```
To filter out and see only few columns
```bash
 docker search --filter "is-official=true" registry
 docker search alpine --filter "is-automated=true"
 docker search --format "{{.Name}}\t{{.Description}}\t{{.IsOfficial}}" registry
```
To list out all the present images in the node:
```bash
 docker images  
 docker images ls
 docker images nginx
```
***To pull an Image***
```bash
 docker image pull nginx:latest
 docker image pull nginx:alpine
 docker image pull --all-tags nginx
```
***Clean up***
```bash
 docker images
 docker image rm nginx:l-alpine-perl
 docker rmi 38049a7d921n293423
 docker rmi 3849a7sdf9sdf923f9 --force

```
## Container
A container is an isolated application, it is built from one or more images, and acts as an entire package system which includes all the libraries and dependencies required for an application to run. Docker containers come without OS, they use the Host OS for functionality, hence it is a more portable, efficient and lightweight system that comes with a guarantee that the software will run in any environment.

### Starting a new container
<br>The most common way of starting containers is using the Docker CLI. The following
docker container run command will start a simple new container.
```bash
 docker container create -it --name cc_busybox_A busybox:latest  
 docker container run -itd --rm --name cc_busybox_B busybox:latest
 docker ps -a
 docker container start cc_busybox_A
 docker container stop cc_busybox_B
 docker container restart --time 5 cc_busybox_A
 docker container rename cc_busybox_A my_busybox
 docker container run -d --name webserver -p 80:80 nginx

```
To execute any command
```bash
 docker exec -it my_busybox pwd 
 docker exec -it ubuntu1 bash
 docker attach ubuntu1
 docker container run --name ubuntu -it ubuntu:latest /bin/bash
 docker container run --name neversaydie -it --restart always alpine sh
 docker container run -d --name always \
  --restart always \
  alpine sleep 1d
 docker container run -d --name unless-stopped \
  --restart unless-stopped \
  alpine sleep 1d
```
***Port Mapping***
```bash
 docker container run -itd --name nginx -p 8080:80 /tcp nginx:latest
 docker container run -itd --name nginx_A -p nginx:latest
```
***Remove Containers***
```bash
 docker ps-a   ## list all the contianers
 docker container rm 672fc9dasd83h3j393
 docker container rm my_busybox --force
 docker container prune
 docker rm $( docker ps -aq)
```

## Containerizing an app
Containers are all about apps! In particular, they’re about making apps simple to
build, ship, and run.<br>
The process of containerizing an app looks like this:<br>
1. Start with your application code.<br>
2. Create a Dockerfile that describes your app, its dependencies, and how to run
it.<br>
3. Feed this Dockerfile into the docker image build command.<br>


### Containerize a single-container app
 The process of containerizing a simple single-container Node.js web app <br>
 
 ***Getting the application code***
 ```bash
$ git clone https://github.com/nigelpoulton/psweb.git
 ```
***Inspecting the Dockerfile***
```bash
$ cat Dockerfile
```

The Dockerfile has two main purposes:<br>
1. To describe the application<br>
2. To tell Docker how to containerize the application (create an image with the app inside)

***Containerize the app/build the image***
```bash
 docker image build -t web:latest .
```

***Run the app***
```bash
 docker container run -d --name c1 \
  -p 80:8080 \
  web:latest
```
#### Contenerising Java application
Create Dockerfile

```bash
$ vi Dockerfile

FROM tomcat:8.0-alpine
LABEL maintainer="anand.kumar1@hotmail.com"

ADD sample.war /usr/local/tomcat/webapps/

EXPOSE 8080
CMD ["catalina.sh", "run"]
```
Paste the above mentioned code in Dockerfile

And build the image using command
` docker build -t mywebapp .`

Run a container using the image
`$docker run -p 8080:8080 mywebapp`

###  Deploying Apps with Docker Compose
Instead of gluing everything together with scripts and long docker commands, Docker Compose lets you describe an entire app in a single declarative configuration file. You then deploy it with a single command.<br>
Once the app is deployed, you can manage its entire lifecycle with a simple set of commands. You can even store and manage the configuration file in a version control system!

```bash
 docker-compose --version
```

***Deploying an app with Compose***
 We'll use the following 4 files from https://github.com/maveric-coder/dockerCompose-Counter-App.git:<br>
• Dockerfile<br>
• app.py<br>
• requirements.txt<br>
• docker-compose.yml<br>
Clone the Git repo locally.<br>
```bash
$ git clone https://github.com/maveric-coder/dockerCompose-Counter-App.git
$ cd dockerCompose-Counter-App
$ ls

```
Let’s quickly describe each file:<br>
• app.py is the application code (a Python Flask app)<br>
• docker-compose.yml is the Docker Compose file that describes how Docker should deploy the app<br>
• Dockerfile describes how to build the image for the web-fe service<br>
• requirements.txt lists the Python packages required for the app<br>

 Compose to bring the app up.<br>
 
 ```bash
  docker-compose up &
  docker-compose -f prod-equus-bass.yml up
 ```
 
 
 
 ## Docker Swarm
 At a high level Swarm has two major components:<br>
• A secure cluster<br>
• An orchestration engine<br>

Each of the nodes needs Docker installed and needs to be able to communicate with the rest of the swarm. It’ also beneficial if name resolution is configured — it makes it easier to identify nodes in command outputs and helps when troubleshooting.

***Initializing a brand new swarm***
Docker nodes that are not part of a swarm are said to be in single-engine mode. Once they’re added to a swarm they’re switched into swarm mode.<br>
The following steps will put mgr1 into swarm mode and initialize a new swarm. It will then join wrk1, wrk2, and wrk3 as worker nodes — automatically putting them into swarm mode. Finally, it will add mgr2 and mgr3 as additional managers and switch them into swarm mode.<br>

1. Log on to mgr1 and initialize a new swarm
```bash
 docker swarm init \
  --advertise-addr 10.0.0.1:2377 \
  --listen-addr 10.0.0.1:2377
  
 docker swarm join-token worker
 docker swarm join-token manager
  
 ``` 
 
 To join:
 ```bash
 docker swarm join \
  --token SWMTKN-1-0uahebax...ue4hv6ps3p \  
  10.0.0.1:2377 \
  --advertise-addr 10.0.0.2:2377 \
  --listen-addr 10.0.0.1:2377
 ```
 
List the nodes in the swarm by running docker node ls from any of the manager nodes in the swarm.
```bash
 docker node ls
```

***Swarm services***
`docker service create` to tell Docker we are declaring a new service, and we used the `--name` flag to name it `web-fe`. We told Docker to map `port 8080 `on every node in the swarm to 8080 inside of each service replica. Next, we used the `-- replicas` flag to tell Docker that there should always be 5 replicas of this service.
```bash
 docker service create --name web-fe \
  -p 8080:8080 \
  --replicas 5 \
  nigelpoulton/pluralsight-docker-ci
```  

*Viewing and inspecting services*
```bash
 docker service ls
 docker service ps web-fe
 docker service inspect --pretty web-fe
```

***Scaling a service***
```bash
 docker service scale web-fe=10
 docker service scale web-fe=5
```

***Removing a service***
```bash
 docker service rm web-fe
```

Be careful using the docker service rm command, as it deletes all service replicas without asking for confirmation.


***Rolling updates***

This creates a new overlay network called “uber-net” that we’ll be able to leverage with the service we’re about to create. An overlay network creates a new layer 2
network that we can place containers on, and all containers on it will be able to communicate. This works even if the Docker hosts the containers are running on are
on different underlying networks. Basically, the overlay network creates a new layer 2 container network on top of potentially multiple different underlying networks.
```bash
 docker network create -d overlay uber-net
 docker network ls

 docker service create --name uber-svc \
  --network uber-net \
  -p 80:80 --replicas 12 \
  nigelpoulton/tu-demo:v1
  
 docker service ls
 docker service ps uber-svc

 docker service update \
  --image nigelpoulton/tu-demo:v2 \
  --update-parallelism 2 \
  --update-delay 20s uber-svc
  
 docker service ps uber-svc
  
```


## Docker Networking
Docker runs applications inside of containers, and these need to communicate over lots of different networks. This means Docker needs strong networking capabilities.
Docker networking is based on an open-source pluggable architecture called the Container Network Model (CNM).
Different types of Networks are:
 * bridge
 * host
 * overlay
 * IPvLAN
 * macvlan
<br>
Docker ships with several built-in drivers, known as native drivers or local drivers. On Linux they include; bridge, overlay, and macvlan. On Windows they include; nat, overlay, transparent, and l2bridge.

```bash
 docker network ls
 docker network inspect bridge

$ ip link show docker0
```
The default “bridge” network, on all Linux-based Docker hosts, maps to an underly- ing Linux bridge in the kernel called “docker0”. 
```bash
 docker network inspect bridge | grep bridge.name

 docker network create -d bridge localnet
 docker container run -d --name c1 \
  --network localnet \
  alpine sleep 1d
  
 docker network inspect localnet --format '{{json .Containers}}'

 docker container run -it --name c2 \
  --network localnet \
  alpine sh
```

From within the “c2” container, ping the “c1” container by name.
```bash
$ ping c1
```

Port mappings let you map a container port to a port on the Docker host. Any traffic hitting the Docker host on the configured port will be directed to the container. This is mapped to port 5000 on the host’s 10.0.0.15 interface. The end result is all traffic hitting the host on 10.0.0.15:5000 being redirected to the container on port 80.
An example of mapping port 80 on a container running a web server, to port 5000 on the Docker host.
```bash
 docker container run -d --name web \
  --network localnet \
  --publish 5000:80 \
  
```
`localhost or 127.0.0.1.`
  nginx
  
To attach a running container to a network
```bash
 docker network connect <network_name> <container_ID>
 docker network disconnect <network_name> <container_ID>
```
## Docker Volumes

The recommended way to persist data in containers is with volumes.
At a high-level, you create a volume, then you create a container, and you mount the volume into it. The volume gets mounted to a directory in the container’s filesystem, and anything written to that directory is written to the volume. If you then delete the container, the volume and its data will still exist.

Use the following command to create a new volume called myvol.
```bash 
 docker volume create myvol
 docker volume inspect myvol
```
By default, Docker creates new volumes with the built-in local driver. As the name suggests, local volumes are only available to containers on the node they’re created on. Use the -d flag to specify a different driver.

There are two ways to detele docker volumes i.e. `docker volume prune` and `docker volume rm`
To attach the created *Volume* to a container, we willl execute below commands.
```bash
 docker container run -dit --name voltainer \ --mount source=myvol,target=/vol \ alpine
 docker run --name MyJenkins1 -v myvol1:/var/jenkins_home -p 8080:8080 -p 50000:50000 jenkins
```
#### Spring boot mongo app

To understand better how making the data persistent actually works. Let's see through a live application deployment.
Clone the repository to the server with docker, the same server should have maven installed as well (to build the application)
```bash
$ git clone https://github.com/maveric-coder/spring-boot-mongo-docker.git
$ cd spring-boot-mongo-docker
$ mvn clean package
```
By executing the above commands, in the target folder `.jar` file will be available as the artifact ready to be deployed.
The `Dockerfile` present in the folder contains info and commands to build the image to run the created artifact.Below mentioned command will build an image.
```bash
 docker build -t anand2909/spring-boot-mongo .
```
To check the build image run ` docker images` and inspect the image by running `$docker image inspect anand2909/spring-boot-mongo`.
Create an App container by
```bash
 docker run -d --name springmongoapp -p 8080:8080 anand2909/spring-boot-mongo
```
Let's create a Mongo container to enter and process the entered data.
```bash
 docker run -d --name mongo mongo
```
Now if we will open the webpage for the application we will still not be able to connect as we did not do the needed configuration.
Delete the existing app container and recreate by mentioned commands
```bash
 docker rm -f <container_id>
 docker run -d --name springmongoapp -p 8080:8080 anand2909/spring-boot-mongo
java -Dspring.data.mongodb.uri=mongodb://<Mongo_container_IP>:27017/spring-mongo -Djava.security.egd=file:/dev/./urandom -jar ./spring-boot-mongo.jar
```
The date being entered will be stored now but it will be only till the Mongo DB is present in the server. Once the Mongo container is deleted the saved data will be flushed out. Docker volume will make the date persistent and can be attached to other containers.
Delete the existing mongo container and create a new Mongo container with a new volume.
```bash
 docker volume create mongobckp
 docker run -d --name mongo -v mongobckp:/data/db mongo
```

There are two types of  volumes:
* Local volume
* Network volume

To use the network volume, [REX-RAY](https://rexray.readthedocs.io/en/v0.9.0/user-guide/docker-plugins/) can be used
```bash
 docker plugin install rexray/ebs   EBS_ACCESSKEY=AKIA2NNCZ7U3DFCHL7N4   EBS_SECRETKEY=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
 docker volume create --driver rexray/ebs --name ebsvol
 docker run -d --name mongo -v rexray/ebs:/data/db mongo
```
In earlier step, private IP address was entered to connect with Mongo DB container. But if both the containers will be in same network then they can communicate by container names.
Steps to create and configure containers in the same network are below:
```bash
 docker network create -d bridge appnet
 docker network connect appnet mongo
 docker network connect appnet springmongoapp
```

## ENV vs ARG

**ARG (build time):**

Variables defined through ARG are also known as build-time variables. They are only available from the moment they are ‘announced’ in the Dockerfile with an ARG instruction in the Dockerfile.

Running containers can’t access the values of ARG variables. So anything you run via CMD and ENTRYPOINT instructions won’t see those values by default.

The benefit of ARG is, that Docker will expect to get values for those variables. At least, if you don’t specify a default value. If those values are not provided when running the build command, there will be an error message. Here is an example where Docker fill complain during build:
```bash
# no default value is specified!
ARG some_value
```
Even though ARG values are not available to the container, they can easily be inspected through the Docker CLI after an image is built. For example by running docker history on an image. ARG and ENV are a poor choice for sensitive data if untrusted users have access to your images.

**ENV (build time and run time):**

ENV variables are available both during the build and to the future running container. In the Dockerfile, they are usable as soon as you introduce them with an ENV instruction.

Unlike ARG, ENV values are accessible by containers started from the final image. ENV values can be overridden when starting a container, more on that below.

<img src ="https://github.com/maveric-coder/Kubernetes/blob/main/files/img/docker_environment_build_args_overview.png" height="300" width="700"/>

