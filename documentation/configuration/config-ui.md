---
layout: page
title: "Identity Analytics configuration interface"
parent: "Configuration"

toc: true
---

# Identity Analytics configuration interface

## First connection

Upon first connection to the configuration interface `<hostname>/config`, you will be redirected to the login page.

![Login Page](images/config-interface/login.png)

first connect using the default setup account and update your password.  

Once logged in you will be redirected to the configuration home page which displays all configuration sections and their warnings. Click on git section to go to the configuration page.

> [!warning] Do not forget to click on `Save` to make your changes effective. If you navigate to a new page before clicking save you will loose all applied configuration.  

We recommend to restart the service after each configuration section to isolate possible errors.

## Header Menu

![Header Menu](images/config-interface/header-menu.png)

### Change language

You can change the language of the configuration interface by clicking on the language icon `fr` or `en` in the header menu.  

### Restart services

You can restart the services by clicking on the restart icon in the header menu.  

This will restart the following services:  

* `bwportal`
* `bwdatabase`
* `bwbrainwavedb`
* `bwauth`
* `bwcontroller`

> **Note:** The restart of the services can take a few minutes. Only modified services will be restarted.

### Logout

You can logout by clicking on the logout icon in the header menu.

## Pages

![Sidebar Menu](images/config-interface/sidebar-menu.png)

### Home

![Home Page](images/config-interface/home.png)

This screen show all the configuration sections and their warnings. If configuration settings are missing or incorrect, an orange box and a warning sign will appear.

### Configuration sections

When you click on a configuration section, you will be redirected to the configuration page of this section.  

Please note that:  

* You need to click on `Save` to make your changes effective.
* You can't save an invalid configuration.
* For most settings, a service restart is required to make your changes effective.
* If a field is required or invalid, a red box and a warning will appear.
* if a password is missing, an orange box will surround the field.

#### Global

![Global Page](images/config-interface/global.png)

* Domain name: The domain name of the server. It can only be changed through Identity Analytics CLI.  
* Timezone: The timezone of the server.  
* TLS enabled: Enable or disable TLS. If you activate TLS, you will have to provide a certificate and a key. Please refer to [SSL configuration page](/configuration/ssl-configuration) for more information.  
* Debug mode enabled: Enable or disable debug mode. This will add some logs. It shouldn't be used in production.  
* Batch memory: The max memory allocated to the batch.  
* Portal memory: The max memory allocated to the portal.  

#### Database

##### Internal

![Database Internal Page](images/config-interface/db_internal.png)

* Expose database: Expose the database port outside of the container.
* Exposure port: The port used to expose the database.
* Internal database password: The password of the database. Click on `Copy password` to copy the password in your clipboard.

##### External

![Database External Page](images/config-interface/db_external.png)

* Database driver: The driver used to connect to the database. Please note that if you are using another database than postgres, you will have to provide the JDBC driver.  
* Hostname: The hostname of the database.
* Port: The port of the database.
* Database name: The name of the database.  
* Microsoft SQL Server allows the configuration of **JDBC connection string options:** Those options will be added to the end of JDBC connection string. It could be used to set some ssl parameters for example.
* Postgres allows the use of **auto init:**. If the database doesn't exist, it will be created.
* Usernames: The usernames used to connect to your different schemas.
* Passwords: The passwords used to connect to your different schemas.

#### Git project

##### Without proxy

![Git Project Page](images/config-interface/git.png)

* Git project URL: The URL of the git project.
* Branch: The branch of the git project.
* Username: The username used to connect to the git project.
* Password: The **access token** used to connect to the git project.
* Project directory: the name of the project's directory on the **git repo**, for example `brainwave`:  
  ![Project directory](images/config-interface/repository_project_folder_name.png)
* Connection test: This button will test the connection to the specific branch of the git project with the provided credentials. It is highly recommended to test your connection before saving the configuration.

##### With proxy

Enabling this option will route Git traffic through an HTTP proxy. Useful if accessing Git over a restricted network or to increase security.

![Git Project Page](images/config-interface/git_proxy.png)

* Use http proxy: Enable or disable the use of http proxy.
* Proxy host: The host of the proxy.
* Proxy port: The port of the proxy.
* Proxy requires authentication: Enable or disable the authentication to the proxy.
* Proxy username: The username used to connect to the proxy.
* Proxy password: The password used to connect to the proxy.

#### Mail

##### SMTP Settings

![SMTP Page](images/config-interface/smtp.png)

* Enable email sending: Enable or disable the email sending.
* SMTP Domain Name: The domain name of the SMTP server.
* Port: The port of the SMTP server.
* Use SMTP authentication: Enable or disable the SMTP authentication.
* Username: The username used to connect to the SMTP server.
* Password: The password used to connect to the SMTP server.
* Sender email address: The email address used to send the emails.
* Sender name: The name used to send the emails.

##### Portal and Batch emails Settings

* Send portal and batch emails: Enable or disable the email sending by the portal and the batch.
* Redirect email to: The email address to where portal and batch the emails will be redirected.
* Email subject prefix: The prefix of the email subject.
* Recipients in attachment: Enable or disable the recipients in attachment. If false, the receiver will not know who received the email other than him.
* Maximum number of emails per session: The maximum number of emails per session. If set to 0, there will be no limit.
* Split size (in megabytes): The split size in megabytes. If the size of the email is greater than the split size, the email will be split in multiple emails. This Field has to be formatted like this `numberM`. e.g `10M`, `4M`, `16M`

#### Batch

![Batch Page](images/config-interface/batch.png)

* Technical configuration name: The name of the technical configuration used by the batch.

#### Scheduling

![Scheduling Page](images/config-interface/scheduling.png)

##### Task automation settings

Here you can configure the frequency when the tasks will be executed.

For each container (Batch, extraction and portal), you can configure the frequency on the left side of the box. On the right side of the box, you can see th corresponding cron expression.

* Secure portal stop timeout (ms): When the portal is stopped, it closes securely by allowing time for ongoing tasks to complete. This parameter sets the waiting time before forcing the portal to stop.

### Uploads

![Uploads Page](images/config-interface/uploads.png)

This page allows you to upload your Identity Analytics license file.

> **Note:** The license file has to contain `.lic` extension. (e.g. `brainwave.lic`)

If a license file is already uploaded, you can delete it by clicking on the `Delete` button.  
