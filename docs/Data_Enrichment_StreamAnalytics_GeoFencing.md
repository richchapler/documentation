# Data Enrichment: Stream Analytics, Geo-Fencing

<img src="https://github.com/richchapler/AzureSolutions/assets/44923999/92b29e97-31a6-4d39-b494-9d1d4dd5a6c7" width="1000" />

## Use Case
* "Our devices stream hundreds of millions of events daily (including GSP coordinate data)"
* "We need to compare streaming coordinates to known geofence locations"
* "We need to characterize streaming coordinates as 'outside of', 'entering', 'inside of', or 'exiting' known geofence locations"

## Proposed Solution
* Configure Inputs / Outputs
* Generate Sample Data
* Automate Comparison

## Solution Requirements
* [**Event Hub**](https://learn.microsoft.com/en-us/azure/event-hubs/) >> Namespace :: Hub :: Consumer Group
* [**Storage Account**](Infrastructure_StorageAccount.md)
* [**Stream Analytics**](https://learn.microsoft.com/en-us/azure/stream-analytics/stream-analytics-introduction) [**Job**](https://learn.microsoft.com/en-us/azure/stream-analytics/stream-analytics-quick-create-portal)

-----

## Exercise 1: Configure Inputs / Outputs
In this exercise, we will configure inputs and outputs in the Stream Analytics Job.

### Step 1: Add Stream Input, Event Hub
Navigate to your Stream Analytics Job, then select "**Inputs**" from the "**Job topology**" group of the navigation pane.

<img src="https://github.com/richchapler/AzureSolutions/assets/44923999/589d5957-5d3a-42c7-935e-f9d643c7f47d" width="800" title="Snipped: Oct 3, 2023" />

Click "**Add input**" and select "**Event Hub**" from the "**Stream input**" group in the resulting dropdown.

<img src="https://github.com/richchapler/AzureSolutions/assets/44923999/1af03d71-adc8-44d6-ba4c-e07d3f01b37e" width="800" title="Snipped: Oct 3, 2023" />

Complete the resulting "**Event Hub**" >> "**New Input**" popout and click "**Save**".

### Step 2: Add Reference Input, Blob Storage
Navigate to your Stream Analytics Job, then select "**Inputs**" from the "**Job topology**" group of the navigation pane.

<img src="https://github.com/richchapler/AzureSolutions/assets/44923999/e5e52708-0966-42e0-adfc-d13591c1e84a" width="800" title="Snipped: Oct 3, 2023" />

Click "**Add input**" and select "**Blob storage/ADLS Gen2**" from the "**Reference input**" group in the resulting dropdown.

<img src="https://github.com/richchapler/AzureSolutions/assets/44923999/2dcc9363-0a28-40b2-af92-f1b4f71df795" width="800" title="Snipped: Oct 3, 2023" />

Complete the resulting "**Blob storage/ADLS Gen2**" >> "**New Input**" popout and click "**Save**".

### Step 3: Add Output, ADLS Gen2

Navigate to your Stream Analytics Job, then select "**Outputs**" from the "**Job topology**" group of the navigation pane.

<img src="https://github.com/richchapler/AzureSolutions/assets/44923999/3bc3ee8d-e63c-4a0a-9ba0-2062ffcfe1cb" width="800" title="Snipped: Oct 3, 2023" />

Click "**Add output**" and select "**Blob storage/ADLS Gen2**" from the the resulting dropdown.

<img src="https://github.com/richchapler/AzureSolutions/assets/44923999/98d91db0-9b1f-4001-a716-53bbc0f12dc4" width="800" title="Snipped: Oct 3, 2023" />

Complete the resulting "**Blob storage/ADLS Gen2**" >> "**New Output**" popout and click "**Save**".

-----

## Exercise 2: Generate Sample Data
In this exercise, we will fabricate stream data in the Event Hub and reference data in the Storage Account.

### Step 1: Event Hub Data Generator
Navigate to your Event Hub, then select "**Generate Data**..." from the "**Features**" group of the navigation pane.

<img src="https://github.com/richchapler/AzureSolutions/assets/44923999/6638fc3f-ce28-4c8c-878b-c14f5bd8c0aa" width="800" title="Snipped: Oct 3, 2023" />

Paste the following JSON in the "**Enter payload**" textbox:

```
[
    {
        "latitude": "47.6370891183",
        "longitude": "-122.123736172"
    }
]
```

Click "Send".

<img src="https://github.com/richchapler/AzureSolutions/assets/44923999/5306e642-6d3d-4ae1-b703-2ecaa17066d5" width="800" title="Snipped: Oct 3, 2023" />

_Note: This will create a single event that we will pickup in Stream Analytics. You will repeat this in later steps._

### Sample data for geojson column
```
{"type":"Polygon", "coordinates": [[ [10.0, 10.0], [20.0, 10.0], [20.0, 20.0], [10.0, 20.0], [10.0, 10.0] ]]}
```

https://learn.microsoft.com/en-us/azure/stream-analytics/stream-analytics-geospatial-functions

## Stream Analytics 'parseJson' Logic
```
function parseJson (strjson) { return JSON.parse(strjson); }
```

## Stream Analytics Query Logic
```
WITH events AS (
    SELECT e.EventProcessedUtcTime as processedOn,
        CreatePoint(e.latitude,e.longitude) as geography
    FROM rchaplereh e
    ),
comparison AS (
    SELECT s.dealer_cd,
        e.processedOn,
        e.geography, -- streamed coordinates
        udf.parseJson(s.feature_geometry) polygon, -- geofence
        ST_WITHIN(e.geography, udf.parseJson(s.feature_geometry)) as isWithin
    FROM events e CROSS JOIN rchaplers s
    ),
lookback AS (
    SELECT LAG(*,1) OVER (PARTITION BY dealer_cd LIMIT DURATION(minute, 5)) AS previous, *
    FROM comparison
    )
SELECT dealer_cd,
    processedOn,
    geography gps_current,
    previous.geography gps_previous,
    polygon geofence,
    previous.polygon geofence_previous,
    CASE WHEN isWithin = 1 AND previous.isWithin = 0 THEN 'ENTER'
        WHEN isWithin = 0 AND previous.isWithin = 1 THEN 'EXIT'
        ELSE ''
        END Status
INTO rchaplerdlsfs
FROM lookback
```

## Miscellaneous
Sample GeoJSON file (for using Azure Blob Storage, reference)
https://www.kaggle.com/datasets/pompelmo/usa-states-geojson

## Reference

* [Introducing Event Hubs Data Generator](https://www.microsoft.com/en-gb/industry/blog/technetuk/2020/02/20/introducing-event-hubs-data-generator/)