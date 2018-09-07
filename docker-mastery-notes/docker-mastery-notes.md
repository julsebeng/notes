# Docker Mastery Notes

## Table of Contents
- [Docker Versions and Releases](#docker-versions-and-releases)
- [Docker on Windows](#docker-on-windows)
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

### Using `docker-machine`
- `docker-machine ls` will show a list of currently installed Docker VMs.
- `docker-machine start` will start a Docker VM.
- `docker-machine env <name of machine>` will spit out environment variables that need to be set for a terminal to interact with
  the machine in question.