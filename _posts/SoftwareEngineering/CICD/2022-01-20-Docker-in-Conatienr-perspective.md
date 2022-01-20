---
title : "What is Container? ,Docker in Container perspective"

excerpt: "Docker and Container"

categories:
  - CI/CD
tags:
  - [SoftwareEngineering , Docker,Container,CI/CD]
# classes : wide
toc: true
toc_sticky: true
---

Docker is used to build a development environment to unify the environment in production and test environments. And, most of the explanations say that unlike a VM, you can use it without running the OS. In fact, if you follow the Docker tutorial a few times, you can make a simple imitation. Many people know that it is used to unify the development environment, but not  internal principles of operation. If you are going to use only Docker, it is enough to read only the Docker tutorial, check the operation of each command in Docker, and remember the docker image that is used a lot. This is the same as studying only github if you are going to use github. If you do not focus on the operation of each tool, but disassemble and understand the basic concept and operation principle based on it, you can easily learn it using transfer learning when other platforms or other technologies that use these technologies come out. 

# Container

## What is Container

### Definition

What is a Container? To understand containers, you must first start with the linux machine.



![img](https://www.098.co.kr/wp-content/uploads/2020/09/container-what-is-container.png)

In simple terms, OS can be seen as a software package that helps us to abstract and use interfaces for various hardware after encapsulation without worrying about it. At this time, each process shares and uses resources such as memory. You may want to isolate these processes (as opposed to isolation in os). In the process namespace, specific processes, namespaces, and groups are combined together and isolated is called container in runtime. This is justice. Since processes are bound in namespace, only processes within the same namespace can be viewed. (It is convenient to recall Python's LEGB scope rule.) Each container can be viewed as a sandbox in Runtime.

### Container Life Cycle

A container is an isolated process. The process starts only when the container starts. Then, the Process performs a specific task and exits. And, the container is terminated because there is no reason to exist anymore. In other words, we can see that the lifecycle of the Container is completely consistent with the lifecycle of the Process.



## Image Tree

### pull

When we talk about Container, what we usually refer to as Container is Image. An Image is a Tree.

![image](https://user-images.githubusercontent.com/50165842/150354604-1ad8bb22-f4e5-4036-a373-8fa0abaf54f2.png)



A Docker image is a tree structure. Using the example above, we build busybox from scratch, then sshd , then ,app. When you want to run the app, pull the app branch and run it. In other words, you don't need to fetch the entire tree, you can fetch only the specific branch you want.

### share

![Docker Image Tree-Page-2 drawio](https://user-images.githubusercontent.com/50165842/150355019-82a937fd-64b5-4949-a027-1054c764ee87.png)



And, other nodes can share and use what I'm running. For example, another node could take a branch from the app tree to sshd and create a node called app2 . You can effectively manage your images with .Tree.

### Weak point analysis

Because it is a tree structure, if any node (ex.busybox, sshd,) has a problem, it is easy to know which node is affected by the vulnerability because it knows which node is a child node.







# Docker file, image, container

I knew how to isolate resources in the container definition, and how to effectively manage images through the tree structure. If so, through what process is the container created? To know this, you need to know the relationship between Docker File, Image, and Container.

## DockerFile

DockerFile is famous for Docker, so I took Dockerfile as an example. Docker File means any txt file.

```dockerfile
FROM python:3.8.7-slim-buster

COPY. /app
WORKDIR /app
ENV PYTHONPATH=/app
ENV PYTHONUNBUFFERED=1

RUN pip install pip==21.2.4 && \
    pip install -r requirements.txt

CMD["python", "main.py"]
```

In DockerFile we can RUN anything we want. An image is created with the command docker build from Docker File.

## Image

![Docker Image Tree-Page-2 drawio](https://user-images.githubusercontent.com/50165842/150355019-82a937fd-64b5-4949-a027-1054c764ee87.png)

Image is a template. Template instances can be created countless times. The template instance is called container runtime.

## Container

![img](https://www.098.co.kr/wp-content/uploads/2020/09/container-what-is-container.png)

An instance created from an Image. When the container starts, the process starts, and when the process ends, the container also ends.



## Relationships

The relationship between these tubes is as follows.

- Docker File -> Image -> Container
- Container -> Image

An image is created from a Docker file, and a container is created from the image. However, you don't necessarily have to start from a Docker file. After performing a task with a container, you can use commit to make it an image.

## Not install dependency

Container, DockerFile, and Image have the same relationship as above, and we learned that the starting point does not have to be a Dockerfile when creating a container. Usually, different versions of libraries are required to run a process. We install it using apt-get. However, the container includes everything without having to install it. In other words, an Image is a package of software, and a process is created from this pacakage. Apps that require different library versions can run on a single OS. Create a Container Instance from Image and the Container disappears when the task is finished. If you delete this image, it will all be gone. This means you don't need to install any dependencies.



# Docker

Since the image is a tree structure, it can be managed efficiently, and it has been confirmed that the container is isolated from each other. And, we looked at the relationship between Dockerfile , Image and Contianer. If so, let's connect all of these.

## Docker Host

![img](https://data-flair.training/blogs/wp-content/uploads/sites/2/2018/10/Docker-Architecture-vol.1-01.jpg)

There is such a thing as Docker Host. There is an image cache in it, and there is a thing called Registry. Docker Host either receives or pushes only the necessary bits from the registry through docker pull.

### Docker Push & Pull

Docker pull checks how many parent nodes are in the image cache. Then, it decides which derivatives should be brought to form the child nodes. Since you already have a paraent node in the image cache, you only need to bring the node you need to build the app.

docker push only pushes differences from the tree in the image cache to the registry. In other words, it records a snapshot. (Similar to git.) Since it is already in the registry except for differences, only the differences are pushed.

Like git, it has the advantage of being faster to create another image from one image to record a snapshot.



## Docker Client

There is something called Docker Client. In simple terms, Docker Client is the entity that performs Docker pull , Docker create , Docker run , and Docker commit.

There is a thing called deamon in Docker Host, Docker Client will talk to deamon. Then, the deamon exposes the API to the Client.

Docker Client has two roles.

1. Manage the container lifecycle of Docker Host.
2. Configure Docker Host's Infrastructure.

Here, we should pay attention to this number two.

This is equivalent to saying that Docker manages the network and storage. Here, we will omit the explanation of Network as we have not yet learned it.

### Storage

Docker's storage is preemtible by default. When the container is terminated, the work inside it will also disappear. If so, can't it be made into a persistent disk? A persistent disk in Docker is called a Volume. If a container wants to keep (store) data (or state) even after the lifetime of the container is over, use a Volume.
