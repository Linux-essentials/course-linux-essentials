# Building Images

When building images to create containers from, one could start from scratch; compiling, building, configuring and installing all the dependencies by our self. This would be feasible but tedious task. A better idea is to start *from* an already existing image that provides the basis and build on top of that.

Let's say we wish to create a container that hosts a small web-application, build in NodeJS that uses the express framework.

## A NodeJS app

Start by creating a small hello world application on NodeJS. Do this by creating a project directory on your development host called `hello-docker-from-node`. Best to put this beauty in a git repo with a `README.md` file added to it.

Issue an `npm init` inside the project directory to setup the `package.json` file:

```json
{
  "name": "hello-docker-from-node",
  "version": "1.0.0",
  "description": "Hello World App in NodeJS to demonstrate docker",
  "main": "app.js",
  "scripts": {
    "start": "node app.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [
    "hello",
    "world",
    "docker"
  ],
  "author": "Nico De Witte <nico.dewitte@vives.be>",
  "license": "ISC",
  "repository": {
    "type": "git",
    "url": "git+https://github.com/BioBoost/hello-docker-from-node.git"
  },
  "bugs": {
    "url": "https://github.com/BioBoost/hello-docker-from-node/issues"
  },
  "homepage": "https://github.com/BioBoost/hello-docker-from-node#readme",
  "dependencies": {
    "express": "^4.16.4"
  }
}

```

Next install the express framework by issueing `npm install express --save`.

Add a small hello world app to the application file:

```javascript
const express = require('express');
const app = express();
const port = 3000;

app.get('/', (req, res) => {
  res.send('Hello World!');
});

app.listen(port, () => console.log(`Hello World app listening on port ${port}!`));
```

Test it by running `npm start`. Also surf to `http://localhost:3000` to test it.

## Finding a Base Image

Before starting with building containers, we first need to decide from which image to start from. The closer it matches our dependencies, the less work we'll have.

Many image are available that provide support for Node inside Docker. Many of them could be used. To make it easier to determine an active and supported image, Docker Hub will provide some extra information such as 'stars' and 'pulls'. People can 'star' images that they like. This will give an indication of the popularity. 'pulls' shows the amount of times that the images has been pulled, either to create an new image, or to deploy a container that is based on that image.

Another parameter that could help deciding what image to use is the developer. The developer is the first part before the `/` in the full container name. For example `nodered/node-red-docker` means that the image with name `node-red-docker` is made by the `nodered` developer or team.

Some images don't have a developer name and `/` sign. These images are 'official' images. They are created and maintained by the people of docker. They provide many of the popular solutions. Many times depending on one of these images is preferable.

The best choice in our case would be the official `Node` image. Luckily node is so popular, that it also supported for both `arm32v7` and `arm64v8`. If that was not the case we had to start from an image such as alpine or raspbian and install the dependencies ourselves, or use an unofficial image.

The node image can be found at [https://hub.docker.com/_/node](https://hub.docker.com/_/node). Different versions are available but since we are running this on a Raspberry Pi, its best to go lightweight and pick the alpine based image. A good version (tag) would be `11.11.0-alpine`.

> **INFO** - **TestDrive the Base Image**
>
> You can always test-drive the base image by issueing the command `docker run -it --rm node:11.11.0-alpine sh`, which would spawn a shell. You can check the node version using `node --version` or you can test a simple script. Starting a container with the command `docker run -it --rm node:11.11.0-alpine` would allow you to just enter javascript code.

## The Dockerfile

A `Dockerfile` is human readable text file with a list of steps that describe how to build the image, such as

* configure the system
* copy the application files
* install the required dependencies
* set the working directory
* running commands in the container
* setting environment variables
* exposing ports in the container

Now that the base image is determined, it's time to start building the `Dockerfile`.

### FROM

Add the base image to the Dockerfile using the `FROM` keyword, followed by the name of the image and the tag we would like to start from.

```dockerfile
# The base image to start from
FROM node:11.11.0-alpine
```

Comments can be added using hashtags `#`.

Now we have an Alpine Linux image with NodeJS installed.

### WORKDIR

Next we can set a default application directory using the `WORKDIR` instruction followed by a path. It sets the **working directory** for instructions that follow. If the directory does not exist yet, it will be created.

```dockerfile
# Setup a working directory for our app
WORKDIR /app
```

### COPY

At this point we are ready to copy all necessary application files into the docker image.

This can be achieved with the `COPY` keyword. The keyword accepts two arguments. The source of the files on our computer, and the destination in the Docker image. The simplest option here would be to copy all files using the instruction shown below:

```dockerfile
# Copy the application files
COPY . .
```

The source is the current directory `.` and the destination is the current directory `.` inside the image. This is the `WORKDIR`.

There are however some caveats here. Certain files and directories don't need to be added to our image. Some examples are the `.git` directory and the `node_modules` directory (this one needs to be downloaded using the `npm install` command instead).

To make this process easier, docker supports a `.dockerignore` file which can be added to the project. The files and directories listed in this file are ignore for certain instructions.

Add a `.dockerignore` file to your project with the following content:

```text
node_modules/
.git/
.gitignore
Dockerfile
.dockerignore
```

### RUN

The `RUN` instruction allows us to execute instructions in a shell. This is for example used to install applications, libraries or other dependencies. Each `RUN` instruction will execute any commands in a new layer on top of the current image and commit the results

So the `RUN` instruction can be used to instruct `npm` to install or dependencies.

```dockerfile
# Install the node modules
RUN npm install
```

### EXPOSE

The goal of docker is to isolate software as much as possible. This means that TCP/IP traffic is unable to get in or out of the container. This can be solved by `EXPOSE`ing a TCP or UDP port. This will open up the port on the container, making communication with the outside world and vice versa possible.

Our node application runs on port `3000` to serve HTTP traffic. This means that we need to open up this port in the container. This can be done with the following command:

```dockerfile
# Expose port 3000 from node
EXPOSE 3000
```

If a protocol different from TCP is used, you can specify the protocol following the format `EXPOSE <port>/<udp,tcp>`. If no protocol is specified, than it **defaults to tcp**.

Do note that the `EXPOSE` instruction does not actually publish the port - this needs to be done when running the container.

### CMD

Last but not least we must tell docker what process to start for our container. This can be achieved using the `CMD` instruction. It specifies the application or **executable process that needs to be run** in the container when started. Do note that only a single `CMD` instruction can be specified within a Dockerfile.

```dockerfile
# The final command that starts the app
CMD ["npm", "start"]
```

Note that the `CMD` instruction requires all arguments to be listed separately.

### Final Result

The final result should then be:

```dockerfile
# The base image to start from
FROM node:11.11.0-alpine

# Setup a working directory for our app
WORKDIR /app

# Copy the application files
COPY . .

# Install the node modules
RUN npm install

# Expose port 3000 from node
EXPOSE 3000

# The final command that starts the app
CMD ["npm", "start"]
```

## Building the image

The `Dockerfile` only describes how an image is created. The next step is to build the image. Building the image will execute the commands in the `Dockerfile`.  Building the image needs to be done with the `docker build` command in the terminal.

The docker build command needs some extra information to be able to build the image correctly.

The first argument is a tag. This can be provided with the `-t` or `--tag` argument followed by the name of the tag. Lets tag our image with the name `hello-docker`. The last argument should be the directory where the `Dockerfile` is located. In our case this is the current directory and can be notated with a dot `.`.

```shell
docker build -t hello-docker .
```

If all goes well, the output should be similar to:

```shell
Sending build context to Docker daemon  2.003MB
Step 1/6 : FROM node:11.11.0-alpine
11.11.0-alpine: Pulling from library/node
8e402f1a9c57: Pull complete
5e2195587d10: Pull complete
6f595b2fc66d: Pull complete
Digest: sha256:6da4878fc63b98ef5fde771b1f05fec9c796e49d249816fe8d544f336ae89d80
Status: Downloaded newer image for node:11.11.0-alpine
 ---> a13f3a3ed57f
Step 2/6 : WORKDIR /app
 ---> Running in 6fe448759b36
Removing intermediate container 6fe448759b36
 ---> f5e47465ac6f
Step 3/6 : COPY . .
 ---> 7898fbd309fa
Step 4/6 : RUN npm install
 ---> Running in dd584dc9f9c9
audited 121 packages in 1.44s
found 0 vulnerabilities

Removing intermediate container dd584dc9f9c9
 ---> a5119d82476c
Step 5/6 : EXPOSE 3000
 ---> Running in fe4accfe9097
Removing intermediate container fe4accfe9097
 ---> 9b4c6e3d9fa0
Step 6/6 : CMD ["npm", "start"]
 ---> Running in 6af090ec06d4
Removing intermediate container 6af090ec06d4
 ---> 4bc117519d32
Successfully built 4bc117519d32
Successfully tagged hello-docker:latest
```

First we can see that Docker will download (pull) the `node:11.11.0-alpine` image from the internet. Next it will execute the commands provided in the `Dockerfile`. The result is a new images called `hello-docker`.

### The hello-docker image

You can list the installed images using the following command:

```shell
docker images
```

This command will list all installed images on the computer. The `hello-docker` image should be visible in that list.

```shell
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-docker        latest              76b45fcd8c1e        2 minutes ago       77.5MB
```

You have now created your own custom Docker image.

## Running a Container

Running a container from this image is pretty straightforward now.

To run an interactive container that stops when pressing CTRL-C:

```shell
docker run -it --rm --name hello-from-node -p 3000:3000 hello-docker
```

<!-- If you create container using `docker run --name hello-from-node -p 3000:3000 hello-docker` and press CTRL-C, you will detach the TTY, but the container will keep running. -->

Note that the port needs to be mapped to a port on the host machine, so it can be accessed from outside of the docker environment.

To run a detached container:

```shell
docker run -d --name hello-from-node -p 3000:3000 hello-docker
```

<!-- ## Optimizing the Dockerfile

Note that the order of the instructions does play a significant role when creating a `Dockerfile`. During the process of building an image Docker steps through the instructions in your `Dockerfile `executing each in the order specified. As each instruction is examined, Docker looks for an previous generated intermediate image in its cache that it can reuse, rather than creating a new \(duplicate\) image. This means that your build stage layers \(created by most `Dockerfile `instructions\) should be ordered from the less frequently changed to the more frequently changed allowing maximum use of cached images, and speeding up building times immensely. -->

<!-- So we should refactor the dockerfile a bit to optimize it. -->

## Common Dockerfile Instructions

The table below lists the most common used instructions for a Dockerfile.

| Instruction | Details |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `FROM` | When creating a Dockerfile it should always specify **on which image it should be based**. This is achieved using the `FROM` instruction. A common source of images to base your image on is Docker Hub. Specify a specific tag using a colon `:<version_number>`use the latest image using `:latest` |
| `WORKDIR` | Sets the **working directory** for instructions that follow. If the directory does not exist yet, it will be created. |
| `RUN` | Allows the **execution of commands in the shell**. This is for example used to install applications, libraries or other dependencies. Each `RUN` instruction will execute any commands in a new layer on top of the current image and commit the results |
| `ENV` | Sets an **environmental variable **to a specified value. These **will persist **when a container is running. Environment variable are used to inject variables in the container that can be used for any reason. eg: setting server settings, user accounts,.... |
| `COPY <src> <dst>` | **Copies files and directories** from the host file system to the image. Relative paths can be used for both the source and the destination. Relative paths in the destination are relative to the working directory. |
| `EXPOSE <port>/<udp,tcp>` | Indicates that the **container must open the specified ports** when running. If no protocol is specified, than it **defaults to tcp**. Do note that the `EXPOSE` instruction does not actually publish the port - this needs to be done when running the container. |
| `CMD ["executable","arg1","arg2"]` | Specifies the application or **executable process that needs to be run** in the container when started. Only a single CMD instruction can be specified within a Dockerfile. |