
# All Things Docker and Kubernetes #

This repository will contain my notes, drafts, and projects on containerization and orchestration.

It's still at an early stage, but building (and breaking) and deploying containerized workloads are one of the targets I have on my list for ~~2021~~ the present.

Ayt, going full steam ahead!

<p align=center>
    <img src="Images/docker-6.jpg" width=500>
</p>


## Docker Basics

Whether you're entirely new to the world of containers or you just simply want to do a quick refresher, feel free to select the topic you're interested in and browse through the bite-sized information.

<details><summary> From VMs to Containers </summary>

### From VMs to Containers

The traditional way fo deploying applications was through *virtual machines or VMs*. The application would use the OS file system, the OS's resources and default packages installed. 

Virtual machines have been extremely efficient in maximizing the use of infrastructure. Instead of running one application on one physical machine, we could run multiple VMs on top of the *hypervisor* which sits on the physical machine.

![](Images/us-5-vms.png)

While it has proved to be useful, it still has its own disadvantages:
- Each virtual machine would require its own operating system. - If you have three VMs sitting on the hypervisor, this would mean there's also three operating system running on top of the hypervisor
- The OS plus the required libraries of each VM takes up a chunk of space on the underlying machine

![](Images/us-5-vm-containers.png)

**Enters containers.** To further optimize the usage of the server, container can be used to *virtualized the operating system.* Through containers, we won't need to run replicated OS. Regardless of how many containers we run on the machine, they will all use the same underlying operating system of the physical machine itself.

To facilitate the creation and management of the containers, we can use a container engine tool, such as **Docker.** 

Overall, the benefits of containers are:
- lightweight in nature
- provides better use of the resources
- develop applications that run consistently across platforms
- can be managed to scale well
</details>

<details><summary> What is Docker? </summary>

### What is Docker?

Docker is a container platform that allows you to separate your application from the underlying infrastructure by bundling the code and all of its dependencies into a self-contained entity that will run the same on any supported system.

![](Images/udacity-suse-5-banner.png)

Once you have implemented the software, the next phase would be to release it. The steps would primarily be:
- package the source code, config files, and dependencies in a container
- deploy it using a container manage

There three docker objects which you need to know here:
the 
- dockerfile
- docker image
- docker registry

</details>

<details><summary> Dockerfile </summary>

### Dockerfile

Before we "bake" the image, we first need to understand how the application is built. We could start by listing down how we might deploy an application manually.

As an example, if we are to deploy a web application, this would be the steps we'll follow:

```bash
1.  Start with OS - CentOS
2.  Update repo
3.  Install dependencies
4.  Install python dependencies
5.  Copy source code to /opt folder
6.  Run the web server using the flask command
```

Once we have the steps laid down, we can begin containerizing our application:

```bash
1.  Create a DOCKERFILE
2.  Build your image and specify the file as input, as well as the tag name
3.  Push it to the dockerhub repository to make it publicly available 
```

### So What the Heck is a Dockerfile?

This is a set of step-by-step instructions on how to create the image:
- how to package the code and its dependencies
- each operation represents a layer and is cached
- when dockerfile is modified, onlychanged layers will be built

The full list of instructions can be found in the official [Docker website](https://docs.docker.com/engine/reference/builder/). Here are some of the widely used instructions:
- **FROM** - sets the base image
- **RUN** - executes a command
- **COPY** and **ADD** - copy files from host to container
- **CMD** - default command to execute when container starts
- **EXPOSE** - exposes a port

Below is an example of a Dockerfile that targets to package a Python hello-world application:

<details><summary> dockerfile </summary>
 
```bash
# set the base image. A Python base image is used
FROM python:3.8

# set a key-value label for the Docker image
LABEL maintainer="Eden Jose"

# All the files in the current directory is copied
# to the  `/app` directory in the container
COPY . /app

#  defines the working directory within the container
WORKDIR /app

# run commands within the container. 
# Here we install dependencies defined in the requirements.txt file. 
RUN pip install -r requirements.txt

# provide a command to run on container start. 
# For example, start the `app.py` application.
CMD [ "python", "app.py" ]
```
 
</details>


</details>

<details><summary> Docker image </summary>

### Docker Image

After we've created the dockerfile, we can now create the docker image. 
- read-only template
- creates a runnable instance of the application
- used to run the container

A Docker image can be built from an existing Dockerfile using the docker build command. Below is the syntax for this command:

```docker
$ docker build [OPTIONS] PATH
```

To find all valid options for the <code>build</code> command:
```docker
$ docker build --help
```

For example, to build the image of a Python "Hello-world " application from the Dockerfile in the current directory, the following command can be used:

```docker
$ docker build -t python-helloworld .
```

Additionally, you can build the same app that's located on a different directory, say a <code>lesson1/python-app</code> directory

```docker
$ docker build -t python-helloworld /another/directory/python-app
```

To list all available imageS
```DOCKER
$ docker images
```

You can read more about the docker basic commands in the succeeding sections.

</details>


<details><summary> Docker registry </summary>

### Docker Registry

Once you've package the application, tested it locally, and proved that it's meeting the expected behavior, You are now ready to store and distribute it.

To do this, you can push the image to a public Docker image registry, such as
- DockerHub
- Harbor
- Amazon ECR (Elastic Container Registry)
- Google Container Registry

You can also store the image to a private registries and make it available to authorized parties. 

### Tagging the Image

Before pushing an image to a Docker registry, it is highly recommended to tag it first. Without the tagging, the image would be allocated with a random ID during the build stage.

Tagging also provides version control as a new tage would indicate a new release. To tag an existing image on the local machine,

To login into DockerHub
```bash
$ docker login
```

Once the image is tagged, the final step is to push the image to a registry. 

```bash
$ docker push NAME[:TAG]
```

For example, to push the sample Python hello-world application tagged with v1 to 'my-repo' repository in DockerHub
```bash
$ docker push my-repo/python-helloworld:v1.0.0
```

To pull an image from DockerHub,
```bash
$ docker pull NAME[:TAG]
```

</details>


<details><summary> Basic Commands </summary>

### Basic commands

This will run an image from a container.

```bash
$ docker run <image>
```

In the example below, an NGINX container will be ran. Docker will look in the host for the image, and if not found, will check dockerhub na dpull image down.

Note that this is only done first time. For subsequest executions, the same image pulled down will be re-used.
docker run nginx

```bash
$ sudo docker run nginx 
```

To find all valid options for this command
```docker
$ docker run --help
```

Run the next command to list all running containers. Each container will get a random 'silly name' assigned to it. This is found in the last column of the output.

```bash
$ docker ps
```

To list all running AND not running containers, run the command below. This also shows the state, 
- if not running - "Exited"
- if running - "Up"

```bash
$ docker ps -a
```

You can set your own contianer-name by using the "--name" flag.

```bash
$ sudo docker run --name Thanos_of_2019 docker/whalesay cowsay Im-Inevitable! 
```

You can also specify the version of image to run in case you have multiple versions of an image by specifying "image:version". This is called a **TAG**.

In this example, we'll pull down two versions of nginx.

```bash
$ sudo docker run nginx
$ sudo docker run nginx:1.14-alpine
```

To run a container from the 1.14-alpine image and set container-name to nginx-2

```bash
$ sudo docker run -d --name nginx-2 nginx:1.14-alpine  
```

To know all the available tags for an image, look for the image in dockerhub.com and you'll find all the supported tags there.

You can also run multiple isntances/containers from the same image

```bash
$ sudo docker run docker/whalesay cowsay Infinity-and-beyond!
$ sudo docker run docker/whalesay cowsay Hello-there!
$ sudo docker run docker/whalesay cowsay Cowabunga!
```

This will show three running containers when you list them.

<details><summary> sudo docker ps -a </summary>
 
```bash
$ sudo docker ps -a

CONTAINER ID   IMAGE             COMMAND                  CREATED          STATUS                      PORTS     NAMES
b4b832aecfb9   docker/whalesay   "cowsay Cowabunga!"      8 seconds ago    Exited (0) 7 seconds ago              happy_gates
a870817d8e27   docker/whalesay   "cowsay Hello-there!"    2 minutes ago    Exited (0) 2 minutes ago              festive_elion
16e5d555d741   docker/whalesay   "cowsay Infinity-and…"   41 minutes ago   Exited (0) 41 minutes ago             friendly_feistel
```
</details>
</br>


This pulls the image down to our host but doesn't run a container.

```bash
$ sudo docker pull <image-name>
$ sudo docker pull nginx
```

This lists all images present in our host.

```bash 
$ docker images
```

This stops a running container. You can simply specify the "silly-name" or first few digits of the container-id. If the first 2 or 3 digits of the container-id is unique, docker will immediately know which container you want to stop.

```bash
$ docker stop <silly-name>
$ docker stop <container-id>
```

This removes the container permanently. You can list all containers afterwards to check.
```bash
$ docker rm <silly-name>
```

You can also **remove all stopped containers** AT ONCE by running the _ps_ command and passing it to the _rm_ command. Note that it'll return an error if there is a running container.

```bash
$ sudo docker rm $(sudo docker ps -a -q)
```

This removes an image permanently. NOTE that no container must be running off of that image. Delete all dependent containers to delete an image.

```bash 
$ docker rmi <image-name>
```

It's important to remember that containers are not meant to run operating systems. Container are meant to run a specific task or process.

Once task is complete, the container exits. The container runs as long as the process inside it is alive. When you run ubuntu, you're just running a base-image of an OS. There is no application/process running in it by default.

```bash 
$ sudo docker run ubuntu
```

You can instruct a process to run by appending the command. In this case, we're telling ubuntu to sleep for 60 seconds. This will appear as a running process for a minute when we run docker _ps_. After that, container will exit.

```bash
$ sudo docker run ubuntu <command>
$ sudo docker run ubuntu cat /etc/hosts
```

We can also execute a command on a running container

```bash
$ sudo docker exec <container-id> <command>
$ sudo docker exec <silly-name> <command>
```

If container is running, we can use _exec_ to exeucte a command.

```bash
$ sudo docker ps
$ sudo docker exec <container-id> cat /etc/hosts
$ sudo docker exec <silly-name> cat /etc/hosts
```

To retrieve the docker container logs, we can use the command below, where <code>95173091eb5e</code> is the container ID.

```docker
$ docker logs 95173091eb5e

## Example output from a Flask application
 * Serving Flask app "app" (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: off
```

</details>

<details><summary> Attach Mode </summary>

### Attach Mode

You can run a container is an **ATTACH** mode - this means process will run in the foreground. You cannot do anything else while process is attached to the console until container exits The console won't response to any input, except if you stop it by running Ctrl-C

As an example, we can run a simple web-server that listens on port 8080.

```bash
$ sudo docker run kodekloud/simple-webapp
```

Check the running containers.

```bash 
$ sudo docker ps

CONTAINER ID   IMAGE                     COMMAND           CREATED          STATUS          PORTS      NAMES
734e84936864   kodekloud/simple-webapp   "python app.py"   30 seconds ago   Up 29 seconds   8080/tcp   relaxed_grothendieck
```

</details>

<details><summary> Detach Mode </summary>

### Detach Mode

You can run a container in the background mode by using the "-d" flag. By doing this, you can still work on the console while container runs in the background.

```bash
$ sudo docker run -d ubuntu sleep 60 
```

To attach to the running container in the background, you can run the **attach** command, followed by either the container ID or the container name.

```bash
$ sudo docker ps
$ sudo docker attach <container-id>
$ sudo docker attach <silly-name>
```

You can also run and automatically log in to the container by using the "-it" flag.

```bash
sudo docker run -it -d --name nyancat2 06kellyjac/nyancat
```

</details>

<details><summary> Inputs </summary>

### Inputs

By default, a docker container doesn't listen to standard input. It runs in a non-interactive shell. You can map the standard input of your host to the docker container using the "-i" flag.

You can also map your terminal to the container's terminal by using the "-t" flag

    "-i"  - interactive
    "-t"  - terminal

As an example, we can use a simple image of an app that prompts the user for its name.

```bash
$ docker pull kodekloud/simple-prompt-docker
$ docker run -it kodekloud/simple-prompt-docker
```
</details>

<details><summary> Port Mapping </summary>

### Port Mapping

Recall that the underlying host (your machine) where docker is installed is called **DOCKER HOST** or **DOCKER ENGINE**.

If you want to access your app in the container through a web browser, we can use the container's IP, but note that this is an internal IP and is only accessible from the host itselF. This means that users outside the host cannot access this IP.

To get the IP address;

```bash
$ docker ps
$ docker inspect <container-id>
```

To access the ip from within the host, we can open the ip address in a browser in the host. we can also do a curl in the host's terminal. Note that 8080 is the default port of the container

```bash
$ curl <ip-of-vm>:8080
```

We can also use the IP of the docker host, but we need to map the port inside the container to the free port inside the docker host. We can use the "-p" flag to map the ports

As an example, we can use to map container port 5000 to host port 80.

```bash
$ docker run -d -p 80:8080 kodekloud/simple-webapp
```

To see the port mappings in your linux machine:

```bash 
$ netstat -tulpn
```
</details>

<details><summary> Data Persistence </summary>

### Data Persistence

If you have a database inside the container, the data in it will be lost as soon as the container exist or is removed. If you want to persist data, you can map a directory(in the container) to another directory(in the docker host).

To do this, use the "-v" flag (volume mapping), followed by the directory mapping.

```bash 
$ docker run -v <host-dir>:<container-dir> <image>
```

As an example, we create a /tmp/mysqldir in my linux machine and then mapped it to the default mysql directory inside the container.

```bash 
$ docker run -v /tmp/mysqldir:/var/lib/mysql mysql
```
</details>

<details><summary> Logs </summary>

### Logs

To see the logs, you can simply use the logs command

```bash
$ docker ps
$ docker logs <container-id>
```
</details>

<details><summary> Environment Variables </summary>

### Environment Variables
 
We can specify an environment variable that we can use in a given code. This is useful when we don't want to modify the code and instead use variables for any values that we need to change in the code

As an example, we have color-message.py which display a message on background color which we can change.

<details><summary> color-message.py </summary>
 
```python   
# color-message.py
import os
from flask import Flask

app = Flask(__name__)

color= os.environ.get(APP_COLOR)

@app.route("/")
def main():
    print(color)
    return render_template('hello.html', color=color)

if __name__="__main__":
    app.run(host="0.0.0.0", port="8080")
```
 
</details>
</br>


To run this image with an environment variable, use the -e flag followed by variable

```bash
$ docker run -e APP_COLOR=blue simple-webapp-color
```
</details>

<details><summary> CMD and ENTRYPOINT </summary>

### CMD and ENTRYPOINT

Recall that containers are not meant to host operating systems. Thus when you launch a container of a Linux Image like Ubuntu, it's default command or CMD is bash. This can be seen from the dockerfile itself. 

However if it doesn't detect any terminal, it just stops the process which also stops the container.

If you want to define a command or instruction to run besides the bash when the container is ran, you can specify it in the dockerfile using the CMD keyword.

As an example, we can set the container to sleep for 60 seconds when it is ran by:

```bash 
docker run ubuntu sleep 60
```

An easier way to do this is by including the command itself when creating the **Dockerfile.**

```bash
$ vi dockerfile 

FROM ubuntu

CMD sleep 60
```

These are the ways to specify a command in the dockerfile

```bash 
CMD <command> <parameter1>
CMD ["<command>", "<parameter1>"]                   <<< JSON format
```

We can also use a parameter from the commandline itself. This can be done by using ENTRYPOINT in the dockerfile
FROM ubuntu

```bash 
ENTRYPOINT ["sleep"]
```

Now when you run the container, you'll just have to define the parameter.

```bash
$ docker run ubuntu-sleeper 60
```

Note that you'll get an error when you don't append a parameter in the _docker run_ command because the ENTRYPOINT is expecting a parameter.

To include a default value in case user doesn't provide a parameter along with the _docker run_ command, you can use CMD and ENTRYPOINT together

```bash
FROM ubuntu

ENTRYPOINT ["sleep"]

CMD ["60"]
```

You can also override the entrypoint during runtime by using the "--entrypoint" flag

```bash
docker run --entrypoint sleep2.0 ubuntu-sleeper 60
```
</details>

<details><summary> Docker Compose </summary>

### Docker Compose 

Instead of running multiple RUN commands of different images, we could use docker compose. In this example, we'll use a sample voting app with result app architecture.

First method - multiple RUN

```bash 
docker run voting=app
docker run redis
docker run worker
docker run db
docker run result-app
```

Second method - using DOCKER COMPOSE

```bash 
# docker-compose.yml
services:
    web:
        image: "voting-app"
    cache:
        image: "db"
    messaging:
        image: "worker"
    db:
        image: "db"
    result:
        image: "result-app"
```

To run the entire stack defined in the docker-compose.yml,

```bash
$ docker-compose up 
```

**NOTE: **This is only applicable if you're running multiple containers in a SINGLE DOCKER HOST.

A cleaner and more precise way to create a docker-compose from the same images above


<details><summary> docker-compose.yml </summary>
 
```bash
redis:
    image: "redis"
db:
    image: postgres:9.4
vote:
    # here we're telling it to build the image from the ./vote directory
    build: ./vote
    ports:
        - 5000:80
    # we're linkedin the voting-app container to the redis container
    # note that links may be deprecated now.
    links:
        - redis
result:
    build: ./result
    ports:
        - 5001:80
    links:
        - db
worker:
    image: worker
    links:
        - db
        - redis
```
 
</details>

</details>


<details><summary> Three versions of docker-compose file </summary>

There are 3 versions of a docker-compose file. For v2 and v3, you must specify the VERSION

**VERSION 1**
All containers are attached to the default bridge network and then use LINKS to enable communication between the containers.

```bash
redis:
    image: redis
db:
    image: postgres:9.4
vote:
    image: voting-app
    ports:
        - 5000:80
    links:
        - redis
```
<br>

**VERSION 2**
A dedicated network is automatically created for the application and then attaches all containers to that new network. We can also introduce a "DEPENDENCY" feature where a container can only be started based on a condition

```bash 
version: 2
services:
    redis:
        image: redis
    db:
        image: postgres:9.4
    vote:
        image: voting-app
        ports:
            - 5000:80
        # voting-app is created only when the redis container is started
        depends_on:
            - redis
```
<br>

**VERSION 3**
Almost similar with v2, but this one supports DOCKER SWARM.

```bash 
version: 2
services:
    redis:
        image: redis
    db:
        image: postgres:9.4
    vote:
        image: voting-app
        ports:
            - 5000:80
```

</details>



## Cloud-Native

Another technology that comes to mind when you talk about containers is the concept of cloud-native applications. 

<details><summary> What the heck is Cloud Native? </summary>
 
### So what the heck is Cloud Native?

As defined by [Cloud Native Computing Foundation (CNCF)](https://www.cncf.io/about/charter/) 

> *Cloud native technologies empower organizations to build and run scalable applications in modern, dynamic environments such as public, private, and hybrid clouds. Containers, service meshes, microservices, immutable infrastructure, and declarative APIs exemplify this approach.*
>
> *These techniques enable loosely coupled systems that are resilient, manageable, and observable. Combined with robust automation, they allow engineers to make high-impact changes frequently and predictably with minimal toil.*

In its simplest terms, Cloud native refers to building and managing applications at scale using either private, public, or hybrid cloud platforms.

</details>

<details><summary> Containers and being Cloud Native </summary>

### Containers and being Cloud Native

There are three key things to know here. The first two are **speed** and **agility** - how quickly anorganization can response and adapt to change. 

The third key thing: **containers**.

![](Images/udacity-suse-1-container.png)

To recall, containers are simply **processes** but are treated as the smallest unit of an application. They are closely associated with cloud native applications as containers are a great way to deploy applications quickly and resiliently given their lightweight feature.

Now, when you hear containers, it is also often followed by another buzzword: **microservices**.

![](Images/udacity-suse-1-microservices.png)

This will have its own section but for now, just know that microservices are simply a collection of small, independent, and containerized applications.

</details>

<details><summary> Cloud-Native Landscape </summary>

### Cloud-Native Landscape

With the advent of containers, the need for tools to manage and maintain them also arise. Some of the container orchestrator tools that are being used is the market are:

- Kubernetes,
- Apache Mesos, and 
- Docker Swarm 

Of the three, **Kubernetes** is currently the leading tool in deploying containerized workloads.
 
![](Images/udacity-suse-1-kubernetes.png)

It was a project inside Google and was released in 2014 and is currently being maintained by **CNCF** or **Cloud Native Computing Foundation**, a vendor-agnostic organization that manages open-source projects. The main features of Kubernetes are the automation of:

- Configuration 
- Management
- Scalability

Over time, Kubernetes was developed to include more than just automation but also other functionalities:

- Runtime
- Networking
- Storage
- Service Mesh
- Logs and metrics
- Tracing

</details>

<details><summary> Business and Technical Considerations </summary>

### Business and Technical Considerations

Adoption cloud-native practices means consideration alot of factors, specifically business and technical keypoints, which would need to be assessed by all the stakeholders.

From a business perspective, the adoption of cloud-native tooling represents:

- **Agility** - perform strategic transformations
- **Growth** - quickly iterate on customer feedback
- **Service availability** - ensures the product is available to - customers 24/7

From a technical perspective, the adoption of cloud-native tooling represents:

- **Automation** - release a service without human intervention
- **Orchestration** - introduce a container orchestrator to manage  thousands of services with minimal effort
- **Observability** - ability to independently troubleshoot and debug each component

</details>

## Kubernetes

Adding this section soon!

<p align=center>
    <img src="Images/comingsoon.png" width=500>
</p>