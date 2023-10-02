---
layout: page
title: "Backup and Restore"
parent: "Run-ops"
nav_order: 2
toc: true
---

# Backup and Restore

The persistent data is stored using docker volumes. In server mode these volumes use bind mounts to store all files in pre-defined paths in the filesystem. Data includes configuration, encrypted credentials, tls certificates, logs and IGRC project.

The Identity Analytics tools CLI includes an option to backup and restore all the data contained in the volumes of a given instance.

All data contained in the volumes will be backed up to a single `.tar` file. For safety, this file must be stored in a different location. Using this single file the full instance can be restored.

> [!warning] The backup **only** works on the data **contained** in the docker volumes. If an external database is used the database is **NOT** backed-up using the following CLI command.  
> When using an external database it is necessary to perform in addition a backup of the database.  
> The CLI backup command will backup the database data when an internal database is configured.  

## Pre-requisites

All containers must be shutdown before running the backup and restore process. To do so run the following command:

```sh
brainwave stop
```

Wait until all services are properly stopped before trying to backup or restore.

## Backup sequence

To backup the data within the docker volumes please run the following command:

```sh
brainwave backup create --output /path/to/output/dir
```

For more information on the use of the backup command line please type: `brainwave backup create --help`

The configured output directory is is the directory where the `.tar` will be created. The name of the file is generated automatically and it will include the full date and time information. For example: `bwbackup_202212271307.tar`.

## Restore sequence

To restore the data run the following command:

```sh
brainwave backup restore --input /path/to/tar/bwbackup_XXXXX.tar
```

> [!warning] The data present in the tar file will completely **override** the current data in the instance!

## Post operations

> [!warning] The backup includes the certificates when the `https://` has been configured. When restoring a backup on another server it is necessary to update the desired certificates accordingly.  

To restart the service please run the following command:  

```sh
brainwave start
```

This will bring all the services back up and running.
