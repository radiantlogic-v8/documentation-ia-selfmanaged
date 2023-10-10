---
title: Installation
description: Guides to how to install Identity Analytics Self-Managed solution 
---

# Identity Analytics Self-managed installation

Installation of the self-managed solution is only supported for:

- [Debian](debian) in a production environment
- [Windows desktop](windows-desktop) for dev and demo purposes

Before installing the self-managed solution please refer to the [installation requirements](../before-installation/preface) page to validate a your system requirements and desired installation method.  

## Debug options

> The following documentation is not to be used in a PROD environnement. This documentation is provided for demonstration or development environments.  

The following action are to be executed after having installed the self-managed solution for Identity Analytics.  

### Activate debug options  

It is possible to enable debug options in the global tab of the `/config` frontend.  

![Activate debug options](./images/selfmanagedDebugOptions.png "Activate debug options")

This option adds two additional containers:  

- `bwstmp4dev`: That provides the user with a development smtp server to test or demonstrate the behavior of emails
- `bwpgadmin`: That provides the user with a tool to access and query the internal databases.  

These two containers help the solution administrator when developing or demonstrating the product.  

### Installation of containers

To install the containers once the option is activated it is required to:  

- Stop the application  
- Pull the missing application  
- Restart the service  

```sh
brainwave stop
brainwave pull 
brainwave start
```

These operations must be performed manually.  