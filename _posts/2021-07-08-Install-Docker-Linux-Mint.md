---
layout: post
title: "Install Docker on Linux Mint"
categories: misc
---

About once a year I have to install Docker on Linux Mint, so I did a few weeks ago. I always have to search for the proper solution about a half a hour. Therefore maybe it's time to document it for me and maybe also for you.

### The Problem

When you go to the office [install documentation](https://docs.docker.com/engine/install/) there is no install instruction for Linux Mint. As Mint is based on Ubuntu I thought it would work on Mint, too. The answer is yes and no. The binaries work but the install script will not work and here is the reason.
The install script contains this command.
```bash
$(lsb_release -cs)
```
It will return the name of the distribution. But as you are on Mint it will not return the name of the underlying Ubunutu version but the name of the Mint version. The last time I run in this problem the name was  *ulyssa*. As you can see here, there is no binary available.

![docker-ubunut-release-screenshot](_posts/ubuntu-docker-releases.png)

### The Solution

Check out your distribution infos with this command `cat /etc/*-release`. 

```bash
cat /etc/*-release
DISTRIB_ID=LinuxMint
DISTRIB_RELEASE=20.1
DISTRIB_CODENAME=ulyssa
DISTRIB_DESCRIPTION="Linux Mint 20.1 Ulyssa"
NAME="Linux Mint"
VERSION="20.1 (Ulyssa)"
ID=linuxmint
ID_LIKE=ubuntu
PRETTY_NAME="Linux Mint 20.1"
VERSION_ID="20.1"
HOME_URL="https://www.linuxmint.com/"
SUPPORT_URL="https://forums.linuxmint.com/"
BUG_REPORT_URL="http://linuxmint-troubleshooting-guide.readthedocs.io/en/latest/"
PRIVACY_POLICY_URL="https://www.linuxmint.com/"
VERSION_CODENAME=ulyssa
UBUNTU_CODENAME=focal
```

The interesting part is the very last one of the variables *UBUNTU_CODENAME*. 

Now simply follow the [install instructions](https://docs.docker.com/engine/install/). 

1. Update the apt package index and install packages to allow apt to use a repository over HTTPS:
```bash
sudo apt-get update
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```
2. Add Docker’s official GPG key:
```bash
 curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

```
3. Now add docker to your apt source list and replace `$(lsb_release -cs)` with the name of your underlying ubuntu distribution. In my case it was `focal`

```bash
echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  focal stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
4. Install docker
```bash
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```
5. Test if everything worked as expected
```bash
docker run --rm hello-world
```
The output should be something like this:

```bash
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
b8dfde127a29: Pull complete 
Digest: sha256:5122f6204b6a3596e048758cabba3c46b1c937a46b5be6225b835d091b90e46c
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