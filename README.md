## Life without Docker

-------------------------------------------
### (1) "It works on my machine" problems

- Developers would build and test apps on their laptops.
- When they move the app to servers, it often breaks because of differences in OS, libraries, versions, configurations, etc.
- Without Docker, teams would spend hours or days debugging these environment differences.

### (2) Complex Software Installations

- installing something like a database (e.g., PostgreSQL) required manually:
		- download right version of s/w
		- install dependencies
		- configure services properly

### (3) Heavy Virtual Machines (VMs)

- Before Docker, developers used Virtual Machines like VirtualBox, VMware, or Hyper-V to simulate servers.
- VMs are large, slow to start, and resource-hungry (each VM has a full OS inside).

### (4) Scaling Applications is Difficult
- Manually configuring and copying app binaries.
- Manually setting up load balancers.

## Running Real-world applications using docker images

### public docker image name (java springboot app) : ashokit/spring-boot-rest-api
- docker pull ashokit/spring-boot-rest-api
- docker run ashokit/spring-boot-rest-api
- docker run -d ashokit/spring-boot-rest-api
- Syntax : docker run -d -p <host-port>:<container-port> ashokit/spring-boot-rest-api
- docker run -d -p 9090:9090 ashokit/spring-boot-rest-api
- Note: To access application running in the container we will use below URL

### Java App URL : http://host-public-ip:host-port/welcome/{name}
- Note: Host port number we need to enable in ec2-vm security group inbound rules to allow the traffic.

### Refer `Output 1`

-------------------------------------------

### create docker image using dockerfile
$ docker build -t img-1 .

### Run docker image to create docker container
$ docker run img-1

-------------------------------------------

## What is Port Mapping ?

- Note: By default, services running inside a Docker container are isolated and not accessible from outside.
- Docker port mapping is the process of linking container port to host machine port.
- It is used to  allow external access to applications running inside the container.
- Syntax : docker run -p <host_port>:<container_port> image_name
- Note: host port and container port no need to be same.

-------------------------------------------

### public docker image name (python app) : ashokit/python-flask-app

docker pull ashokit/python-flask-app

docker run -d -p 5000:5000 ashokit/python-flask-app

### Python App URL : http://host-public-ip:host-port/

Note: Host port number we need to enable in ec2-vm security group inbound rules to allow the traffic.

### Refer `Output 2`

-------------------------------------------

## Dockerfile

- Dockerfile contains set of instructions to build docker image.
- Filename : Dockerfile
- To write dockerfile we will use below keywords
- FROM
- MAINTAINER
- RUN
- CMD
- COPY
- ADD
- WORKDIR
- EXPOSE
- ENTRYPOINT

### FROM
- It is used to specify base image required to run our application.
- ex:
- FROM openjdk:17
- FROM python:3.3
- FROM tomcat:9.0
- FROM mysql:8.5
- FROM node:19.5

### MAINTAINER
- MAINTAINER is used to specify who is author of this Dockerfile.
- This is Optional in Dockerfile.
- Ex:
- MAINTAINER Ashok <ashok.b@oracle.com>

### RUN 
- RUN keyword is used to specify instructions (commands) to execute at the time of docker image creation.
- Ex : 
- RUN 'git clone <repo-url>'
- RUN 'mvn clean package'
- Note: We can specify multiple RUN instructions in Dockerfile and all those will execute in sequential manner.

### CMD
- CMD keyword is used to specify instructions (commands) which are required to execute at the time of docker container creation.
- Ex:
- CMD 'java -jar app.jar'
- CMD 'python app.py'
- Note: If we write multiple CMD instructions in dockerfile, docker will execute only last CMD instruction.

### COPY
- COPY instruction is used to copy the files from source to destination.
- Note: It is used to copy application code from host machine to container machine.
- Source : HOST Machine
- Destination : Container machine
- Ex : 
- COPY target/app.jar  /usr/app/		
- COPY target/app.war /usr/bin/tomcat/webapps/
- COPY app.py  /usr/app/

### ADD
- ADD instruction is used to copy the files from source to destination.
- Ex : 
- ADD <source> <destination>
- ADD <URL> <destination>

### WORKDIR
- WORKDIR instruction is used to set / change working directory in container machine.
- ex: 
- COPY target/sbapp.jar /usr/app/
- WORKDIR /usr/app/
- CMD 'java -jar sbapp.jar'

### EXPOSE
- EXPOSE instruction is used to specify application is running on which PORT number.
- Ex : 
- EXPOSE 8080
- Note: By using EXPOSE keyword we can't change application port number. It is just to provide information the people who are reading our Dockerfile.

### ENTRYPOINT
- It is used to execute instruction when container is getting created.
- Note: ENTRYPOINT is used as alternate for 'CMD' instructions.
- CMD "java -jar app.jar"
- ENTRYPOINT ["java", "-jar", "app.jar"]

### What is the diff between 'CMD' & 'ENTRYPOINT' ?
- CMD instructions we can override while creating docker container.
- ENTRYPOINT instructions we can't override.

### Sample Dockerfile

FROM ubuntu

MAINTAINER Ashok <ashok.b@oracle.com>

RUN echo 'hello from run instruction-1'
RUN echo 'hello from run instruction-2'

CMD echo 'hi from cmd-1'
CMD echo 'hi from cmd-2'

### Refer `Output 3`

-------------------------------------------

## Dockerizing java web app (Without SpringBoot)

- Java web app will be packaged as "war" file.
- Note: To package java application we will use 'Maven' as build tool.
- Note: war file will be created inside project "target" directory
- To deploy war file we need web server (Ex: tomcat)
- Inside tomcat server 'webapps' directory we need to place our war file to run the application.

### Dockerfile to run java web app

FROM tomcat:latest

EXPOSE 8080

COPY target/app.war /usr/local/tomcat/webapps/

## Lab Task

@@ Java Web App Git Repo : https://github.com/ashokitschool/maven-web-app.git

- Note: Connect with Docker VM using SSH client and execute below commands

### install git client
$ sudo yum install git -y

### install maven s/w
$ sudo yum install maven -y

### clone project git repo
$ git clone https://github.com/ashokitschool/maven-web-app.git

### build maven project
$ cd maven-web-app
$ mvn clean package

### check project war file
$ ls -l target

### build docker image
$ docker build -t <img-name> .
$ docker images

### Create Docker Container
$ docker run -d -p 8080:8080 <image-name>
$ docker ps

- Enable host port number in security group inbound rules and access our application.
- URL :: http://public-ip:8080/maven-web-app/

### Refer `Output 4`

-------------------------------------------

## Dockerizing Java Spring Boot Application	
- Every JAVA SpringBoot application will be packaged as "jar" file only.
- Note: To package java application we will use 'Maven' as build tool.
- To run spring boot application we need to execute jar file.
- Syntax : java -jar <jar-file-name>
- Note: When we run springboot application jar file then springboot will start tomcat server with 8080 port number (embedded tomcat server).

## Dockerfile to run SpringBoot App

FROM openjdk:17

COPY target/app.jar /usr/app/

WORKDIR /usr/app/

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]

## Lab Task

### Java Spring Boot App Git Repo : https://github.com/ashokitschool/spring-boot-docker-app.git

- Note: Connect with Docker VM using SSH client and execute below commands

### clone project git repo
$ git clone https://github.com/ashokitschool/spring-boot-docker-app.git

### build maven project
$ cd spring-boot-docker-app
$ mvn clean package

### check project war file
$ ls -l target

### build docker image
$ docker build -t <img-name> .
$ docker images

### Create Docker Container
$ docker run -d -p 9090:8080 <image-name>
$ docker ps

=> Enable host port number in security group inbound rules and access our application.

		URL :: http://public-ip:9090/
		
### Refer `Output 5`

-------------------------------------------

## Dockerizing Python application 
- Python is a general purpose language.
- Note: It is also called as scripting language.
- We don't need any build tool for python applications.
- We can run python application code directley like below
- Syntax : $ python app.py
- If we need any libraries for python (Ex: Flask) application development then we will mention them in "requirements.txt" file
- Note: We will use "python pip" s/w to download libraries configured in requirements.txt file.

## Python Flask App Dockerfile

FROM python:3.6

COPY . /app/

WORKDIR /app/

RUN pip install -r requirements.txt

EXPOSE 5000

ENTRYPOINT ["python", "app.py"]

-------------------------------------------

## Lab Task

@@ Python Flask App Git Repo : https://github.com/ashokitschool/python-flask-docker-app.git


### Clone git repo
$ git clone https://github.com/ashokitschool/python-flask-docker-app.git

### Go inside project directory
$ cd python-flask-docker-app

### Create docker image
$ docker build -t ashokit/pyapp .
$ docker images

### Create container
$ docker run -d -p 5000:5000 pyapp
$ docker ps

### Access application in browser
URL : http://public-ip:5000/

### Refer `Output 6`

-------------------------------------------

## Dockerfile for Angular Application

FROM node:18 AS build

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

RUN npm run build --prod

FROM nginx:alpine

EXPOSE 80

COPY --from=build /app/dist/angular_docker_app /usr/share/nginx/html

### 1. Build Stage (Multi-stage Build with Node.js)

### FROM node:18 AS build
- This starts a new build stage using the Node.js v18 image.
- The alias build allows us to refer to this stage later in the final image.
- Node.js is required here to install dependencies and build the Angular app.

### WORKDIR /app
- Sets the working directory inside the container to /app.
- All subsequent commands will run relative to this directory.

### COPY package*.json ./
- Copies package.json and package-lock.json (if it exists) from your host to the container.
- These files are essential for installing project dependencies.

### RUN npm install
- Installs the npm dependencies listed in package.json.

### COPY . .
- Copies the entire Angular project (source code and files) into the container's /app directory.

### RUN npm run build --prod
- Runs the Angular CLI build command in production mode, outputting the built app into /app/dist/angular_docker_app.


###  2. Production Stage (Nginx)

### FROM nginx:alpine
- This starts a second stage using a lightweight Alpine-based Nginx image.
- Nginx is used to serve the static Angular files.

### EXPOSE 80
- Informs Docker that the container will listen on port 80 (standard HTTP port).

### COPY --from=build /app/dist/angular_docker_app /usr/share/nginx/html
- Copies the built Angular files from the build stage to Nginx's default web root directory.
- These files are now ready to be served by Nginx.

### Refer `Output 7`

-------------------------------------------

## Dockerfile for React Application:

FROM node:18.13.0 as build

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

RUN npm run build --prod

FROM nginx:latest

COPY --from=build /app/build /usr/share/nginx/html

EXPOSE 80

###  Stage 1: Build the App
- FROM node:18.13.0 as build
- Uses the official Node.js 18.13.0 image
- This stage is named build for reference in the next stage.

### WORKDIR /app
- Sets the working directory inside the container to /app.

### COPY package*.json ./
- Copies package.json and package-lock.json (if it exists) to the container. These files contain dependencies.

### RUN npm install
- Installs all dependencies listed in package.json.

### COPY . .
- Copies the rest of the project files into the container's /app directory.

### RUN npm run build --prod
- Builds the project in production mode.
- This will usually generate a build (React) or dist (Angular) folder containing optimized static files (HTML, CSS, JS).

### Stage 2: Serve with Nginx
- FROM nginx:latest
- Uses the official Nginx image to serve the static files.

### COPY --from=build /app/build /usr/share/nginx/html
- Copies the production-ready build output from the previous stage (build stage) to Nginx’s default public directory.

### EXPOSE 80
- Opens port 80 so Nginx can serve traffic on that port.

### For React Application refer `Output 8`

-------------------------------------------






















