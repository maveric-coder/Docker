# Prep

1. What is a docker container?
* In simplest terms, docker containers consist of applications and all their dependencies.
* They share the kernel and system resources with other containers and run as isolated systems in the host operating system.
* The main aim of docker containers is to get rid of the infrastructure dependency while deploying and running applications. This means that any containerized application can run on any platform irrespective of the infrastructure being used beneath.
* Technically, they are just the runtime instances of docker images.
  
2. What are docker images?
<br>They are executable packages(bundled with application code & dependencies, software packages, etc.) for the purpose of creating containers. Docker images can be deployed to any docker environment and the containers can be spun up there to run the application.

3. What is a DockerFile?
<br>It is a text file that has all commands which need to be run for building a given image.

4. Can you tell what is the functionality of a hypervisor?
<br>A hypervisor is a software that makes virtualization happen because of which is sometimes referred to as the Virtual Machine Monitor. This divides the resources of the host system and allocates them to each guest environment installed.

<br>This means that multiple OS can be installed on a single host system. Hypervisors are of 2 types:

1. Native Hypervisor: This type is also called a Bare-metal Hypervisor and runs directly on the underlying host system which also ensures direct access to the host hardware which is why it does not require base OS.
2. Hosted Hypervisor: This type makes use of the underlying host operating system which has the existing OS installed.
