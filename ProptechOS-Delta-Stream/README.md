# Delta Stream
The intention of Delta Stream is to notify subscribers about each change in Digital Twin graph of ProptechOS. ProptechOS Delta Stream is delivered via Azure EventHub, and can be consumed via a Kafka Consumer .
## Consuming the stream
The Delta stream is using the same stream implementation as the general stream, so refer to [ProptechOS-Streaming-Api docs for details](../ProptechOS-Streaming-Api).

## Example
See a full working example: [Java Spring ProptechOS Streaming API consumer](examples/java).

## The Idun ProptechOS Delta Stream message:

```json
{
  "iris" : ["https://proptechos.com/api/sensor/0234c884-f8dc-48d6-b627-2f0d8f8705d6"],
  "operation": "CREATE",
  "operationCompleteTime": "2022-03-24T21:26:14.219Z"
}
```
### Where:
* **iris** - array of affected twin IRIs (a URI is a kind of IRI);
* **operation** - the operation that was executed (possible values: _'CREATE'_, _'UPDATE'_, _'DELETE'_)

## Proprietary Microsoft Eventhub client
For information about event hubs and how to consume messages via the EventProcessorHost, please refer to https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-programming-guide

# Twin history
Each change in Digital Twin graph of ProptechOS is stored into Azure Blobs and could be retrieved via ProptechOS API _/history_ endpoints.
Thus, Delta Stream notifies that change has been introduced and Twin history reflects what actually has been changed.

## List of history endpoints:
* GET /api/json/realestate/{id}/history
* GET /api/json/realestatecomponent/{id}/history
* GET /api/json/buildingcomponent/{id}/history
* GET /api/json/room/{id}/history
* GET /api/json/storey/{id}/history
* GET /api/json/asset/{id}/history
* GET /api/json/device/{id}/history
* GET /api/json/sensor/{id}/history
* GET /api/json/actuator/{id}/history
* GET /api/json/actuationinterface/{id}/history

Each endpoint accepts a mandatory path parameter `id` - twin id and two optional parameters `startTime` and `endTime`.

## History endpoint response example

```json
[
  {
    "snapshotTime": "2022-03-24T21:26:14.219Z",
    "operation": "[CREATE|UPDATE|DELETE]",
    "agent": "test@company.com",
    "snapshot": {
      "id": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
      "createdTime": "2022-03-24T21:26:13.434Z",
      "hasAlias": [
        {
          "id": "string",
          "isMemberOfAliasNamespace": "3fa85f64-5717-4562-b3fc-2c963f66afa6"
        }
      ],
      "hasGeoReferenceOrigo": "0.0;0.0;0.0",
      "littera": "string",
      "popularName": "string",
      "class": "RealEstate",
      "ownedBy": "3fa85f64-5717-4562-b3fc-2c963f66afa6"
    }

  }
]
```

# Delta Stream and Twin timestamp comparison
* Twin properties `createdTime`/`updatedTime` - indicates timestamp when corresponding operation with the twin **was initiated** on the server side.
* Delta Stream message property `operationCompleteTime` and Delta Trail record property `snapshotTime` - indicates timestamp when mutation operation over the twin **was finished**.

Possible mutation operations: _'CREATE'_, _'UPDATE'_, _'DELETE'_.

Though `operationCompleteTime`/`snapshotTime` differs from `createdTime`/`updatedTime` and is greater.

