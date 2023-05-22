# Open Liberty Containerizing Microservices

## 1. Introduction

### 1.1 What you'll learn

#### 1.1.1 Service Containerization from development to production

From development to production, and across your DevOps environments, you can deploy your microservices in a lightweight and portable manner by using containers. You can run a container from a container image. Each container image is a package of what you need to run your microservice or application, from the code to its dependencies and configuration.

You’ll learn how to build container images and run containers using Docker for your microservices. You’ll learn about the Open Liberty container images and how to use them for your containerized applications. You’ll construct `Dockerfile` files, create and run Docker images. 

#### 1.1.2 Communication between services

Comunication between services: The two microservices that you’ll be working with are called system and inventory. The system microservice returns the JVM system properties of the running container. The inventory microservice adds the properties from the system microservice to the inventory. This guide demonstrates how both microservices can run and communicate with each other in different Docker containers.

### 1.2 Prerequisites

- [x] Have linux environment --> In my cas wsl [How to install wsl](https://www.omgubuntu.co.uk/how-to-install-wsl2-on-windows-10)
- [x] Have mvn installed --> [How to install mvn in wsl](https://kontext.tech/article/630/install-maven-on-wsl)
- [x] Have Docker installed --> [How to install docker desktop in WSL](https://docs.docker.com/desktop/windows/wsl/)

### 1.3 Repository Structure

```
├── mvn_ready/ --> multi-module liberty mvn project ready to build and run with mvn
|    ├── inventory/ --> inventory microservice ready to build with liberty mvn
|    |    ├── src/main/ --> microservice code
|    |    |      ├── java/ --> app code
|    |    |      ├── liberty/config/ --> server.xml server cinfiguration
|    |    |      └── webapp/ --> web configuration template
|    |    ├── target/ --> compiled directory
|    |    └── pom.xml --> mvn file to compile the microservice
|    ├── system/main/ --> inventory microservice ready to build with liberty mvn
|    └── pom.xml --> pom.xml to compile both apps
├── docker_ready/ --> liberty apps ready to build and run with Docker
|    ├── inventory/
|    ├── system/
|    └── pom.xml
├── scripts/ --> unit testing for build and deploy steps 
├── README.md
└── ...
```

## 2. Build & Run Liberty Services with Maven

Navigate to the `mvn_ready` directory to begin.

You can find the starting Java project in the mvn_ready directory. This project is a multi-module Maven project that is made up of the system and inventory microservices. Each microservice is located in its own corresponding directory, system and inventory.

To try out the microservices by using Maven, run the following Maven goal to build the system microservice and run it inside Open Liberty:

```bash
mvn -pl system liberty:run
```

pen another command-line session and run the following Maven goal to build the inventory microservice and run it inside Open Liberty:

```bash
mvn -pl inventory liberty:run
```

After you see the following message in both command-line sessions, both of your services are ready:

```
The defaultServer server is ready to run a smarter planet.
```

To access the `inventory` service, which displays the current contents of the inventory, see [http://localhost:9081/inventory/systems](http://localhost:9081/inventory/systems)

```bash
curl http://localhost:9081/inventory/systems
```


> Defined at [mvn_ready/inventory/pom.xml](mvn_ready/inventory/pom.xml) and [mvn_ready/inventory/src/main/liberty/config/server.xml](mvn_ready/inventory/src/main/liberty/config/server.xml)

To access the `system` service, which shows the system properties of the running JVM, see [http://localhost:9080/system/properties](http://localhost:9080/system/properties)

```bash
curl http://localhost:9080/system/properties
```

> Defined at [mvn_ready/system/pom.xml](mvn_ready/system/pom.xml) and [mvn_ready/system/src/main/liberty/config/server.xml](mvn_ready/system/src/main/liberty/config/server.xml)

You can add the system properties of your localhost to the `inventory` service at [http://localhost:9081/inventory/systems/localhost](http://localhost:9081/inventory/systems/localhost)

```bash
curl http://localhost:9081/inventory/systems/localhost
```

After you are finished checking out the microservices, stop the Open Liberty servers by pressing `CTRL+C` in the command-line sessions where you ran the servers. Alternatively, you can run the `liberty:stop` goal in another command-line session:

```
mvn -pl system liberty:stop
mvn -pl inventory liberty:stop
```

To package your microservices, run the Maven package goal to build the application .war files from the `mvn_ready` directory so that the .war files are in the system/target and inventory/target directories. This is needed to run them with Docker.

```
mvn package
```

To learn more about RESTful web services and how to build them, see https://openliberty.io/guides/rest-intro.html[Creating a RESTful web service^] for details about how to build the `system` service. The `inventory` service is built in a similar way.

## 3. Build & Run Liberty Services with Docker

### 3.1 Defining Dockerfiles

#### 3.1.1 What is Dockerfile?

A Docker image is a binary file. It is made up of multiple layers and is used to run code in a Docker container. Images are built from instructions in `Dockerfile` to create a containerized version of the application.

Every `Dockerfile` begins with a parent or base image over which various commands are run. For example, you can start your image from scratch and run commands that download and install a Java runtime, or you can start from an image that already contains a Java installation.

Learn more about Docker on the [Official Docker page](https://www.docker.com/what-docker)

You will be creating two Docker images to run the inventory service and system service. The first step is to create Dockerfiles for both services.

#### 3.1.2 Liberty base image used

In this guide, you’re using an official image from the IBM Container Registry (ICR), `icr.io/appcafe/open-liberty:full-java11-openj9-ubi`, as your parent image. This image is tagged with the word full, meaning it includes all Liberty features. full images are recommended for development only because they significantly expand the image size with features that are not required by the application.

>:mega: **NOTA:**
>
>To minimize your image footprint in production, you can use one of the kernel-slim images, such as `icr.io/appcafe/open-liberty:kernel-slim-java11-openj9-ubi`. This image installs the basic server. You can then add all the necessary features for your application with the usage pattern that is detailed in the Open Liberty container image documentation. To use the default image that comes with the Open Liberty runtime, define the `FROM` instruction as `FROM icr.io/appcafe/open-liberty`. You can find all official images on the [Open Liberty container image repository](https://openliberty.io/docs/latest/container-images.html).

### 3.2 Building services images

Autoexplained with comments [docker_ready/inventory/Dockerfile](docker_ready/inventory/Dockerfile).

Autoexplained with comments [docker_ready/system/Dockerfile](docker_ready/inventory/Dockerfile).

Now that your microservices are packaged and you have written your Dockerfiles, you will build your Docker images by using the `docker build` command.

```bash
docker build -t system:1.0-SNAPSHOT system/.
docker build -t inventory:1.0-SNAPSHOT inventory/.
```

>:warning: **WARNING:**
>
> A previous `mvn package` of the project is needed, because Dockerfiles use .war files generated by `mvn package` execution.

To verify that the images are built, run the docker images command to list all local Docker images:

```bash
docker images -f "label=org.opencontainers.image.authors=Your Name"
```
```
REPOSITORY    TAG             IMAGE ID        CREATED          SIZE
inventory     1.0-SNAPSHOT    08fef024e986    4 minutes ago    1GB
system        1.0-SNAPSHOT    1dff6d0b4f31    5 minutes ago    977MB
```


### 3.3 Running your microservices in Docker containers


