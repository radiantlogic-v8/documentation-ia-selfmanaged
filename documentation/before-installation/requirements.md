---
title: System Requirements
description: List of system requirements for the self-managed solution
---

# System requirements

The recommended deployment consists of:

- A host machine that will run the docker containers
- PostgreSQL database running in an separate host

## PostgreSQL

- PostgreSQL 14
- PostgreSQL 15

We always recommend using the latest available minor release.

It is recommended to dedicate the PostgreSQL environment to the Identity Analytics solution.

> [!note] Please retrieve the PostgreSQL version for the target distribution. Do **NOT** recompile the PostgreSQL kernel.

### PostgreSQL RAM

A minimum of **8Gb** is required. It is recommended to allocate between **16Gb** and **32Gb** (dynamic allocation is possible on a virtual machine) as the number of identities, accounts, permissions loaded have an impact on the final storage space of the database as well as the number of simultaneous connections expected. As a reminder, each client process has its own private memory area (`work_mem`).

### PostgreSQL Hard Drive

Your choice of hard drive will determine the performance of your database. Prefer fast disks such as SAS 15K RPM, or SSD disks for example (with backup battery) for storing the WAL and statistics. This part is crucial so please consult your storage experts for a better expertise.

### PostgreSQL CPU

The number of cores will help with parallel processing and determine the number of parallel client connections possible. The frequency is less important than the number of cores.

> [!tip] PostgreSQL is a multi-process system. Each client connection is managed by a separate process, responsible for executing the request and returning the data to the client. As of PostgreSQL 9.6, a process executing a request can request the help of other processes (workers) to process this request (In 9.6 this is limited to the execution of read-only requests and not enabled by default). PostgreSQL 10 and above proposes the parallelization of new operations (although always in reading) as in the index path (index scan and index only scan), Merge Joints, the collection of results while preserving the sort order (gather Merge).

This is a configuration example that can be used in most cases for Linux:  

- Processor: Intel(R) Xeon(R) CPU E5-2660 v4 @ 2.00GHz (Number of cores / Threads: 14 / 28)
- Memory: 16 Gb
- Network Ethernet: 1Gb
- Hard Drive: SAS 15K RPM + SSD

## Docker Host

### Docker Host RAM

32 GB of RAM is recommended  

### Docker Host CPU

The Suggestion is at least 4 cores

### Docker Host Disk

A total of 512 GB of disk space is recommended.

#### Server mode

When installing the self-managed service in server mode. The following folder paths are used by the self-managed service. These paths cannot be changed.
The following list will also provide you with information on what the folder are used for and provide an estimation of the **minimum** size requirements for each.  

- `/etc/brainwave`

Minimal Size 1GB. This is used to store among others the `Brainwavefile` configuration file, the keys used for the vault to encrypt secrets as well as the TLS certificates.  

- `/var/lib/brainwave`

Minimal size 100GB. This folder includes all Docker volumes. The different databases, encrypted secrets and in particular all source files and import files. If an internal database is use the data is stored in this folder. encrypted secrets and in particular:

- `/var/lib/docker`

Minimal size 20GB. This folder contains all docker daemon data, including the images. As a rule, all images take around 5 to 6GB of disk space.

> [!note] It is possible to remove older versions by using the `--clean-images` option when upgrading the application: `brainwave admin upgrade --clean-images`

It is possible to customize the location of Docker's Daemon data directory. Please refer to Docker's official documentation for more information:

[https://docs.docker.com/config/daemon/](https://docs.docker.com/config/daemon/)

- `/usr/local/brainwave`

Minimal size 1GB. This folder contains all Docker compose configuration files and other configuration/environment files required by the application. It also contains the CLI logs.  

- `/var/log/brainwave`

Minimal size 10GB. This folder include the container and the batch logs. The occupied volume will depend on log retention policy.

- `/usr/local/bin`

This folder will contain the 10MB Identity Analytics cli tool, named `brainwave`.  

> To avoid issues with partition sizes and mount points, prefer a single large (512GB) partition

### OS and Software

Debian 11 (stable) or Ubuntu Server 22.04 (LTS)  

Docker Engine version 20.10
Please refer to the following documentation for more information :

- Debian: [https://docs.docker.com/engine/install/debian/](https://docs.docker.com/engine/install/debian/)
- Ubuntu: [https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)

Docker Compose plugin must be installed.

## External Database Requirements

To prepare the environnement to use an external database, it is required to:

- Create 3 users: `igrc`, `activiti` and `auditlog`
- Create 1 schema by user. It should match the user name. Each user should own its corresponding schema
  - The `igrc` user should own the `igrc` schema
  - The `activiti` user should own the `activiti` schema
  - The `auditlog` user should own the `auditlog` schema

> All users and schemas **must** be on the same database  
> **Warning:** Hosting the database in the same machine than runs the Docker runtime is not supported.

Visit the Configuration UI at `<hostname>/config` to finalize the configuration of the external database:

[Configuration UI](/configuration/config-ui)

The standard requirements are to be followed when sizing the database:

[Database best practices](/best-practice/databases)

### Data base initialization

When using an external database it is possible configure the initialization of the schemas directly in the `/config` webpage.  

To initialize the database manually, download the SQL scripts from this direct link (it is required to be authenticated for the following link to work):

[https://repository.brainwavegrc.com/Brainwave/-/packages/generic/brainwave_database_scripts/1.2](https://repository.brainwavegrc.com/Brainwave/-/packages/generic/brainwave_database_scripts/1.2)

> Please select the scripts as a function of your desired database engine.  

1. Initialize the `igrc` schema by connecting as the `igrc` user and execute the script in file `100-ledger.sql`
2. Initialize the `activiti` schema by connecting as the `activiti` user and execute these 3 scripts depending on the system:
  - `200-activiti.postgres.create.engine.sql` or `200-activiti.sqlserver.create.engine.sql`
  - `210-activiti.postgres.create.identity.sql` or `210-activiti.sqlserver.create.identity.sql`
  - `220-activiti.postgres.create.history.sql` or `220-activiti.sqlserver.create.history.sql`
3. Initialize the `auditlog` schema by connecting as the `auditlog` user and execute the script in file `300-auditlog.sql`

#### Postgres Example

An example of initializing a postgresql database using psql:

```bash
export PGPASSWORD="<igrc-password>"
psql -h dbhostname -d brainwave -U igrc -p 5432 -f 100-ledger.sql

export PGPASSWORD="<activiti-password>"
psql -h dbhostname -d brainwave -U activiti -p 5432 -f 200-activiti.postgres.create.engine.sql
psql -h dbhostname -d brainwave -U activiti -p 5432 -f 210-activiti.postgres.create.identity.sql
psql -h dbhostname -d brainwave -U activiti -p 5432 -f 220-activiti.postgres.create.history.sql

export PGPASSWORD="<auditlog-password>"
psql -h dbhostname -d brainwave -U auditlog -p 5432 -f 300-auditlog.sql
```

#### MS SQL Server Example

##### SQLCMD

You can run the scripts using `sqlcmd`, for example:  

```powershell  
sqlcmd -H dbhostname -d brainwavedb -U igrc -P <igrc-password> -i 100-ledger.sql -o 100-result.rpt
sqlcmd -H dbhostname -d brainwavedb -U activiti -P <activiti-password> -i 200-activiti.sqlserver.create.engine.sql -o 200-result.rpt
sqlcmd -H dbhostname -d brainwavedb -U activiti -P <activiti-password> -i 210-activiti.sqlserver.create.identity.sql -o 210-result.rpt
sqlcmd -H dbhostname -d brainwavedb -U activiti -P <activiti-password> -i 220-activiti.sqlserver.create.history.sql -o 220-result.rpt
sqlcmd -H dbhostname -d brainwavedb -U auditlog -P <auditlog-password> -i 300-auditlog.sql -o 300-result.rpt
```

> Change `dbhostname`, `brainwavedb`, `<igrc-password>`, `<activiti-password>` and `<auditlog-password>` to match your environment  

##### SQL Server Management Studio

Or you can add the following at the top of the SQL scripts to run them as the correct user in MS SQL Server:

```sql  
EXECUTE AS USER = '<LOGIN>';
USE "<DATABASE>";
```

Where `<DATABASE>` is the name of your database, and `<LOGIN>` the login for the correct user:  

- For `100-ledger.sql`, `<LOGIN>` is: `igrc`
- For `200-activiti.*.sql`, `<LOGIN>` is: `activiti`
- For `300-auditlog.sql`, `<LOGIN>` is: `auditlog`

You can also choose to directly connect with the correct user:  
![MS SQL Server user connection](images/msql_server_user_connection.png)  
