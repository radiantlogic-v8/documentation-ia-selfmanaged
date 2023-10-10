---
title: Data purge
description: Identity Analytics self-managed solution guide to purge data
---

# Purging timeslots

Two mechanisms are involved regarding the purge of timeslots:  

1. Hidden timeslots
2. Purge policy

> Both are run in this order when a new timeslot is launched by a cron job  
> It is not run if the timeslot is launched manually via the portal  

## Hidden timeslots

Hidden timeslots will be purged automatically after the next ingestion (timeslot creation).  
You can hide a timeslot directly from the webportal:  

* Navigate to `Datasource Management` > `History`
* Select the `Timeslots` tab
* Select the desired timeslot
* Click the `...` menu
* Select `Hide timeslot in the Web Portal`

![Hide a timeslot](images/hide_timeslot.png)  

## Purge Policy

If a purge policy is defined in the technical configuration, it will be run automatically after each ingestion (timeslot creation).  

> See [Define the purge](/configuration/purge-timeslots-policy/#define-the-purge) to learn how to configure the purge in your technical configuration.  

Be aware that there is already a purge policy in the default `docker` technical configuration that comes with the self-managed project:  
![Docker configuration default purge](images/docker_conf_default_purge.png)  
