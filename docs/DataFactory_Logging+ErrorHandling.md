# Data Factory: Logging + Error Handling

## Studio >> Monitor: Pipeline and Trigger Runs

* Navigate to Azure Data Factory >> Studio >> Monitor >> Pipeline Runs
* Review filter settings {e.g., date, name, status, etc.}
* Click into a specific log entry and review
* Repeat for Trigger Runs... note that a Trigger Run might have "Succeeded" even though the corresponding Pipeline Run failed

## Studio >> Monitor: Alerts & Metrics
Create alerts to notify you via email, SMS, or other means when a pipeline run meets certain conditions, such as failure or success.

 * Navigate to Azure Data Factory >> Studio >> Monitor >> Alerts & Metrics
 * Click "New alert rule" and complete the resulting pop-out form, including:

   Label | Value
   :----- | :-----
   Severity | Sev0 = most severe... Sev5 = least severe
   Add criteria | Select and configure the desired Metric on the resulting pop-out<br><sub>Metric descriptions included in Appendix</sub>
   Configure Notification | Define "action group" {i.e., notification action and recipient

## Portal >> Monitoring >> Diagnostic Settings

* Navigate to Azure Data Factory >> Monitoring >> Diagnostic Settings
* Click "+ Add diagnostic setting" and complete the resulting screen, including:

   Label | Value
   :----- | :-----
   Logs | Select and configure the desired Log Categories<br><sub>Category descriptions included in Appendix</sub>
   Destination Details | Select and configure the desired Log Categories<br><sub>Destination descriptions included in Appendix</sub><br><sub>Note that destination selection expands the interface and necessitates additional entries</sub>

### Destination: Log Analytics

Selection of "Send to Log Analytics workspace" necessitates selection of "Destination table":
   
   * **Azure Diagnostics** - When you choose "Azure Diagnostics", **all your diagnostic logs from all resource types are sent to a single table called AzureDiagnostics**. This can make querying easier if you want to correlate events across different resource types, as you only need to query one table. However, the AzureDiagnostics table is a flat table, so complex properties are serialized into JSON strings which can make some data harder to work with.  
   * **Resource Specific** - When you choose "Resource Specific", your **logs are sent to a table that corresponds to the resource type**. For example, if you're working with a Storage Account, your logs might be sent to a table like StorageAccountLogs. These tables are designed to best fit the schema of the log data of the specific resource type, and complex properties are represented as distinct fields, which can make queries more intuitive and the data easier to work with. However, if you want to correlate events across different resource types, you would need to query multiple tables.  

Be sure to click "Save".

## Portal >> Monitoring >> Logs

### Sample #1: `AzureDiagnostics`

* Navigate to Azure Data Factory >> Monitoring >> Logs
* Close the "Welcome to Log Analytics" and "Queries hub" popups  
* In the query window, paste this very simple starter KQL query:  
   
   ```kql  
   AzureDiagnostics   
   ```  
   
* Click "Run" to execute the query.  

### Sample #2: `PipelineRuns`

* Repeat with the following KQL:

   ```kql  
   AzureDiagnostics    
   | where ResourceProvider == "MICROSOFT.DATAFACTORY" and Category == "PipelineRuns"    
   | where TimeGenerated > ago(1d)    
   | order by TimeGenerated desc       
   ```

## Portal >> Monitoring >> Alerts

* Navigate to Azure Data Factory >> Monitoring >> Alerts
* Click "Create" and select "Alert rule" from the resulting dropdown
* Complete the resulting "Create an alert rule" page, including:

   Label | Value
   :----- | :-----
   Signal name | Select and configure the desired Signal<br><sub>Signal descriptions included in Appendix</sub>

* Review additional tabs {e.g., Scope, Actions, Details, Tags}, then "Review + create"

## Custom Logging: Pipeline / Activity failures and error handling

Create a New Pipeline:

Open Azure Data Factory Studio.
Click on the "Author" tab and then click on the "+" button to create a new pipeline.
Add Activities to the Pipeline:

Drag and drop the activities you want to include in your pipeline from the Activities pane to the pipeline canvas. For example, you might have a Copy Data activity that copies data from one source to another.
Configure the "onFail" Feature:

Click on the activity for which you want to configure the "onFail" feature.
In the "Activities" pane, click on the "Add activity" button under the "onFail" section.
Select the activity you want to execute when the primary activity fails. For example, you can use a Stored Procedure activity to log the failure information to a SQL Server table.
Set Up the Stored Procedure Activity:

Drag and drop a Stored Procedure activity onto the pipeline canvas.
Configure the Stored Procedure activity to connect to your SQL Server database.
Specify the stored procedure that will log the failure information. The stored procedure should have parameters to accept the necessary log information, such as the error message, activity name, and timestamp.
Link the Activities:

Link the primary activity to the Stored Procedure activity using the "onFail" dependency.
Ensure that the pipeline is configured to execute the Stored Procedure activity only when the primary activity fails.
Publish and Test the Pipeline:

Click on the "Publish" button to save and publish your pipeline.
Trigger the pipeline to test its execution. If the primary activity fails, the "onFail" feature should execute the Stored Procedure activity and log the failure information to the SQL Server table.

## Appendix

### Azure Data Factory >> Alert Metrics

| Metric | Description |  
| --- | --- |  
| Airflow Integration Runtime Celery Task Timeout Error | Number of Celery tasks that have timed out. |  
| Airflow Integration Runtime Collect DB Dags | Number of DAGs collected from the database. |  
| Airflow Integration Runtime Cpu Percentage | CPU usage percentage of the Airflow Integration Runtime. |  
| Airflow Integration Runtime Cpu Usage | CPU usage of the Airflow Integration Runtime. |  
| Airflow Integration Runtime Dag Bag Size | Size of the DAG bag in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Dag Callback Exceptions | Number of exceptions occurred during DAG callbacks. |  
| Airflow Integration Runtime DAG File Refresh Error | Number of errors occurred during DAG file refresh. |  
| Airflow Integration Runtime DAG Processing Import Errors | Number of import errors occurred during DAG processing. |  
| Airflow Integration Runtime DAG Processing Last Duration | Duration of the last DAG processing. |  
| Airflow Integration Runtime DAG Processing Last Run Seconds Ago | Time in seconds since the last DAG processing run. |  
| Airflow Integration Runtime DAG Processing Processes | Number of processes used for DAG processing. |  
| Airflow Integration Runtime DAG Processing Processor Timeouts | Number of processor timeouts during DAG processing. |  
| Airflow Integration Runtime DAG Processing Total Parse Time | Total parse time for DAG processing. |  
| Airflow Integration Runtime DAG ProcessingManager Stalls | Number of stalls in the DAG ProcessingManager. |  
| Airflow Integration Runtime DAG Run Dependency Check | Number of dependency checks run for DAGs. |  
| Airflow Integration Runtime DAG Run Duration Failed | Duration of failed DAG runs. |  
| Airflow Integration Runtime DAG Run Duration Success | Duration of successful DAG runs. |  
| Airflow Integration Runtime DAG Run First Task Scheduling Delay | Delay in scheduling the first task of a DAG run. |  
| Airflow Integration Runtime DAG Run Schedule Delay | Delay in scheduling a DAG run. |  
| Airflow Integration Runtime Executor Open Slots | Number of open slots in the executor. |  
| Airflow Integration Runtime Executor Queued Tasks | Number of tasks queued in the executor. |  
| Airflow Integration Runtime Executor Running Tasks | Number of tasks running in the executor. |  
| Airflow Integration Runtime Heartbeat Failure | Number of heartbeat failures in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Job End | Number of jobs ended in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Job Start | Number of jobs started in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Memory Percentage | Memory usage percentage of the Airflow Integration Runtime. |  
| Airflow Integration Runtime Memory Usage | Memory usage of the Airflow Integration Runtime. |  
| Airflow Integration Runtime Node Count | Number of nodes in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Operator Failures | Number of operator failures in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Operator Successes | Number of operator successes in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Pool Open Slots | Number of open slots in the pool. |  
| Airflow Integration Runtime Pool Queued Slots | Number of queued slots in the pool. |  
| Airflow Integration Runtime Pool Running Slots | Number of running slots in the pool. |  
| Airflow Integration Runtime Pool Starving Tasks | Number of starving tasks in the pool. |  
| Airflow Integration Runtime Scheduler Critical Section Busy | Number of times the scheduler critical section was busy. |  
| Airflow Integration Runtime Scheduler Critical Section Duration | Duration of the scheduler critical section. |  
| Airflow Integration Runtime Scheduler Failed SLA Email Attempts | Number of failed attempts to send SLA emails by the scheduler. |  
| Airflow Integration Runtime Scheduler Heartbeats | Number of heartbeats from the scheduler. |  
| Airflow Integration Runtime Scheduler Orphaned Tasks Adopted | Number of orphaned tasks adopted by the scheduler. |  
| Airflow Integration Runtime Scheduler Orphaned Tasks Cleared | Number of orphaned tasks cleared by the scheduler. |  
| Airflow Integration Runtime Scheduler Tasks Executable | Number of executable tasks in the scheduler. |  
| Airflow Integration Runtime Scheduler Tasks Killed Externally | Number of tasks killed externally in the scheduler. |  
| Airflow Integration Runtime Scheduler Tasks Running | Number of running tasks in the scheduler. |  
| Airflow Integration Runtime Scheduler Tasks Starving | Number of starving tasks in the scheduler. |  
| Airflow Integration Runtime Started Task Instances | Number of task instances started in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Task Instance Created Using Operator | Number of task instances created using an operator. |  
| Airflow Integration Runtime Task Instance Duration | Duration of task instances in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Task Instance Failures | Number of task instance failures in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Task Instance Finished | Number of finished task instances in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Task Instance Previously Succeeded | Number of task instances that previously succeeded. |  
| Airflow Integration Runtime Task Instance Successes | Number of successful task instances in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Task Removed From DAG | Number of tasks removed from a DAG. |  
| Airflow Integration Runtime Task Restored To DAG | Number of tasks restored to a DAG. |  
| Airflow Integration Runtime Triggers Blocked Main Thread | Number of triggers that blocked the main thread. |  
| Airflow Integration Runtime Triggers Failed | Number of failed triggers in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Triggers Running | Number of running triggers in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Triggers Succeeded | Number of successful triggers in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Zombie Tasks Killed | Number of zombie tasks killed in the Airflow Integration Runtime. |  
| Cancelled activity runs metrics | Number of cancelled activity runs. |  
| Cancelled pipeline runs metrics | Number of cancelled pipeline runs. |  
| Cancelled SSIS integration runtime start metrics | Number of cancelled SSIS integration runtime starts. |  
| Cancelled SSIS package execution metrics | Number of cancelled SSIS package executions. |  
| Cancelled trigger runs metrics | Number of cancelled trigger runs. |  
| Copy available capacity percentage of MVNet integration runtime | Percentage of available capacity for copy operations in the MVNet integration runtime. |  
| Copy capacity utilization of MVNet integration runtime | Capacity utilization for copy operations in the MVNet integration runtime. |  
| Copy waiting queue length of MVNet integration runtime | Length of the waiting queue for copy operations in the MVNet integration runtime. |  
| Elapsed Time Pipeline Runs Metrics | Elapsed time for pipeline runs. |  
| External available capacity percentage of MVNet integration runtime | Percentage of available capacity for external operations in the MVNet integration runtime. |  
| External capacity utilization of MVNet integration runtime | Capacity utilization for external operations in the MVNet integration runtime. |  
| External waiting queue length of MVNet integration runtime | Length of the waiting queue for external operations in the MVNet integration runtime. |  
| Failed activity runs metrics | Number of failed activity runs. |  
| Failed pipeline runs metrics | Number of failed pipeline runs. |  
| Failed SSIS integration runtime start metrics | Number of failed SSIS integration runtime starts. |  
| Failed SSIS package execution metrics | Number of failed SSIS package executions. |  
| Failed trigger runs metrics | Number of failed trigger runs. |  
| Integration runtime available memory | Available memory in the integration runtime. |  
| Integration runtime available node count | Number of available nodes in the integration runtime. |  
| Integration runtime CPU utilization | CPU utilization in the integration runtime. |  
| Integration runtime queue duration | Duration of the queue in the integration runtime. |  
| Integration runtime queue length | Length of the queue in the integration runtime. |  
| Maximum allowed entities count | Maximum number of entities allowed. |  
| Maximum allowed factory size (GB unit) | Maximum size of the factory allowed in GB. |  
| Pipeline available capacity percentage of MVNet integration runtime | Percentage of available capacity for pipeline operations in the MVNet integration runtime. |  
| Pipeline capacity utilization of MVNet integration runtime | Capacity utilization for pipeline operations in the MVNet integration runtime. |  
| Pipeline waiting queue length of MVNet integration runtime | Length of the waiting queue for pipeline operations in the MVNet integration runtime. |  
| Stuck SSIS integration runtime stop metrics | Number of stuck SSIS integration runtime stops. |  
| Succeeded activity runs metrics | Number of successful activity runs. |  
| Succeeded pipeline runs metrics | Number of successful pipeline runs. |  
| Succeeded SSIS integration runtime start metrics | Number of successful SSIS integration runtime starts. |  
| Succeeded SSIS integration runtime stop metrics | Number of successful SSIS integration runtime stops. |  
| Succeeded SSIS package execution metrics | Number of successful SSIS package executions. |  
| Succeeded trigger runs metrics | Number of successful trigger runs. |  
| Total entities count | Total number of entities. |  
| Total factory size (GB unit) | Total size of the factory in GB. |  

### Azure Data Factory >> Diagnostic Setting >> Log Categories

| Category | Description |  
| --- | --- |  
| allLogs | Choose to send all types of logs {e.g., pipeline runs, trigger runs, activity runs, and more} |  
| Pipeline activity runs log | This log captures details about the execution of activities within a pipeline. |  
| Pipeline runs log | This log records information about each pipeline run, including its start time, end time, and status. |  
| Trigger runs log | This log captures information about trigger runs, which are instances where a trigger has fired to start a pipeline. |  
| Sandbox Pipeline runs log | This is a specialized log for capturing information about pipeline runs in a sandbox environment. |  
| Sandbox Activity runs log | This is a specialized log for capturing information about activity runs in a sandbox environment. |  
| SSIS package event messages | This log captures event messages from the execution of SQL Server Integration Services (SSIS) packages. |  
| SSIS package executable statistics | This log captures detailed statistical data about the execution of SSIS packages. |  
| SSIS package event message context | This log captures context information about event messages during the execution of SSIS packages. |  
| SSIS package execution component phases | This log records information about the different phases of execution for components within an SSIS package. |  
| SSIS package execution data statistics | This log captures data statistics from the execution of SSIS packages. |  
| SSIS integration runtime logs | This log records information about the runtime environment for SSIS packages. |  
| Airflow task execution logs | This log captures details about the execution of tasks within an Apache Airflow workflow. |  
| Airflow worker logs | This log records information about the operation of Apache Airflow workers. |  
| Airflow dag processing logs | This log captures information about the processing of Directed Acyclic Graphs (DAGs), which are the workflows defined in Apache Airflow. |  
| Airflow scheduler logs | This log records information about the operation of the Apache Airflow scheduler. |  
| Airflow web logs | This log captures information about the operation of the Apache Airflow web interface. | 

### Azure Data Factory >> Diagnostic Setting >> Destinations

| Destination | Description |  
| --- | --- |  
| Send to Log Analytics workspace | This option allows you to send the logs to a Log Analytics workspace. This is a cloud-based service for log data ingestion and storage. It provides real-time and historical analysis, visualizations, and insights for the data. |  
| Archive to a storage account | This option allows you to archive the logs to a storage account. This is useful for long-term storage and for compliance purposes. The logs can be retrieved and analyzed at a later date if needed. |  
| Stream to an event hub | This option allows you to stream the logs to an event hub. Event Hubs is a real-time data ingestion service that is capable of receiving and processing millions of events per second. You can use this option to integrate your logs with third-party services or custom analytics systems. |  
| Send to partner solution | This option allows you to send the logs to a partner solution. Azure has a number of partner solutions that provide advanced analytics and visualization capabilities. This option requires you to have a subscription with the partner solution. |  

### Azure Data Factory >> Alert Rules >> Signals

| Signal Name | Signal Type | Description |  
| --- | --- | --- |  
| Custom log search | Log Analytics | Allows you to perform a custom search on the logs. |  
| Activity Runs Availability | Log Analytics | Provides information about the availability of activity runs. |  
| PipelineRuns Availability | Log Analytics | Provides information about the availability of pipeline runs. |  
| TriggerRuns Availability | Log Analytics | Provides information about the availability of trigger runs. |  
| Resource health | Resource health | Provides information about the health of the resource. |  
| Airflow Integration Runtime Celery Task Timeout Error | Platform metrics | Number of Celery tasks that have timed out in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Collect DB Dags | Platform metrics | Number of DAGs collected from the database in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Cpu Percentage | Platform metrics | CPU usage percentage of the Airflow Integration Runtime. |  
| Airflow Integration Runtime Cpu Usage | Platform metrics | CPU usage of the Airflow Integration Runtime. |  
| Airflow Integration Runtime Dag Bag Size | Platform metrics | Size of the DAG bag in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Dag Callback Exceptions | Platform metrics | Number of exceptions occurred during DAG callbacks in the Airflow Integration Runtime. |  
| Airflow Integration Runtime DAG File Refresh Error | Platform metrics | Number of errors occurred during DAG file refresh in the Airflow Integration Runtime. |  
| Airflow Integration Runtime DAG Processing Import Errors | Platform metrics | Number of import errors occurred during DAG processing in the Airflow Integration Runtime. |  
| Airflow Integration Runtime DAG Processing Last Duration | Platform metrics | Duration of the last DAG processing in the Airflow Integration Runtime. |  
| Airflow Integration Runtime DAG Processing Last Run Seconds Ago | Platform metrics | Time in seconds since the last DAG processing run in the Airflow Integration Runtime. |  
| Airflow Integration Runtime DAG Processing Processes | Platform metrics | Number of processes used for DAG processing in the Airflow Integration Runtime. |  
| Airflow Integration Runtime DAG Processing Processor Timeouts | Platform metrics | Number of processor timeouts during DAG processing in the Airflow Integration Runtime. |  
| Airflow Integration Runtime DAG Processing Total Parse Time | Platform metrics | Total parse time for DAG processing in the Airflow Integration Runtime. |  
| Airflow Integration Runtime DAG ProcessingManager Stalls | Platform metrics | Number of stalls in the DAG ProcessingManager in the Airflow Integration Runtime. |  
| Airflow Integration Runtime DAG Run Dependency Check | Platform metrics | Number of dependency checks run for DAGs in the Airflow Integration Runtime. |  
| Airflow Integration Runtime DAG Run Duration Failed | Platform metrics | Duration of failed DAG runs in the Airflow Integration Runtime. |  
| Airflow Integration Runtime DAG Run Duration Success | Platform metrics | Duration of successful DAG runs in the Airflow Integration Runtime. |  
| Airflow Integration Runtime DAG Run First Task Scheduling Delay | Platform metrics | Delay in scheduling the first task of a DAG run in the Airflow Integration Runtime. |  
| Airflow Integration Runtime DAG Run Schedule Delay | Platform metrics | Delay in scheduling a DAG run in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Executor Open Slots | Platform metrics | Number of open slots in the executor in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Executor Queued Tasks | Platform metrics | Number of tasks queued in the executor in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Executor Running Tasks | Platform metrics | Number of tasks running in the executor in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Heartbeat Failure | Platform metrics | Number of heartbeat failures in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Job End | Platform metrics | Number of jobs ended in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Job Start | Platform metrics | Number of jobs started in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Memory Percentage | Platform metrics | Memory usage percentage of the Airflow Integration Runtime. |  
| Airflow Integration Runtime Memory Usage | Platform metrics | Memory usage of the Airflow Integration Runtime. |  
| Airflow Integration Runtime Node Count | Platform metrics | Number of nodes in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Operator Failures | Platform metrics | Number of operator failures in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Operator Successes | Platform metrics | Number of operator successes in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Pool Open Slots | Platform metrics | Number of open slots in the pool in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Pool Queued Slots | Platform metrics | Number of queued slots in the pool in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Pool Running Slots | Platform metrics | Number of running slots in the pool in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Pool Starving Tasks | Platform metrics | Number of starving tasks in the pool in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Scheduler Critical Section Busy | Platform metrics | Number of times the scheduler critical section was busy in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Scheduler Critical Section Duration | Platform metrics | Duration of the scheduler critical section in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Scheduler Failed SLA Email Attempts | Platform metrics | Number of failed attempts to send SLA emails by the scheduler in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Scheduler Heartbeats | Platform metrics | Number of heartbeats from the scheduler in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Scheduler Orphaned Tasks Adopted | Platform metrics | Number of orphaned tasks adopted by the scheduler in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Scheduler Orphaned Tasks Cleared | Platform metrics | Number of orphaned tasks cleared by the scheduler in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Scheduler Tasks Executable | Platform metrics | Number of executable tasks in the scheduler in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Scheduler Tasks Killed Externally | Platform metrics | Number of tasks killed externally in the scheduler in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Scheduler Tasks Running | Platform metrics | Number of running tasks in the scheduler in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Scheduler Tasks Starving | Platform metrics | Number of starving tasks in the scheduler in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Started Task Instances | Platform metrics | Number of task instances started in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Task Instance Created Using Operator | Platform metrics | Number of task instances created using an operator in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Task Instance Duration | Platform metrics | Duration of task instances in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Task Instance Failures | Platform metrics | Number of task instance failures in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Task Instance Finished | Platform metrics | Number of finished task instances in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Task Instance Previously Succeeded | Platform metrics | Number of task instances that previously succeeded in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Task Instance Successes | Platform metrics | Number of successful task instances in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Task Removed From DAG | Platform metrics | Number of tasks removed from a DAG in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Task Restored To DAG | Platform metrics | Number of tasks restored to a DAG in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Triggers Blocked Main Thread | Platform metrics | Number of triggers that blocked the main thread in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Triggers Failed | Platform metrics | Number of failed triggers in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Triggers Running | Platform metrics | Number of running triggers in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Triggers Succeeded | Platform metrics | Number of successful triggers in the Airflow Integration Runtime. |  
| Airflow Integration Runtime Zombie Tasks Killed | Platform metrics | Number of zombie tasks killed in the Airflow Integration Runtime. |  
| Cancelled activity runs metrics | Platform metrics | Number of cancelled activity runs. |  
| Cancelled pipeline runs metrics | Platform metrics | Number of cancelled pipeline runs. |  
| Cancelled SSIS integration runtime start metrics | Platform metrics | Number of cancelled SSIS integration runtime starts. |  
| Cancelled SSIS package execution metrics | Platform metrics | Number of cancelled SSIS package executions. |  
| Cancelled trigger runs metrics | Platform metrics | Number of cancelled trigger runs. |  
| Copy available capacity percentage of MVNet integration runtime | Platform metrics | Percentage of available capacity for copy operations in the MVNet integration runtime. |  
| Copy capacity utilization of MVNet integration runtime | Platform metrics | Capacity utilization for copy operations in the MVNet integration runtime. |  
| Copy waiting queue length of MVNet integration runtime | Platform metrics | Length of the waiting queue for copy operations in the MVNet integration runtime. |  
| Elapsed Time Pipeline Runs Metrics | Platform metrics | Elapsed time for pipeline runs. |  
| External available capacity percentage of MVNet integration runtime | Platform metrics | Percentage of available capacity for external operations in the MVNet integration runtime. |  
| External capacity utilization of MVNet integration runtime | Platform metrics | Capacity utilization for external operations in the MVNet integration runtime. |  
| External waiting queue length of MVNet integration runtime | Platform metrics | Length of the waiting queue for external operations in the MVNet integration runtime. |  
| Failed activity runs metrics | Platform metrics | Number of failed activity runs. |  
| Failed pipeline runs metrics | Platform metrics | Number of failed pipeline runs. |  
| Failed SSIS integration runtime start metrics | Platform metrics | Number of failed SSIS integration runtime starts. |  
| Failed SSIS package execution metrics | Platform metrics | Number of failed SSIS package executions. |  
| Failed trigger runs metrics | Platform metrics | Number of failed trigger runs. |  
| Integration runtime available memory | Platform metrics | Available memory in the integration runtime. |  
| Integration runtime available node count | Platform metrics | Number of available nodes in the integration runtime. |  
| Integration runtime CPU utilization | Platform metrics | CPU utilization in the integration runtime. |  
| Integration runtime queue duration | Platform metrics | Duration of the queue in the integration runtime. |  
| Integration runtime queue length | Platform metrics | Length of the queue in the integration runtime. |
| Maximum allowed entities count | Platform metrics | Maximum number of entities allowed. |  
| Maximum allowed factory size (GB unit) | Platform metrics | Maximum size of the factory allowed in GB. |  
| Pipeline available capacity percentage of MVNet integration runtime | Platform metrics | Percentage of available capacity for pipeline operations in the MVNet integration runtime. |  
| Pipeline capacity utilization of MVNet integration runtime | Platform metrics | Capacity utilization for pipeline operations in the MVNet integration runtime. |  
| Pipeline waiting queue length of MVNet integration runtime | Platform metrics | Length of the waiting queue for pipeline operations in the MVNet integration runtime. |  
| Stuck SSIS integration runtime stop metrics | Platform metrics | Number of stuck SSIS integration runtime stops. |  
| Succeeded activity runs metrics | Platform metrics | Number of successful activity runs. |  
| Succeeded pipeline runs metrics | Platform metrics | Number of successful pipeline runs. |  
| Succeeded SSIS integration runtime start metrics | Platform metrics | Number of successful SSIS integration runtime starts. |  
| Succeeded SSIS integration runtime stop metrics | Platform metrics | Number of successful SSIS integration runtime stops. |  
| Succeeded SSIS package execution metrics | Platform metrics | Number of successful SSIS package executions. |  
| Succeeded trigger runs metrics | Platform metrics | Number of successful trigger runs. |  
| Total entities count | Platform metrics | Total number of entities. |  
| Total factory size (GB unit) | Platform metrics | Total size of the factory in GB. |
