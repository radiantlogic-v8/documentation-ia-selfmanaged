title: Break reconciliations 
description: Feature that allows the batch to break unwanted reconciliations during the Batch
---

# Break reconciliations

As of version 1.5 a new feature has been implemented that allows the project administrator to create a view that provides the list of reconciliations to break

> [!warning] This process is systematically executed, regardless whether the batch was scheduled or executed manually.

## Procedure

If non existant, add in the project a view with the identifier `bwdsm_reconciliation_break`. This view will be automatically used to create a csv file containing the account identifier and the repository identifier.  

The columns **must** be named as following in order to be taken into account:

- account_identifier: for the account identifier
- repository_code: for the repository code of the account

> The result of the execution of the view is kept in the importfiles volume to allow debugging if necessary.
