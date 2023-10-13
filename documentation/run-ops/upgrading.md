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

## Updating all containers (minor versions)

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
## Major version upgrade:
  
The major upgrade process differs slightly from minor upgrades as you need to upgrade your CLI.  
This guide is primarily written for a **server install** on a linux platform.  
Most steps after the installation of the CLI should stay the same for a Desktop install.  

### Prerequisites:

Make sure to have your project's repository configured to be our official Gitea repository: repository.brainwavegrc.com/brainwave.  
You can run `brainwave status` to retrieve that info.  
If you happen to be on another repository, you will need to modify your .env file (in /usr/local/brainwave):  
Change your REGISTRY_URL variable to : `repository.brainwavegrc.com/brainwave`  

### Performing the upgrade

1. Download the new CLI (URL will change depending on platform and version, check [the repository](https://repository.brainwavegrc.com/Brainwave/-/packages?q=tools&type=)) :
    ```
    curl --user "username":"password" --output brainwave https://repository.brainwavegrc.com/api/packages/Brainwave/generic/brainwavetools_linux_amd64/1.4/brainwave
    curl --user "username":"password" --output brainwave.sha256 https://repository.brainwavegrc.com/api/packages/Brainwave/generic/brainwavetools_linux_amd64/1.4/brainwave.sha256
    echo "$(cat brainwave.sha256)  brainwave" | sha256sum --check
    ```
2. Install to your execution directory:
    ```
    sudo mkdir -p /usr/local/bin
    sudo cp brainwave /usr/local/bin/brainwave
    sudo chown brainwave:brainwave /usr/local/bin/brainwave
    sudo chmod ug+rx /usr/local/bin/brainwave
    ```
   (Optional): You can install the binaries to your /usr/bin:  
    ```
    sudo cp brainwave /usr/bin/brainwave
    sudo chown brainwave:brainwave /usr/bin/brainwave
    sudo chmod ug+rx /usr/bin/brainwave
    ```

3. Run `brainwave status` to check that you are running the right CLI.  
   You should get something like this:  
   ```
   Installation mode:  Server
   Project name:       brainwave
   Client version:     1.4.12
   Installed version:  1.2.198

   × This client version is incompatible with the installed application
   ```
4. Run `brainwave admin upgrade --clean-images --pull --start`
  
If you have an internal database you should have now a functional install of your new version!  
If you run an external database, consider these next steps:
  
5. Navigate to `<hostname>/config` and go to the Database panel.

6. Verify and refill all fields with the appropriate connection information and test the connections.  
   Save when done.

7. Restart all container using:
   ```
   brainwave stop
   brainwave start
   ```  

### Troubleshooting

> This section aims to list all issues one might encounter during an upgrade and their fixes  
> If you encounter any new issues please add it to the list, or contact us to do so.  
  
##### Git issues

1. `fatal: Unable to create '/gitrepos/sync/.git/shallow.lock'`  

You might see you portal restarting with this error:  
```log
2023-10-10 09:22:21 Traceback (most recent call last):
2023-10-10 09:22:21   File "docker-init.py", line 218, in <module>
2023-10-10 09:22:21 FileNotFoundError: [Errno 2] No such file or directory: '/gitrepos/sync/current'
```
And find this error in you bwprojectmirror logs :
```
too many failures, aborting {"error":"Run(git fetch https://repository.brainwavegrc.com/john.doe/AcmeCorp.git 2956094eb17f9133a65c41bb5ad70fccaa44a5fb --verbose --no-progress --prune --no-auto-gc --depth 1): exit status 128: { stdout: \"\", stderr: \"POST git-upload-pack (69 bytes)\\nPOST git-upload-pack (157 bytes)\\nfatal: Unable to create '/gitrepos/sync/.git/shallow.lock': File exists.\\n\\nAnother git process seems to be running in this repository, e.g.\\nan editor opened by 'git commit'. Please make sure all processes\\nare terminated then try again. If it still fails, a git process\\nmay have crashed in this repository earlier:\\nremove the file manually to continue.\" }","failCount":1}
```
To solve this simply delete the `shallow.lock` mentioned here.  
On a server install you will find it in `/var/lib/brainwave/repos/sync/.git/`.  
On a desktop install it will be in `\\wsl$\docker-desktop-data\data\docker\volumes\brainwave_bwgitrepos\_data\sync\.git\`  
  
After deleting the file, simply restart the application.  
  
2. `fatal: early EOF`  
  
You might see you portal restarting with the same error stated above, but with this time this error in your bwprojectmirror logs:  
```log
/brainwave bwprojectmirror run 1672576749be 2923 10 10 13:49:57.453093 too many failures, aborting ("error*:"Run(git fetch
https://reposivory.brainwavegrc.com/Brainwave/identityanalytics 42392570332979904c4C041530ab4ta386967ed --verbose --no-progress --prune --no-auto-gc --depth
l): context deadline exceeded: { stdout: \"\", stderr: \“POST git-tuploaa-pack (69 bytes) \\nPOST git-upload-pack (157 bytes) \\nfatal: early BOR.”}", "failcount":1}

```
This error basically means that git fails to pull the whole commit/repository.  
It mostly happens during large pulls/clones, especially when there is a slow connection/connectivity issues between local and remote.
  
To unlock the situation you can:  
1. Open a terminal and navigate to the folder containing your docker-compose.yml file (`/usr/local/brainwave` on a server install and .brainwave folder on a desktop install).  
2. Run `docker compose run --entrypoint=/bin/sh bwprojectmirror`
3. Run `cd gitrepos/sync`
4. Run `git pull`
  
  This forces the pull operation manually.  
  After this simply restart the application
