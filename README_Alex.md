# Open Liberty Containeriztion Tutorial

## 1. Introduction

### 1.1 What you'll learn

#### 1.1.1 Service Containerization from development to production

From development to production, and across your DevOps environments, you can deploy your microservices in a lightweight and portable manner by using containers. You can run a container from a container image. Each container image is a package of what you need to run your microservice or application, from the code to its dependencies and configuration.

You’ll learn how to build container images and run containers using Docker for your microservices. You’ll learn about the Open Liberty container images and how to use them for your containerized applications. You’ll construct Dockerfile files, create and run Docker images. 

#### 1.1.2 Communication between services

Comunication between services: The two microservices that you’ll be working with are called system and inventory. The system microservice returns the JVM system properties of the running container. The inventory microservice adds the properties from the system microservice to the inventory. This guide demonstrates how both microservices can run and communicate with each other in different Docker containers.

### 1.2 Prerequisites

- [x] Have linux environment --> In my cas wsl [How to install wsl](https://www.omgubuntu.co.uk/how-to-install-wsl2-on-windows-10)
- [x] Have mvn installed --> [How to install mvn in wsl](https://kontext.tech/article/630/install-maven-on-wsl)
- [x] Have Docker installed --> [How to install docker desktop in WSL](https://docs.docker.com/desktop/windows/wsl/)

### 1.3 Repository Structure

```
├── start/ --> multi-module liberty mvn project ready to build and run with mvn
|    ├── inventory/ --> inventory microservice ready to build with liberty mvn
|    ├── system/ --> inventory microservice ready to build with liberty mvn
|    └── pom.xml --> pom.xml to compile both apps
├── finish/ --> liberty apps ready to build and run with Docker
|    ├── inventory/
|    ├── system/
|    └── pom.xml
├── scripts/ --> unit testing for build and deploy steps 
├── README.md
└── ...
```

## 2. Build & Run Liberty Apps with mvn

Navigate to the `start` directory to begin.

You can find the starting Java project in the start directory. This project is a multi-module Maven project that is made up of the system and inventory microservices. Each microservice is located in its own corresponding directory, system and inventory.

To try out the microservices by using Maven, run the following Maven goal to build the system microservice and run it inside Open Liberty:

