---
title: "Selfmanaged Identity Analytics release notes"
descirption: "Selfmanaged Identity Analytics release notes"
---

# Selfmanaged Identity Analytics Release Notes

## Version 1.5

### Overview

Version 1.5 of the selfmanaged solution for IDA includes:

- RedHat enterprise Linux support for version.
- Batch: new feature to break reconciliations during the execution plan.
- Config: Allow to customize the max memory limit fo the controller.
- Config: provide a default git configuration out-of-the box.
- Config: Modify the JAVA_OPTS used for running the batch.
- Various vulnerabilities and bug fixes  

### Work item list

- `PP-764`: Installation in windows desktop fails: protocol not available
- `PP-763`: Docker compose version 2.24.7 is no longer compatible
- `PP-761`: The base ledger and/or activity do not seem to be restored.
- `PP-757`: File upload through DSM seems KO
- `PP-756`: CLI extraction loops indefinitely
- `PP-755`: CLI backup fails
- `PP-752`: CLI updating resources fails
- `PP-751`: Vault fails to start
- `PP-748`: smtp4dev container fails to start up
- `PP-747`: Batch fails due to missing project file
- `PP-746`: Custom values in containers.conf
- `PP-745`: Issue while configuring the external DB
- `PP-744`: The follow on logs seems KO
- `PP-743`: Ingress redirection seems to systematically add the 9080 port
- `PP-742`: Installing on RHEL 8 does not work
- `PP-741`: CLI brainwave admin destroy fails
- `PP-740`: SELinux is blocking containers volumes mount
- `PP-738`: Brainwave CLI updates
- `PP-737`: Database container fails to mount the tempfs /dev/shm
- `PP-736`: Ingress bind port on the host fails
- `PP-735`: Ingress permission denied 0.0.0.0:80 internally
- `PP-734`: Portal link is not supported
- `PP-733`: Orchestrator fails to mount /var/run/docker.sock
- `PP-732`: bwlogs does not have permissions to mount the fluentd socket
- `PP-731`: Stop using multiple networks
- `PP-730`: Unsupported log driver fluentd
- `PP-729`: Install procedure on a podman environment
- `PP-726`: New simplified Tomcat Add-On pipeline
- `PP-724`: xargs command not found in the bwauth log file
- `PP-723`: Allow to modify the values of JAVA_OPTS when running the batch
- `PP-722`: Update base image for bwextract
- `PP-720`: Pipeline - Bump version to 1.5
- `PP-717`: The collection of objectclass in the collector line of generic ldap are not truly multivalued
- `PP-716`: [New] AI Steward service - Add Ingress
- `PP-715`: Update bwingress image
- `PP-712`: Upgrade APISIX and Keycloak to latest version
- `PP-711`: [New] Portal WS should be accessible to other services without authentication
- `PP-709`: Favicon KO when login to the IA interface, keycloak favicon instead of Radiant
- `PP-708`: [New] Integrate ServiceNow CMDB connector
- `PP-706`: [New] AI Steward service
- `PP-705`: [New] Allow to remove an account from a group in IDDM
- `PP-704`: [New] Allow to deactivate an account in IDDM
- `PP-696`: [DSM] Azure AD : issues with EVERYONE_EXCEPT_EXTERNAL_USERS group
- `PP-693`: [DSM] Files uploaded during auto discover or test datasource remain forever on container and are accessible to connected users
- `PP-676`: Improve error logs when user does not have at least the user role (401)
- `PP-671`: [Config] Be able to set a default git config on a compose deployment
- `PP-651`: [Config] Add an option to modify the mem_limit allocated to the controller
- `PP-546`: Add an option to break reconciliations
- `PP-428`: Corrupted file management in importfiles is not possible outside of the volume
