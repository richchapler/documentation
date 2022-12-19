# Data Acquisition: Mid-Stream Processing

![image](https://user-images.githubusercontent.com/44923999/208464152-33914e21-5ae5-49fc-8a9a-dc7dddcf0339.png)

## Consider Requirements
This solution considers the following requirements:

* "We stream millions of messages per hour from an Event Hub owned and controlled by another organization"
* "Each message requires special, mid-stream handling {e.g., format translation, decompression, re-packing of JSON, etc.}"

## Prepare Infrastructure
This solution requires the following resources:

* Data Explorer [**Cluster**](Infrastructure_DataExplorer_Cluster.md) and [**Database**](Infrastructure_DataExplorer_Database.md)
* [**Event Hubs**](Infrastructure_EventHub.md) >> Namespace :: Hub :: Consumer Group ... one to mimic the untouchable source and a second to mimic post-processing
* Function Apps ... one to mock data flowing through the untouchable source and a second to hand mid-stream processing
* [Visual Studio](https://visualstudio.microsoft.com/) with **Azure development** workload

## Exercise 1: Mock Untouchable Source
In this exercise, we will use a Function App to mock the flow of messages coming from the source Event Hub.

### Create Visual Studio Project

* Open Visual Studio

  <img src="https://user-images.githubusercontent.com/44923999/201695714-2c2be122-0b23-42ef-8a47-f71a4bc3ac76.png" width="800" title="Snipped: November 14, 2022" />

* Click "**Create a new project**"

  <img src="https://user-images.githubusercontent.com/44923999/201696928-02adc19a-6cfe-45b9-8271-eb71da588f0d.png" width="800" title="Snipped: November 14, 2022" />

* On the "**Create a new project**" page, search for and select "Azure Functions", then click **Next**

  <img src="https://user-images.githubusercontent.com/44923999/208470843-1698307e-c955-4c60-9173-798460baa275.png" width="800" title="Snipped: December 19, 2022" />

* Complete the "**Configure your new project**" form and then click **Next**

  <img src="https://user-images.githubusercontent.com/44923999/201709567-ea5c59be-014e-4f38-9781-582afd44edea.png" width="800" title="Snipped: November 14, 2022" />

* Complete the "**Additional information**" form, including:

  | Prompt | Entry |
  | ------ | ----- |
  | **Functions worker** | Select "**.NET 6.0 (Long Term Support)**" |
  | **Function** | Select "**Timer trigger**" |
  | **Use Azurite...** | Checked |
  | **Schedule** | Enter "*/1 * * * *" (the CRON expression for every one minute) |

* Click **Create**

### "Function1.cs" Logic, Update Method

* Visual Studio will open a "Function1.cs" tab

  <img src="https://user-images.githubusercontent.com/44923999/208475058-2f42f4c6-4783-4bdd-b838-c2cbcb4ca755.png" width="800" title="Snipped: December 19, 2022" />
![image](https://user-images.githubusercontent.com/44923999/.png)

* Replace the default code `public void Run([TimerTrigger("*/1 * * * *")]TimerInfo myTimer, TraceWriter log)` with:

  ```
  public async Task Run(
    [TimerTrigger("*/1 * * * *")] TimerInfo theTimer,
    [EventHub("dest", Connection = "EventHubConnectionAppSetting")] IAsyncCollector<string> theEventHub,
    ILogger theLogger)
  ```

  Logic Explained:

  * `async Task` ... provides for use of asynchronous calls
  * `[EventHub...` ... provides for output to Event Hub (for later Data Explorer ingestion)

# Delete Me
```
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using System;
using System.Threading.Tasks;

namespace Event_Hub_Message_Generator
{
    public class Function1
    {
        [FunctionName("Function1")]
        public async Task Run(
            [TimerTrigger("*/1 * * * *")] TimerInfo theTimer,
            [EventHub("dest", Connection = "EventHubConnectionAppSetting")] IAsyncCollector<string> theEventHub,
            ILogger theLogger)
        {
            string output = "{\"rows\":[{\"id\":"+Guid.NewGuid()+ "},{\"id\":"+Guid.NewGuid()+"},{\"id\":"+Guid.NewGuid()+"}]}";

            theLogger.LogInformation(output);

            await theEventHub.AddAsync(output);
        }
    }
}
```