---
layout: post
title:  "Getting Started with Docker : Introduction to Docker"
author: Nemi Chand
categories: [ Docker ]
tags: [docker]
image: assets/images/2016/05/intro-to-docker_title.png
description: "In this article I've covered an introduction of docker platform."
featured: false
hidden: false
#rating: 4.5
---


### Introduction 
Docker is a platform for developing, shipping and running applications using container Hypervisor-based virtualization technology.

Docker allows you to package an application with all of its dependencies into a standardized unit for software development.

Docker containers wrap up a piece of software in a complete file system that contains everything it needs to run: code, runtime, system tools, system libraries – anything you can install on a server. This guarantees that it will always run the same, regardless of the environment it is running in.

You can run a single service per container. Let's say one container for sql , one container for web application.


![Layered architecture]({{ site.baseurl }}/assets\images\2016\05\Intro-to-docker_what_is_layered_filesystems.png)
(Source : docker.com)

## Container VS Virtual machine

### Virtual Machine

Virtual machine includes application, binaries and guest operating system which consumes more space.
1. Virtual machine consume more resources, RAM and disk space.
    ![Virtual Machine]({{ site.baseurl }}/assets\images\2016\05\Intro-to-docker_vm.png)
(Source : docker.com)

2. The Container
    a. The container is a hypervisor based virtualization technology.

    b. It includes the binaries to run the application (all of its dependencies ) and they share the kernel with other containers.

    ![Container Image]({{ site.baseurl }}/assets\images\2016\05\Intro-to-docker_dockerImage.png)
(Source : docker.com)

#### Why is docker architecture popular 
Nowadays docker is getting popularity due to its architecture
There are numerous reasons for its popularity.

1. **Ease of use**: Docker is designed in such a way so that developers, architects and administration can take advantage of this. Docker allows you to package a application while developing that run same on production server.
2. **Speed**: Docker ship 7x more faster than other technology because it consume less resource & memory to run application
3. **Lightweight**: Containers running on a single physical machine all share the same operating system kernel so they start instantly and make more efficient use of RAM. Images are constructed from layered file systems so they can share common files, making disk usage and image downloads much more efficient .
4. **Docker Hub**: Docker hub is a public repository for docker images. We can say its app store of docker images. You can create your own private docker hub by creating private repository.
5. **Secure**: Containers isolate applications from each other and the underlying infrastructure while providing an added layer of protection for the application.
6. **Modularity and scalability**: Docker can easily break out your application and docker images . Let's say multiple web applicationsand database servers are running, you can easily scale up and down. Docker containers spin up and down in seconds making it easy to scale an application service at any time to satisfy peak customer demand, then just as easily spin down those containers to only use the resources you need when you need it.
7. **Open**: Docker containers are based on open standards that allow containers to run on every platform, cloud and machine.

#### The future of docker
The docker container is growing very fast.

Linux platform

![Linux Image]({{ site.baseurl }}/assets\images\2016\05\Intro-to-docker_linux-docker-image.png)

Windows platform

![Windows Image]({{ site.baseurl }}/assets\images\2016\05\Intro-to-docker_windows-docker-image.png)

#### Docker Terminology
Docker platform includes

1. **Docker engine**: Docker engine is the program that enables containers to be built, ship and run.
2. **Images** : An Image includes all the dependencies sach as configuration files, framework and deployment.it is immutable once it has been created. this is image drive from multiple base images that are layered stack on top of each other. 
3. **Containers**: Isolated application platform. Contains everything needed to run your application.
    a. Each container has its own.
    b. Root file system
    c. Processes
    d. Memory
    e. Devices
    f. Network ports
4. **Registry** : It is a service that provide access to repositories. The default registry for most public images is Docker Hub.
5. **Docker File** : It contains set of instruction to build and run the docker image.
6. **Build** : This is command or process to generate image from application source and it include assets like images and other files.
7. **Repositories** : A collection of related Docker images, labeled with a tag that indicates the image version. Some repos contain multiple variants of a specific image, such as an image containing SDKs (heavier), an image containing only runtimes (lighter), etc. Those variants can be marked with tags.
8. **Docker Hub** : A public registry to upload images and work with them. Docker Hub provides Docker image hosting, public or private registries, build triggers and web hooks.


#### Docker Orchestration tools
Three tools for orchestration distributed application with docker.
1. **Docker Machine**: Tool that proviosions docker hosts and install the docker engine on them.
2. **Docker Swarm**: Tool that cluster many engine and schedule containers.
3. **Docker Compose**: Tool to create and manage multi- container application.


#### Docker Images 
It is a read only template used to create containers. Built by you or other docker users. Stored in the docker hub or your local repository.
Docker images is a layered architecture. A docker image contains a base image and that image is referenced by other layers.

Docker images is manifest of software. It contain the binaries to run a application. Docker images are the basis of containers.

#### Summary

In this article I've covered an introduction of the docker platform. Build,ship and run.

#### References