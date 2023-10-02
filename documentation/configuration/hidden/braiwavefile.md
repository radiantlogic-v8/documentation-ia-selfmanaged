---
layout: page
title: "Brainwavefile configuration"
nav_exclude: true
search_exclude: true
toc: true
---

## Global parameters

```yaml
globals:
  hostname: localhost
  timezone: Europe/Paris
  tls: false
```

Modifications to any of the attributes in the globals config section requires a full stop and start of the services.  
To do so:

- `brainwave stop`
- `brainwave start`

## Database configuration

### Internal Database

To deploy an internal PostgreSQL database as a containerized service, use this config in the `Brainwavefile`

```yaml
database:
  type: internal
```

The internal database is already initialized with the 3 schemas ( igrc , activiti and auditlog ). So its ready to use out of the box.

#### Connection

To expose the postgresql port of the internal database, it is possible to set the option `expose_port` :

```yaml
database:
  type: internal
  expose_port: 5432
```

You can use any available port, and it will be redirected the internal PostgreSQL port. There is a single user in this database: `igrc`

To obtain the password that allows to connect using the user `igrc`, go to the config UI at `<hostname>/config`. Search for the secret id `BW_DATABASE_INTERNAL_SECRET` in the Secrets Manager.

Internal database name is `brainwave`.

Alternatively, to access the internal database, when the `debug services` are activated. An instance of pgadmin is accesible at `<hostname>/pgadmin` and the internal database will be pre-configured. 

### External Database

To prepare an external PostgreSQL database:

- Create 3 users: `igrc` , `activiti` and `auditlog`
- Create 1 schema by user that matches the user name. Each user should own its corresponding schema
  - the `igrc` user should own the `igrc` schema
  - the `activiti` user should own the `activiti` schema
  - the `auditlog` user should own the `auditlog` schema

- Declare the database information in the `Brainwavefile`. Here an example:

```yaml
database:
  type: external
  hostname: myhostname
  port: 5432
  dbname: brainwave
  driver: postgresql
  ledger:
    username: igrc
    password: $(DB_LEDGER_SECRET)
  workflow:
    username: activiti
    password: $(DB_WORKFLOW_SECRET)
  auditlog:
    username: auditlog
    password: $(DB_AUDITLOG_SECRET)
```

> **Warning** hosting the database in the same machine than runs the Docker runtime is not supported.

- Visit the Configuration UI at `<hostname>/config` , to provide the values for the secrets: `DB_LEDGER_SECRET` , `DB_WORKFLOW_SECRET` and `DB_AUDITLOG_SECRET` using the secrets manager

#### Database initialization

There is an option to initialize the external database automatically. The option is called `auto_init` and its disabled by default. Add it to the Brainwavefile like this:

```yaml
database:
  type: external
  hostname: myhostname
  port: 5432
  dbname: brainwave
  driver: postgresql
  auto_init: true
  ledger:
    username: igrc
    password: $(DB_LEDGER_SECRET)
  workflow:
    username: activiti
    password: $(DB_WORKFLOW_SECRET)
  auditlog:
    username: auditlog
    password: $(DB_AUDITLOG_SECRET)
```

> **Warning** this option is in beta mode, use for testing environments only


## IGRC Project from GIT

The igrc project that is deployed in both portal and batch containers is taken from a git repository.

A full mirror of the git repository is cloned and maintained in a volume. This speeds up the process of taking the most recent version when restarting the portal or starting the batch.

If the configuration of the IGRC project is not complete, the portal will not start. Go to `<hostname>/config` to verify that the remote git is well configured and that the password is set in the `Secrets Manager`.

Configuration looks like this:

```yaml
igrc_project:
  git:
    url: https://bitbucket.org/ericbacher/pkg_iap.git
    username: myusername
    password: $(IGRC_PROJECT_GIT_SECRET)
    project_dir: projectname
    branch: uat
```

> Only the **https** protocol is supported

For this example the clone command will look like

```
git clone https://myusername:mypassword@bitbucket.org/ericbacher/pkg_iap.git
```

Make sure to provide a value for secret `IGRC_PROJECT_GIT_SECRET` using the `Secrets Manager`.

The specified branch is polled regularly to check for new commits. See the **Scheduling** section for more information.

Restarting the portal manually is also possible using the command:

```yaml
brainwave portal restart
```

> Note that restarting the portal both manually and scheduled results in a service interruption. Service interruption should be estimated to 40 minutes.


## Scheduling

The scheduling section of the config allows to set the automated execution of 3 tasks:

- batch execution
- extraction executions
- portal monitoring

Cron expressions are used to configure the frequency of each of the tasks. Refer to a tool like [crontab.guru](https://crontab.guru/) as a guide for cron expressions.

```yaml
scheduling:
  igrc:
    batch: "0 1 * * *"     ## every day at 1 am
    extract: "0 * * * *"   ## every hour
  monitoring:
    portal: "0 1 * * *"    ## every day at 1 am
```

### Batch Scheduling

Any valid cron expression is supported. When the task is executed, a batch execution request is sent to the controller. The controller will register the request as coming from principal 'scheduler'.

Scheduled batch requests will be visible from the controller UI.

### Extraction Scheduling

Any valid cron expression is supported. When the task is executed, the controller will receive a request to check if any of the declared connectors requires an extraction.

> Remember that the scheduling of each connector is configured indidually.
> During the extraction scheduling, **only** the connectors that are scheduled will be executed.

### Portal Monitoring

The goal of this task is to monitor the IGRC project git repository to check for changes in the project. If changes are detected, the portal is restarted.

Only simple cron expressions are supported here ( `,` `-` and `/` and any `non-standard` expressions are **not** supported ). When the action is executed , 2 tasks are triggered:

- Update the local mirror of the git repository that contains the project
- Check for changes and restart the portal if changes are found

There is a `5` minutes delay between the 2 tasks.

> Note that when the portal is restarted , there will be a service interruption. Keep that in mind when configuring the scheduling of this task. Service interruption should be estimated to last 40 minutes

#### Portal Unload Delay

The portal can be requested to stop the webapps, this will happen either manually when using the command `brainwave stop` , either automatically as a result of the portal monitoring scheduling.

`Unload Delay` is the allowed time that the portal can take to stop all the services. After this delay, the portal can be **killed**.

By default, the unload delay is set to `20 minutes` . This value is considered to be enough for *most* cases. However, in some projects that handle long and complicated workflows, the portal can take longer than that to stop properly. For this reason, it is possible to override the default value of the `Unload Delay`:

```yaml
scheduling:
  igrc:
    batch: "0 1 * * *"
    extract: "0 * * * *"
  monitoring:
    portal: "0 1 * * *"
      unload_delay: 1200000    ## value in milliseconds
```

> Note that the value is in milliseconds

## IGRC Batch

In this section we can configure the name of the IGRC technical configuration to be used when executing the batch

```yaml
igrc_batch:
  technical_configuration:
    name: default
```

## Mail

This section allows to configure the mail options that will end up in the `mail.properties` file for both portal and batch.

```yaml
mail:
  smtp:
    username: user
    password: $(SMTP_SECRET)
    port: 25
    host: smtphostname
  redirect_to: mail@domain
```

## Debug

When the debug mode is activated, we deploy these services

- pgadmin ( available at `<hostname>/pgadmin` )
- smtp4dev ( available at `<hostname>/smtp4dev` )

```yaml
debug:
  services:
    enabled: false  ## true or false
```

Modifications to this flag requires a full stop and start of the services.