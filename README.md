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
```
To exec any command
```bash
> docker exec -it my_busybox pwd
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



