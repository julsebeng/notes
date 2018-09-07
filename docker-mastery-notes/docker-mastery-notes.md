# Docker Mastery Notes

## Table of Contents
- [Docker Versions and Releases](#docker-versions-and-releases)
- [Docker on Windows](#docker-on-windows)
- [Docker on Linux](#docker-on-linux)
- [Checking a Docker Installation and Config](#checking-a-docker-installation-and-config)
- [Managing Containers](#managing-containers)
- [Using `docker-machine`](#using-docker-machine)

---
### Docker Versions and Releases
#### CE vs. EE
The Community Edition is free, Enterprise Edition is a premium solution.

EE is certified on specific versions of Linux and Windows, while CE is tested on a larger variety of platforms.

#### Stable vs. Edge
CE Edge releases are monthly beta releases, and are only supported for the month until the next Edge release comes out.
CE Stable releases come out once a quarter, and are supported until the next release.

EE releases are supported for much longer past the release of newer versions.

Note that the versioning format for Docker changed in early 2017 - the format is now `YY.MM` of the release of the update.
The old version numbers looked like `1.13.1` while the current ones look like `17.03.0`.

---

### Docker on Windows
- Windows supports both Linux containers and Windows containers.
- Win10 Pro/Ent supports the use of **Docker for Windows**, but the Home version and older Windows editions are unsupported;
  use **Docker Toolbox** instead.
- Windows Server 2016 also supports Windows containers.

#### Docker on Windows
- Utilizes Hyper-V to spin up a tiny Linux VM for Linux containers.
- Powershell native: should be interacting with it via Powershell.

#### Docker Toolbox
- Utilizes a VirtualBox VM to spin up a tiny Linux VM and manages it with the `docker-machine` command-line tool.
- Uses the Docker Quickstart Terminal shell by default in this machine.
- Doesn't support Windows containers, only Linux.

##### Setup and Tips
1. Download Docker Toolbox from https://store.docker.com
2. Install Toolbox with all components.
3. Launch **Docker Quickstart Terminal** to spin up the VirtualBox Linux ISO at least once, so that the VMs are configured automatically.

Since Docker Toolbox uses VirtualBox in the background, all the settings for that machine can be changed to suit your needs
by launching VirtualBox and locating the "default" machine that's created automatically. `docker-machine` can also be used
to adjust settings.

Code paths do work in the Toolbox, however they can only be made inside `C:\Users`. Different drive paths *do not work*.

To use a terminal like Cmder with Docker:
1. Run `docker version` in Cmder - you should see an "Error during connect..." message. This is because Docker is running
   inside the VM and not on Windows natively.
2. Use `docker-machine env default` to produce environment variables for the "default" VM.
3. Set those variables, either manually, or by running the command that `docker-machine env default` gives you with its output.

#### Docker on Windows Server 2016
- Supports native Windows containers: no Hyper-V or VirtualBox right out of the box.
- Also supports Docker on Windows but this is more a tool for local development, rather than a production tool.
- Previous versions of Windows Server do not support any version of Docker.
---
### Docker on Linux
There are 2 preferred ways to install Docker on Linux:
1. Via a script provided by Docker: `curl -sSL https://get.docker.com/ | sh`
    - This will install the latest Edge release
2. Via instructions provided by store.docker.com
    - Note that Docker for RHEL only supports the EE version officially; you can still install the CE version for CentOS, however.

Note that your user can be added to the "docker" group: `sudo usermod -aG docker <username>`; this allows Docker to
run some things as sudo without prompting for a password every time. Some distros don't even allow you to perform this action,
such as RHEL and CentOS, due to security concerns.

You can run `docker-machine version` and check to see if there are any "permission denied" errors if you suspect permissions are
causing problems with Docker.

After Docker proper is installed, there are a few other tools that are needed:
- `docker-machine`: docs.docker.com/machine/install-machine
- `docker-compose`: docs.docker.com/compose/install
- Note that the versions installed from these pages may still be behind the releases on GitHub, check there first to see what the
  actual newest version of these tools is.

Note that the Docker daemon may need to be spooled up: `sudo systemctl start docker` or equivalent syntax should work. A simple
way to verify this is to try `docker container run hello-world`.

---
### Checking a Docker Installation and Config
- `docker version`: lists version of installed Docker client (CLI) and server (engine).
    - Getting info from the server verifies that the connection is established and working.
- `docker info`: gives even more detail about the client and server configurations.
- `docker`: on its own, returns a list of most of the available Docker commands.

The `manager` sub-command: if you look at the output of the `docker` command, you'll see a separate section for "Management
Commands". These commands are invoked with the syntax:
```
New:
docker <command> <sub-command> ...
$ docker container run

Old:
docker <sub-command> ...
$ docker run
```
Note that the old way is still valid; if backwards compatibility is a concern, use the old syntax if it exists.

#### Starting an Nginx Web Server
To test the capabilities of Docker, you can spin up a container of an Nginx web server image.

A note about Docker terminology:
- An **image** is an application that is runnable.
    - An image is pulled from a **registry**, which is like a repository for Docker images. Docker uses Docker Hub
      (hub.docker.com)
- A **container** is a running instance of an image.
    - Many containers can be run off of one image.

To spin up an Nginx server, run `docker container run --publish 80:80 nginx`.
- The old, equivalent syntax would be `docker run ...`
- `--publish 80:80`: forwards traffic recieved on the local port 80 to the container's port 80.
- `nginx`: Docker will look for an Nginx image locally; if it can't find one, it will download one from Docker Hub automatically.

---
### Managing Containers
`docker container run` will spin up a new container of the specified image. In the background, Docker will first search for the
requested image locally, moving on to Docker Hub by default if it's not found. When that container is created, instead of
copying the image, Docker instead creates a new layer of changes on top of that image's current state. The container is then
assigned its own virtual IP inside the Docker engine's private network, and any published ports are subsequently opened.
- By default the latest version of an image is downloaded; a version can be specified as so: `<name>:<version>`,
i.e. `docker container run nginx:5.3.2`
- By default the output of the container will appear in the foreground; the `--detach` command will run the container in the
  background, and returns its unique container ID.
    - To view the output of a detached container, use `docker container logs <name>` (old: `docker logs`)
- A container can be stopped with `docker container stop <container-id>`; note that the container ID only has to be enough symbols
  as is necessary to uniquely identify the target container.
- A stopped container can be restarted with `docker container start`, **not** `docker container run`. The latter will spin up
  a new container with a different container ID.
- A new Docker image will be assigned a random name if one isn't given with the `--name` flag.
- `--env` can be used to pass an environment variable to a container.

`docker container ls` will list all running containers.
- The old syntax is `docker ps`
- By default this command only lists currently running containers.
    - The `-a` flag will list all containers.

`docker container top <name>` will show the processes running inside of a particular container.

`docker container rm <id>` will remove a container of the specified ID; multiple space-separated IDs can be given.
- By default, running containers cannot be deleted; the `-f` flag will override this behavior.

---
### Using `docker-machine`
- `docker-machine ls` will show a list of currently installed Docker VMs.
- `docker-machine start` will start a Docker VM.
- `docker-machine env <name of machine>` will spit out environment variables that need to be set for a terminal to interact with
  the machine in question.