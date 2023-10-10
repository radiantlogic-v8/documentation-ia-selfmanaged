---
title: Solution Upgrades
description: Identity Analytics self-managed solution upgrade guides
---

# Upgrading the application

> [!warning] It is **always** recommended to perform a backup of the application before running the upgrade.  
> See [here](backup-restore.md) for more information

## Upgrading the CLI

To upgrade the version of the CLI.  

1. Download the latest version [here](https://repository.brainwavegrc.com/Brainwave/-/packages)
2. Copy the downloaded file to the desired folder:  
   - `/usr/local/bin` when in server mode
   - Into the created folder in desktop mode

## Updating all containers

To check for updates, run `brainwave status`.  
If an update is available, the `installed version` will be lower than the `latest available version`. It will also show up in the console with a `!!` after the `Latest available version` section. For example:  

```bash  
XXXX@XXXX:~$ brainwave status
Installation mode:         Server
Project name:              sandbox
Client version:            1.2.21
Installed version:         1.2.153
Latest available version:  1.2.183                   ‼
Registry:                  igrcanalytics.azurecr.io
Git configuration:         Valid                     √
Images:                    All present               √
Services                   Stopped                   ‼
```

> Please stop the service before performing the upgrade: `brainwave stop` 

To run the update, the easiest method is to run the following commands:  

```bash  
brainwave admin upgrade
brainwave pull
brainwave start
```

For the full list of available option run `brainwave admin upgrade --help`.  

> To clean the old images, after having performed the above upgrade use `brainwave admin clean-images`.  

Use the following to upgrade, pull the new images, delete the old images and restart the service in one command:

```bash
brainwave admin upgrade --clean-images --pull --start
```
