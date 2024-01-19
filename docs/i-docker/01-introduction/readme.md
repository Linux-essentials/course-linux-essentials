# Introduction to Docker

![Docker logo](./img/docker_logo.jpg)

Docker is a tool for running software in an isolated environment. Docker uses a technique called containerization to create separate environments. Docker in its self is nothing new. Docker is just a collection of readily available features that are present in Linux.

Docker makes use of the following technologies to create a system level containerization:

* PID namespace: Process identifiers and capabilities
* UTS namespace: Host and domain name
* MNT namespace: File system access and structure
* IPC namespace: Inter Process communication over shared memory
* NET namespace: Network access and structure
* USR namespace: User names and identifiers
* chroot: Controls the location of the file system root
* cgroups: Resource protection

Docker makes it just more easy to use these technologies. With Docker, users get containers at a much lower cost.

## Containers are not virtualization

Containers provide similar features as virtualization, but with less overhead. Containers share the same Linux kernel, removing the need to provide an OS for every instance. Containers don't add an extra layer. The applications run directly on the hosts system.

![Virtual Machines versus Docker](./img/virtualmachines_vs_docker.png)

Containers offer advantages similar to running applications in virtual machines.

1. Same environment
2. Sandboxed projects (security and conflicting dependencies)
3. It just works (all dependencies are available inside the container)

The isolation that containers provide is not as strict as with virtual machines. Containers are a compromise: separation and sandboxing are not strict, but its enough. Reducing a lot of the overhead makes containerization a viable solution for isolation.

The reduced overhead, and the fact that containers run directly on the host system, result in the fact that containers start up in seconds. Use less resources, and less memory. They are very light weight.