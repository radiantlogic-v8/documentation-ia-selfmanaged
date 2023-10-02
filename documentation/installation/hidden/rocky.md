---
layout: page
title: "Rocky Linux"
toc: true
nav_exclude: true
search_exclude: true
---

> [!warning] The following procedure is provided as an example. Rocky Linux is not supported officially by docker and BrainwaveGRC.  
> Use at your own risk.  

Before installing the solution it is necessary to configure the timezone of your instance:  

```sh  
sudo timedatectl set-timezone Europe/Paris
```

## Install Docker Runtime

The usual docker repository is not compatible with Rocky Linux, Docker's CentOS repo has to be used instead.

To install the docker runtime you can execute these commands:

```sh
sudo dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo
sudo dnf update
sudo dnf install docker-ce docker-ce-cli containerd.io --allowerasing
sudo systemctl enable docker
sudo chmod +x /etc/rc.d/rc.local

sudo gpasswd -a $(whoami) docker
sudo usermod -aG docker $(whoami)
```

> [!warning] Log out and log back in, to make sure your user gets the permissions to run docker commands

For more information on the installation of docker pleaser refer to the official documentation:
[https://docs.rockylinux.org/gemstones/docker/](https://docs.rockylinux.org/gemstones/docker/)

## Creating Users are Required Directories

It is recommended to create a set of system users and groups to install the brainwave CLI.

```sh
sudo groupadd --system brainwave
sudo useradd --system --gid brainwave --no-create-home brainwave
```

Create the following directories:

```sh
sudo mkdir -p /var/log/brainwave
sudo mkdir -p /var/lib/brainwave
sudo mkdir -p /etc/brainwave
sudo mkdir -p /usr/local/brainwave
```

Set the owner of the new directories:

```sh
sudo chown -R brainwave:brainwave /var/log/brainwave
sudo chown -R brainwave:brainwave /var/lib/brainwave
sudo chown -R brainwave:brainwave /etc/brainwave
sudo chown -R brainwave:brainwave /usr/local/brainwave
```

Set the permissions

```sh
sudo chmod ug+rwx -R /var/log/brainwave
sudo chmod ug+rwx -R /var/lib/brainwave
sudo chmod ug+rwx -R /etc/brainwave
sudo chmod ug+rwx -R /usr/local/brainwave
```

## Download and Install Brainwave CLI

Download the Brainwave tools binary and its corresponding sha256 file to verify the download, from Brainwave's Gitea repository [https://repository.brainwavegrc.com/](https://repository.brainwavegrc.com/). You can also use the following direct link:  

[https://repository.brainwavegrc.com/Brainwave/-/packages/generic/brainwavetools_linux_amd64/1.2](https://repository.brainwavegrc.com/Brainwave/-/packages/generic/brainwavetools_linux_amd64/1.2)

Verify the download

```sh
echo "$(cat brainwave.sha256)  brainwave" | sha256sum --check
```

Installation

```sh
sudo mkdir -p /usr/local/bin
sudo cp brainwave /usr/local/bin/brainwave
sudo chown brainwave:brainwave /usr/local/bin/brainwave
sudo chmod ug+rx /usr/local/bin/brainwave
```

Add the current user to the brainwave group

```sh
sudo usermod -aG brainwave $USER
```

> [!warning] Log out and log back in, to make sure your user gets the permissions to run brainwave commands
> You can also run the following command to activate the changes to groups:  

```sh  
newgrp brainwave
```

## Brainwave Registry

It is necessary to log into the docker registry for Brainwave to be able to pull the desired images:  

```sh
docker login repository.brainwavegrc.com
```

## Installation

To install the solution in server mode please use the following command:  

```sh
brainwave install --project-name brainwave --server
brainwave pull
```

## Initial configuration

> [!note] The hostname `brainwave.local` used below is provided as an example for the command line. The parameter should be updated to your context, the hostname of the machine hosting the docker service.  
> The hostname value **must** be `lowercase`  

Before starting the services, set up the hostname

```sh
brainwave config --hostname brainwave.local
```

## TLS configuration (Optional)

To activate tls  

```sh
brainwave config --tls
```

Before starting the service it is necessary to copy the certificate and the private key into the certificates folder

```sh
cp brainwave.local.key /etc/brainwave/certificates/  
cp brainwave.local.crt /etc/brainwave/certificates/  
```

> Note that the filename musth match the domain name. More generally the files have to be named `<hostname>.crt` and `<hostname>.key`

Please refer to the following page for more information:

[SSL configuration page](/configuration/ssl-configuration)

## Starting the Services

```sh  
brainwave start
```

Once installed navigate to the `/config` webpage to finalize the configuration. Please see [here](/configuration/config-ui) for more information.  
