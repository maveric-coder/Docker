# Docker

## Index

[Containerizing an app](https://github.com/maveric-coder/Docker-Kubernetes#containerizing-an-app)

<h2 align="left">Hello Containers!</h2>
For a long time, the big web-scale players, like Google, have been using container technologies to address the shortcomings of the VM model. In the container model, the container is roughly analogous to the VM. The major difference is that every container does not require its own full-blown OS. In fact, all containers on a single host share a single OS. This frees up huge amounts of system resources such as CPU, RAM, and storage. It also reduces potential licensing costs and reduces the overhead of OS patching and other maintenance. Net result: savings on the cap-ex and op-ex fronts. Containers are also fast to start and ultra-portable. Moving container workloads from your laptop, to the cloud, and then to VMs or bare metal in your data center, is a breeze.<br>

### The best place to play is [**Docker Playground**](https://labs.play-with-docker.com/)<br><br>
The ***Docker Engine*** is the infrastructure plumbing software that runs and orchestrates containers.
<br>
<br>
When you install Docker, you get two major components:<br>
• the Docker client<br>
• the Docker daemon (sometimes called “server” or “engine”)<br><br>

You can use the ``` docker version ```command to test that the client and daemon (server) are running and talking to each other.
```bash
$ docker version ## to check docker version
```
### Images
It’s useful to think of a Docker image as an object that contains an OS filesystemand an application. If you work in operations, it’s like a virtual machine template.
<br>Run the ```docker image ls ``` command on your Docker host.
```bash
$ docker image ls or docker images ## to check the list of images
$ docker search python:3.7  ## to serach the particularly image
$ docker search registry
```
To filter out and see only few columns
```bash
$ docker search --filter "is-official=true" registry
$ docker search alpine --filter "is-automated=true"
$ docker search --format "{{.Name}}\t{{.Description}}\t{{.IsOfficial}}" registry
```
To list out all the present images in the node:
```bash
$ docker images  
$ docker images ls
$ docker images nginx
```
***To pull an Image***
```bash
$ docker image pull nginx:latest
$ docker image pull nginx:alpine
$ docker image pull --all-tags nginx ## it will pull all nginx images
```
***Clean up***
```bash
$ docker images
$ docker image rm nginx:l-alpine-perl
$ docker rmi 38049a7d921n293423  ## to delete image
$ docker rmi 3849a7sdf9sdf923f9 --force

```
## Starting a new container
<br>The most common way of starting containers is using the Docker CLI. The following
docker container run command will start a simple new container.
```bash
$ docker container create -it --name cc_busybox_A busybox:latest  
$ docker container run -itd --rm --name cc_busybox_B busybox:latest
$ docker ps -a
$ docker container start cc_busybox_A
$ docker container stop cc_busybox_B
$ docker container restart --time 5 cc_busybox_A
$ docker container rename cc_busybox_A my_busybox
$ docker container run -d --name webserver -p 80:8080 \
  nigelpoulton/pluralsight-docker-ci

```
To execute any command
```bash
$ docker exec -it my_busybox pwd 
$ docker exec -it ubuntu1 bash
$ docker attach ubuntu1   ## to attach the shell of the contianer
$ docker container run --name percy -it ubuntu:latest /bin/bash
$ docker container run --name neversaydie -it --restart always alpine sh
$ docker container run -d --name always \
  --restart always \
  alpine sleep 1d
$ docker container run -d --name unless-stopped \
  --restart unless-stopped \
  alpine sleep 1d
```
***Port Mapping***
```bash
$ docker container run -itd --name cont_nginx -p 8080:80 /tcp nginx:latest
$ docker container run -itd --name cont_nginx_A -p nginx:latest
```
***Remove Containers***
```bash
$ docker ps-a   ## list all the contianers
$ docker container rm 672fc9dasd83h3j393    ## to delete container by using container id
$ docker container rm my_busybox --force    ## to delete container by using container name
$ docker container prune     ## this cmd will delete all containers which are created ( don't use this cmd)
$ docker rm $( docker ps -aq)  ##This will return container ID while removing them
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
$ docker image build -t web:latest .
```

***Run the app***
```bash
$ docker container run -d --name c1 \
  -p 80:8080 \
  web:latest
```
###  Deploying Apps with Docker Compose
Instead of gluing everything together with scripts and long docker commands, Docker Compose lets you describe an entire app in a single declarative configuration file. You then deploy it with a single command.<br>
Once the app is deployed, you can manage its entire lifecycle with a simple set of commands. You can even store and manage the configuration file in a version control system!

```bash
$ docker-compose --version
```

***Deploying an app with Compose***
 you’ll need the following 4 files from https://github.com/nigelpoulton/counter-app:<br>
• Dockerfile<br>
• app.py<br>
• requirements.txt<br>
• docker-compose.yml<br>
Clone the Git repo locally.<br>
```bash
$ git clone https://github.com/nigelpoulton/counter-app.git
$ cd counter-app
$ ls

```
Let’s quickly describe each file:<br>
• app.py is the application code (a Python Flask app)<br>
• docker-compose.yml is the Docker Compose file that describes how Docker should deploy the app<br>
• Dockerfile describes how to build the image for the web-fe service<br>
• requirements.txt lists the Python packages required for the app<br>

 Compose to bring the app up.<br>
 
 ```bash
 $ docker-compose up &
 $ docker-compose -f prod-equus-bass.yml up
 ```
 
 
 
 ###  Docker Swarm
 At a high level Swarm has two major components:<br>
• A secure cluster<br>
• An orchestration engine<br>

Each of the nodes needs Docker installed and needs to be able to communicate with the rest of the swarm. It’ also beneficial if name resolution is configured — it makes it easier to identify nodes in command outputs and helps when troubleshooting.

***Initializing a brand new swarm***
Docker nodes that are not part of a swarm are said to be in single-engine mode. Once they’re added to a swarm they’re switched into swarm mode.<br>
The following steps will put mgr1 into swarm mode and initialize a new swarm. It will then join wrk1, wrk2, and wrk3 as worker nodes — automatically putting them into swarm mode. Finally, it will add mgr2 and mgr3 as additional managers and switch them into swarm mode.<br>

1. Log on to mgr1 and initialize a new swarm
```bash
$ docker swarm init \
  --advertise-addr 10.0.0.1:2377 \
  --listen-addr 10.0.0.1:2377
  
$ docker swarm join-token worker
$ docker swarm join-token manager
  
 ``` 
 
 To join:
 ```bash
$ docker swarm join \
  --token SWMTKN-1-0uahebax...ue4hv6ps3p \  
  10.0.0.1:2377 \
  --advertise-addr 10.0.0.2:2377 \
  --listen-addr 10.0.0.1:2377
 ```
 
List the nodes in the swarm by running docker node ls from any of the manager nodes in the swarm.
```bash
$ docker node ls
```

***Swarm services***
`docker service create` to tell Docker we are declaring a new service, and we used the `--name` flag to name it `web-fe`. We told Docker to map `port 8080 `on every node in the swarm to 8080 inside of each service replica. Next, we used the `-- replicas` flag to tell Docker that there should always be 5 replicas of this service.
```bash
$ docker service create --name web-fe \
  -p 8080:8080 \
  --replicas 5 \
  nigelpoulton/pluralsight-docker-ci
```  

*Viewing and inspecting services*
```bash
$ docker service ls
$ docker service ps web-fe
$ docker service inspect --pretty web-fe
```

***Scaling a service***
```bash
$ docker service scale web-fe=10
$ docker service scale web-fe=5
```

***Removing a service***
```bash
$ docker service rm web-fe
```

Be careful using the docker service rm command, as it deletes all service replicas without asking for confirmation.


***Rolling updates***

This creates a new overlay network called “uber-net” that we’ll be able to leverage with the service we’re about to create. An overlay network creates a new layer 2
network that we can place containers on, and all containers on it will be able to communicate. This works even if the Docker hosts the containers are running on are
on different underlying networks. Basically, the overlay network creates a new layer 2 container network on top of potentially multiple different underlying networks.
```bash
$ docker network create -d overlay uber-net
$ docker network ls

$ docker service create --name uber-svc \
  --network uber-net \
  -p 80:80 --replicas 12 \
  nigelpoulton/tu-demo:v1
  
$ docker service ls
$ docker service ps uber-svc

$ docker service update \
  --image nigelpoulton/tu-demo:v2 \
  --update-parallelism 2 \
  --update-delay 20s uber-svc
  
$ docker service ps uber-svc
  
```


### Docker Networking
Docker runs applications inside of containers, and these need to communicate over lots of different networks. This means Docker needs strong networking capabilities.
Docker networking is based on an open-source pluggable architecture called the Container Network Model (CNM).


```bash
$ docker network ls

$ docker network create -d bridge localnet
$ docker container run -d --name c1 \
  --network localnet \
  alpine sleep 1d
  
$ docker network inspect localnet --format '{{json .Containers}}'

$ docker container run -it --name c2 \
  --network localnet \
  alpine sh

```
From within the “c2” container, ping the “c1” container by name.
```bash
$ ping c1
```

An example of mapping port 80 on a container running a web server, to port 5000 on the Docker host.
```bash
$ docker container run -d --name web \
  --network localnet \
  --publish 5000:80 \
  
```
`localhost or 127.0.0.1.`
  nginx
