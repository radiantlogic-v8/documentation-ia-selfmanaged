---
title: Before installation
description: List of pre-requisites to validate before installing the self-managed solution
---

# Before installation

> [!important] You **MUST** have an account on our [repository](https://repository.brainwavegrc.com/)  
> Please notify your usual contact within Radiant Logics Identity Analytics so that the appropriate accesses can be granted to you.  

Please read the following pages before installing the solution. They will provide you with information on:

* Available installation modes
* Supported environments
* System requirements

Please find below the list of supported plate forms and modes :

| Platform   | Mode           | Configuration             |
| :--------- | :------------- | :------------------------ |
| Windows 10 | Desktop        | WSL2 + Docker for Desktop |
| Linux      | Server/Desktop | server mode recommended   |

> Please note that for linux environments server mode is recommended.  

If you require more information on the available modes please refer to the following pages :

[Installation modes](installation-modes.md)

## Supported Platforms

### Architectures

The only supported architectures are:  

* x86-64
* x64
* amd64
* intel64

### Production

Production environments are only supported on Linux. All other environments are to be used exclusively for development or testing purposes.  

Tested distributions are:  

* Debian 11
* Ubuntu Server
* CentOS 7
* Amazon Linux
* Fedora

### Docker Runtime

For further information on Docker please refer to the installation guides:  
[https://docs.docker.com/engine/install/](https://docs.docker.com/engine/install/)

> [!important] In server mode, please uninstall the docker runtime delivered by your distribution if any is installed by default (snap, apt, yum).  
> These docker runtimes are either outdated or they don't have enough rights. They are not suitable for a server install.  
> Please follow the server installation guides provided by [docker](https://docs.docker.com/engine/install/)  
