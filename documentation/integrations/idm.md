---
layout: page
title: "Identity Data Management Integration"
parent: "Integrations"
---

# Identity Data Management Integration

Integrating Identity Data Management as a data source for Identity Analytics can be achieved using the Generic Bridge LDAP Connector.

## Declaring the Datasource

Once inside the Identity Analytics Portal, expand the left menu and select the *Configure* option in the *Datasource Management* Section

![Datasource Management](images/radiantone_datasource_configure.png "Datasource Management")

In this section we can see the list of datasources if any has been declared. The list will be empty if no datasource has been declared. Click on the *Add* button to declare a new datasource.

![Add Datasource](images/radiantone_datasource_add.png "Add Datasource")

A wizard to declare a new datasources will pop up. Select the *Generic bridge - LDAP* from the list.  

![Add Generic LDAP](images/radiantone_datasource_generic_ldap.png "Add Generic LDAP")

After clicking on Next, provided the required information in order to connect to the Identity Data Management LDAP URL. We will find:

- **LDAP Server URL**
- **Login**
- **Password**
- **Search base** used for the data extraction
- **Search filter** used to select the LDAP objects that will be extracted
- **Attributes** to extract, leave this empty to extract all available attributes  

![Generic LDAP Params](images/radiantone_datasource_genericldap_params.png "Generic LDAP Params")

Click on Next to schedule the extraction. You can for example extract data once a week or every day.

![Generic LDAP Schedule](images/radiantone_datasource_genericldap_schedule.png "Generic LDAP Schedule")

Click on *Next* for to reach the last step of the wizard and then *Finish* to confirm the datasource declaration.

![Generic LDAP Finish](images/radiantone_datasource_genericldap_finish.png "Generic LDAP Finish")

## Declaring Multiple Datasources

If we have multiple Identity Data Management URLs or different *Search Bases* to extract, you can repeat the process to declare one datasource by URL or by *Search Base*. Keep in mind that each datasource must have a unique datasource name.

## Testing the Data Extraction

Each declared datasource will have a corresponding connector created in the Controller.

You can reach the Controller using the URL `/controller`. Then click on the *Connectors* item in the menu that you will find on the left.

![Controller Connectors](images/radiantone_controller_connectors.png "Controller Connectors")

Click on the connector to see more details.

![Controller Connectors Details](images/radiantone_controller_details.png "Controller Connectors Details")

From here, you can use the button *Submit a new test execution*, this will trigger the data extraction process. It will take a few minutes.

When the data extraction test process has finished, the details page will automatically refresh to display the test results.

![Controller Connectors Tests](images/radiantone_controller_testresults.png "Controller Connectors Tests")

You can download the test results to verify the extracted files.  

For more information on the controller pleaser refer to the dedicated documentation:

[Controller](/containers/controller)
