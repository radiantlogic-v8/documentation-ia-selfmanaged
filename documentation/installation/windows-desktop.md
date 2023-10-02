---
layout: page
title: "Windows desktop installation"
parent: "Installation"

toc: true 
---

# Windows desktop installation

Only Desktop mode is supported in Windows environments. Docker for Desktop is required. It is recommended to create a project folder with the desired project name where the binary file should be placed. For example: `brainwave`.  

> [!warning] The project folder used **must** all be lower case and not include spaces.  

## Requirements

### Docker for Desktop

Install docker for Desktop and configure it using WSL 2.  

### WSL 2 Memory Management

In order to configure the maximum memory allocation for the Docker services, create a file called `.wslconfig` in your home folder (e.g. `C:/Users/username/.wslconfig`)

Is a simple text file with following content:

```config  
[wsl2]
memory=16GB
```

> [!warning] This configuration is **required**. The services can silently fail if the default memory management does not allocate enough memory.  

A system restart might be required. More information [here](https://learn.microsoft.com/en-us/windows/wsl/wsl-config)

## Download and install Identity Analytics CLI

Download the Identity Analytics tools binary from Identity Analytics's Gitea repository [https://repository.brainwavegrc.com/](https://repository.brainwavegrc.com/). You can also use the following direct link:  

[https://repository.brainwavegrc.com/Brainwave/-/packages/generic/brainwavetools_windows_amd64/1.2](https://repository.brainwavegrc.com/Brainwave/-/packages/generic/brainwavetools_windows_amd64/1.2)

> Note the windows binary is signed.  

### Docker registry

It is necessary to log into the docker registry for Identity Analytics:  

```sh
docker login repository.brainwavegrc.com
```

### Installation

To install the solution in desktop mode please use the following command:  

```sh
brainwave brainwave install --project-name brainwave --desktop
brainwave pull
```

### Initial configuration

> [!note] The hostname `brainwave.local` used below is provided as an example for the command line. The parameter should be updated to your context, the hostname of the machine hosting the docker service.  
> The hostname value **must** be `lowercase`  

Before starting the services, set up the hostname:  

```sh
brainwave config --hostname brainwave.local
```

### TLS configuration (Optional)

To activate tls  

```sh
brainwave config --tls
```

The certificate (`.crt`) and the private key (`.key`) must be placed in the docker volume called: `bwcertificates`.  
Find the path of the volume in your local deployment place the files inside the volume.  

> Note that the filename musth match the domain name. More generally the files have to be named `<hostname>.crt` and `<hostname>.key`

Please refer to the following page for more information:

[SSL configuration page](/configuration/ssl-configuration)

### Start the Services

```sh
brainwave start
```

Once installed navigate to the `/config` webpage to finalize the configuration. Please see [here](/configuration/config-ui) for more information.  

## Where are the logs ?

If you are using Docker Desktop with WSL2 (strongly recommended). You can reach the docker volumes in the following path:

```cmd  
\\wsl$\docker-desktop-data\data\docker\volumes
```

The logs of the instance will be found in the folder `*_bwlogs`.  
