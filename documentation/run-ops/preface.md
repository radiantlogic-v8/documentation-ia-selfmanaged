---
title: Run-ops
description: Identity Analytics self-managed solution run-ops guide
---

# Run-ops

The sub-pages include the documentation on the different operations executed when running the self-managed solution.  

These include, among others:  

- Upgrading the application
- Performing a backup  
- ...

Below you will find different common operations when using the solution.  

## Common operations

### Interface URLS

`<hostname>/` will allow the user to navigate to the portal. It will forward to configuration interface if its not possible to load the portal.  

`<hostname>/config` will open the configuration interface. This interface contains the secret manager and license upload location. Please see [here](/configuration/config-ui) for the documentation on the configuration interface.  

`<hostname>/auth` will open Keycloak admin interface.  

`<hostname>/controller` will open the controller UI. Please see [here](/containers/controller) for the controller documentation.  

### CLI operations

Here a list of common operations using the Identity Analytics tools CLI.  

To get the full list of commands type `brainwave --help`. For more information on a detailed action add --help to the desired command. For example `brainwave backup create --help`  

Please find bellow some examples.

- Start the services: `brainwave start`
- Stop the services: `brainwave stop`
- Display log files:  
  - `brainwave logs` to see logs from all containers
  - `brainwave logs bwportal` to see the logs from the portal
  - `brainwave logs bwbatch` to see the logs from the batch
