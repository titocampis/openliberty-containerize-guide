# Open Liberty Containerizing Microservices

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
|    |    ├── src/main/ --> microservice code
|    |    |      ├── java/ --> app code
|    |    |      ├── liberty/config/ --> server.xml server cinfiguration
|    |    |      └── webapp/ --> web configuration template
|    |    ├── target/ --> compiled directory
|    |    └── pom.xml --> mvn file to compile the microservice
|    ├── system/main/ --> inventory microservice ready to build with liberty mvn
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

[role='command']
```bash
mvn -pl system liberty:run
```

pen another command-line session and run the following Maven goal to build the inventory microservice and run it inside Open Liberty:

[role='command']
```bash
mvn -pl inventory liberty:run
```

After you see the following message in both command-line sessions, both of your services are ready:
[role='command']
```
The defaultServer server is ready to run a smarter planet.
```

To access the `inventory` service, which displays the current contents of the inventory, see http://localhost:9081/inventory/systems[http://localhost:9081/inventory/systems^].

> Defined at [start/inventory/pom.xml](start/inventory/pom.xml) and [start/inventory/src/main/liberty/config/server.xml](start/inventory/src/main/liberty/config/server.xml)

To access the `system` service, which shows the system properties of the running JVM, see http://localhost:9080/system/properties[^].

> Defined at [start/system/pom.xml](start/system/pom.xml) and [start/system/src/main/liberty/config/server.xml](start/system/src/main/liberty/config/server.xml)

You can add the system properties of your localhost to the `inventory` service at http://localhost:9081/inventory/systems/localhost[http://localhost:9081/inventory/systems/localhost^].

After you are finished checking out the microservices, stop the Open Liberty servers by pressing `CTRL+C` in the command-line sessions where you ran the servers. Alternatively, you can run the `liberty:stop` goal in another command-line session:

[role='command']
```
mvn -pl system liberty:stop
mvn -pl inventory liberty:stop
```




