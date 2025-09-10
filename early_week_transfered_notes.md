Week 1: CS5950 Docker Certificate Studying
Docker Mastery: with Kubernetes +Swarm from a Docker Captain

Section 1 - Quick Start
Software first produced and started gaining traction in 2013
Composed of three main components: Image, Registry, Container (Build, Ship, Run)
Any Cloud Native Computing Foundation (CNCF) software utilizes the Build, Ship, Run framework. This includes Helm, kubernetes, etc.
Build: A `DockerFile` is created to define the outline of an image to be built. Details of this structure will be defined later but essentially this file will define the layers of the image to be built which includes only the absolute needed dependencies to run. No OS, which means these can be run universally on any system. Use `Docker build [location_of_dockerfile]`
Registry: Is the Universal app distribution. Every major platform (github, docker registry, bitbucket, etc has their own versions of this. Docker images can be identified by a unique hash that ties in all of their specific layers ensuring that finding the same hash on two systems means they are the same base image. 
Container: As a baseline you need a docker engine downloaded onto your OS (can be any). Images are downloaded into your local docker registry. Running the container which is built of an image creates your software application. This application gets its own process list, IP address, file system, etc. You can run two containers of the same image, they will be completely unique and isolated from one another.

Registered account through Dockerhub: https://hub.docker.com/ 
Used Docker virtual sandbox to run through a demo: https://www.docker.com/play-with-docker/ 
The demo showed how to pull containers, build, run multiple instances. I assigned each container a public IP address from outside the container that was then funneled in.

Docker exists and is popular because it provides Isolation, Environments and Speed.
Systems and programs used to be run on shared servers which conflicting environments, dependencies, shared resources often made it difficult to manage increasingly complex systems. 
Docker originated from problems like “matrix from hell’. Containers reduced complexity drastically. The Open Container Initiative (OCI) was established soon after.
Docker and containers help develop faster, build faster, test faster, deploy faster.

Section 2 - Course Introduction
Topics that will be discussed: Docker install, container basics, image basics, docker networking, docker volumes, docker compose, orchestration, docker swarm, kubernetes, swarm vs k8’s.
Kubernetes gets a lot more attention but docker can be simpler to use and adjust in smaller-scale environments.

Cloned class repo, setup docker, preferences, different ways to run docker. 

Section 3 - The Best Way to Setup Docker for Your OS
There are three main ways to run docker, Docker desktop (locally), docker engine (servers) and Cloud Run (PaaS). Due to all products following OCI, most configuration and deployments should be the same.

I have downloaded docker desktop for local development and am running this with a wsl backend. I will be running all docker assignments from wsl2.

Section 4 - Creating and Using Containers
`docker version`, `docker info` for more information and `docker` to see all possible commands.
Use `docker container run` to start a container. The format of most commands will be `docker` <management command> <subcommand> <options>. However docker heavily supports backwards compatibility so you can multiple things that will result the same: `docker run` = `docker container run`

An Image is the application that we want to run.
A container is an instance of that image running as a process.
Multiple containers can be run off of the same image.
Images can be custom created our pulled from the docker registry: hub.docker.com
I pulled and ran a nginx server container and published a port opening on 80:80 allowing me to access it using localhost on my browser
`docker container run --publish 80:80 --detach nginx`. Detach is optional to put container running in the background. `docker container ls`, `docker container stop <container ID>`
You can use `docker container logs <container name> to see into detached terminals
`docker container top <container name>` lets you see processes that are running.
You can remove multiple containers at the same time: `docker container rm <container id>`

What happens when you run `docker container run`?
Looks for image in local image cache
Then looks for remote image repository (default is Docker hub)
Download latest version (you can define version)
Creates new container based on image and prepares to start
Gives container a virtual IP on a private network in docker engine
Opens up defined port on host and then forwards that traffic to same port on container
Starts container using the CMD in the image dockerfile

Containers are just processes that run on the local operating system, distinctly different from a VM. I ran through an exercise creating a mongo container, showing the operating system process list and how you can see containers on that list. In a virtual machine, the process are isolated from your machine so you can’t look directly that you are running mongo. Containers are essentially just restricted processes on your OS kernel.

Containers generally run off linux operating systems however microsoft made containers compatible in 2016. There is no sign that macOS will make container compatible with its kernel. Microsoft containers never caught on and are generally a pain to use with low support. Stick with Linux.













Week 1: CS5950 Docker Certificate Studying
Docker Mastery: with Kubernetes +Swarm from a Docker Captain
Section 4 - Creating and Using Containers
docs.docker.com contains lots of useful information. After poking around, I have noticed there is a lot of support with new AI models, being able to spin them up open source editions very easily as well as easy API integration for subscription based AI.
I ran multiple docker containers together on various ports and ran various environment commands to interact with the containers. Do specify forwarding of host machine port to container port you do `8080:8080` (host_port:container_port).
I completed a quiz on docker container commands.

List of commands to see what is happening in a docker container:
`docker container top` : process list in one container
`docker container inspect` : details of one container config
`docker container stats` : performance stats for all containers
The official MySQL image does not support certain commands like `ps` and `apt-get` so in the class I am going to use `mariadb` instead.
You can run or execute commands “interactively” in a container by using the `-it` tag.
Almost all containers have bash installed, so launching `bash` interactively from a container will open up its shell.
Many containers run on `Alpine Linux` as it is incredibly small (5mb) and is security focused. Alpine linux does not have `bash` but you can exec into it with `sh`.

Each container is connected to a private virtual network known as “bridge”
Each virtual network routes through NAT firewall on host IP
All containers on a virtual network can talk to each other without having to specify open ports.
Best practice is to create a new virtual network for each app.
Most networking by default works well but it is easy to swap out parts and customize.
You can switch containers to new virtual networks at anytime
You can attach containers to more than one virtual network (or none)
You can opt to not use virtual networks and just use host IP (kinda looses point of containerization)
You can use various docker network drivers to gain new abilities.
Special format option that would be good to know: `docker container inspect –format ‘{{ .NetworkSettings.IPAddress }}’ __containername__`
Conceptually walked through how packets travel through docker containers, virtual networks and host machines. Also how opening up ports and the host firewall interacts with these layers.








