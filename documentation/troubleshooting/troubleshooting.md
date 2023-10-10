---
title: Troubleshooting
description: Identity Analytics self-managed solution troubleshooting guide
---

# Troubleshooting

Troubleshooting is an integral part of running a solution. The following documentation will provide you with a list of common errors that have been encountered when installing and running the solution.  

This is **not** an exhaustive list, and it'll will updated regularly.  
Please don't hesitate to contact the support services if you wish to update or apply a revision to the current pages.  

> [!important] The following elements are provided as information to help diagnose potential issues. Use at your own risks !

## Locating the log files

### Docker runtime

`brainwave logs` to see logs from all containers
`brainwave logs bwportal` to see the logs from the portal
`brainwave logs bwbatch` to see the logs from the batch

### Location on Disk

For the Portal and Batch services, the logs can also be found on disk.  

#### Server mode

In server mode the logs are located in the following folder:  

- `/var/log/brainwave`

#### Desktop mode

Please use the docker UI to find the volume that is called `*_bwlogs`.

If you are using Docker Desktop with WSL2 ( strongly recommended ). You can reach the docker volumes in the following path:

> `\\wsl$\docker-desktop-data\data\docker\volumes`

## Log level

The default log level is 0 (`info`).  

You can choose to increase the log level using the `-d` or `--debug N` arguments.  

> `-d` is equivalent to `--debug 1`, and `-dd` to `--debug 2`  

You can decrease the logs level using the `-q` or `--quiet N` arguments.

> `-q` is equivalent to `--quiet 1`, and `-qq` to `--quiet 2`  

The log level is valid for the **current command** only (it is not persisted).  
It only impacts the **CLI**, not the docker logs.  

You can use it with the CLI command.  
For example: `brainwave start --debug 3`  

Available log levels are:  

- Trace: 2, `-dd`, or `--debug 2`
- Debug: 1, `-d`, or `--debug 1`
- Info: 0, the **default**
- Warning: -1, `-q`, or `--quiet 1`
- Error: -2, `-qq`, or `--quiet 2`

> Most functions do not return log level above info (so no trace or debug)  
> Notable exceptions are `config` and `install`  

## Common errors

### Application won't start

The application can fail to start due to different reasons.

```sh
brainwave start
● Starting application
√ Start logs [Complete]
√ Start vault [Complete]
√ Generate configuration [Complete]
‼ Get Project [Disabled] (Git configuration incomplete)
× Start application [Failed]
× Failed to start application:
  exit status 1
```

#### Check the license

Did you upload a license file in the `/config` app, in the `Uploads` section?

You can check that the license has been saved in the vault using the following command:  

```bash  
docker exec <vault-id> pass show BW_LICENSE
```

> So for example: `docker exec sandbox-bwvault-1 pass show BW_LICENSE`  

#### Checking the CLI logs

When This occurs to help identify the container at the root of the issue you can navigate to the folder that contains the logs of the CLI:

```sh
cd /usr/local/brainwave/logs
```

This folder contains the logs file for the CLI: `brainwave_yyyyMMdd.log`. Once the container responsible for the failure identified you can query the logs of the container by using the standard command:  

```sh  
brainwave logs <container>
```

### Certificates & TLS

#### Folder rights

If you have trouble starting your instance with `TLS` active, check the permissions of the `/etc/brainwave/certificates` folder and its contents:  

- The owner and group should be `brainwave`; to fix it, run:
  - Run `sudo chown brainwave:brainwave /etc/brainwave/certificates`
- You need read and execute permissions for all users (not just owner and group); to fix it, run:
  - `sudo chmod a+rx /etc/brainwave/certificates`
  - `sudo chmod a+rx /etc/brainwave/certificates/*`

> Sample errors associated with certificates folder access rights:  
> `Missing certificate file to enable HTTPS`
> `err:property "cert" validation failed`

#### Invalid certificate

[//]: ## TODO

### FireWall

If in the CLI logs you find the following error:

```sh  
Error
Container brainwave-bwbrainwavedb-1
Error
dependency failed to start: container brainwave-bwbrainwavedb-1 is unhealthy
```

when querying the logs fo the `bwbrainwavedb` container you find the following errors:

```  
brainwave-bwbrainwavedb-1  | Operation timed out
brainwave-bwbrainwavedb-1  | Operation timed out
brainwave-bwbrainwavedb-1  | Operation timed out
brainwave-bwbrainwavedb-1  | Operation timed out
brainwave-bwbrainwavedb-1  | Operation timed out
brainwave-bwbrainwavedb-1  | Operation timed out
brainwave-bwbrainwavedb-1  | Operation timed out
brainwave-bwbrainwavedb-1  | Operation timed out
brainwave-bwbrainwavedb-1  | Operation timed out
brainwave-bwbrainwavedb-1  | Operation timed out
brainwave-bwbrainwavedb-1  | Operation timed out
brainwave-bwbrainwavedb-1  | Operation timed out
brainwave-bwbrainwavedb-1  | Operation timed out
```

Please check the configuration of your firewall.  
The following commands were used to update the firewall settings accordingly to CentOS. These commands are provided as an example:  

```sh
sudo firewall-cmd --list-all
sudo firewall-cmd --get-zones
```

If no docker zone is listed in the final command you can add the docker zone by using:  

```sh
sudo firewall-cmd --permanent --new-zone=docker
```

You can check if which zones are active using the following command:  

```sh
sudo firewall-cmd --get-active-zones
```

If the docker zone isn't activated, add the `docker0` interface to the `docker` zone by using the following commands:  

```sh
sudo firewall-cmd --permanent --zone=docker --change-interface=docker0
```

Once activated it is necessary to add a rich rule to your firewall within the docker zone to allow communication between the containers:  

```sh
sudo firewall-cmd --permanent --zone=docker --add-rich-rule='rule family="ipv4" source address=172.0.0.0/8 masquerade'
```
  
After applying the changes to your firewall reload the service:  

```sh
sudo firewall-cmd --reload
```

You can also use the following command to check the application of the rule:  

```sh
sudo firewall-cmd --list-rich-rules --zone=docker
```

It is necessary to restart docker for the changes to be taken into account:  

```sh
sudo systemctl restart docker
```

Once all operations done you can relaunch the service using:  

```sh
brainwave start
```

### Portal

Common errors preventing the portal from loading:  

- Wrong Git credentials
  - You need to use an **Access token**
- Wrong `Project directory` value
  - This must match the name of the folder on the **Git repository** that contains the project (by default: `brainwave`)
- Wrong `JDBC` library (jar) version
  - You need the JDBC for **Java 11**

#### Wrong project directory

The `Project directory` value in the `/config/git` page must match the name of the folder on the **Git repository** that contains the project (by default: `brainwave`).  
If it does not, the `bwportal` container will fail to start, and by running `docker compose -f docker-compose.yml -f server-mode-resources.yml up --no-deps bwportal`, you will see the following error in the logs:  

```txt  
sandbox-bwportal-1  | [init] 21-Jun-2023 09:35:02.707 INFO [INIT] Copy full project to /usr/local/bwapp/app
sandbox-bwportal-1  | Traceback (most recent call last):
sandbox-bwportal-1  |   File "docker-init.py", line 201, in <module>
sandbox-bwportal-1  |   File "shutil.py", line 555, in copytree
sandbox-bwportal-1  | FileNotFoundError: [Errno 2] No such file or directory: '/usr/local/bwprojectclone/identityanalytics'
sandbox-bwportal-1  | [37] Failed to execute script 'docker-init' due to unhandled exception!
sandbox-bwportal-1 exited with code 0
```

> If you can't access the `/config` webapp, you can run `cat /usr/local/brainwave/.env | grep IGRC_PROJECT_GIT_PROJECT_DIR` to verify the value  
> If needed, you can also fix the project directory value using the following command: `brainwave config git --project-dir '<direcotryName>'` (where is the folder name, by default `brainwave`)  

#### Portal indefinitely loading

When navigating to `localhost` or the configured url and the portal is not available. Please check the following steps to validate the configuration.  

First Navigate to the secrets manager to make sure the password for the git repository is set. If incorrectly set update the password in the configuration UI (`/config`) and click on 'Save Config'. This will restart the portal.

If after a few minutes the portal is still not up then on the Docker server hosting Identity Analytics check the logs of the project by using the command `brainwave logs bwproject`. This will show if errors occurred while cloning the git repository. This could be related to an authentication issue or a if the network is unreachable.  

You should also heck the logs of the portal using the command `brainwave logs bwportal` on the Docker host. This will show errors in the configuration of the database for example (Injector configuration invalid).  

You can also try restarting the service by first running the command `brainwave stop` and then `brainwave start`. You can follow the steps detailed above to locate potential issues in the configuration.  

#### Client error, Browser-Widget can not access unkown domain (unknown domain)

When starting your portal for the first time you might get the `Client Error` pop-up.
When clicking on details here is the stack tou get:  

```
Error: Operation "call" on target "w172" of type "rwt.widgets.Browser" failed:
SecurityRestriction:
Browser-Widget can not access unkown domain from "debian-11:443".

Parameters: {"functions":["__ppDispatchClientMessage"]}

...
Stack: rwt.remote.MessageProcessor._processError@https://debian-11/app/rwt-resources/3210/resources.js:269:26
processOperationArray@https://debian-11/app/rwt-resources/3210/rap-client.js:226:924
processMessage@https://debian-11/app/rwt-resources/3210/rap-client.js:226:253
_handleSuccess@https://debian-11/app/rwt-resources/3210/rap-client.js:230:3804
setSuccessHandler/this._success@https://debian-11/app/rwt-resources/3210/rap-client.js:98:784
_sendWithFetch/
```

Where `debian-11` is the hostname of the system (yours might differ).  
The solution is to provide only lowercase letters when configuring the hostname with `brainwave config --hostname`.  
So, `brainwave config --hostname LocalHost.local` is in correct, you should use `brainwave config --hostname localhost.local` instead.  

### Workflow issues

If you can't start tasks in the web app and have the `Cannot start workflow` error message in the portal logs, check the `schema` of the user database user.  

If you are using the integrated Postgre SQL database, this is set by the `search_path` parameter:  

```bash  
docker exec <projectname>-bwdatabase-1 psql -d brainwave -U igrc -c 'show search_path;'
 search_path
-------------
 ledger
(1 row)
```

> Replace `<projectname>` by the name of you project (you can also use `docker ps` to list the containers and get their name)
> `ledger` here is incorrect  

You can set it to the default values for the integrated database as follows:  

```bash  
docker exec <projectname>-bwdatabase-1 psql -d brainwave -U igrc -c 'ALTER ROLE igrc SET search_path = i
grc, activiti, auditlog, public;'
ALTER ROLE
```

> You will need to do `brainwave stop` then `brainwave start` for this to be taken into account  

### Pulling issues

If the pull fails, for example with:  

```  
Cannot pull some image:
cannot pull all images from registry repository.brainwavegrc.com/brainwave and tag version-1.2.156: exit status 1
```

Then try to manually pull the image(s) that fail directory using the `docker pull` command. For example:  

```bash  
docker pull igrcanalytics.azurecr.io/bwbatch:version-1.2.153
```

> It should provide a more detailed error message.  

### False positives

These error are thrown as such in the logs but do not actually prevent the application from functioning correctly:  

#### Failed to add user 'master-admin'

```txt  
/brainwave-bwauth-1    2023-05-10 15:17:55,390 ERROR [org.keycloak.services] (main) KC-SERVICES0010: Failed to add user 'master-admin' to realm 'master': user with username exists
```

### Starting containers manually

If the `brainwave start` command is stuck at the `Start application` step, it can be because one container did not start, and the others are waiting on it (dependencies).  

The most likely suspect is the `bwingress` container, as it's the applications' **entry point**.  

> This guide will use `bwingress` as an example, but the same logic applies if other containers do not start

Launch the `docker ps` command to check if the `bwingress` container is running or not.  

You can see which containers it depends on in the `/usr/local/brainwave/docker-compose.yml` file, in the **depends_on** category:  

```yml  
  bwingress:
    image: ${REGISTRY_URL}/bwingress:${VERSION_TAG:-latest}
    networks:
      - internal
      - restricted
    depends_on:
      bwauth-init:
        condition: service_completed_successfully
    restart: unless-stopped
```

> `bwingress` depends on (or *requires*) the `bwauth-init` container

Launch the `docker ps` command to check if the `bwauth-init` container is running (probably not).  
And we run the same steps again to find which containers are required for `bwauth-init`.  

```yml  
  bwauth-init:
    image: ${REGISTRY_URL}/bwauth-init:${VERSION_TAG:-latest}
    depends_on:
      bwlogs:
        condition: service_healthy
      bwvault:
        condition: service_healthy
      bwauth:
        condition: service_healthy
    networks:
      - restricted
      - internal
```

> `bwingress` depends on (or *requires*) the `bwlogs`, `bwvault` and `bwauth` containers

Launch the `docker ps` command to find out which of those containers are down.  
Now you can manually launch the containers from the bottom to the top.  
For example: start with `bwlogs`, `bwvault` and `bwauth`, then `bwauth-init` and finally `bwingress`.  

To do so, you will need to use the `docker start` command, followed by your container name.  
The container name is the name of the project, followed by the container and `-1`.  
For example: `demo-bwlogs-1`, and so `docker start demo-bwlogs-1`  

This should allow you to start the applications, or find out why one of the containers does not start. For example:  

```bash  
docker start demo-bwingress-1
Error response from daemon: driver failed programming external connectivity on endpoint demo-bwingress-1 (d00ec29ef4539015970ba0069b3ed7c072385b3d4ebe3baa6fea704d2ec4c218): Error starting userland proxy: listen tcp4 0.0.0.0:443: bind: address already in use
Error: failed to start containers: demo-bwingress-1
```

### Test a specific container

You might want to test a specific container without all the dependencies of `docker compose`.  
To do so, try the following:  

```bash  
cd /usr/local/brainwave
docker compose -f docker-compose.yml -f server-mode-resources.yml up --no-deps <image>
```

> Where `<image>` is the name of the image, for example `bwportal` or `bwingress`  
