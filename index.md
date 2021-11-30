---
label: How to install and start using Docker on Ubuntu 20.04
tags: [tutorial, docker, ubuntu 20.04]
author:
  name: Matej
  email: matej.matkuliak@gmail.com
date: 2020-11-30
icon: rocket
---

![](docker.jpg)
[!badge variant="info" text="Tutorial"]

Introduction
----

Docker is an open-source platform used for easier development, shipping and running of applications. With Docker, you can separate applications from your local environment and infrastructure. Docker provides the ability to package and run an application in a loosely isolated environment called a container. Containers allow applications to run in a resource-isolated environment. You can imagine containerized design similar to a virtual machine, but containers are more resource-friendly and portable.

Step 1 - Installation
----

We’ll install Docker from the official Docker repository to ensure we get the latest version. To do that, we’ll add a new package source, add the GPG key from Docker to ensure the downloads are valid, and then install the package.

Per best practice, we start the install process by updating our existing packages:

```bash
sudo apt update
```

Next we install prerequisite packages that allow [!badge variant="secondary" text="apt"] use packages over HTTPS:

```bash
sudo apt install apt-transport-https ca-certificates curl software-properties-common
```

Next, add the GPG key for the official Docker repository to your system:

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

Add the Docker repository to [!badge variant="secondary" text="apt"] sources:

```bash
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
```

This will also update our package database with the Docker packages from the newly added repo.

Make sure you are about to install from the Docker repo instead of the default Ubuntu repo:

```bash
apt-cache policy docker-ce
```

You should see something like this as an output, although your version number may be different:

``` output
docker-ce:
  Installed: (none)
  Candidate: 5:20.10.11~3-0~ubuntu-focal
  Version table:
     5:20.10.11~3-0~ubuntu-focal 500
        500 https://download.docker.com/linux/ubuntu focal/stable amd64 Packages
```

You can see that docker-ce is not yet installed, but the candidate for installation is from the Docker repository for Ubuntu 20.04 (focal).

Now we install Docker:

```bash
sudo apt install docker-ce
```
 

Docker should now be installed, the daemon started, and the process enabled to start on boot. You can check whether it's running by issuing:

```bash
sudo systemctl status docker
```
 

The output will show you that the service is active and running:

``` output
● docker.service - Docker Application Container Engine
     Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2021-11-30 16:50:58 CET; 15s ago
TriggeredBy: ● docker.socket
       Docs: https://docs.docker.com
   Main PID: 7488 (dockerd)
      Tasks: 7
     Memory: 26.9M
     CGroup: /system.slice/docker.service
             └─7488 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
```

Step 2 - Adding user to Docker group
----

After installation, by default the [!badge variant="secondary" text="docker"] command can only be run by the **root** user or by a user in the **docker** group. This means that when you attempt to run a [!badge variant="secondary" text="docker"] command without **sudo** prefix, you will be met with a warning like this:

``` output
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: 
Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.24/containers/json": dial unix /var/run/docker.sock: connect: permission denied
```

If you don't want to use **sudo** with every [!badge variant="secondary" text="docker"] command, you need to add your username to the docker group:

```bash
sudo usermod -aG docker ${USER}
```

To apply you can log out and log back in, or issue the following:

```bash
su - ${USER}
```

You can confirm that your user is in the **docker** group with this command:

```bash
groups
```

``` output
matej sudo docker
```
Now you can use [!badge variant="secondary" text="docker"] command freely without needing **sudo**.

Step 3 - Using Docker
---

Similarly to command structure of most tools on Ubuntu, Docker command consists of [!badge variant="secondary" text="docker"] prefix followed by options, commands and arguments:

```bash
docker [option] [command] [arguments]
```

To see all commands, just issue [!badge variant="secondary" text="docker"]:

```bash
docker
```

Along with other useful info, you will get a list of all main commands:

``` output
Commands:
  attach      Attach local standard input, output, and error streams to a running container
  build       Build an image from a Dockerfile
  commit      Create a new image from a container's changes
  cp          Copy files/folders between a container and the local filesystem
  create      Create a new container
  diff        Inspect changes to files or directories on a container's filesystem
  events      Get real time events from the server
  exec        Run a command in a running container
  export      Export a container's filesystem as a tar archive
  history     Show the history of an image
  images      List images
  import      Import the contents from a tarball to create a filesystem image
  info        Display system-wide information
  inspect     Return low-level information on Docker objects
  kill        Kill one or more running containers
  load        Load an image from a tar archive or STDIN
  login       Log in to a Docker registry
  logout      Log out from a Docker registry
  logs        Fetch the logs of a container
  pause       Pause all processes within one or more containers
  port        List port mappings or a specific mapping for the container
  ps          List containers
  pull        Pull an image or a repository from a registry
  push        Push an image or a repository to a registry
  rename      Rename a container
  restart     Restart one or more containers
  rm          Remove one or more containers
  rmi         Remove one or more images
  run         Run a command in a new container
  save        Save one or more images to a tar archive (streamed to STDOUT by default)
  search      Search the Docker Hub for images
  start       Start one or more stopped containers
  stats       Display a live stream of container(s) resource usage statistics
  stop        Stop one or more running containers
  tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
  top         Display the running processes of a container
  unpause     Unpause all processes within one or more containers
  update      Update configuration of one or more containers
  version     Show the Docker version information
  wait        Block until one or more containers stop, then print their exit codes

Run 'docker COMMAND --help' for more information on a command.
```

Step 4 - Docker images and containers
----

Containerized applications are built from Docker images. These images are by default downloaded (pulled) from Docker Hub [Docker Hub](https://hub.docker.com/). Docker Hub is the official repository that allows anyone to store Docker images here.

Let's try to create a container from an image:

```bash
docker run hello-world
```

``` output
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
2db29710123e: Pull complete 
Digest: sha256:cc15c5b292d8525effc0f89cb299f1804f3a725c8d05e158653a563f15e4f685
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

With fresh docker install, you will get output similar to the one above. You can see that Docker was initially unable to find image **hello-world** locally. Because of that, it looked it up in the Docker Hub repository where it found and pulled the image from. From this image, it created a container in which the sample application was executed.

You can look for the image with [!badge variant="secondary" text="search"] command:

```bash
docker search hello-world
```

```bash output
NAME                                       DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
hello-world                                Hello World! (an example of minimal Dockeriz…   1589      [OK]  
```
You will see that there is a **hello-world** image in the Docker Hub repository. The **[OK]** in the **OFFICIAL** column means that this is a verified, official image.

We can now check the details of **hello-world** container that we created from the image that we pulled.

```bash
docker ps -al
```

!!!
We use **-a** flag/option to see exited containers (by default we only see the running ones) and a **-l** for better visibility.
!!!

```bash output
CONTAINER ID   IMAGE         COMMAND    CREATED         STATUS                     PORTS     NAMES
a79d7c741dcf   hello-world   "/hello"   6 minutes ago   Exited (0) 6 minutes ago             nostalgic_payne
```
We see that the image-hello world was successfully used to create a container with id **a79d7c741dcf** and destroyed it when the application finished.

You can list your images with:

```bash
docker images
```

```bash output
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    feb5d9fea6a5   2 months ago   13.3kB
```

Currently, we only have one image present in our system.

If we want to remove that image, we must first remove the container that was created by this image:

```bash
docker container rm nostalgic_payne
```

!!!
nostalgic_payne is the name automatically given to the container, yours will be different. To find out what it is, you can use the before-mentioned **docker container ls -al** command.
!!!

Now that the underlying container is removed, we can remove the image. The name of the container must also be followed by image tag:

```bash
docker image rm hello-world:latest
```

```bash output
Untagged: hello-world:latest
Untagged: hello-world@sha256:cc15c5b292d8525effc0f89cb299f1804f3a725c8d05e158653a563f15e4f685
Deleted: sha256:feb5d9fea6a5e9606aa995e879d862b825965ba48de054caab5ef356dc6b3412
Deleted: sha256:e07ee1baac5fae6a26f30cabfe54a36d3402f96afda318fe0a96cec4ca393359
```

If we now issue **docker container ls -al** and **docker images -a** we see that there are no images and containers present.


Conclusion
----

In this Docker introductory tutorial, you learned how to install and use Docker. You pulled a sample application image, created a container from this image and then cleaned after yourself by removing existing containers and images. In the next part, we will edit an image and show you how to push it to your own Docker Hub repository.