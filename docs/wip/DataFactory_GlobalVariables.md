# Azure Data Factory: "Global Variables"

While Azure Data Factory (ADF) doesn't support global variables in the traditional sense, there are various work-arounds:

| Method | Comment | Feasibility |      
| :--- | :--- | :--- |      
| Pipeline / Data Flow | Unidirectional {i.e., parent >> child only} | 50% |      
| SQL Database | Additional latency / complexity | 100% |      
| Blob Storage | Additional latency / complexity | 100% |   
   
## ...via Pipeline

### Step 1: Create `GlobalVariable_Parent` Pipeline
  
* Navigate to Data Factory Studio >> Author
* Create a pipeline and name it `GlobalVariable_Parent`
* Add a new variable named `globalVariable` of type Array with default value `["Initial Value"]`

### Step 2: Create `GlobalVariable_Child` Pipeline
  
* Create another pipeline and name it `GlobalVariable_Child`
* Add a new parameter named `input` of type Array (with no default value)
* Add a new variable named `localVariable` of type Array (with no default value)
* Add a 'Set Variable' activity to the pipeline canvas with settings:
  * Name: `localVariable`
  * Value: `@pipeline().parameters.input`
* Add a 'Web' activity to the pipeline canvas, name it `Web_Original` and change default settings:
  * URL: `http://httpbin.org/post`
  * Method: POST
  * Body: `@variables('localVariable')`
* Create a success dependency from the 'Set Variable' activity to the 'Web' activity

### Step 3: `GlobalVariable_Parent` Pipeline + 'Execute Pipeline'

* Return to the `GlobalVariable_Parent` pipeline
* Add a 'Execute Pipeline' activity to the pipeline canvas with settings:
  * Invoked Pipeline: `GlobalVariable_Child`
  * Wait on Completion: checked
  * Parameters >> `input` >> Value: `@variables('globalVariable')`

### Step 4: `GlobalVariable_Child` Pipeline + `Append Variable`
  
* Return to the `GlobalVariable_Child` pipeline
* Add a 'Append Variable' activity to the pipeline canvas with settings:
  * Name: `localVariable`
  * Value: `New Value`
* Add a 'Web' activity to the pipeline canvas, name it `Web_Changed` and change default settings:
  * URL: `http://httpbin.org/post`
  * Method: POST
  * Body: `@variables('localVariable')`
* Create a success dependency from the 'Set Variable' activity to the 'Web' activity
  
### Step 5: `GlobalVariable_Parent` Pipeline >> `GlobalVariable_Child` Pipeline  
  
Unfortunately, Azure Data Factory does not natively support passing values from a child pipeline back to a parent pipeline using output parameters. The scope of variables and parameters is limited to the pipeline in which they are defined.

The common workaround for this limitation is to use an external service such as Azure Key Vault, Azure Blob Storage, or a database to temporarily store the values that need to be passed between pipelines, as I mentioned in the previous response.

Another possible workaround is to restructure your pipelines so that all the necessary data transformations are done within a single pipeline, thus avoiding the need to pass data between pipelines.

_Note: The same limitation applies to Data Flows. Data Flows do not support output parameters and cannot pass values back to the parent pipeline._

## ...via SQL Database  
   
### Step 1: Create a SQL Database  
   
* Navigate to the Azure portal and create a new SQL Database.  
* Create a table to store the global variables. The table should have at least two columns: one for the variable name and one for the variable value.  
   
### Step 2: Create `GlobalVariable_Parent` Pipeline  
   
* Navigate to Data Factory Studio >> Author.  
* Create a pipeline and name it `GlobalVariable_Parent`.  
* Add a 'Lookup' activity to the pipeline canvas. Configure it to retrieve the value of the global variable from the SQL Database.  
* Add a 'Set Variable' activity to the pipeline canvas. Configure it to set the value of a pipeline variable to the value retrieved by the 'Lookup' activity.  
   
### Step 3: Create `GlobalVariable_Child` Pipeline  
   
* Create another pipeline and name it `GlobalVariable_Child`.  
* Add a new parameter named `input` of type String (with no default value).  
* Add a 'Set Variable' activity to the pipeline canvas. Configure it to set the value of a pipeline variable to the value of the `input` parameter.  
* Add a 'SQL Stored Procedure' activity to the pipeline canvas. Configure it to update the value of the global variable in the SQL Database.  
   
### Step 4: `GlobalVariable_Parent` Pipeline + 'Execute Pipeline'  
   
* Return to the `GlobalVariable_Parent` pipeline.  
* Add a 'Execute Pipeline' activity to the pipeline canvas. Configure it to invoke the `GlobalVariable_Child` pipeline and pass the value of the global variable as a parameter.  
   
### Step 5: `GlobalVariable_Child` Pipeline + 'Lookup'  
   
* Return to the `GlobalVariable_Child` pipeline.  
* Add a 'Lookup' activity to the pipeline canvas. Configure it to retrieve the updated value of the global variable from the SQL Database.  
   
With this setup, the `GlobalVariable_Parent` pipeline can pass the value of the global variable to the `GlobalVariable_Child` pipeline, which can then update the value of the global variable in the SQL Database. The updated value can be retrieved by the `GlobalVariable_Parent` pipeline in subsequent runs.