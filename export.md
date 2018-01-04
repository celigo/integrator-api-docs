Export API
==========
>**Guidance**: this API is typically only needed for SmartConnector development (where you need to create exports in your installer, or dynamically configure exports based on settings that you expose in your app), or for Embedded Integration development (where you want to automate deploying your exports via cloning or dynamically creating them from scratch).

### Endpoints
| Relative URI| Method | Success Code | Description|
|:-------------------|:-------|:------------:|:------------------------------|
|/exports|GET|200|Get all exports.|
|/exports/{_id}|GET|200|Get a specific export.|
|/exports|POST|201|Create a new export.|
|/exports/{_id}|PUT|200|Update a specific export.|
|/exports/{_id}|DELETE|204|Delete a specific export.|
|/exports/:_id/distributed|GET|200|Get the distributed configuration for a specific export (i.e. the fields stored directly in the export application).  Note that this API is currently only needed for Salesforce and NetSuite real-time exports.|
|/exports/:_id/distributed|PUT|200|Update the distributed configuration for a specific export (i.e. the fields stored directly in the export application).  Note that this API is currently only needed for Salesforce and NetSuite real-time exports.|
|/exports/{_id}/clone|POST|201|Clone a specific export.|

## Examples

#### 1.  Get a specific export.

```
GET /v1/exports/55ea776bb751843c37b7b366 HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token
```

Sample Response:

```
{
    "_id": "55ea776bb751843c37b7b366",
    "lastModified": "2017-04-16T18:56:15.879Z",
    "name": "GitHub Event Listener",
    "type": "webhook",
    "hooks": {
        "preSavePage": {
            "_stackId": null,
            "function": null
        }
    },
    "webhook": {
        "provider": "github",
        "key": "******"
    }
}
```

#### 2.  Update the name field for a specific export.

```
PUT /v1/exports/55ea776bb751843c37b7b366 HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token

{
    "_id": "55ea776bb751843c37b7b366",
    "lastModified": "2017-04-16T18:56:15.879Z",
    "name": "GitHub Event Listener (updated!)",
    "type": "webhook",
    "hooks": {
        "preSavePage": {
            "_stackId": null,
            "function": null
        }
    },
    "webhook": {
        "provider": "github",
        "key": "******"
    }
}
```

Sample Response:

```
{
    "_id": "55ea776bb751843c37b7b366",
    "lastModified": "2017-11-28T19:37:04.180Z",
    "name": "GitHub Event Listener (updated!)",
    "type": "webhook",
    "webhook": {
        "provider": "github",
        "key": "******"
    }
}
```
