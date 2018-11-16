# Docker Mastery Notes

## Table of Contents
- [Docker Versions and Releases](#docker-versions-and-releases)
- [Docker on Windows](#docker-on-windows)
- [Docker on Linux](#docker-on-linux)
- [Checking a Docker Installation and Config](#checking-a-docker-installation-and-config)
- [Managing Containers](#managing-containers)
- [CLI Process Monitoring](#cli-process-monitoring)
- [Getting a Shell to a Container](#getting-a-shell-to-a-container)
- [Docker Networks](#docker-networks)
- [Docker Images](#docker-images)
- [Persistent Data](#persistent-data)
- [Docker Compose](#docker-compose)
- [Using Swarm](#using-swarm)
- [Using `docker-machine`](#using-docker-machine)
- [Miscellaneous Notes](#miscellaneous-notes)


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
-  be downloaded but not run with `docker pull <name>`
- By default the output of the container will appear in the foreground; the `--detach` command will run the container in the
  background, and returns its unique container ID.
    - To view the output of a detached container, use `docker container logs <name>` (old: `docker logs`)
- A container can be stopped with `docker container stop <container-id>`; note that the container ID only has to be enough symbols
  as is necessary to uniquely identify the target container.
- A stopped container can be restarted with `docker container start`, **not** `docker container run`. The latter will spin up
  a new container with a different container ID.
- A new Docker image will be assigned a random name if one isn't given with the `--name` flag.
- `--env` can be used to pass an environment variable to a container.

`docker container ls` will list all running containers. `docker image ls` will list all images available on the system. Note that
`container ls` may list a single image multiple times, if it has been referred to by different tags - if multiple images have the
same Image ID, they all refer to the same file on disk, and are not duplicates.
- The old syntax is `docker ps`
- By default this command only lists currently running containers.
    - The `-a` flag will list all containers.

`docker container top <name>` will show the processes running inside of a particular container.

`docker container rm <id>` will remove a container of the specified ID; multiple space-separated IDs can be given.
- By default, running containers cannot be deleted; the `-f` flag will override this behavior.
- `docker container run --rm` will have a container started, then removed automatically once it's done running.

### CLI Process Monitoring
Useful commands for gathering information about a container:
- `docker container top`: process list of the container.
- `docker container inspect`: details of the container configuration.
  - Provides metadata on how the container was started; doesn't provide info on the active container.
- `docker container stats`: performance stats for all containers
  - can use `docker container stats <name>` to get info on just a specific container.
  - Provides realtime monitoring of performance metrics of containers.

---

### Getting a Shell to a Container
- `docker container run -it`: run a container interactively.
  -`-i`: keeps STDIN open even if not attached to the container.
  -`-t`: gives a sudo-TTY to the container.
  - To start a container interactively, use `-ia` instead of `-it`.
- `docker container exec -it`: run a command in addition to the default command a container starts with. The container must already
  be running.
- Can specify a command and args after the name of the image is specified when using `docker container run`
  - E.x. `docker container run -it --name jaro nginx bash` will launch a container from the nginx image and start an interactive
    shell.
    - You will be root in the container, even if you're not root on the host.
  - Note that once you exit the shell the container will **stop**.
  - If you use `docker container ls`, you can see what command a container was started with. *A container will only live as long
    as the command it was started with is running.*
    - use `docker container exec` instead to run a command in addition to the command the container starts with.
      - E.x. `docker container run -it mysql bash` will start up MySQL, *as well as* run bash. You can exit bash and the container
        will still run.
  - If you modify a container in some way, such as using an `apt` command to install software, it will only affect *that
    container*, not the original image!

---

### Docker Networks

A simple way to expose a port in a docker container is `docker container run -p`. Typically when testing and developing locally,
Docker networking just works.

- `docker container port <container>`: list exposed ports in a container.
- When a container is started, it is connected to a particular Docker network in the background - the "bridge" network by default.
  - Each virtual network routes through a NAT firewall on the host IP to the rest of the network/internet.
  - All containers on the same virtual network can talk to each other, even if `-p` is not used to expose any ports.
    - As such it's best practice to create each container with its own virtual network.
- `docker container port <name>`: get port of the given container.
- `docker container inspect --format '{{ .NetworkSettings.IPAddress }} <name>`: get the IP of the given container.

#### How Docker Virtual Networks Work
  - Note that, by default, the IP of the container and the IP of the host are not on the same subnet.
    - The host machine the computer runs on will have its own NAT layer and firewall that container traffic will be routed out of.
    - Docker also has virtual networks ("bridge" by default) which its containers attach themselves to.
    - By default these virtual networks are attached to the host's network interface so that packets can come and go.
    - The command `-p 80:8080` will open up port 80 on the host's network interface and forward anything coming in through the
      virtual network to the container on port 8080.
    - If `-p` is never specified, containers in the same virtual network can communicate with each other, but traffic cannot leave
      this network through the host's network adapter.
      - This is useful since you can have a web server expose its port to the host adapter, but have its backend database not do
        so; incoming traffic can still be sent to the web server, and it can still talk to its backend if they are on the same
        virtual network, but the database is not exposed to the outside directly.

#### Managing Docker Virtual Networks
- `docker network ls`: list Docker networks.
  - "bridge" (or "docker0" depending on the version of Docker): the default virtual network.
  - "host": a special network that bypasses Docker virtual networks and attaches the container directly to the host's interface.
  - "none": removes the "eth0" interface in the container and only provides a localhost interface to it.
- `docker network inspect`: list details on a specific network in JSON format.
- `docker network create`: create a new virtual network.
  - `--driver`: create a new network using a specific built-in or 3rd-party driver.
  - `docker network create <name of interface>`: creates a new virtual network that uses the bridge driver by default.
  - `docker container run --network <name of interface> <name>` will assign the network to a container when it's created.
- `docker network connect` & `docker network disconnect`: create/destroy a NIC on a virtual network for a running container.
  - `docker network connect <name of interface> <name>` to connect an already-running container to a virtual network.
  - Note that this container will be connected to the new network **in addition to its previous networks**.

#### DNS in Docker
Since IP addresses of containers are so dynamic, you can't trust them to always be the same. Since containers are being created,
destroyed, expanded, shrunk, etc in a large-scale environment, it's a bad idea to rely on individual IP addresses (static IPs
should be avoided).
- The docker daemon has a built-in DNS server that containers will use by default, *unless they are on the default bridge
  network* - this network does not have the DNS server features.
  - By default, Docker will use the container names as the equivalent of a hostname.
  - `docker container exec -it <name of container> ping <name of other container>` to test connectivity between two running
    containers in the same virtual network.
  - Note that even though the default "bridge" network doesn't support DNS, the `--link` flag can be used when creating containers
    in that network, which allows you to explicitly tell a container to link to another container.
    - It's easier to just create a new virtual network.

#### Implementing Round-Robin in Docker
- Round-robin is a technique used to have multiple servers with different IPs respond to the same DNS name.
- `--net-alias <aliased DNS name>`: flag to use when creating a container to create an alias to the given DNS name for the
  container. **Note that this flag only works inside of a user-created virtual network.**
  - E.x. `docker container run --net user_network --network-alias my_alias alpine`
- `nslookup my_alias` can be used to validate that multiple containers are aliased to the same DNS record (this has to be done from
  a container on the same network, of course).

---

### Docker Images
A Docker image is a set of application binaries and dependencies, and metadata on the image data and how to run it.
Officially, a Docker image is "an ordered collection of root filesystem changes and the corresponding execution parameters
for use within a container runtime." A Docker image does *not* contain a complete OS - no kernel, drivers, etc.

#### Image Layers
Images are designed around the Union File System concept - changes are made in layers.
- `docker history <image>` shows the history of changes made on a Docker image. The first layer is always the scratch layer,
  and all subsequent layers represent changes that were made to the file system (through Docker hub updates, etc).
- Changes to images are cached, so changes made to an image are saved, and if the same action ever needs to be done to that image,
  Docker will just refer to the cached result.
- If a file is changed inside of a container, the storage driver utilizes the concept of **copy-on-write**: files that are
  changed by a container will be copied into that container. When you're looking at the file system of a container, you're actually
  seeing layers of changes being stacked on top of each other by the storage driver, with the most recent change to each file
  being what is actually seen in the file system.

#### Pushing and Tagging Images
- `docker image pull <name>`: push changes made to a local image to a repository (Docker Hub by default).
  - May have to use `docker login` before you can push to the Hub.
    - **Note: logging in will automatically create an authentication key on the local machine; be sure that it's gone if you don't want
      this machine to have access to your repositories.**
  - Pushed repositories are public by default; if you want a private repo you should create it in Hub *first*, then upload the image.
- `docker image tag <name of image> <name of image>:<name of tag>`: give a local image an additional tag.
  - Note that even if an image has a different tag, if one of its layers already exists as a part of another tag, it will use that instead
    and not upload it again.
  - `docker image tag my_image:latest jse13/my_image:latest` to create a new tag and name for an existing image.

#### Creating Docker Images
Docker files specify how a Docker image is configured. These files (the default name is always `Dockerfile`) are composed of certain keywords, called
stanzas.

Note that each one of these stanzas will end up being its own layer inside of an image, so the order they're written in matters. The layers are also
cached, so if the layer has been run before it won't run again if the image is built again (unless that stanza has been changed). *Every subsequent
line after the change must be rebuilt as well*, so keep commands that change frequently near the bottom of the Dockerfile.
- `FROM <some distribution>`: build the Docker image on top of this distribution - typically this will be Alpine.
  - E.x. `FROM debian:jesse`
  - These distributions are much more minimal than their proper images.
  - Note that the current Dockerfile will inherit everything inside of the `FROM` image's Dockerfile. In this way, images can be chained together.
- `ENV <environment variable> <value>`: set an environment variable inside of the image
  - E.x. `ENV NGINX_VERSION 1.11.0~Jesse`
  - Because each stanza is its own layer, if an environment variable is set subsequent commands will be able to reference it.
- `RUN <command>`: run the specified command in the image.
  - E.x. `RUN apt-get update`
  - You will usually have commands that perform one well-defined task chained together so that they're saved as the same layer.
    - E.x. `RUN apt-get update && apt-get upgrade && apt-get install vim`
  - Since Docker expects log output to be on STDOUT and STDERR, a common stanza to see is something like
  ```
  RUN ln -sf /dev/stdout /var/log/nginx/access.log \
      && ln -sf /dev/stderr /var/log/nginx/error.log
  ```
- `EXPOSE <port>`: expose containers on the given ports in the virtual network that they're assigned.
  - E.x. `EXPOSE 80 443`
  - Note: this does not expose the images to the host's network adapter, a.k.a. it does not replace `-p` when a container is created. This
    exposes the ports to the virtual network *only*.
- `CMD [<name>,<flags>,<other args>]`: required stanza that describes the command that should be run when a new container is created.
  - E.x. `CMD ["nginx","-p","daemon off;"]`
  - Note: if there is a `CMD` present in the image sourced in `FROM`, you don't have to define your own.
- `WORKDIR <path>`: change working directory inside of the image.
  - `RUN cp <path>` would accomplish the same thing, but using `WORKDIR` is best practice.
- `COPY <source in host> <target in image>`: copy files from the host machine into the image.
- `ARG var=value`: specify a build argument that can be passed in via command-line, that defaults to `value`.
    - This can be used in following stanzas as `${var}`.

#### Building Docker Images
- `docker image build <file path>`: build the Dockerfile at the given location; note that if the name `Dockerfile` is not used, the `-f` flag can
  be used to explicitly give the filename.
    - `-t <name>`: specify a name for the image that will be created.
      - if no tagname is given, the image will be given "latest" by default; a tag can be specified like: `-t <name>:<tagname>`

#### Extending Official Images
- To extend existing images, you can use `FROM <some image>` to base your changes off of some existing image, allowing you to
  extend its functionality to meet your needs.
    - Required stanzas, like `CMD`, will be inherited from the base container, so its not necessary to provide these in the new  
      configuration.

---

### Persistent Data
- "immutable infrastructure": once a container is running, it shouldn't change; changes must be redeployed.
    - Good design choice, but what about persistent data? Unique data, such as databases, need to persist even as containers
      are updated and redeployed.
    - The Docker solution is the "separation of concerns"; persistent data should be completely separated from the application
      binaries.
- Containers that are created and changed will remain changed when stopped, but the images they're based on do not change with
  them.
- Docker uses **volumes** and **bind mounts** to solve this problem.
    - Volumes: configuration option for a container that creates a special location outside of the container UFS to store
      persistent data; this is transparent to the container.
    - Bind Mounts: links a container path to a host path.

#### Data Volumes
##### Creation
- Volumes can be created a couple ways.
    - `VOLUME <path>` stanza in a Dockerfile.
        - This creates a new volume and assigns it to `<path>`.
    - Specifying on run with the `-v`.
        - `-v /path/inside/container` will create an unnamed volume mounted at the path given.
        - `-v my_volume:/path/inside/container` will create a named volume mounted at the path given; this makes it more
          identifiable when listing existing volumes on the host, when inspecting mount information, etc.
    - `docker volume create`
        - This is the only way to create a volume with customizable driver options.

##### Interacting with Volumes
- `docker image inspect` will reveal any images attached to a container.
    - Under the "Volumes" object.
    - If a container is running, `docker container inspect <name>` will have a "Mounts" object that describes the host path
      that is mapped to the volume path inside the container.
        - Note that for MacOS and Windows, because this host directory is technically stored inside of a Linux VM, it isn't
          accessible from the host.
- Volumes have to be removed manually.
    - `docker volume prune` will delete unused volumes.
- `docker volume ls` will list existing volumes.
    - This will list all volumes; there is no `-a` flag.
    - `docker volume inspect <name>` will reveal more information about a volume.

#### Bind Mounting
- Maps a host directory into a container directory.
- Skips the UFS, just like volumes.
- Mounts over any data inside the container, just like traditional mount points.
    - Unmounting will uncover the overridden path and files.
- Binds can't be specified in Dockerfiles, they must be asserted when a container is run.
- Syntactically, bind mounts are similar to volumes:
```bash
  ... run -v /path/on/host:/path/container    # Linux and MacOS
  ... run -v //c/path/on/host:/path/container # Windows

  ... run -v $(pwd):/path/container # a helpful shortcut!
```

---

### Docker Compose
- Docker Compose is a command-line tool as well as a configuration file format.
- Docker Compose allows the configuration of containers and their relationships to be defined and executed as one command.
    - Configuration files are written in YAML that specify containers, networks, volumes, etc needed.
    - `docker-compose` is used for local development and testing.

#### Docker Compose Files
- Compose files can be used locally with `docker-compose` or with `docker` directly in production with Swarm (as of v1.13).
- The Compose YAML format has its own versions.
    - The version must be specified, else version 1 will be assumed.

Compose configurations have several sections.
```yaml
version: '3.1'                     # defaults to 1 unless explicitly specified; v2 is a recommended minimum.
services:                          # containers, equates to docker run commands
    jekyll:                        # a friendly name, this is also the DNS name inside the virtual network
        image: jengel/jekyll-serve # optional if you use build:
        command: ...               # optional, replaces the default CMD specified by the image
        environment:               # optional, equates to -e in docker run
            SOME_ENV_VAR: example  # note that env vars don't use the list format like the volumes and ports sections
        volumes:                   # optional, equates to -v in docker run
            - .:/site              # compose understands the "." shortcut
        ports:                     # optional, equates to -p in docker run
            - '80:4000'
        depends-on:                # defines what containers this container relies on
    servicename2:                  # other containers to configure...

volumes: ...                       # optional, equates to docker volume create
networks: ...                      # optional, equates to docker network create
```

#### The Docker Compose Command Line
- Compose is a separate tool from Docker
    - Comes bundled with Docker on MacOS and Windows, but is a separate download on Linux
- Compose is not designed for production, but moreso to make development and testing easier to set up.
- `docker-compose up`: set up volumes/networks and spin up all containers.
- `docker-compose down`: stop and remove all containers, and remove volumes and networks.
- `docker-compose -d`: set up in the background.
- `docker-compose logs`: show logs; these are the same logs that get output with `docker-compose up`, but if Compose was run
  detached this will allow you to view those logs again.
- `docker-compose ps`: shows currently running containers
- `docker-compose top`: shows running processes in each container
- `docker-compose --help`

#### Adding Image Building to Compose Files
- Compose can also build custom images.
    - If they are cached, Compose will use those versions of the images.
    - Can be forcibly rebuilt with `docker-compose build`
- Built images are not removed when `docker-compose down` is run.
    - Can use `docker-compose -rmi local` to remove images created as well. Note that "local" will not remove any images
      that have a custom tag set by the `image:` field, such as in the below example.

Building images has its own set of YAML key-values.
```yaml
services: ...
    mycontainer:
        build:
            context: .                        # builds in current directory
            dockerfile: CurrentDir.Dockerfile # Dockerfile to build from
        image: newcontainer                   # name of the container once its built; looked for in cache before building
        # image is optional; Compose will generate a name for it if it's not given.
        # If image is given, the built container will not be removed by docker-container down -rmi local
```

---

### Using Swarm
- Scalability and container lifecycle management is important when deploying a lot of containers.
- Docker aims to solve:
    - Container lifecycle
    - Scaling out/in/up/down
    - Automatic respawning of failed containers
    - Replacing containers with zero downtime (blue/green deploy)
    - Controlling/tracking when containers get started
    - Creating cross-node virtual networks
    - Ensuring only trusted servers are running containers
    - Securely storing secrets, keys, passwords, etc, and transporting them securely and exclusively to the right container
- Swarm features are disabled by default; run `docker swarm init` to enable Swarm features.
    - This command performs a lot of PKI (public key infrastructure) and security automation.
        - Root Signing Certificate created for the swarm that's used to establish trust and sign certs for all nodes and managers.
        - Certificate is issued to the first Manager node.
        - Join tokens are created for other nodes to be able to join this swarm.
        - Enables the Swarm API and creates a Raft database to store root CA, configs, and secrets.
            - Encrypted by default on disk (1.13+).
            - No need for another key/value system to hold orchestration/secrets.
            Replicates logs amongst managers via mutual TLS in "control plane".

---

### Using `docker-machine`
- `docker-machine ls` will show a list of currently installed Docker VMs.
- `docker-machine start` will start a Docker VM.
- `docker-machine env <name of machine>` will spit out environment variables that need to be set for a terminal to interact with
  the machine in question.

---

### Miscellaneous Notes
- **Alpine Linux**: a very small, security-focused Linux distro.
  - Its package manager is `apk`.
  - This distro does not come installed with bash, but it does have sh.