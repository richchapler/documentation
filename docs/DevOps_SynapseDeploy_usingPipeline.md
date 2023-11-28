# Deploy Synapse Changes using a DevOps Pipeline

## Prepare Environment

* [DevOps](https://dev.azure.com/) with Organization, Project, Repository (dedicated to Synapse), and Branches "DEV" and "QA"
* [Key Vault](https://learn.microsoft.com/en-us/azure/key-vault) ???
* "DEV" Environment
  * [Synapse](Infrastructure_Synapse.md)
    * Git Configuration... Repository Type: Azure DevOps Git | Collaboration Branch "DEV"
    * Integration Runtime "DEV" on a machine with [SQL Server](https://www.microsoft.com/en-us/sql-server/sql-server-downloads) / Database "DEV"
* "QA" Environment
  * [Synapse](Infrastructure_Synapse.md)
    * Git Configuration... Repository Type: Azure DevOps Git | Collaboration Branch "QA"
    * Integration Runtime "QA" on a machine with [SQL Server](https://www.microsoft.com/en-us/sql-server/sql-server-downloads) / Database "QA"