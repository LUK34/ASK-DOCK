## Life without Docker

### (1) "It works on my machine" problems

- Developers would build and test apps on their laptops.

- When they move the app to servers, it often breaks because of differences in OS, libraries, versions, configurations, etc.

Without Docker, teams would spend hours or days debugging these environment differences.


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


## What is Port Mapping ?

- Note: By default, services running inside a Docker container are isolated and not accessible from outside.
- Docker port mapping is the process of linking container port to host machine port.
- It is used to  allow external access to applications running inside the container.
- Syntax : docker run -p <host_port>:<container_port> image_name
- Note: host port and container port no need to be same.



=============
Dockerfile
=============

=> Dockerfile contains set of instructions to build docker image.

		Filename : Dockerfile

=> To write dockerfile we will use below keywords

		1) FROM
		2) MAINTAINER
		3) RUN
		4) CMD
		5) COPY
		6) ADD
		7) WORKDIR
		8) EXPOSE
		9) ENTRYPOINT

====
FROM
=====

=> It is used to specify base image required to run our application.

ex:

FROM openjdk:17

FROM python:3.3

FROM tomcat:9.0

FROM mysql:8.5

FROM node:19.5

============
MAINTAINER
============

=> MAINTAINER is used to specify who is author of this Dockerfile.

=> This is Optional in Dockerfile.

Ex : MAINTAINER Ashok <ashok.b@oracle.com>


=====
RUN 
=====

=> RUN keyword is used to specify instructions (commands) to execute at the time of docker image creation.

Ex : 

RUN 'git clone <repo-url>'

RUN 'mvn clean package'

Note: We can specify multiple RUN instructions in Dockerfile and all those will execute in sequential manner.

========
CMD
========

=> CMD keyword is used to specify instructions (commands) which are required to execute at the time of docker container creation.

Ex:

CMD 'java -jar app.jar'

CMD 'python app.py'

Note: If we write multiple CMD instructions in dockerfile, docker will execute only last CMD instruction.


=====
COPY
=====

=> COPY instruction is used to copy the files from source to destination.

Note: It is used to copy application code from host machine to container machine.

		Source : HOST Machine

		Destination : Container machine

Ex : 

COPY target/app.jar  /usr/app/		

COPY target/app.war /usr/bin/tomcat/webapps/

COPY app.py  /usr/app/

=====
ADD
=====

=> ADD instruction is used to copy the files from source to destination.

Ex : 

ADD <source> <destination>

ADD <URL> <destination>

========
WORKDIR
========

=> WORKDIR instruction is used to set / change working directory in container machine.

ex: 

COPY target/sbapp.jar /usr/app/

WORKDIR /usr/app/

CMD 'java -jar sbapp.jar'

========
EXPOSE
========

=> EXPOSE instruction is used to specify application is running on which PORT number.

Ex : 

EXPOSE 8080

Note: By using EXPOSE keyword we can't change application port number. It is just to provide information the people who are reading our Dockerfile.


===========
ENTRYPOINT
===========

=> It is used to execute instruction when container is getting created.

Note: ENTRYPOINT is used as alternate for 'CMD' instructions.


CMD "java -jar app.jar"

ENTRYPOINT ["java", "-jar", "app.jar"]

================================================
What is the diff between 'CMD' & 'ENTRYPOINT' ?
=================================================

=> CMD instructions we can override while creating docker container.

=> ENTRYPOINT instructions we can't override.

==================
Sample Dockerfile
==================

FROM ubuntu

MAINTAINER Ashok <ashok.b@oracle.com>

RUN echo 'hello from run instruction-1'
RUN echo 'hello from run instruction-2'

CMD echo 'hi from cmd-1'
CMD echo 'hi from cmd-2'

-------------------------------------------

# create docker image using dockerfile
$ docker build -t img-1 .

# Run docker image to create docker container
$ docker run img-1
