---
title: Red Hat installation
description: Identity Analytics self-managed installation on Red Hat
---

# Red Hat installation

Before installing the solution it is necessary to configure the timezone of your instance, this command is to be executed as root:  

```sh  
timedatectl set-timezone Europe/Paris
```

## Install Podman

To install podman please refer to podman's official documentation:

- [https://podman.io/docs/installation](https://podman.io/docs/installation)

> [!warning] Log out and log back in, to make sure your user gets the permissions to run podman commands

To install podman please run all the following commands as root:

```sh
dnf -y update
dnf -y install podman
```

To run the selfmanaged solution in RHEL using podman it is necessary to install docker compose [https://github.com/docker/compose](https://github.com/docker/compose)

```sh
curl -SL https://github.com/docker/compose/releases/latest/download/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
chmod a+rx /usr/local/bin/docker-compose
```

To validate the installation of podman and docker compose run the following commands as the standard user (e.g. brainwave):

```sh
podman --version
docker-compose --version
```

When running podman as a non root user it is necessary to enable the service and the socket for desired user: 

```sh
systemctl enable --now --user podman podman.socket
systemctl --user status podman.socket
```

### Red Hat 8 requirements

By default RHEL8 runs on croup-v1, however to run podman rootless in RHEL 8 it is necessary to set cgroups V2 as the defautl in the grub. Please execute the following commands as root (see [here](https://access.redhat.com/solutions/6898151) for more information):  

```sh
grubby --update-kernel=ALL --args="systemd.unified_cgroup_hierarchy=1"
```

Once the grubb is updated it is necessary to reboot the service. 

Once the grub is updated it is necessary to update the configuration of the controllers for the user running podman. The following commands should be updated to correspond to the UID of the owner of the podman socket.

```sh
mkdir /etc/systemd/system/user-1000.slice.d
nano /etc/systemd/system/user-1000.slice.d/controllers.conf
```

Paste the following in the previously created file:

```sh
[Slice]
CPUAccounting=yes
MemoryAccounting=yes
TasksAccounting=yes
```

Once all is updated it is necessary to reload the daemon as root:

```sh
systemctl daemon-reload
```

### Podman requirements

Install the required package netavark as root:

```sh
dnf install netavark
```

The following configuration changes Podman are required for the service to run. The values are overwritten by the values present in the following files. Please adapt the path to correspond to the user running podman: 

```sh
mkdir /home/<user>/.config/containers
nano /home/<user>/.config/containers/containers.conf
```

Copy the following the values into the newly created file:

```sh
[containers]
log_driver = "journald"

[network]
network_backend = "netavark"

[engine]
events_logger = "journald"
```

Once the podman configuration is update it is required to restart the socket:

```sh
systemctl --user restart podman.socket
```

To validate the changes please check the following values:

```sh
podman info | grep event
podman info | grep backend
```

### Port configuration

By default we bind to the standard ports 80 and 443. To do so, in the follwing file as root: 

```sh
nano /etc/sysctl.conf
```

Add the following lines, to open the ports 80 and 443: 

```sh
net.ipv4.ip_unprivileged_port_start=80
```

if you wish to open only the 443 port the the following line should be added instead:  

```sh
net.ipv4.ip_unprivileged_port_start=443
```

### Environment variables

If not added please add the following parameters to your users `~/.bashrc` file:

```sh
export XDG_RUNTIME_DIR=/run/user/$(id -u)
export DBUS_SESSION_BUS_ADDRESS="unix:path=${XDG_RUNTIME_DIR}/bus"
```

## Creating Users are Required Directories

When running the service in server mode it is necessary to create the following folders and change the rights on the folders. In the following commands it is assumed podman is run by the user `brainwave`. Please adapt all commands accordingly.

Create the following directories as root:

```sh
mkdir -p /var/log/brainwave
mkdir -p /var/lib/brainwave
mkdir -p /etc/brainwave
mkdir -p /usr/local/brainwave
```

Set the owner of the new directories:

```sh
chown -R brainwave:brainwave /var/log/brainwave
chown -R brainwave:brainwave /var/lib/brainwave
chown -R brainwave:brainwave /etc/brainwave
chown -R brainwave:brainwave /usr/local/brainwave
```

Set the permissions

```sh
chmod ug+rwx -R /var/log/brainwave
chmod ug+rwx -R /var/lib/brainwave
chmod ug+rwx -R /etc/brainwave
chmod ug+rwx -R /usr/local/brainwave
```

## Download and Install Brainwave CLI

Download the Identity Analytics tools binary and its corresponding sha256 file to verify the download, from Identity Analytics's Gitea repository [https://repository.brainwavegrc.com/](https://repository.brainwavegrc.com/). You can also use the following direct link:  

[https://repository.brainwavegrc.com/Brainwave/-/packages/generic/brainwavetools_linux_amd64/1.2](https://repository.brainwavegrc.com/Brainwave/-/packages/generic/brainwavetools_linux_amd64/1.2)

Verify the download: 

```sh
echo "$(cat brainwave.sha256)  brainwave" | sha256sum --check
```

To Install the solution run the following commands as root. It is assumed that the user `brainwave` is running the podman socket.

```sh
mkdir -p /usr/local/bin
cp brainwave /usr/local/bin/brainwave
chown brainwave:brainwave /usr/local/bin/brainwave
chmod ug+x /usr/local/bin/brainwave
```

Add the current user to the brainwave group

```sh
sudo gpasswd -a $(whoami) brainwave
```

> [!warning] Log out and log back in, to make sure your user gets the permissions to run Identity Analytics commands `brainwave XXXX`

## Brainwave Registry

It is necessary to log into the docker registry for Identity Analytics to be able to pull the desired images:  

```sh
podman login brainwave.azurecr.io
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
cp brainwave.local.key /etc/brainwave/certificates/brainwave.local.key
cp brainwave.local.crt /etc/brainwave/certificates/brainwave.local.crt
```

> Note that the filename musth match the domain name. More generally the files have to be named `<hostname>.crt` and `<hostname>.key`

Please refer to the following page for more information:

[SSL configuration page](../configuration/ssl-configuration)

## Starting the Services

```sh
brainwave start
```

Once installed navigate to the `/config` webpage to finalize the configuration. Please see [here](/configuration/config-ui) for more information.  


## Auto completion

An auto completion bash exists for linux environments. Add the following command line to your users bash profile:


```sh
source <(brainwave completion bash)
```

