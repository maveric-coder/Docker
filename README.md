# Docker

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
> docker version
```
### Images
It’s useful to think of a Docker image as an object that contains an OS filesystemand an application. If you work in operations, it’s like a virtual machine template.
<br>Run the ```docker image ls ``` command on your Docker host.
```bash
> docker image ls
> docker search python:3.7
> docker search registry
```
To filter out and see only few columns
```bash
> docker search --filter "is-official=true" registry
> docker search alpine --filter "is-automated=true"
> docker search --format "{{.Name}}\t{{.Description}}\t{{.IsOfficial}}" registry
```
To list out all the present images in the node:
```bash
> docker images
> docker images ls
> docker images nginx
```
***To pull an Image***
```bash
> docker image pull nginx:latest
> docker image pull nginx:alpine
> docker image pull --all-tags nginx
```
***Clean up***
```bash
> docker images
> docker image rm nginx:l-alpine-perl
> docker rmi 38049a7d921n293423
> docker rmi 3849a7sdf9sdf923f9 --force
```
## Starting a new container
<br>The most common way of starting containers is using the Docker CLI. The following
docker container run command will start a simple new container.
```bash
> docker container create -it --name cc_busybox_A busybox:latest
> docker container run -itd --rm --name cc_busybox_B busybox:latest
> docker ps -a
> docker container start cc_busybox_A
> docker container stop cc_busybox_B
> docker container restart --time 5 cc_busybox_A
> docker container rename cc_busybox_A my_busybox
> docker container run -d --name webserver -p 80:8080 \
  nigelpoulton/pluralsight-docker-ci

```
To exec any command
```bash
> docker exec -it my_busybox pwd
>  docker container run --name percy -it ubuntu:latest /bin/bash
> docker container run --name neversaydie -it --restart always alpine sh
> docker container run -d --name always \
  --restart always \
  alpine sleep 1d
> docker container run -d --name unless-stopped \
  --restart unless-stopped \
  alpine sleep 1d
```
***Port Mapping***
```bash
> docker container run -itd --name cont_nginx -p 8080:80 /tcp nginx:latest
> docker container run -itd --name cont_nginx_A -p nginx:latest
```
***Remove Containers***
```bash
> docker ps-a
> docker container rm 672fc9dasd83h3j393
> docker container rm my_busybox --force
> docker container prune
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
 > git clone https://github.com/nigelpoulton/psweb.git
 ```
***Inspecting the Dockerfile***
```bash
> cat Dockerfile
```

The Dockerfile has two main purposes:<br>
1. To describe the application<br>
2. To tell Docker how to containerize the application (create an image with the app inside)

***Containerize the app/build the image***
```bash
> docker image build -t web:latest .
```

***Run the app***
```bash
> docker container run -d --name c1 \
  -p 80:8080 \
  web:latest
```
###  Deploying Apps with Docker Compose
Instead of gluing everything together with scripts and long docker commands, Docker Compose lets you describe an entire app in a single declarative configuration file. You then deploy it with a single command.<br>
Once the app is deployed, you can manage its entire lifecycle with a simple set of commands. You can even store and manage the configuration file in a version control system!

```bash
> docker-compose --version
```

***Deploying an app with Compose***
 you’ll need the following 4 files from https://github.com/nigelpoulton/counter-app:<br>
• Dockerfile<br>
• app.py<br>
• requirements.txt<br>
• docker-compose.yml<br>
Clone the Git repo locally.<br>
```bash
> git clone https://github.com/nigelpoulton/counter-app.git
> cd counter-app
> ls

```
Let’s quickly describe each file:<br>
• app.py is the application code (a Python Flask app)<br>
• docker-compose.yml is the Docker Compose file that describes how Docker should deploy the app<br>
• Dockerfile describes how to build the image for the web-fe service<br>
• requirements.txt lists the Python packages required for the app<br>

 Compose to bring the app up.<br>
 
 ```bash
 > docker-compose up &
 > docker-compose -f prod-equus-bass.yml up
 ```
