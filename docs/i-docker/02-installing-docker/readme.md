# Installation

## Installing Docker on Raspberry Pi

Start by updating the apt repository and installing the latest packages:

```shell
sudo apt update && sudo apt upgrade
```

Fetch a nice docker installation script and execute it

```shell
curl -sSL https://get.docker.com | sh
```

The script will run with root privileges and will therefore request your password.

To allow the `pi` user (or your own user) to run docker commands, you will need to add the user to the `docker` group:

```shell
sudo adduser pi docker
```

Reboot your Raspberry Pi for all changes to take effect.

### Verifying the Installation

The docker installation can be easily verified by requesting the current installed version:

```shell
docker --version
```

### Running Hello World

DockerHub hosts a `hello-world` docker image that just outputs a hello world message. This image is intended to test your setup.

Do note that the architecture of the Raspberry is ARM, while most images were primarily build for x86. This basically means that most official images will not run on the Raspberry Pi. However, some organizations exists which host images for the ARM architecture such as `arm32v6`, `arm32v7` and `arm64v8`. You may also come across the `armhf` organization, but this one is deprecated in favor the previously mentioned organizations.

```shell
docker run hello-world
```

On successful execution, you should see a similar output"

```text
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
c1eda109e4da: Pull complete
Digest: sha256:2557e3c07ed1e38f26e389462d03ed943586f744621577a99efb77324b0fe535
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (arm32v7)
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

Note how the `hello-world` image was actually pulled from the organization `arm32v7`, which hosts a number of docker images for the ARM architecture compatible with the Raspberry Pi 2. If you are using a Raspberry Pi 3, the image should be pulled from the `arm64v8` organization, since the Cortex-A53 is v8 with a 64-bit instruction set.

More info about Docker on the Raspberry Pi can be found at:

* [https://www.raspberrypi.org/blog/docker-comes-to-raspberry-pi/](https://www.raspberrypi.org/blog/docker-comes-to-raspberry-pi/)
* [https://blog.alexellis.io/getting-started-with-docker-on-raspberry-pi/](https://blog.alexellis.io/getting-started-with-docker-on-raspberry-pi/)

## Installing Docker on Windows

Docker uses Linux-specific kernel features and therefore does not run natively on Windows. Docker Desktop solves this by providing a command line interface to the docker engine running in WSL or a virtual machine equipped with a Linux kernel.

To use docker on Windows, download the Docker Desktop at [https://www.docker.com/products/docker-desktop/](https://www.docker.com/products/docker-desktop/)

### Launching the CLI

To start using the Docker Desktop find the "**Docker Desktop**" and launch it.

You are now ready to start using Docker in the Windows CLI.