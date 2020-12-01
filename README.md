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
```
