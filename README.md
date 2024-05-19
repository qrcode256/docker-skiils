# docker-skiils

## ➤ BASIC

#### What is the docker


#### Docker guide
*  [🐳 Docker Guide](https://github.com/alpha74/Docker-Guide)
*  [🐳 Docker Cheat Sheet](https://github.com/slydeveloper/docker-cheat-sheet)


#### Docker Environment Variables
*  [🐳 Pass Docker Environment Variables During The Image Build](https://vsupalov.com/docker-build-pass-environment-variables/)
*  [🐳 How to Use Environment Variables ARG and ENV in Docker](https://www.bitdoze.com/docker-env-vars/)
*  [🐳 Syntax for environment files in Docker Compose](https://docs.docker.com/compose/environment-variables/env-file/)


#### Docker Practice resources
*  [🐳 Docker containers through concepts in slides and hands-on labs.](https://github.com/layer5io/containers-101-workshop/tree/master/)
*  [🐳 Docker training with DockerMe](https://github.com/AhmadRafiee/Docker_training_with_DockerMe)
*  [🐳 Docker Tutorials and Labs](https://github.com/docker/labs)
*  [🐳 Docker get started guide](https://docs.docker.com/get-started/)
*  [Docker for Node.js Course](https://www.bretfisher.com/docker-mastery-for-nodejs/)

#### Docker Video resources
*  [🐳 Docker Tutorial for Beginners](https://www.youtube.com/watch?v=pTFZFxd4hOI)
*  [🐳 Docker Compose Tutorial](https://www.youtube.com/watch?v=HG6yIjZapSA)
*  [DockerCon 2017 - Moby's Cool Hack Session](https://www.youtube.com/watch?v=-h2VTE9WnZs)

### Docker books
*  [🐳 Mastering Docker – Fourth Edition](https://github.com/PacktPublishing/Mastering-Docker-Fourth-Edition)


### Docker best practice
*  [🐳 General best practices for writing Dockerfiles ](https://docs.docker.com/develop/develop-images/guidelines/#create-ephemeral-containers)

### Docker using with Nodejs examples
- [Apollo server starter project with TypeScript and Docker.](https://github.com/AndreaDiotallevi/apollo-server-docker)
- [Kinsta - Hello World - Dockerfile - Node.js](https://github.com/kinsta/hello-world-dockerfile-nodejs)
- [Simple blog made using many different languages](https://github.com/arshamalh/blogo/blob/master/docker-compose.yml)
- [🎉 Node.js Rocks in Docker](https://github.com/BretFisher/nodejs-rocks-in-docker)
- [Docker for Node.js Projects From a Docker Captain](https://www.udemy.com/course/docker-mastery-for-nodejs)
- [🎉🎉🎉Play with Docker Classroom](https://training.play-with-docker.com/alacart/), [Labs play with docker](https://labs.play-with-docker.com/) [play-with-docker](https://github.com/play-with-docker/play-with-docker)

# Docker components

### Docker container

* #### What is the docker container
A Docker container is a runnable instance of an image. It is a normal operating system process except that this process is isolated and has its own file system, its own networking, and its own isolated process tree separate from the host. Containers are lightweight and contain everything needed to run the application, and it becomes the unit for distributing and testing your application.

### Docker images
* #### What is the docker image

A Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings. A Docker image is a read only template with instructions for creating a Docker container. In other words, a docker image is a docker object which is a template for creating containers.


### Docker registry

### Docker volums

    Volumes are persistent data stores implemented by the container engine. Compose offers a neutral way for services to mount volumes, and configuration parameters to allocate them to infrastructure.


* [🐳 Volumes top-level elements](https://docs.docker.com/compose/compose-file/07-volumes/)

### Docker dockerfile
* #### What is the dockerfile
Docker file is a blue print for creating docker images. Each instruction on a dockerfile creates a layer in the image. If you change the dockerfile and rebuild the image, only the changed layers are rebuilt. This makes images lightweight, small and fast. It is always named Dockerfile without an extention.

### Docker network

### Docker client
* #### What is the docker client
Docker client docker is how users interact with docker. docker client sends commands such as docker run to dockerd to be carried out. Docker client can communicate with more than one docker daemon.


### Docker compose



#### Sources files:

[RU] [Read more about docker](https://habr.com/ru/companies/ruvds/articles/439980/)
[RU] [Docker compose for beginner](https://habr.com/ru/companies/ruvds/articles/450312/)


### From Containers to Kubernetes with Node.js
https://www.digitalocean.com/community/tutorial-series/from-containers-to-kubernetes-with-node-js