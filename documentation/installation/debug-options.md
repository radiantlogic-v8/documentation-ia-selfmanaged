---
layout: page
title: "Debug options"
parent: "Installation"

toc: true 
---

# Debug options

> The following documentation is not to be used in a PROD environnement. This documentation is provided for demonstration or development environments.  

## Activate debug options  

It is possible to enable debug options in the global tab of the `/config` frontend.  

![Activate debug options](./images/selfmanagedDebugOptions.png "Activate debug options")

This option adds two additional containers:  

- `bwstmp4dev`: That provides the user with a development smtp server to test or demonstrate the behavior of emails
- `bwpgadmin`: That provides the user with a tool to access and query the internal databases.  

These two containers help the solution administrator when developing or demonstrating the product.  

## Installation of containers

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
