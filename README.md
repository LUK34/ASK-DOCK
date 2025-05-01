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

## --------------------------		

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

## --------------------------

## What is Port Mapping ?

- Note: By default, services running inside a Docker container are isolated and not accessible from outside.
- Docker port mapping is the process of linking container port to host machine port.
- It is used to  allow external access to applications running inside the container.
- Syntax : docker run -p <host_port>:<container_port> image_name
- Note: host port and container port no need to be same.

## --------------------------	