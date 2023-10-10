# Docker

## What is Docker?
 - Docker is an open-source software platform that enables developers to build, deploy, run, update, and manage containers.
 - Docker uses OS-level virtualization to deliver software in packages called containers.
 - Docker standardized executable components that combine application source code with
   the operating system libraries and dependencies required to run that code in any *environment*.

---

## Why do we need Docker?
 - Docker enables you to separate your applications from your infrastructure so you can deliver software quickly.
 - Docker's container-based platform allows for highly portable workloads.
 - Docker containers can run on a developer's local laptop, on physical or virtual machines in a data center, on cloud providers, or in a mixture of environments.
 - Docker's portability and lightweight nature also make it easy to dynamically manage workloads, scaling up or tearing down applications and services as business needs dictate, in near real time.

---

## Docker Objects
When you use Docker, you are creating and using images, containers, networks, volumes, plugins, and other objects. This section is a brief overview of some of those objects.

---

### Images
 - An image is a read-only template with instructions for creating a Docker container.
 - Often, an image is based on another image, with some additional customization.
 - For example, you may build an image which is based on the ubuntu image, but installs the Apache web server
 - and your application, as well as the configuration details needed to make your application run.
 - You might create your own images or you might only use those created by others and published in a registry.

An image is like a class. It is just a blueprint with set of instructions to create a container.

|Class       | Image/Dockerfile |
|:----------:|:----------------:|
| Class Car {                    |FROM ubuntu    |
|    private wheels;            | RUN apt-get install nginx |
|    private steering;          |
|    private fuel;              |
|                               |
|    public function move() {   |
|    }                          |
| }                             |


---

### Container
- A container is a runnable instance of an image.
- You can create, start, stop, move, or delete a container using the Docker API or CLI.
- You can create multiple containers using the same image

| Instance | Container |
|:--------:|:---------:|
| $car = new Car() | docker run ubuntu |

```bash
 docker run bash:latest bash -c "date > file.txt && cat file.txt"
```

---

### Network
 - Container networking refers to the ability for containers to connect to and communicate with each other, or to non-Docker workloads.
 - By default, when you create or run a container using docker create or docker run, the container doesn't expose any of its ports to the outside world.
 - Use the --publish or -p flag to make a port available to services outside of Docker.
 - This creates a firewall rule in the host, mapping a container port to a port on the Docker host to the outside world.

---

#### Port
```bash
docker run nginx
docker ps
```

You wont be able to access the nginx server http://localhost

```bash
docker run -p 80:80 nginx
docker ps
```

Now we will be able to access the nginx server via http://localhost:80

```bash
docker run -p 8989:80 nginx
docker ps
```

We can bind different port for host machine.
Docker will act as proxy and serves the data.

Now we wont be able to access via port 80 but http://localhost:8989

---

#### Network Drivers
 - Docker's networking subsystem is pluggable, using drivers. Several drivers exist by default, and provide core networking functionality.
    * bridge // the default network driver
    * host
    * overlay
    * ipvlan
    * macvlan
    * none

```bash
docker run bash bash -c "wget http://localhost:80"
```
Nginx wont be accessible from the bash container.
the localhost refers to the same bash container not the nginx container.
The nginx container has its own ip inside the network

```bash
docker run --network=bridge -d nginx
docker container inspect 'name_of_the_container' | grep IPAddress
docker run --network=bridge bash bash -c "wget http://ip_address_of_the_nginx_container:80"
```

---

### Volume
 - While containers can create, update, and delete files, those changes are lost when you remove the container and Docker isolates all changes to that container.
 - With volumes, you can change all of this.
 - Volumes provide the ability to connect specific filesystem paths of the container back to the host machine.
 - If you mount a directory in the container, changes in that directory are also seen on the host machine.
 - If you mount that same directory across container restarts, you'd see the same files.

 E.g:
 Did you able to get the `file.txt` from the above command

 ```bash
 docker run -it bash:latest bash -c "date > file.txt && cat file.txt && bash"
 docker run -it -w /app -v $(pwd):/app bash:latest bash -c "date > file.txt && cat file.txt && bash"
 ```
 Now you can see the files in this host folder will be available in the containers `/app` directory and vice versa.
 There are different types of volume and different ways to mount a volume to a container.
